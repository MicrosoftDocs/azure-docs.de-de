---
title: VM-Start bleibt in Azure bei „Windows wird vorbereitet. Computer nicht ausschalten.“ hängen. | Microsoft-Dokumentation
description: 'Hier werden die Schritte zur Behebung des Problems erläutert, bei dem der virtuelle Computer beim Starten hängen bleibt und die folgende Meldung angezeigt wird: „Windows wird vorbereitet. Computer nicht ausschalten.“ hängen.'
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c3592529d20680c6920e569887effee4ffe38344
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684008"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM-Start bleibt in Azure bei „Windows wird vorbereitet. Computer nicht ausschalten.“ hängen.

In diesem Artikel erfahren Sie, was Sie tun können, wenn Ihr virtueller Computer (Virtual Machine, VM) in der Startphase bei „Windows wird vorbereitet. Computer nicht ausschalten.“ hängen bleibt.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Symptome

Wenn Sie die **Startdiagnose** verwenden, um die Momentaufnahme eines virtuellen Computers zu erhalten, wird das Betriebssystem nicht vollständig gestartet. Der virtuelle Computer zeigt die Meldung „Windows wird vorbereitet. Computer nicht ausschalten.“ hängen.

![Beispielmeldung für Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Meldungsbeispiel](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Ursache

Dieses Problem tritt in der Regel auf, wenn der Server nach einer Änderung der Konfiguration den letzten Neustart ausführt. Die Konfigurationsänderung wurde möglicherweise durch Windows-Updates oder durch Änderungen an den Rollen/Features des Servers initiiert. Bei großen Windows-Updates benötigt das Betriebssystem mehr Zeit, um die Änderungen neu zu konfigurieren.

## <a name="back-up-the-os-disk"></a>Sichern des Betriebssystemdatenträgers

Sichern Sie den Betriebssystemdatenträger, bevor Sie versuchen, das Problem zu beheben.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Bei VMs mit einem verschlüsselten Datenträger müssen Sie die Datenträger zuerst entsperren (entschlüsseln)

Gehen Sie wie folgt vor, um zu ermitteln, ob der virtuelle Computer verschlüsselt ist:

1. Öffnen Sie im Azure-Portal Ihren virtuellen Computer, und navigieren Sie zu den Datenträgern.

2. In der Spalte **Verschlüsselung** sehen Sie, ob die Verschlüsselung aktiviert ist.

Ist der Betriebssystemdatenträger verschlüsselt, entsperren Sie den verschlüsselten Datenträger. Gehen Sie dazu wie folgt vor:

1. Erstellen Sie einen Wiederherstellungs-VM, der sich in der gleichen Ressourcengruppe, im gleichen Speicherkonto und am gleichen Speicherort befindet wie der betroffene virtuelle Computer.

2. Löschen Sie im Azure-Portal den betroffenen virtuellen Computer, aber behalten Sie den Datenträger bei.

3. Führen Sie PowerShell als Administrator aus.

4. Führen Sie das folgende Cmdlet aus, um den Geheimnisnamen abzurufen.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Führen Sie nach Erhalt des Geheimnisnamens die folgenden Befehle in PowerShell aus:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konvertieren Sie den Base64-codierten Wert in Bytes, und schreiben Sie die Ausgabe in eine Datei. 

    > [!Note]
    > Wenn Sie die USB-Entsperroption verwenden, muss der Name der BEK-Datei dem ursprünglichen BEK-GUID entsprechen. Erstellen Sie auf dem Laufwerk C: einen Ordner mit dem Namen „BEK“, bevor Sie die nächsten Schritte ausführen.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Kopieren Sie die BEK-Datei nach der Erstellung auf Ihrem PC auf den Wiederherstellungs-VM, dem Sie den gesperrten Betriebssystemdatenträger angefügt haben. Führen Sie die folgenden Befehle unter Verwendung des Speicherorts der BEK-Datei aus:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Optional:** In bestimmten Szenarien muss der Datenträger ggf. mithilfe des folgenden Befehls entschlüsselt werden:
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Bei dem obigen Befehl wird davon ausgegangen, dass der zu entschlüsselnde Datenträger den Buchstaben F hat.

8. Falls Sie Protokolle benötigen, navigieren zum Pfad **LAUFWERKBUCHSTABE:\Windows\System32\winevt\Logs**.

9. Trennen Sie das Laufwerk vom Wiederherstellungscomputer.

### <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

Wenn Sie eine Momentaufnahme erstellen möchten, führen Sie die unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md) beschriebenen Schritte aus.

## <a name="collect-an-os-memory-dump"></a>Erfassen eines Betriebssystemspeicherabbilds

Führen Sie die im Abschnitt [Sammeln der Speichersicherungsdatei](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) aufgeführten Schritte aus, um ein Betriebssystemabbild zu erfassen, wenn sich der virtuelle Computer bei der Konfiguration aufgehängt hat.

## <a name="contact-microsoft-support"></a>Microsoft-Support kontaktieren

Nachdem Sie die Abbilddatei erfasst haben, können Sie sich an den [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) wenden, um die Grundursache zu analysieren.


## <a name="rebuild-the-vm-by-using-powershell"></a>Neuerstellen des virtuellen Computers mithilfe von PowerShell

Führen Sie nach Erfassung der Speicherabbilddatei die folgenden Schritte aus, um den virtuellen Computer neu zu erstellen:

**Nicht verwaltete Datenträger**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Verwaltete Datenträger**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
