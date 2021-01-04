---
title: 'Azure Cosmos DB: .NET-Beispiele (Microsoft.Azure.Cosmos) für die SQL-API'
description: C# .NET V3 SDK-Beispiele für allgemeine Aufgaben mit der SQL-API von Azure Cosmos DB finden Sie auf GitHub.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 37ecca6829f348137bbbc8b76fb1934c024550fd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097259"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB: .NET V3-Beispiele (Microsoft.Azure.Cosmos) für die SQL-API
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

Das GitHub-Repository [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) enthält die neuesten .NET-Beispiellösungen zur Ausführung CRUD-basierter und anderer gängiger Vorgänge für Azure Cosmos DB-Ressourcen. Falls Sie mit der vorherigen Version des .NET SDK vertraut sind, kennen Sie unter Umständen bereits die Begriffe „Sammlung“ und „Dokument“. Azure Cosmos DB unterstützt mehrere API-Modelle. Daher werden in Version 3.0 des .NET SDK die generischen Begriffe „Container“ und „Element“ verwendet. Ein Container kann eine Sammlung, ein Graph oder eine Tabelle sein. Ein Element kann ein Dokument, ein Edge/Vertex oder eine Zeile sein und stellt den Inhalt eines Containers dar. Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen C#-Beispielprojektdateien.
* Links zum zugehörigen API-Referenzinhalt.

## <a name="prerequisites"></a>Voraussetzungen

Installation von Visual Studio 2019 mit dem Workflow für die Azure-Entwicklung

- Sie können die **kostenlose** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

   [NuGet-Paket „Microsoft.Azure.cosmos“](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Azure-Abonnement oder kostenloses Cosmos DB-Testkonto

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für kostenpflichtige Azure-Dienste verwenden können.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Die Beispiele sind eigenständig, werden automatisch eingerichtet und zum Schluss automatisch bereinigt. Für jedes Vorkommen wird Ihrem Abonnement in der Leistungsstufe des Containers eine Nutzungsdauer von einer Stunde in Rechnung gestellt.
> 

## <a name="database-examples"></a>Datenbankbeispiele

Die Methode [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) des Beispielprojekts *DatabaseManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md), um sich über Azure Cosmos-Datenbanken zu informieren.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Lesen einer Datenbank nach ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet&preserve-view=true) |
| [Lesen aller Datenbanken für ein Konto](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet&preserve-view=true) |
| [Löschen einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet&preserve-view=true) |

## <a name="container-examples"></a>Containerbeispiele

Die Methode [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) des Beispielprojekts *ContainerManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele den Artikel [Arbeiten mit Datenbanken, Containern und Elementen in Azure Cosmos DB](account-databases-containers-items.md), um sich über Azure Cosmos-Container zu informieren.

| Aufgabe | API-Referenz |
| --- | --- |
| [Container erstellen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Erstellen eines Containers mit benutzerdefinierter Indexrichtlinie](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Ändern der konfigurierten Leistung eines Containers](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) |
| [Abrufen eines Containers nach ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container.ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?preserve-view=true&view=azure-dotnet) |
| [Lesen aller Container in einer Datenbank](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Löschen eines Containers](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet&preserve-view=true) |

## <a name="item-examples"></a>Elementbeispiele

Die Methode [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) des Beispielprojekts *ItemManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Elemente den Artikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md), um sich über Azure Cosmos-Dokumente zu informieren.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Elements](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true) |
| [Lesen eines Elements nach ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true) |
| [Abfragen von Elementen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Ersetzen eines Elements](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet&preserve-view=true) |
| [Aktualisieren/Einfügen (Upsert) eines Elements](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet&preserve-view=true) |
| [Löschen eines Elements](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet&preserve-view=true) |
| [Ersetzen eines Elements mit bedingter ETag-Überprüfung](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet&preserve-view=true) |

## <a name="indexing-examples"></a>Indizierungsbeispiele

Die Methode [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) des Beispielprojekts *IndexManagement* veranschaulicht die Ausführung folgender Aufgaben. Lesen Sie vor dem Ausführen der folgenden Beispiele die Artikel [Indizierungsrichtlinien](index-policy.md), [Indizierungstypen](index-overview.md#index-kinds) und [Indizierungspfade](index-policy.md#include-exclude-paths), um sich über die Indizierung in Azure Cosmos DB zu informieren. 

| Aufgabe | API-Referenz |
| --- | --- |
| [Ausschließen eines Elements vom Index](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet&preserve-view=true) |
| [Verwenden der verzögerten Indizierung](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet&preserve-view=true) |
| [Ausschließend bestimmter Elementpfade aus dem Index](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet&preserve-view=true) |

## <a name="query-examples"></a>Beispiele für Abfragen

Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) des Beispielprojekts *Queries* veranschaulicht die Ausführung der folgenden Aufgaben mithilfe der SQL-Abfragegrammatik, des LINQ-Anbieters mit Abfrage und Lambda. Machen Sie sich im Artikel [SQL-Abfragebeispiele](./sql-query-getting-started.md) mit der SQL-Abfragereferenz in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen.

| Aufgabe | API-Referenz |
| --- | --- |
| [Abfragen von Elementen aus einer einzelnen Partition](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Abfragen von Elementen aus mehreren Partitionen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Abfragen mithilfe einer SQL-Anweisung](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |

## <a name="change-feed-examples"></a>Beispiel für einen Änderungsfeed

Die Methode [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) des Beispielprojekts *ChangeFeed* veranschaulicht die Ausführung folgender Aufgaben. Machen Sie sich anhand von [Lesen des Azure Cosmos DB-Änderungsfeeds](read-change-feed.md) und [Änderungsfeedprozessor in Azure Cosmos DB](change-feed-processor.md) mit Änderungsfeeds in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen.

| Aufgabe | API-Referenz |
| --- | --- |
| [Grundlegende Funktionen von Änderungsfeeds](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Lesen eines Änderungsfeeds von einem bestimmten Zeitpunkt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Lesen eines Änderungsfeeds von Anfang an](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet&preserve-view=true) |
| [Migrieren vom Änderungsfeedprozessor zum Änderungsfeed im V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |

## <a name="server-side-programming-examples"></a>Serverseitige Programmierbeispiele

Die Methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) des Beispielprojekts *ServerSideScripts* veranschaulicht die Ausführung folgender Aufgaben. Machen Sie sich anhand von [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md) mit der serverseitigen Programmierung in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Ausführen einer gespeicherten Prozedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Löschen einer gespeicherten Prozedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
