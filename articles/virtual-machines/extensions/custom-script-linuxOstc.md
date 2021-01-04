---
title: Ausführen von Version 1 der Azure-Erweiterung für benutzerdefinierte Skripts (veraltet) für Linux-VMs in Azure
description: Automatisieren Sie Konfigurationsaufgaben für virtuelle Linux-Computer mithilfe der Erweiterung für benutzerdefinierte Skripts (v1).
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: afce76c8f5330f7d73ff58cc9b9307af2b8832f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962126"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Verwenden von Version 1 der Azure-Erweiterung für benutzerdefinierte Skripts mit virtuellen Linux-Computern

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Version 1 der Erweiterung für benutzerdefinierte Skripts lädt Skripts auf virtuelle Azure-Computer herunter und führt sie dort aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Sie können Skripts von Azure Storage oder einem anderen zugänglichen Speicherort im Internet herunterladen oder sie für die Erweiterungsruntime bereitstellen.

Die Erweiterung für benutzerdefinierte Skripts ist mit Azure Resource Manager-Vorlagen integriert. Sie können sie auch mithilfe der von PowerShell, der Azure-Befehlszeilenschnittstelle, dem Azure-Portal oder der Azure Virtual Machines-REST-API ausführen.

In diesem Artikel wird erläutert, wie Sie die Erweiterung für benutzerdefinierte Skripts über die Azure-Befehlszeilenschnittstelle verwenden und die Erweiterung mithilfe einer Azure Resource Manager-Vorlage ausführen. Außerdem enthält dieser Artikel Schritte zur Problembehandlung für Linux-Systeme.

Die Erweiterung für benutzerdefinierte Linux-Skripts ist in zwei Versionen verfügbar:

* Version 1: Microsoft.OSTCExtensions.CustomScriptForLinux

* Version 2: Microsoft.Azure.Extensions.CustomScript

Stellen Sie neue und bereits vorhandene Bereitstellungen auf die neue Version ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) um. Die neue Version ist als direkter Ersatz konzipiert. Für die Migration müssen daher lediglich Name und Version geändert werden. Änderungen an der Erweiterungskonfiguration sind nicht erforderlich.

### <a name="operating-system"></a>Betriebssystem

Unterstützte Linux-Distributionen:

* CentOS 6.5 oder höher
* Debian 8 oder höher
  * Debian 8.7 wird in den neuesten Images nicht mehr mit Python2 bereitgestellt, wodurch „CustomScriptForLinux“ nicht mehr funktioniert.
* FreeBSD
* OpenSUSE 13.1 oder höher
* Oracle Linux 6.4 oder höher
* SUSE Linux Enterprise Server 11 SP3 oder höher
* Ubuntu 12.04 oder höher

### <a name="script-location"></a>Speicherort des Skripts

Mit der Erweiterung können Sie unter Verwendung Ihrer Azure Blob Storage-Anmeldeinformationen auf Azure Blob Storage zugreifen. Alternativ kann das Skript an einem beliebigen Ort gespeichert sein, solange der virtuelle Computer ein Routing an diesen Endpunkt (GitHub, interner Dateiserver oder Ähnliches) durchführen kann.

### <a name="internet-connectivity"></a>Internetverbindung

Wenn Sie ein Skript extern herunterladen möchten (etwa im Fall von GitHub oder Azure Storage), müssen zusätzliche Firewall-/Netzwerksicherheitsgruppen-Ports geöffnet werden. Wenn sich Ihr Skript also beispielsweise in Azure Storage befindet, können Sie Zugriff über Azure-NSG-Diensttags für [Storage](../../virtual-network/network-security-groups-overview.md#service-tags) gewähren.

Befindet sich Ihr Skript auf einem lokalen Server, müssen gegebenenfalls noch weitere Firewall-/Netzwerksicherheitsgruppen-Ports geöffnet werden.

### <a name="tips-and-tricks"></a>Tipps und Tricks

* Die meisten Fehler für diese Erweiterung sind auf Syntaxfehler im Skript zurückzuführen. Vergewissern Sie sich, dass das Skript fehlerfrei ausgeführt wird, und versehen Sie es zusätzlich mit einer Protokollierung, um die Fehlersuche zu erleichtern.
* Schreiben Sie idempotente Skripts, um Systemänderungen zu vermeiden, wenn das Skript versehentlich mehrmals ausgeführt wird.
* Stellen Sie sicher, dass während der Skriptausführung keine Benutzereingaben erforderlich sind.
* Die Skriptausführung darf maximal 90 Minuten dauern. Danach gilt die Bereitstellung der Erweiterung als nicht erfolgreich.
* Das Skript darf keine Systemneustarts enthalten, da diese zu Problemen mit anderen Erweiterungen führen, die installiert werden. Außerdem wird die Erweiterung nach dem Neustart nicht fortgesetzt. 
* Wenn Sie über ein Skript verfügen, das einen Neustart des Systems bewirkt, installieren Sie Anwendungen, und führen Sie Skripts usw. aus. Planen Sie den Neustart des Systems mithilfe eines Cron-Auftrags oder mit einem Tool wie DSC oder Chef oder mit Puppet-Erweiterungen.
* Die Erweiterung führt jedes Skript nur einmal aus. Soll ein Skript bei jedem Neustart des Systems ausgeführt werden, können Sie [cloud-init image](../linux/using-cloud-init.md) und ein Modul vom Typ [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) (Skripts pro Boot) verwenden. Alternativ können Sie mithilfe des Skripts eine Systemdiensteinheit erstellen.
* Wenn Sie den Ausführungszeitpunkt eines Skripts planen möchten, erstellen Sie mithilfe der Erweiterung einen Cron-Auftrag.
* Während der Skriptausführung wird im Azure-Portal sowie in der CLI nur ein Übergangsstatus für die Erweiterung angezeigt. Sollten Sie häufigere Statusaktualisierungen für ein ausgeführtes Skript benötigen, müssen Sie eine eigene Lösung erstellen.
* Die Erweiterung für benutzerdefinierte Skripts verfügt über keine native Proxyserverunterstützung. Sie können innerhalb Ihres Skripts jedoch ein Dateiübertragungstool mit Proxyserverunterstützung verwenden (beispielsweise *cURL*).
* Achten Sie auf nicht standardmäßige Verzeichnispfade, von denen Ihre Skripts oder Befehle gegebenenfalls abhängen, und verwenden Sie eine entsprechende Logik, um diese zu behandeln.

## <a name="extension-schema"></a>Erweiterungsschema

In der Konfiguration der benutzerdefinierten Skripterweiterung werden Aspekte wie der Skriptspeicherort und der auszuführende Befehl angegeben. Sie können diese Konfiguration in Konfigurationsdateien speichern oder sie in der Befehlszeile oder in einer Azure Resource Manager-Vorlage angeben. 

Sie können sensible Daten in einer geschützten Konfiguration speichern. Diese ist verschlüsselt und wird nur auf dem virtuellen Computer entschlüsselt. Die geschützte Konfiguration ist hilfreich, wenn der Ausführungsbefehl vertrauliche Informationen (beispielsweise ein Kennwort) enthält.

Diese Elemente müssen als vertrauliche Daten behandelt und in der Konfiguration mit den geschützten Einstellungen der Erweiterung angegeben werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | Zeichenfolge |
| type | CustomScriptForLinux | Zeichenfolge |
| typeHandlerVersion | 1.5 | INT |
| fileUris (Beispiel) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (Beispiel) | python MyPythonScript.py \<my-param1\> | Zeichenfolge |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (Beispiel) | examplestorageacct | Zeichenfolge |
| storageAccountKey (Beispiel) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | Zeichenfolge |

### <a name="property-value-details"></a>Details zu Eigenschaftswerten

* `fileUris` (optional, Zeichenfolgenarray): Die URI-Liste der Skripts.
* `enableInternalDNSCheck` (optional, boolescher Wert): Der Standardwert lautet „true“. Durch Festlegen auf „false“ wird die DNS-Prüfung deaktiviert.
* `commandToExecute` (optional; Zeichenfolge): Das auszuführende Einstiegspunktskript.
* `storageAccountName` (optional; Zeichenfolge): Der Name des Speicherkontos.
* `storageAccountKey` (optional, Zeichenfolge): der Zugriffsschlüssel des Speicherkontos.

Die folgenden Werte können in öffentlichen oder geschützten Einstellungen festgelegt werden, aber nicht in beidem.

* `commandToExecute`

Die Verwendung öffentlicher Einstellungen kann zwar beim Debuggen hilfreich sein, es wird jedoch dringend empfohlen, geschützte Einstellungen zu verwenden.

Öffentliche Einstellungen werden in Klartext an den virtuellen Computer gesendet, auf dem das Skript ausgeführt wird.  Geschützte Einstellungen werden mit einem Schlüssel verschlüsselt, der nur in Azure und auf dem virtuellen Computer bekannt ist. Die Einstellungen werden unverändert auf dem virtuellen Computer gespeichert. Waren die Einstellungen also verschlüsselt, werden sie auch verschlüsselt auf dem virtuellen Computer gespeichert. Das Zertifikat zum Entschlüsseln der verschlüsselten Werte wird auf dem virtuellen Computer gespeichert und gegebenenfalls zur Laufzeit zum Entschlüsseln der Einstellungen verwendet.

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der benutzerdefinierten Skripterweiterung im Rahmen einer Azure Resource Manager-Bereitstellung verwendet werden.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Bei Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden. Um Bereitstellungsprobleme zu vermeiden, verwenden Sie die Namen wie hier gezeigt.

## <a name="azure-cli"></a>Azure CLI

Wenn Sie die Azure-Befehlszeilenschnittstelle zum Ausführen der Erweiterung für benutzerdefinierte Skripts verwenden, erstellen Sie eine Konfigurationsdatei oder mehrere Konfigurationsdateien. Es muss mindestens commandToExecute festgelegt sein.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Optional können Sie die Einstellungen im Befehl als JSON-formatierte Zeichenfolge angeben. Dadurch kann die Konfiguration während der Ausführung und ohne separate Konfigurationsdatei angegeben werden.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Beispiele für die Azure-Befehlszeilenschnittstelle

#### <a name="public-configuration-with-no-script-file"></a>Öffentliche Konfiguration ohne Skriptdatei

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Befehl für die Azure-Befehlszeilenschnittstelle:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Dateien für die öffentliche und geschützte Konfiguration

Dateien für die öffentliche Konfiguration werden zum Angeben des Skriptdatei-URI verwendet. Dateien für die geschützte Konfiguration werden zum Angeben des auszuführenden Befehls verwendet.

Datei für die öffentliche Konfiguration:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Datei für die geschützte Konfiguration:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Befehl für die Azure-Befehlszeilenschnittstelle:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Problembehandlung

Beim Ausführen der Erweiterung für benutzerdefinierte Skripts wird das Skript erstellt oder wie im folgenden Beispiel in ein Verzeichnis heruntergeladen. Die Ausgabe des Befehls wird ebenfalls in diesem Verzeichnis gespeichert (in den Dateien `stdout` und `stderr`).

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Überprüfen Sie zur Problembehandlung zunächst das Protokoll des Linux-Agents, und vergewissern Sie sich, dass die Erweiterung ausgeführt wurde:

```bash
/var/log/waagent.log
```

Suchen Sie nach der Erweiterungsausführung. Diese sieht in etwa wie folgt aus:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Hinweise:

1. „Enable“ gibt den Beginn der Befehlsausführung an.
1. „Download“ bezieht sich auf das Herunterladen des CustomScript-Erweiterungspakets von Azure (nicht auf die in „fileUris“ angegebenen Skriptdateien).
1. Darüber hinaus sehen Sie, welche Protokolldatei in `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log` geschrieben wird.

Überprüfen Sie als Nächstes die Protokolldatei. Das Format sieht wie folgt aus:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Suchen Sie nach der jeweiligen Ausführung. Diese ähnelt der folgenden:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Darin sind folgende Informationen enthalten:

* Der Befehl „Enable“ zum Starten dieses Protokolls.
* Die an die Erweiterung übergebenen Einstellungen.
* Die Downloaddatei der Erweiterung und das entsprechende Ergebnis.
* Der ausgeführte Befehl und das Ergebnis.

Sie können den Ausführungsstatus der Erweiterung für benutzerdefinierte Skripts auch über die Azure-Befehlszeilenschnittstelle abrufen:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Die Ausgabe sieht in etwa wie folgt aus:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Nächste Schritte

Den Code sowie aktuelle Probleme und Versionen finden Sie im [Repository der CustomScript-Erweiterung](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).