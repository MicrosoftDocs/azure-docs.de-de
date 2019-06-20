---
title: Datenspeicherung und -eingang in Azure Time Series Insights Preview | Microsoft-Dokumentation
description: Grundlagen zur Datenspeicherung und zum Dateneingang in Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.custom: seodec18
ms.openlocfilehash: 33ca86fc2d13fb7f6e29c43e9a7c1d2dc6ef4169
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755239"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Datenspeicherung und -eingang in Azure Time Series Insights Preview

In diesem Artikel werden Änderungen bei Datenspeicherung und Dateneingang in Azure Time Series Insights Preview beschrieben. Es werden die zugrunde liegende Speicherstruktur, das Dateiformat und die „Time Series-ID“-Eigenschaft besprochen. Außerdem wird der zugrunde liegende Dateneingangsprozess, der Durchsatz und Einschränkungen erläutert.

## <a name="data-storage"></a>Datenspeicher

Wenn Sie eine Time Series Insights Preview-SKU-Umgebung mit nutzungsbasierter Bezahlung erstellen, erstellen Sie zwei Ressourcen:

* Eine Time Series Insights-Umgebung.
* Ein Azure-Speicherkonto vom Typ „Allgemein v1“, in dem die Daten gespeichert werden.

Die Time Series Insights Preview verwendet Azure-Blobspeicher mit dem Parquet-Dateityp. Time Series Insights verwaltet alle Datenvorgänge, einschließlich der Erstellung von Blobs sowie der Indizierung und Partitionierung der Daten im Azure-Speicherkonto. Sie erstellen diese Blobs, indem Sie ein Azure-Speicherkonto verwenden.

Wie andere Azure-Speicherblobs auch, gestatten Ihnen von Time Series Insights erstellte Blobs, sie zu lesen und darin zu schreiben, um verschiedene Integrationsszenarien zu unterstützen.

> [!TIP]
> Die Leistung von Time Series Insights kann beeinträchtigt werden, wenn Sie zu häufig Ihre Blobs lesen oder darin schreiben.

Eine Übersicht über Azure-Blobspeicher finden Sie unter [Einführung in Speicherblobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Weitere Informationen zum Parquet-Dateityp finden Sie unter [Unterstützte Dateitypen in Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

## <a name="parquet-file-format"></a>Parquet-Dateiformat

Parquet ist ein spaltenorientiertes Datendateiformat, das unter folgenden Aspekten entwickelt wurde:

* Interoperabilität
* Platzeffizienz
* Abfrageeffizienz

Time Series Insights hat Parquet ausgewählt, weil es eine effiziente Datenkomprimierung und Codierungsschemas mit verbesserter Leistung bietet, die komplexe Massendaten verarbeiten können.

Ein besseres Verständnis des Parquet-Dateiformats finden Sie in der [Parquet-Dokumentation](https://parquet.apache.org/documentation/latest/).

### <a name="event-structure-in-parquet"></a>Ereignisstruktur in Parquet

Time Series Insights erstellt und speichert Kopien von Blobs in den beiden folgenden Formaten:

1. Die erste, ursprüngliche Kopie, wird nach Ankunftszeit partitioniert:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Bloberstellungszeit für nach Ankunftszeit partitionierte Blobs.

1. Die zweite, neu partitionierte Kopie wird mittels einer dynamischen Gruppierung der Times Series-ID partitioniert:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Mindestereignis-Zeitstempel in einem Blob für nach Time Series-ID partitionierte Blobs.

> [!NOTE]
> * `<YYYY>` entspricht einer 4-stelligen Jahresdarstellung.
> * `<MM>` entspricht einer 2-stelligen Monatsdarstellung.
> * `<YYYYMMDDHHMMSSfff>` entspricht einer Zeitstempeldarstellung mit 4-stelligem Jahr (`YYYY`), 2-stelligem Monat (`MM`), 2-stelligem Tag (`DD`), 2-stelliger Stunde (`HH`), 2-stelliger Minute (`MM`), 2-stelliger Sekunde (`SS`) und 3-stelliger Millisekunde (`fff`).

Time Series Insights-Ereignisse werden dem Inhalt von Parquet-Dateien wie folgt zugeordnet:

* Jedes Ereignis wird einer einzelnen Zeile zugeordnet.
* Integrierte **Zeitstempel**-Spalte mit einem Ereigniszeitstempel. Die Timestamp-Eigenschaft ist nie null. Ihr Standardwert ist der **Zeitpunkt der Einreihung der Ereignisquelle in die Warteschlange**, wenn die Timestamp-Eigenschaft in der Ereignisquelle nicht angegeben ist. Der Zeitstempel ist in UTC. 
* Alle anderen Eigenschaften, die Spalten zugeordnet werden, enden mit `_string` (Zeichenfolge), `_bool` (boolesch), `_datetime` (Datum/Uhrzeit) und `_double` (double), je nach Eigenschaftentyp.
* Dies ist das Zuordnungsschema für die erste Version des Dateiformats, auf die wir als **V=1** verweisen. Mit der Weiterentwicklung dieses Features wird der Name zu **V=2**, **V=3** usw. hochgezählt.

## <a name="partitions"></a>Partitionen

Jede Time Series Insights Preview-Umgebung benötigt eine **Time Series ID**-Eigenschaft und eine **Timestamp**-Eigenschaft, die sie eindeutig identifizieren. Ihre Time Series-ID fungiert als logische Partition für Ihre Daten und bietet der Time Series Insights Preview-Umgebung eine natürliche Grenze für die Verteilung von Daten über physische Partitionen hinweg. Die Verwaltung physischer Partitionen erfolgt durch Time Series Insights Preview in einem Azure-Speicherkonto.

Time Series Insights verwendet dynamische Partitionierung, um Speicher- und Abfrageleistung zu optimieren, indem Partitionen entfernt und neu erstellt werden. Der dynamische Partitionierungsalgorithmus von Time Series Insights Preview versucht zu verhindern, dass auf einer einzelnen physischen Partition Daten für mehrere getrennte, logische Partitionen gespeichert werden. Mit anderen Worten, der Partitionierungsalgorithmus hält alle für eine einzelne Time Series-ID spezifischen Daten, die ausschließlich in Parquet-Dateien vorhanden sind, ohne dass sie mit Daten anderer Time Series-IDs überlappen. Der dynamische Partitionierungsalgorithmus versucht außerdem, die ursprüngliche Reihenfolge der Ereignisse innerhalb einer einzelnen Time Series-ID beizubehalten.

Anfänglich, zum Eingangszeitpunkt der Daten, werden diese nach dem Zeitstempel partitioniert, sodass eine einzelne logische Partition innerhalb eines angegebenen Zeitraums auf mehrere physische Partitionen verteilt werden kann. Eine einzelne physische Partition könnte auch viele oder alle logischen Partitionen enthalten. Aufgrund von Größeneinschränkungen für Blobs kann eine einzelne logische Partition, auch bei optimaler Partitionierung, mehrere physische Partitionen enthalten.

> [!NOTE]
> Standardmäßig ist der Timestamp-Wert der *Zeitpunkt der Einreihung der Nachricht in die Warteschlange* in Ihrer konfigurierten Ereignisquelle.

Wenn Sie Verlaufsdaten oder Batchnachrichten hochladen, weisen Sie den Wert zu, den Sie mit Ihren Daten in der Timestamp-Eigenschaft speichern möchten, die dem entsprechenden Zeitstempel zugeordnet ist. Die Timestamp-Eigenschaft beachtet die Groß- und Kleinschreibung. Weitere Informationen finden Sie unter [Zeitreihenmodell](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Physische Partitionen

Eine physische Partition ist ein Blockblob, der in Ihrem Speicherkonto gespeichert ist. Die tatsächliche Größe der Blobs kann variieren, weil die Größe von der Push-Rate abhängig ist. Wir gehen jedoch davon aus, dass Blobs etwa 20 MB bis 50 MB groß sind. Aufgrund dieser Erwartung hat das Time Series Insights-Team 20 MB als die Größe zur Optimierung der Abfrageleistung ausgewählt. Diese Größe kann sich im Laufe der Zeit ändern, je nach Dateigröße und Geschwindigkeit des Dateneingangs.

> [!NOTE]
> * Blobs haben eine Größe von 20 MB.
> * Azure-Blobs werden gelegentlich zur Verbesserung der Leistung neu partitioniert, indem Sie entfernt und neu erstellt werden.
> * Außerdem können dieselben Time Series Insights-Daten in zwei oder mehr Blobs vorhanden sein.

### <a name="logical-partitions"></a>Logische Partitionen

Eine logische Partition ist eine Partition innerhalb einer physischen Partition, die alle Daten speichert, die mit einem einzelnen Partitionsschlüsselwert verknüpft sind. Die Time Series Insights Preview partitioniert jeden Blob logisch basierend auf zwei Eigenschaften:

* **Time Series-ID**: Der Partitionsschlüssel für alle Time Series Insights-Daten im Ereignisstream und Modell.
* **Timestamp** (Zeitstempel): Die Zeit, basierend auf dem ersten Eingang.

Die Time Series Insights Preview bietet leistungsstarke Abfragen, die auf diesen beiden Eigenschaften basieren. Diese beiden Eigenschaften bieten außerdem die effektivste Methode zur schnellen Bereitstellung von Time Series Insights-Daten.

Es ist wichtig, eine entsprechende Time Series-ID auszuwählen, weil es sich dabei um eine unveränderliche Eigenschaft handelt. Weitere Informationen finden Sie unter [Auswählen von Time Series-IDs](./time-series-insights-update-how-to-id.md).

## <a name="azure-storage"></a>Azure-Speicher

### <a name="your-storage-account"></a>Ihr Speicherkonto

Wenn Sie eine Time Series Insights-Umgebung mit nutzungsbasierter Bezahlung erstellen, erstellen Sie zwei Ressourcen: eine Time Series Insights-Umgebung und ein Azure-Speicherkonto vom Typ „Allgemein V1“, in dem die Daten gespeichert werden. Wir haben uns entschieden, Azure-Speicher vom Typ „Allgemein V1“ zur Standardressource zu machen aufgrund seiner Interoperabilität, des Preises und der Leistung. 

Time Series Insights veröffentlicht bis zu zwei Kopien jedes Ereignisses in Ihrem Azure-Speicherkonto. Die ursprüngliche Kopie wird immer beibehalten, sodass Sie sie schnell mithilfe anderer Dienste abfragen können. Sie können ganz einfach Spark, Hadoop und andere vertraute Tools mit Time Series-IDs und Parquet-Rohdateien verwenden, da diese Engines grundlegende Dateinamensfilterung unterstützen. Das Gruppieren von Blobs nach Jahr und Monat ist eine nützliche Möglichkeit zum Auflisten von Blobs innerhalb eines bestimmten Zeitraums für einen benutzerdefinierten Auftrag. 

Darüber hinaus partitioniert Time Series Insights die Parquet-Dateien neu, um eine Optimierung für die Time Series Insights-APIs vorzunehmen. Die zuletzt neu partitionierte Datei wird außerdem gespeichert.

Während der öffentlichen Preview werden Daten mit unbegrenzter Dauer in Ihrem Azure-Speicherkonto gespeichert.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Schreiben und Bearbeiten von Time Series Insights-Blobs

Um Abfrageleistung und Datenverfügbarkeit sicherzustellen, bearbeiten oder löschen Sie keine Blobs, die von Time Series Insights erstellt wurden.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Zugreifen auf und Exportieren von Daten aus Time Series Insights Preview

Sie können auf im Time Series Insights Preview-Explorer gespeicherte Daten zugreifen, um sie in Verbindung mit anderen Diensten zu verwenden. Beispielsweise könnten Sie mit Ihren Daten Berichte in Power BI erstellen, mit den Daten maschinelles Lernen mittels Azure Machine Learning Studio durchführen oder sie in einer Notizbuchanwendung mit Jupyter Notebooks verwenden.

Sie können auf drei allgemeine Arten auf Ihre Daten zugreifen:

* Über den Time Series Insights Preview-Explorer: Sie können Daten aus dem Time Series Insights Preview-Explorer als CSV-Datei exportieren. Weitere Informationen finden Sie unter [Time Series Insights Preview-Explorer](./time-series-insights-update-explorer.md).
* Über die Time Series Insights Preview-APIs: Der API-Endpunkt ist erreichbar unter `/getRecorded`. Weitere Informationen zu dieser API finden Sie unter [Zeitreihenabfrage](./time-series-insights-update-tsq.md).
* Direkt aus einem Azure-Speicherkonto (unten).

#### <a name="from-an-azure-storage-account"></a>Aus einem Azure-Speicherkonto

* Sie benötigen Lesezugriff auf das jeweilige Konto, das Sie für den Zugriff auf Ihre Time Series Insights-Daten verwenden. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Ihre Speicherkontoressourcen](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).
* Informationen zu direkten Methoden zum Lesen von Daten aus dem Azure-Blobspeicher finden Sie unter [Verschieben von Daten in und aus Ihrem Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* So exportieren Sie Daten aus einem Azure-Speicherkonto
    * Stellen Sie zunächst sicher, dass Ihr Konto die notwendigen Anforderungen zum Exportieren von Daten erfüllt. Weitere Informationen finden Sie unter [Anforderungen für den Speicherimport und -export](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).
    * Informationen zu anderen Möglichkeiten zum Exportieren von Daten aus Ihrem Azure-Speicherkonto finden Sie unter [Importieren und Exportieren von Daten aus Blobs](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Löschen von Daten

Löschen Sie keine Blobs. Sie sind nicht nur nützlich für die Überwachung und Speicherung eines Datensatzes Ihrer Daten, sondern die Time Series Insights Preview speichert auch Blobmetadaten innerhalb jedes Blobs.

## <a name="time-series-insights-data-ingress"></a>Time Series Insights-Dateneingang

### <a name="ingress-policies"></a>Eingangsrichtlinien

Die Time Series Insights Preview unterstützt dieselben Ereignisquellen und Dateitypen, die aktuell von Time Series Insights unterstützt werden.

Unterstützte Ereignisquellen sind unter anderem:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Azure Event Hub-Instanzen unterstützen Kafka.

Unterstützte Dateitypen sind unter anderem:

* JSON: Weitere Informationen zu den unterstützten JSON-Formen, die wir verarbeiten können, finden Sie unter [Formen von JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Datenverfügbarkeit

Die Time Series Insights Preview indiziert Daten mithilfe einer Optimierungsstrategie für die Blobgröße. Daten werden nach der Indizierung für Abfragen verfügbar, wobei die Indizierung davon abhängt, wie viele Daten eingehen und mit welcher Geschwindigkeit.

> [!IMPORTANT]
> * Das allgemeine Verfügbarkeitsrelease von Time Series Insights stellt Daten innerhalb von 60 Sekunden nach dem Erreichen einer Ereignisquelle zur Verfügung. 
> * Während der Vorschauphase (Preview) sollten Sie mit einem längeren Zeitraum rechnen, bis die Daten verfügbar gemacht werden.
> * Wenn es zu merklichen Wartezeiten kommt, wenden Sie sich in jedem Fall an uns.

### <a name="scale"></a>Skalieren

Die Time Series Insights Preview unterstützt eine anfängliche Dateneingangsskalierung von bis zu 1 Mbit/s pro Umgebung. Verbesserte Skalierungsunterstützung erfolgt fortwährend. Wir planen, unsere Dokumentation mit diesen Verbesserungen entsprechend zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Speicherung und Dateneingang in Azure Time Series Insights Preview](./time-series-insights-update-storage-ingress.md).

- Lesen Sie über die neue [Datenmodellierung](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
