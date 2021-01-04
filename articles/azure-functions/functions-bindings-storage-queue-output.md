---
title: Azure Queue Storage-Ausgabebindung für Azure Functions
description: Erfahren Sie, wie Sie in Azure Functions Azure Queue Storage-Nachrichten erstellen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 087073437fe9d6159422799c04ce095c0aae5eca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001251"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Queue Storage-Ausgabebindungen für Azure Functions

Azure Functions kann neue Azure Queue Storage-Nachrichten durch Einrichten einer Ausgabebindung erstellen.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-storage-queue.md).

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Die [C#-Funktion](functions-dotnet-class-library.md) des folgenden Beispiels erstellt eine Warteschlangennachricht für jede empfangene HTTP-Anforderung.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel enthält eine HTTP-Triggerbindung in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem die Bindung verwendet wird. Die Funktion erstellt ein Warteschlangenelement mit einer **CustomQueueMessage**-Objektnutzlast für jede empfangene HTTP-Anforderung.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der folgende C#-Skriptcode erstellt eine einzelne Warteschlangennachricht:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Mithilfe eines Parameters vom Typ `ICollector` oder `IAsyncCollector` können mehrere Nachrichten gleichzeitig gesendet werden. Der folgende C#-Skriptcode sendet mehrere Nachrichten – eine mit den HTTP-Anforderungsdaten und eine mit hartcodierten Werten:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 Die Java-Funktion des folgenden Beispiels erstellt eine Warteschlangennachricht bei Auslösung durch eine HTTP-Anforderung.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Verwenden Sie die `@QueueOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert in Queue Storage geschrieben wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei `T` für einen beliebigen nativen Java-Typ eines POJO steht.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel enthält eine HTTP-Triggerbindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion erstellt ein Warteschlangenelement für jede empfangene HTTP-Anforderung.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Wenn Sie mehrere Nachrichten gleichzeitig senden möchten, können Sie ein Nachrichtenarray für die Ausgabebindung `myQueueItem` definieren. Der folgende JavaScript-Code sendet zwei Warteschlangennachrichten mit hartcodierten Werten für jede empfangene HTTP-Anforderung.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Die folgenden Codebeispiele veranschaulichen, wie eine Warteschlangennachricht aus einer durch HTTP ausgelösten Funktion ausgegeben wird. Der Konfigurationsabschnitt mit dem `type` von `queue` definiert die Ausgabebindung.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Mithilfe dieser Bindungskonfiguration kann eine PowerShell-Funktion mithilfe von `Push-OutputBinding` eine Warteschlangennachricht erstellen. In diesem Beispiel wird eine Nachricht aus einer Abfragezeichenfolge oder einem Textparameter erstellt.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Wenn mehrere Nachrichten gleichzeitig gesendet werden sollen, definieren Sie ein Nachrichtenarray und verwenden `Push-OutputBinding`, um Nachrichten an die Warteschlangenausgabebindung zu senden.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Im folgenden Beispiel wird veranschaulicht, wie Sie einzelne und mehrere Werte an Speicherwarteschlangen ausgeben. Die für *function.json* erforderliche Konfiguration ist in beiden Fällen identisch.

Eine Speicherwarteschlangenbindung ist in *function.json* definiert, wobei *type* auf `queue` festgelegt ist.

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Um eine einzelne Nachricht in die Warteschlange zu stellen, übergeben Sie einen einzelnen Wert an die `set`-Methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Um mehrere Nachrichten in der Warteschlange zu erstellen, deklarieren Sie einen Parameter als geeigneten Listentyp und übergeben ein Array von Werten (die dem Listentyp entsprechen) an die `set`-Methode.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Das Attribut gilt für einen Parameter vom Typ `out` oder für den Rückgabewert der Funktion. Der Konstruktor des Attributs akzeptiert den Namen der Warteschlange, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabebeispiel](#example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter „Trigger: Attribute“.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Die `QueueOutput`-Anmerkung ermöglicht Ihnen, eine Nachricht als Ausgabe einer Funktion zu schreiben. Das folgende Beispiel zeigt eine HTTP-ausgelöste Funktion, die eine Warteschlangennachricht erstellt.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Eigenschaft    | BESCHREIBUNG |
|-------------|-----------------------------|
|`name`       | Deklariert den Parameternamen in der Funktionssignatur. Wenn die Funktion ausgelöst wird, enthält der Wert dieses Parameters den Inhalt der Warteschlangennachricht. |
|`queueName`  | Deklariert den Warteschlangennamen im Speicherkonto. |
|`connection` | Verweist auf die Speicherkonto-Verbindungszeichenfolge. |

Der Parameter, der der Anmerkung `QueueOutput` zugeordnet ist, ist als [OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)-Instanz typisiert.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Queue` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `queue` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `out` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlange im Funktionscode darstellt. Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.|
|**queueName** |**QueueName** | Der Name der Warteschlange. |
|**connection** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Runtime nach einer App-Einstellung namens „MyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Schreiben einer einzelnen Warteschlangennachricht mittels eines Methodenparameters wie `out T paramName`. Anstelle eines Parameters vom Typ `out` können Sie den Rückgabetyp der Methode verwenden, und `T` kann einer der folgenden Typen sein:

* Ein Objekt, das als JSON serialisierbar ist
* `string`
* `byte[]`
* [CloudQueueMessage] 

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

Verwenden Sie einen der folgenden Typen, um in C# und C#-Skripts mehrere Warteschlangennachrichten zu schreiben: 

* `ICollector<T>` oder `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Schreiben einer einzelnen Warteschlangennachricht mittels eines Methodenparameters wie `out T paramName`. `paramName` ist der Wert, der in der Eigenschaft `name` von *function.json* angegeben wird. Anstelle eines Parameters vom Typ `out` können Sie den Rückgabetyp der Methode verwenden, und `T` kann einer der folgenden Typen sein:

* Ein Objekt, das als JSON serialisierbar ist
* `string`
* `byte[]`
* [CloudQueueMessage] 

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

Verwenden Sie einen der folgenden Typen, um in C# und C#-Skripts mehrere Warteschlangennachrichten zu schreiben: 

* `ICollector<T>` oder `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="java"></a>[Java](#tab/java)

Es gibt zwei Optionen für das Ausgeben einer Warteschlangennachricht aus einer Funktion mittels der [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput)-Anmerkung:

- **Rückgabewert**: Wenn Sie die Anmerkung auf die Funktion selbst anwenden, wird der Rückgabewert der Funktion als Warteschlangennachricht beibehalten.

- **Imperativ**: Um den Nachrichtenwert explizit festzulegen, wenden Sie die Anmerkung auf einen bestimmten Parameter des Typs [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) an, wobei `T` ein POJO oder ein beliebiger nativer Java-Typ ist. Bei dieser Konfiguration wird bei Übergabe eines Werts an die `setValue`-Methode der Wert als Warteschlangennachricht beibehalten.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das Ausgabewarteschlangen-Element ist über `context.bindings.<NAME>` verfügbar, wobei `<NAME>` dem in *function.json* definierten Namen entspricht. Für die Nutzlast des Warteschlangenelements kann eine Zeichenfolge oder ein JSON-serialisierbares Objekt verwendet werden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Die Ausgabe an die Warteschlangennachricht ist über `Push-OutputBinding` verfügbar. Dort übergeben Sie Argumente, die dem Namen entsprechen, der durch den `name`-Parameter der Bindung in der Datei *function.json* festgelegt wird.

# <a name="python"></a>[Python](#tab/python)

Es gibt zwei Optionen für das Ausgeben einer Warteschlangennachricht aus einer Funktion:

- **Rückgabewert**: Legen Sie die Eigenschaft `name` in *function.json* auf `$return` fest. Mit dieser Konfiguration wird der Rückgabewert der Funktion als Queue Storage-Nachricht beibehalten.

- **Imperativ**: Übergeben Sie einen Wert an die [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none)-Methode des Parameters, der als [Out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true)-Typ deklariert ist. Der an `set` übergebene Wert wird als Queue Storage-Nachricht beibehalten.

---

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung |  Verweis |
|---|---|
| Warteschlange | [Warteschlangen-Fehlercodes](/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabelle, Warteschlange | [Speicherfehlercodes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange |  [Problembehandlung](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x und höheren Versionen beschrieben. Die nachfolgende Beispieldatei „host.json“ enthält nur die Einstellungen für Version 2.x und höhere Versionen für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x und höheren Versionen finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

> [!NOTE]
> Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Das maximale Intervall zwischen Warteschlangenabfragen. Der Mindestwert lautet 00:00:00.100 (100 ms), er wird bis auf 00:01:00 (1 Min.) erhöht.  In 1.x ist der Datentyp Millisekunden, und in 2.x und höheren Versionen ist es ein TimeSpan-Wert.|
|visibilityTimeout|00:00:00|Das Zeitintervall zwischen Wiederholungsversuchen, wenn bei der Verarbeitung einer Nachricht ein Fehler auftritt. |
|batchSize|16|Die Anzahl der Warteschlangennachrichten, die die Functions-Runtime gleichzeitig abruft und parallel verarbeitet. Wenn die zu verarbeitende Anzahl `newBatchThreshold` erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten `batchSize` plus `newBatchThreshold`. Dieser Grenzwert gilt separat für jede Funktion, die durch die Warteschlange ausgelöst wird. <br><br>Wenn Sie eine parallele Ausführung für in einer Warteschlange empfangene Nachrichten vermeiden möchten, können Sie `batchSize` auf „1“ festlegen. Diese Einstellung verhindert Parallelität jedoch nur so lange, wie Ihre Funktions-App auf einem einzelnen virtuellen Computer (VM) ausgeführt wird. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, kann jeder virtuelle Computer eine Instanz jeder durch die Warteschlange ausgelösten Funktion ausführen.<br><br>Die maximale `batchSize` beträgt 32. |
|maxDequeueCount|5|Die Anzahl der Versuche zum Verarbeiten einer Nachricht, bevor diese in die Warteschlange für nicht verarbeitete Nachrichten verschoben wird.|
|newBatchThreshold|batchSize/2|Wenn die Anzahl der gleichzeitig verarbeiteten Nachrichten auf diesen Wert sinkt, ruft die Runtime einen weiteren Batch ab.|

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn sich Queue Storage-Daten ändern (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
