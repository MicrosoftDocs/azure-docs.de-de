---
title: 'Tutorial: Strukturiertes Apache Spark-Streaming mit Apache Kafka: Azure HDInsight'
description: Erfahren Sie, wie Sie Apache Spark-Streaming verwenden, um Daten in oder aus Apache Kafka zu streamen. In diesem Tutorial streamen Sie Daten mithilfe eines Jupyter Notebooks aus Spark in HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: 51f84234ac35be5f60d1aaa5dac661ad9ce5e0c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257899"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka in HDInsight

Dieses Tutorial veranschaulicht die Verwendung des [strukturierten Apache Spark-Streamings](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) zum Lesen und Schreiben von Daten mit [Apache Kafka](https://kafka.apache.org/) in Azure HDInsight.

Strukturiertes Spark-Streaming ist ein auf Spark SQL beruhendes Modul zur Streamverarbeitung. Sie können damit Streamingberechnungen genauso wie Batchberechnung auf statischen Daten ausdrücken.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden einer Azure Resource Manager-Vorlage zum Erstellen von Clustern
> * Verwenden des strukturierten Spark-Streamings mit Kafka

Denken Sie nach dem Ausführen der Schritte in diesem Dokument daran, die Cluster zu löschen, um das Anfallen von Gebühren zu verhindern.

## <a name="prerequisites"></a>Voraussetzungen

* jq, ein JSON-Befehlszeilenprozessor.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Kenntnisse im Umgang mit [Jupyter Notebooks](https://jupyter.org/) mit Spark in HDInsight. Weitere Informationen finden Sie im Dokument [Tutorial: Laden von Daten und Ausführen von Abfragen auf einem Apache Spark-Cluster in Azure HDInsight](spark/apache-spark-load-data-run-query.md).

* Kenntnisse in der Programmiersprache [Scala](https://www.scala-lang.org/). Der in diesem Tutorial verwendete Code ist in Scala geschrieben.

* Kenntnisse im Erstellen von Kafka-Themen. Weitere Informationen finden Sie im Dokument [Schnellstart: Erstellen eines Apache Kafka-Clusters in HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Für die in diesem Dokument beschriebenen Schritte wird eine Azure-Ressourcengruppe benötigt, die sowohl einen Spark- und einen Kafka-Cluster in HDInsight beinhaltet. Die Cluster befinden sich innerhalb eines virtuellen Azure-Netzwerks, wodurch Spark- und Kafka-Cluster direkt miteinander kommunizieren können.
> 
> Der Einfachheit halber enthält dieses Dokument Links zu einer Vorlage, mit der die erforderlichen Azure-Ressourcen erstellt werden können. 
>
> Weitere Informationen zur Verwendung von HDInsight in einem virtuellen Netzwerk finden Sie im Dokument [Erweitern von HDInsight mit einem virtuellen Netzwerk](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Strukturiertes Streaming mit Apache Kafka

Das strukturierte Spark-Streaming ist eine auf Spark SQL basierende Streamverarbeitungs-Engine. Wenn Sie das strukturierte Streaming verwenden, können Sie Streamingabfragen auf dieselbe Weise wie Batchabfragen schreiben.

Die folgenden Codeausschnitte veranschaulichen das Lesen aus Kafka und das Speichern in einer Datei. Der erste ist ein Batchvorgang, und der zweite ist ein Streamingvorgang:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

In beiden Ausschnitten werden die Daten aus Kafka gelesen und in eine Datei geschrieben. Folgende Unterschiede weisen die Beispiele auf:

| Batch | Streaming |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Der Streamingvorgang verwendet außerdem `awaitTermination(30000)`, sodass der Stream nach 30.000 ms beendet wird. 

Um das strukturierte Streaming mit Kafka zu verwenden, benötigt das Projekt eine Abhängigkeit im Paket `org.apache.spark : spark-sql-kafka-0-10_2.11`. Die Version dieses Pakets sollte der Version von Spark in HDInsight entsprechen. Für Spark 2.2.0 (verfügbar in HDInsight 3.6) finden Sie die Abhängigkeitsinformationen für verschiedene Projekttypen unter [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Für das in diesem Tutorial verwendete Jupyter Notebook lädt die folgende Zelle diese Paketabhängigkeit:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Erstellen von Clustern

Apache Kafka in HDInsight ermöglicht keinen Zugriff auf die Kafka-Broker über das öffentliche Internet. Alle von Kafka verwendeten Elemente müssen sich im gleichen virtuellen Azure-Netzwerk befinden. In diesem Tutorial befinden sich der Kafka- und der Spark-Cluster im gleichen virtuellen Azure-Netzwerk. 

Das folgende Diagramm zeigt den Kommunikationsfluss zwischen Spark und Kafka:

![Diagramm der Spark- und Kafka-Cluster in einem virtuellen Azure-Netzwerk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Der Kafka-Dienst ist auf die Kommunikation innerhalb des virtuellen Netzwerks beschränkt. Auf andere Dienste auf dem Cluster, wie z.B. SSH und Ambari, kann über das Internet zugegriffen werden. Weitere Informationen zu den öffentlichen Ports, die für HDInsight verfügbar sind, finden Sie unter [Von HDInsight verwendete Ports und URIs](hdinsight-hadoop-port-settings-for-services.md).

Führen Sie zum Erstellen eines virtuellen Azure-Netzwerks und zum anschließenden Erstellen der Kafka- und Spark-Cluster in diesem die folgenden Schritte aus:

1. Verwenden Sie die folgende Schaltfläche, um sich bei Azure anzumelden, und öffnen Sie die Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Die Azure Resource Manager-Vorlage finden Sie unter **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Diese Vorlage erstellt die folgenden Ressourcen:

   * Ein Kafka-auf-HDInsight-3.6-Cluster.
   * Einen Spark 2.2.0-Cluster in HDInsight 3.6.
   * Ein Azure Virtual Network, das die HDInsight-Cluster enthält.

     > [!IMPORTANT]  
     > Das in diesem Tutorial für strukturiertes Streaming verwendete Notebook benötigt Spark 2.2.0 in HDInsight 3.6. Bei Verwendung einer früheren Version von Spark auf HDInsight erhalten Sie Fehlermeldungen, wenn Sie das Notebook verwenden.

2. Geben Sie im Abschnitt **Benutzerdefinierte Vorlage** folgende Informationen an:

    | Einstellung | Wert |
    | --- | --- |
    | Abonnement | Ihr Azure-Abonnement |
    | Ressourcengruppe | Die Ressourcengruppe mit den Ressourcen. |
    | Location | Die Azure-Region, in der die Ressourcen erstellt werden. |
    | Spark Cluster Name (Spark-Clustername) | Der Name des Spark-Clusters. Die ersten sechs Zeichen müssen sich vom Kafka-Clusternamen unterscheiden. |
    | Kafka Cluster Name (Kafka-Clustername) | Der Name des Kafka-Clusters. Die ersten sechs Zeichen müssen sich vom Spark-Clusternamen unterscheiden. |
    | Benutzername für Clusteranmeldung | Der Administratorbenutzername für die Cluster. |
    | Kennwort für Clusteranmeldung | Das Administratorbenutzerkennwort für die Cluster. |
    | SSH-Benutzername | Der SSH-Benutzer, der für die Cluster erstellt werden soll. |
    | SSH-Kennwort | Das Kennwort für den SSH-Benutzer. |
   
    ![Screenshot der angepassten Vorlage](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Lesen Sie die **Geschäftsbedingungen**, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu** aus.

4. Aktivieren Sie zum Schluss **An Dashboard anheften**, und wählen Sie dann **Kaufen** aus. 

> [!NOTE]  
> Die Clustererstellung kann bis zu 20 Minuten dauern.

## <a name="use-spark-structured-streaming"></a>Verwenden des strukturierten Spark-Streamings

Dieses Beispiel zeigt, wie Sie strukturiertes Spark-Streaming mit Kafka in HDInsight verwenden. Im Beispiel werden von New York City bereitgestellte Daten für Taxifahrten verwendet.  Das von diesem Notebook verwendete Dataset stammt aus [2016 Green Taxi Trip Data](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Sammeln Sie Hostinformationen. Verwenden Sie die unten stehenden cURL- und [jq](https://stedolan.github.io/jq/)-Befehle, um Ihre Kafka ZooKeeper- und -Broker-Hostinformationen abzurufen. Die Befehle sind für eine Windows-Eingabeaufforderung vorgesehen. Für andere Umgebungen sind geringfügige Änderungen erforderlich. Ersetzen Sie `KafkaCluster` durch den Namen Ihres Kafka-Clusters und `KafkaPassword` durch das Kennwort für die Clusteranmeldung. Ersetzen Sie außerdem `C:\HDI\jq-win64.exe` durch den tatsächlichen Pfad zu Ihrer jq-Installation. Geben Sie die Befehle an einer Windows-Eingabeaufforderung ein, und speichern Sie die Ausgabe zur Verwendung in späteren Schritten.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. Verbinden Sie in Ihrem Webbrowser das Jupyter Notebook mit Ihrem Spark-Cluster. Ersetzen Sie in der folgenden URL `CLUSTERNAME` durch den Namen Ihres __Spark__-Clusters:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Geben Sie bei Aufforderung den Clusterbenutzernamen (Administrator) und das Kennwort ein, den bzw. das Sie beim Erstellen des Clusters verwendet haben.

3. Wählen Sie **Neu > Spark** aus, um ein Notebook zu erstellen.

4. Laden Sie die vom Notebook verwendeten Pakete, indem Sie die folgenden Informationen in einer Zelle des Notebooks eingeben. Führen Sie den Befehl mit **STRG+EINGABETASTE**aus.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Erstellen Sie das Kafka-Thema. Bearbeiten Sie den folgenden Befehl, indem Sie `YOUR_ZOOKEEPER_HOSTS` durch die ZooKeeper-Hostinformationen ersetzen, die Sie im ersten Schritt extrahiert haben. Geben Sie den bearbeiteten Befehl in Ihrem Jupyter Notebook ein, um das Thema `tripdata` zu erstellen.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Rufen Sie Daten zu Taxifahrten ab. Geben Sie den Befehl in der nächsten Zelle ein, um Daten zu Taxifahrten in New York City zu laden. Die Daten werden in einen Datenrahmen geladen, der dann als Zellenausgabe angezeigt wird.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString
    
    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)
    
    // Display the dataframe containing trip data
    taxiDF.show()
    ```

7. Legen Sie die Hostinformationen für den Kafka-Broker fest. Ersetzen Sie `YOUR_KAFKA_BROKER_HOSTS` durch die Broker-Hostinformationen, die Sie in Schritt 1 extrahiert haben.  Geben Sie den bearbeiteten Befehl in der nächsten Zelle des Jupyter Notebooks ein.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Senden Sie die Daten an Kafka. Im folgenden Befehl wird das Feld `vendorid` als Schlüsselwert für die Kafka-Nachricht verwendet. Der Schlüssel wird beim Partitionieren von Daten von Kafka verwendet. Alle Felder werden in der Kafka-Nachricht als JSON-Zeichenfolgenwert gespeichert. Geben Sie den folgenden Befehl in Jupyter ein, um die Daten mithilfe einer Batchabfrage in Kafka zu speichern.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Deklarieren Sie ein Schema. Der folgende Befehl zeigt, wie Sie beim Lesen von JSON-Daten aus Kafka ein Schema verwenden. Geben Sie den Befehl in der nächsten Jupyter-Zelle ein.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)
    
    println("Schema declared")
    ```

10. Wählen Sie Daten aus, und starten Sie den Stream. Der folgende Befehl zeigt, wie Daten mithilfe einer Batchabfrage aus Kafka abgerufen und die Ergebnisse anschließend in das Hadoop Distributed File System (HDFS) auf dem Spark-Cluster geschrieben werden. In diesem Beispiel ruft `select` die Nachricht (Wertfeld) aus Kafka ab und wendet das Schema an. Die Daten werden dann im Parquet-Format in das Hadoop Distributed File System (WASB oder ADL) geschrieben. Geben Sie den Befehl in der nächsten Jupyter-Zelle ein.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Sie können überprüfen, ob die Dateien erstellt wurden, indem Sie den Befehl in Ihrer nächsten Jupyter-Zelle eingeben. Der Befehl listet die Dateien im Verzeichnis `/example/batchtripdata` auf.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Im vorherige Beispiel wurde eine Batchabfrage verwendet. Der folgende Befehl zeigt, wie Sie den gleichen Vorgang mit einer Streamingabfrage ausführen. Geben Sie den Befehl in der nächsten Jupyter-Zelle ein.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Führen Sie die folgende Zelle aus, um zu überprüfen, ob die Dateien von der Streamingabfrage geschrieben wurden.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen können Sie die Ressourcengruppe löschen. Dadurch werden auch der zugeordnete HDInsight-Cluster sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

So entfernen Sie die Ressourcengruppe über das Azure-Portal:

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf __Ressourcengruppen__, um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die zu löschende Ressourcengruppe, und klicken Sie mit der rechten Maustaste rechts neben dem Eintrag auf die Schaltfläche __Mehr__ (...).
3. Klicken Sie auf __Ressourcengruppe löschen__, und bestätigen Sie den Vorgang.

> [!WARNING]  
> Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden.
> 
> Wenn Sie einen Kafka-Cluster in HDInsight löschen, werden auch alle in Kafka gespeicherten Daten gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie das [strukturierte Apache Spark-Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) zum Schreiben und Lesen von Daten aus [Apache Kafka](https://kafka.apache.org/) in HDInsight verwenden. Unter dem folgenden Link erfahren Sie, wie Sie [Apache Storm](https://storm.apache.org/) mit Kafka verwenden:

> [!div class="nextstepaction"]
> [Tutorial: Verwenden von Apache Storm mit Apache Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)
