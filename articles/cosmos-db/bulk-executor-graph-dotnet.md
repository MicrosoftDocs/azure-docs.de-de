---
title: Verwenden der Bulk Executor-Graphbibliothek von .NET mit der Azure Cosmos DB-Gremlin-API
description: Hier erfahren Sie, wie die Bulk Executor-Bibliothek verwendet wird, um Massenimporte von Graphdaten in einen Gremlin-API-Container von Azure Cosmos DB durchzuführen.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: b31cb33e09158de5912132d0fb7bd31a62131181
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360512"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Verwenden der Bulk Executor-.NET-Graphbibliothek zum Ausführen von Massenvorgängen in der Gremlin-API von Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Dieses Tutorial bietet Anleitungen zum Verwenden der Bulk Executor-.NET-Bibliothek von Azure Cosmos DB zum Importieren und Aktualisieren von Graphobjekten in einen Gremlin-API-Container von Azure Cosmos DB. Bei diesem Prozess wird die Graphklasse in der [Bulk Executor-Bibliothek](./bulk-executor-overview.md) verwendet, um Scheitelpunkt- und Kantenobjekte programmgesteuert zu erstellen und anschließend mehrere davon pro Netzwerkanforderung einzufügen. Dieses Verhalten ist über die Bulk Executor-Bibliothek konfigurierbar, um sowohl Datenbankressourcen als auch lokale Speicherressourcen optimal zu nutzen.

Anders als beim Senden von Gremlin-Abfragen an eine Datenbank, wobei der Befehl nacheinander ausgewertet und dann ausgeführt wird, ist bei Verwendung der Bulk Executor-Bibliothek stattdessen das lokale Erstellen und Überprüfen der Objekte erforderlich. Nach dem Erstellen der Objekte ermöglicht Ihnen die Bibliothek, Graphobjekte nacheinander an den Datenbankdienst zu senden. Mit dieser Methode kann die Geschwindigkeit der Datenerfassung bis zu 100-fach erhöht werden. Dadurch ist sie eine ideale Methode für erste Datenmigrationen oder regelmäßige Datenverschiebungsvorgänge. Weitere Informationen finden Sie auf der GitHub-Seite zur [Bulk Executor-Beispielanwendung für Graphobjekte von Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Massenvorgänge mit Graphdaten

Die [Bulk Executor-Bibliothek](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet) enthält einen `Microsoft.Azure.CosmosDB.BulkExecutor.Graph`-Namespace, um Funktionalität für das Erstellen und Importieren von Graphobjekten bereitzustellen. 

Der folgende Prozess beschreibt, wie Datenmigration für einen Gremlin-API-Container verwendet werden kann:
1. Rufen Sie Datensätze aus der Datenquelle ab.
2. Erstellen Sie `GremlinVertex`- und `GremlinEdge`-Objekte aus den abgerufenen Datensätzen, und fügen Sie sie einer `IEnumerable`-Datenstruktur hinzu. In diesem Teil der Anwendung muss die Logik zum Erkennen und Hinzufügen von Beziehungen implementiert werden, falls die Datenquelle keine Graphdatenbank ist.
3. Verwenden Sie die [Graphmethode BulkImportAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?preserve-view=true&view=azure-dotnet) zum Einfügen der Graphobjekte in die Sammlung.

Dieser Mechanismus steigert die Effizienz der Datenmigration im Vergleich zur Verwendung eines Gremlin-Clients. Der Grund für diese Verbesserung ist, dass das Einfügen von Daten mit Gremlin erfordert, dass die Anwendung einzelne Abfragen sendet, die überprüft, ausgewertet und dann ausgeführt werden müssen, um die Daten zu erstellen. Die Bulk Executor-Bibliothek führt die Überprüfung in der Anwendung durch und sendet jeweils mehrere Graphobjekte für eine Netzwerkanforderung.

### <a name="creating-vertices-and-edges"></a>Erstellen von Scheitelpunkten und Kanten

`GraphBulkExecutor` stellt die `BulkImportAsync`-Methode bereit, die eine `IEnumerable`-Liste von `GremlinVertex`- oder `GremlinEdge`-Objekten erfordert, die beide im `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`-Namespace definiert sind. Im Beispiel haben wir die Kanten und Scheitelpunkte in zwei BulkExecutor-Importaufgaben getrennt. Betrachten Sie das folgende Beispiel:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Weitere Informationen zu den Parametern der Bulk Executor-Bibliothek finden Sie im Thema [Massenimport von Daten in Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

Die Nutzlast muss in `GremlinVertex`- und `GremlinEdge`-Objekten instanziiert werden. So können diese Objekte erstellt werden:

**Scheitelpunkte** :
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Kanten** :
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Das Bulk Executor-Dienstprogramm überprüft vor dem Hinzufügen von Kanten nicht automatisch, ob Scheitelpunkte vorhanden sind. Dies muss vor der Ausführung der BulkImport-Aufgaben in der Anwendung überprüft werden.

## <a name="sample-application"></a>Beispielanwendung

### <a name="prerequisites"></a>Voraussetzungen
* Visual Studio 2019 mit der Workload „Azure-Entwicklung“. Mit der [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) können Sie kostenlos beginnen.
* Ein Azure-Abonnement. Sie können [hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Alternativ können Sie mit [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) ein Cosmos-Datenbankkonto ohne Azure-Abonnement erstellen.
* Eine Gremlin-API-Datenbank von Azure Cosmos DB mit einer **unbegrenzten Sammlung**. Diese Anleitung zeigt die ersten Schritte mit [der Gremlin-API von Azure Cosmos DB in .NET](./create-graph-dotnet.md).
* Git. Weitere Informationen finden Sie auf der [Git-Downloadseite](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung
In diesem Tutorial begleiten wir Sie durch die ersten Schritte und verwenden dazu das auf GitHub gehostete [Bulk Executor-Beispiel für Graphobjekte von Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started). Diese Anwendung besteht aus einer .NET-Lösung, die nach dem Zufallsprinzip Scheitelpunkt- und Kantenobjekte generiert und dann Masseneinfügungen in das angegebene Graphdatenbankkonto durchführt. Führen Sie den folgenden `git clone`-Befehl aus, um die Anwendung abzurufen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Dieses Repository enthält das GraphBulkExecutor-Beispiel mit den folgenden Dateien:

Datei|BESCHREIBUNG
---|---
`App.config`|Hier werden die anwendungs- und datenbankspezifischen Parameter angegeben. Diese Datei muss zuerst geändert werden, um eine Verbindung mit der Zieldatenbank und Sammlungen herzustellen.
`Program.cs`| Diese Datei enthält die Logik hinter der Erstellung der `DocumentClient`-Sammlung. Sie führt die Bereinigungen durch und sendet die Bulk Executor-Anforderungen.
`Util.cs`| Diese Datei enthält eine Hilfsklasse mit der Logik hinter dem Generieren von Testdaten und dem Überprüfen, ob die Datenbank und Sammlungen vorhanden sind.

In der `App.config`-Datei können folgende Konfigurationswerte angegeben werden:

Einstellung|BESCHREIBUNG
---|---
`EndPointUrl`|Dies ist **Ihr .NET SDK-Endpunkt** , den Sie auf dem Blatt „Übersicht“ Ihres Gremlin-API-Datenbankkontos von Azure Cosmos DB finden. Er hat folgendes Format: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Dies ist der primäre oder sekundäre Schlüssel, der unter Ihrem Azure Cosmos DB-Konto aufgeführt ist. Weitere Informationen über das [Sichern des Zugriffs auf Azure Cosmos DB-Daten](./secure-access-to-data.md#primary-keys)
`DatabaseName`, `CollectionName`|Dies sind die **Zieldatenbank- und Sammlungsnamen**. Wenn `ShouldCleanupOnStart` auf `true` festgelegt ist, werden diese Werte zusammen mit `CollectionThroughput` verwendet, um sie zu löschen und eine neue Datenbank und Sammlung zu erstellen. Analog dazu gilt: Wenn `ShouldCleanupOnFinish` auf `true` festgelegt ist, werden sie zum Löschen der Datenbank verwendet, sobald die Erfassung abgeschlossen ist. Beachten Sie, dass die Zielsammlung **eine unbegrenzte Sammlung** sein muss.
`CollectionThroughput`|Hiermit wird eine neue Sammlung erstellt, wenn die `ShouldCleanupOnStart`-Option auf `true` festgelegt ist.
`ShouldCleanupOnStart`|Dadurch werden das Datenbankkonto und Sammlungen vor der Ausführung des Programms gelöscht und mit den Werten `DatabaseName`, `CollectionName` und `CollectionThroughput` neu erstellt.
`ShouldCleanupOnFinish`|Hiermit werden das Datenbankkonto und Sammlungen mit den angegebenen `DatabaseName`- und `CollectionName`-Werten nach der Ausführung des Programms gelöscht.
`NumberOfDocumentsToImport`|Hiermit wird die Anzahl der im Beispiel generierten Testscheitelpunkten und -kanten bestimmt. Diese Anzahl gilt sowohl für Scheitelpunkte als auch Kanten.
`NumberOfBatches`|Hiermit wird die Anzahl der im Beispiel generierten Testscheitelpunkten und -kanten bestimmt. Diese Anzahl gilt sowohl für Scheitelpunkte als auch Kanten.
`CollectionPartitionKey`|Diese Einstellung dient zum Erstellen der Testscheitelpunkte und -kanten, wobei diese Eigenschaft automatisch zugewiesen wird. Sie wird auch bei der erneuten Erstellung der Datenbank und Sammlungen verwendet, wenn die `ShouldCleanupOnStart`-Option auf `true` festgelegt ist.

### <a name="run-the-sample-application"></a>Ausführen der Beispielanwendung

1. Fügen Sie Ihre jeweiligen Datenbank-Konfigurationsparameter in `App.config` hinzu. Damit wird eine DocumentClient-Instanz erstellt. Wenn die Datenbank und der Container noch nicht erstellt wurden, werden sie automatisch erstellt.
2. Führen Sie die Anwendung aus. Dadurch wird `BulkImportAsync` zweimal aufgerufen, einmal zum Importieren von Scheitelpunkten und einmal zum Importieren von Kanten. Wenn eines der Objekte beim Einfügen einen Fehler erzeugt, werden sie entweder `.\BadVertices.txt` oder `.\BadEdges.txt` hinzugefügt.
3. Werten Sie die Ergebnisse durch Abfragen der Graphdatenbank aus. Wenn die `ShouldCleanupOnFinish`-Option auf „true“ festgelegt ist, wird die Datenbank automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu NuGet-Paketen und Versionshinweise zur Bulk Executor-.NET-Bibliothek finden Sie unter [.NET-Bulk Executor-Bibliothek: Informationen zum Download](sql-api-sdk-bulk-executor-dot-net.md). 
* Sehen Sie sich die [Leistungstipps](./bulk-executor-dot-net.md#performance-tips) an, um die Verwendung von Bulk Executor weiter zu optimieren.
* Lesen Sie den [Referenzartikel zu BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet), um mehr über die in diesem Namespace definierten Klassen und Methoden zu erfahren.