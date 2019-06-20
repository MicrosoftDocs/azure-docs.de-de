---
title: Erstellen von hoch verfügbaren Spark Streaming-Aufträgen in YARN – Azure HDInsight
description: Einrichten von Spark Streaming für ein Szenario mit Hochverfügbarkeit.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 79a36ad39284dc66467ba7c500a363668f78b893
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720661"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Erstellen von hoch verfügbaren Apache Spark-Streamingaufträgen mit YARN

Mit [Apache Spark-Streamings](https://spark.apache.org/) können Sie skalierbare und fehlertolerante Anwendungen mit hohem Durchsatz zur Verarbeitung von Datenströmen implementieren. Sie können Spark-Anwendungen für Streamings in einem HDInsight Spark-Cluster mit mehreren Datenquellen verbinden, z.B. mit Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), RAW-TCP-Sockets, oder indem Sie das [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)-Dateisystem auf Änderungen überwachen. Spark Streaming unterstützt die Fehlertoleranz mit der Garantie, dass alle angegebenen Ereignisse jeweils genau einmal verarbeitet werden – selbst bei einem Ausfall eines Knotens.

Spark Streaming erstellt Aufträge mit langer Ausführungszeit, bei denen Transformationen auf die Daten angewendet und die Ergebnisse dann auf Dateisysteme, Datenbanken, Dashboards und die Konsole übertragen werden können. Spark Streaming verarbeitet Mikrobatches von Daten. Dabei wird zunächst ein Batch von Ereignissen in einem definierten Zeitintervall gesammelt. Dann wird dieser Batch zur Verarbeitung und Ausgabe gesendet. Batchzeitintervalle werden normalerweise in Sekundenbruchteilen definiert.

![Spark Streaming](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming stellt einen kontinuierlichen Datenstrom mithilfe eines *diskretisierten Datenstroms* (DStream) dar. Ein DStream kann aus Eingabequellen wie Event Hubs oder Kafka oder durch Anwenden von Transformationen auf einen anderen DStream erstellt werden. Wenn ein Ereignis in Ihrer Spark Streaming-Anwendung eingeht, wird es auf zuverlässige Weise gespeichert. Das heißt, die Ereignisdaten werden so repliziert, dass eine Kopie auf mehreren Knoten vorhanden ist. Dies gewährleistet, dass der Ausfall eines einzelnen Knotens nicht zum Verlust des Ereignisses führt.

Der Spark-Kern verwendet *Resilient Distributed Datasets* (RDDs). RDDs verteilen Daten über mehrere Knoten im Cluster, wobei jeder Knoten in der Regel zur Leistungsoptimierung die zugehörigen Daten vollständig im internen Arbeitsspeicher verwaltet. Jedes RDD stellt Ereignisse dar, die in einem Batchintervall gesammelt wurden. Nach Ablauf des Batchintervalls generiert Spark Streaming ein neues RDD, das alle Daten in dem Intervall enthält. Dieser kontinuierliche Satz von RDDs wird in einem DStream gesammelt. Eine Spark Streaming-Anwendung verarbeitet die im RDD jedes Batchs gespeicherten Daten.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Structured Streaming-Aufträge

Spark Structured Streaming wurde in Spark 2.0 als Analysemodul zur Verwendung beim Streaming strukturierter Daten eingeführt. Spark Structured Streaming verwendet die Spark SQL-APIs für Batchverarbeitung. Wie bei Spark Streaming werden Berechnungen bei Spark Structured Streaming über kontinuierlich eingehende Mikrobatches von Daten ausgeführt. Spark Structured Streaming stellt einen Datenstrom als Eingabetabelle mit unbegrenzter Anzahl von Zeilen dar. Das heißt, die Eingabetabelle vergrößert sich mit dem Eingang neuer Daten. Diese Eingabetabelle wird durch eine Abfrage mit langer Ausführungszeit kontinuierlich verarbeitet, und die Ergebnisse werden in eine Ausgabetabelle geschrieben.

![Spark Structured Streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

In Structured Streaming werden die im System eingehenden Daten sofort in der Eingabetabelle erfasst. Sie schreiben Abfragen, mit denen Vorgänge für diese Eingabetabelle ausgeführt werden. Die Abfrageausgabe erfolgt in einer anderen Tabelle, der sogenannten Ergebnistabelle. Die Ergebnistabelle enthält die Ergebnisse Ihrer Abfrage, aus denen Sie Daten zum Senden an einen externen Datenspeicher, z.B. eine relationale Datenbank, beziehen. Mit dem *Triggerintervall* wird der Zeitpunkt festgelegt, zu dem die Daten aus der Eingabetabelle verarbeitet werden. Standardmäßig werden die Daten in Structured Streaming verarbeitet, sobald sie eingehen. Sie können den Trigger jedoch auch zur Ausführung in einem längeren Intervall konfigurieren, sodass die Streamingdaten in zeitbasierten Batches verarbeitet werden. Die Daten in der Ergebnistabelle können bei jedem Eingang neuer Daten vollständig aktualisiert werden, sodass die Ergebnistabelle alle Ausgabedaten seit dem Start der Streamingabfrage enthält (*vollständiger Modus*), oder die Ergebnistabelle kann lediglich die Daten enthalten, die seit der letzten Verarbeitung der Abfrage neu sind (*Anfügemodus*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Erstellen fehlertoleranter Spark Streaming-Aufträge

Zum Erstellen einer hochverfügbaren Umgebung für Ihre Spark Streaming-Aufträge codieren Sie zunächst die einzelnen Aufträge zur Wiederherstellung bei einem Ausfall. Derartige Aufträge zur selbstständigen Wiederherstellung sind fehlertolerant.

RDDs umfassen mehrere Eigenschaften, die hochverfügbare und fehlertolerante Spark Streaming-Aufträge unterstützen:

* Batches mit in RDDs als DStream gespeicherten Eingabedaten werden zum Zweck der Fehlertoleranz im Arbeitsspeicher automatisch repliziert.
* Daten, die aufgrund eines Workerausfalls verloren gegangen sind, können aus replizierten Eingabedaten auf anderen Workern neu berechnet werden, sofern diese Workerknoten verfügbar sind.
* Die schnelle Wiederherstellung nach einem Fehler ist innerhalb einer Sekunde möglich, da die Wiederherstellung nach Fehlern und Abweichungen über die Berechnung im Arbeitsspeicher erfolgt.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Semantik vom Typ „Exactly Once“ mit Spark Streaming

Zum Erstellen einer Anwendung, die jedes Ereignis einmal (und nur einmal) verarbeitet, muss berücksichtigt werden, wie alle Komponenten im System nach Ausfällen neu gestartet werden und wie Datenverlust vermieden werden kann. Die „Exactly-Once“-Semantik erfordert, dass keine Daten an irgendeiner Stelle verloren gehen und dass die Nachrichtenverarbeitung neu gestartet werden kann, unabhängig davon, wo der Fehler auftritt. Siehe dazu [Erstellen von Spark-Streamingaufträgen mit Ereignisverarbeitung vom Typ „Exactly-Once“](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark-Streaming und Apache Hadoop YARN

In HDInsight wird die Verwendung von Clustern über *Yet Another Resource Negotiator* (YARN) koordiniert. Das Entwerfen einer Architektur für Hochverfügbarkeit für Spark Streaming umfasst Techniken für Spark Streaming sowie für YARN-Komponenten.  Eine Beispielkonfiguration unter Verwendung von YARN ist nachfolgend dargestellt. 

![YARN-Architektur](./media/apache-spark-streaming-high-availability/yarn-arch.png)

In den folgenden Abschnitten werden Überlegungen zum Entwurf für diese Konfiguration beschrieben.

### <a name="plan-for-failures"></a>Einplanen von Fehlern

Beim Erstellen einer YARN-Konfiguration für Hochverfügbarkeit sollten Sie einen möglichen Executor- oder Treiberfehler einplanen. Einige Spark Streaming-Aufträge enthalten zudem Garantieanforderungen, die einer zusätzlichen Konfiguration und Einrichtung bedürfen. Eine Streaminganwendung kann beispielsweise eine Geschäftsanforderung für eine Garantie mit null Datenverlust enthalten, trotz Fehlern, die im Hoststreamingsystem oder HDInsight-Cluster auftreten.

Wenn bei einem **Executor** ein Fehler auftritt, werden die zugehörigen Aufgaben und Empfänger von Spark automatisch neu gestartet, sodass keine Änderung der Konfiguration erforderlich ist.

Wenn jedoch bei einem **Treiber** ein Fehler auftritt, treten auch bei allen zugeordneten Executors Fehler auf und alle empfangenen Blöcke und Berechnungsergebnisse gehen verloren. Verwenden Sie zur Wiederherstellung nach einem Treiberfehler die *DStream-Prüfpunktausführung* entsprechend der Beschreibung unter [Erstellen von Spark-Streamingaufträgen mit Ereignisverarbeitung vom Typ „Exactly-Once“](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Bei der DStream-Prüfpunktausführung wird der *gerichtete azyklische Graph* (Directed Acyclic Graph, DAG) von DStreams in regelmäßigen Abständen in einem fehlertoleranten Speicher (z.B. Azure Storage) gespeichert.  Durch die Prüfpunktausführung kann Spark Structured Streaming den fehlerhaften Treiber über die Prüfpunktinformationen neu starten.  Bei diesem Neustart des Treibers werden neue Executors gestartet und zudem Empfänger neu gestartet.

So stellen Sie Treiber mit der DStream-Prüfpunktausführung wieder her

* Konfigurieren Sie den automatischen Treiberneustart in YARN mit der Konfigurationseinstellung `yarn.resourcemanager.am.max-attempts`.
* Legen Sie mit der Einstellung `streamingContext.checkpoint(hdfsDirectory)` ein Prüfpunktverzeichnis in einem HDFS-kompatiblen Dateisystem fest.
* Strukturieren Sie den Quellcode um, sodass Prüfpunkte zur Wiederverwendung verwendet werden, beispielsweise:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Konfigurieren Sie die Wiederherstellung von Datenverlust durch Aktivieren des Write-Ahead-Protokolls (WAL) mit der Einstellung `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`, und deaktivieren Sie die speicherinterne Replikation für Eingabe-DStreams mit `StorageLevel.MEMORY_AND_DISK_SER`.

Zusammenfassend gilt: Durch Verwendung von Prüfpunktausführung, WAL und zuverlässigen Empfängern kann sichergestellt werden, dass die Datenwiederherstellung mindestens einmal erfolgt:

* Genau einmal, sofern empfangene Daten nicht verloren gehen und die Ausgaben idempotent oder transaktional sind.
* Genau einmal mit dem neuen Kafka Direct-Ansatz, bei dem anstelle der Verwendung von Empfängern oder WALs Kafka als repliziertes Protokoll verwendet wird.

### <a name="typical-concerns-for-high-availability"></a>Typische Überlegungen im Hinblick auf die Hochverfügbarkeit

* Streamingaufträge lassen sich schwieriger überwachen als Batchaufträge. Spark Streaming-Aufträge weisen normalerweise eine lange Ausführungszeit auf, und YARN aggregiert Aufträge erst nach dem Abschluss.  Spark-Prüfpunkte gehen bei Anwendungs- oder Spark-Upgrades verloren, und Sie müssen das Prüfpunktverzeichnis bei einem Upgrade löschen.

* Konfigurieren Sie den YARN-Clustermodus so, dass Treiber auch ausgeführt werden, wenn bei einem Client ein Fehler auftritt. So richten Sie den automatischen Neustart für Treiber ein

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark und die Spark Streaming-Benutzeroberfläche verfügen über ein konfigurierbares Metriksystem. Sie können auch zusätzliche Bibliotheken, z.B. Graphite/Grafana, zum Herunterladen von Dashboardmetriken wie „num records processed“, „memory/GC usage on driver & executors“, „total delay“, „utilization of the cluster“ usw. verwenden. In Structured Streaming Version 2.1 oder höher können Sie mithilfe von `StreamingQueryListener` zusätzliche Metriken erfassen.

* Sie sollten Aufträge mit langer Ausführungszeit segmentieren.  Wenn eine Spark Streaming-Anwendung an den Cluster übertragen wird, muss die YARN-Warteschlange, in der der Auftrag ausgeführt wird, definiert werden. Mithilfe eines [YARN Capacity Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) (eines YARN-Kapazitätsplaners) können Sie Aufträge mit langer Ausführungszeit zum Trennen von Warteschlangen übertragen.

* Fahren Sie die Streaminganwendung ordnungsgemäß herunter. Wenn die Offsets bekannt sind und alle Anwendungsstatus extern gespeichert werden, können Sie die Streaminganwendung an der entsprechenden Stelle programmgesteuert beenden. Eine Möglichkeit ist die Verwendung von „Thread-Hooks“ in Spark durch Überprüfen eines externen Flags alle *n* Sekunden. Alternativ können Sie eine *Markerdatei* verwenden, die beim Starten der Anwendung in HDFS erstellt und beim Beenden entfernt wird. Verwenden Sie bei dieser Vorgehensweise einen separaten Thread in der Spark-Anwendung, mit dem Code ähnlich dem folgenden aufgerufen wird:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Apache Spark-Streaming](apache-spark-streaming-overview.md)
* [Erstellen von Apache Spark-Streamingaufträgen mit Ereignisverarbeitung vom Typ „Exactly-Once“](apache-spark-streaming-exactly-once.md)
* [Spark-Streamingaufträge mit langer Ausführungszeit in YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) (in englischer Sprache) 
* [Structured Streaming: Fault Tolerant Semantics](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics) (Strukturiertes Streaming: Fehlertolerante Semantik)
* [Discretized Streams: A Fault-Tolerant Model for Scalable Stream Processing](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf) (Diskretisierte Datenströme: fehlertolerantes Modell für skalierbare Datenstromverarbeitung)
