---
title: Verbinden von Apache Spark mit Azure Cosmos DB
description: Hier erfahren Sie mehr über den Azure Cosmos DB Spark-Connector, mit dem Sie Apache Spark mit Azure Cosmos DB verbinden können.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: eae5c38c80c43ffed5138733d9425e2055dc0b76
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334003"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Beschleunigen von Big Data-Analysen mit dem Apache Spark-Connector für Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Sie können [Spark](https://spark.apache.org/)-Aufträge mit in Azure Cosmos DB gespeicherten Daten mithilfe des Cosmos DB-Spark-Connectors ausführen. Sie können Cosmos für die Batch- und Streamverarbeitung und als Bereitstellungsebene für Zugriff mit geringer Latenz verwenden.

Sie können den Connector mit [Azure Databricks](https://azure.microsoft.com/services/databricks) oder [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) verwenden, die beide verwaltete Spark-Cluster in Azure bieten. Die folgende Tabelle zeigt die unterstützten Spark-Versionen.

| Komponente | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x und 2.1.x |
| Scala | 2.11 |
| Azure Databricks Runtime-Version | 3\.4 und höher |

> [!WARNING]
> Dieser Connector unterstützt die Haupt-(SQL)-API von Azure Cosmos DB.
> Verwenden Sie für die Cosmos DB für MongoDB-API den [MongoDB-Spark-Connector](https://docs.mongodb.com/spark-connector/master/).
> Verwenden Sie für die Cosmos DB-Cassandra-API den [Cassandra-Spark-Connector](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Schnellstart

* Führen Sie die Schritte unter [Erste Schritte mit dem Java SDK](./create-sql-api-java.md) durch, um ein Cosmos DB-Konto einzurichten und Daten aufzufüllen.
* Führen Sie die Schritte unter [Einführung in Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) durch, um einen Azure Databricks-Arbeitsbereich und -Cluster einzurichten.
* Jetzt können Sie neue Notebooks erstellen und die Cosmos DB-Connectorbibliothek importieren. Im Abschnitt [Verwenden des Connectors](#bk_working_with_connector) erhalten Sie Informationen zur Einrichtung Ihres Arbeitsbereichs.
* Im folgenden Abschnitt finden Sie Codeausschnitte, mit denen Sie mithilfe des Connectors Lese- und Schreibvorgänge durchführen können.

### <a name="batch-reads-from-cosmos-db"></a>Batchlesevorgänge aus Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Spark-Datenmatrix erstellen können, um in PySpark aus Cosmos DB lesen zu können.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batchschreibvorgänge in Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Datenmatrix in Cosmos DB in PySpark schreiben können.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streaminglesevorgänge aus Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Verbindung mit einem Azure Cosmos DB-Änderungsfeed herstellen und daraus lesen.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streamingschreibvorgänge in Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Datenmatrix in Cosmos DB in PySpark schreiben können.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Weitere Codeausschnitte und vollständige Beispiele finden Sie unter [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Verwenden des Connectors

Sie können den Connector über eine Quelle auf GitHub erstellen oder die Uber-JAR-Dateien von Maven über die unten aufgeführten Links herunterladen.

| Spark | Scala | Aktuelle Version |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Verwenden von Azure Databricks-Notebooks

Befolgen Sie die Anweisungen im Azure Databricks-Leitfaden im Abschnitt [Use the Azure Cosmos DB Spark connector (Verwenden des Azure Cosmos DB-Connectors)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html), um eine Bibliothek mithilfe Ihres Databricks-Arbeitsbereichs zu erstellen.

> [!NOTE]
> Die Seite **Use the Azure Cosmos DB Spark connector** (Verwenden des Azure Cosmos DB-Connectors) ist derzeit nicht auf dem neuesten Stand. Statt die sechs JAR-Dateien in sechs verschiedene Bibliotheken zu laden, können Sie einfach die Uber-JAR-Datei von Maven unter [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) herunterladen und diese eine JAR-Datei/Bibliothek installieren.
> 

### <a name="using-spark-cli"></a>Verwenden von spark-cli

Verwenden Sie den Parameter `--packages` mit den [Maven-Koordinaten](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11) des Connectors, um den Connector mit spark-cli, also `spark-shell`, `pyspark` und `spark-submit`, zu verwenden.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Verwenden von Jupyter Notebook-Instanzen

Wenn Sie Jupyter Notebook in HDInsight verwenden, können Sie mithilfe der „spark-magic“-Zelle `%%configure` die Maven-Koordinaten des Connectors angeben.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Beachten Sie, dass `spark.jars.excludes` verwendet wird, um potenzielle Konflikte zwischen dem Connector, Apache Spark und Livy aufzulösen.

### <a name="build-the-connector"></a>Erstellen des Connectors

Aktuell wird in diesem Connectorprojekt `maven` verwendet. Führen Sie den folgenden Code aus, um den Connector ohne Abhängigkeiten zu erstellen:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Verwenden der Beispiele

Im [GitHub-Repository zum Spark-Connector für Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) sind die folgenden Beispielnotebooks und -skripts enthalten, die Sie ausprobieren können:

* **On-Time Flight Performance with Spark and Cosmos DB (Seattle) (Pünktlichkeit von Flügen mit Spark und Cosmos DB (Seattle))** [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Verbinden Sie Spark mithilfe des HDInsight Jupyter Notebook-Diensts mit Cosmos DB, um Spark SQL und GraphFrames nutzen und Flugverspätungen mit Machine Learning-Pipelines vorhersagen zu können.
* **Twitter Source with Apache Spark and Azure Cosmos DB Change Feed (Twitter mit Apache Spark und Azure Cosmos DB-Änderungsfeeds)** : [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Using Apache Spark to query Cosmos DB Graphs (Abfragen von Cosmos DB-Graphen mit Apache Spark)** : [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Verbinden von Azure Databricks mit Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** mit `azure-cosmosdb-spark`.  Hier ist auch eine Azure Databricks-Version des Notebooks [On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks) verlinkt.
* **[Lambda Architecture with Azure Cosmos DB and HDInsight (Apache Spark) (Lambda-Architektur mit Azure Cosmos DB und HDInsight (Apache Spark))](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : Reduzieren Sie den Betriebsaufwand für die Wartung von Big Data-Pipelines mit Cosmos DB und Spark.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen finden Sie im `azure-cosmosdb-spark`-[Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki), u. a.:

* [Azure Cosmos DB Spark Connector User Guide (Benutzerhandbuch zum Azure Cosmos DB-Spark-Connector)](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Aggregations Examples (Aggregationsbeispiele)](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguration und Setup

* [Konfiguration des Spark-Connectors](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark to Cosmos DB Connector Setup (Einrichtung des Spark-Connectors für Cosmos DB)](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (In Bearbeitung)
* [Configuring Power BI Direct Query to Azure Cosmos DB via Apache Spark (HDI) (Konfigurieren von Power BI-Direct Query-Funktionen in Azure Cosmos DB über Apache Spark (HDI))](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Problembehandlung

* [Using Cosmos DB Aggregates (Verwenden von Cosmos DB-Aggregaten)](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Bekannte Probleme](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Leistung

* [Leistungstipps](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Query Test Runs (Abfragetestläufe)](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Writing Test Runs (Schreiben von Testläufen)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Änderungsfeed

* [Stream processing changes using Azure Cosmos DB Change Feed and Apache Spark (Streamen von Verarbeitungsänderungen mit dem Azure Cosmos DB-Änderungsfeed und Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Change feed demos (Demos zu Änderungsfeeds)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Structured Stream Demos (Demos zu strukturierten Streams)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Überwachung

* [Monitoring Spark jobs with application insights (Überwachen von Spark-Aufträgen mit Application Insights)](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Nächste Schritte

Laden Sie den Spark-Connector für Azure Cosmos DB aus dem GitHub-Repository [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) herunter (falls nicht bereits geschehen). Erkunden Sie die folgenden zusätzlichen Ressourcen im Repository:

* [Aggregationsbeispiele](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Sample scripts and notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Beispielskripts und Notebooks)

Weitere nützliche Informationen finden Sie in den Artikeln [Leitfaden für Apache Spark-SQL, Datenrahmen und Datasets](https://spark.apache.org/docs/latest/sql-programming-guide.html) (in englischer Sprache) und [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).