---
title: Apache Storm-Beispieltopologien in Azure HDInsight
description: Eine Liste von Beispieltopologien, die mit Apache Storm in HDInsight erstellt und getestet wurden, einschließlich der grundlegenden C#- und Java-Topologien, und mit Event Hubs funktionieren.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: b2407200b995ad956f233248f6cdd58488b583d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688789"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Apache Storm-Beispieltopologien und -komponenten für Apache Storm in HDInsight

Es folgt eine Liste von Beispielen zur Verwendung mit [Apache Storm](https://storm.apache.org/) in HDInsight, die von Microsoft erstellt und verwaltet wird. Diese Beispiele decken eine Vielzahl von Themen ab – von der Erstellung grundlegender C#- und Java-Topologien bis hin zur Arbeit mit Azure-Diensten wie Event Hubs, Cosmos DB, SQL-Datenbank, [Apache HBase](https://hbase.apache.org/) in HDInsight und Azure Storage. Einige Beispiele demonstrieren außerdem die Arbeit mit nicht-Azure oder sogar nicht-Microsoft-Technologien, wie SignalR und Socket.IO.

| BESCHREIBUNG | Zeigt | Sprache/Framework |
|:--- |:--- |:--- |
| [Schreiben von Daten in Azure Data Lake Storage aus Apache Storm](apache-storm-write-data-lake-store.md) |Schreiben von Daten in Azure Data Lake Storage |Java |
| [Quelle für Event Hub-Spout und -Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Quelle für Event Hub-Spout und -Bolt |Java |
| [Entwickeln von Java-basierten Topologien für Apache Storm in HDInsight][5797064f] |Maven |Java |
| [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio][16fce2d1] |HDInsight Tools für Visual Studio |C#, Java |
| [Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (C#)][844d1d81] |Event Hubs |C# und Java |
| [Verarbeitung von Ereignissen von Azure-Event-Hubs mit Storm auf HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Verarbeiten von Fahrzeugsensordaten von Event Hubs mit Apache Storm in HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extrahieren, Transformieren und Laden (ETL) von Azure Event Hubs in Apache HBase mit Apache Storm in HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [C#-Storm-Topologie-Vorlagenprojekt für die Arbeit mit Azure-Diensten über Apache Storm in HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL-Datenbank, HBase, SignalR |C#, Java |
| [Skalierbarkeitsbenchmarks zum Lesen aus Azure Event Hubs mit Apache Storm in HDInsight][d6c540e3] |Nachrichtendurchsatz, Event Hubs, SQL-Datenbank |C#, Java |
| [Verwenden von Python mit Apache Storm in HDInsight](apache-storm-develop-python-topology.md) |Python-Komponenten mit einer Flux-Topologie |Python |
| [Verwenden von Apache Kafka mit Apache Storm in HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm: Lese- und Schreibvorgänge für Apache Kafka | Java |

> [!WARNING]  
> Die C#-Beispiele in dieser Liste wurden ursprünglich mit HDInsight unter Windows erstellt und getestet und funktionieren möglicherweise nicht ordnungsgemäß mit Linux-basierten HDInsight-Clustern. Linux-basierte Cluster verwenden Mono zum Ausführen von .NET-Code und weisen möglicherweise Kompatibilitätsprobleme mit den im Beispiel verwendeten Frameworks und Paketen auf.
>
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird.

### <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Apache Storm in HDInsight][2b8c3488]
* [Bereitstellen und Verwalten von Apache Storm-Topologien mit Apache Storm in HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Erstellen eines Apache Storm -Clusters in HDInsight und Verwenden des Storm-Dashboards zum Bereitstellen von Beispieltopologien"
[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Bereitstellen und Verwalten von Topologien mit dem webbasierten Apache Storm-Dashboard und der Storm-Benutzeroberfläche oder den HDInsight-Tools für Visual Studio"
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Erfahren Sie, wie Sie C#-Storm-Topologien mithilfe der HDInsight-Tools für Visual Studio erstellen."
[5797064f]:apache-storm-develop-java-topology.md "Erfahren Sie, wie Sie Storm-Topologien in Java mit Maven erstellen, indem Sie eine Grundtopologie zur Wortzählung erstellen."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Erfahren Sie, wie Sie Daten von Azure Event Hubs mit Storm in HDInsight lesen und schreiben."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Erfahren Sie, wie Sie eine Storm-Topologie verwenden, um Azure Event Hubs-Nachrichten und Azure Cosmos DB-Dokumente zur Datenreferenzierung zu lesen und Daten in Azure Storage zu speichern."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Enthält mehrere Topologien, um den Durchsatz beim Lesen von Azure Event Hubs und das Speichern in einer SQL-Datenbank unter Verwendung von Apache Storm in HDInsight zu veranschaulichen."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Erfahren Sie, wie Sie Daten von Azure Event Hubs lesen, die Daten aggregieren und transformieren und dann in HBase in HDInsight speichern."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dieses Projekt enthält Vorlagen für Spouts, Bolts und Topologien für die Interaktion mit verschiedenen Azure-Diensten wie Event Hubs, Cosmos DB und SQL-Datenbank."

