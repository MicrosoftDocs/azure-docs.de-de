---
title: 'Erfassen von Streamingereignissen: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel gibt einen Überblick über die Capture-Funktion, mit der Sie Ereignisse erfassen können, die Azure Event Hubs durchlaufen.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ee64d25afcb2be73bed26c6c4dec87f216a0fd82
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383874"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Erfassen von Ereignissen über Azure Event Hubs in Azure Blob Storage oder Azure Data Lake Storage
Azure Event Hubs ermöglicht Ihnen die automatische Erfassung von Streamingdaten in Event Hubs in einem [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- oder [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-Konto Ihrer Wahl. Um für mehr Flexibilität zu sorgen, ist dabei die Angabe eines Zeit- oder Größenintervalls möglich. Das Einrichten von Capture geht schnell, für das Ausführen fallen keine Verwaltungskosten an, und die Skalierung erfolgt automatisch mit den [Event Hub-Durchsatzeinheiten](event-hubs-features.md#throughput-units). Event Hubs Capture ist die einfachste Möglichkeit zum Laden von Streamingdaten in Azure und ermöglicht es Ihnen, sich auf die Datenverarbeitung anstatt auf die Datenerfassung zu konzentrieren.

Event Hubs Capture ermöglicht Ihnen das Verarbeiten von Echtzeitpipelines und batchbasierten Pipelines für den gleichen Datenstrom. Dies bedeutet, dass Sie Lösungen erstellen können, die im Laufe der Zeit gemäß Ihren veränderten Anforderungen mitwachsen. Ob Sie gerade batchbasierte Systeme mit Blick auf zukünftige Echtzeitverarbeitung erstellen oder einer vorhandenen Echtzeitlösung effiziente kalte Daten hinzufügen möchten – Event Hubs Capture erleichtert Ihnen das Arbeiten mit Streamingdaten.

> [!NOTE]
> Die Event Hubs Capture-Funktion unterstützt zurzeit nur Gen 1 von Azure Data Lake Store, nicht Gen 2. 

## <a name="how-event-hubs-capture-works"></a>Funktionsweise von Event Hubs Capture

Event Hubs ist ein beständiger Puffer mit zeitbasierter Speicherung für Telemetrieeingänge, vergleichbar mit einem verteilten Protokoll. Der Schlüssel zur Skalierung in Event Hubs ist das [partitionierte Consumermodell](event-hubs-features.md#partitions). Jede Partition ist ein unabhängiges Datensegment und wird unabhängig genutzt. Mit der Zeit werden diese Daten basierend auf der konfigurierbaren Beibehaltungsdauer ersetzt. Daher kann ein Event Hub nie „zu voll“ werden.

Mit Event Hubs Capture können Sie zum Speichern der erfassten Daten ein eigenes Azure Blob Storage-Konto und einen Container bzw. ein Azure Data Lake Store-Konto angeben. Diese Konten können sich in der gleichen Region wie Ihr Event Hub oder in einer anderen Region befinden, um die Flexibilität von Event Hubs Capture zu erhöhen.

Erfasste Daten werden im [Apache Avro][Apache Avro]-Format geschrieben. Hierbei handelt es sich um ein kompaktes, schnelles, binäres Format, das umfangreiche Datenstrukturen mit Inlineschema bietet. Dieses Format wird häufig im Hadoop-Ökosystem, von Stream Analytics und von Azure Data Factory verwendet. Weitere Informationen zum Arbeiten mit Avro finden Sie weiter unten in diesem Artikel.

### <a name="capture-windowing"></a>Capture-Fenster

Event Hubs Capture ermöglicht Ihnen das Einrichten eines Fensters zur Steuerung der Erfassung. Dieses Fenster ist eine Konfiguration mit Mindestgröße und -zeit, für die das Prinzip „First wins“ gilt. Dies bedeutet, dass der erste Trigger, der auftritt, zu einem Erfassungsvorgang führt. Wenn Sie ein Erfassungsfenster von 15 Minuten und mit 100 MB verwenden und 1 MB/s senden, wird das Größenfenster vor dem Zeitfenster ausgelöst. Jede Partition wird unabhängig erfasst und schreibt zum Erfassungszeitpunkt einen abgeschlossenen Blockblob. Dieser wird nach dem Zeitpunkt benannt, zu dem das Erfassungsintervall aufgetreten ist. Die Namenskonvention für die Speicherung lautet wie folgt:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Beachten Sie, dass die Datumswerte mit Nullen aufgefüllt werden. Ein Beispieldateiname hierfür lautet:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Skalierung auf Durchsatzeinheiten

Der Datenverkehr von Event Hubs wird von [Durchsatzeinheiten](event-hubs-features.md#throughput-units) gesteuert. Eine einzelne Durchsatzeinheit lässt eingehenden Datenverkehr von 1 MB pro Sekunde oder 1.000 Ereignisse pro Sekunde und die doppelte Menge an ausgehendem Datenverkehr zu. Event Hubs Standard kann mit 1 bis 20 Durchsatzeinheiten konfiguriert werden, und über eine [Supportanfrage][support request] für eine Kontingenterhöhung können weitere Einheiten erworben werden. Bei Überschreitung der erworbenen Durchsatzeinheiten wird die Nutzung gedrosselt. Bei Event Hubs Capture werden Daten direkt aus dem internen Event Hubs-Speicher kopiert. Dabei werden Durchsatzeinheitenkontingente für ausgehenden Datenverkehr umgangen und stattdessen für andere Verarbeitungsreader wie Stream Analytics oder Spark verwendet.

Nach der Konfiguration wird Event Hubs Capture automatisch ausgeführt, wenn Sie Ihr erstes Ereignis senden, und die Ausführung bleibt aktiv. Damit für Ihre Downstreamverarbeitung leichter erkannt wird, dass der Prozess ausgeführt wird, schreibt Event Hubs leere Dateien, wenn keine Daten vorhanden sind. Dieser Prozess sorgt für einen vorhersagbaren Rhythmus und Marker, die als Feed für Ihre Batchprozessoren fungieren.

## <a name="setting-up-event-hubs-capture"></a>Einrichten von Event Hubs Capture

Sie können die Erfassung bei der Event Hub-Erstellung konfigurieren, indem Sie das [Azure-Portal](https://portal.azure.com) oder Azure Resource Manager-Vorlagen verwenden. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Aktivieren von Event Hubs Capture über das Azure-Portal](event-hubs-capture-enable-through-portal.md)
- [Erstellen eines Event Hubs-Namespace mit einem Event Hub und Aktivieren von Capture über eine Azure Resource Manager-Vorlage](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Untersuchen der erfassten Dateien und Verwenden von Avro

Mit Event Hubs Capture werden Dateien im Avro-Format erstellt, die für das konfigurierte Zeitfenster angegeben werden. Sie können diese Dateien mit einem beliebigen Tool wie beispielsweise dem [Azure Storage-Explorer][Azure Storage Explorer] anzeigen. Sie können die Dateien zur Bearbeitung lokal herunterladen.

Die von Event Hubs Capture erzeugten Dateien weisen das folgende Avro-Schema auf:

![Avro-Schema][3]

Eine einfache Möglichkeit zum Untersuchen von Avro-Dateien ist die Verwendung der [Avro Tools][Avro Tools]-JAR-Datei von Apache. Sie können auch [Apache Drill][Apache Drill] verwenden, um eine einfache SQL-gesteuerte Benutzeroberfläche zu erhalten, oder [Apache Spark][Apache Spark], wenn Sie für die erfassten Daten eine komplexe verteilte Verarbeitung durchführen möchten. 

### <a name="use-apache-drill"></a>Verwenden von Apache Drill

[Apache Drill][Apache Drill] ist eine Open Source-SQL-Abfrage-Engine für Big Data-Untersuchungen, mit der strukturierte und halb strukturierte Daten unabhängig vom Speicherort abgefragt werden können. Die Engine kann als eigenständiger Knoten oder als großer Cluster ausgeführt werden, um eine hohe Leistung zu erzielen.

Native Unterstützung für Azure Blob Storage ist verfügbar, damit Sie Daten in einer Avro-Datei leicht abfragen können. Dies ist in der Dokumentation beschrieben:

[Apache Drill: Azure Blob Storage-Plug-In][Apache Drill: Azure Blob Storage Plugin]

Zum einfachen Abfragen von erfassten Dateien können Sie eine VM erstellen und ausführen, für die Apache Drill über einen Container für den Zugriff auf Azure Blob Storage aktiviert ist:

https://github.com/yorek/apache-drill-azure-blob

Ein vollständiges umfassendes Beispiel finden Sie im Repository zum bedarfsorientierten Streaming:

[Bedarfsorientiertes Streaming: Event Hubs Capture]

### <a name="use-apache-spark"></a>Verwenden von Apache Spark

[Apache Spark][Apache Spark] ist eine vereinheitlichte Engine zur Verarbeitung von umfangreichen Daten. Sie unterstützt verschiedene Sprachen, z.B. SQL, und der Zugriff auf Azure Blob Storage ist einfach. Es gibt zwei Optionen für die Ausführung von Apache Spark in Azure, die beide einfachen Zugriff auf Azure Blob Storage ermöglichen:

- [HDInsight: Zugreifen auf Dateien in Azure-Speicher][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Verwenden von Avro-Tools

[Avro-Tools][Avro Tools] sind als JAR-Paket verfügbar. Nach dem Herunterladen der JAR-Datei können Sie das Schema einer bestimmten Avro-Datei anzeigen, indem Sie folgenden Befehl ausführen:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Dieser Befehl gibt Folgendes zurück:

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Sie können die Datei auch mithilfe von Avro-Tools in das JSON-Format konvertieren und anders verarbeiten.

Für eine erweiterte Verarbeitung laden Sie Avro herunter und installieren es für eine Plattform Ihrer Wahl. Zum Zeitpunkt der Erstellung dieses Dokuments sind Implementierungen für C, C++, C\#, Java, NodeJS, Perl, PHP, Python und Ruby verfügbar.

Apache Avro bietet vollständige Anleitungen für die ersten Schritte mit [Java][Java] und [Python][Python]. Lesen Sie auch den Artikel zu den [ersten Schritten mit Event Hubs Capture](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Berechnung der Gebühren für Event Hubs Capture

Die Gebühren für Event Hubs Capture werden ähnlich wie für Durchsatzeinheiten auf Stundenbasis erhoben. Die Gebühr ist direkt proportional zur Anzahl der Durchsatzeinheiten, die für den Namespace erworben werden. Event Hubs Capture misst die Erhöhung und Verringerung der Durchsatzeinheiten, um eine entsprechende Leistung bereitstellen zu können. Die Verbrauchseinheiten treten zusammen auf. Informationen zu Preisen finden Sie unter [Event Hubs – Preise](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="integration-with-event-grid"></a>Integration in Event Grid 

Sie können ein Azure Event Grid-Abonnement mit einem Event Hubs-Namespace als Quelle erstellen. Das folgende Tutorial zeigt, wie Sie ein Event Grid-Abonnement mit einem Event Hub als Quelle und einer Azure Functions-App als Senke erstellen können: [Verarbeiten und Migrieren erfasster Event Hubs-Daten in einer SQL Data Warehouse-Instanz mithilfe von Event Grid und Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Nächste Schritte

Event Hubs Capture ist die einfachste Möglichkeit, Daten für Azure abzurufen. Mithilfe von Azure Data Lake, Azure Data Factory und Azure HDInsight können Sie Batchverarbeitung und andere Analysen mit vertrauten Tools und Plattformen Ihrer Wahl und jeder Größenordnung durchführen.

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Senden von Ereignissen an Azure Event Hubs mithilfe von .NET Frameworks](event-hubs-dotnet-framework-getstarted-send.md)
* [Übersicht über Event Hubs][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.9.0/java/avro-tools-1.9.0.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Bedarfsorientiertes Streaming: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
