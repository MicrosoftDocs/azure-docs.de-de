---
title: Migrieren von Daten aus Apache Cassandra zur Azure Cosmos DB-Cassandra-API mithilfe von Databricks (Spark)
description: Erfahren Sie, wie Sie Daten mithilfe von Azure Databricks und Spark aus einer Apache Cassandra-Datenbank zur Azure Cosmos DB-Cassandra-API migrieren.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 827abcdb9eb2eb8b7328eb3e3a78f49d51e87e6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349179"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrieren von Daten aus Cassandra zum Azure Cosmos DB-Cassandra-API-Konto mithilfe von Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Die Cassandra-API in Azure Cosmos DB hat sich aus einer Reihe unterschiedlicher Gründe zu einer guten Wahl für Unternehmensworkloads entwickelt, die unter Apache Cassandra ausgeführt werden: 

* **Kein Mehraufwand für Verwaltung und Überwachung:** Beseitigt den Mehraufwand der Verwaltung und Überwachung von unzähligen Einstellungen für Betriebssystem, JVM und YAML-Dateien und deren Interaktionen.

* **Erhebliche Kosteneinsparungen:** Sie können Kosten sparen mit Azure Cosmos DB, einschließlich der Kosten für virtuelle Computer, Bandbreite und alle anwendbaren Lizenzen. Außerdem entfällt die Verwaltung der Kosten für Rechenzentren, Server, SSD-Speicher, Netzwerk und Strom. 

* **Möglichkeit der Verwendung von vorhandenem Code und Tools:** Azure Cosmos DB bietet Kompatibilität auf Verbindungsprotokollebene mit vorhandenen SDKs und Tools. Durch diese Kompatibilität ist sichergestellt, dass Sie die vorhandene Codebasis mit der Cassandra-API für Azure Cosmos DB mit nur geringfügigen Änderungen verwenden können.

Es gibt verschiedene Möglichkeiten, Datenbankworkloads von einer Plattform zu einer anderen zu migrieren. [Azure Databricks](https://azure.microsoft.com/services/databricks/) ist ein Platform as a Service-Angebot für [Apache Spark](https://spark.apache.org/), das Offlinemigrationsvorgänge in großem Umfang ermöglicht. In diesem Artikel werden die erforderlichen Schritte zum Migrieren von Daten aus nativen Apache Cassandra-Keyspaces/-Tabellen zur Azure Cosmos DB-Cassandra-API mithilfe von Azure Databricks beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

* [Bereitstellen eines Azure Cosmos DB-Cassandra-API-Kontos](create-cassandra-dotnet.md#create-a-database-account)

* [Überprüfen der Grundlagen des Herstellens der Verbindung mit der Azure Cosmos DB-Cassandra-API](cassandra-spark-generic.md)

* Überprüfen Sie die [in der Azure Cosmos DB-Cassandra-API unterstützten Features](cassandra-support.md), um die Kompatibilität sicherzustellen.

* Vergewissern Sie sich, dass im Zielkonto der Azure Cosmos DB-Cassandra-API bereits leere Keyspaces und Tabellen erstellt wurden.

* [Verwenden von cqlsh oder der gehosteten Shell für die Prüfung](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Bereitstellen eines Azure Databricks-Clusters

Nutzen Sie die Anweisungen zum [Bereitstellen eines Azure Databricks-Clusters](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Beachten Sie jedoch, dass Apache Spark 3.x derzeit nicht für den Apache Cassandra-Connector unterstützt wird. Sie müssen eine Databricks-Runtime mit einer unterstützten Version von Apache Spark (2.x) bereitstellen. Es wird empfohlen, Version 6.6 der Databricks-Runtime zu verwenden:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks-Runtime":::


## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Sie müssen dem Cluster die Apache Spark-Cassandra-Connectorbibliothek hinzufügen, um eine Verbindung mit nativen und Cosmos DB-Cassandra-Endpunkten herzustellen. Wählen Sie in Ihrem Cluster „Bibliotheken“ > „Neue installieren“ > „Maven“ > „Pakete durchsuchen“ aus:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks: „Pakete durchsuchen“":::

Geben Sie `Cassandra` in das Suchfeld ein, und wählen Sie das neueste verfügbare Maven-Repository `spark-cassandra-connector` und dann „Installieren“ aus:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks: Auswählen von Paketen":::

> [!NOTE]
> Stellen Sie sicher, dass Sie den Databricks-Cluster neu starten, nachdem die Cassandra-Connectorbibliothek installiert wurde.

## <a name="create-scala-notebook-for-migration"></a>Erstellen eines Scala-Notebooks für die Migration

Erstellen Sie in Databricks ein Scala-Notebook mit folgendem Code. Ersetzen Sie Ihre Quell- und Zielkonfigurationen für Cassandra durch die entsprechenden Anmeldeinformationen und die Quell-/Zielkeyspaces und -tabellen, und führen Sie dann Folgendes aus:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> Die Konfigurationen `spark.cassandra.output.concurrent.writes` und `connections_per_executor_max` sind wichtig, um eine [Ratenbegrenzung](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/) zu vermeiden. Diese erfolgt, wenn Anforderungen an Cosmos DB den bereitgestellten Durchsatz ([Anforderungseinheiten](./request-units.md)) überschreiten. Sie müssen diese Einstellungen möglicherweise abhängig von der Anzahl der Executors im Spark-Cluster und eventuell der Größe (und somit der RU-Kosten) jedes in die Zieltabellen geschriebenen Datensatzes anpassen.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md) 
* [Bewährte Methoden für Partitionsschlüssel](partitioning-overview.md#choose-partitionkey)
* Artikel zum [Schätzen des Durchsatzes (RU/s) mit dem Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
* [Elastische Skalierung in der Cassandra-API von Azure Cosmos DB](manage-scale-cassandra.md)