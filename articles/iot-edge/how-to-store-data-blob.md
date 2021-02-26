---
title: Speichern von Blockblobs auf Geräten – Azure IoT Edge | Microsoft-Dokumentation
description: Enthält grundlegende Informationen zu Features für Tiering und Gültigkeitsdauer, zu unterstützten Blobspeichervorgängen und zur Verbindungsherstellung mit Ihrem Blobspeicherkonto.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6cefb60d794defcce54766b9c7f71e7fbf40fe5c
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539432"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Speichern von Daten im Edgebereich mit Azure Blob Storage in IoT Edge

Mit Azure Blob Storage in IoT Edge erhalten Sie eine [Blockblob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)- und [Anfügeblob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)-Speicherlösung im Edgebereich. Ein Blob Storage-Modul auf Ihrem IoT Edge-Gerät verhält sich wie ein Azure-Blobdienst – mit dem einzigen Unterschied, dass die Blobs auf Ihrem IoT Edge-Gerät lokal gespeichert werden. Sie können mit denselben Azure Storage SDK-Methoden oder Blob-API-Aufrufen auf Ihre Blobs zugreifen, mit denen Sie bereits arbeiten. In diesem Artikel werden die Konzepte zu Azure Blob Storage auf einem IoT Edge-Container erläutert, der einen Blobdienst auf Ihrem IoT Edge-Gerät ausführt.

Dieses Modul ist in folgenden Fällen hilfreich:

* In Szenarien, in denen Daten lokal gespeichert werden müssen, bis sie verarbeitet oder in die Cloud übertragen werden können. Bei diesen Daten kann es sich um Videos, Bilder, Finanzdaten, Krankenhausdaten oder andere unstrukturierte Daten handeln.
* In Szenarien, in denen sich Geräte an Orten mit eingeschränkter Konnektivität befinden.
* In Szenarien, in denen Sie die Daten effizient lokal verarbeiten möchten, um mit möglichst geringer Wartezeit auf die Daten zugreifen und schnellstmöglich auf Notfälle reagieren zu können.
* In Szenarien, in denen Sie die Kosten für Bandbreite senken und die Übertragung umfangreicher Datenmengen in die Cloud vermeiden möchten. Sie können die Daten lokal verarbeiten und nur die verarbeiteten Daten an die Cloud senden.

Das folgende Video enthält eine kurze Einführung:
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Dieses Modul gehört zum Umfang der Features **deviceToCloudUpload** und **deviceAutoDelete**.

**deviceToCloudUpload** ist eine konfigurierbare Funktion. Diese Funktion lädt die Daten aus Ihrem lokalen Blobspeicher automatisch in Azure hoch und unterstützt sporadische Internetkonnektivität. Die Funktion ermöglicht Folgendes:

* AKTIVIEREN/DEAKTIVIEREN des Features deviceToCloudUpload.
* Auswählen der Reihenfolge, in der die Daten in Azure kopiert werden sollen (beispielsweise „NewestFirst“ oder „OldestFirst“).
* Angeben des Azure Storage-Kontos, in das Ihre Daten hochgeladen werden sollen
* Angeben der Container, die Sie in Azure hochladen möchten. Mit diesem Modul können Sie sowohl Quell- als auch Zielcontainernamen angeben.
* Auswählen der Möglichkeit, die Blobs sofort nach dem Hochladen in den Cloudspeicher zu löschen
* Führen Sie einen vollständigen Blobupload (mithilfe des Vorgangs `Put Blob`) und einen Upload auf Blockebene (mithilfe der Vorgänge `Put Block`, `Put Block List` und `Append Block`) durch.

Dieses Modul verwendet den Upload auf Blockebene, wenn Ihr Blob aus Blöcken besteht. Im Anschluss finden Sie einige gängige Szenarien:

* Ihre Anwendung aktualisiert einige Blöcke eines zuvor hochgeladenen Blockblobs oder fügt neue Blöcke an einen Anfügeblob an. Dieses Modul lädt also nur die aktualisierten Blöcke und nicht den gesamten Blob hoch.
* Falls beim Hochladen eines Blobs die Internetverbindung unterbrochen wird, lädt das Modul ebenfalls nur die restlichen Blöcke und nicht das gesamte Blob hoch, nachdem die Verbindung wiederhergestellt wurde.

Im Falle einer unerwarteten Prozessbeendigung während eines Blob-Uploads (etwa durch einen Stromausfall) werden alle Blöcke, die für den Upload vorgesehen waren, erneut hochgeladen, sobald das Modul wieder online ist.

**deviceAutoDelete** ist eine konfigurierbare Funktion. Diese Funktion löscht Ihre Blobs automatisch aus dem lokalen Speicher, wenn die angegebene Zeit (in Minuten) abläuft. Die Funktion ermöglicht Folgendes:

* AKTIVIEREN/DEAKTIVIEREN des Features deviceAutoDelete.
* Angeben der Zeit in Minuten (deleteAfterMinutes), nach der die Blobs automatisch gelöscht werden.
* Auswählen der Möglichkeit, das Blob während des Hochladens beizubehalten, wenn der deleteAfterMinutes-Wert abläuft.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure IoT Edge-Gerät:

* Sie können Ihren Entwicklungscomputer oder einen virtuellen Computer als IoT Edge-Gerät verwenden, indem Sie die Schritte ausführen, die in der Schnellstartanleitung für [Linux](quickstart-linux.md)- oder [Windows](quickstart.md)-Geräte beschrieben sind.

* Unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems) finden Sie eine Liste mit unterstützten Betriebssystemen und -Architekturen. Azure Blob Storage im IoT Edge-Modul unterstützt folgende Architekturen:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (Vorschauversion)

Cloudressourcen:

Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) mit Standardtarif in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Eigenschaften deviceToCloudUpload und deviceAutoDelete

Verwenden Sie die gewünschten Eigenschaften des Moduls, um **deviceToCloudUploadProperties** und **deviceAutoDeleteProperties** festzulegen. Gewünschte Eigenschaften können im Rahmen der Bereitstellung festgelegt oder nachträglich durch Bearbeitung des Modulzwillings ohne erneute Bereitstellung geändert werden. Es empfiehlt sich, den Modulzwilling für `reported configuration` und `configurationValidation` zu überprüfen, um sicherzustellen, dass die Werte korrekt weitergegeben werden.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Der Name dieser Einstellung lautet `deviceToCloudUploadProperties`. Wenn Sie den IoT Edge-Simulator verwenden, legen Sie die Werte auf die zugehörigen Umgebungsvariablen für diese Eigenschaften fest, die Sie im Abschnitt „Erläuterung“ finden können.

| Eigenschaft | Mögliche Werte | Erklärung |
| ----- | ----- | ---- |
| uploadOn | true, false | Standardmäßig auf `false` festgelegt. Wenn Sie das Feature aktivieren möchten, legen Sie dieses Feld auf `true` fest. <br><br> Umgebungsvariable: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Ermöglicht das Auswählen der Reihenfolge, in der die Daten in Azure kopiert werden. Standardmäßig auf `OldestFirst` festgelegt. Die Reihenfolge richtet sich nach dem Zeitpunkt der letzten Änderung des Blobs. <br><br> Umgebungsvariable: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` ist eine Verbindungszeichenfolge, die es ermöglicht, das Speicherkonto anzugeben, in das Ihre Daten hochgeladen werden sollen. Geben Sie `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix` an. Fügen Sie ein geeignetes Endpunktsuffix (EndpointSuffix) von Azure für das Ziel des Datenuploads hinzu (variiert für Azure global, Azure Government und Microsoft Azure Stack). <br><br> Sie können hier eine Azure Storage SAS-Verbindungszeichenfolge angeben. Allerdings müssen Sie diese Eigenschaft aktualisieren, wenn sie abläuft. SAS-Berechtigungen können das Erstellen des Zugriffs für Container sowie Erstellen, Schreiben und Hinzufügen des Zugriffs für Blobs umfassen.  <br><br> Umgebungsvariable: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Ermöglicht Ihnen das Angeben der Containernamen, die Sie in Azure hochladen möchten. Mit diesem Modul können Sie sowohl Quell- als auch Zielcontainernamen angeben. Falls Sie keinen Zielcontainernamen angeben, wird der Containername automatisch wie folgt zugewiesen: `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Sie können Vorlagenzeichenfolgen für den Zielcontainernamen erstellen. Weitere Informationen finden Sie in der Spalte mit den möglichen Werten. <br>* %h -> IoT Hub-Name (drei bis 50 Zeichen) <br>* %d -> IoT Edge-Geräte-ID (1 bis 129 Zeichen) <br>* %m -> Modulname (ein bis 64 Zeichen) <br>* %c -> Quellcontainername (drei bis 63 Zeichen) <br><br>Der Containername darf maximal 63 Zeichen lang sein. Bei automatischer Zuweisung des Zielcontainernamens gilt Folgendes: Ist der Name länger als 63 Zeichen, werden die einzelnen Bestandteile (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) jeweils auf 15 Zeichen gekürzt. <br><br> Umgebungsvariable: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true, false | Standardmäßig auf `false` festgelegt. Bei Festlegung auf `true` werden die Daten automatisch gelöscht, wenn der Upload in den Cloudspeicher abgeschlossen ist. <br><br> **VORSICHT**: Wenn Sie Anfügeblobs verwenden, löscht diese Einstellung Anfügeblobs nach einem erfolgreichen Upload aus dem lokalen Speicher, und alle künftigen Vorgänge zum Anfügen von Blöcken an diese Blobs schlagen fehl. Verwenden Sie diese Einstellung mit Vorsicht, und aktivieren Sie sie nicht, wenn Ihre Anwendung Anfügevorgänge nur selten ausführt oder kontinuierliche Anfügevorgänge nicht unterstützt.<br><br> Umgebungsvariable: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Der Name dieser Einstellung lautet `deviceAutoDeleteProperties`. Wenn Sie den IoT Edge-Simulator verwenden, legen Sie die Werte auf die zugehörigen Umgebungsvariablen für diese Eigenschaften fest, die Sie im Abschnitt „Erläuterung“ finden können.

| Eigenschaft | Mögliche Werte | Erklärung |
| ----- | ----- | ---- |
| deleteOn | true, false | Standardmäßig auf `false` festgelegt. Wenn Sie das Feature aktivieren möchten, legen Sie dieses Feld auf `true` fest. <br><br> Umgebungsvariable: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Geben Sie die Zeit in Minuten an. Nach Ablauf dieser Zeit löscht das Modul Ihre Blobs automatisch aus dem lokalen Speicher. Derzeit sind maximal 35791 Minuten zulässig. <br><br> Umgebungsvariable: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Die standardmäßige Einstellung ist `true`, und das Blob wird während des Uploads in den Cloudspeicher beibehalten, wenn deleteAfterMinutes abläuft. Sie können `false` festlegen, sodass die Daten gelöscht werden, sobald deleteAfterMinutes abgelaufen ist. Hinweis: Damit diese Eigenschaft funktioniert, setzen Sie uploadOn auf „true“.  <br><br> **VORSICHT**: Sollten Sie Anfügeblobs verwenden, löscht diese Einstellung Anfügeblobs aus dem lokalen Speicher, wenn der Wert abläuft, und künftige Vorgänge zum Anfügen von Blöcken an diese Blobs schlagen fehl. Vielleicht möchten Sie sicherstellen, dass der Ablaufwert groß genug für die erwartete Häufigkeit von Anfügevorgängen ist, die von Ihrer Anwendung ausgeführt werden.<br><br> Umgebungsvariable: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Verwenden der SMB-Freigabe als lokalen Speicher

Sie können die SMB-Freigabe als Ihren lokalen Speicherpfad bereitstellen, wenn Sie einen Windows-Container dieses Moduls auf dem Windows-Host bereitstellen.

SMB-Freigabe und IoT-Gerät müssen sich in Domänen befinden, die sich gegenseitig vertrauen.

Sie können den PowerShell-Befehl `New-SmbGlobalMapping` ausführen, um die SMB-Freigabe auf dem IoT-Gerät lokal zuzuordnen, auf dem Windows ausgeführt wird.

Im Folgenden sind die Konfigurationsschritte aufgeführt:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Beispiel:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Dieser Befehl verwendet die Anmeldeinformationen zum Authentifizieren beim SMB-Remoteserver. Ordnen Sie anschließend den Remotefreigabepfad dem Laufwerkbuchstaben „G:“ zu (kann ein beliebiger anderer verfügbarer Laufwerkbuchstabe sein). Das IoT-Gerät verfügt jetzt über das Datenvolumen, das einem Pfad auf Laufwerk „G:“ zugeordnet ist.

Der Benutzer des IoT-Geräts muss über Lese-und Schreibzugriff auf die SMB-Remotefreigabe verfügen.

Für Ihre Bereitstellung kann der Wert von `<storage mount>` gleich **G:/ContainerData:C:/BlobRoot** lauten.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Gewähren von Verzeichniszugriff für Containerbenutzer unter Linux

Wenn Sie in Ihren Erstellungsoptionen für Linux-Container [volume mount](https://docs.docker.com/storage/volumes/) für Speicher verwendet haben, sind keine weiteren Schritte erforderlich. Bei Verwendung von [bind mount](https://docs.docker.com/storage/bind-mounts/) müssen dagegen die folgenden Schritte ausgeführt werden, damit der Dienst korrekt ausgeführt wird.

Zur Einhaltung des Prinzips der geringsten Rechte, das dazu dient, die Zugriffsrechte von Benutzern auf die Mindestberechtigungen zu beschränken, die sie für ihre Aufgaben benötigen, enthält dieses Modul einen Benutzer (Name: absie, ID: 11000) und eine Benutzergruppe (Name: absie, ID: 11000). Wenn der Container als **root** gestartet wird (Standardbenutzer ist **root**), wird unser Dienst als der Benutzer **absie** mit geringen Berechtigungen gestartet.

Dieses Verhalten macht die Konfiguration der Berechtigungen für Hostpfadbindungen erforderlich, damit der Dienst korrekt funktioniert. Andernfalls stürzt der Dienst mit Zugriffsverweigerungsfehlern ab. Der Containerbenutzer (Beispiel: absie 11000) muss auf den in der Verzeichnisbindung verwendeten Pfad zugreifen können. Sie können dem Containerbenutzer Zugriff auf das Verzeichnis gewähren, indem Sie auf dem Host die folgenden Befehle ausführen:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Beispiel:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Wenn Sie den Dienst als ein Benutzer ausführen müssen, bei dem es sich nicht um **absie** handelt, können Sie in Ihrem Bereitstellungsmanifest in „createOptions“ unter der Eigenschaft „User“ Ihre benutzerdefinierte Benutzer-ID angeben. In diesem Fall müssen Sie die Standard- oder Stammgruppen-ID `0` verwenden.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Gewähren Sie nun dem Containerbenutzer Zugriff auf das Verzeichnis.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurieren von Protokolldateien

Informationen zur Konfiguration von Protokolldateien für Ihr Modul finden Sie unter den [bewährten Methoden für die Produktion](./production-checklist.md#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Herstellen einer Verbindung mit Ihrem Blob Storage-Modul

Sie können mit dem Kontonamen und dem Kontoschlüssel, die Sie für Ihr Modul konfiguriert haben, auf Blob Storage auf Ihrem IoT Edge-Gerät zugreifen.

Geben Sie Ihr IoT Edge-Gerät als Blobendpunkt für Speicheranforderungen an, die Sie vornehmen. Mithilfe der IoT Edge-Geräteinformationen und dem Kontonamen, den Sie konfiguriert haben, können Sie eine [Verbindungszeichenfolge für einen bestimmten Speicherendpunkt erstellen](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

* Bei Modulen, die auf demselben Gerät bereitgestellt werden, auf dem auch das Modul „Azure Blob Storage auf IoT Edge“ ausgeführt wird, lautet der Blobendpunkt wie folgt: `http://<module name>:11002/<account name>`.
* Bei Modulen oder Anwendungen, die auf einem anderen Gerät ausgeführt werden, müssen Sie den richtigen Endpunkt für Ihr Netzwerk auswählen. Wählen Sie abhängig von Ihrer Netzwerkeinrichtung ein geeignetes Endpunktformat aus, damit der Datenverkehr von Ihrem externen Modul oder Ihrer externen Anwendung das Gerät erreichen kann, auf dem das Modul „Azure Blob Storage auf IoT Edge“ ausgeführt wird. In diesem Szenario wird einer der folgenden Blob-Endpunkte verwendet:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Bei Modulaufrufen für Azure IoT Edge muss die Groß-/Kleinschreibung beachtet werden, und auch für das Storage SDK werden standardmäßig Kleinbuchstaben verwendet. Der Name des Moduls im [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) lautet **AzureBlobStorageonIoTEdge**. Indem Sie den Namen aber ändern und Kleinbuchstaben verwenden, können Sie sicherstellen, dass Ihre Verbindungen mit dem Modul „Azure Blob Storage auf IoT Edge“ nicht unterbrochen werden.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Schnellstartbeispiele für Azure Blob Storage

Die Dokumentation zu Azure Blob Storage enthält Schnellstart-Beispielcode in mehreren Sprachen. Sie können diese Beispiele zum Testen von Azure Blob Storage in IoT Edge herunterladen, indem Sie den Blobendpunkt dahingehend ändern, dass er eine Verbindung mit Ihrem lokalen Blobspeichermodul herstellt.

Die folgenden Schnellstartbeispiele verwenden Sprachen, die auch von IoT Edge unterstützt werden, sodass Sie sie als IoT Edge-Module zusammen mit dem Blobspeichermodul bereitstellen können:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * Das Modul Azure Blob Storage in IoT Edge ist bis Version 1.4.0 mit dem SDK WindowsAzure.Storage 9.3.3 kompatibel. Außerdem unterstützt Version 1.4.1 auch das SDK Azure.Storage.Blobs 12.8.0.
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * In Versionen vor V2.1 des Python SDKs tritt ein bekanntes Problem auf, bei dem das Modul keine Bloberstellungszeit zurückgibt. Aufgrund dieses Problems funktionieren einige Methoden wie das Auflisten von Blobs nicht. Als Problemumgehung legen Sie die API-Version auf dem Blobclient explizit auf „2017-04-17“ fest. Beispiel: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Beispiel für Anfügeblob](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Herstellen einer Verbindung mit Ihrem lokalen Speicher mit Azure Storage-Explorer

Sie können [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, um eine Verbindung mit Ihrem lokalen Speicherkonto herzustellen.

1. Laden Sie den Azure Storage-Explorer herunter, und installieren Sie ihn.

1. Stellen Sie mithilfe einer Verbindungszeichenfolge eine Verbindung mit Azure Storage her.

1. Geben Sie die Verbindungszeichenfolge an: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Führen Sie die Schritte für die Verbindungsherstellung aus.

1. Erstellen Sie einen Container innerhalb Ihres lokalen Speicherkontos.

1. Beginnen Sie mit dem Hochladen von Dateien als Blockblobs oder Anfügeblobs.
   > [!NOTE]
   > Dieses Modul unterstützt keine Seitenblobs.

1. Auf Wunsch können Sie Ihre Azure-Speicherkonten auch im Storage-Explorer verbinden. Mit dieser Konfiguration erhalten Sie eine zentrale Ansicht, die sowohl Ihr lokales Speicherkonto als auch Ihr Azure-Speicherkonto enthält.

## <a name="supported-storage-operations"></a>Unterstützte Speichervorgänge

Blob Storage-Module in IoT Edge verwenden die Azure Storage SDKs und entsprechen der Version „2017-04-17“ der Azure Storage-API für Blockblob-Endpunkte.

Da nicht alle Azure Blob Storage-Vorgänge von Azure Blob Storage auf IoT Edge unterstützt werden, ist in diesem Abschnitt der jeweilige Status angegeben.

### <a name="account"></a>Konto

Unterstützt:

* Auflisten von Containern

Nicht unterstützt:

* Abrufen und Festlegen von Blobdiensteigenschaften
* Preflightüberprüfung von Blobanforderungen
* Abrufen von Statistiken zum Blobdienst
* Abrufen von Kontoinformationen

### <a name="containers"></a>Container

Unterstützt:

* Erstellen und Löschen von Containern
* Abrufen von Containereigenschaften und -metadaten
* Auflisten von Blobs
* Abrufen und Festlegen von Container-ACLs
* Festlegen von Containermetadaten

Nicht unterstützt:

* Abrufen der Lease von Containern

### <a name="blobs"></a>BLOBs

Unterstützt:

* Festlegen, Abrufen und Löschen von Blobs
* Abrufen und Festlegen von Blobeigenschaften
* Abrufen und Festlegen von Blobmetadaten

Nicht unterstützt:

* Abrufen der Lease von Blobs
* Erstellen einer Momentaufnahme für Blobs
* Kopieren von Blobs und Abbrechen eines Blobkopiervorgangs
* Wiederherstellen von Blobs
* Festlegen des Blobtarifs

### <a name="block-blobs"></a>Blockblobs

Unterstützt:

* Festlegen von Blocks
* Festlegen und Abrufen von Blocklisten

Nicht unterstützt:

* Festlegen von Blocks über die URL

### <a name="append-blobs"></a>Anfügeblobs

Unterstützt:

* Anfügen von Blöcken

Nicht unterstützt:

* Anfügen von Blöcken über URL

## <a name="event-grid-on-iot-edge-integration"></a>Integration von Event Grid in IoT Edge

> [!CAUTION]
> Die Integration von Event Grid in IoT Edge ist als Vorschauversion verfügbar.

Dieses Azure Blob Storage in IoT Edge-Modul ermöglicht jetzt die Integration von Event Grid in IoT Edge. Ausführliche Informationen zu dieser Integration finden Sie im [Tutorial zum Bereitstellen der Module, Veröffentlichen von Ereignissen und Überprüfen der Ereignisbereitstellung](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Versionsinformationen

Dies sind die [Versionshinweise im Docker-Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) für dieses Modul. Möglicherweise finden Sie in den Versionshinweisen zu einer bestimmten Version weitere Informationen zur Fehlerbehebungen und Wartung.

## <a name="suggestions"></a>Vorschläge

Ihr Feedback ist uns wichtig und trägt dazu bei, den Nutzen und die Benutzerfreundlichkeit dieses Moduls und seiner Features zu verbessern. Übermitteln Sie uns Ihr Feedback, und lassen Sie uns wissen, was wir verbessern können.

Sie erreichen uns unter absiotfeedback@microsoft.com.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Bereitstellen von Azure Blob Storage auf IoT Edge](how-to-deploy-blob.md).

Bleiben Sie in Bezug auf die neuesten Updates und Ankündigung im [Blog zu Azure Blob Storage in IoT Edge](https://aka.ms/abs-iot-blogpost) auf dem Laufenden.
