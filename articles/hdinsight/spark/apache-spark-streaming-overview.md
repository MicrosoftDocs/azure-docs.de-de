---
title: Spark-Streaming in Azure HDInsight
description: Verwendung von Spark-Streaminganwendungen auf HDInsight-Spark-Clustern.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 19d77d4aa49008232a01cd3ac2761a796505a35c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712014"
---
# <a name="overview-of-apache-spark-streaming"></a>Übersicht über Apache Spark-Streaming

[Apache Spark](https://spark.apache.org/)-Streaming bietet eine Datenstromverarbeitung auf HDInsight Spark-Clustern mit der Garantie, dass alle Eingabeereignisse genau einmal verarbeitet werden, selbst dann, wenn ein Knotenfehler auftritt. Ein Spark-Stream ist ein Auftrag mit langer Ausführungsdauer, der Eingabedaten aus mehreren Quellen empfängt, einschließlich Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), RAW-TCP-Sockets oder aus der Überwachung von [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Dateisystemen. Im Gegensatz zu einem ausschließlich ereignisgesteuerten Prozess unterteilt ein Spark-Stream Eingabedaten in Zeitfensterbatches, z.B. einen 2-Sekunden-Slice, und transformiert dann jeden Datenbatch mithilfe von Zuordnungs-, Reduzierungs-, Verknüpfungs- und Extrahierungsvorgängen. Der Spark-Stream schreibt die transformierten Daten dann in Dateisysteme, Datenbanken, Dashboards und die Konsole.

![Datenstromverarbeitung mit HDInsight und Spark-Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark-Streaming-Anwendungen müssen einige Sekundenbruchteile warten, bis jeder *Mikrobatch* von Ereignissen erfasst ist, bevor dieser Batch zur Verarbeitung gesendet wird. Im Gegensatz dazu verarbeitet eine ereignisgesteuerte Anwendung jedes Ereignis sofort. Die Spark-Streaming-Latenzzeit liegt in der Regel unter wenigen Sekunden. Die Vorteile des Mikrobatchansatzes sind eine effizientere Datenverarbeitung und eine Vereinfachung aggregierter Berechnungen.

## <a name="introducing-the-dstream"></a>Einführung in DStream

Spark-Streaming stellt einen kontinuierlichen Strom von eingehenden Daten mithilfe eines als DStream bezeichneten *diskretisierten Datenstroms* dar. Ein DStream kann aus Eingabequellen, z.B. Event Hubs oder Kafka, oder durch Anwenden von Transformationen auf einen anderen DStream erstellt werden.

Ein DStream stellt eine Abstraktionsebene über den Ereignisrohdaten dar. 

Am Anfang steht ein einzelnes Ereignis, z.B. ein Temperaturwert auf einem vernetzten Thermostat. Wenn dieses Ereignis Ihre Spark-Streaming-Anwendung erreicht, wird es auf zuverlässige Weise gespeichert (auf mehreren Knoten repliziert). Diese Fehlertoleranz gewährleistet, dass der Ausfall eines einzelnen Knotens nicht zum Verlust des Ereignisses führt. Der Spark-Kern verwendet eine Datenstruktur, die Daten über mehrere Knoten im Cluster verteilt, wobei jeder Knoten in der Regel zur Leistungsoptimierung seinen eigenen internen Datenarbeitsspeicher verwaltet. Diese Datenstruktur wird als *Resilient Distributed Dataset* (RDD) bezeichnet.

Jedes RDD stellt Ereignisse dar, die in einem als *Batchintervall* bezeichneten benutzerdefinierten Zeitraum gesammelt wurden. Wenn ein Batchintervall verstreicht, wird ein neues RDD erzeugt, das alle Daten dieses Intervalls enthält. Der kontinuierliche Satz von RDDs wird in einem DStream gesammelt. Wenn das Batchintervall z.B. eine Sekunde lang ist, gibt Ihr DStream jede Sekunde einen Batch aus, der ein RDD mit allen während dieser Sekunde erfassten Daten enthält. Bei der DStream-Verarbeitung tritt das Temperaturereignis in einem dieser Batches auf. Eine Spark-Streaming-Anwendung verarbeitet die Batches, die die Ereignisse enthalten, und führt die endgültigen Aktionen an den Daten durch, die in den einzelnen RDDs gespeichert werden.

![Beispiel-DStream mit Temperaturereignissen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktur einer Spark-Streaming-Anwendung

Eine Spark-Streaming-Anwendung ist eine Anwendung mit langer Ausführungsdauer, die Daten von Erfassungsquellen empfängt, Transformationen zum Verarbeiten der Daten anwendet und die Daten dann an ein oder mehrere Ziele sendet. Die Struktur einer Spark-Streaming-Anwendung besteht aus einem statischen und einem dynamischen Teil. Mit dem statischen Teil wird definiert, woher die Daten kommen, welcher Verarbeitung sie unterzogen werden sollen und wohin die Ergebnisse gesendet werden sollen. Der dynamische Teil führt die Anwendung unendlich lange aus und wartet auf das Beendigungssignal.

Die folgende einfache Anwendung empfängt beispielsweise eine Textzeile über einen TCP-Socket und zählt, wie oft die einzelnen Wörter vorkommen.

### <a name="define-the-application"></a>Definieren der Anwendung

Die Definition der Anwendungslogik besteht aus vier Schritten:

1. Erstellen eines StreamingContext.
2. Erstellen eines DStream aus dem StreamingContext.
3. Anwenden von Transformationen auf den DStream.
4. Ausgeben der Ergebnisse.

Diese Definition ist statisch, und Daten werden erst dann verarbeitet, wenn Sie die Anwendung ausführen.

#### <a name="create-a-streamingcontext"></a>Erstellen eines StreamingContext

Erstellen Sie einen StreamingContext aus dem SparkContext, der auf Ihren Cluster verweist. Beim Erstellen eines StreamingContext geben Sie z.B. die Größe des Batches in Sekunden an:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Erstellen eines DStream

Mit der StreamingContext-Instanz erstellen Sie einen Eingabe-DStream für Ihre Eingabequelle. In diesem Fall überwacht die Anwendung das Vorkommen neuer Dateien im Standardspeicher, der an den HDInsight-Cluster angefügt ist.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Anwenden von Transformationen

Sie implementieren die Verarbeitung durch Anwenden von Transformationen auf den DStream. Diese Anwendung empfängt jeweils eine Textzeile aus der Datei, unterteilt jede Zeile in Wörter und verwendet dann ein MapReduce-Muster, um zu zählen, wie oft jedes Wort vorkommt.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Ausgeben von Ergebnissen

Senden Sie die Transformationsergebnisse durch Anwenden von Ausgabevorgängen an die Zielsysteme. In diesem Fall wird das Ergebnis jedes Berechnungsdurchlaufs in der Konsole ausgegeben.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Starten Sie die Streaminganwendung, und führen Sie sie aus, bis ein Beendigungssignal empfangen wird.

```
ssc.start()
ssc.awaitTermination()
```

Weitere Informationen zur Spark-Stream-API und zu den unterstützten Ereignisquellen, Transformationen und Ausgabevorgängen finden Sie im [Programmierhandbuch für Apache Spark-Streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Die folgende Beispielanwendung ist eigenständig, sodass Sie sie in einem [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md) ausführen können. In diesem Beispiel wird eine simulierte Datenquelle in der Klasse DummySource erstellt, die alle fünf Sekunden den Wert eines Zählers und die aktuelle Zeit in Millisekunden ausgibt. Ein neues StreamingContext-Objekt verfügt über ein Batchintervall von 30 Sekunden. Bei jeder Batcherstellung überprüft die Streaming-Anwendung das erzeugte RDD, konvertiert es in einen Spark-DataFrame und erstellt eine temporäre Tabelle über dem DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
} 

// Start the stream processing
ssc.start()
```

Warten Sie nach dem Starten der Anwendung oben etwa 30 Sekunden lang.  Dann können Sie den DataFrame in regelmäßigen Abständen abfragen, um den im Batch vorhandenen aktuellen Satz von Werten anzuzeigen, indem Sie beispielsweise die folgende SQL-Abfrage verwenden:

```sql
%%sql
SELECT * FROM demo_numbers
```

Daraus ergibt sich folgende Ausgabe:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Es sind sechs Werte, da die DummySource alle 5 Sekunden einen Wert erstellt und die Anwendung alle 30 Sekunden einen Batch ausgibt.

## <a name="sliding-windows"></a>Gleitende Fenster

Um über einen bestimmten Zeitraum Aggregationsberechnungen für Ihren DStream durchzuführen (beispielsweise zum Abrufen der Durchschnittstemperatur in den letzten zwei Sekunden), können Sie die in Spark-Streaming verfügbaren Vorgänge vom Typ *Gleitendes Fenster* verwenden. Ein gleitendes Fenster hat eine Dauer (Fensterlänge) und ein Intervall, in dem der Inhalt des Fensters ausgewertet wird (Gleitintervall).

Gleitende Fenster können sich überschneiden, sodass Sie beispielsweise ein Fenster mit einer Länge von zwei Sekunden definieren können, das einmal pro Sekunde verschoben wird. Dies bedeutet, dass das Fenster jedes Mal, wenn Sie eine Aggregationsberechnung ausführen, Daten aus der letzten Sekunde des vorherigen Fensters sowie neue Daten aus der nächsten Sekunde enthält.

![Beispiel für das erste Fenster mit Temperaturereignissen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Beispiel für ein Fenster mit Temperaturereignissen nach dem Gleiten](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Im folgenden Beispiel wird der Code aktualisiert, der die DummySource verwendet, um die Batches in einem Fenster mit einer Dauer von einer Minute und einem Gleiten in einer Minute zu sammeln.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
} 

// Start the stream processing
ssc.start()
```

Nach der ersten Minute ergibt dies zwölf Einträge – sechs Einträge aus jedem der beiden im Fenster gesammelten Batches.

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Zu den in der Spark-Streaming-API verfügbaren „Gleitendes Fenster“-Funktionen zählen „window“, countByWindow, reduceByWindow und countByValueAndWindow. Weitere Informationen zu diesen Funktionen finden Sie unter [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams) (DStream-Transformationen).

## <a name="checkpointing"></a>Setzen von Prüfpunkten

Um Resilienz und Fehlertoleranz zu gewährleisten, stellt das Spark-Streaming mithilfe von Prüfpunkten sicher, dass die Datenstromverarbeitung auch bei Knotenfehlern kontinuierlich durchgeführt werden kann. In HDInsight erstellt Spark Prüfpunkte in permanentem Speicher (Azure Storage oder Data Lake Storage). Diese Prüfpunkte speichern die Metadaten der Streaminganwendung, z.B. die Konfiguration, die von der Anwendung definierten Vorgänge und alle Batches, die in die Warteschlange gestellt, aber noch nicht verarbeitet wurden. In einigen Fällen beinhalten die Prüfpunkte auch das Speichern der Daten in den RDDs, um die Neuerstellung des Zustands der Daten aus den Daten, die in den von Spark verwalteten RDDs vorhanden sind, zu beschleunigen.

## <a name="deploying-spark-streaming-applications"></a>Bereitstellen von Spark-Streaminganwendungen

Sie erstellen eine Spark-Streaming-Anwendung in der Regel lokal in einer JAR-Datei und stellen sie dann unter HDInsight für Spark bereit, indem Sie die JAR-Datei in den Standardspeicher kopieren, der an Ihren HDInsight-Cluster angefügt ist. Sie können Ihre Anwendung mit den LIVY-REST-APIs, die in Ihrem Cluster verfügbar sind, mit einem POST-Vorgang starten. Der POST-Textkörper enthält ein JSON-Dokument, das Folgendes angibt: den Pfad zu Ihrer JAR-Datei, den Namen der Klasse, deren Hauptmethode die Streaming-Anwendung definiert und ausführt, und optional die Ressourcenanforderungen des Auftrags (z.B. die Anzahl der Executors, Speicher und Kerne) sowie alle Konfigurationseinstellungen, die Ihr Anwendungscode erfordert.

![Bereitstellen von Spark-Streaming-Anwendungen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Der Status aller Anwendungen kann auch mit einer GET-Anforderung an einem LIVY-Endpunkt überprüft werden. Außerdem können Sie eine ausgeführte Anwendung durch das Senden einer DELETE-Anforderung an den LIVY-Endpunkt beenden. Weitere Informationen zur LIVY-API finden Sie unter [Übermitteln von Remoteaufträgen mit Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Linux-basierten Clustern in HDInsight mithilfe des Azure-Portals](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmierhandbuch für Apache Spark-Streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Remotestarten von Apache Spark-Aufträgen mit Apache LIVY](apache-spark-livy-rest-interface.md)
