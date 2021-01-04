---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0cd514c852e13b83a679821ca2d940e4ed112bd8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558925"
---
Der Funktionstrigger kann verwendet werden, um auf ein Ereignis zu reagieren, das an einen Event Hub-Datenstrom gesendet wird. Sie benötigen Lesezugriff auf den zugrunde liegenden Event Hub, um den Trigger einzurichten. Beim Auslösen der Funktion wird die an die Funktion übergebene Nachricht als Zeichenfolge eingegeben.

## <a name="scaling"></a>Skalierung

Jede Instanz einer durch ein Ereignis ausgelösten Funktion wird durch eine einzelne [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-Instanz gesichert. Der (auf Event Hubs basierende) Trigger stellt sicher, dass nur eine [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-Instanz eine Lease für eine bestimmte Partition erhalten kann.

Stellen Sie sich einen Event Hub wie folgt vor:

* 10 Partitionen
* 1\.000 gleichmäßig auf alle Partitionen verteilte Ereignisse mit 100 Nachrichten in jeder Partition

Wenn Ihre Funktion zuerst aktiviert wird, gibt es nur eine Instanz der Funktion. Wir nennen die erste Funktionsinstanz `Function_0`. Die `Function_0`-Funktion umfasst eine einzelne Instanz von [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) mit einer Lease auf allen zehn Partitionen. Diese Instanz beginnt mit dem Lesen von Ereignissen von den Partitionen 0-9. Von diesem Punkt an wird eines der folgenden Ereignisse eintreten:

* **Es sind keine neuen Funktionsinstanzen erforderlich:** `Function_0` kann alle 1.000 Ereignisse verarbeiten, bevor die Skalierungslogik von Functions einsetzt. In diesem Fall werden alle 1.000 Nachrichten von `Function_0` verarbeitet.

* **Eine weitere Funktionsinstanz wird hinzugefügt**: Wenn die Functions-Skalierungslogik bestimmt, dass `Function_0` mehr Nachrichten enthält, als verarbeitet werden können, wird eine neue Funktions-App-Instanz (`Function_1`) erstellt. Diese neue Funktion umfasst auch eine zugeordnete Instanz von [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Wenn die zugrunde liegende Event Hubs-Instanz erkennt, dass eine neue Hostinstanz versucht, Nachrichten zu lesen, wird ein Lastenausgleich der Partitionen in den Hostinstanzen vorgenommen. Zum Beispiel können die Partitionen 0-4 `Function_0` und die Partitionen 5-9 `Function_1` zugewiesen werden.

* **N weitere Funktionsinstanzen werden hinzufügt**: Wenn die Functions-Skalierungslogik bestimmt, dass sowohl `Function_0` als auch `Function_1` mehr Nachrichten aufweisen, als verarbeitet werden können, werden neue `Functions_N`-Funktions-App-Instanzen erstellt.  Apps werden erstellt, bis `N` größer ist als die Anzahl der Event Hub-Partitionen. In unserem Beispiel für Event Hubs erneut einen Lastenausgleich für die Partitionen aus, in diesem Fall für die Instanzen `Function_0`... `Functions_9`.

Wenn skaliert wird, entsprechen `N` Instanzen einer Zahl, die größer als die Anzahl der Event Hub-Partitionen ist. Dieses Muster wird verwendet, um sicherzustellen, dass [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-Instanzen verfügbar sind, um Partitionen zu sperren, sobald diese von anderen Instanzen bereitgestellt werden. Es werden Ihnen nur die Ressourcen berechnet, die bei der Ausführung der Funktionsinstanz in Anspruch genommen werden. Das heißt, die Kosten für diese übermäßige Bereitstellung werden nicht berechnet.

Wenn alle Funktionsausführungen abgeschlossen sind (mit oder ohne Fehler), werden Prüfpunkte dem zugehörigen Speicherkonto hinzugefügt. Wenn die Prüfpunkte erfolgreich erstellt wurden, werden alle 1.000 Nachrichten nie wieder abgerufen.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](../articles/azure-functions/functions-dotnet-class-library.md), die den Nachrichtentext des Event Hub-Triggers protokolliert.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Um Zugriff auf die [Ereignismetadaten](#event-metadata) im Funktionscode zu erhalten, muss eine Bindung an ein [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Objekt erfolgen (erfordert eine using-Anweisung für `Microsoft.Azure.EventHubs`). Sie können auch mithilfe von Bindungsausdrücken in der Methodensignatur auf die gleichen Eigenschaften zugreifen.  Im folgenden Beispiel werden beide Methoden zum Abrufen der gleichen Daten veranschaulicht:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Um Ereignisse in einem Batch zu empfangen, deklarieren Sie `string` oder `EventData` als Array.  

> [!NOTE]
> Beim Empfangen in einem Batch können Sie `DateTime enqueuedTimeUtc` nicht für Bindungen mit Methodenparametern wie im obigen Beispiel verwenden und müssen diese von jedem `EventData`-Objekt erhalten.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](../articles/azure-functions/functions-reference-csharp.md), die die Bindung verwendet. Die Funktion protokolliert den Nachrichtentext des Event Hub-Triggers.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei.

### <a name="version-2x-and-higher"></a>Version 2.x und höher

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Um Zugriff auf die [Ereignismetadaten](#event-metadata) im Funktionscode zu erhalten, muss eine Bindung an ein [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Objekt erfolgen (erfordert eine using-Anweisung für `Microsoft.Azure.EventHubs`). Sie können auch mithilfe von Bindungsausdrücken in der Methodensignatur auf die gleichen Eigenschaften zugreifen.  Im folgenden Beispiel werden beide Methoden zum Abrufen der gleichen Daten veranschaulicht:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Um Ereignisse in einem Batch zu empfangen, deklarieren Sie `string` oder `EventData` als ein Array:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](../articles/azure-functions/functions-reference-node.md), die die Bindung verwendet. Die Funktion liest [Ereignismetadaten](#event-metadata) und protokolliert die Meldung.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei.

### <a name="version-2x-and-higher"></a>Version 2.x und höher

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Legen Sie zum Empfangen von Ereignissen in einem Batch in der *function.json*-Datei `cardinality` auf `many` fest, wie im folgenden Beispiel gezeigt.

### <a name="version-2x-and-higher"></a>Version 2.x und höher

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung in einer Datei *function.json* sowie eine [Python-Funktion](../articles/azure-functions/functions-reference-python.md), die die Bindung verwendet. Die Funktion liest [Ereignismetadaten](#event-metadata) und protokolliert die Meldung.

Die folgenden Beispiele zeigen die Event Hubs-Bindungsdaten in der *function.json*-Datei.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt eine Event Hub-Triggerbindung, die den Nachrichtentext des Event Hub-Triggers protokolliert.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Verwenden Sie die `EventHubTrigger`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert von Event Hub empfangen wird. Parameter mit diesen Anmerkungen führen dazu, dass die Funktion ausgeführt wird, wenn ein Ereignis empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

 ---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](../articles/azure-functions/functions-dotnet-class-library.md) das Attribut [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Der Konstruktor des Attributs nimmt den Namen des Event Hubs, den Namen der Consumergruppe und den Namen einer App-Einstellung an, die die Verbindungszeichenfolge enthält. Weitere Informationen zu diesen Einstellungen finden Sie im Abschnitt [Triggerkonfiguration](#configuration). Dieses Beispiel zeigt ein Attribut `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Trigger: C#-Beispiel](#example).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie die [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger)-Anmerkung aus der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert von Event Hub empfangen wird. Parameter mit diesen Anmerkungen führen dazu, dass die Funktion ausgeführt wird, wenn ein Ereignis empfangen wird. Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `EventHubTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `eventHubTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die das Ereigniselement im Funktionscode darstellt. |
|**path** |**EventHubName** | Nur Functions 1.x. Der Name des Event Hubs. Wenn der Event Hub-Name auch in der Verbindungszeichenfolge enthalten ist, setzt dieser Wert diese Eigenschaft zur Laufzeit außer Kraft. |
|**eventHubName** |**EventHubName** | Functions 2.x und höher Der Name des Event Hubs. Wenn der Event Hub-Name auch in der Verbindungszeichenfolge enthalten ist, setzt dieser Wert diese Eigenschaft zur Laufzeit außer Kraft. Darauf kann über [App-Einstellungen](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) `%eventHubName%` verwiesen werden. |
|**consumerGroup** |**ConsumerGroup** | Eine optionale Eigenschaft, die zum Festlegen der [Consumergruppe](../articles/event-hubs/event-hubs-features.md#event-consumers) verwendet wird, mit der Ereignisse im Hub abonniert werden. Wird sie nicht angegeben, wird die Consumergruppe `$Default` verwendet. |
|**cardinality** | – | Wird für alle Sprachen außer C# verwendet. Legen Sie hierfür `many` fest, um Batchverarbeitung zu aktivieren.  Wenn diese Eigenschaft nicht angegeben oder auf `one` festgelegt ist, wird eine einzelne Nachricht an die Funktion übergeben.<br><br>In C# wird diese Eigenschaft automatisch zugewiesen, wenn der Auslöser ein Array für den Typ aufweist.|
|**connection** |**Connection** | Der Name einer App-Einstellung, die die Zeichenfolge für die Verbindung mit dem Namespace des Event Hubs enthält. Kopieren Sie diese Verbindungszeichenfolge, indem Sie für den [Namespace](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) (nicht für den eigentlichen Event Hub) auf die Schaltfläche **Verbindungsinformationen** klicken. Diese Verbindungszeichenfolge muss mindestens über Leseberechtigungen verfügen, um den Trigger zu aktivieren.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Ereignismetadaten

Der Event Hubs-Trigger stellt mehrere [Metadateneigenschaften](../articles/azure-functions/./functions-bindings-expressions-patterns.md) bereit. Metadateneigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Die Eigenschaften stammen aus der [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Klasse.

|Eigenschaft|type|BESCHREIBUNG|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Die `PartitionContext`-Instanz.|
|`EnqueuedTimeUtc`|`DateTime`|Die in die Warteschlange eingereihte Uhrzeit in UTC.|
|`Offset`|`string`|Der Offset der Daten relativ zum Event Hub-Partitionsdatenstrom. Der Offset ist ein Marker oder ein Bezeichner für ein Ereignis innerhalb des Event Hubs-Datenstroms. Der Bezeichner ist innerhalb einer Partition des Event Hubs-Datenstroms eindeutig.|
|`PartitionKey`|`string`|Die Partition, an die Ereignisdaten gesendet werden sollen.|
|`Properties`|`IDictionary<String,Object>`|Die Benutzereigenschaften der Ereignisdaten.|
|`SequenceNumber`|`Int64`|Die Protokollfolgenummer des Ereignisses.|
|`SystemProperties`|`IDictionary<String,Object>`|Die Systemeigenschaften, einschließlich der Ereignisdaten.|

[Codebeispiele](#example) mit diesen Eigenschaften finden Sie weiter oben in diesem Artikel.

## <a name="hostjson-properties"></a>Eigenschaften von „host.json“
<a name="host-json"></a>

Die Datei [host.json](../articles/azure-functions/functions-host-json.md#eventhub) enthält Einstellungen, die das Verhalten des Event Hubs-Triggers steuern. Die Konfiguration unterscheidet sich abhängig von der Azure Functions-Version.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]