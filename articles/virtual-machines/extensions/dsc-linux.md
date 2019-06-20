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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 4b0cd88cbb3729a3e81aeb5d6f43f417c8cb2f17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64682763"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-Erweiterung für Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) ist eine Verwaltungsplattform, die Ihnen das Verwalten Ihrer IT- und Entwicklungsinfrastruktur per Konfiguration in Form von Code ermöglicht.

Die DSCForLinux-Erweiterung wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den OMI- und DSC-Agent auf virtuellen Azure-Computern. Die DSC-Erweiterung kann auch die folgenden Aktionen ausführen


- Registrieren Sie den virtuellen Linux-Computer beim Azure Automation-Konto, um die Konfigurationen vom Azure Automation-Dienst zu pullen (ExtensionAction registrieren)
- Pushen Sie die MOF-Konfigurationen zum virtuellen Linux-Computer (ExtensionAction mithilfe von Push übertragen)
- Wenden Sie die MOF-Metakonfiguration auf den virtuellen Linux-Computer an, um den Pullserver so zu konfigurieren, dass die Knotenkonfiguration gepullt wird (ExtensionAction mit Pull übertragen)
- Installieren Sie benutzerdefinierte DSC-Module für den virtuellen Linux-Computer (ExtensionAction installieren)
- Entfernen Sie benutzerdefinierte DSC-Module für den virtuellen Linux-Computer (ExtensionAction entfernen)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Die DSC Linux-Erweiterung unterstützt alle [von Azure unterstützte Distributionen von Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros), außer:

| Distribution | Version |
|---|---|
| Debian | Alle Versionen |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Internetkonnektivität

Um die DSC-Erweiterung für Linux verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. Die Registrierung von Erweiterungen beispielsweise setzt Konnektivität mit dem Automation-Dienst voraus. Für andere Aktionen, z.B. Pull, erfordert die Installation Konnektivität mit Azure Storage/GitHub. Entscheidend sind die vom Kunden bereitgestellten Einstellungen.

## <a name="extension-schema"></a>Erweiterungsschema

### <a name="11-public-configuration"></a>1.1 Öffentliche Konfiguration

Die folgenden öffentlichen Konfigurationsparameter werden unterstützt:

* `FileUri`: (optional, Zeichenfolge) URI der MOF-Datei/Meta-MOF-Datei/benutzerdefinierten Ressourcen-ZIP-Datei.
* `ResourceName`: (optional, Zeichenfolge) Name des benutzerdefinierten Ressourcenmoduls
* `ExtensionAction`: (optional, Zeichenfolge) Gibt an, was eine Erweiterung erwirkt. Gültige Werte: Register, Push, Pull, Install, Remove. Wenn nicht angegeben, wird standardmäßig eine Push-Aktion angenommen.
* `NodeConfigurationName`: (optional, Zeichenfolge) Name einer anzuwendenden Knotenkonfiguration.
* `RefreshFrequencyMins`: (optional, Int) Gibt an, wie oft (in Minuten) DSC versucht, die Konfiguration vom Pullserver abzurufen. 
       Wenn sich die Konfiguration auf dem Pullserver und die aktuelle auf dem Zielknoten unterscheiden, wird sie auf den ausstehenden Speicher kopiert und angewendet.
* `ConfigurationMode`: (optional, Zeichenfolge) Gibt an, wie die Konfiguration von DSC angewendet werden soll. Gültige Werte sind: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (optional, Int) Gibt an, wie häufig (in Minuten) DSC wird sicherstellt, dass die Konfiguration im gewünschten Zustand ist.

> [!NOTE]
> Wenn Sie eine Version vor Version 2.3 verwenden, ist der Mode-Parameter mit ExtensionAction identisch. „Mode“ scheint ein überladener Begriff zu sein. Um Verwechslungen zu vermeiden, wird ExtensionAction ab Version 2.3 verwendet. Um Abwärtskompatibilität zu gewährleisten, unterstützt die Erweiterung Modus und ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 Geschützte Konfiguration

Die folgenden geschützten Konfigurationsparameter werden unterstützt:

* `StorageAccountName`: (optional, Zeichenfolge) Name des Speicherkontos, das die Datei enthält
* `StorageAccountKey`: (optional, Zeichenfolge) Schlüssel des Speicherkontos, das die Datei enthält
* `RegistrationUrl`: (optional, Zeichenfolge) URL des Azure Automation-Kontos
* `RegistrationKey`: (optional, Zeichenfolge) Zugriffsschlüssel des Azure Automation-Kontos


## <a name="scenarios"></a>Szenarien

### <a name="register-to-azure-automation-account"></a>Registrieren beim Azure Automation-Konto
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Anwenden einer MOF-Konfigurationsdatei (im Azure-Speicherkonto) auf den virtuellen Computer

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Anwenden einer MOF-Konfigurationsdatei (im öffentlichen Speicher) auf den virtuellen Computer

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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Anwenden einer MOF-Metakonfigurationsdatei (im Azure-Speicherkonto) auf den virtuellen Computer

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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Installieren eines benutzerdefinierten Ressourcenmoduls (ZIP-Datei im Azure Storage-Konto) auf den virtuellen Computer
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Installieren eines benutzerdefinierten Ressourcenmoduls (ZIP-Datei im Azure Storage-Konto) auf dem virtuellen Computer
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

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie mindestens einen virtuellen Computer bereitstellen, der eine Konfiguration nach der Bereitstellung erfordert, wie z.B. Onboarding bei Azure Automation. 

Die Resource Manager-Beispielvorlage ist [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) und [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Weitere Informationen zu den Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

### <a name="21-using-azure-cliazure-cli"></a>2.1. Verwenden von [**Azure CLI**][azure-cli]
Sie sollten vor der Bereitstellung der DSCForLinux-Erweiterung die Dateien `public.json` und `protected.json`, gemäß den verschiedenen Szenarios in Abschnitt 3 konfigurieren.

#### <a name="211-classic"></a>2.1.1. Klassisch
Der klassische Modus wird auch Azure Service Management-Modus genannt. Sie können in den Modus wechseln, indem Sie Folgendes ausführen:
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

#### <a name="212-resource-manager"></a>2.1.2. Ressourcen-Manager
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

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Verwenden von [**Azure PowerShell**][azure-powershell]

#### <a name="221-classic"></a>2.2.1 Klassisch

Sie können sich an Ihrem Azure-Konto (Azure-Dienstverwaltungsmodus) anmelden, indem Sie Folgendes ausführen:

```powershell>
Add-AzureAccount
```

Stellen Sie die DSCForLinux-Erweiterung bereit, indem Sie Folgendes ausführen:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Sie müssen den Inhalt von $privateConfig und $publicConfig gemäß verschiedenen Szenarios im obigen Abschnitt ändern 
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

#### <a name="222resource-manager"></a>2.2.2. Resource Manager

Sie können sich bei Ihrem Azure-Konto (Azure Resource Manager-Modus) anmelden, indem Sie Folgendes ausführen:

```powershell>
Login-AzAccount
```

Klicken Sie [**HIER**](../../azure-resource-manager/manage-resources-powershell.md), um weitere Informationen über die Verwendung von Azure PowerShell mit Azure Resource Manager anzuzeigen.

Sie können die DSCForLinux-Erweiterung bereitstellen, indem Sie Folgendes ausführen:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Sie müssen den Inhalt von $privateConfig und $publicConfig gemäß verschiedenen Szenarios im obigen Abschnitt ändern 
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

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Fehlercode: 51 stellt eine nicht unterstützte Distribution oder eine nicht unterstützte Erweiterungsaktion dar.
In einigen Fällen kann die DSC-Linus-Erweiterung OMI nicht installieren, wenn eine höhere Version von OMI bereits auf dem Rechner vorhanden ist. [Fehlerantwort: (000003) Downgrade nicht zulässig.]



### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
