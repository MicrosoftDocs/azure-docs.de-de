---
title: 'Azure Cosmos DB: .NET-Beispiele für die SQL-API'
description: C# .NET-Beispiele für allgemeine Aufgaben mit der SQL-API von Azure Cosmos DB (einschließlich CRUD-Vorgängen) finden Sie auf GitHub.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 7f520d6f0f9774fcd945d8d2453644e8e2db7f6f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359573"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: .NET-Beispiele für die SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-Beispiele](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-Beispiele](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK-Beispiele](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-Beispiele](sql-api-spring-data-sdk-samples.md)
> * [Node.js-Beispiele](sql-api-nodejs-samples.md)
> * [Python-Beispiele](sql-api-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Das GitHub-Repository [azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) enthält die neuesten .NET-Beispiellösungen zur Ausführung CRUD-basierter und anderer gängiger Vorgänge für Azure Cosmos DB-Ressourcen. Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen C#-Beispielprojektdateien. 
* Links zum zugehörigen API-Referenzinhalt.

Codebeispiele für die Version 3.0 des .NET SDK (Vorschauversion) finden Sie in den aktuellen Beispielen im GitHub-Repository [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3). 

## <a name="prerequisites"></a>Voraussetzungen

Installation von Visual Studio 2019 mit dem Workflow für die Azure-Entwicklung
- Sie können die **kostenlose** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**. 

[NuGet-Paket „Microsoft.Azure.DocumentDB“](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Azure-Abonnement oder kostenloses Cosmos DB-Testkonto
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für kostenpflichtige Azure-Dienste verwenden können.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Die Beispiele sind eigenständig und werden durch mehrere Aufrufe von [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync) automatisch eingerichtet und bereinigt. Für jedes Vorkommen wird Ihrem Abonnement eine Nutzungsdauer von einer Stunde in Rechnung gestellt. 
> 

## <a name="database-examples"></a>Datenbankbeispiele
Die Methode [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) des Beispielprojekts *DatabaseManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md), um sich über Azure Cosmos-Datenbanken zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Lesen einer Datenbank nach ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Lesen aller Datenbanken](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Löschen einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Sammlungsbeispiele
Die Methode [RunCollectionDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) des Beispielprojekts *CollectionManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md), um sich über Azure Cosmos-Sammlungen zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient.CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Ändern der konfigurierten Leistung einer Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Abrufen einer Auflistung nach ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Lesen aller Sammlungen in einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Löschen einer Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Dokumentbeispiele
Die Methode [RunDocumentsDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) des Beispielprojekts *DocumentManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md), um sich über Azure Cosmos-Dokumente zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Dokuments](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Lesen eines Dokuments nach ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Lesen aller Dokumente einer Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Abfragen von Dokumenten](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Ersetzen eines Dokuments](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Aktualisieren und Einfügen (Upsert) eines Dokuments](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Löschen eines Dokuments](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Verwenden von dynamischen .NET-Objekten](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Ersetzen eines Dokuments mit bedingter ETag-Überprüfung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Lesen eines Dokuments, nur wenn das Dokument geändert wurde](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Indizierungsbeispiele
Die Methode [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) des Beispielprojekts *IndexManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele die Artikel [Indizierungsrichtlinien](index-policy.md), [Indizierungstypen](index-overview.md#index-kinds) und [Indizierungspfade](index-policy.md#include-exclude-paths), um sich über die Indizierung in Azure Cosmos DB zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Ausschließen eines Dokuments aus dem Index](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Verwenden der verzögerten Indizierung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Ausschließen von angegebenen Dokumentpfaden aus dem Index](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Erzwingen eines Bereichsscanvorgangs auf einem Pfad mit Hashindizierung](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Verwenden von Bereichsindizes in Zeichenfolgen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Durchführen einer Indextransformation](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Geodatenbeispiele
Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L94-L139) des Beispielprojekts *Geospatial* veranschaulicht die Ausführung folgender Aufgaben.  Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Gewusst wie: Verwenden von Geodaten und GeoJSON-Standortdaten](./sql-query-geospatial-intro.md), um sich über GeoJSON und Geodaten zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Aktivieren der Geoindizierung für eine neue Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Einfügen von Dokumenten mit GeoJSON-Punkten](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Suchen nach Punkten innerhalb eines angegebenen Abstands](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Suchen nach Punkten innerhalb eines Polygons](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polygon](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Aktivieren der Geoindizierung in einer vorhandenen Sammlung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Überprüfen von Punkt- und Polygondaten](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Beispiele für Abfragen
Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L64-L129) des Beispielprojekts *Queries* veranschaulicht die Ausführung der folgenden Aufgaben mithilfe der SQL-Abfragegrammatik, des LINQ-Anbieters mit Abfrage und Lambda. Machen Sie sich im Artikel [SQL-Abfragebeispiele](./sql-query-getting-started.md) mit der SQL-Abfragereferenz in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Abfragen aller Dokumente](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen der Gleichheit mit „==“](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen der Ungleichheit mit „!=“ und „NOT“](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfrage mit Bereichsoperatoren wie „&gt;“, „&lt;“, „&gt;=“, „&lt;=“](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit Bereichsoperatoren für Zeichenfolgen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit „ORDER BY“](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit Aggregatfunktionen](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Verwenden von Unterdokumenten](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit dokumentinternen Verknüpfungen (Joins)](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit Zeichenfolgen-, mathematischen und Arrayoperatoren](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfragen mit parametrisierter SQL per SqlQuerySpec](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Abfragen mit expliziter Auslagerung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Paralleles Abfragen partitionierter Sammlungen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Abfrage mit „ORDER BY“ für partitionierte Sammlungen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Beispiel für einen Änderungsfeed 
Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) des Beispielprojekts *ChangeFeed* veranschaulicht die Ausführung folgender Aufgaben. Machen Sie sich anhand von [Lesen des Azure Cosmos DB-Änderungsfeeds](read-change-feed.md) und [Änderungsfeedprozessor in Azure Cosmos DB](change-feed-processor.md) mit Änderungsfeeds in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Lesen des Änderungsfeeds](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Lesen der Partitionsschlüsselbereiche](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

Das Beispiel für den Änderungsfeedprozessor ([ChangeFeedMigrationTool](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/ChangeFeedMigrationTool)) veranschaulicht, wie Sie die Prozessorbibliothek für Änderungsfeeds zum Replizieren von Daten in einem anderen Cosmos-Container verwenden.   

## <a name="server-side-programming-examples"></a>Serverseitige Programmierbeispiele
Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) des Beispielprojekts *ServerSideScripts* veranschaulicht die Ausführung folgender Aufgaben. Machen Sie sich anhand von [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md) mit der serverseitigen Programmierung in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Ausführen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Lesen eines Dokumentfeeds für eine gespeicherte Prozedur](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Erstellen einer gespeicherten Prozedur mit „ORDER BY“](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Erstellen eines vorangestellten Triggers](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Erstellen eines nachgestellten Triggers](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Erstellen einer benutzerdefinierten Funktion (User-Defined Function, UDF)](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Benutzerverwaltungsbeispiele
Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/UserManagement/Program.cs#L55-L129) des Beispielprojekts *UserManagement* veranschaulicht die Ausführung folgender Aufgaben:

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Benutzers](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Festlegen von Berechtigungen für eine Sammlung oder ein Dokument](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Abrufen einer Liste mit Berechtigungen eines Benutzers](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |
