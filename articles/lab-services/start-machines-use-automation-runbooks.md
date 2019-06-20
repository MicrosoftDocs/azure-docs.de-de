---
title: Starten von Computern mit Automation-Runbooks in Azure DevTest Labs | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie virtuelle Computer in einem Lab in Azure DevTest Labs starten, indem Sie Azure Automation-Runbooks verwenden.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61083617"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Starten von virtuellen Computern in einem Lab nach einer bestimmten Reihenfolge mit Azure Automation-Runbooks
Mit dem Feature [Autostart](devtest-lab-set-lab-policy.md#set-autostart) von DevTest Labs können Sie virtuelle Computer so konfigurieren, dass sie zu einem bestimmten Zeitpunkt automatisch gestartet werden. Bei diesem Feature wird das Starten von Computern in einer bestimmten Reihenfolge aber nicht unterstützt. Es gibt mehrere Szenarien, in denen diese Art von Automatisierung sinnvoll ist.  Ein Beispiel ist das Szenario, in dem eine Jumpbox-VM in einem Lab vor den anderen VMs zuerst gestartet werden muss, da die Jumpbox als Zugriffspunkt für die anderen VMs verwendet wird.  In diesem Artikel wird veranschaulicht, wie Sie ein Azure Automation-Konto mit einem PowerShell-Runbook einrichten, über das ein Skript ausgeführt wird. Im Skript werden Tags auf VMs im Lab genutzt, damit Sie die Startreihenfolge steuern können, ohne das Skript ändern zu müssen.

## <a name="setup"></a>Einrichtung
In diesem Beispiel muss den VMs im Lab das Tag **StartupOrder** mit dem entsprechenden Wert (0, 1, 2 usw.) hinzugefügt werden. Kennzeichnen Sie alle Computer, die nicht gestartet werden sollen, mit „-1“.

## <a name="create-an-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos
Erstellen Sie ein Azure Automation-Konto, indem Sie die Anleitung in [diesem Artikel](../automation/automation-create-standalone-account.md) befolgen. Wählen Sie beim Erstellen des Kontos die Option **Ausführende Konten**. Öffnen Sie nach dem Erstellen des Automation-Kontos die Seite **Module**, und wählen Sie in der Menüleiste die Option **Azure-Module aktualisieren**. Die Standardmodule sind mehrere Versionen alt, und ohne das Update funktioniert das Skript ggf. nicht.

## <a name="add-a-runbook"></a>Hinzufügen eines Runbooks
Wählen Sie nun im Menü auf der linken Seite die Option **Runbooks**, um dem Automation-Konto ein Runbook hinzuzufügen. Wählen Sie im Menü die Option **Runbook hinzufügen**, und befolgen Sie die Anleitung zum [Erstellen eines PowerShell-Runbooks](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-Skript
Im folgenden Skript werden der Abonnementname und der Lab-Name als Parameter verwendet. Das Skript läuft so ab, dass alle VMs im Lab abgerufen und anschließend die Taginformationen analysiert werden, um eine Liste mit den VM-Namen und der Startreihenfolge zu erstellen. Das Skript geht die VMs der Reihenfolge nach durch und startet die VMs. Wenn mehrere virtuelle Computer in einer bestimmten Reihenfolge vorhanden sind, werden sie über PowerShell-Aufträge asynchron gestartet. Legen Sie den Startwert für die VMs ohne Tag auf den letzten Wert (10) fest, damit sie standardmäßig zuletzt gestartet werden.  Wenn der virtuelle Computer für das Lab nicht automatisch gestartet werden soll, können Sie den Tagwert auf 11 festlegen, damit er ignoriert wird.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Erstellen eines Zeitplans
[Erstellen Sie im Automation-Konto einen Zeitplan](../automation/shared-resources/schedules.md#creating-a-schedule), wenn dieses Skript täglich ausgeführt werden soll. [Verknüpfen Sie den Zeitplan nach der Erstellung mit dem Runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

In einem umfassenderen Fall, wenn mehrere Abonnements mit mehreren Labs verwendet werden, sollten Sie die Parameterinformationen für verschiedene Labs in einer zentralen Datei speichern und die Datei anstelle der einzelnen Parameter an das Skript übergeben. Das Skript müsste geändert werden, aber die eigentliche Ausführung wäre identisch. In diesem Beispiel wird Azure Automation zum Ausführen des PowerShell-Skripts verwendet, aber es gibt auch andere Optionen, z. B. die Verwendung einer Aufgabe in einer Build-/Releasepipeline.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Automation finden Sie im folgenden Artikel: [Einführung in Azure Automation](../automation/automation-intro.md)
