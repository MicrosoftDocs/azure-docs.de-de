---
title: Azure Service Bus-Bindungen für Azure Functions
description: Erfahren Sie, wie Azure Service Bus-Trigger und -Bindungen in Azure Functions verwendet werden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 199ce2fe24d76595493dc2128cebb3fcb642fcab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241150"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-Bindungen für Azure Functions

Dieser Artikel erläutert das Arbeiten mit Azure Service Bus-Bindungen in Azure Functions. Azure Functions unterstützt Trigger und Ausgabebindungen für Service Bus-Warteschlangen und -Themen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Service Bus-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), Version 2.x bereitgestellt. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die Service Bus-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus), Version 3.x, bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Verwenden Sie den Service Bus-Trigger, um auf Nachrichten von einer Service Bus-Warteschlange oder einem Thema zu reagieren. 

## <a name="trigger---example"></a>Trigger: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#trigger---c-example)
* [C#-Skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger: C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die [Nachrichtenmetadaten](#trigger---message-metadata) liest und eine Service Bus-Warteschlangennachricht protokolliert:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

Dieses Beispiel bezieht sich auf Azure Functions Version 1.x. Damit dieser Code für 2.x funktioniert:

- [lassen Sie den Parameter für die Zugriffsrechte aus](#trigger---configuration)
- ändern Sie den Typ des Protokollparameters von `TraceWriter` in `ILogger`
- ändern Sie `log.Info` in `log.LogInformation`

### <a name="trigger---c-script-example"></a>Trigger: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Service Bus-Triggerbindung in einer Datei vom Typ *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion liest [Nachrichtenmetadaten](#trigger---message-metadata) und protokolliert eine Service Bus-Warteschlangennachricht.

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Trigger: F#-Beispiel

Das folgende Beispiel zeigt eine Service Bus-Triggerbindung in einer Datei vom Typ *function.json* sowie eine [F#-Funktion](functions-reference-fsharp.md), die die Bindung verwendet. Die Funktion protokolliert eine Service Bus-Warteschlangennachricht. 

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Der F#-Skriptcode sieht wie folgt aus:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger: Java-Beispiel

Die folgende Java-Funktion verwendet die `@ServiceBusQueueTrigger`-Anmerkung aus der [Java-Funktions-Laufzeitbibliothek](/java/api/overview/azure/functions/runtime), um die Konfiguration für einen Service Bus-Warteschlangentrigger zu beschreiben. Die Funktion greift die Nachricht aus der Warteschlange ab und fügt sie den Protokollen hinzu.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java-Funktionen können auch dadurch ausgelöst werden, dass eine Nachricht einem Service Bus-Thema hinzugefügt wird. Im folgenden Beispiel wird die `@ServiceBusTopicTrigger`-Anmerkung verwendet, um die Triggerkonfiguration zu beschreiben.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Trigger: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Service Bus-Triggerbindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion liest [Nachrichtenmetadaten](#trigger---message-metadata) und protokolliert eine Service Bus-Warteschlangennachricht. 

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Der JavaScript-Skriptcode sieht wie folgt aus:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Service Bus-Trigger zu konfigurieren:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Der Konstruktor des Attributs nimmt den Namen der Warteschlange oder des Themas und des Abonnements an. In Version 1.x von Azure Functions können Sie auch die Zugriffsrechte der Verbindung angeben. Wenn Sie keine Zugriffsrechte angeben, ist der Standardwert `Manage`. Weitere Informationen finden Sie im Abschnitt [Trigger: Konfiguration](#trigger---configuration).

  Das folgende Beispiel zeigt das mit einem Zeichenfolgenparameter verwendete Attribut:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Service Bus-Konto angeben, wie das folgende Beispiel zeigt:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ein vollständiges Beispiel finden Sie unter [Trigger: C#-Beispiel](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Eine weitere Möglichkeit zum Angeben des zu verwendenden Service Bus-Kontos. Der Konstruktor nimmt den Namen einer App-Einstellung mit einer Service Bus-Verbindungszeichenfolge an. Das Attribut kann auf Parameter-, Methoden- oder Klassenebene angewendet werden. Das folgende Beispiel zeigt die Anwendung auf Klassen- und Methodenebene:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Das zu verwendende Service Bus-Konto wird in der folgendem Reihenfolge bestimmt:

* Die Eigenschaft `Connection` des Attributs `ServiceBusTrigger`.
* Das Attribut `ServiceBusAccount`, das auf den gleichen Parameter angewendet wird wie das Attribut `ServiceBusTrigger`.
* Das Attribut `ServiceBusAccount`, das auf die Funktion angewendet wird.
* Das Attribut `ServiceBusAccount`, das auf die Klasse angewendet wird.
* Die App-Einstellung „AzureWebJobsServiceBus“.

## <a name="trigger---configuration"></a>Trigger: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `ServiceBusTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „serviceBusTrigger“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf „in“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlangen- oder Themanachricht im Funktionscode darstellt. Legen Sie diesen Wert auf „$return“ fest, um auf den Rückgabewert der Funktion zu verweisen. |
|**queueName**|**QueueName**|Der Name der zu überwachenden Warteschlange.  Legen Sie diesen nur fest, wenn Sie eine Warteschlange überwachen (nicht für ein Thema).
|**topicName**|**TopicName**|Der Name des zu überwachenden Themas. Legen Sie diesen nur fest, wenn Sie ein Thema überwachen (nicht für eine Warteschlange).|
|**subscriptionName**|**SubscriptionName**|Der Name des zu überwachenden Abonnements. Legen Sie diesen nur fest, wenn Sie ein Thema überwachen (nicht für eine Warteschlange).|
|**Verbindung**|**Connection**|Der Name einer App-Einstellung, die die Service Bus-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyServiceBus“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyServiceBus“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Service Bus-Verbindungszeichenfolge aus der App-Einstellung „AzureWebJobsServiceBus“.<br><br>Um die Verbindungszeichenfolge zu erhalten, führen Sie die Schritte unter [Abrufen der Verwaltungsanmeldeinformationen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) aus. Die Verbindungszeichenfolge muss für einen Service Bus-Namespace gelten und darf nicht auf eine bestimmte Warteschlange oder ein Thema beschränkt sein. |
|**accessRights**|**zugreifen**|Zugriffsberechtigungen für die Verbindungszeichenfolge. Verfügbare Werte sind `manage` und `listen`. Die Standardeinstellung ist `manage`, d.h. heißt, dass die `connection` die Berechtigung **Manage** hat. Wenn Sie eine Verbindungszeichenfolge verwenden, die nicht über die Berechtigung **Manage** verfügt, legen Sie `accessRights` auf „listen“ fest. Andernfalls versucht die Functions-Runtime ggf. erfolglos Vorgänge auszuführen, die Verwaltungsrechte erfordern. In Version 2.x von Azure Functions ist diese Eigenschaft nicht verfügbar, da die aktuelle Version des Storage SDK Verwaltungsvorgänge nicht unterstützt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger: Verwendung

In C#- und C#-Skripts können Sie die folgenden Parametertypen für die Warteschlangen- oder Themanachricht verwenden:

* `string`: Wenn es sich bei der Nachricht um Text handelt.
* `byte[]`: Nützlich für Binärdaten.
* Ein benutzerdefinierter Typ: Wenn die Nachricht JSON enthält, versucht Azure Functions, die JSON-Daten zu deserialisieren.
* `BrokeredMessage`: Gibt die deserialisierte Nachricht mit der [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)-Methode zurück.

Diese Parameter gelten für Azure Functions Version 1.x. Verwenden Sie für 2.x [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) anstelle von `BrokeredMessage`.

Greifen Sie in JavaScript auf die Warteschlangen- oder Themanachricht mit `context.bindings.<name from function.json>` zu. Die Service Bus-Nachricht wird als Zeichenfolge oder als JSON-Objekt an die Funktion übergeben.

## <a name="trigger---poison-messages"></a>Trigger: Nicht verarbeitbare Nachrichten

Die Verarbeitung von nicht verarbeitbaren Nachricht kann nicht in Azure Functions gesteuert oder konfiguriert werden. Service Bus verarbeitet nicht verarbeitbare Nachrichten selbst.

## <a name="trigger---peeklock-behavior"></a>Trigger: PeekLock-Verhalten

Die Functions-Laufzeit empfängt eine Nachricht im [PeekLock Modus](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Sie ruft bei erfolgreicher Ausführung der Funktion `Complete` für die Nachricht auf. Ist die Ausführung nicht erfolgreich, wird `Abandon` aufgerufen. Wenn die Funktion länger als im `PeekLock`-Timeout angegeben ausgeführt wird, wird die Sperre automatisch verlängert, solange die Funktion ausgeführt wird. 

Die `maxAutoRenewDuration` kann in der Datei *host.json* konfiguriert werden, die [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet) zugeordnet ist. Der maximal zulässige Wert für diese Einstellung beträgt entsprechend der Service Bus-Dokumentation 5 Minuten, wohingegen Sie den Standardwert von 5 Minuten für das Functions-Zeitlimit auf 10 Minuten erhöhen können. Bei Service Bus-Funktionen sollten Sie dies nicht tun, da Sie den Service Bus-Verlängerungsgrenzwert übersteigen würden.

## <a name="trigger---message-metadata"></a>Trigger: Nachrichtenmetadaten

Der Service Bus-Trigger stellt mehrere [Metadateneigenschaften](./functions-bindings-expressions-patterns.md#trigger-metadata) bereit. Diese Eigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Dies sind Eigenschaften der [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)-Klasse.

|Eigenschaft|Typ|BESCHREIBUNG|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Die Anzahl der Übermittlungen.|
|`DeadLetterSource`|`string`|Die Quelle von unzustellbaren Nachrichten.|
|`ExpiresAtUtc`|`DateTime`|Die Ablaufzeit in UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Die in die Warteschlange eingereihte Uhrzeit in UTC.|
|`MessageId`|`string`|Benutzerdefinierter Wert, mit dem Service Bus doppelte Nachrichten ermitteln kann (sofern aktiviert).|
|`ContentType`|`string`|Ein Inhaltstypbezeichner, der vom Sender und Empfänger für anwendungsspezifische Logik verwendet wird.|
|`ReplyTo`|`string`|Die Antwort auf die Warteschlangenadresse.|
|`SequenceNumber`|`Int64`|Eindeutige Nummer, die vom Service Bus einer Nachricht zugewiesen wird.|
|`To`|`string`|Die Zieladresse.|
|`Label`|`string`|Die anwendungsspezifische Bezeichnung.|
|`CorrelationId`|`string`|Die Korrelations-ID.|

> [!NOTE]
> Zurzeit ist der Service Bus-Trigger, der mit sitzungsaktivierten Warteschlangen und Abonnements funktioniert, in der Vorschauphase. Verfolgen Sie [dieses Element](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458), um alle weiteren Updates zu dieser Funktion zu erhalten. 

[Codebeispiele](#trigger---example) mit diesen Eigenschaften finden Sie weiter oben in diesem Artikel.

## <a name="trigger---hostjson-properties"></a>Trigger: Eigenschaften von „host.json“

Die Datei [host.json](functions-host-json.md#servicebus) enthält Einstellungen, mit denen das Verhalten des Service Bus-Triggers gesteuert werden kann.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxConcurrentCalls|16|Die maximale Anzahl gleichzeitiger Aufrufe für den Rückruf, der vom Nachrichtensystem initiiert werden soll. Die Functions-Runtime verarbeitet standardmäßig mehrere Nachrichten gleichzeitig. Um die Runtime anzuweisen, jeweils nur eine Warteschlangen- oder Themennachricht zu verarbeiten, legen Sie `maxConcurrentCalls` auf „1“ fest. |
|prefetchCount|–|Das standardmäßige PrefetchCount, das von dem zugrunde liegenden MessageReceiver verwendet wird.|
|maxAutoRenewDuration|00:05:00|Die maximale Zeitspanne, in der die Nachrichtensperre automatisch erneuert wird.|

## <a name="output"></a>Output

Verwendet Azure Service Bus-Ausgabebindung zum Senden von Warteschlangen- oder Themanachrichten.

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Service Bus-Warteschlangennachricht sendet:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion verwendet einen Timertrigger, um alle 15 Sekunden eine Warteschlangennachricht zu senden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Der folgende C#-Skriptcode erstellt eine einzelne Nachricht:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Der folgende C#-Skriptcode erstellt mehrere Nachrichten:

```cs
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Ausgabe: F#-Beispiel

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [F#-Skriptfunktion](functions-reference-fsharp.md), die die Bindung verwendet. Die Funktion verwendet einen Timertrigger, um alle 15 Sekunden eine Warteschlangennachricht zu senden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Der folgende F#-Skriptcode erstellt eine einzelne Nachricht:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Ausgabe: Java-Beispiel

Das folgende Beispiel zeigt eine Java-Funktion, die eine Nachricht an die Service Bus-Warteschlange `myqueue` sendet, wenn sie durch eine HTTP-Anforderung ausgelöst wird.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Verwenden Sie die `@QueueOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Funktionsparameter, deren Wert in eine Service Bus-Warteschlange geschrieben wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei „T“ für einen beliebigen nativen Java-Typ eines POJO steht.

Java-Funktionen können auch in ein Service Bus-Thema schreiben. Im folgenden Beispiel wird die `@ServiceBusTopicOutput`-Anmerkung verwendet, um die Konfiguration für die Ausgabebindung zu beschreiben. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion verwendet einen Timertrigger, um alle 15 Sekunden eine Warteschlangennachricht zu senden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Der folgende JavaScript-Skriptcode erstellt eine einzelne Nachricht:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Der folgende JavaScript-Skriptcode mehrere Nachrichten:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Ausgabe: Attribute

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Der Konstruktor des Attributs nimmt den Namen der Warteschlange oder des Themas und des Abonnements an. Sie können auch die Zugriffsrechte der Verbindung angeben. Wie Sie die Einstellung für die Zugriffsrechte auswählen, wird im Abschnitt [Ausgabe: Konfiguration](#output---configuration) erläutert. Das folgende Beispiel zeigt, wie das Attribut auf den Rückgabewert der Funktion angewendet wird:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Service Bus-Konto angeben, wie das folgende Beispiel zeigt:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

Mit dem Attribut `ServiceBusAccount` können Sie das zu verwendende Service Bus-Konto auf Klassen-, Methoden- oder Parameterebene angeben.  Weitere Informationen finden Sie unter [Trigger: Attribute](#trigger---attributes).

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `ServiceBus` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „serviceBus“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf „out“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlange oder das Thema im Funktionscode darstellt. Legen Sie diesen Wert auf „$return“ fest, um auf den Rückgabewert der Funktion zu verweisen. |
|**queueName**|**QueueName**|Name der Warteschlange.  Legen Sie diesen nur fest, wenn Warteschlangennachrichten gesendet werden (nicht für ein Thema).
|**topicName**|**TopicName**|Der Name des zu überwachenden Themas. Legen Sie diesen nur fest, wenn Themanachrichten gesendet werden (nicht für eine Warteschlange).|
|**Verbindung**|**Connection**|Der Name einer App-Einstellung, die die Service Bus-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyServiceBus“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyServiceBus“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Service Bus-Verbindungszeichenfolge aus der App-Einstellung „AzureWebJobsServiceBus“.<br><br>Um die Verbindungszeichenfolge zu erhalten, führen Sie die Schritte unter [Abrufen der Verwaltungsanmeldeinformationen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) aus. Die Verbindungszeichenfolge muss für einen Service Bus-Namespace gelten und darf nicht auf eine bestimmte Warteschlange oder ein Thema beschränkt sein.|
|**accessRights**|**zugreifen**|Zugriffsberechtigungen für die Verbindungszeichenfolge. Verfügbare Werte sind `manage` und `listen`. Die Standardeinstellung ist `manage`, d.h. heißt, dass die `connection` die Berechtigung **Manage** hat. Wenn Sie eine Verbindungszeichenfolge verwenden, die nicht über die Berechtigung **Manage** verfügt, legen Sie `accessRights` auf „listen“ fest. Andernfalls versucht die Functions-Runtime ggf. erfolglos Vorgänge auszuführen, die Verwaltungsrechte erfordern. In Version 2.x von Azure Functions ist diese Eigenschaft nicht verfügbar, da die aktuelle Version des Storage SDK Verwaltungsvorgänge nicht unterstützt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Ausgabe: Verwendung

In Azure Functions 1.x erstellt die Runtime die Warteschlange, falls sie nicht vorhanden ist und Sie `accessRights` auf `manage` festgelegt haben. In Functions-Version 2.x muss die Warteschlange oder das Thema bereits vorhanden sein. Wenn Sie eine nicht vorhandene Warteschlange oder ein nicht vorhandenes Thema angeben, tritt bei der Funktion ein Fehler auf. 

In C#- und C#-Skripts können Sie die folgenden Parametertypen für die Ausgabebindung verwenden:

* `out T paramName` - `T` kann jeder JSON-serialisierbare Typ sein. Wenn der Parameterwert NULL ist, wenn die Funktion beendet wird, erstellt Functions die Nachricht mit einem NULL-Objekt.
* `out string`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out byte[]`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out BrokeredMessage`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `ICollector<T>` oder `IAsyncCollector<T>`: Zum Erstellen mehrerer Nachrichten. Beim Aufrufen der `Add` -Methode wird eine Nachricht erstellt.

In asynchronen Funktionen verwenden Sie den Rückgabewert oder `IAsyncCollector` anstelle eines `out`-Parameters.

Diese Parameter gelten für Azure Functions Version 1.x. Verwenden Sie für 2.x [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) anstelle von `BrokeredMessage`.

Greifen Sie in JavaScript auf die Warteschlange oder das Thema mit `context.bindings.<name from function.json>` zu. Sie können `context.binding.<name>` eine Zeichenfolge, ein Bytearray oder ein Javascript-Objekt (deserialisiert in JSON) zuweisen.

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Service Bus | [Service Bus-Fehlercodes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-Grenzwerte](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x beschrieben. Die nachfolgende Beispieldatei „host.json“ enthält nur die Einstellungen für Version 2.x für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

> [!NOTE]
> Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Die maximale Zeitspanne, in der die Nachrichtensperre automatisch erneuert wird.|
|autoComplete|true|Ob der Trigger sofort als vollständig markiert werden soll (Autovervollständigung) oder ob er darauf wartet, dass die Verarbeitung abgeschlossen wird.|
|maxConcurrentCalls|16|Die maximale Anzahl gleichzeitiger Aufrufe für den Rückruf, der vom Nachrichtensystem initiiert werden soll. Die Functions-Runtime verarbeitet standardmäßig mehrere Nachrichten gleichzeitig. Um die Runtime anzuweisen, jeweils nur eine Warteschlangen- oder Themennachricht zu verarbeiten, legen Sie `maxConcurrentCalls` auf „1“ fest. |
|prefetchCount|–|Das standardmäßige PrefetchCount, das von dem zugrunde liegenden MessageReceiver verwendet wird.|


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
