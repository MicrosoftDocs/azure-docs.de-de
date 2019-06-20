---
title: Speichern von Blockblobs auf Geräten – Azure IoT Edge | Microsoft-Dokumentation
description: Enthält grundlegende Informationen zu Features für Tiering und Gültigkeitsdauer, zu unterstützten Blobspeichervorgängen und zur Verbindungsherstellung mit Ihrem Blobspeicherkonto.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991465"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Speichern von Daten am Edge mit Azure Blob Storage in IoT Edge (Vorschau)

Mit Azure Blob Storage in IoT Edge erhalten Sie eine [Blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)-Speicherlösung im Edgebereich. Ein Blob Storage-Modul auf Ihrem IoT Edge-Gerät verhält sich wie ein Azure-Blockblob-Dienst, aber die Blockblobs werden lokal auf Ihrem IoT Edge-Gerät gespeichert. Sie können mit den gleichen Azure Storage SDK-Methoden oder Blockblob-API-Aufrufen auf Ihre Blobs zugreifen, mit denen Sie bereits arbeiten.

> [!NOTE]
> Azure Blob Storage in IoT Edge ist als [öffentliche Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verfügbar.

Dieses Modul verfügt über Features für **Tiering** und **Gültigkeitsdauer** (Time-to-Live, TTL).

> [!NOTE]
> Die Funktionen für Tiering und Gültigkeitsdauer sind derzeit nur in Linux AMD64 und Linux ARM32 verfügbar.

**Tiering** ist eine konfigurierbare Funktion. Sie ermöglicht das automatische Hochladen der Daten aus Ihrem lokalen Blobspeicher in Azure und unterstützt sporadische Internetkonnektivität. Die Funktion ermöglicht Folgendes:

- Aktivieren/Deaktivieren des Tiering-Features
- Auswählen der Reihenfolge, in der die Daten in Azure kopiert werden sollen (beispielsweise „NewestFirst“ oder „OldestFirst“)
- Angeben des Azure Storage-Kontos, in das Ihre Daten hochgeladen werden sollen
- Angeben der Container, die Sie in Azure hochladen möchten. Mit diesem Modul können Sie sowohl Quell- als auch Zielcontainernamen angeben.
- Vollständiges Blob-Tiering (mithilfe des Vorgangs `Put Blob`) sowie Tiering auf Blockebene (mithilfe der Vorgänge `Put Block` und `Put Block List`)

Dieses Modul verwendet Tiering auf Blockebene, wenn Ihr Blob aus Blöcken besteht. Im Anschluss finden Sie einige gängige Szenarien:

- Wenn Ihre Anwendung einige Blöcke eines zuvor hochgeladenen Blobs aktualisiert, lädt dieses Modul nicht das gesamte Blob hoch, sondern nur die aktualisierten Blöcke.
- Falls beim Hochladen eines Blobs die Internetverbindung unterbrochen wird, lädt das Modul ebenfalls nur die restlichen Blöcke und nicht das gesamte Blob hoch, nachdem die Verbindung wiederhergestellt wurde.

Im Falle einer unerwarteten Prozessbeendigung während eines Blob-Uploads (etwa durch einen Stromausfall) werden alle Blöcke, die für den Upload vorgesehen waren, erneut hochgeladen, wenn das Modul wieder online ist.

Die **Gültigkeitsdauer** (Time-to-Live, TTL) ist eine konfigurierbare Funktion, bei der das Modul Ihre Blobs automatisch aus dem lokalen Speicher löscht, wenn die angegebene Dauer (in Minuten) abläuft. Die Gültigkeitsdauer ermöglicht Folgendes:

- Aktivieren/Deaktivieren des Tiering-Features
- Angeben der Gültigkeitsdauer in Minuten

Szenarien, in denen Daten wie Videos, Bilder, Finanzdaten, Krankenhausdaten oder beliebige andere lokal gespeicherte Daten verwendet werden, die lokal verarbeitet oder in die Cloud übertragen werden sollen, sind gute Beispiele für die Nutzung dieses Moduls.

Dieser Artikel enthält Anweisungen zum Bereitstellen von Azure Blob Storage für einen IoT Edge-Container, der einen Blobdienst auf Ihrem IoT Edge-Gerät ausführt.

> [!NOTE]
> Die im Video verwendeten Begriffe „Automatisches Tiering“ (Auto-Tiering) und „Automatisches Ablaufen“ (Auto-Expiration) wurden durch „Tiering“ und „Gültigkeitsdauer“ (Time-to-Live) ersetzt.

Das folgende Video enthält eine kurze Einführung:
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure IoT Edge-Gerät:

- Sie können Ihren Entwicklungscomputer oder einen virtuellen Computer als IoT Edge-Gerät verwenden, indem Sie die Schritte ausführen, die in der Schnellstartanleitung für [Linux](quickstart-linux.md)- oder [Windows](quickstart.md)-Geräte beschrieben sind.

- Azure Blob Storage im IoT Edge-Modul unterstützt die folgenden Gerätekonfigurationen:

  | Betriebssystem | Architecture |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core (Oktober-Update) | AMD64 |
  | Windows 10 Enterprise (Oktober-Update) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

Cloudressourcen:

Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) mit Standardtarif in Azure.

## <a name="tiering-and-time-to-live-properties"></a>Eigenschaften von Tiering und Gültigkeitsdauer

Verwenden Sie gewünschte Eigenschaften, um die Eigenschaften für das Tiering und die Gültigkeitsdauer festzulegen. Diese können während der Bereitstellung festgelegt oder nachträglich durch Bearbeitung des Modulzwillings ohne erneute Bereitstellung geändert werden. Es empfiehlt sich, den Modulzwilling für `reported configuration` und `configurationValidation` zu überprüfen, um sicherzustellen, dass die Werte korrekt weitergegeben werden.

### <a name="tiering-properties"></a>Eigenschaften des Tiering

Der Name dieser Einstellung lautet `tieringSettings`.

| Feld | Mögliche Werte | Erklärung |
| ----- | ----- | ---- |
| tieringOn | true, false | Dieser Wert ist standardmäßig auf `false` festgelegt. Wenn Sie das Feature aktivieren möchten, legen Sie ihn auf `true` fest.|
| backlogPolicy | NewestFirst, OldestFirst | Ermöglicht das Auswählen der Reihenfolge, in der die Daten in Azure kopiert werden. Standardwert: `OldestFirst`. Die Reihenfolge richtet sich nach dem Zeitpunkt der letzten Änderung des Blobs. |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` ist eine Verbindungszeichenfolge, die es ermöglicht, das Azure Storage-Konto anzugeben, in das Ihre Daten hochgeladen werden sollen. Geben Sie `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix` an. Fügen Sie ein geeignetes Endpunktsuffix (EndpointSuffix) von Azure für das Ziel des Datenuploads hinzu (variiert für Azure global, Azure Government und Microsoft Azure Stack). |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Ermöglicht das Angeben der Containernamen, die Sie in Azure hochladen möchten. Mit diesem Modul können Sie sowohl Quell- als auch Zielcontainernamen angeben. Falls Sie keinen Zielcontainernamen angeben, wird der Containername automatisch wie folgt zugewiesen: `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Sie können Vorlagenzeichenfolgen für den Zielcontainernamen erstellen. Weitere Informationen finden Sie in der Spalte mit den möglichen Werten. <br>* %h -> IoT Hub-Name (drei bis 50 Zeichen) <br>* %d -> IoT-Geräte-ID (ein bis 129 Zeichen) <br>* %m -> Modulname (ein bis 64 Zeichen) <br>* %c -> Quellcontainername (drei bis 63 Zeichen) <br><br>Der Containername darf maximal 63 Zeichen lang sein. Bei automatischer Zuweisung des Containernamens gilt Folgendes: Ist der Name länger als 63 Zeichen, werden die einzelnen Bestandteile (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) jeweils auf 15 Zeichen gekürzt. |

### <a name="time-to-live-properties"></a>Eigenschaften der Gültigkeitsdauer

Der Name dieser Einstellung lautet `ttlSettings`.

| Feld | Mögliche Werte | Erklärung |
| ----- | ----- | ---- |
| ttlOn | true, false | Dieser Wert ist standardmäßig auf `false` festgelegt. Wenn Sie das Feature aktivieren möchten, legen Sie ihn auf `true` fest.|
| timeToLiveInMinutes | `<minutes>` | Geben Sie die Gültigkeitsdauer in Minuten an. Nach Ablauf der Gültigkeitsdauer (TTL) löscht das Modul Ihre Blobs automatisch aus dem lokalen Speicher. |

## <a name="configure-log-files"></a>Konfigurieren von Protokolldateien

Informationen zur Konfiguration von Protokolldateien für Ihr Modul finden Sie unter den [bewährten Methoden für die Produktion](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Herstellen einer Verbindung mit Ihrem Blob Storage-Modul

Sie können mit dem Kontonamen und dem Kontoschlüssel, die Sie für Ihr Modul konfiguriert haben, auf Blob Storage auf Ihrem IoT Edge-Gerät zugreifen.

Geben Sie Ihr IoT Edge-Gerät als Blobendpunkt für Speicheranforderungen an, die Sie vornehmen. Mithilfe der IoT Edge-Geräteinformationen und dem Kontonamen, den Sie konfiguriert haben, können Sie eine [Verbindungszeichenfolge für einen bestimmten Speicherendpunkt erstellen](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

- Bei Modulen, die auf demselben Gerät bereitgestellt werden, auf dem auch das Modul „Azure Blob Storage auf IoT Edge“ ausgeführt wird, lautet der Blobendpunkt wie folgt: `http://<module name>:11002/<account name>`.
- Bei Modulen, die nicht auf dem Gerät bereitgestellt werden, auf dem das Modul „Azure Blob Storage auf IoT Edge“ ausgeführt wird, wird abhängig von Ihrem Setup einer der folgenden Blobendpunkte verwendet:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Schnellstartbeispiele für Azure Blob Storage

Die Dokumentation zu Azure Blob Storage enthält Schnellstartanleitungen, in denen Beispielcode in mehreren Sprachen bereitgestellt wird. Sie können diese Beispiele zum Testen von Azure Blob Storage in IoT Edge herunterladen, indem Sie den Blobendpunkt dahingehend ändern, dass er eine Verbindung mit Ihrem Blob Storage-Modul herstellt.

Die folgenden Schnellstartanleitungen verwenden Sprachen, die auch von IoT Edge unterstützt werden, sodass Sie sie als IoT Edge-Module zusammen mit dem Blob Storage-Modul bereitstellen können:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Herstellen einer Verbindung mit Ihrem lokalen Speicher mit Azure Storage-Explorer

Sie können **Azure Storage-Explorer** verwenden, um eine Verbindung mit Ihrem lokalen Speicherkonto herzustellen. Dies ist nur mit [Version 1.5.0 von Azure Storage-Explorer](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0) möglich.

> [!NOTE]
> Bei den folgenden Schritten können Fehler auftreten, z. B. beim Hinzufügen einer Verbindung zu einem lokalen Speicherkonto oder beim Erstellen von Containern im lokalen Speicherkonto. Dies können Sie ignorieren und aktualisieren.

1. Laden Sie den Azure Storage-Explorer herunter, und installieren Sie ihn.

1. Stellen Sie mithilfe einer Verbindungszeichenfolge eine Verbindung mit Azure Storage her.

1. Geben Sie die Verbindungszeichenfolge an: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Führen Sie die Schritte für die Verbindungsherstellung aus.

1. Erstellen Sie einen Container innerhalb Ihres lokalen Speicherkontos.

1. Beginnen Sie damit, Dateien als Blockblobs hochzuladen.
   > [!NOTE]
   > Dieses Modul unterstützt keine Seitenblobs.

1. Auf Wunsch können Sie eine Verbindung mit Ihren Azure-Speicherkonten herstellen, in die Sie die Daten hochladen. Dadurch erhalten Sie eine zentrale Ansicht, die sowohl Ihr lokales Speicherkonto als auch Ihr Azure-Speicherkonto enthält.

## <a name="supported-storage-operations"></a>Unterstützte Speichervorgänge

Blob Storage-Module in IoT Edge verwenden die gleichen Azure Storage SDKs und entsprechen der Version „2017-04-17“ der Azure Storage-API für Blockblobendpunkte. Spätere Releases hängen von den Kundenanforderungen ab.

Da nicht alle Azure Blob Storage-Vorgänge von Azure Blob Storage auf IoT Edge unterstützt werden, ist in diesem Abschnitt der jeweilige Status angegeben.

### <a name="account"></a>Konto

Unterstützt:

- Auflisten von Containern

Nicht unterstützt:

- Abrufen und Festlegen von Blobdiensteigenschaften
- Preflightüberprüfung von Blobanforderungen
- Abrufen von Statistiken zum Blobdienst
- Abrufen von Kontoinformationen

### <a name="containers"></a>Container

Unterstützt:

- Erstellen und Löschen von Containern
- Abrufen von Containereigenschaften und -metadaten
- Auflisten von Blobs
- Abrufen und Festlegen von Container-ACLs
- Festlegen von Containermetadaten

Nicht unterstützt:

- Abrufen der Lease von Containern

### <a name="blobs"></a>Blobs (in englischer Sprache)

Unterstützt:

- Festlegen, Abrufen und Löschen von Blobs
- Abrufen und Festlegen von Blobeigenschaften
- Abrufen und Festlegen von Blobmetadaten

Nicht unterstützt:

- Abrufen der Lease von Blobs
- Erstellen einer Momentaufnahme für Blobs
- Kopieren von Blobs und Abbrechen eines Blobkopiervorgangs
- Wiederherstellen von Blobs
- Festlegen des Blobtarifs

### <a name="block-blobs"></a>Blockblobs

Unterstützt:

- Festlegen von Blocks
- Festlegen und Abrufen von Blocklisten

Nicht unterstützt:

- Festlegen von Blocks über die URL

## <a name="feedback"></a>Feedback

Ihr Feedback ist uns wichtig und trägt dazu bei, den Nutzen und die Benutzerfreundlichkeit dieses Moduls und seiner Features zu verbessern. Übermitteln Sie uns Ihr Feedback, und lassen Sie uns wissen, was wir verbessern können.

Sie erreichen uns unter absiotfeedback@microsoft.com.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über das [Bereitstellen von Azure Blob Storage auf IoT Edge](how-to-deploy-blob.md).
