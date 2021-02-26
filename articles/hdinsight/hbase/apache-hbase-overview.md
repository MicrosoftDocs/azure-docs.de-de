---
title: Was ist Apache HBase in Azure HDInsight?
description: Eine Einführung in Apache HBase in HDInsight, ein NoSQL-Datenbankbuild auf Hadoop. Erfahren Sie mehr über Anwendungsfälle, und vergleichen Sie HBase mit anderen Hadoop-Clustern.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942919"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Was ist Apache HBase in Azure HDInsight?

[Apache HBase](https://hbase.apache.org/) ist eine Open-Source-NoSQL-Datenbank, die auf Apache Hadoop basiert und nach dem Vorbild von [Google BigTable](https://cloud.google.com/bigtable/) erstellt wurde. HBase bietet wahlfreien Zugriff und starke Konsistenz für große Datenmengen in einer schemalosen Datenbank. Die Datenbank ist nach Spaltenfamilien organisiert.

Aus der Benutzerperspektive ähnelt HBase einer Datenbank. Daten werden in den Zeilen und Spalten einer Tabelle gespeichert und die Daten in einer Zeile zu einer Spaltenfamilie zusammengefasst. HBase ist eine schemalose Datenbank. Die Spalten und Datentypen können vor der Verwendung undefiniert sein. Der Open-Source-Code lässt sich linear skalieren, sodass Petabytes von Daten auf Tausenden von Knoten verarbeitet werden können. HBase nutzt Datenredundanz, Stapelverarbeitung und andere Funktionen, die von verteilten Anwendungen in der Hadoop-Umgebung zur Verfügung gestellt werden.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Wie wird Apache HBase in Azure HDInsight implementiert?

HDInsight HBase wird als verwalteter Cluster angeboten, der in die Azure-Umgebung integriert ist. Die Cluster sind so konfiguriert, dass Daten direkt in [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) gespeichert werden. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. Dank dieser Eigenschaft können Kunden interaktive Websites für große Datasets erstellen, Dienste erstellen, die Sensor- und Telemetriedaten von Millionen von Endpunkten speichern, und diese Daten mit Hadoop-Aufträgen analysieren. HBase und Hadoop sind gute Ausgangspunkte für Big Data-Projekte in Azure. Die Dienste ermöglichen in Echtzeitanwendungen die Verarbeitung großer Datasets.

Die HDInsight-Implementierung nutzt die Architektur mit horizontaler Skalierung von HBase für automatisches Sharding von Tabellen, für starke Konsistenz bei Lese- und Schreibvorgängen sowie für automatisches Failover. Die Leistung wird durch speicherinterne Zwischenspeicherung für Lesevorgänge und Schreibvorgänge mit hohem Durchsatz optimiert. Ein HBase-Cluster kann in einem virtuellen Netzwerk erstellt werden. Details hierzu finden Sie unter [Erstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Wie werden Daten in HDInsight HBase verwaltet?

Daten können Sie in HBase mit den Befehlen `create`, `get`, `put` und `scan` über die HBase-Shell verwalten. Daten werden mit dem Befehl `put` in die Datenbank geschrieben und mit `get` gelesen. Der `scan`-Befehl wird verwendet, um Daten aus mehreren Zeilen in einer Tabelle abzurufen. Sie können Daten auch über die HBase-C#-API verwalten. Diese bietet eine Clientbibliothek auf der HBase-REST-API. Eine HBase-Datenbank kann auch mithilfe von [Apache Hive](https://hive.apache.org/) abgefragt werden. Eine Einleitung in diese Programmiermodelle finden Sie unter [Erste Schritte mit Apache HBase mit Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md). Coprozessoren sind ebenfalls verfügbar und ermöglichen die Verarbeitung von Daten in den Knoten, die die Datenbank hosten.

> [!NOTE]  
> Thrift wird von HBase in HDInsight nicht unterstützt.

## <a name="use-cases-for-apache-hbase"></a>Anwendungsfälle für Apache HBase

Der kanonische Anwendungsfall, für den BigTable (und daher auch HBase) aus der Websuche erstellt wurde. Suchmaschinen erstellen Indizes, die Begriffe den Webseiten zuordnen, die diese Begriffe enthalten. Es gibt jedoch noch viele weitere Anwendungsfälle für HBase. Einige von ihnen werden in diesem Abschnitt beschrieben.

|Szenario |BESCHREIBUNG |
|---|---|
|Schlüssel-Wert-Speicherung|HBase kann für die Schlüssel-Wert-Speicherung verwendet werden und ist für die Verwaltung von Nachrichtensystemen geeignet. Facebook nutzt HBase für sein Nachrichtensystem. HBase ist ideal für die Speicherung und Verwaltung von Internetkommunikation. WebTable nutzt HBase, um Tabellen, die aus Webseiten extrahiert wurden, zu suchen und zu verwalten.|
|Sensordaten|HBase kann für das Erfassen von Daten verwendet werden, die schrittweise aus verschiedenen Quellen gesammelt werden. Diese Daten umfassen Analysen sozialer Netzwerke, Zeitreihen, Gewährleistung der Aktualität interaktiver Dashboards mit Trends und Indikatoren sowie die Verwaltung von Überwachungsprotokollierungssystemen. Zu den Beispielen zählen das Bloomberg Trader Terminal und die Open Time Series Database (OpenTSDB). OpenTSDB speichert Metriken, die zum Status von Serversystemen gesammelt wurden, und ermöglicht den Zugriff darauf.|
|Echtzeitabfrage|[Apache Phoenix](https://phoenix.apache.org/) ist eine SQL-Abfrage-Engine für Apache HBase. Der Zugriff erfolgt als JDBC-Treiber. So können Sie HBase-Tabellen mit SQL abfragen und verwalten.|
|HBase als Plattform|Anwendungen können auf HBase ausgeführt werden, indem sie HBase als Datenspeicher nutzen. Beispiele hierfür sind Phoenix, OpenTSDB, `Kiji` und Titan. Es ist auch ein Integration von Anwendungen mit HBase möglich. Beispiele: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` und Apache Drill|

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Erstellen von HDInsight-Clustern in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurieren der Apache HBase-Replikation in HDInsight](apache-hbase-replication.md)
