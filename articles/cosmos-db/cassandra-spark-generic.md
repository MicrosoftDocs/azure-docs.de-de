---
title: Arbeiten mit der Azure Cosmos DB-Cassandra-API von Spark
description: Dieser Artikel ist die Hauptseite für die Integration der Cosmos DB-Cassandra-API von Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090510"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Herstellen einer Verbindung mit der Azure Cosmos DB-Cassandra-API von Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Dieser Artikel stammt aus einer Reihe von Artikeln zur Integration der Cosmos DB-Cassandra-API von Spark. In den Artikeln werden die Konnektivität, DDL-Vorgänge (Data Definition Language), grundlegende DML-Vorgänge (Data Manipulation Language) und die erweiterte Integration der Azure Cosmos DB-Cassandra-API von Spark behandelt. 

## <a name="prerequisites"></a>Voraussetzungen
* [Ein Azure Cosmos DB-Cassandra-API-Konto muss vorhanden sein.](create-cassandra-dotnet.md#create-a-database-account)

* Stellen Sie eine Spark-Umgebung Ihrer Wahl [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Sonstiges] bereit.

## <a name="dependencies-for-connectivity"></a>Abhängigkeiten für die Konnektivität
* **Spark-Connector für Cassandra:** Der Spark-Connector wird verwendet, um eine Verbindung mit der Azure Cosmos DB-Cassandra-API herzustellen.  Identifizieren und verwenden Sie die Version des Connectors unter [Maven > Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector), die mit den Spark- und Scala-Versionen Ihrer Spark-Umgebung kompatibel ist.

* **Bibliothek für das Azure Cosmos DB-Hilfsprogramm für die Cassandra-API:** Neben dem Spark-Connector benötigen Sie eine weitere Bibliothek mit dem Namen [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) von Azure Cosmos DB. Diese Bibliothek enthält benutzerdefinierte Klassen für die Verbindungsfactory und Wiederholungsrichtlinie.

  Die Wiederholungsrichtlinie in Azure Cosmos DB ist für die Verarbeitung von Ausnahmen mit dem HTTP-Statuscode 429 („Anforderungsrate ist groß“) konfiguriert. Die Azure Cosmos DB-Cassandra-API übersetzt diese Ausnahmen in Überladungsfehler im nativen Cassandra-Protokoll, und Sie können mit Backoffs wiederholen. Da Azure Cosmos DB das bereitgestellte Durchsatzmodell verwendet, treten Ausnahmen in Bezug auf große Anforderungsraten auf, wenn die Rate der eingehenden/ausgehenden Daten steigt. Durch die Wiederholungsrichtlinie werden Ihre Spark-Aufträge vor Datenspitzen geschützt, die den für Ihren Container zugeordneten Durchsatz vorübergehend überschreiten.

  > [!NOTE] 
  > Die Wiederholungsrichtlinie kann Ihre Spark-Aufträge nur vor temporären Spitzen schützen. Wenn Sie nicht genügend RUs konfiguriert haben, die zum Ausführen Ihrer Workloads erforderlich sind, ist die Wiederholungsrichtlinie nicht anwendbar, und die Richtlinienklasse löst erneut die Ausnahme aus.

* **Verbindungsdetails zum Azure Cosmos DB-Konto:** Der Name, der Kontoendpunkt und der Schlüssel Ihres Azure Cassandra-API-Kontos.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parameter zur Konfiguration des Durchsatzes für den Spark-Connector

Die folgende Tabelle enthält die für die Azure Cosmos DB-Cassandra-API spezifischen Durchsatzkonfigurationsparameter, die vom Connector bereitgestellt werden. Eine detaillierte Liste aller Konfigurationsparameter finden Sie im GitHub-Repository für den Cassandra-Connector für Spark auf der Seite [Konfigurationsreferenz](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md).

| **Eigenschaftenname** | **Standardwert** | **Beschreibung** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Anzahl der Zeilen pro Batch. Legen Sie diesen Parameter auf „1“ fest. Dieser Parameter wird verwendet, um einen höheren Durchsatz für umfassende Workloads zu erzielen. |
| spark.cassandra.connection.connections_per_executor_max  | Keine | Maximale Anzahl von Verbindungen pro Knoten pro Executor. 10 x n entspricht 10 Verbindungen pro Knoten in einem Cassandra-Cluster mit n Knoten. Wenn Sie also 5 Verbindungen pro Knoten pro Executor für einen Cassandra-Cluster mit 5 Knoten benötigen, sollten Sie diese Konfiguration auf „25“ festlegen. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder der Anzahl von Executors, für die Ihre Spark-Aufträge konfiguriert sind.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definiert die Anzahl von parallelen Schreibvorgängen, die pro Executor auftreten können. Da Sie „batch.size.rows“ auf „1“ festlegen, skalieren Sie diesen Wert entsprechend hoch. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder dem Durchsatz, den Sie für Ihren Workload erzielen möchten. |
| spark.cassandra.concurrent.reads |  512 | Definiert die Anzahl der parallelen Lesevorgänge, die pro Executor auftreten können. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder dem Durchsatz, den Sie für Ihren Workload erzielen möchten.  |
| spark.cassandra.output.throughput_mb_per_sec  | Keine | Definiert den gesamten Schreibdurchsatz pro Executor. Dieser Parameter kann als oberer Grenzwert für den Durchsatz Ihres Spark-Auftrags verwendet werden und basiert auf dem bereitgestellten Durchsatz Ihres Cosmos-Containers.   |
| spark.cassandra.input.reads_per_sec| Keine   | Definiert den gesamten Lesedurchsatz pro Executor. Dieser Parameter kann als oberer Grenzwert für den Durchsatz Ihres Spark-Auftrags verwendet werden und basiert auf dem bereitgestellten Durchsatz Ihres Cosmos-Containers.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Definiert die Anzahl der Batches pro Spark-Task, die In-Memory gespeichert werden können, bevor diese an die Cassandra-API gesendet werden. |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definiert den Zeitraum, in dem nicht verwendete Verbindungen zur Verfügung stehen. | 

Passen Sie den Durchsatz und den Parallelitätsgrad dieser Parameter basierend auf der Workload an, die Sie für Ihre Spark-Aufträge erwarten, sowie dem Durchsatz, den Sie für Ihr Cosmos DB-Konto bereitgestellt haben.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Herstellen einer Verbindung mit der Azure Cosmos DB-Cassandra-API von Spark

### <a name="cqlsh"></a>cqlsh
Die folgenden Befehle verdeutlichen, wie eine Verbindung mit der Azure Cosmos DB-Cassandra-API über cqlsh hergestellt werden kann.  Dies ist nützlich für die Validierung, während Sie die Beispiele in Spark durchgehen.<br>
**Auf Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Im folgenden Artikel werden die Azure Databricks-Clusterbereitstellung, die Clusterkonfiguration für die Verbindung mit der Azure Cosmos DB-Cassandra-API sowie einige Beispielnotebooks behandelt, die u.a. DDL- und DML-Vorgänge abdecken.<BR>
[Zugreifen auf Azure Cosmos DB-Cassandra-API-Daten von Azure Databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight Spark
Im folgenden Artikel werden der HDinsight Spark-Dienst, die Bereitstellung, die Clusterkonfiguration für die Verbindung mit der Azure Cosmos DB-Cassandra-API sowie einige Beispielnotebooks behandelt, die u.a. DDL- und DML-Vorgänge abdecken.<BR>
[Zugreifen auf die Azure Cosmos DB-Cassandra-API über Spark in YARN mit HDInsight](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Allgemeiner Überblick über die Spark-Umgebung
Die obigen Abschnitte gelten speziell für Azure Spark-basierte PaaS-Dienste. In diesem Abschnitt wird ein allgemeiner Überblick über die Spark-Umgebung vermittelt.  Nachfolgend werden Connectorabhängigkeiten, Importe und die Spark-Sitzungskonfiguration beschrieben. Der Abschnitt „Nächste Schritte“ enthält Codebeispiele für DDL- und DML-Vorgänge und vieles mehr.  

#### <a name="connector-dependencies"></a>Connectorabhängigkeiten:

1. Fügen Sie die Maven-Koordinaten zum Abrufen des [Cassandra-Connectors für Spark](cassandra-spark-generic.md#dependencies-for-connectivity) hinzu.
2. Fügen Sie die Maven-Koordinaten für die [Bibliothek für das Azure Cosmos DB-Hilfsprogramm](cassandra-spark-generic.md#dependencies-for-connectivity) für die Cassandra-API hinzu.

#### <a name="imports"></a>Importe:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark-Sitzungskonfiguration:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln wird die Spark-Integration mit der Azure Cosmos DB-Cassandra-API behandelt. 
 
* [DDL-Vorgänge in der Azure Cosmos DB-Cassandra-API von Spark](cassandra-spark-ddl-ops.md)
* [Erstellungs-/Einfügevorgänge](cassandra-spark-create-ops.md)
* [Lesevorgänge](cassandra-spark-read-ops.md)
* [Upsertvorgänge](cassandra-spark-upsert-ops.md)
* [Löschvorgänge](cassandra-spark-delete-ops.md)
* [Aggregationsvorgänge](cassandra-spark-aggregation-ops.md)
* [Tabellenkopiervorgänge an der Azure Cosmos DB-Cassandra-API von Spark aus](cassandra-spark-table-copy-ops.md)