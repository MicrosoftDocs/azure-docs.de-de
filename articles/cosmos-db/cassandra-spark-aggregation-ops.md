---
title: Aggregierungsvorgänge an Azure Cosmos DB-Cassandra-API-Tabellen von Spark aus
description: In diesem Artikel werden grundlegende Aggregierungsvorgänge an Azure Cosmos DB-Cassandra-API-Tabellen von Spark aus beschrieben.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087620"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Aggregierungsvorgänge an Azure Cosmos DB-Cassandra-API-Tabellen von Spark aus 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In diesem Artikel werden grundlegende Aggregierungsvorgänge an Azure Cosmos DB-Cassandra-API-Tabellen von Spark aus beschrieben. 

> [!NOTE]
> Serverseitige Filterung und serverseitige Aggregierung werden derzeit von der Cassandra-API von Azure Cosmos DB nicht unterstützt.

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
## <a name="sample-data-generator"></a>Beispieldatengenerator

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Zählvorgang


### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Ausgabe:**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Datenrahmen-API

Die Zählung von Datenrahmen wird derzeit nicht unterstützt.  Das folgende Beispiel zeigt die Ausführung einer Zählung von Datenrahmen nach Beibehalten des Datenrahmens im Arbeitsspeicher als Problemumgehung.

Wählen Sie eine [Speicheroption]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) aus den folgenden verfügbaren Optionen, um das Problem „Nicht genügend Arbeitsspeicher“ zu vermeiden:

* MEMORY_ONLY: Dies ist die Standardspeicheroption. Speichert das RDD als deserialisierte Java-Objekte in der JVM. Wenn das RDD nicht in den Arbeitsspeicher passt, werden einige Partitionen nicht zwischengespeichert und im laufenden Betrieb jedes Mal bei Bedarf neu berechnet.

* MEMORY_AND_DISK: Speichert das RDD als deserialisierte Java-Objekte in der JVM. Wenn das RDD nicht in den Arbeitsspeicher passt, speichern Sie die Partitionen, die nicht auf den Datenträger passen, und lesen Sie sie bei Bedarf an ihrem Speicherort.

* MEMORY_ONLY_SER (Java/Scala): Speichert das RDD als serialisierte Java-Objekte – 1-Byte-Array pro Partition. Diese Option ist platzsparend im Vergleich zu deserialisierten Objekten, insbesondere bei Verwendung eines schnellen Serialisierungsprogramms, aber das Lesen erfordert mehr CPU-Leistung.

* MEMORY_AND_DISK_SER (Java/Scala): Diese Speicheroption entspricht MEMORY_ONLY_SER – der einzige Unterschied besteht darin, dass Partitionen überlaufen, die nicht in den Datenträgerspeicher passen, anstatt dass sie bei Bedarf neu berechnet werden.

* DISK_ONLY: Speichert die RDD-Partitionen nur auf dem Datenträger.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: Identisch mit den oben genannten Ebenen, aber jede Partition wird auf beiden Clusterknoten repliziert.

* OFF_HEAP (experimentell): Ähnelt MEMORY_ONLY_SER, speichert aber die Daten im Off-Heap-Arbeitsspeicher und benötigt Off-Heap-Arbeitsspeicher, um im voraus aktiviert zu werden. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Durchschnittsvorgang

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Ausgabe:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Datenrahmen-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Ausgabe:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Ausgabe:**
```
16.016000175476073
```

## <a name="min-operation"></a>Minimalvorgang

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Ausgabe:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Datenrahmen-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Ausgabe:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Ausgabe:**
```
11.33
```

## <a name="max-operation"></a>Maximalvorgang

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Datenrahmen-API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Ausgabe:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Ausgabe:**
```
22.45
```

## <a name="sum-operation"></a>Summierungsvorgang

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Ausgabe:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Datenrahmen-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Ausgabe:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Ausgabe:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Spitzen- oder vergleichbarer Vorgang

### <a name="rdd-api"></a>RDD-API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Ausgabe:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Datenrahmen-API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Ausgabe:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Ausführung von Tabellenkopiervorgängen finden Sie unter:

* [Tabellenkopiervorgänge an der Azure Cosmos DB-Cassandra-API von Spark aus](cassandra-spark-table-copy-ops.md)
