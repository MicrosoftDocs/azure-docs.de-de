---
title: Zugriff auf die Azure Cosmos DB-Cassandra-API über Spark in YARN mit HDInsight
description: Dieser Artikel behandelt die Arbeit mit der Azure Cosmos DB-Cassandra-API von Spark in YARN mit HDInsight.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359590"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Zugriff auf die Azure Cosmos DB-Cassandra-API über Spark in YARN mit HDInsight
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Dieser Artikel behandelt den Zugriff auf die Azure Cosmos DB-Cassandra-API von Spark in YARN mit HDInsight Spark über spark-shell. HDInsight ist der Hortonworks Hadoop-PaaS-Dienst von Microsoft in Azure, der auf den Objektspeicher für HDFS zurückgreift und in verschiedenen Varianten, einschließlich [Spark](../hdinsight/spark/apache-spark-overview.md), verfügbar ist.  Der Inhalt in diesem Dokument bezieht sich zwar auf HDInsight Spark, gilt jedoch für alle Hadoop-Distributionen.  

## <a name="prerequisites"></a>Voraussetzungen

* [Die Azure Cosmos DB-Cassandra-API muss bereitgestellt sein.](create-cassandra-dotnet.md#create-a-database-account)

* [Überprüfen der Grundlagen des Herstellens der Verbindung mit der Azure Cosmos DB-Cassandra-API](cassandra-spark-generic.md)

* [Ein HDInsight Spark-Cluster muss bereitgestellt sein.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Überprüfen der Codebeispiele für die Arbeit mit der Cassandra-API](cassandra-spark-generic.md#next-steps)

* [Verwenden von cqlsh für die Validierung, falls bevorzugt](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguration der Cassandra-API in Spark2**: Für den Spark-Connector für Cassandra müssen die Details der Cassandra-Verbindung als Teil des Spark-Kontexts initialisiert werden. Wenn Sie Jupyter Notebook starten, sind die Spark-Sitzung und der Kontext bereits initialisiert. Es wird davon abgeraten, den Spark-Kontext zu beenden und erneut zu initialisieren, es sei denn, er ist abgeschlossen, wobei jede Konfiguration als Teil des standardmäßigen Starts von HDInsight Jupyter Notebook festgelegt ist. Eine Problemumgehung ist, die Details der Cassandra-Instanz direkt zur Dienstkonfiguration von Spark2 mit Ambari hinzuzufügen. Dies ist eine einmalige Aktivität pro Cluster, die einen Neustart des Spark2-Diensts erfordert.
 
  1. Navigieren Sie zum Spark2-Dienst mit Ambari, und wählen Sie die Konfigurationen aus.

  2. Navigieren Sie dann zu „spark2-defaults“, fügen Sie eine neue Eigenschaft mit dem folgenden Code hinzu, und starten Sie den Spark2-Dienst neu:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Zugriff auf die Azure Cosmos DB-Cassandra-API über spark-shell

Die Spark-Shell wird für Tests bzw. explorative Analysen verwendet.

* Starten Sie spark-shell mit den erforderlichen Maven-Abhängigkeiten, die mit der Spark-Version Ihres Clusters kompatibel sind.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Führen Sie einige DDL- und DML-Vorgänge aus:

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Führen Sie CRUD-Vorgänge aus:

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Zugriff auf die Azure Cosmos DB-Cassandra-API über Jupyter Notebook

HDInsight Spark beinhaltet die Dienste Zeppelin und Jupyter Notebook. Hierbei handelt es sich um webbasierte Notebookumgebungen, die Scala und Python unterstützen. Notebooks sind hervorragend für die interaktive explorative Analyse und Zusammenarbeit geeignet, jedoch nicht für auf den Betrieb bzw. die Produktion ausgerichtete Prozesse vorgesehen.

Die folgenden Jupyter-Notebooks können in Ihre HDInsight Spark-Cluster hochgeladen werden und bieten sofort nutzbare Beispiele für die Arbeit mit der Azure Cosmos DB-Cassandra-API. Lesen Sie unbedingt das erste Notebook `1.0-ReadMe.ipynb` zur Spark-Dienstkonfiguration, um eine Verbindung mit der Azure Cosmos DB-Cassandra-API herzustellen.

Laden Sie diese Notebooks unter [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) auf Ihrem Computer herunter.
  
### <a name="how-to-upload"></a>Informationen zum Upload:
Navigieren Sie zu Scala, nachdem Sie Jupyter gestartet haben. Erstellen Sie zunächst ein Verzeichnis, und laden Sie die Notebooks in das Verzeichnis hoch. Die Schaltfläche zum Hochladen befindet sich am oberen Rand auf der rechten Seite.  

### <a name="how-to-run"></a>Informationen zur Ausführung:
Gehen Sie nacheinander die Notebooks und die einzelnen Notebookzellen durch.  Klicken Sie auf die Schaltfläche zum Ausführen am oberen Rand jedes Notebooks, um alle Zellen auszuführen, oder drücken Sie für jede Zelle die UMSCHALT+EINGABETASTE.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Zugriff mit der Azure Cosmos DB-Cassandra-API über Ihr Spark Scala-Programm

Bei automatisierten Prozessen in der Produktion werden Spark-Programme über [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) an den Cluster übermittelt.

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erstellen einer Scala-Maven-Anwendung für Spark in HDInsight mithilfe von IntelliJ](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Herstellen einer Verbindung mit der Azure Cosmos DB-Cassandra-API von Spark Scala-Programmen aus](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Vollständige Liste der Codebeispiele für die Arbeit mit der Cassandra-API](cassandra-spark-generic.md)
