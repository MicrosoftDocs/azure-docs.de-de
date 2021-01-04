---
title: Optimieren der Datenspeicherung für Apache Spark – Azure HDInsight
description: Erfahren Sie, wie Sie die Datenspeicherung für die Verwendung mit Apache Spark für Azure HDInsight optimieren können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ad09cdc2c0054c9d9a58e6bfa00252862f1e8c0f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028222"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Optimierung der Datenspeicherung für Apache Spark

In diesem Artikel werden Strategien zur Optimierung der Datenspeicherung für eine effiziente Ausführung von Apache Spark-Aufträgen für Azure HDInsight erörtert.

## <a name="overview"></a>Übersicht

Spark unterstützt eine Vielzahl von Formaten, z.B. CSV, JSON, XML, PARQUET, ORC und AVRO. Spark kann erweitert werden und viele weitere Formate mit externen Datenquellen unterstützen. Weitere Informationen dazu finden Sie unter [Apache Spark Packages](https://spark-packages.org) (Apache Spark-Pakete).

Hinsichtlich der Leistung ist Parquet mit *Snappy-Komprimierung* das beste Format. Dies ist die Standardeinstellung in Spark 2.x. Parquet speichert Daten im Spaltenformat und ist in Spark in hohem Maß optimiert.

## <a name="choose-data-abstraction"></a>Auswählen der Datenabstraktion

Frühere Versionen von Spark verwenden RDDs zum Abstrahieren von Daten. In Spark 1.3 und 1.6 wurden DataFrames und Datasets eingeführt. Diese Features bieten jeweils folgende Vorteile:

* **Dataframes**
    * In den meisten Situationen die beste Wahl.
    * Ermöglicht die Abfrageoptimierung durch Catalyst.
    * Codegenerierung für die gesamte Phase.
    * Direkter Zugriff auf den Arbeitsspeicher.
    * Geringer Overhead durch Garbage Collection (GC).
    * Nicht so entwicklerfreundlich wie Datasets, da keine Überprüfungen zur Kompilierzeit und Domänenobjektprogrammierung vorhanden sind.
* **Datasets**
    * Gut geeignet für komplexe ETL-Pipelines (Extrahieren, Transformieren, Laden), in denen die Leistungseinbußen akzeptabel sind.
    * Nicht gut geeignet in Aggregationen, in denen die Auswirkungen auf die Leistung beträchtlich sein können.
    * Ermöglicht die Abfrageoptimierung durch Catalyst.
    * Entwicklerfreundlich dank Domänenobjektprogrammierung und Überprüfungen zu Kompilierzeit.
    * Erzeugt Overhead für Serialisierung/Deserialisierung.
    * Hoher GC-Overhead.
    * Unterbricht die Codegenerierung für die gesamte Phase.
* **RDDs**
    * Sie müssen RDDs nicht verwenden, sofern Sie kein neues benutzerdefiniertes RDD erstellen.
    * Keine Abfrageoptimierung durch Catalyst.
    * Keine Codegenerierung für die gesamte Phase.
    * Hoher GC-Overhead.
    * Müssen ältere Spark 1.x-APIs verwenden.

## <a name="select-default-storage"></a>Auswählen des Standardspeichers

Wenn Sie einen neuen Spark-Cluster erstellen, stehen Azure Blob Storage oder Azure Data Lake Storage als Standardspeicher für Ihren Cluster zur Auswahl. Beide Optionen bieten den Vorteil einer langfristigen Speicherung für kurzlebige Cluster. So werden Ihre Daten nicht automatisch gelöscht, wenn Sie Ihren Cluster löschen. Sie können einen kurzlebigen Cluster erneut erstellen und weiterhin auf Ihre Daten zugreifen.

| Speichertyp | Dateisystem | Geschwindigkeit | Kurzlebig | Anwendungsfälle |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //url/ | **Standard** | Ja | Kurzlebiger Cluster |
| Azure-Blobspeicher (sicher) | **wasbs:** //url/ | **Standard** | Ja | Kurzlebiger Cluster |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **Schneller** | Ja | Kurzlebiger Cluster |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **Schneller** | Ja | Kurzlebiger Cluster |
| Lokales HDFS | **hdfs:** //url/ | **Sehr schnell** | Nein | Interaktiver 24/7-Cluster |

Eine vollständige Beschreibung der Speicheroptionen finden Sie unter [Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Verwenden des Caches

Spark stellt einen eigenen nativen Cachemechanismus bereit, der über verschiedene Methoden wie `.persist()`, `.cache()` und `CACHE TABLE` verwendet werden kann. Dieses native Caching ist bei kleinen Datasets und in ETL-Pipelines effektiv, bei denen Zwischenergebnisse zwischengespeichert werden müssen. Das native Spark-Caching funktioniert jedoch derzeit nicht gut mit Partitionierung, da eine zwischengespeicherte Tabelle die Partitionierungsdaten nicht beibehält. Eine allgemeinere und zuverlässige Cachetechnik ist das *Caching auf Speicherebene*.

* Natives Spark-Caching (nicht empfohlen)
    * Gut geeignet für kleine Datasets.
    * Funktioniert nicht mit Partitionierung. Dies wird sich möglicherweise in zukünftigen Spark-Releases ändern.

* Caching auf Speicherebene (empfohlen)
    * Kann in HDInsight mithilfe des [IO Cache](apache-spark-improve-performance-iocache.md)-Features implementiert werden.
    * Verwendet In-Memory- und SSD-Caching.

* Lokales HDFS (empfohlen)
    * Pfad: `hdfs://mycluster`.
    * Verwendet SSD-Caching.
    * Zwischengespeicherte Daten gehen verloren, wenn Sie den Cluster löschen, daher ist eine Neuerstellung des Caches erforderlich.

## <a name="optimize-data-serialization"></a>Optimieren Sie die Datenserialisierung

Spark-Aufträge sind verteilt, eine geeignete Datenserialisierung ist also wichtig, um die beste Leistung zu erzielen.  Es gibt zwei Serialisierungsoptionen für Spark:

* Die Java-Serialisierung ist die Standardeinstellung.
* Die `Kryo`-Serialisierung ist ein neueres Format und kann eine schnellere und kompaktere Serialisierung als Java ermöglichen.  `Kryo` erfordert die Registrierung der Klassen in Ihrem Programm und bietet noch keine Unterstützung für alle serialisierbaren Typen.

## <a name="use-bucketing"></a>Verwenden der Zuordnung von Buckets

Das Zuordnen von Buckets ähnelt der Datenpartitionierung, aber jeder Bucket kann nicht nur einen Wert, sondern eine Reihe von Spaltenwerten aufnehmen. Diese Methode funktioniert gut bei der Partitionierung einer großen Menge an Werten (im Millionenbereich und darüber), z. B. für Produktbezeichner. Ein Bucket wird durch eine Hashberechnung für den Bucketschlüssel der Zeile bestimmt. Buckettabellen bieten einzigartige Optimierungsmöglichkeiten, da sie Metadaten über die Art ihrer Bucketzuordnung und ihrer Sortierung speichern.

Das Zuordnen von Buckets bietet u.a. folgende erweiterte Features:

* Abfrageoptimierung basierend auf Metadaten zur Zuordnung von Buckets.
* Optimierte Aggregationen.
* Optimierte Joins.

Sie können die Partitionierung und die Zuordnung von Buckets gleichzeitig verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Datenverarbeitung für Apache Spark](optimize-cluster-configuration.md)
* [Optimieren der Speicherauslastung für Apache Spark](optimize-memory-usage.md)
* [Optimieren der Clusterkonfiguration für Apache Spark](optimize-cluster-configuration.md)
