---
title: Streamen von Daten als Eingabe in Azure Stream Analytics
description: Hier erfahren Sie, wie eine Datenverbindung mit Stream Analytics eingerichtet wird. Eingaben umfassen einen Datenstrom von Ereignissen und Verweisdaten.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 649abf6d07a95c7f20f6416f7d3155f8d115782b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127568"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Streamen von Daten als Eingabe in Stream Analytics

Stream Analytics verfügt über eine erstklassige Integration in Azure-Datenströme als Eingaben aus drei Arten von Ressourcen:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Diese Eingaberessourcen können aus demselben Azure-Abonnement wie dem Ihres Stream Analytics-Auftrags oder aus einem anderen Abonnement stammen.

### <a name="compression"></a>Komprimierung

Stream Analytics unterstützt die Komprimierung für alle Datenstrom-Eingabequellen. Die folgenden Komprimierungstypen werden unterstützt: Keine, GZip- und Deflate-Komprimierung. Die Unterstützung der Komprimierung steht für Referenzdaten nicht zur Verfügung. Wenn das Eingabeformat komprimierte Avro-Daten sind, werden sie transparent behandelt. Sie müssen bei der Avro-Serialisierung keinen Komprimierungstyp angeben. 

## <a name="create-edit-or-test-inputs"></a>Erstellen, Bearbeiten oder Testen von Eingaben

Mit dem [Azure-Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md) und [Visual Studio Code](quick-create-visual-studio-code.md) können Sie Eingaben hinzufügen und anzeigen oder vorhandene Eingaben für Ihren Streamingauftrag bearbeiten. Sie können auch Tests für Eingabeverbindungen und [Abfragen](stream-analytics-manage-job.md#test-your-query) anhand von Beispieldaten über das Azure-Portal, [Visual Studio](stream-analytics-vs-tools-local-run.md) und [Visual Studio Code](visual-studio-code-local-run.md) ausführen. Beim Schreiben einer Abfrage listen Sie die Eingabe in der FROM-Klausel auf. Sie erhalten die Liste mit den verfügbaren Eingaben im Portal über die Seite **Abfrage**. Wenn Sie mehrere Eingaben verwenden möchten, können Sie sie per `JOIN` verknüpfen oder mehrere `SELECT`-Abfragen schreiben.


## <a name="stream-data-from-event-hubs"></a>Streamen von Daten aus Event Hubs

Azure Event Hubs sind hoch skalierbare Ereigniserfasser zum Veröffentlichen/Abonnieren. Ein Event Hub kann Millionen von Ereignissen pro Sekunde erfassen. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Event Hubs und Stream Analytics stellen zusammen eine End-to-End-Lösung für Echtzeitanalysen dar. Event Hubs ermöglichen es Ihnen, Ereignisse in Echtzeit an Azure zu übergeben, sodass Stream Analytics-Aufträge diese Ereignisse in Echtzeit verarbeiten können. Beispielsweise können Sie Webklicks, Sensormesswerte oder Onlineprotokollereignisse an Event Hubs senden. Anschließend können Sie Stream Analytics-Aufträge erstellen, um Event Hubs als Eingabedatenströme zum Filtern, Aggregieren und Korrelieren in Echtzeit zu verwenden.

`EventEnqueuedUtcTime` ist der Zeitstempel für die Ankunft eines Ereignisses in einem Event Hub und der Standardzeitstempel für Ereignisse aus Event Hubs für Stream Analytics. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) verwenden.

### <a name="event-hubs-consumer-groups"></a>Event Hubs-Consumergruppen

Sie sollten für jede Event Hub-Eingabe in Stream Analytics eine eigene Consumergruppe konfigurieren. Wenn ein Auftrag eine Selbstverknüpfung oder mehrere Eingaben enthält, können einige Eingaben unter Umständen von mehreren nachgeschalteten Lesern gelesen werden. Dies wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Zur Vermeidung der Überschreitung des Event Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben. Darüber hinaus gilt ein Grenzwert von 20 Consumergruppen für einen Event Hub im Standard-Tarif. Weitere Informationen finden Sie unter [Troubleshoot input connections](stream-analytics-troubleshoot-input.md) (Problembehandlung für Eingabeverbindungen).

### <a name="create-an-input-from-event-hubs"></a>Erstellen einer Eingabe aus Event Hubs

In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, um Dateneingaben aus einem Event Hub zu streamen:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** |Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| **Abonnement** | Wählen Sie das Abonnement, in dem die Event Hub-Ressource vorhanden ist. | 
| **Event Hub-Namespace** | Ein Event Hub-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung einer neuen Event Hub-Instanz auch den Namespace erstellt. |
| **Event Hub-Name** | Der Name des Event Hubs, der als Eingabe verwendet wird. |
| **Event Hub-Richtlinienname** | Die SAS-Richtlinie, die Zugriff auf den Event Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Diese Option wird automatisch ausgefüllt, sofern Sie nicht die Option zum manuellen Festlegen der Event-Hub-Einstellungen wählen.|
| **Event Hub-Consumergruppe** (empfohlen) | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden. Diese Zeichenfolge identifiziert die Consumergruppe, die zum Erfassen von Daten aus dem Event Hub verwendet werden soll. Wenn keine Consumergruppe angegeben wird, verwendet der Stream Analytics-Auftrag die $Default-Consumergruppe.  |
| **Partitionsschlüssel** | Dies ist ein optionales Feld, das nur verfügbar ist, wenn Ihr Auftrag so konfiguriert ist, dass er den [Kompatibilitätsgrad](./stream-analytics-compatibility-level.md) 1.2 oder höher verwendet. Wenn Ihre Eingabe durch eine Eigenschaft partitioniert wird, können Sie den Namen dieser Eigenschaft hier hinzufügen. Dies wird verwendet, um die Leistung Ihrer Abfrage zu verbessern, wenn sie eine PARTITION BY- oder GROUP BY-Klausel für diese Eigenschaft enthält. Wenn dieser Auftrag den Kompatibilitätsgrad 1.2 oder höher verwendet, wird dieses Feld standardmäßig auf „PartitionId“ festgelegt. |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV, Avro, oder [Sonstige (Protobuf, XML, Proprietär...)](custom-deserializer.md)) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| **Typ der Ereigniskomprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |

Wenn Ihre Daten aus einer Event Hub-Datenstromeingabe stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **EventProcessedUtcTime** |Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| **EventEnqueuedUtcTime** |Das Datum und die Uhrzeit des Ereignisempfangs durch die Event Hubs. |
| **PartitionId** |Die nullbasierte Partitions-ID für den Eingabeadapter. |

Beispielsweise können Sie anhand dieser Felder eine wie im folgenden Beispiel gezeigte Abfrage schreiben:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Bei Verwendung von Event Hub als Endpunkt für IoT Hub-Routen können Sie mithilfe der [GetMetadataPropertyValue-Funktion](/stream-analytics-query/getmetadatapropertyvalue) auf die IoT Hub-Metadaten zugreifen.
> 

## <a name="stream-data-from-iot-hub"></a>Streamen von Daten aus IoT Hub

Azure IoT Hub ist ein hochgradig skalierbares Erfassungsmodul für das Veröffentlichen und Abonnieren von Ereignissen, das für IoT-Szenarien optimiert ist.

Der Standardzeitstempel von Ereignissen, die von IoT Hub in Stream Analytics stammen, ist der Zeitstempel, an dem das Ereignis in IoT Hub eingeht, also `EventEnqueuedUtcTime`. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) verwenden.

### <a name="iot-hub-consumer-groups"></a>IoT Hub-Consumergruppen

Sie sollten für jede IoT Hub-Eingabe in Stream Analytics eine eigene Consumergruppe konfigurieren. Wenn ein Auftrag eine Selbstverknüpfung oder mehrere Eingaben enthält, können einige Eingaben möglicherweise von mehreren nachgeschalteten Lesern gelesen werden. Dies wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Zur Vermeidung der Überschreitung des Azure IoT Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurieren von IoT Hub als Datenstromeingabe

In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, wenn Sie IoT Hub als Datenstromeingabe konfigurieren.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.|
| **Abonnement** | Wählen Sie das Abonnement, in dem die IoT Hub-Ressource vorhanden ist. | 
| **IoT Hub** | Der Name von IoT Hub, der als Eingabe verwendet wird. |
| **Endpunkt** | Der Endpunkt für IoT Hub.|
| **Name der SAS-Richtlinie** | Die SAS-Richtlinie, die Zugriff auf IoT Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| **Schlüssel für SAS-Richtlinie** | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf IoT Hub verwendet wird.  Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der IoT Hub-Einstellungen. |
| **Consumergruppe** | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine andere Consumergruppe zu verwenden. Die Consumergruppe, die zum Erfassen von Daten aus IoT Hub verwendet werden soll. Stream Analytics verwendet die $Default-Consumergruppe, sofern nicht anders angegeben.  |
| **Partitionsschlüssel** | Dies ist ein optionales Feld, das nur verfügbar ist, wenn Ihr Auftrag so konfiguriert ist, dass er den [Kompatibilitätsgrad](./stream-analytics-compatibility-level.md) 1.2 oder höher verwendet. Wenn Ihre Eingabe durch eine Eigenschaft partitioniert wird, können Sie den Namen dieser Eigenschaft hier hinzufügen. Dies wird verwendet, um die Leistung Ihrer Abfrage zu verbessern, wenn sie eine PARTITION BY- oder GROUP BY-Klausel für diese Eigenschaft enthält. Wenn dieser Auftrag den Kompatibilitätsgrad 1.2 oder höher verwendet, wird dieses Feld standardmäßig auf „PartitionId“ festgelegt. |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV, Avro, oder [Sonstige (Protobuf, XML, Proprietär...)](custom-deserializer.md)) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| **Typ der Ereigniskomprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |


Wenn Ihre gestreamten Daten aus IoT Hub stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **EventProcessedUtcTime** | Das Datum und die Uhrzeit der Verarbeitung des Ereignisses. |
| **EventEnqueuedUtcTime** | Das Datum und die Uhrzeit des Ereignisempfangs durch den IoT Hub. |
| **PartitionId** | Die nullbasierte Partitions-ID für den Eingabeadapter. |
| **IoTHub.MessageId** | Eine ID, die zum Korrelieren einer bidirektionalen Kommunikation in IoT Hub verwendet wird. |
| **IoTHub.CorrelationId** | Eine ID, die in Nachrichtenantworten und im Feedback in IoT Hub verwendet wird. |
| **IoTHub.ConnectionDeviceId** | Die Authentifizierung-ID, die zum Senden dieser Nachricht verwendet wird. Dieser Wert wird auf servicebound-Nachrichten von IoT Hub gestempelt. |
| **IoTHub.ConnectionDeviceGenerationId** | Die Generierungs-ID des authentifizierten Geräts, das zum Senden dieser Nachricht verwendet wurde. Dieser Wert wird auf servicebound-Nachrichten von IoT Hub gestempelt. |
| **IoTHub.EnqueuedTime** | Der Zeitpunkt, an dem die Nachricht durch IoT Hub empfangen wurde. |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>Streamen von Daten aus Blobspeicher oder Data Lake Storage Gen2
Für Szenarien mit großen Mengen unstrukturierter Daten, die in der Cloud gespeichert werden sollen, bietet der Azure-Blobspeicher oder Azure Data Lake Storage Gen2 (ADLS Gen2) eine kostengünstige und skalierbare Lösung. Daten im Blobspeicher oder ADLS Gen2 werden im Allgemeinen als „ruhende“ Daten angesehen. Diese Daten können aber als Datenstrom von Stream Analytics verarbeitet werden. 

Ein häufig verwendetes Szenario für solche Eingaben bei Stream Analytics ist die Protokollverarbeitung. In diesem Szenario wurden Telemetriedatendateien von einem System erfasst, die zur Extraktion aussagekräftiger Daten analysiert und verarbeitet werden müssen.

Der Standardzeitstempel von Blobspeicher- oder ADLS Gen2-Ereignissen in Stream Analytics ist der Zeitstempel, an dem es zuletzt geändert wurde, also `BlobLastModifiedUtcTime`. Wenn ein Blob um 13:00 Uhr auf ein Speicherkonto hochgeladen wird und der Azure Stream Analytics-Auftrag mit der Option *Jetzt* um 13:01 Uhr gestartet wird, wird es nicht abgeholt, da seine geänderte Zeit außerhalb des Zeitraums für die Auftragsausführung liegt.

Wenn ein Blob um 13:00 Uhr in einen Speicherkontocontainer hochgeladen wird und der Azure Stream Analytics-Auftrag mit der Option *Benutzerdefinierte Uhrzeit* um 13:00 Uhr oder früher gestartet wird, wird der Blob abgeholt, da seine geänderte Zeit in den Zeitraum für die Auftragsausführung fällt.

Wenn ein Azure Stream Analytics-Auftrag mit der Option *Jetzt* um 13:00 Uhr gestartet wird und ein Blob um 13:01 Uhr in den Speicherkontocontainer hochgeladen wird, holt Azure Stream Analytics den Blob ab. Der jedem Blob zugewiesene Zeitstempel basiert nur auf `BlobLastModifiedTime`. Der Ordner, in dem sich das Blob befindet, hat keine Beziehung zum zugewiesenen Zeitstempel. Wenn es beispielsweise einen Blob *2019/10-01/00/b1.txt* mit einer `BlobLastModifiedTime` von 2019-11-11 gibt, lautet der dem Blob zugewiesene Zeitstempel 2019-11-11.

Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](/stream-analytics-query/stream-analytics-query-language-reference) verwenden. Bei einem Stream Analytics-Auftrag werden Daten jede Sekunde per Pullvorgang aus der Azure-Blobspeicher- oder ADLS Gen2-Eingabe abgerufen, wenn die Blobdatei verfügbar ist. Falls die Blobdatei nicht verfügbar ist, kommt es zu einem exponentiellen Backoff mit einer maximalen Zeitverzögerung von 90 Sekunden.

Eingaben im CSV-Format müssen über eine Überschriftenzeile verfügen, um Felder für das Dataset zu definieren. Alle Felder der Überschriftenzeile müssen eindeutig sein.

> [!NOTE]
> Stream Analytics unterstützt das Hinzufügen von Inhalten zu einer vorhandenen Blobdatei nicht. Stream Analytics zeigt jede Datei nur einmal an. Des Weiteren werden alle Änderungen, die in der Datei vorgenommen wurden, nachdem der Auftrag die Daten gelesen hat, nicht verarbeitet. Die Methode, alle Daten für eine Blobdatei auf einmal hochzuladen und dann zusätzliche neuere Ereignisse einer anderen, neuen Blobdatei hinzuzufügen, hat sich bewährt.

In Szenarien, in denen fortlaufend viele Blobs hinzugefügt werden und Stream Analytics die Blobs beim Hinzufügen verarbeitet, kann es in seltenen Fällen vorkommen, dass einige Blobs aufgrund der Granularität von `BlobLastModifiedTime` übersprungen werden. Sie können dies entschärfen, indem Sie Blobs in einem Abstand von mindestens zwei Sekunden hochladen. Wenn diese Option nicht praktikabel ist, können Sie Event Hubs verwenden, um große Mengen von Ereignissen zu streamen.

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurieren von Blob Storage als Datenstromeingabe 

In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, wenn Sie Blob Storage als Datenstromeingabe konfigurieren.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| **Abonnement** | Wählen Sie das Abonnement, in dem die Speicherressource vorhanden ist. | 
| **Speicherkonto** | Der Name des Speicherkontos an, in dem sich die Blobdateien befinden. |
| **Speicherkontoschlüssel** | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der Einstellungen. |
| **Container** | Container bieten eine logische Gruppierung für Blobs. Sie können entweder **Vorhandenes Element verwenden** oder **Neues Element erstellen** wählen, um einen neuen Container zu erstellen.|
| **Pfadmuster** (optional) | Der Dateipfad, der verwendet wird, um die Blobs im angegebenen Container zu suchen. Wenn Sie Blobs aus dem Containerstamm lesen möchten, legen Sie kein Pfadmuster fest. In dem Pfad können Sie mindestens eine Instanz der folgenden drei Variablen angeben: `{date}`, `{time}` oder `{partition}`.<br/><br/>Beispiel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Beispiel 2: `cluster1/logs/{date}`<br/><br/>Das Zeichen `*` ist kein zulässiger Wert für das Pfadpräfix. Es sind nur gültige <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Azure Blob-Zeichen</a> zulässig. Schließen Sie keine Containernamen oder Dateinamen ein. |
| **Datumsformat** (optional) | Wenn Sie die Datumsvariable im Pfad verwenden, wird das Datumsformat, in dem die Dateien organisiert sind, verwendet. Beispiel: `YYYY/MM/DD` <br/><br/> Wenn Blobeingaben `{date}` oder `{time}` in ihrem Pfad aufweisen, werden die Ordner in zeitlich aufsteigender Reihenfolge untersucht.|
| **Zeitformat** (optional) |  Wenn Sie die Zeitvariable im Pfad verwenden, wird das Zeitformat, in dem die Dateien organisiert sind, verwendet. Der einzige derzeit unterstützte Wert ist `HH` für Stunden. |
| **Partitionsschlüssel** | Dies ist ein optionales Feld, das nur verfügbar ist, wenn Ihr Auftrag so konfiguriert ist, dass er den [Kompatibilitätsgrad](./stream-analytics-compatibility-level.md) 1.2 oder höher verwendet. Wenn Ihre Eingabe durch eine Eigenschaft partitioniert wird, können Sie den Namen dieser Eigenschaft hier hinzufügen. Dies wird verwendet, um die Leistung Ihrer Abfrage zu verbessern, wenn sie eine PARTITION BY- oder GROUP BY-Klausel für diese Eigenschaft enthält. Wenn dieser Auftrag den Kompatibilitätsgrad 1.2 oder höher verwendet, wird dieses Feld standardmäßig auf „PartitionId“ festgelegt. |
| **Anzahl von Eingabepartitionen** | Dieses Feld ist nur vorhanden, wenn {partition} im Pfadmuster vorhanden ist. Der Wert dieser Eigenschaft ist eine ganze Zahl >=1. Wo immer {partition} in „pathPattern“ auftritt, wird eine Zahl zwischen 0 und dem Wert dieses Felds minus 1 verwendet. |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV, Avro, oder [Sonstige (Protobuf, XML, Proprietär...)](custom-deserializer.md)) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| **Komprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |

Wenn Ihre Daten aus einer Blob Storage-Quelle stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **BlobName** |Der Name des Eingabe-Blobs, aus dem das Ereignis stammt. |
| **EventProcessedUtcTime** |Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| **BlobLastModifiedUtcTime** |Das Datum und die Uhrzeit der letzten Änderung des Blobs. |
| **PartitionId** |Die nullbasierte Partitions-ID für den Eingabeadapter. |

Beispielsweise können Sie anhand dieser Felder eine wie im folgenden Beispiel gezeigte Abfrage schreiben:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/