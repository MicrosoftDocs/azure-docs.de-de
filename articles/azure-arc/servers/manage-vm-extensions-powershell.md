---
title: Aktivieren der VM-Erweiterung mithilfe von Azure PowerShell
description: In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell VM-Erweiterungen auf Azure Arc-fähigen Servern bereitstellen, die in Hybrid Cloud-Umgebungen ausgeführt werden.
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6b38c425042c260a29682db11212a1f6324abd38
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387409"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie Azure-VM-Erweiterungen, die von Azure Arc-fähigen Servern unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer unter Verwendung von Azure PowerShell bereitstellen oder deinstallieren.

## <a name="prerequisites"></a>Voraussetzungen

- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

Bevor Sie Azure PowerShell zum Verwalten von VM-Erweiterungen auf Ihrem Hybridserver, der von Arc-fähigen Servern verwaltet wird, verwenden können, müssen Sie das Modul `Az.ConnectedMachine` installieren. Führen Sie auf Ihrem Arc-fähigen Server den folgenden Befehl aus:

`Install-Module -Name Az.ConnectedMachine`.

Nach Abschluss der Installation wird die folgende Meldung zurückgegeben:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Aktivieren der Erweiterung

Verwenden Sie [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) mit den Parametern `-Name`, `-ResourceGroupName`, `-MachineName`, `-Location`, `-Publisher`, -`ExtensionType` und `-Settings`, um eine VM-Erweiterung auf dem Arc-fähigen Server zu aktivieren.

Im folgenden Beispiel wird die Log Analytics-VM-Erweiterung auf einem Arc-fähigen Linux-Server aktiviert:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentforLinux"
```

Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts auf einem Arc-fähigen Server aktiviert:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault-VM-Erweiterung (Vorschau)

> [!WARNING]
> PowerShell-Clients fügen `"` häufig `\` in der Datei „settings.json“ hinzu. Dies verursacht bei akvvm_service folgenden Fehler: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Im folgenden Beispiel wird die Key Vault-VM-Erweiterung (Vorschau) auf einem Arc-fähigen Server aktiviert:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Auflisten der Installierten Erweiterungen

Verwenden Sie [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) mit den Parametern `-MachineName` und `-ResourceGroupName`, um eine Liste der VM-Erweiterungen auf dem Arc-fähigen Server abzurufen.

Beispiel:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Entfernen einer installierten Erweiterung

Verwenden Sie [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) mit den Parametern `-Name`, `-MachineName` und `-ResourceGroupName`, um eine installierte VM-Erweiterung auf dem Arc-fähigen Server zu entfernen.

Um beispielsweise die Log Analytics-VM-Erweiterung für Linux zu entfernen, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).
