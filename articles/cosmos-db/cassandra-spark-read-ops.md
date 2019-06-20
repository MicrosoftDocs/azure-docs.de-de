---
title: Lesen von Daten in Cassandra-API-Tabellen mithilfe von Spark
titleSufix: Azure Cosmos DB
description: In diesem Artikel wird beschrieben, wie Sie Daten in Cassandra-API-Tabellen in Azure Cosmos DB lesen.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893398"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Lesen von Daten in Azure Cosmos DB-Cassandra-API-Tabellen mithilfe von Spark

 In diesem Artikel wird beschrieben, wie Sie in der Azure Cosmos DB-Cassandra-API von Spark gespeicherte Daten lesen.

## <a name="cassandra-api-configuration"></a>Konfiguration der Cassandra-API
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Datenrahmen-API

### <a name="read-table-using-sessionreadformat-command"></a>Lesen einer Tabelle mit dem Befehl „session.read.format“

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Lesen einer Tabelle mithilfe von spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Lesen von bestimmten Spalten in einer Tabelle

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Anwenden von Filtern

Die Prädikatweitergabe wird derzeit nicht unterstützt. Die unten aufgeführten Beispiele stellen die clientseitige Filterung dar. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD-API

### <a name="read-table"></a>Lesen einer Tabelle
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Lesen von bestimmten Spalten in einer Tabelle

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL-Ansichten 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Erstellen einer temporären Ansicht eines Datenframes

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Ausführen von Abfragen mit der Ansicht

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Nächste Schritte

Im Folgenden finden Sie weitere Artikel zur Arbeit mit der Azure Cosmos DB-Cassandra-API von Spark:
 
 * [Upsertvorgänge](cassandra-spark-upsert-ops.md)
 * [Löschvorgänge](cassandra-spark-delete-ops.md)
 * [Aggregationsvorgänge](cassandra-spark-aggregation-ops.md)
 * [Tabellenkopiervorgänge](cassandra-spark-table-copy-ops.md)

