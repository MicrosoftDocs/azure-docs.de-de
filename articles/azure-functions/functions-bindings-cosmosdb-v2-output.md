---
title: Azure Cosmos DB-Ausgabebindung für Functions 2.x und höher
description: Erfahren Sie, wie Sie die Azure Cosmos DB-Ausgabebindung in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 454ac9a377800bd11a53250569c3e7b65bac713a
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558792"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB-Ausgabebindung für Azure Functions 2.x und höher

Die Azure Cosmos DB-Ausgabebindung ermöglicht das Schreiben eines neuen Dokuments in eine Azure Cosmos DB-Datenbank mithilfe der SQL-API.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Dieser Abschnitt enthält folgende Beispiele:

* [Warteschlangentrigger: Schreiben eines einzelnen Dokuments](#queue-trigger-write-one-doc-c)
* [Warteschlangentrigger: Schreiben von Dokumenten unter Verwendung von IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Die Beispiele beziehen sich auf einen einfachen `ToDoItem`-Typ:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Warteschlangentrigger: Schreiben eines einzelnen Dokuments

Das folgende Beispiel zeigt eine [ C#-Funktion](functions-dotnet-class-library.md), die einer Datenbank ein Dokument hinzufügt und dazu die Daten aus der Meldung vom Warteschlangenspeicher verwendet.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Warteschlangentrigger: Schreiben von Dokumenten unter Verwendung von „IAsyncCollector“

Das folgende Beispiel zeigt eine [ C#-Funktion](functions-dotnet-class-library.md), die einer Datenbank eine Sammlung von Dokumenten hinzufügt und dabei Daten aus dem JSON-Code einer Warteschlangennachricht verwendet.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Dieser Abschnitt enthält folgende Beispiele:

* [Warteschlangentrigger: Schreiben eines einzelnen Dokuments](#queue-trigger-write-one-doc-c-script)
* [Warteschlangentrigger: Schreiben von Dokumenten unter Verwendung von IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Warteschlangentrigger: Schreiben eines einzelnen Dokuments

Das folgende Beispiel zeigt eine Azure Cosmos DB-Ausgabebindung in einer *function.json*-Datei sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion verwendet eine Warteschlangeneingabebindung für eine Warteschlange, die JSON-Code im folgenden Format empfängt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Die Funktion erstellt Azure Cosmos DB-Dokumente im folgenden Format für die einzelnen Datensätze:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Warteschlangentrigger: Schreiben von Dokumenten unter Verwendung von „IAsyncCollector“

Für das Erstellen mehrerer Dokumente können Sie eine Bindung mit `ICollector<T>` oder `IAsyncCollector<T>` erstellen, wobei `T` einer der unterstützten Typen ist.

Dieses Beispiel bezieht sich auf einen einfachen `ToDoItem`-Typ:

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

Die Datei „function.json“ sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Azure Cosmos DB-Ausgabebindung in einer *function.json*-Datei sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion verwendet eine Warteschlangeneingabebindung für eine Warteschlange, die JSON-Code im folgenden Format empfängt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Die Funktion erstellt Azure Cosmos DB-Dokumente im folgenden Format für die einzelnen Datensätze:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

Formen Sie für das Masseneinfügen zuerst die Objekte, und führen Sie dann die stringify-Funktion aus. Der JavaScript-Code sieht wie folgt aus:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Im folgenden Beispiel wird veranschaulicht, wie ein Dokument als Ausgabe einer Funktion in eine Azure CosmosDB-Datenbank geschrieben wird.

Die Bindungsdefinition ist in *function.json* definiert, wobei *type* auf `cosmosDB` festgelegt ist.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Um in die Datenbank zu schreiben, übergeben Sie ein Dokumentobjekt an die `set`-Methode des Parameters „database“.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Warteschlangentrigger: Speichern einer Nachricht in einer Datenbank über den Rückgabewert](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-Trigger: Speichern eines Dokuments in einer Datenbank über den Rückgabewert](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-Trigger: Speichern eines Dokuments in einer Datenbank über OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-Trigger: Speichern mehrerer Dokumente in einer Datenbank über OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Warteschlangentrigger: Speichern einer Nachricht in einer Datenbank über den Rückgabewert

Das folgende Beispiel zeigt eine Java-Funktion, die einer Datenbank ein Dokument hinzufügt und dazu die Daten aus der Meldung in Queue Storage verwendet.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-Trigger: Speichern eines Dokuments in einer Datenbank über den Rückgabewert

Das folgende Beispiel zeigt eine Java-Funktion, deren Signatur mit ```@CosmosDBOutput``` kommentiert ist und einen Rückgabewert des Typs ```String``` besitzt. Das von der Funktion zurückgegebene JSON-Dokument wird automatisch in die entsprechende COSMOS CosmosDB-Sammlung geschrieben.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-Trigger: Speichern eines Dokuments in einer Datenbank über OutputBinding

Das folgende Beispiel zeigt eine Java-Funktion, die ein Dokument über einen ```OutputBinding<T>```-Ausgabeparameter in CosmosDB schreibt. Beachten Sie, dass es in diesem Beispiel der Parameter ```outputItem``` ist, der mit ```@CosmosDBOutput``` kommentiert sein muss, nicht die Funktionssignatur. Durch die Verwendung von ```OutputBinding<T>``` kann Ihre Funktion die Bindung nutzen, um das Dokument in CosmosDB zu schreiben, und gleichzeitig einen anderen Wert an den Funktionsaufrufer zurückgeben, z. B. ein JSON- oder XML-Dokument.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-Trigger: Speichern mehrerer Dokumente in einer Datenbank über OutputBinding

Das folgende Beispiel zeigt eine Java-Funktion, die mehrere Dokumente über einen ```OutputBinding<T>```-Ausgabeparameter in CosmosDB schreibt. Beachten Sie, dass in diesem Beispiel der Parameter ```outputItem``` mit ```@CosmosDBOutput``` kommentiert sein muss, nicht die Funktionssignatur. Der Ausgabeparameter ```outputItem``` enthält eine Liste von ```ToDoItem```-Objekten als deren Vorlagenparametertyp. Durch die Verwendung von ```OutputBinding<T>``` kann Ihre Funktion die Bindung nutzen, um die Dokumente in CosmosDB zu schreiben, und gleichzeitig einen anderen Wert an den Funktionsaufrufer zurückgeben, z. B. ein JSON- oder XML-Dokument.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Verwenden Sie die `@CosmosDBOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, die in Cosmos DB geschrieben werden.  Der Parametertyp der Anmerkung muss ```OutputBinding<T>``` sein, wobei „T“ ein nativer Java-Typ oder ein POJO ist.

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests).

Der Attributkonstruktor akzeptiert den Datenbanknamen und den Sammlungsnamen. Weitere Informationen zu diesen Einstellungen und anderen Eigenschaften, die Sie konfigurieren können, finden Sie unter [Ausgabe: Konfiguration](#configuration). Hier ist ein Beispiel für ein `CosmosDB`-Attribut in einer Methodensignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Die `CosmosDBOutput`-Anmerkung dient dem Schreiben von Daten in Cosmos DB. Sie können die Anmerkung auf die Funktion oder einen einzelnen Funktionsparameter anwenden. Bei Verwendung in der Funktionsmethode wird der Rückgabewert der Funktion in Cosmos DB geschrieben. Wenn Sie die Anmerkung mit einem Parameter verwenden, muss der Typ des Parameters als `OutputBinding<T>` deklariert werden, wobei `T` ein nativer Java-Typ oder ein POJO ist.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `CosmosDB` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type**     | – | Muss auf `cosmosDB` festgelegt sein.        |
|**direction**     | – | Muss auf `out` festgelegt sein.         |
|**name**     | – | Der Name des Bindungsparameters, der das Dokument in der Funktion darstellt  |
|**databaseName** | **DatabaseName**|Die Datenbank mit der Sammlung, in der das neue Dokument erstellt wird     |
|**collectionName** |**CollectionName**  | Der Name der Sammlung, in der das neue Dokument erstellt wird |
|**createIfNotExists**  |**CreateIfNotExists**    | Ein boolescher Wert, der angibt, ob die Sammlung erstellt werden soll, wenn sie nicht vorhanden ist. Der Standardwert ist *FALSE*, da neue Sammlungen mit reserviertem Durchsatz erstellt werden. Dies wirkt sich auf die Kosten aus. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Wenn `CreateIfNotExists` den Wert TRUE hat, definiert dies den Partitionsschlüsselpfad für die erstellte Sammlung.|
|**collectionThroughput**|**CollectionThroughput**| Wenn `CreateIfNotExists` den Wert TRUE hat, definiert dies den [Durchsatz](../cosmos-db/set-throughput.md) für die erstellte Sammlung.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Der Name der App-Einstellung mit Ihrer Azure Cosmos DB-Verbindungszeichenfolge.        |
|**preferredLocations**| **PreferredLocations**| (Optional) Definiert bevorzugte Standorte (Regionen) für georeplizierte Datenbankkonten im Azure Cosmos DB-Dienst. Werte sollten durch Trennzeichen getrennt sein. Beispiel: „USA, Osten,USA, Süden-Mitte,Europa, Norden“. |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| (Optional) Wenn diese Option auf `true` festgelegt und zusammen mit `PreferredLocations` verwendet wird, können [Schreibvorgänge in mehreren Regionen](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) im Azure Cosmos DB-Dienst genutzt werden. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

Standardmäßig wird beim Schreiben in den Ausgabeparameter in Ihrer Funktion ein Dokument in der Datenbank erstellt. Dieses Dokument besitzt eine automatisch generierte GUID als Dokument-ID. Sie können die Dokument-ID des Ausgabedokuments angeben, indem Sie die `id`-Eigenschaft im JSON-Objekt angeben, das an den Ausgabeparameter übergeben wird.

> [!Note]
> Wenn Sie die ID eines vorhandenen Dokuments angeben, wird dieses vom neuen Ausgabedokument überschrieben.

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| CosmosDB | [CosmosDB-Fehlercodes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x beschrieben. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|GatewayMode|Gateway|Der von der Funktion zum Herstellen von Verbindungen mit dem Azure Cosmos DB-Dienst verwendete Verbindungsmodus. Die Optionen sind `Direct` und `Gateway`.|
|Protocol|HTTPS|Das von der Funktion zum Herstellen von Verbindungen mit dem Azure Cosmos DB-Dienst verwendete Verbindungsprotokoll.  Hier finden Sie [eine Erläuterung der beiden Modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|–|Lease-Präfix, das durchgängig für alle Funktionen in einer App verwendet wird.|

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn ein Azure Cosmos DB-Dokument erstellt oder geändert wird (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Lesen eines Azure Cosmos DB-Dokuments (Eingabebindung)](./functions-bindings-cosmosdb-v2-input.md)
