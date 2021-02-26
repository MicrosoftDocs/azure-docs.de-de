---
title: Ausführen von benutzerdefinierten Skripterweiterungen auf Linux-VMs in Azure
description: Automatisieren von Konfigurationsaufgaben für virtuelle Linux-Computer mithilfe der Erweiterung für benutzerdefinierte Skripts (v2)
services: virtual-machines-linux
documentationcenter: ''
author: amjads1
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: amjads
ms.openlocfilehash: 5ab312dc6da57279ce5cbd4d8efd7912b8a36503
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390112"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Verwenden von Version 2 der Azure-Erweiterung für benutzerdefinierte Skripts mit virtuellen Linux-Computern
Version 2 der Erweiterung für benutzerdefinierte Skripts lädt Skripts auf virtuelle Azure-Computer herunter und führt sie dort aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Sie können Skripts von Azure Storage oder einem anderen zugänglichen Speicherort im Internet herunterladen oder sie für die Erweiterungsruntime bereitstellen. 

Die Erweiterung für benutzerdefinierte Skripts ist mit Azure Resource Manager-Vorlagen integriert. Sie können sie auch mithilfe der PowerShell, der Azure-Befehlszeilenschnittstelle oder der Azure Virtual Machines-REST-API ausführen.

In diesem Artikel wird erläutert, wie Sie die Erweiterung für benutzerdefinierte Skripts über die Azure-Befehlszeilenschnittstelle verwenden und die Erweiterung mithilfe einer Azure Resource Manager-Vorlage ausführen. Außerdem enthält dieser Artikel Schritte zur Problembehandlung für Linux-Systeme.


Die Erweiterung für benutzerdefinierte Linux-Skripts ist in zwei Versionen verfügbar:
* Version 1: Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2: Microsoft.Azure.Extensions.CustomScript

Stellen Sie neue und bereits vorhandene Bereitstellungen auf die neue Version 2 um. Die neue Version ist als direkter Ersatz konzipiert. Für die Migration müssen daher lediglich Name und Version geändert werden. Änderungen an der Erweiterungskonfiguration sind nicht erforderlich.


### <a name="operating-system"></a>Betriebssystem

Die Erweiterung für benutzerdefinierte Skripts für Linux wird auf den von Erweiterungen unterstützten Betriebssystemen ausgeführt. Weitere Informationen finden Sie in diesem [Artikel](../linux/endorsed-distros.md).

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
* Es wird nicht empfohlen, ein Skript auszuführen, das bewirkt, dass der VM-Agent angehalten oder aktualisiert wird. Das kann dazu führen, dass die Erweiterung in einem Übergangszustand verbleibt und ein Timeout verursacht wird.
* Wenn Sie über ein Skript verfügen, das einen Neustart des Systems bewirkt, installieren Sie Anwendungen, und führen Sie Skripts usw. aus. Planen Sie den Neustart des Systems mithilfe eines Cron-Auftrags oder mit einem Tool wie DSC oder Chef oder mit Puppet-Erweiterungen.
* Die Erweiterung führt jedes Skript nur einmal aus. Soll ein Skript bei jedem Neustart des Systems ausgeführt werden, können Sie [cloud-init image](../linux/using-cloud-init.md) und ein Modul vom Typ [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) (Skripts pro Boot) verwenden. Alternativ können Sie mithilfe des Skripts eine systemd-Diensteinheit erstellen.
* Es kann nur eine Version einer Erweiterung auf die VM angewendet werden. Um ein zweites benutzerdefiniertes Skript auszuführen, können Sie die vorhandene Erweiterung mit der neuen Konfiguration aktualisieren. Alternativ können Sie die benutzerdefinierte Skripterweiterung entfernen und sie mit dem aktualisierten Skript erneut anwenden.
* Wenn Sie den Ausführungszeitpunkt eines Skripts planen möchten, erstellen Sie mithilfe der Erweiterung einen Cron-Auftrag. 
* Während der Skriptausführung wird im Azure-Portal sowie in der CLI nur ein Übergangsstatus für die Erweiterung angezeigt. Sollten Sie häufigere Statusaktualisierungen für ein ausgeführtes Skript benötigen, müssen Sie eine eigene Lösung erstellen.
* Die Erweiterung für benutzerdefinierte Skripts verfügt über keine native Proxyserverunterstützung. Sie können innerhalb Ihres Skripts jedoch ein Dateiübertragungstool mit Proxyserverunterstützung verwenden (beispielsweise *cURL*). 
* Achten Sie auf nicht standardmäßige Verzeichnispfade, von denen Ihre Skripts oder Befehle gegebenenfalls abhängen, und verwenden Sie eine entsprechende Logik, um diese zu behandeln.
*  Beim Bereitstellen eines benutzerdefinierten Skripts für VMSS-Produktionsinstanzen wird empfohlen, eine JSON-Vorlage zu verwenden und das Skriptspeicherkonto zu speichern, in dem Sie Kontrolle über das SAS-Token haben. 


## <a name="extension-schema"></a>Erweiterungsschema

In der Konfiguration der benutzerdefinierten Skripterweiterung werden Aspekte wie der Skriptspeicherort und der auszuführende Befehl angegeben. Sie können diese Konfiguration in Konfigurationsdateien speichern oder sie in der Befehlszeile oder in einer Azure Resource Manager-Vorlage angeben. 

Sie können sensible Daten in einer geschützten Konfiguration speichern. Diese ist verschlüsselt und wird nur auf dem virtuellen Computer entschlüsselt. Die geschützte Konfiguration ist hilfreich, wenn der Ausführungsbefehl vertrauliche Informationen (beispielsweise ein Kennwort) enthält.

Diese Elemente müssen als vertrauliche Daten behandelt und in der Konfiguration mit den geschützten Einstellungen der Erweiterung angegeben werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> Die managedIdentity-Eigenschaft **darf nicht** in Verbindung mit den Eigenschaften storageAccountName oder storageAccountKey verwendet werden.

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | Zeichenfolge |
| type | CustomScript | Zeichenfolge |
| typeHandlerVersion | 2.1 | INT |
| fileUris (Beispiel) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (Beispiel) | python MyPythonScript.py \<my-param1> | Zeichenfolge |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | Zeichenfolge |
| skipDos2Unix (Beispiel) | false | boolean |
| timestamp (Beispiel) | 123456789 | 32-bit integer |
| storageAccountName (Beispiel) | examplestorageacct | Zeichenfolge |
| storageAccountKey (Beispiel) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | Zeichenfolge |
| managedIdentity (z. B.) | { } oder { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } oder { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON-Objekt |

### <a name="property-value-details"></a>Details zu Eigenschaftswerten
* `apiVersion`: Die aktuelle apiVersion kann über den [Ressourcen-Explorer](https://resources.azure.com/) oder über die Azure-Befehlszeilenschnittstelle mit dem Befehl `az provider list -o json` gefunden werden.
* `skipDos2Unix` (optional, boolesch): Überspringen der dos2unix-Konvertierung von skriptbasierten Datei-URLs oder des Skripts.
* `timestamp` (optional, 32-Bit-Integer): Durch Ändern dieses Felds können Sie eine erneute Ausführung des Skripts auslösen.  Jeder Integerwert ist akzeptabel; er muss sich lediglich vom vorherigen Wert unterscheiden.
* `commandToExecute` (**erforderlich**, wenn Skript nicht festgelegt; Zeichenfolge): das auszuführende Skript für den Einstiegspunkt. Verwenden Sie dieses Feld, falls Ihr Befehl vertrauliche Informationen (beispielsweise Kennwörter) enthält.
* `script` (**erforderlich**, wenn „commandToExecute“ nicht festgelegt; Zeichenfolge): ein Base64-codiertes (und optional mit Gzip komprimiertes) Skript, das durch „/bin/sh“ ausgeführt wird.
* `fileUris` (optional, Zeichenfolgenarray): die URLs für die herunterzuladenden Dateien.
* `storageAccountName` (optional, Zeichenfolge): der Name des Speicherkontos. Wenn Sie Speicheranmeldeinformationen angeben, muss es sich bei allen `fileUris` um URLs für Azure-Blobs handeln.
* `storageAccountKey` (optional, Zeichenfolge): der Zugriffsschlüssel des Speicherkontos.
* `managedIdentity` (optional, JSON-Objekt): die [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zum Herunterladen von Dateien
  * `clientId` (optional, Zeichenfolge): die Client-ID der verwalteten Identität
  * `objectId` (optional, Zeichenfolge): die Objekt-ID der verwalteten Identität


Die folgenden Werte können in öffentlichen oder geschützten Einstellungen festgelegt werden. Die Erweiterung lehnt jedoch jede Konfiguration ab, bei der die Werte sowohl in öffentlichen als auch in geschützten Einstellungen festgelegt sind.
* `commandToExecute`
* `script`
* `fileUris`

Die Verwendung öffentlicher Einstellungen kann zwar beim Debuggen hilfreich sein, es wird jedoch dringend empfohlen, geschützte Einstellungen zu verwenden.

Öffentliche Einstellungen werden in Klartext an den virtuellen Computer gesendet, auf dem das Skript ausgeführt wird.  Geschützte Einstellungen werden mit einem Schlüssel verschlüsselt, der nur in Azure und auf dem virtuellen Computer bekannt ist. Die Einstellungen werden unverändert auf dem virtuellen Computer gespeichert. Waren die Einstellungen also verschlüsselt, werden sie auch verschlüsselt auf dem virtuellen Computer gespeichert. Das Zertifikat zum Entschlüsseln der verschlüsselten Werte wird auf dem virtuellen Computer gespeichert und gegebenenfalls zur Laufzeit zum Entschlüsseln der Einstellungen verwendet.

#### <a name="property-skipdos2unix"></a>Eigenschaft: skipDos2Unix

Der Standardwert lautet „FALSE“, d.h., die dos2unix-Konvertierung **wird** ausgeführt.

Mit der früheren Version von CustomScript, d.h. mit Microsoft.OSTCExtensions.CustomScriptForLinux, wurden DOS-Dateien durch Umwandlung von `\r\n` in `\n` automatisch in UNIX-Dateien konvertiert. Diese Umwandlung ist noch vorhanden und standardmäßig aktiviert. Die Konvertierung wird basierend auf den folgenden Kriterien auf alle über fileUris heruntergeladenen Dateien oder die Skripteinstellung angewandt.

* Wenn die Erweiterung `.sh`, `.txt`, `.py` oder `.pl` lautet, wird die Konvertierung durchgeführt. Die Skripteinstellung erfüllt dieses Kriterium immer, da davon ausgegangen wird, dass das Skript durch „/bin/sh“ ausgeführt und unter „script.sh“ auf dem virtuellen Computer gespeichert wird.
* Wenn die Datei mit `#!` beginnt.

Die dos2unix-Konvertierung kann übersprungen werden, indem skipDos2Unix auf TRUE festgelegt wird.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Eigenschaft: script

CustomScript unterstützt die Ausführung eines benutzerdefinierten Skripts. In den Skripteinstellungen werden commandToExecute und fileUris in einer einzelnen Einstellung kombiniert. Anstatt eine Datei für den Download von Azure Storage oder dem GitHub Gist einrichten zu müssen, können Sie das Skript einfach als eine Einstellung codieren. Das Skript kann verwendet werden, um commandToExecute und fileUris zu ersetzen.

Das Skript **muss** Base64-codiert sein.  Das Skript kann **optional** mit Gzip komprimiert werden. Die Skripteinstellung kann in öffentlichen oder geschützten Einstellungen verwendet werden. Die Daten des Skriptparameters können eine Größe von maximal 256 KB aufweisen. Wenn das Skript diese Größe überschreitet, wird es nicht ausgeführt.

Beispielsweise wurde das folgende Skript in der Datei „script.sh“ gespeichert.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Die richtige CustomScript-Skripteinstellung wird durch Abrufen der Ausgabe des folgenden Befehls erstellt.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Das Skript kann optional mit Gzip komprimiert werden, um die Größe weiter zu verringern (in den meisten Fällen). (CustomScript erkennt die Verwendung der Gzip-Komprimierung automatisch.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript verwendet den folgenden Algorithmus zum Ausführen eines Skripts.

 1. Bestätigen, dass die Länge des Skriptwerts 256 KB nicht überschreitet.
 1. Base64-Decodierung des Skriptwerts
 1. _Versuchen_, für den Base64-decodierten Wert den Befehl „gunzip“ auszuführen
 1. Schreiben des decodierten (und optional dekomprimierten) Werts auf den Datenträger („/var/lib/waagent/custom-script/#/script.sh“)
 1. Ausführen des Skripts unter Verwendung von „_/bin/sh -c /var/lib/waagent/custom-script/#/script.sh“

####  <a name="property-managedidentity"></a>Eigenschaft: managedIdentity
> [!NOTE]
> Diese Eigenschaft **muss** nur in geschützten Einstellungen angegeben werden.

CustomScript (ab Version 2.1) unterstützt [verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) zum Herunterladen von Dateien von URLs, die in der Einstellung „fileUris“ angegeben werden. So kann CustomScript auf private Azure Storage-Blobs oder -Container zugreifen, ohne dass der Benutzer Geheimnisse wie SAS-Token oder Speicherkontoschlüssel übergeben muss.

Um diese Funktion verwenden zu können, muss der Benutzer der VM oder VMSS, auf der CustomScript ausgeführt werden soll, eine [vom System zugewiesene](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) oder [vom Benutzer zugewiesene](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) Identität hinzufügen und [der verwalteten Identität Zugriff auf den Azure Storage-Container oder das -Blob gewähren](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Um die vom System zugewiesene Identität für die Ziel-VM/VMSS zu verwenden, legen Sie das Feld „managedidentity“ auf ein leeres JSON-Objekt fest. 

> Beispiel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Um die vom Benutzer zugewiesene Identität für die Ziel-VM/VMSS zu verwenden, konfigurieren Sie das Feld „managedidentity“ mit der Client-ID oder der Objekt-ID der verwalteten Identität.

> Beispiele:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> Die managedIdentity-Eigenschaft **darf nicht** in Verbindung mit den Eigenschaften storageAccountName oder storageAccountKey verwendet werden.

## <a name="template-deployment"></a>Bereitstellung von Vorlagen
Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der benutzerdefinierten Skripterweiterung im Rahmen einer Azure Resource Manager-Bereitstellung verwendet werden. Eine Beispielvorlage, die die benutzerdefinierte Skripterweiterung enthält, finden Sie hier auf [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Bei Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden. Um Bereitstellungsprobleme zu vermeiden, verwenden Sie die Namen wie hier gezeigt.

## <a name="azure-cli"></a>Azure CLI
Wenn Sie die Azure-Befehlszeilenschnittstelle zum Ausführen der Erweiterung für benutzerdefinierte Skripts verwenden, erstellen Sie eine Konfigurationsdatei oder mehrere Konfigurationsdateien. Es muss mindestens commandToExecute festgelegt sein.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Optional können Sie die Einstellungen im Befehl als JSON-formatierte Zeichenfolge angeben. Dadurch kann die Konfiguration während der Ausführung und ohne separate Konfigurationsdatei angegeben werden.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Beispiele für die Azure-Befehlszeilenschnittstelle

#### <a name="public-configuration-with-script-file"></a>Öffentliche Konfiguration mit Skriptdatei

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Befehl für die Azure-Befehlszeilenschnittstelle:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Problembehandlung
Beim Ausführen der Erweiterung für benutzerdefinierte Skripts wird das Skript erstellt oder wie im folgenden Beispiel in ein Verzeichnis heruntergeladen. Die Ausgabe des Befehls wird ebenfalls in diesem Verzeichnis gespeichert (in den Dateien `stdout` und `stderr`).

```bash
/var/lib/waagent/custom-script/download/0/
```

Überprüfen Sie zur Problembehandlung zunächst das Protokoll des Linux-Agents, und vergewissern Sie sich, dass die Erweiterung ausgeführt wurde:

```bash
/var/log/waagent.log 
```

Suchen Sie nach der Erweiterungsausführung. Diese sieht in etwa wie folgt aus:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Hinweise:
1. „Enable“ gibt den Beginn der Befehlsausführung an.
2. „Download“ bezieht sich auf das Herunterladen des CustomScript-Erweiterungspakets von Azure (nicht auf die in „fileUris“ angegebenen Skriptdateien).


Das von der Azure-Skripterweiterung erzeugte Protokoll finden Sie hier:

```bash
/var/log/azure/custom-script/handler.log
```

Suchen Sie nach der jeweiligen Ausführung. Diese ähnelt der folgenden:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Darin sind folgende Informationen enthalten:
* Der Befehl „Enable“ zum Starten dieses Protokolls.
* Die an die Erweiterung übergebenen Einstellungen.
* Die Downloaddatei der Erweiterung und das entsprechende Ergebnis.
* Der ausgeführte Befehl und das Ergebnis.

Sie können den Ausführungsstatus der Erweiterung für benutzerdefinierte Skripts einschließlich der als `commandToExecute` übergebenen Argumente auch über die Azure-Befehlszeilenschnittstelle abrufen:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Die Ausgabe sieht in etwa wie folgt aus:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Nächste Schritte
Den Code sowie aktuelle Probleme und Versionen finden Sie im [Repository der CustomScript-Erweiterung für Linux](https://github.com/Azure/custom-script-extension-linux).
