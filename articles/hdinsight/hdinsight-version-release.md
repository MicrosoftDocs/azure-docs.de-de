---
title: Übersicht über HDInsight 4.0 – Azure
description: Vergleich der Features, Einschränkungen und Aktualisierungsempfehlungen zwischen HDInsight 3.6 und HDInsight 4.0.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: overview
ms.date: 04/15/2019
ms.openlocfilehash: aa0d01bfbebd5279b5268bae9d161492f30b81de
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546309"
---
# <a name="hdinsight-40-overview"></a>Übersicht über HDInsight 4.0

Azure HDInsight ist einer der beliebtesten Dienste unter Enterprise-Kunden für die Open Source-Analyseframeworks Apache Hadoop und Apache Spark in Azure. HDInsight 4.0 ist eine Clouddistribution der Apache Hadoop-Komponenten von [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html). Dieser Artikel enthält Informationen zur neuesten Release von Azure HDInsight und zur Aktualisierung.

## <a name="whats-new-in-hdinsight-40"></a>Neuigkeiten in HDInsight 4.0

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 und LLAP

Apache Hive LLAP (Low-Latency Analytical Processing, Analyseverarbeitung mit geringer Latenz) verwendet beständige Abfrageserver und Zwischenspeicherung im Arbeitsspeicher, um schnelle SQL-Abfrageergebnisse zu Daten im Remotecloudspeicher ausführen zu können. Hive LLAP nutzt eine Reihe von beständigen Daemons, die Fragmente von Hive-Abfragen ausführen. Die Abfrageausführung unter LLAP ähnelt Hive ohne LLAP, wobei Workeraufgaben in LLAP-Daemons ausgeführt werden und nicht in Containern.

Hive LLAP bietet u.a. folgende Vorteile:

* Möglichkeit zum Durchführen umfassender SQL-Analysen wie komplexer Verknüpfungen, Unterabfragen, Windowingfunktionen, Sortierung, benutzerdefinierter Funktionen und komplexer Aggregationen ohne Einbußen bei Leistung und Skalierbarkeit.

* Interaktive Abfragen von Daten im gleichen Speicher, in dem Daten vorbereitet werden, ohne Notwendigkeit der Verschiebung von Daten aus dem Speicher zu einem anderen Modul für die analytische Verarbeitung.

* Durch die Zwischenspeicherung der Abfrageergebnisse können zuvor berechnete Abfrageergebnisse wiederverwendet werden. Das spart Zeit und Ressourcen in den ausgeführten Clustertasks für die Abfrage.

### <a name="hive-dynamic-materialized-views"></a>Dynamisch materialisierte Sichten in Hive

Hive unterstützt jetzt dynamisch materialisierte Sichten (Vorberechnung relevanter Zusammenfassungen), die zum Beschleunigen der Abfrageverarbeitung in Data Warehouses verwendet werden. Materialisierte Sichten können nativ in Hive gespeichert werden und können nahtlos auf LLAP Beschleunigung zugreifen.

### <a name="hive-transactional-tables"></a>Transaktionale Hive-Tabellen

HDI 4.0 umfasst Apache Hive 3, das ACID-Konformität (Atomicity, Consistency, Isolation, Durability – Unteilbarkeit, Konsistenz, Isolation, Dauerhaftigkeit) für Transaktionstabellen erfordert, die im Hive-Warehouse gespeichert sind. ACID-konforme Tabellen und Tabellendaten werden von Hive abgerufen und verwaltet. Daten in CRUD-Tabellen (Create, Retrieve, Update, Delete – Erstellen, Abrufen, Aktualisieren, Löschen) müssen das ORC-Dateiformat (Optimized Row Column – optimierte Zeilen in Spalten) aufweisen, Tabellen nur mit Einfügung unterstützen jedoch alle Dateiformate.

* ACID v2 weist Leistungsverbesserungen beim Speicherformat und der Ausführungsengine auf. 

* ACID ist standardmäßig aktiviert, um vollständige Unterstützung für Datenaktualisierungen zu ermöglichen.

* Durch verbesserte ACID-Funktionen können Sie auf Zeilenebene aktualisieren und löschen.

* Kein zusätzlicher Leistungsaufwand.

* Kein Bucketing erforderlich.

* Spark kann Hive-ACID-Tabellen mithilfe des Hive-Warehouse-Connectors lesen und schreiben.

Erfahren Sie mehr über [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark ruft aktualisierbare Tabellen und ACID-Transaktionen mit dem Hive-Warehouse-Connector ab. Über den Hive-Warehouse-Connector können Sie Hive-Transaktionstabellen als externe Tabellen in Spark registrieren, um auf alle transaktionalen Funktionen zugreifen zu können. In früheren Versionen wurde nur die Bearbeitung von Tabellenpartitionen unterstützt. Der Hive-Warehouse-Connector unterstützt auch Streaming DataFrames für das Streamen von Lese- und Schreibvorgängen in transaktionale und Streaming-Hive-Tabellen von Spark.

Spark-Executors können direkte Verbindungen mit Hive LLAP-Daemons herstellen, um Daten auf transaktionale Weise abzurufen und zu aktualisieren, sodass Hive die Kontrolle über die Daten behält.

Apache Spark unter HDInsight 4.0 unterstützt die folgenden Szenarien:

* Ausführen des Trainings von Modellen zum maschinellen Lernen über dieselbe Transaktionstabelle wie für die Berichterstellung
* Verwenden von ACID-Transaktionen zum sicheren Hinzufügen von Spalten aus Spark ML in eine Hive-Tabelle
* Ausführen eines Spark-Streamingauftrags im Änderungsfeed von einer Hive-Streamingtabelle
* Erstellen von ORC-Dateien direkt aus einem strukturierten Spark-Streaming-Auftrag

Sie müssen sich keine Sorgen mehr darum machen, versehentlich direkt aus Spark auf transaktionale Hive-Tabellen zuzugreifen und dadurch inkonsistente Ergebnisse, doppelte Daten oder Datenbeschädigungen zu verursachen. In HDInsight 4.0 werden Spark- und Hive-Tabellen in separaten Metastores beibehalten. Verwenden Sie den Hive-Data Warehouse-Connector, um Hive-Transaktionstabellen explizit als externe Spark-Tabellen zu registrieren.

Erfahren Sie mehr über [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 ist in HDI 4.0 mit den folgenden Änderungen enthalten:

* Oozie führt keine Hive-Aktionen mehr aus. Die Hive-Befehlszeilenschnittstelle wurde entfernt und durch BeeLine ersetzt.

* Sie können unerwünschte Abhängigkeiten von freigegebenen Bibliotheken ausschließen, indem Sie in Ihre Datei **job.properties** ein Ausschlussmuster aufnehmen.

Erfahren Sie mehr über [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Ausführen eines Upgrades auf HDInsight 4.0

Wie bei jeder Hauptversion ist es wichtig, Ihre Komponenten gründlich zu testen, bevor Sie die neueste Version in einer Produktionsumgebung implementieren. HDInsight 4.0 steht für den Upgradevorgang zur Verfügung, die Standardoption ist jedoch HDInsight 3.6, um versehentliche Missgeschicke zu verhindern.

Es gibt keinen unterstützten Upgradepfad von früheren Versionen von HDInsight auf HDInsight 4.0. Da sich der Metastore und die Blobdatenformate geändert haben, ist HDInsight 4.0 nicht mit früheren Versionen kompatibel. Es ist wichtig, die neue HDInsight 4.0-Umgebung von der aktuellen Produktionsumgebung getrennt zu halten. Wenn Sie HDInsight 4.0 in Ihrer aktuellen Umgebung bereitstellen, wird Ihr Metastore aktualisiert, ohne dass dies rückgängig gemacht werden kann.  

## <a name="limitations"></a>Einschränkungen

* HDInsight 4.0 unterstützt MapReduce für Apache Hive nicht. Verwenden Sie stattdessen Apache Tez. Erfahren Sie mehr über [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 unterstützt Apache Storm nicht. 
* Hive View ist in HDInsight 4.0 nicht mehr verfügbar. 
* Shellinterpreter in Apache Zeppelin wird in Spark- und Interactive Query-Clustern nicht unterstützt.
* Sie können LLAP in einem Spark-LLAP-Cluster nicht *deaktivieren*. Sie können LLAP nur ausschalten.
* Für Azure Data Lake Storage Gen2 können Juypter Notebooks nicht in einem Spark-Cluster gespeichert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Azure HDInsight-Dokumentation](index.yml)
* [Anmerkungen zu dieser Version](hdinsight-release-notes.md)
