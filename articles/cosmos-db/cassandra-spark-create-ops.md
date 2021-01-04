---
title: Erstellen oder Einfügen von Daten in die Cassandra-API für Azure Cosmos DB aus Spark
description: Dieser Artikel erläutert, wie Sie Beispieldaten in Cassandra-API-Tabellen für Azure Cosmos DB einfügen.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 3ec44d20b763a98683d9b947c94ad6be75180113
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092239"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Erstellen/Einfügen von Daten in die Cassandra-API für Azure Cosmos DB aus Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]
 
Dieser Artikel erläutert, wie Sie Beispieldaten in eine Tabelle in der Cassandra-API für Azure Cosmos DB aus Spark einfügen.

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

### <a name="create-a-dataframe-with-sample-data"></a>Erstellen eines Datenrahmens mit Beispieldaten

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> Die Funktion „Erstellen, falls nicht vorhanden“ wird auf Zeilenebene noch nicht unterstützt.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Speichern in der Cassandra-API für Azure Cosmos DB

Beim Speichern von Daten können Sie auch Einstellungen für die Gültigkeitsdauer und die Konsistenzrichtlinien festlegen, wie im folgenden Beispiel gezeigt:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Die Gültigkeitsdauer wird auf Spaltenebene noch nicht unterstützt.

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>RDD-API (Resilient Distributed Dataset)

### <a name="create-a-rdd-with-sample-data"></a>Erstellen eines RDD mit Beispieldaten
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Die Funktion „Erstellen, falls nicht vorhanden“ wird noch nicht unterstützt.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Speichern in der Cassandra-API für Azure Cosmos DB

Beim Speichern von Daten in der Cassandra-API können Sie auch Einstellungen für die Gültigkeitsdauer und die Konsistenzrichtlinien festlegen, wie im folgenden Beispiel gezeigt:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Daten in die Cassandra-API-Tabelle für Azure Cosmos DB eingefügt haben, lesen Sie die folgenden Artikel, um andere Vorgänge mit den in der Cassandra-API für Cosmos DB gespeicherten Daten auszuführen:
 
* [Lesevorgänge](cassandra-spark-read-ops.md)
* [Upsertvorgänge](cassandra-spark-upsert-ops.md)
* [Löschvorgänge](cassandra-spark-delete-ops.md)
* [Aggregationsvorgänge](cassandra-spark-aggregation-ops.md)
* [Tabellenkopiervorgänge an der Azure Cosmos DB-Cassandra-API von Spark aus](cassandra-spark-table-copy-ops.md)

