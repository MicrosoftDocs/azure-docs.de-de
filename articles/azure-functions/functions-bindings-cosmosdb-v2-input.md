---
title: Azure Cosmos DB-Eingabebindung für Functions 2.x und höher
description: Erfahren Sie, wie Sie die Azure Cosmos DB-Eingabebindung in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: dec41a5e05d22891aae9d16280ebb6b0c8da3f20
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185112"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB-Eingabebindung für Azure Functions 2.x und höher

Die Azure Cosmos DB-Eingabebindung verwendet die SQL-API, um mindestens ein Azure Cosmos DB-Dokument abzurufen und an den Eingabeparameter der Funktion zu übergeben. Die Dokument-ID oder die Abfrageparameter können basierend auf dem Trigger, der die Funktion aufruft, ermittelt werden.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Wenn die Sammlung [partitioniert](../cosmos-db/partitioning-overview.md#logical-partitions) ist, müssen Suchvorgänge auch den Partitionsschlüsselwert angeben.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Dieser Abschnitt enthält folgende Beispiele:

* [Warteschlangentrigger: Suchen der ID in JSON-Code](#queue-trigger-look-up-id-from-json-c)
* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-Trigger: Suchen der ID in Routendaten](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-Trigger: Suchen der ID in Routendaten unter Verwendung von „SqlQuery“](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von „SqlQuery“](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von „DocumentClient“](#http-trigger-get-multiple-docs-using-documentclient-c)

Die Beispiele beziehen sich auf einen einfachen `ToDoItem`-Typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Warteschlangentrigger: Suchen der ID in JSON-Code 

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine Warteschlangennachricht ausgelöst, die ein JSON-Objekt enthält. Der Warteschlangentrigger zerlegt den JSON-Code in ein Objekt vom Typ `ToDoItemLookup`, das die ID und den Partitionsschlüsselwert enthält, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

>[!NOTE]
>Beim Wert für den HTTP-Abfragezeichenfolge-Parameter muss die Groß-/Kleinschreibung beachtet werden.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-Trigger: Suchen der ID in Routendaten

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die Routendaten verwendet, um die ID und den Schlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-Trigger: Suchen der ID in Routendaten unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Routendaten verwendet, um die zu suchende ID anzugeben. Anhand dieser ID wird ein Dokument vom Typ `ToDoItem` aus der angegebenen Datenbank und Sammlung abgerufen.

Das Beispiel veranschaulicht die Verwendung eines Bindungsausdrucks im Parameter `SqlQuery`. Sie können Routendaten wie gezeigt an den Parameter `SqlQuery` übergeben. [Die Übergabe von Abfragezeichenfolgenwerten ist derzeit allerdings nicht möglich.](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)

> [!NOTE]
> Wenn Sie nur über die ID eine Abfrage ausführen, sollten Sie wie in den [vorherigen Beispielen](#http-trigger-look-up-id-from-query-string-c) einen Suchvorgang verwenden, da dadurch weniger [Anforderungseinheiten](../cosmos-db/request-units.md) verbraucht werden. Punktlesevorgänge (GET) sind [effizienter](../cosmos-db/optimize-cost-reads-writes.md) als Abfragen über die ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Liste von Dokumenten abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst. Die Abfrage wird in der Attributeigenschaft `SqlQuery` angegeben.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von DocumentClient

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Liste von Dokumenten abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst. Der Code verwendet eine von der Azure Cosmos DB-Bindung bereitgestellte `DocumentClient`-Instanz, um eine Liste von Dokumenten zu lesen. Die `DocumentClient`-Instanz kann auch für Schreibvorgänge verwendet werden.

> [!NOTE]
> Sie können auch die [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet)-Schnittstelle verwenden, um das Testen zu vereinfachen.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Dieser Abschnitt enthält folgende Beispiele:

* [Warteschlangentrigger: Suchen der ID in einer Zeichenfolge](#queue-trigger-look-up-id-from-string-c-script)
* [Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von „SqlQuery“](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-Trigger: Suchen der ID in Routendaten](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von „SqlQuery“](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von „DocumentClient“](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Die Beispiele für einen HTTP-Trigger beziehen sich auf einen einfachen `ToDoItem`-Typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Warteschlangentrigger: Suchen der ID in einer Zeichenfolge

Das folgende Beispiel zeigt eine Cosmos DB-Eingabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion liest ein einzelnes Dokument und aktualisiert den Textwert des Dokuments.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine Azure Cosmos DB-Eingabebindung in einer *function.json*-Datei sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion ruft mehrere von einer SQL-Abfrage angegebene Dokumente mithilfe eines Warteschlangentriggers ab, um die Abfrageparameter anzupassen.

Der Warteschlangentrigger stellt den Parameter `departmentId` bereit. Die Warteschlangennachricht `{ "departmentId" : "Finance" }` gibt dann alle Datensätze für die Finanzabteilung zurück.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge

Das folgende Beispiel zeigt eine [C#-Skriptfunktion](functions-reference-csharp.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-Trigger: Suchen der ID in Routendaten

Das folgende Beispiel zeigt eine [C#-Skriptfunktion](functions-reference-csharp.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die Routendaten verwendet, um die ID und den Schlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine [C#-Skriptfunktion](functions-reference-csharp.md), die eine Liste von Dokumenten abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst. Die Abfrage wird in der Attributeigenschaft `SqlQuery` angegeben.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-Trigger: Abrufen mehrerer Dokumente unter Verwendung von DocumentClient

Das folgende Beispiel zeigt eine [C#-Skriptfunktion](functions-reference-csharp.md), die eine Liste von Dokumenten abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst. Der Code verwendet eine von der Azure Cosmos DB-Bindung bereitgestellte `DocumentClient`-Instanz, um eine Liste von Dokumenten zu lesen. Die `DocumentClient`-Instanz kann auch für Schreibvorgänge verwendet werden.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dieser Abschnitt enthält die folgenden Beispiele, die ein einzelnes Dokument lesen, indem ein ID-Wert aus verschiedenen Quellen angegeben wird:

* [Warteschlangentrigger: Suchen der ID in JSON-Code](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-Trigger: Suchen der ID in Routendaten](#http-trigger-look-up-id-from-route-data-javascript)
* [Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von „SqlQuery“](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Warteschlangentrigger: Suchen der ID in JSON-Code

Das folgende Beispiel zeigt eine Cosmos DB-Eingabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion liest ein einzelnes Dokument und aktualisiert den Textwert des Dokuments.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge

Das folgende Beispiel zeigt eine [JavaScript-Skriptfunktion](functions-reference-node.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-Trigger: Suchen der ID in Routendaten

Das folgende Beispiel zeigt eine [JavaScript-Skriptfunktion](functions-reference-node.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die Routendaten verwendet, um die ID und den Schlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine Azure Cosmos DB-Eingabebindung in einer *function.json*-Datei sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion ruft mehrere von einer SQL-Abfrage angegebene Dokumente mithilfe eines Warteschlangentriggers ab, um die Abfrageparameter anzupassen.

Der Warteschlangentrigger stellt den Parameter `departmentId` bereit. Die Warteschlangennachricht `{ "departmentId" : "Finance" }` gibt dann alle Datensätze für die Finanzabteilung zurück.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Dieser Abschnitt enthält die folgenden Beispiele, die ein einzelnes Dokument lesen, indem ein ID-Wert aus verschiedenen Quellen angegeben wird:

* [Warteschlangentrigger: Suchen der ID in JSON-Code](#queue-trigger-look-up-id-from-json-python)
* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-Trigger: Suchen der ID in Routendaten](#http-trigger-look-up-id-from-route-data-python)
* [Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von „SqlQuery“](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Warteschlangentrigger: Suchen der ID in JSON-Code

Das folgende Beispiel zeigt eine Cosmos DB-Eingabebindung in einer Datei namens *function.json* sowie eine [Python-Funktion](functions-reference-python.md), die die Bindung verwendet. Die Funktion liest ein einzelnes Dokument und aktualisiert den Textwert des Dokuments.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Dies ist der Python-Code:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge

Das folgende Beispiel zeigt eine [Python-Funktion](functions-reference-python.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-Trigger: Suchen der ID in Routendaten

Das folgende Beispiel zeigt eine [Python-Funktion](functions-reference-python.md), die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die Routendaten verwendet, um die ID und den Schlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein `ToDoItem`-Dokument aus der angegebenen Datenbank und Sammlung abgerufen.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Warteschlangentrigger: Abrufen mehrerer Dokumente unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine Azure Cosmos DB-Eingabebindung in einer Datei namens *function.json* sowie eine [Python-Funktion](functions-reference-python.md), die die Bindung verwendet. Die Funktion ruft mehrere von einer SQL-Abfrage angegebene Dokumente mithilfe eines Warteschlangentriggers ab, um die Abfrageparameter anzupassen.

Der Warteschlangentrigger stellt den Parameter `departmentId` bereit. Die Warteschlangennachricht `{ "departmentId" : "Finance" }` gibt dann alle Datensätze für die Finanzabteilung zurück.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Dies ist der Python-Code:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge – Zeichenfolgenparameter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge – POJO-Parameter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-Trigger: Suchen der ID in Routendaten](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-Trigger: Suchen der ID in Routendaten unter Verwendung von „SqlQuery“](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-Trigger: Abrufen mehrerer Dokumente aus Routendaten unter Verwendung von SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Die Beispiele beziehen sich auf einen einfachen `ToDoItem`-Typ:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge – Zeichenfolgenparameter

Das folgende Beispiel zeigt eine Java-Funktion, die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein Dokument im Zeichenfolgenformat aus der angegebenen Datenbank und Sammlung abgerufen.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Verwenden Sie die `@CosmosDBInput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Funktionsparameter, deren Wert von Cosmos DB empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge – POJO-Parameter

Das folgende Beispiel zeigt eine Java-Funktion, die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID oder den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein Dokument aus der angegebenen Datenbank und Sammlung abgerufen. Das Dokument wird dann in eine Instanz des zuvor erstellten ```ToDoItem``` POJO konvertiert und als Argument an die Funktion übergeben.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-Trigger: Suchen der ID in Routendaten

Das folgende Beispiel zeigt eine Java-Funktion, die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die einen Routenparameter verwendet, um die ID und den Partitionsschlüsselwert anzugeben, der gesucht werden soll. Anhand dieser ID und dieses Partitionsschlüsselwerts wird ein Dokument aus der angegebenen Datenbank und Sammlung abgerufen und als ```Optional<String>``` zurückgegeben.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-Trigger: Suchen der ID in Routendaten unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine Java-Funktion, die ein einzelnes Dokument abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die einen Routenparameter verwendet, um die zu suchende ID anzugeben. Diese ID wird verwendet, um ein Dokument aus der angegebenen Datenbank und Sammlung abzurufen, wobei das Resultset in ein ```ToDoItem[]``` konvertiert wird, da viele Dokumente zurückgegeben werden können, je nach den Abfragekriterien.

> [!NOTE]
> Wenn Sie nur über die ID eine Abfrage ausführen, sollten Sie wie in den [vorherigen Beispielen](#http-trigger-look-up-id-from-query-string---pojo-parameter-java) einen Suchvorgang verwenden, da dadurch weniger [Anforderungseinheiten](../cosmos-db/request-units.md) verbraucht werden. Punktlesevorgänge (GET) sind [effizienter](../cosmos-db/optimize-cost-reads-writes.md) als Abfragen über die ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-Trigger: Abrufen mehrerer Dokumente aus Routendaten unter Verwendung von SqlQuery

Das folgende Beispiel zeigt eine Java-Funktion, die mehrere Dokumente abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die den Routenparameter ```desc``` verwendet, um die in dem Feld ```description``` zu suchende Zeichenfolge anzugeben. Der Suchbegriff wird verwendet, um ein Sammlung von Dokumenten aus der angegebenen Datenbank und Sammlung abzurufen, wobei das Resultset in ein ```ToDoItem[]``` konvertiert und dieses als Argument an die Funktion übergeben wird.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Der Attributkonstruktor akzeptiert den Datenbanknamen und den Sammlungsnamen. Weitere Informationen zu diesen Einstellungen und anderen Eigenschaften, die Sie konfigurieren können, finden Sie im [folgenden Konfigurationsabschnitt](#configuration).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie die `@CosmosDBOutput`-Anmerkung in der [Runtimebibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, die in Cosmos DB schreiben. Der Parametertyp der Anmerkung sollte `OutputBinding<T>` sein, wobei `T` ein nativer Java-Typ oder ein POJO ist.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `CosmosDB` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type**     | – | Muss auf `cosmosDB` festgelegt sein.        |
|**direction**     | – | Muss auf `in` festgelegt sein.         |
|**name**     | – | Der Name des Bindungsparameters, der das Dokument in der Funktion darstellt  |
|**databaseName** |**DatabaseName** |Die Datenbank mit dem Dokument        |
|**collectionName** |**CollectionName** | Der Name der Sammlung mit dem Dokument |
|**id**    | **Id** | Die ID des abzurufenden Dokuments. Diese Eigenschaft unterstützt [Bindungsausdrücke](./functions-bindings-expressions-patterns.md). Legen Sie nicht die beiden Eigenschaften `id` und **sqlQuery** fest. Wenn Sie keine der beiden festlegen, wird die gesamte Sammlung abgerufen. |
|**sqlQuery**  |**SqlQuery**  | Eine SQL-Abfrage in Azure Cosmos DB zum Abrufen mehrerer Dokumente. Die Eigenschaft unterstützt Laufzeitbindungen, wie in diesem Beispiel: `SELECT * FROM c where c.departmentId = {departmentId}`. Legen Sie nicht die beiden Eigenschaften `id` und `sqlQuery` fest. Wenn Sie keine der beiden festlegen, wird die gesamte Sammlung abgerufen.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Der Name der App-Einstellung mit Ihrer Azure Cosmos DB-Verbindungszeichenfolge. |
|**partitionKey**|**PartitionKey**|Gibt den Wert des Partitionsschlüssels für die Suche an. Kann den Bindungsparameter enthalten. Für Suchvorgänge in [partitionierten](../cosmos-db/partitioning-overview.md#logical-partitions) Sammlungen ist dies erforderlich.|
|**preferredLocations**| **PreferredLocations**| (Optional) Definiert bevorzugte Standorte (Regionen) für georeplizierte Datenbankkonten im Azure Cosmos DB-Dienst. Werte sollten durch Trennzeichen getrennt sein. Beispiel: „USA, Osten,USA, Süden-Mitte,Europa, Norden“. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Wenn die Funktion erfolgreich beendet wird, werden alle Änderungen am Eingabedokument mithilfe benannter Eingabeparameter automatisch beibehalten.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Wenn die Funktion erfolgreich beendet wird, werden alle Änderungen am Eingabedokument mithilfe benannter Eingabeparameter automatisch beibehalten.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aktualisierungen erfolgen beim Beenden der Funktion nicht automatisch. Verwenden Sie stattdessen `context.bindings.<documentName>In` und `context.bindings.<documentName>Out`, um Aktualisierungen vorzunehmen. Siehe das JavaScript-Beispiel.

# <a name="python"></a>[Python](#tab/python)

Daten werden der Funktion über einen `DocumentList`-Parameter zur Verfügung gestellt. Änderungen am Dokument werden nicht automatisch dauerhaft gespeichert.

# <a name="java"></a>[Java](#tab/java)

Die [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput)-Anmerkung der [Runtimebibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) macht Cosmos DB-Daten für die Funktion verfügbar. Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

---

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn ein Azure Cosmos DB-Dokument erstellt oder geändert wird (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Speichern der Änderungen an einem Azure Cosmos DB-Dokument (Ausgabebindung)](./functions-bindings-cosmosdb-v2-output.md)