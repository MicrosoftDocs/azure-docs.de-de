---
title: Erstellen von Definitionen der Gastkonfigurationsrichtlinie anhand der Gruppenrichtlinien-Baseline für Windows
description: Erfahren Sie, wie Sie die Gruppenrichtlinie aus der Sicherheitsbaseline von Windows Server 2019 in eine Richtliniendefinition konvertieren.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348643"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Erstellen von Definitionen der Gastkonfigurationsrichtlinie anhand der Gruppenrichtlinien-Baseline für Windows

Vor dem Erstellen von benutzerdefinierten Richtliniendefinitionen empfiehlt es sich, die allgemeinen Informationen zur [Azure Policy-Gastkonfiguration](../concepts/guest-configuration.md) zu lesen. Informationen zum Erstellen benutzerdefinierter Richtliniendefinitionen für Gastkonfigurationen für Linux finden Sie unter [Erstellen von Richtlinien für Gastkonfigurationen für Linux](./guest-configuration-create-linux.md). Informationen zum Erstellen von benutzerdefinierten Richtliniendefinitionen für Gastkonfigurationen für Windows finden Sie unter [Erstellen von Richtlinien für Gastkonfigurationen für Windows](./guest-configuration-create.md).

Beim Überwachen von Windows wird für die Gastkonfiguration ein [DSC](/powershell/scripting/dsc/overview/overview)-Ressourcenmodul (Desired State Configuration) zum Erstellen der Konfigurationsdatei verwendet. Die DSC-Konfiguration definiert den Zustand, in dem sich der Computer befinden soll. Wenn die Auswertung der Konfiguration **nicht konform** ergibt, wird der Richtlinieneffekt *auditIfNotExists* ausgelöst.
Die [Azure Policy-Gastkonfiguration](../concepts/guest-configuration.md) führt nur eine Überprüfung der Einstellungen auf Computern durch.

> [!IMPORTANT]
> Die Gastkonfigurationserweiterung ist zum Durchführen von Überprüfungen in virtuellen Azure-Computern erforderlich. Weisen Sie die folgenden Richtliniendefinitionen zu, um die Erweiterung auf allen Windows-Computern im gewünschten Umfang bereitzustellen:
> - [Erforderliche Komponenten bereitstellen, um die Gastkonfigurationsrichtlinie auf Windows-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Verwenden Sie keine geheimen oder vertraulichen Informationen in benutzerdefinierten Inhaltspaketen.

Von der DSC-Community wurde das [BaselineManagement-Modul](https://github.com/microsoft/BaselineManagement) veröffentlicht, mit dem exportierte Gruppenrichtlinienvorlagen in das DSC-Format konvertiert werden können. In Verbindung mit dem Cmdlet GuestConfiguration erstellt das BaselineManagement-Modul ein Azure Policy-Gastkonfigurationspaket für Windows aus dem Gruppenrichtlinieninhalt. Ausführliche Informationen zur Verwendung des BaselineManagement-Moduls finden Sie im Artikel [Schnellstart: Konvertieren von Gruppenrichtlinien in DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

In diesem Leitfaden werden die Schritte zum Erstellen eines Azure Policy-Gastkonfigurationspakets aus einem Gruppenrichtlinienobjekt (GPO) beschrieben. Zwar wird in der exemplarischen Vorgehensweise die Konvertierung der Sicherheitsbaseline von Windows Server 2019 beschrieben, derselbe Prozess kann jedoch auch auf andere GPOs angewendet werden.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Herunterladen der Windows Server 2019-Sicherheitsbaseline und Installieren der zugehörigen PowerShell-Module

So installieren Sie die **DSC-** , **GuestConfiguration-** , **BaselineManagement-** und zugehörigen Azure-Module in PowerShell:

1. Führen Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl aus:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Erstellen Sie ein Verzeichnis für die Windows Server 2019-Sicherheitsbaseline, und laden Sie diese aus dem Windows Security Compliance-Toolkit herunter.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Entsperren und erweitern Sie die heruntergeladene Server 2019-Baseline.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Überprüfen Sie den Inhalt der Server 2019-Baseline mithilfe von **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Konvertieren aus der Gruppenrichtlinie in die Azure Policy-Gastkonfiguration

Im nächsten Schritt wird die heruntergeladene Server 2019-Baseline mithilfe der Module GuestConfiguration und BaselineManagement in ein Gastkonfigurationspaket konvertiert.

1. Konvertieren Sie die Gruppenrichtlinie mithilfe des BaselineManagement-Moduls in die Desired State Configuration.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Benennen Sie die konvertierten Skripts um, formatieren Sie sie neu, und führen Sie sie dann aus, bevor Sie ein Richtlinieninhaltspaket erstellen.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Erstellen Sie ein Azure Policy-Gastkonfigurationsinhaltspaket.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Erstellen der Azure Policy-Gastkonfiguration

1. Im nächsten Schritt wird die Datei in Azure Blob Storage veröffentlicht. Für den Befehl `Publish-GuestConfigurationPackage` ist das `Az.Storage`-Modul erforderlich.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Nachdem ein benutzerdefiniertes Richtlinienpaket für Gastkonfigurationen erstellt und hochgeladen wurde, erstellen Sie die Richtliniendefinition für Gastkonfigurationen. Verwenden Sie das Cmdlet `New-GuestConfigurationPolicy`, um die Gastkonfiguration zu erstellen.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Veröffentlichen Sie die Richtliniendefinitionen mit dem Cmdlet `Publish-GuestConfigurationPolicy`. Das Cmdlet verfügt nur über den Parameter **Path** , mit dem auf den Speicherort der JSON-Dateien verwiesen wird, die mit `New-GuestConfigurationPolicy` erstellt werden. Um den Befehl „Veröffentlichen“ auszuführen, benötigen Sie Zugriff zum Erstellen von Richtliniendefinitionen in Azure. Die entsprechenden Autorisierungsanforderungen sind auf der Seite mit der [Übersicht über Azure Policy](../overview.md#getting-started) dokumentiert. Die beste integrierte Rolle ist **Mitwirkender bei Ressourcenrichtlinien**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Zuweisen der Richtliniendefinition für die Gastkonfiguration

Bei der in Azure erstellten Richtlinie ist der letzte Schritt das Zuweisen der Initiative. Informieren Sie sich darüber, wie Sie die Initiative per [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) oder [Azure PowerShell](../assign-policy-powershell.md) zuweisen können.

> [!IMPORTANT]
> Richtliniendefinitionen für Gastkonfigurationen müssen **immer** über die Initiative zugewiesen werden, in der die Richtlinien _AuditIfNotExists_ und _DeployIfNotExists_ kombiniert sind. Wenn nur die Richtlinie _AuditIfNotExists_ zugewiesen wird, werden die erforderlichen Komponenten nicht bereitgestellt, und die Richtlinie zeigt immer an, dass „0“ Server konform sind.

Das Zuweisen einer Richtliniendefinition mit der Auswirkung _DeployIfNotExists_ erfordert eine zusätzliche Zugriffsebene. Zum Erteilen der geringsten Berechtigung können Sie eine benutzerdefinierte Rollendefinition erstellen, die **Mitwirkender bei Ressourcenrichtlinien** erweitert. Im folgenden Beispiel wird eine Rolle mit dem Namen **Resource Policy Contributor DINE** mit der zusätzlichen Berechtigung _Microsoft.Authorization/roleAssignments/write_ erstellt.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Überprüfung von VMs mit [Gastkonfiguration](../concepts/guest-configuration.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](./programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](./get-compliance-data.md).
