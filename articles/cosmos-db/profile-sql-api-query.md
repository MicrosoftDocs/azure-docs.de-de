---
title: Abrufen von SQL-Abfrageleistungs- und -ausführungsmetriken
description: Erfahren Sie, wie Sie SQL-Abfrageausführungsmetriken abrufen und ein Profil der SQL-Abfrageleistung von Azure Cosmos DB-Anforderungen erstellen.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097395"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Abrufen von SQL-Abfrageausführungsmetriken und Analysieren der Abfrageleistung mit dem .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel wird veranschaulicht, wie Sie ein SQL-Abfrageleistungsprofil für Azure Cosmos DB erstellen. Die Profilerstellung kann mit dem aus dem .NET SDK abgerufenen Objekt `QueryMetrics` erfolgen. Dies wird hier näher erläutert. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) ist ein stark typisiertes Objekt mit Informationen über die Back-End-Abfrageausführung. Diese Metriken werden im Artikel [Optimieren der Abfrageleistung](./sql-api-query-metrics.md) ausführlicher dokumentiert.

## <a name="set-the-feedoptions-parameter"></a>Festlegen des FeedOptions-Parameters

Alle Überladungen für [DocumentClient.CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) verwenden einen optionalen [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions)-Parameter. Durch diese Option kann die Abfrageausführung optimiert und parametrisiert werden. 

Um die SQL-Abfrageausführungsmetriken erfassen zu können, müssen Sie den Parameter [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) in den [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) auf `true` festlegen. Wenn Sie `PopulateQueryMetrics` auf „true“ festlegen, enthält die `FeedResponse` die relevanten `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Abrufen von Abfragemetriken mit der AsDocumentQuery()-Methode
Im folgenden Codebeispiel wird das Abrufen von Metriken mithilfe der [AsDocumentQuery()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery)-Methode veranschaulicht:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Aggregieren von QueryMetrics

Beachten Sie, dass im vorherigen Abschnitt mehrere Aufrufe der [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync)-Methode ausgeführt wurden. Jeder Aufruf gab ein `FeedResponse`-Objekt mit einem Wörterbuch von `QueryMetrics` zurück – eins für jede Fortsetzung der Abfrage. Im folgenden Beispiel ist das Aggregieren dieser `QueryMetrics` mithilfe von LINQ dargestellt:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Gruppieren von Abfragemetriken nach Partitions-ID

Sie können die `QueryMetrics` nach Partitions-ID gruppieren. Durch das Gruppieren nach Partitions-ID können Sie feststellen, ob eine bestimmte Partition im Vergleich zu anderen Leistungsprobleme verursacht. Im folgenden Beispiel ist das Gruppieren von `QueryMetrics` mithilfe von LINQ dargestellt:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ in DocumentQuery

Sie können die `FeedResponse` aus einer LINQ-Abfrage auch mithilfe der `AsDocumentQuery()`-Methode abrufen:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Ressourcenintensive Abfragen

Sie können die von jeder Abfrage verbrauchten Anforderungseinheiten erfassen, um ressourcenintensive Abfragen oder Abfragen zu untersuchen, die einen hohen Durchsatz nutzen. Sie können die Anforderungsgebühr abrufen, indem Sie für das `FeedResponse`-Objekt die [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge)-Eigenschaft verwenden. Weitere Informationen zum Abrufen der Anforderungsgebühr mithilfe des Azure-Portals und verschiedener SDKs finden Sie im Artikel [Ermitteln der Gebühr für eine Anforderungseinheit](find-request-unit-charge.md).

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Abrufen der Abfrageausführungsdauer

Stellen Sie beim Berechnen der für die Ausführung einer clientseitigen Abfrage benötigten Zeit sicher, dass Sie nur die Zeit zum Aufrufen der `ExecuteNextAsync`-Methode und nicht andere Teile Ihrer Codebasis berücksichtigen. Diese Aufrufe helfen Ihnen bei der Berechnung der Abfrageausführungsdauer wie im folgenden Beispiel gezeigt:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Überprüfungsabfragen (häufig langsam und ressourcenintensiv)

Eine Überprüfungsabfrage bezieht sich auf eine Abfrage, die nicht vom Index verarbeitet wurde, wodurch viele Dokumente geladen werden, bevor das Resultset zurückgegeben wird.

Nachstehend sehen Sie ein Beispiel für eine Überprüfungsabfrage:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Der Filter dieser Abfrage verwendet die Systemfunktion UPPER, die nicht über den Index verarbeitet wird. Die Ausführung dieser Abfrage für eine große Sammlung ergab die folgenden Abfragemetriken für die erste Fortsetzung:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Beachten Sie die folgenden Werte der Ausgabe der Abfragemetriken:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Diese Abfrage hat 60.951 Dokumente geladen, die insgesamt 399.998.938 Bytes umfassten. Das Laden so vieler Bytes führt zu hohen Kosten oder Gebühren für Anforderungseinheiten. Außerdem nimmt die Ausführung der Abfrage viel Zeit in Anspruch, was anhand der Eigenschaft für die insgesamt benötigte Zeit („Total Query Execution Time“) deutlich wird:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Dies bedeutet, dass die Ausführung der Abfrage 4,5 Sekunden dauerte (und das war nur eine Fortsetzung).

Vermeiden Sie die Verwendung von UPPER im Filter, um diese Beispielabfrage zu optimieren. Wenn Dokumente erstellt oder aktualisiert werden, müssen stattdessen die Werte von `c.description` in Großbuchstaben eingefügt werden. Die Abfrage sieht dann wie folgt aus: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Diese Abfrage kann jetzt über den Index verarbeitet werden.

Weitere Informationen zum Optimieren der Abfrageleistung finden Sie im Artikel [Optimieren der Abfrageleistung](./sql-api-query-metrics.md).

## <a name="references"></a><a id="References"></a>Referenzen

- [Azure Cosmos DB-SQL-Spezifikation](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Nächste Schritte

- [Optimieren der Abfrageleistung](sql-api-query-metrics.md)
- [Übersicht über die Indizierung](index-overview.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)