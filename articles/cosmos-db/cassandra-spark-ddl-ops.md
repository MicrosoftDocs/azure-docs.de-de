---
title: DDL-Vorgänge in der Azure Cosmos DB-Cassandra-API von Spark
description: In diesem Artikel werden DDL-Vorgänge im Keyspace und in Tabellen für die Azure Cosmos DB-Cassandra-API von Spark beschrieben.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 73d31fff362807937cbd87b8e1313cf601909802
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092176"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-Vorgänge in der Azure Cosmos DB-Cassandra-API von Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In diesem Artikel werden DDL-Vorgänge im Keyspace und in Tabellen für die Azure Cosmos DB-Cassandra-API von Spark beschrieben.

## <a name="cassandra-api-related-configuration"></a>Konfiguration für die Cassandra-API 

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

## <a name="keyspace-ddl-operations"></a>Keyspace-DDL-Vorgänge

### <a name="create-a-keyspace"></a>Erstellen eines Keyspace

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

Führen Sie den folgenden Befehl in cqlsh aus. Daraufhin sollte der Keyspace, den Sie zuvor erstellt haben, angezeigt werden.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Verwerfen eines Keyspace

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Tabellen-DDL-Vorgänge

**Überlegungen:**  

- Durchsatz kann mithilfe der Anweisung „create table“ auf Tabellenebene zugewiesen werden.  
- In einem Partitionsschlüssel können 20 GB Daten gespeichert werden.  
- In einem Datensatz können bis zu 2 MB Daten gespeichert werden.  
- In einem Partitionsschlüsselbereich können mehrere Partitionsschlüssel gespeichert werden.

### <a name="create-a-table"></a>Erstellen einer Tabelle

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

Führen Sie den folgenden Befehl in cqlsh aus. Daraufhin sollte die Tabelle „books“ angezeigt werden: 

```bash
USE books_ks;
DESCRIBE books;
```

Der bereitgestellte Durchsatz und die Standard-TTL-Werte werden in der Ausgabe des vorherigen Befehls nicht angezeigt. Sie können diese Werte im Portal abrufen.

### <a name="alter-table"></a>ALTER TABLE

Sie können die folgenden Werte mit dem Befehl ALTER TABLE ändern:

* Bereitgestellter Durchsatz 
* Wert der Gültigkeitsdauer
<br>Spaltenänderungen werden derzeit nicht unterstützt.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Löschen einer Tabelle

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Überprüfen in cqlsh

Führen Sie den folgenden Befehl in cqlsh aus. Sie sollten sehen, dass die Tabelle „books“ nicht mehr verfügbar ist:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie nach dem Erstellen von Keyspace und Tabelle mit den folgenden Artikeln für CRUD-Vorgänge und Ähnliches fort:
 
* [Erstellungs-/Einfügevorgänge](cassandra-spark-create-ops.md)  
* [Lesevorgänge](cassandra-spark-read-ops.md)  
* [Upsertvorgänge](cassandra-spark-upsert-ops.md)  
* [Löschvorgänge](cassandra-spark-delete-ops.md)  
* [Aggregationsvorgänge](cassandra-spark-aggregation-ops.md)  
* [Tabellenkopiervorgänge an der Azure Cosmos DB-Cassandra-API von Spark aus](cassandra-spark-table-copy-ops.md)  
