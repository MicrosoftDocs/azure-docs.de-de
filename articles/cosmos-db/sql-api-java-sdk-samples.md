---
title: 'Azure Cosmos DB SQL-API: Java SDK v4-Beispiele'
description: Java-Beispiele für häufige Aufgaben mit der SQL-API von Azure Cosmos DB, einschließlich CRUD-Vorgängen, finden Sie auf GitHub.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 6b732c2af204cba6e93f9207648a5123b75660b1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545535"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL-API: Java SDK v4-Beispiele
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-Beispiele](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-Beispiele](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-Beispiele](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-Beispiele](sql-api-spring-data-sdk-samples.md)
> * [Node.js-Beispiele](sql-api-nodejs-samples.md)
> * [Python-Beispiele](sql-api-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Weitere Informationen zu Java SDK v4 finden Sie in den [Versionshinweisen](sql-api-sdk-java-v4.md) zu Azure Cosmos DB Java SDK v4, im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos), in den [Tipps zur Leistungssteigerung](performance-tips-java-sdk-v4-sql.md) und im [Leitfaden zur Problembehandlung](troubleshoot-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK v4. Wenn Sie aktuell eine ältere Version als v4 verwenden, lesen Sie den Leitfaden [Migrieren zu Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md), um Hilfe beim Aktualisieren auf v4 zu erhalten.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Die neuesten Beispielanwendungen, in denen CRUD-Vorgänge und andere gängige Vorgänge für Azure Cosmos DB-Ressourcen ausgeführt werden, finden Sie im GitHub-Repository [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples). Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen Java-Beispielprojektdateien. 
* Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

Zum Ausführen dieser Beispielanwendung benötigen Sie Folgendes:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Optional können Sie Maven verwenden, um die aktuellen Binärdateien von Azure Cosmos DB Java SDK v4 zur Verwendung in Ihrem Projekt abzurufen. Maven fügt alle erforderlichen Abhängigkeiten automatisch hinzu. Andernfalls können Sie die in der Datei „pom.xml“ aufgelisteten Abhängigkeiten direkt herunterladen und Ihrem Buildpfad hinzufügen.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Ausführen der Beispielanwendungen**

Klonen des Beispielrepositorys:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Sie können die Beispiele entweder in einer IDE (Eclipse, IntelliJ oder VSCODE) oder über die Befehlszeile mit Maven ausführen.

Diese Umgebungsvariablen müssen festgelegt sein,

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

um den Beispielen Lese-/Schreibzugriff auf Ihr Konto zu gewähren.

Zum Ausführen eines Beispiels müssen Sie die zugehörige Hauptklasse (Main Class) angeben, 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

wobei *sample.synchronicity.MainClass* Folgendem entsprechen kann:
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *(für changefeed gibt es nur ein asychrones und kein synchrones Beispiel)* usw.

> [!NOTE]
> Jedes Beispiel ist eigenständig mit eigener Einrichtung und Bereinigung. In den Beispielen werden mehrere Aufrufe ausgegeben, um einen `CosmosContainer` zu erstellen. Dabei wird Ihrem Abonnement jedes Mal 1 Stunde Nutzung gemäß der Leistungsstufe der erstellten Sammlung berechnet. 
> 
> 

## <a name="database-examples"></a>Datenbankbeispiele
Die Datei [Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Datenbanken zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [Lesen einer Datenbank nach ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [Lesen aller Datenbanken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Löschen einer Datenbank](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Sammlungsbeispiele
Die Datei [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Sammlungen zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md).

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [Ändern der konfigurierten Leistung einer Sammlung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Abrufen einer Auflistung nach ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Lesen aller Sammlungen in einer Datenbank](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Löschen einer Sammlung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Beispiele der Autoskalierungssammlung

Um vor dem Ausführen dieser Beispiele mehr über die Autoskalierung zu erfahren, lesen Sie diese Anweisungen zum Aktivieren der Autoskalierung in Ihrem [Konto](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) und in Ihren [Datenbanken und Containern](./provision-throughput-autoscale.md).

Die Datei [autoscale Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank mit einem definierten maximalen per Autoskalierung bereitgestellten Durchsatz](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Die Datei [autoscale Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung mit einem definierten maximalen per Autoskalierung bereitgestellten Durchsatz](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [Ändern des für eine Sammlung konfigurierten maximalen per Autoskalierung bereitgestellten Durchsatzes](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Lesen der Konfiguration des per Autoskalierung bereitgestellten Durchsatzes für eine Sammlung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Beispiele der Analysespeichersammlung

Die Datei [Analytical storage Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um vor dem Ausführen der folgenden Beispiele mehr über die Azure Cosmos-Sammlungen zu erfahren, lesen Sie die Informationen zu Azure Cosmos DB-Synapse und -Analysespeicher.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Dokumentbeispiele
Die Datei [Document CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Dokumente zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md).

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Dokuments](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Lesen eines Dokuments nach ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Abfragen von Dokumenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Ersetzen eines Dokuments](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Aktualisieren und Einfügen (Upsert) eines Dokuments](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Löschen eines Dokuments](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Ersetzen eines Dokuments mit bedingter ETag-Überprüfung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Lesen eines Dokuments, nur wenn das Dokument geändert wurde](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Indizierungsbeispiele
Die Datei [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Indizierung in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele die Konzeptartikel [Indizierungsrichtlinien](index-policy.md), [Indizierungstypen](index-overview.md#index-kinds) und [Indizierungspfade](index-policy.md#include-exclude-paths). 

| Aufgabe | API-Referenz |
| --- | --- |
| Ausschließen eines Dokuments aus dem Index | ExcludedIndex<br>IndexingPolicy |
| Verwenden der verzögerten Indizierung | IndexingPolicy.IndexingMode |
| [Einschließen bestimmter Dokumentpfade in den Index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Ausschließen bestimmter Dokumentpfade aus dem Index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Erstellen eines zusammengesetzten Index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Erzwingen eines Bereichsscanvorgangs für einen Pfad mit Hashindizierung | FeedOptions.EnableScanInQuery |
| Verwenden von Bereichsindizes für den Datentyp „String“ | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Durchführen einer Indextransformation | - |
| [Erstellen eines „GeoSpatial“-Index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Weitere Informationen zur Indizierung finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Beispiele für Abfragen
Die Datei [Query Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) zeigt, wie die folgenden Aufgaben mithilfe der SDL-Abfragegrammatik ausgeführt werden. Machen Sie sich im Artikel [SQL-Abfragebeispiele](./sql-query-getting-started.md) mit der SQL-Abfragereferenz in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Abfragen aller Dokumente](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Abfragen der Gleichheit mit „==“](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Abfragen der Ungleichheit mit „!=“ und „NOT“](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Abfrage mit Bereichsoperatoren wie „&gt;“, „&lt;“, „&gt;=“, „&lt;=“](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Abfragen mit Bereichsoperatoren für Zeichenfolgen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Abfragen mit „ORDER BY“](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Abfragen mit „DISTINCT“](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Abfragen mit Aggregatfunktionen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Verwenden von Unterdokumenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Abfragen mit dokumentinternen Verknüpfungen (Joins)](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Abfragen mit Zeichenfolgen-, mathematischen und Arrayoperatoren](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Abfragen mit parametrisierter SQL per SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Abfragen mit expliziter Auslagerung](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Paralleles Abfragen partitionierter Sammlungen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Abfrage mit „ORDER BY“ für partitionierte Sammlungen | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Beispiel für einen Änderungsfeed 
Die Datei [Change Feed Processor Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Machen Sie sich anhand von [Lesen des Azure Cosmos DB-Änderungsfeeds](read-change-feed.md) und [Änderungsfeedprozessor in Azure Cosmos DB](change-feed-processor.md) mit Änderungsfeeds in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen.

| Aufgabe | API-Referenz |
| --- | --- |
| [Grundlegende Funktionen von Änderungsfeeds](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Lesen eines Änderungsfeeds von einem bestimmten Zeitpunkt | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Lesen eines Änderungsfeeds von Anfang an](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Serverseitige Programmierbeispiele

Die Datei [Stored Procedure Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Machen Sie sich anhand von [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md) mit der serverseitigen Programmierung in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Ausführen einer gespeicherten Prozedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.execute |
| [Löschen einer gespeicherten Prozedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Benutzerverwaltungsbeispiele
Die Datei „User Management Sample“ zeigt, wie die folgenden Aufgaben ausgeführt werden:

| Aufgabe | API-Referenz |
| --- | --- |
| Erstellen eines Benutzers | - |
| Festlegen von Berechtigungen für eine Sammlung oder ein Dokument | - |
| Abrufen einer Liste mit Berechtigungen eines Benutzers |- |
