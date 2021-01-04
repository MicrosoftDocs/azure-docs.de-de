---
title: Übersicht über HDInsight 4.0 – Azure
description: Vergleich der Features, Einschränkungen und Aktualisierungsempfehlungen zwischen HDInsight 3.6 und HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 2fd7a3e512b79651fdcf6a6ac0c14822361fc263
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350193"
---
# <a name="azure-hdinsight-40-overview"></a>Übersicht über Azure HDInsight 4.0

Azure HDInsight ist unter Unternehmenskunden einer der beliebtesten Dienste für Apache Hadoop und Apache Spark. HDInsight 4.0 ist eine Clouddistribution von Apache Hadoop-Komponenten. Dieser Artikel enthält Informationen zur neuesten Release von Azure HDInsight und zur Aktualisierung.

## <a name="whats-new-in-hdinsight-40"></a>Neuigkeiten in HDInsight 4.0

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 und Low-Latency Analytical Processing (LLAP)

Apache Hive LLAP (Low-Latency Analytical Processing – Analyseverarbeitung mit geringer Latenz) verwendet beständige Abfrageserver und speicherinternes Zwischenspeichern. Dieser Prozess liefert schnelle SQL-Abfrageergebnisse für Daten im Remotecloudspeicher. Hive LLAP nutzt eine Reihe von beständigen Daemons, die Fragmente von Hive-Abfragen ausführen. Die Abfrageausführung unter LLAP ähnelt Hive ohne LLAP, wobei Workeraufgaben in LLAP-Daemons ausgeführt werden und nicht in Containern.

Hive LLAP bietet u.a. folgende Vorteile:

* Möglichkeit zum Durchführen umfassender SQL-Analysen ohne Einbußen bei der Leistung und Flexibilität. Dazu zählen beispielsweise komplexe Verknüpfungen, Unterabfragen, Windowingfunktionen, Sortierung, benutzerdefinierte Funktionen und komplexe Aggregationen.

* Interaktive Abfragen von Daten im gleichen Speicher, in dem Daten vorbereitet werden, ohne Notwendigkeit der Verschiebung von Daten aus dem Speicher zu einem anderen Modul für die analytische Verarbeitung.

* Durch das Zwischenspeichern der Abfrageergebnisse können zuvor berechnete Abfrageergebnisse wiederverwendet werden. Dies spart Zeit und Ressourcen bei der Ausführung der erforderlichen Clusteraufgaben für die Abfrage.

### <a name="hive-dynamic-materialized-views"></a>Dynamisch materialisierte Sichten in Hive

Hive unterstützt jetzt dynamisch materialisierte Sichten bzw. die Vorberechnung relevanter Zusammenfassungen. Diese Sichten beschleunigen die Abfrageverarbeitung in Data Warehouses. Materialisierte Sichten können nativ in Hive gespeichert werden und können nahtlos auf LLAP Beschleunigung zugreifen.

### <a name="hive-transactional-tables"></a>Transaktionale Hive-Tabellen

HDI 4.0 enthält Apache Hive 3. Hive 3 erfordert ACID-Konformität (Atomicity, Consistency, Isolation, Durability – Unteilbarkeit, Konsistenz, Isolation, Dauerhaftigkeit) für Transaktionstabellen, die im Hive-Warehouse gespeichert sind. ACID-konforme Tabellen und Tabellendaten werden von Hive abgerufen und verwaltet. Daten in CRUD-Tabellen (Create, Retrieve, Update, Delete – Erstellen, Abrufen, Aktualisieren, Löschen) müssen das ORC-Dateiformat (Optimized Row Column) aufweisen. Tabellen, in denen nur Einfügungen erfolgen, unterstützen jedoch alle Dateiformate.

* ACID v2 weist Leistungsverbesserungen beim Speicherformat und der Ausführungsengine auf.

* ACID ist standardmäßig aktiviert, um vollständige Unterstützung für Datenaktualisierungen zu ermöglichen.

* Durch verbesserte ACID-Funktionen können Sie auf Zeilenebene aktualisieren und löschen.

* Kein zusätzlicher Leistungsaufwand.

* Kein Bucketing erforderlich.

* Spark kann Hive-ACID-Tabellen mithilfe des Hive-Warehouse-Connectors lesen und schreiben.

Erfahren Sie mehr über [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark ruft aktualisierbare Tabellen und ACID-Transaktionen mit dem Hive-Warehouse-Connector ab. Über den Hive-Warehouse-Connector können Sie Hive-Transaktionstabellen als externe Tabellen in Spark registrieren, um auf alle transaktionalen Funktionen zugreifen zu können. In früheren Versionen wurde nur die Bearbeitung von Tabellenpartitionen unterstützt. Hive Warehouse Connector unterstützt auch Streaming DataFrames.  Dieser Prozess streamt Lese- und Schreibvorgänge in Hive- Transaktions- und Streamingtabellen aus Spark.

Spark-Executors können direkte Verbindungen mit Hive LLAP-Daemons herstellen, um Daten auf transaktionale Weise abzurufen und zu aktualisieren, sodass Hive die Kontrolle über die Daten behält.

Apache Spark unter HDInsight 4.0 unterstützt die folgenden Szenarien:

* Ausführen des Trainings von Modellen zum maschinellen Lernen über dieselbe Transaktionstabelle wie für die Berichterstellung
* Verwenden von ACID-Transaktionen zum sicheren Hinzufügen von Spalten aus Spark ML in eine Hive-Tabelle
* Ausführen eines Spark-Streamingauftrags im Änderungsfeed von einer Hive-Streamingtabelle
* Erstellen von ORC-Dateien direkt aus einem strukturierten Spark-Streaming-Auftrag

Sie müssen sich keine Sorgen mehr darum machen, versehentlich direkt aus Spark auf Hive-Transaktionstabellen zuzugreifen und dadurch inkonsistente Ergebnisse, doppelte Daten oder Datenbeschädigungen zu verursachen. In HDInsight 4.0 werden Spark- und Hive-Tabellen in separaten Metastores beibehalten. Verwenden Sie den Hive-Data Warehouse-Connector, um Hive-Transaktionstabellen explizit als externe Spark-Tabellen zu registrieren.

Erfahren Sie mehr über [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 ist in HDI 4.0 mit den folgenden Änderungen enthalten:

* Oozie führt keine Hive-Aktionen mehr aus. Die Hive-Befehlszeilenschnittstelle wurde entfernt und durch BeeLine ersetzt.

* Sie können unerwünschte Abhängigkeiten von freigegebenen Bibliotheken ausschließen, indem Sie in Ihre Datei **job.properties** ein Ausschlussmuster aufnehmen.

Erfahren Sie mehr über [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Ausführen eines Upgrades auf HDInsight 4.0

Testen Sie Ihre Komponenten gründlich, bevor Sie die aktuelle Version in einer Produktionsumgebung implementieren. HDInsight 4.0 steht für den Upgradeprozess zur Verfügung. Um Pannen zu verhindern, ist die Standardoption jedoch HDInsight 3.6.

Es gibt keinen unterstützten Upgradepfad von früheren Versionen von HDInsight auf HDInsight 4.0. Da sich der Metastore und die Blobdatenformate geändert haben, ist Version 4.0 nicht mit früheren Versionen kompatibel. Es ist wichtig, die neue HDInsight 4.0-Umgebung von der aktuellen Produktionsumgebung getrennt zu halten. Wenn Sie HDInsight 4.0 in Ihrer aktuellen Umgebung bereitstellen, wird Ihr Metastore permanent aktualisiert.  

## <a name="limitations"></a>Einschränkungen

* HDInsight 4.0 unterstützt MapReduce für Apache Hive nicht. Verwenden Sie stattdessen Apache Tez. Erfahren Sie mehr über [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 unterstützt Apache Storm nicht.
* HDInsight 4.0 unterstützt den Clustertyp „ML-Dienste" nicht.
* Hive View ist nur für HDInsight 4.0-Cluster mit einer Versionsnummer gleich oder größer als 4.1 verfügbar. Diese Versionsnummer finden Sie unter „Ambari Admin -> Versionen“.
* Shellinterpreter in Apache Zeppelin wird in Spark- und Interactive Query-Clustern nicht unterstützt.
* Sie können LLAP in einem Spark-LLAP-Cluster nicht *deaktivieren*. Sie können LLAP nur ausschalten.
* Azure Data Lake Storage Gen2 kann Jupyter Notebooks nicht in einem Spark-Cluster speichern.
* Apache Pig wird standardmäßig in Tez ausgeführt. Sie können diese Einstellung jedoch in MapReduce ändern.
* Die Spark SQL Ranger-Integration für Zeilen- und Spaltensicherheit ist veraltet.
* Spark 2.4 und Kafka 2.1 sind in HDInsight 4.0 verfügbar. Spark 2.3 und Kafka 1.1 werden daher nicht mehr unterstützt. In HDInsight 4.0 wird die Verwendung von Spark 2.4 und Kafka 2.1 oder höheren Versionen empfohlen.

## <a name="next-steps"></a>Nächste Schritte

* [HBase-Migrationsleitfaden](./hbase/apache-hbase-migrate-new-version.md)
* [Hive-Migrationsleitfaden](./interactive-query/apache-hive-migrate-workloads.md)
* [Kafka-Migrationsleitfaden](./kafka/migrate-versions.md)
* [Spark-Migrationsleitfaden](./spark/migrate-versions.md)
* [Azure HDInsight-Dokumentation](index.yml)
* [Versionsanmerkungen](hdinsight-release-notes.md)