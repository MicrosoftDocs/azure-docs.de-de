---
title: Azure-DSC-Erweiterungs-Handler (Desired State Configuration)
description: Hochladen und Anwenden einer PowerShell-DSC-Konfiguration auf einer Azure-VM mithilfe der DSC-Erweiterung
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 5254d83c18ddc9f2a5518ed4f711d4cd73ab6de7
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510939"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-Erweiterung

## <a name="overview"></a>Übersicht

Die PowerShell-DSC-Erweiterung für Windows wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung lädt eine PowerShell-DSC-Konfiguration auf einer Azure-VM hoch und wendet diese an. Die DSC-Erweiterung richtet einen Aufruf an PowerShell DSC, um die empfangene DSC-Konfiguration auf der VM in Kraft zu setzen. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die DSC-VM-Erweiterung für Windows.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Die DSC-Erweiterung unterstützt die folgenden Betriebssysteme:

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Internetkonnektivität

Für die DSC-Erweiterung ist es erforderlich, dass der virtuelle Zielcomputer in der Lage ist, mit Azure zu kommunizieren, und der Speicherort des Konfigurationspakets (ZIP-Datei) außerhalb von Azure liegt. 

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für den Bereich mit den Einstellungen der DSC-Erweiterung in einer Azure Resource Manager-Vorlage. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
             "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | INT |

### <a name="settings-property-values"></a>Eigenschaftswerte der Einstellungen

| Name | Datentyp | BESCHREIBUNG
| ---- | ---- | ---- |
| settings.wmfVersion | string | Gibt die Version von Windows Management Framework an, die auf Ihrem virtuellen Computer installiert sein muss. Wenn diese Eigenschaft auf „latest“ festgelegt ist, wird die aktuelle Version von WMF installiert. Die einzigen derzeit möglichen Werte für diese Eigenschaft sind „4.0“, „5.0“ und „latest“. Diese möglichen Werte werden gelegentlich aktualisiert. Der Standardwert ist „latest“. |
| settings.configuration.url | string | Gibt den URL-Speicherort an, von dem die ZIP-Datei Ihrer DSC-Konfiguration herunterzuladen ist. Wenn die bereitgestellte URL ein SAS-Token für den Zugriff erfordert, müssen Sie die protectedSettings.configurationUrlSasToken-Eigenschaft auf den Wert Ihres SAS-Tokens festlegen. Diese Eigenschaft ist erforderlich, wenn „settings.configuration.script“ und/oder „settings.configuration.function“ definiert sind.
| settings.configuration.script | string | Gibt den Dateinamen des Skripts an, das die Definition Ihrer DSC-Konfiguration enthält. Dieses Skript muss sich im Stammverzeichnis der ZIP-Datei befinden, die von der durch die configuration.url-Eigenschaft angegebenen URL heruntergeladen wurde. Diese Eigenschaft ist erforderlich, wenn „settings.configuration.url“ und/oder „settings.configuration.script“ definiert sind.
| settings.configuration.function | string | Gibt den Namen Ihrer DSC-Konfiguration an. Die Konfiguration mit diesem Namen muss im Skript enthalten sein, das durch „configuration.script“ definiert ist. Diese Eigenschaft ist erforderlich, wenn „settings.configuration.url“ und/oder „settings.configuration.function“ definiert sind.
| settings.configurationArguments | Collection | Definiert beliebige Parameter, die Sie Ihrer DSC-Konfiguration übergeben möchten. Diese Eigenschaft wird nicht verschlüsselt.
| settings.configurationData.url | string | Gibt die URL an, unter der die Datei mit Ihren Konfigurationsdaten (.pds1) heruntergeladen werden kann, um sie als Eingabe für Ihre DSC-Konfiguration zu nutzen. Wenn die bereitgestellte URL ein SAS-Token für den Zugriff erfordert, müssen Sie die protectedSettings.configurationDataUrlSasToken-Eigenschaft auf den Wert Ihres SAS-Tokens festlegen.
| settings.privacy.dataEnabled | string | Aktiviert bzw. deaktiviert die Erfassung von Telemetriedaten. Die einzig möglichen Werte für diese Eigenschaft sind „Enable“, „Disable“, „''“ oder „$null“. Wird die Eigenschaft leer gelassen oder „null“ angegeben, ist die Telemetrie aktiviert.
| settings.advancedOptions.forcePullAndApply | Bool | Diese Einstellung ist dafür ausgelegt, die Arbeitserfahrung im Umgang mit der Erweiterung beim Registrieren von Knoten bei Azure Automation DSC zu verbessern.  Wenn der Wert auf `$true` festgelegt ist, wartet die Erweiterung die erste Ausführung der beim Dienst abgerufenen Konfiguration ab, bevor er die Erfolgs-/Fehlermeldung zurückgibt.  Wenn der Wert auf „$false“ festgelegt ist, bezieht sich der von der Erweiterung zurückgegebene Status lediglich darauf, ob der Knoten erfolgreich bei Azure Automation State Configuration registriert wurde, und die Knotenkonfiguration wird während der Registrierung nicht ausgeführt.
| settings.advancedOptions.downloadMappings | Collection | Definiert alternative Speicherorte zum Herunterladen von Abhängigkeiten wie WMF und .NET.

### <a name="protected-settings-property-values"></a>Eigenschaftswerte geschützter Einstellungen

| Name | Datentyp | BESCHREIBUNG
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | Definiert beliebige Parameter, die Sie Ihrer DSC-Konfiguration übergeben möchten. Diese Eigenschaft wird verschlüsselt. |
| protectedSettings.configurationUrlSasToken | string | Gibt das SAS-Token für den Zugriff auf durch „configuration.url“ definierte URL an. Diese Eigenschaft wird verschlüsselt. |
| protectedSettings.configurationDataUrlSasToken | string | Gibt das SAS-Token für den Zugriff auf durch „configurationData.url“ definierte URL an. Diese Eigenschaft wird verschlüsselt. |


## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden.
Vorlagen sind ideal, wenn Sie virtuelle Computer bereitstellen, die nach der Bereitstellung konfiguriert werden müssen.
Eine Resource Manager-Beispielvorlage mit der DSC-Erweiterung für Windows finden Sie im [Azure-Schnellstartkatalog](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Das Erweiterungspaket wird an diesem Speicherort auf der Azure-VM heruntergeladen und bereitgestellt.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Die Statusdatei der Erweiterung enthält den untergeordneten Status und die Erfolgs-/Fehlercodes des Status zusammen mit der ausführlichen Fehlermeldung und einer Beschreibung für jede Erweiterungsausführung.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Ausgabeprotokolle der Erweiterung werden im folgenden Verzeichnis protokolliert:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Fehlercodes und ihre Bedeutung

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 1000 | Allgemeiner Fehler | Die Nachricht für diesen Fehler wird durch die spezifische Ausnahme in Erweiterungsprotokollen bereitgestellt. |
| 52 | Fehler bei der Installation der Erweiterung | Die Nachricht für diesen Fehler wird durch die spezifische Ausnahme bereitgestellt. |
| 1002 | Fehler bei der Installation von WMF | Dies ist ein Fehler bei der Installation von WMF. |
| 1004 | Ungültiges ZIP-Paket | Ungültige ZIP-Datei. Fehler beim Entpacken der ZIP-Datei. |
| 1100 | Argumentfehler | Weist auf ein Problem in der vom Benutzer bereitgestellten Eingabe hin. Die Nachricht für den Fehler wird durch die spezifische Ausnahme bereitgestellt.|


### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
