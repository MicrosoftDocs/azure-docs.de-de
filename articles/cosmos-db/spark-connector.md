---
title: Verbinden von Apache Spark mit Azure Cosmos DB
description: Hier erfahren Sie mehr über den Azure Cosmos DB Spark-Connector, mit dem Sie Apache Spark mit Azure Cosmos DB verbinden können.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bc0f2044f70c674177f9c9786f56f0441db2e282
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978903"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Beschleunigen von Big Data-Analysen mit dem Apache Spark-Connector für Azure Cosmos DB

Sie können [Spark](https://spark.apache.org/)-Aufträge mit in Azure Cosmos DB gespeicherten Daten mithilfe des Cosmos DB-Spark-Connectors ausführen. Sie können Cosmos für die Batch- und Streamverarbeitung und als Bereitstellungsebene für Zugriff mit geringer Latenz verwenden.

Sie können den Connector mit [Azure Databricks](https://azure.microsoft.com/services/databricks) oder [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) verwenden, die beide verwaltete Spark-Cluster in Azure bieten. Die folgende Tabelle zeigt die unterstützten Spark-Versionen.

| Komponente | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x und 2.1.x |
| Scala | 2.11 |
| Azure Databricks Runtime-Version | 3.4 und höher |

> [!WARNING]
> Dieser Connector unterstützt die Haupt-(SQL)-API von Azure Cosmos DB.
> Verwenden Sie für die Cosmos DB für MongoDB-API den [MongoDB-Spark-Connector](https://docs.mongodb.com/spark-connector/master/).
> Verwenden Sie für die Cosmos DB-Cassandra-API den [Cassandra-Spark-Connector](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Schnellstart

* Führen Sie die Schritte unter [Erste Schritte mit dem Java SDK](sql-api-async-java-get-started.md) durch, um ein Cosmos DB-Konto einzurichten und Daten aufzufüllen.
* Führen Sie die Schritte unter [Einführung in Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) durch, um einen Azure Databricks-Arbeitsbereich und -Cluster einzurichten.
* Jetzt können Sie neue Notebooks erstellen und die Cosmos DB-Connectorbibliothek importieren. Im Abschnitt [Verwenden des Connectors](#bk_working_with_connector) erhalten Sie Informationen zur Einrichtung Ihres Arbeitsbereichs.
* Im folgenden Abschnitt finden Sie Codeausschnitte, mit denen Sie mithilfe des Connectors Lese- und Schreibvorgänge durchführen können.

### <a name="reading-from-cosmos-db"></a>Lesen aus Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Spark-Datenmatrix erstellen können, um in PySpark aus Cosmos DB lesen zu können.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Schreiben in Cosmos DB

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie eine Datenmatrix in Cosmos DB in PySpark schreiben können.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

Hier sehen Sie den gleichen Codeausschnitt in Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Weitere Codeausschnitte und vollständige Beispiele finden Sie unter [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Verwenden des Connectors

Sie können den Connector über eine Quelle auf GitHub erstellen oder die Uber-JAR-Dateien von Maven über die unten aufgeführten Links herunterladen.

| Spark | Scala | Aktuelle Version |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Verwenden von Azure Databricks-Notebooks

Befolgen Sie die Anweisungen im Azure Databricks-Leitfaden im Abschnitt [Use the Azure Cosmos DB Spark connector (Verwenden des Azure Cosmos DB-Connectors)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html), um eine Bibliothek mithilfe Ihres Databricks-Arbeitsbereichs zu erstellen.

> [!NOTE]
> Beachten Sie, dass die Seite **Use the Azure Cosmos DB Spark connector (Verwenden des Azure Cosmos DB-Connectors)** aktuell nicht auf dem neuesten Stand ist. Statt die sechs JAR-Dateien in sechs verschiedene Bibliotheken zu laden, können Sie einfach die Uber-JAR-Datei von Maven unter https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) herunterladen und diese eine JAR-Datei/Bibliothek installieren.
> 

### <a name="using-spark-cli"></a>Verwenden von spark-cli

Verwenden Sie den Parameter `--packages` mit den [Maven-Koordinaten](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11) des Connectors, um den Connector mit spark-cli, also `spark-shell`, `pyspark` und `spark-submit`, zu verwenden.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Verwenden von Jupyter-Notebooks

Wenn Sie Jupyter-Notebooks in HDInsight verwenden, können Sie die spark-magic-Zelle `%%configure` verwenden, um die Maven-Koordinaten des Connectors anzugeben.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
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

* **On-Time Flight Performance with Spark and Cosmos DB (Seattle) (Pünktlichkeit von Flügen mit Spark und Cosmos DB (Seattle))** [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Verbinden Sie Spark mithilfe von HDInsight-Jupyter-Notebooks mit Cosmos DB, um Spark SQL und GraphFrames nutzen und Flugverspätungen mit Machine Learning-Pipelines vorhersagen zu können.
* **[Connecting Spark with Cosmos DB Change feed (Verbinden von Spark mit einem Cosmos DB-Änderungsfeed)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Ein Beispiel, dass veranschaulicht, wie Sie Spark mit Cosmos DB-Änderungsfeeds verbinden können.
* **Twitter Source with Apache Spark and Azure Cosmos DB Change Feed (Twitter mit Apache Spark und Azure Cosmos DB-Änderungsfeeds)**: [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Using Apache Spark to query Cosmos DB Graphs (Abfragen von Cosmos DB-Graphen mit Apache Spark)**: [IPYNB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Verbinden von Azure Databricks mit Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** mit `azure-cosmosdb-spark`.  Hier ist auch eine Azure Databricks-Version des Notebooks [On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks) verlinkt.
* **[Lambda Architecture with Azure Cosmos DB and HDInsight (Apache Spark) (Lambda-Architektur mit Azure Cosmos DB und HDInsight (Apache Spark))](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Reduzieren Sie den Betriebsaufwand für die Wartung von Big Data-Pipelines mit Cosmos DB und Spark.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen finden Sie in der `azure-cosmosdb-spark`-[Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki), u.a.:

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
