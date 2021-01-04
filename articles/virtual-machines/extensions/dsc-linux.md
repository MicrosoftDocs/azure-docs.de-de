---
title: Azure DSC-Erweiterung für Linux
description: Installiert OMI- und DSC-Pakete, damit ein virtueller Azure-Linux-Computer mithilfe von Desired State Configuration konfiguriert werden kann.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 1d1a5cf67a10a83a227f240fc31d25abfe9c7dd0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955938"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-Erweiterung für Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) ist eine Verwaltungsplattform, die Ihnen das Verwalten Ihrer IT- und Entwicklungsinfrastruktur per Konfiguration in Form von Code ermöglicht.

> [!NOTE]
> Die DSC-Erweiterung für Linux und die [Azure Monitor-VM-Erweiterung für Linux](./oms-linux.md) stehen zurzeit im Konflikt miteinander, und parallele Konfigurationen der beiden werden nicht unterstützt. Verwenden Sie nicht beide Lösungen zusammen auf der gleichen VM.

Die DSCForLinux-Erweiterung wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den OMI- und DSC-Agent auf virtuellen Azure-Computern. Die DSC-Erweiterung kann auch die folgenden Aktionen ausführen:

- Registrieren des virtuellen Linux-Computers beim Azure Automation-Konto, um Konfigurationen vom Azure Automation-Dienst zu pullen (Register-ExtensionAction)
- Pushen von MOF-Konfigurationen zum virtuellen Linux-Computer (Push-ExtensionAction)
- Anwenden der MOF-Metakonfiguration auf den virtuellen Linux-Computer, um den Pullserver so zu konfigurieren, dass die Knotenkonfiguration gepullt wird (Pull-ExtensionAction)
- Installieren von benutzerdefinierten DSC-Modulen für den virtuellen Linux-Computer (Install-ExtensionAction)
- Entfernen von benutzerdefinierten DSC-Modulen für den virtuellen Linux-Computer (Remove-ExtensionAction)

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Für Linux-Knoten unterstützt die DSC-Linux-Erweiterung alle in der [PowerShell-DSC-Dokumentation](/powershell/scripting/dsc/getting-started/lnxgettingstarted) aufgeführten Linux-Distributionen.

### <a name="internet-connectivity"></a>Internetkonnektivität

Um die DSCForLinux-Erweiterung verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. Die Register-Erweiterung beispielsweise setzt Konnektivität mit dem Automation-Dienst voraus.
Für andere Aktionen (z. B. Pull, Push, Install) sind Verbindungen mit Azure Storage und GitHub erforderlich. Entscheidend sind die vom Kunden bereitgestellten Einstellungen.

## <a name="extension-schema"></a>Erweiterungsschema

### <a name="public-configuration"></a>Öffentliche Konfiguration

Die folgenden öffentlichen Konfigurationsparameter werden unterstützt:

* `FileUri`: (optional, Zeichenfolge) URI der MOF-Datei, Meta-MOF-Datei oder benutzerdefinierten Ressourcen-ZIP-Datei.
* `ResourceName`: (optional, Zeichenfolge) Name des benutzerdefinierten Ressourcenmoduls.
* `ExtensionAction`: (optional, Zeichenfolge) Gibt an, was eine Erweiterung erwirkt. Gültige Werte: Register, Push, Pull, Install und Remove. Wenn nicht angegeben, wird standardmäßig eine Push-Aktion angenommen.
* `NodeConfigurationName`: (optional, Zeichenfolge) Name einer anzuwendenden Knotenkonfiguration.
* `RefreshFrequencyMins`: (optional, Int) Gibt an, wie oft (in Minuten) DSC versucht, die Konfiguration vom Pullserver abzurufen.
       Wenn sich die Konfiguration auf dem Pullserver und die aktuelle auf dem Zielknoten unterscheiden, wird sie auf den ausstehenden Speicher kopiert und angewendet.
* `ConfigurationMode`: (optional, Zeichenfolge) Gibt an, wie die Konfiguration von DSC angewendet werden soll. Mögliche Werte sind ApplyOnly, ApplyAndMonitior und ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (optional, Int) Gibt an, wie häufig (in Minuten) DSC wird sicherstellt, dass die Konfiguration im gewünschten Zustand ist.

> [!NOTE]
> Wenn Sie eine frühere Version als 2.3 verwenden, entspricht der Mode-Parameter ExtensionAction. „Mode“ scheint ein überladener Begriff zu sein. Um Verwechslungen zu vermeiden, wird ab Version 2.3 ExtensionAction verwendet. Um Abwärtskompatibilität zu gewährleisten, unterstützt die Erweiterung Modus und ExtensionAction.
>

### <a name="protected-configuration"></a>Geschützte Konfiguration

Die folgenden geschützten Konfigurationsparameter werden unterstützt:

* `StorageAccountName`: (optional, Zeichenfolge) Name des Speicherkontos, das die Datei enthält.
* `StorageAccountKey`: (optional, Zeichenfolge) Schlüssel des Speicherkontos, das die Datei enthält.
* `RegistrationUrl`: (optional, Zeichenfolge) URL des Azure Automation-Kontos.
* `RegistrationKey`: (optional, Zeichenfolge) Zugriffsschlüssel des Azure Automation-Kontos.


## <a name="scenarios"></a>Szenarien

### <a name="register-an-azure-automation-account"></a>Registrieren eines Azure Automation-Kontos
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-Format
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Anwenden einer MOF-Konfigurationsdatei (in einem Azure-Speicherkonto) auf den virtuellen Computer

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-Format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Anwenden einer MOF-Konfigurationsdatei (im öffentlichen Speicher) auf den virtuellen Computer

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-Format
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Anwenden einer Meta-MOF-Konfigurationsdatei (in einem Azure-Speicherkonto) auf den virtuellen Computer

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-Format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Anwenden einer MOF-Metakonfigurationsdatei (im öffentlichen Speicher) auf den virtuellen Computer
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell-Format
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Installieren eines benutzerdefinierten Ressourcenmoduls (ZIP-Datei im Azure Storage-Konto) auf den virtuellen Computer
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-Format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Installieren eines benutzerdefinierten Ressourcenmoduls (ZIP-Datei im Azure Storage-Konto) auf dem virtuellen Computer
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell-Format
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Entfernen eines benutzerdefinierten Ressourcenmoduls vom virtuellen Computer
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell-Format
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie mindestens einen virtuellen Computer bereitstellen, der eine Konfiguration nach der Bereitstellung erfordert, wie z. B. Onboarding bei Azure Automation.

Die Resource Manager-Beispielvorlage ist [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) und [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Weitere Informationen zu den Azure Ressource Manager-Vorlagen finden Sie unter [Azure Resource Manager-Vorlagen verfassen](../../azure-resource-manager/templates/template-syntax.md)


## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

### <a name="use-azure-cliazure-cli"></a>Verwenden von [Azure CLI][azure-cli]
Konfigurieren Sie vor der Bereitstellung der DSCForLinux-Erweiterung die Dateien `public.json` und `protected.json` gemäß den verschiedenen Szenarios in Abschnitt 3.

#### <a name="classic"></a>Klassisch

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Der klassische Bereitstellungsmodus wird auch Azure-Dienstverwaltungsmodus genannt. Sie können in den Modus wechseln, indem Sie Folgendes ausführen:
```
$ azure config mode asm
```

Sie können die DSCForLinux-Erweiterung bereitstellen, indem Sie Folgendes ausführen:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Um die neueste Version der Erweiterung anzuzeigen, führen Sie Folgendes aus:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Ressourcen-Manager
Sie können in den Ressource Manager-Modus wechseln, indem Sie Folgendes ausführen:
```
$ azure config mode arm
```

Sie können die DSCForLinux-Erweiterung bereitstellen, indem Sie Folgendes ausführen:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Im Azure Resource Manager-Modus ist `azure vm extension list` derzeit nicht verfügbar.
>

### <a name="use-azure-powershellazure-powershell"></a>Verwenden von [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Klassisch

Sie können sich an Ihrem Azure-Konto im Azure-Dienstverwaltungsmodus anmelden, indem Sie Folgendes ausführen:

```powershell
Add-AzureAccount
```

Und Sie können die DSCForLinux-Erweiterung bereitstellen, indem Sie Folgendes ausführen:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändern Sie den Inhalt von $privateConfig und $publicConfig entsprechend den verschiedenen Szenarien im obigen Abschnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Ressourcen-Manager

Sie können sich bei Ihrem Azure-Konto im Azure Resource Manager-Modus anmelden, indem Sie Folgendes ausführen:

```powershell
Login-AzAccount
```

Weitere Informationen zum Verwenden von Azure PowerShell mit Azure Resource Manager finden Sie unter [Verwalten von Azure-Ressourcen mithilfe von Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Sie können die DSCForLinux-Erweiterung bereitstellen, indem Sie Folgendes ausführen:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändern Sie den Inhalt von $privateConfig und $publicConfig entsprechend den verschiedenen Szenarien im obigen Abschnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Fehlercode: 51 stellt eine nicht unterstützte Distribution oder eine nicht unterstützte Erweiterungsaktion dar.
In einigen Fällen kann die DSC-Linus-Erweiterung OMI nicht installieren, wenn bereits eine höhere Version von OMI auf dem Rechner vorhanden ist. [Fehlerantwort: (000003) Downgrade nicht zulässig]



### <a name="support"></a>Support

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, stellen Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/community/)Fragen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
