---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 11ad3bdcaa40c479c9358fd623edf0e6fdafa0d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002073"
---
Mit der Event Hubs-Ausgabebindung werden Ereignisse in einen Ereignisdatenstrom geschrieben. Um Ereignisse in einen Event Hub schreiben zu können, müssen Sie über eine Sendeberechtigung verfügen.

Stellen Sie sicher, dass die erforderlichen Paketverweise vorhanden sind, bevor Sie versuchen, eine Ausgabebindung zu implementieren.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](../articles/azure-functions/functions-dotnet-class-library.md), die eine Nachricht in einen Event Hub schreibt. Dabei wird der Rückgabewert der Methode als Ausgabe verwendet:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Das folgende Beispiel zeigt, wie Sie die `IAsyncCollector`-Schnittstelle verwenden, um einen Nachrichtenbatch zu senden. Dieses Szenario kommt häufig vor, wenn Sie Nachrichten verarbeiten, die von einem Event Hub kommen, und das Ergebnis an einen anderen Event Hub senden.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](../articles/azure-functions/functions-reference-csharp.md), die die Bindung verwendet. Die Funktion schreibt eine Nachricht in einen Event Hub.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei. Das erste Beispiel gilt für Functions 2.x und höher und das zweite für Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Der folgende C#-Skriptcode erstellt eine Nachricht:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Der folgende C#-Skriptcode erstellt mehrere Nachrichten:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](../articles/azure-functions/functions-reference-node.md), die die Bindung verwendet. Die Funktion schreibt eine Nachricht in einen Event Hub.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei. Das erste Beispiel gilt für Functions 2.x und höher und das zweite für Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Der folgende JaveScript-Code sendet eine einzelne Nachricht:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Der folgende JaveScript-Code sendet mehrere Nachrichten:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei *function.json* sowie eine [Python-Funktion](../articles/azure-functions/functions-reference-python.md), die die Bindung verwendet. Die Funktion schreibt eine Nachricht in einen Event Hub.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Der folgende Python-Code sendet eine einzelne Nachricht:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt eine Java-Funktion, die eine Nachricht mit der aktuellen Uhrzeit in einen Event Hub schreibt.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Verwenden Sie die `@EventHubOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert in Event Hub veröffentlicht wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei „T“ für ein POJO oder einen beliebigen nativen Java-Typ steht.

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie für [C#-Klassenbibliotheken](../articles/azure-functions/functions-dotnet-class-library.md) das Attribut [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

Der Konstruktor des Attributs nimmt den Namen des Event Hubs und den Namen einer App-Einstellung an, die die Verbindungszeichenfolge enthält. Weitere Informationen zu diesen Einstellungen finden Sie unter [Ausgabe: Konfiguration](#configuration). Dieses Beispiel zeigt ein Attribut `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#example).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie die [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert in Event Hub veröffentlicht wird. Der Parametertyp sollte `OutputBinding<T>` lauten, wobei `T` für ein POJO oder einen beliebigen nativen Java-Typ steht.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `EventHub` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „eventHub“ festgelegt werden. |
|**direction** | – | Muss auf „out“ festgelegt werden. Dieser Parameter wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen. |
|**name** | – | Der Variablenname, der in Funktionscode verwendet wird, der das Ereignis darstellt. |
|**path** |**EventHubName** | Nur Functions 1.x. Der Name des Event Hubs. Wenn der Event Hub-Name auch in der Verbindungszeichenfolge enthalten ist, setzt dieser Wert diese Eigenschaft zur Laufzeit außer Kraft. |
|**eventHubName** |**EventHubName** | Functions 2.x und höher Der Name des Event Hubs. Wenn der Event Hub-Name auch in der Verbindungszeichenfolge enthalten ist, setzt dieser Wert diese Eigenschaft zur Laufzeit außer Kraft. |
|**connection** |**Connection** | Der Name einer App-Einstellung, die die Zeichenfolge für die Verbindung mit dem Namespace des Event Hubs enthält. Kopieren Sie diese Verbindungszeichenfolge, indem Sie für den *Namespace* (nicht für den eigentlichen Event Hub) auf die Schaltfläche **Verbindungsinformationen** klicken. Diese Verbindungszeichenfolge muss über Sendeberechtigungen zum Senden der Nachricht an den Ereignisstrom verfügen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Senden Sie Nachrichten mithilfe eines Methodenparameters wie `out string paramName`. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Um mehrere Nachrichten zu schreiben, können Sie `ICollector<string>` oder `IAsyncCollector<string>` anstelle von `out string` verwenden.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Senden Sie Nachrichten mithilfe eines Methodenparameters wie `out string paramName`. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Um mehrere Nachrichten zu schreiben, können Sie `ICollector<string>` oder `IAsyncCollector<string>` anstelle von `out string` verwenden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie mithilfe von `context.bindings.<name>` auf das Ausgabeereignis zu, wobei `<name>` der in der Eigenschaft `name` von *function.json* angegebene Wert ist.

# <a name="python"></a>[Python](#tab/python)

Es gibt zwei Optionen für das Ausgeben einer Event Hub-Nachricht aus einer Funktion:

- **Rückgabewert**: Legen Sie die Eigenschaft `name` in *function.json* auf `$return` fest. Mit dieser Konfiguration wird der Rückgabewert der Funktion als Event Hub-Nachricht beibehalten.

- **Imperativ**: Übergeben Sie einen Wert an die [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)-Methode des Parameters, der als [Out](/python/api/azure-functions/azure.functions.out?view=azure-python)-Typ deklariert ist. Der an `set` übergebene Wert wird als Event Hub-Nachricht beibehalten.

# <a name="java"></a>[Java](#tab/java)

Es gibt zwei Optionen für das Ausgeben einer Event Hub-Nachricht aus einer Funktion mittels der [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)-Anmerkung:

- **Rückgabewert**: Wenn Sie die Anmerkung auf die Funktion selbst anwenden, wird der Rückgabewert der Funktion als Event Hub-Nachricht beibehalten.

- **Imperativ**: Um den Nachrichtenwert explizit festzulegen, wenden Sie die Anmerkung auf einen bestimmten Parameter des Typs [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) an, wobei `T` ein POJO oder ein beliebiger nativer Java-Typ ist. Bei dieser Konfiguration wird bei Übergabe eines Werts an die `setValue`-Methode der Wert als Event Hub-Nachricht beibehalten.

---

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Event Hub | [Betriebsleitfaden](/rest/api/eventhub/publisher-policy-operations) |