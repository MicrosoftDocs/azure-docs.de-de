---
title: Cosmos DB-Migrationsoptionen
description: In diesem Dokument werden die verschiedenen Optionen zum Migrieren Ihrer lokalen oder Clouddaten zu Azure Cosmos DB beschrieben.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097582"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Optionen zum Migrieren von lokalen oder Clouddaten zu Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Sie können Daten aus verschiedenen Datenquellen in Azure Cosmos DB laden. Azure Cosmos DB unterstützt verschiedene APIs, die als Ziele genutzt werden können. In den folgenden Szenarien werden Daten zu Azure Cosmos DB migriert:

* Verschieben von Daten aus einem Azure Cosmos-Container in einen anderen Container in derselben oder in einer anderen Datenbank
* Verschieben von Daten zwischen dedizierten Containern in freigegebene Datenbankcontainer
* Verschieben von Daten aus einem Azure Cosmos-Konto, das sich in Region1 befindet, in ein anderes Azure Cosmos-Konto in derselben oder in einer anderen Region
* Verschieben von Daten aus einer Quelle, z. B. Azure Blob Storage, einer JSON-Datei, Oracle-Datenbank, Couchbase oder DynamoDB nach Azure Cosmos DB

Um Migrationspfade aus den verschiedenen Quellen zu den unterschiedlichen Azure Cosmos DB-APIs zu unterstützen, gibt es mehrere Lösungen, die eine spezielle Behandlung für jeden Migrationspfad bereitstellen. In diesem Dokument werden die verfügbaren Lösungen aufgeführt und ihre Vor- und Nachteile beschrieben.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktoren für die Auswahl des Migrationstools

Die folgenden Faktoren haben Einfluss auf die Auswahl des Migrationstools:

* **Online- oder Offlinemigration:** Viele Migrationstools bieten nur einen Pfad für eine einmalige Migration. Dies bedeutet, dass bei Anwendungen, die auf die Datenbank zugreifen, möglicherweise eine gewisse Ausfallzeit auftritt. Einige Migrationslösungen bieten eine Möglichkeit, eine Livemigration durchzuführen, bei der eine Replikationspipeline zwischen der Quelle und dem Ziel eingerichtet ist.

* **Datenquelle** : Die vorhandenen Daten können sich in verschiedenen Datenquellen wie Oracle DB2, DataStax Cassanda, Azure SQL-Datenbank, PostgreSQL usw. befinden. Außerdem können sich die Daten auch in einem vorhandenen Azure Cosmos DB-Konto befinden und das Ziel der Migration besteht darin, das Datenmodell zu ändern oder die Daten in einem Container mit einem anderen Partitionsschlüssel neu zu partitionieren.

* **Azure Cosmos DB-API:** Für die SQL-API in Azure Cosmos DB gibt es eine Reihe von Tools, die vom Azure Cosmos DB-Team für die unterschiedlichen Migrationsszenarien entwickelt wurden. Alle anderen APIs enthalten einen eigenen spezialisierten Satz von Tools, der von der Community entwickelt und verwaltet wird. Da Azure Cosmos DB diese APIs auf einer festen Protokollebene unterstützt, sollten diese Tools während der Migration von Daten zu Azure Cosmos DB ebenfalls unverändert funktionieren. Sie erfordern jedoch möglicherweise eine benutzerdefinierte Behandlung der Drosselung, da dieses Konzept für Azure Cosmos DB spezifisch ist.

* **Datengröße:** Die meisten Migrationstools funktionieren bei kleineren Datasets sehr gut. Wenn das Dataset jedoch einige Hundert Gigabyte überschreitet, ist die Auswahl an Migrationstools beschränkt. 

* **Erwartete Migrationsdauer:** Migrationsvorgänge können so konfiguriert werden, dass sie langsam und schrittweise ausgeführt werden und damit weniger Durchsatz beanspruchen, oder so, dass der gesamten Durchsatz des Ziel Azure Cosmos DB-Zielcontainers genutzt wird, um die Migration in kürzerer Zeit abzuschließen.

## <a name="azure-cosmos-db-sql-api"></a>SQL-API von Azure Cosmos DB

|Migrationstyp|Lösung|Unterstützte Quellen|Unterstützte Ziele|Überlegungen|
|---------|---------|---------|---------|---------|
|Offline|[Datenmigrationstool](import-data.md)| &bull;JSON-/CSV-Dateien<br/>&bull;SQL-API von Azure Cosmos DB<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob Storage|&bull;SQL-API von Azure Cosmos DB<br/>&bull;Azure Cosmos DB-Tabellen-API<br/>&bull;JSON-Dateien |&bull; Einfache Einrichtung und Unterstützung mehrerer Quellen <br/>&bull; Nicht geeignet für große Datasets|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-Dateien<br/>&bull;SQL-API von Azure Cosmos DB<br/>&bull;Azure Cosmos DB-API für MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/> <br/>Informationen zu weiteren unterstützten Quellen finden Sie im Artikel [Azure Data Factory](../data-factory/connector-overview.md).|&bull;SQL-API von Azure Cosmos DB<br/>&bull;Azure Cosmos DB-API für MongoDB<br/>&bull;JSON-Dateien <br/><br/> Informationen zu weiteren unterstützten Zielen finden Sie im Artikel [Azure Data Factory](../data-factory/connector-overview.md). |&bull; Einfache Einrichtung und Unterstützung mehrerer Quellen<br/>&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Geeignet für große Datasets <br/>&bull; Fehlende Prüfpunktausführung: Wenn während der Migration ein Problem auftritt, müssen Sie den gesamten Migrationsprozess neu starten.<br/>&bull; Fehlende Warteschlange für unzustellbare Nachrichten: Dies bedeutet, dass wenige fehlerhafte Dateien den gesamten Migrationsprozess unterbrechen können.|
|Offline|[Azure Cosmos DB-Spark-Connector](spark-connector.md)|Azure Cosmos DB-SQL-API. <br/><br/>Sie können weitere Quellen mit zusätzlichen Connectors aus dem Spark-Ökosystem verwenden.| Azure Cosmos DB-SQL-API. <br/><br/>Sie können weitere Ziele mit zusätzlichen Connectors aus dem Spark-Ökosystem verwenden.| &bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Geeignet für große Datasets <br/>&bull; Erfordert eine benutzerdefinierte Spark-Einrichtung <br/>&bull; Spark bietet keine Toleranz gegenüber Inkonsistenzen beim Schema. Dies kann während der Migration ein Problem darstellen. |
|Offline|[Benutzerdefiniertes Tool mit Cosmos DB-BulkExecutor-Bibliothek](migrate-cosmosdb-data.md)| Die Quelle ist abhängig von benutzerdefiniertem Code. | SQL-API von Azure Cosmos DB| &bull; Bietet Prüfpunktfunktionen für unzustellbare Nachrichten und damit eine höhere Resilienz bei der Migration <br/>&bull; Geeignet für sehr große Datasets (> 10 TB)  <br/>&bull; Erfordert die benutzerdefinierte Einrichtung dieses Tools, das als App Service ausgeführt wird |
|Online|[Cosmos DB-Funktionen + ChangeFeed-API](change-feed-functions.md)| SQL-API von Azure Cosmos DB | SQL-API von Azure Cosmos DB| &bull; Einfache Einrichtung <br/>&bull; Funktioniert nur, wenn die Quelle ein Azure Cosmos DB-Container ist <br/>&bull; Nicht geeignet für große Datasets <br/>&bull; Erfasst keine Löschvorgänge im Quellcontainer |
|Online|[Benutzerdefinierter Migrationsdienst mithilfe von ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| SQL-API von Azure Cosmos DB | SQL-API von Azure Cosmos DB| &bull; Ermöglicht die Statusnachverfolgung <br/>&bull; Funktioniert nur, wenn die Quelle ein Azure Cosmos DB-Container ist <br/>&bull; Funktioniert auch für größere Datasets<br/>&bull; Erfordert die Einrichtung einer App Service-Instanz zum Hosten der Änderungsfeedverarbeitung <br/>&bull; Erfasst keine Löschvorgänge im Quellcontainer|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Informationen zu weiteren unterstützten Quellen finden Sie auf der [Striim-Website](https://www.striim.com/sources-and-targets/). |&bull;SQL-API von Azure Cosmos DB <br/>&bull; Cassandra-API für Azure Cosmos DB<br/><br/> Informationen zu weiteren unterstützten Zielen finden Sie auf der [Striim-Website](https://www.striim.com/sources-and-targets/). | &bull; Funktioniert mit einer Vielzahl von Quellen wie Oracle, DB2, SQL Server<br/>&bull; Einfaches Erstellen von ETL-Pipelines; einschließlich eines Dashboards für die Überwachung <br/>&bull; Unterstützt größere Datasets <br/>&bull; Da dies ein Drittanbietertool ist, muss es über den Marketplace erworben und in der Benutzerumgebung installiert werden.|

## <a name="azure-cosmos-db-mongo-api"></a>Mongo-API von Azure Cosmos DB

|Migrationstyp|Lösung|Unterstützte Quellen|Unterstützte Ziele|Überlegungen|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|Azure Cosmos DB-API für MongoDB |&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| Azure Cosmos DB-API für MongoDB| &bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-Dateien<br/>&bull;SQL-API von Azure Cosmos DB<br/>&bull;Azure Cosmos DB-API für MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/><br/> Informationen zu weiteren unterstützten Quellen finden Sie im Artikel [Azure Data Factory](../data-factory/connector-overview.md). | &bull;SQL-API von Azure Cosmos DB<br/>&bull;Azure Cosmos DB-API für MongoDB <br/>&bull; JSON-Dateien <br/><br/> Informationen zu weiteren unterstützten Zielen finden Sie im Artikel [Azure Data Factory](../data-factory/connector-overview.md).| &bull; Einfache Einrichtung und Unterstützung mehrerer Quellen <br/>&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Geeignet für große Datasets <br/>&bull; Fehlende Prüfpunktausführung: Wenn während der Migration ein Problem auftritt, muss der gesamte Migrationsprozess neu gestartet werden.<br/>&bull; Fehlende Warteschlange für unzustellbare Nachrichten: Dies bedeutet, dass wenige fehlerhafte Dateien den gesamten Migrationsprozess unterbrechen können. <br/>&bull; Erfordert benutzerdefinierten Code, um den Lesedurchsatz für bestimmte Datenquellen zu erhöhen|
|Offline|[Vorhandene Mongo-Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | Azure Cosmos DB-API für MongoDB| &bull; Einfache Einrichtung und Integration <br/>&bull; Erfordert die benutzerdefinierte Behandlung von Drosselungen|

## <a name="azure-cosmos-db-cassandra-api"></a>Cassandra-API für Azure Cosmos DB

|Migrationstyp|Lösung|Unterstützte Quellen|Unterstützte Ziele|Überlegungen|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh-Befehl COPY](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV-Dateien | Cassandra-API für Azure Cosmos DB| &bull; Einfache Einrichtung <br/>&bull; Nicht geeignet für große Datasets <br/>&bull; Funktioniert nur, wenn die Quelle eine Cassandra-Tabelle ist|
|Offline|[Kopieren der Tabelle mit Spark](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Cassandra-API für Azure Cosmos DB| Cassandra-API für Azure Cosmos DB | &bull; Ermöglicht die Verwendung von Spark-Funktionen zum Parallelisieren von Transformation und Erfassung <br/>&bull; Erfordert die Konfiguration mit einer benutzerdefinierten Wiederholungsrichtlinie für die Behandlung der Drosselung|
|Online|[Striim (aus Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Informationen zu weiteren unterstützten Quellen finden Sie auf der [Striim-Website](https://www.striim.com/sources-and-targets/).|&bull;SQL-API von Azure Cosmos DB<br/>&bull;Cassandra-API für Azure Cosmos DB <br/><br/> Informationen zu weiteren unterstützten Zielen finden Sie auf der [Striim-Website](https://www.striim.com/sources-and-targets/).| &bull; Funktioniert mit einer Vielzahl von Quellen wie Oracle, DB2, SQL Server <br/>&bull; Einfaches Erstellen von ETL-Pipelines; einschließlich eines Dashboards für die Überwachung <br/>&bull; Unterstützt größere Datasets <br/>&bull; Da dies ein Drittanbietertool ist, muss es über den Marketplace erworben und in der Benutzerumgebung installiert werden.|
|Online|[Blitzz (aus Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Informationen zu weiteren unterstützten Quellen finden Sie auf der [Blitzz-Website](https://www.blitzz.io/). |Cassandra-API für Azure Cosmos DB. <br/><br/>Informationen zu weiteren unterstützten Zielen finden Sie auf der [Blitzz-Website](https://www.blitzz.io/). | &bull; Unterstützt größere Datasets <br/>&bull; Da dies ein Drittanbietertool ist, muss es über den Marketplace erworben und in der Benutzerumgebung installiert werden.|

## <a name="other-apis"></a>Andere APIs

Bei anderen APIs als SQL-API, Mongo-API und Cassandra-API werden in den Umgebungen der einzelnen APIs verschiedene Tools unterstützt. 

**Tabellen-API** 

* [Datenmigrationstool](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin-API**

* [Graph-BulkExecutor-Bibliothek](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr darüber, indem Sie die Beispielanwendungen ausprobieren, die die BulkExecutor-Bibliothek in [.NET](bulk-executor-dot-net.md) und [Java](bulk-executor-java.md) nutzen. 
* Die BulkExecutor-Bibliothek ist in den Cosmos DB Spark-Connector integriert. Weitere Informationen finden Sie im Artikel [Spark-Connector für Azure Cosmos DB](spark-connector.md).  
* Wenden Sie sich an das Azure Cosmos DB-Team, indem Sie unter dem Problemtyp „General Advisory“ (Allgemeine Ratschläge) und dem Problemuntertyp „Large (TB+) migrations“ (Umfangreiche Migrationen (TB+)) ein Supportticket erstellen, um weitere Hilfe zu größeren Migrationen zu erhalten.
