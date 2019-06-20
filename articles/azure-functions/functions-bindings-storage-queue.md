---
title: Azure Queue Storage-Bindungen für Azure Functions
description: Hier erfahren Sie, wie Sie den Azure Queue Storage-Trigger und die entsprechende Ausgabebindung in Azure Functions verwenden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 72460136f5fa0dcfec78716fc02e0aaf9e860840
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472300"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue Storage-Bindungen für Azure Functions

In diesem Artikel erfahren Sie, wie Sie Azure Queue Storage-Bindungen in Azure Functions verwenden. Azure Functions unterstützt Trigger- und Ausgabebindungen für Warteschlangen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Warteschlangenspeicher-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), Version 2.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die Warteschlangenspeicher-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), Version 3.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Codieren
Funktionen erwarten eine *Base64*-codierte Zeichenfolge. Alle Anpassungen am Codierungstyp (um Daten als *Base64*-codierte Zeichenfolge vorzubereiten) müssen im aufrufenden Dienst implementiert werden.

## <a name="trigger"></a>Trigger

Verwenden Sie den Warteschlangentrigger, um eine Funktion zu starten, wenn bei einer Warteschlange ein neues Element eingeht. Die Warteschlangennachricht wird als Eingabe für die Funktion bereitgestellt.

## <a name="trigger---example"></a>Trigger: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#trigger---c-example)
* [C#-Skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Trigger: C#-Beispiel

Die [C#-Funktion](functions-dotnet-class-library.md) des folgenden Beispiels fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein neues Warteschlangenelement verarbeitet wird.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Trigger: C#-Skriptbeispiel

Das folgende Beispiel enthält eine Warteschlangentrigger-Bindung in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem die Bindung verwendet wird. Die Funktion fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein Warteschlangenelement verarbeitet wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#trigger---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

Im Abschnitt [Verwendung](#trigger---usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#trigger---message-metadata) erläutert.

### <a name="trigger---javascript-example"></a>Trigger: JavaScript-Beispiel

Das folgende Beispiel enthält eine Warteschlangentrigger-Bindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein Warteschlangenelement verarbeitet wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#trigger---configuration).

> [!NOTE]
> Der Namensparameter wird als `context.bindings.<name>` im JavaScript-Code wiedergegeben, der die Nutzlast des Warteschlangenelements enthält. Diese Nutzlast wird der Funktion außerdem als zweiter Parameter übergeben.

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

Im Abschnitt [Verwendung](#trigger---usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#trigger---message-metadata) erläutert.

### <a name="trigger---java-example"></a>Trigger: Java-Beispiel

Das folgende Java-Beispiel zeigt eine Funktion für einen Storage-Warteschlangentrigger, mit der die ausgelöste Nachricht, die in Warteschlange `myqueuename` abgelegt ist, protokolliert wird.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

## <a name="trigger---attributes"></a>Trigger: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Warteschlangentrigger zu konfigurieren:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Der Konstruktor des Attributs akzeptiert den Namen der zu überwachenden Warteschlange, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Ein vollständiges Beispiel finden Sie unter [Trigger: C#-Beispiel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Eine weitere Möglichkeit zum Angeben des zu verwendenden Speicherkontos. Der Konstruktor akzeptiert den Namen einer App-Einstellung mit einer Speicherverbindungszeichenfolge. Das Attribut kann auf Parameter-, Methoden- oder Klassenebene angewendet werden. Das folgende Beispiel zeigt die Anwendung auf Klassen- und Methodenebene:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Das zu verwendende Speicherkonto wird anhand von Folgendem bestimmt (in der angegebenen Reihenfolge):

* Die Eigenschaft `Connection` des Attributs `QueueTrigger`.
* Das Attribut `StorageAccount`, das auf den gleichen Parameter angewendet wird wie das Attribut `QueueTrigger`.
* Das Attribut `StorageAccount`, das auf die Funktion angewendet wird.
* Das Attribut `StorageAccount`, das auf die Klasse angewendet wird.
* Die App-Einstellung „AzureWebJobsStorage“.

## <a name="trigger---configuration"></a>Trigger: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `QueueTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | –| Muss auf `queueTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction**| – | Nur in der Datei *function.json*. Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – |Der Name der Variablen, die die Nutzlast des Warteschlangenelements im Funktionscode enthält.  |
|**queueName** | **QueueName**| Der Name der abzufragenden Warteschlange. |
|**Verbindung** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger: Verwendung

Verwenden Sie in C# und C#-Skripts einen Methodenparameter wie `string paramName`, um auf die Nachrichtendaten zuzugreifen. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Eine Bindung kann mit folgenden Typen erstellt werden:

* Objekt – Die Functions-Runtime deserialisiert eine JSON-Nutzlast in eine Instanz einer beliebigen Klasse, die in Ihrem Code definiert ist. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

Verwenden Sie in JavaScript `context.bindings.<name>`, um auf die Nutzlast des Warteschlangenelements zuzugreifen. Falls es sich um eine JSON-Nutzlast handelt, wird sie in ein Objekt deserialisiert.

## <a name="trigger---message-metadata"></a>Trigger: Nachrichtenmetadaten

Der Warteschlangentrigger stellt mehrere [Metadateneigenschaften](./functions-bindings-expressions-patterns.md#trigger-metadata) bereit. Diese Eigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Im Folgenden werden Eigenschaften der [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)-Klasse aufgeführt:

|Eigenschaft|Typ|BESCHREIBUNG|
|--------|----|-----------|
|`QueueTrigger`|`string`|Die Warteschlangennutzlast (bei einer gültigen Zeichenfolge). Handelt es sich bei der Warteschlangennutzlast um eine Zeichenfolge, hat `QueueTrigger` den gleichen Wert wie die Variable, die durch die Eigenschaft `name` in *function.json* benannt wird.|
|`DequeueCount`|`int`|Gibt an, wie oft diese Nachricht aus der Warteschlange entfernt wurde.|
|`ExpirationTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht abläuft.|
|`Id`|`string`|ID der Warteschlangennachricht|
|`InsertionTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht der Warteschlange hinzugefügt wurde.|
|`NextVisibleTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht als Nächstes sichtbar wird.|
|`PopReceipt`|`string`|Die POP-Bestätigung der Nachricht.|

## <a name="trigger---poison-messages"></a>Trigger: Nicht verarbeitbare Nachrichten

Falls eine Funktion des Warteschlangentriggers nicht erfolgreich ausgeführt werden kann, versucht Azure Functions für eine bestimmte Warteschlangennachricht bis zu fünf Mal (einschließlich des ersten Versuchs), die Funktion auszuführen. Sind alle fünf Versuche nicht erfolgreich, fügt die Functions-Laufzeit einer Warteschlange namens *&lt;Name der Originalwarteschlange>-poison* eine Nachricht hinzu. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist.

Überprüfen Sie zur manuellen Behandlung nicht verarbeitbarer Nachrichten den Wert [dequeueCount](#trigger---message-metadata) der Warteschlangennachricht.

## <a name="trigger---polling-algorithm"></a>Trigger: Abrufalgorithmus

Der Warteschlangentrigger implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet die Runtime zwei Sekunden und prüft dann, ob eine andere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet sie ungefähr vier Sekunden vor dem erneuten Versuch. Nach aufeinander folgenden fehlgeschlagenen Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit, standardmäßig eine Minute, erreicht ist. Die maximale Wartezeit kann über die `maxPollingInterval`-Eigenschaft in der Datei [host.json](functions-host-json.md#queues) konfiguriert werden.

## <a name="trigger---concurrency"></a>Trigger: Parallelität

Wenn mehrere Warteschlangennachrichten warten, ruft der Warteschlangentrigger einen Batch mit Nachrichten ab und ruft parallel Funktionsinstanzen zur Verarbeitung auf. Standardmäßig ist die Batchgröße 16. Wenn die zu verarbeitende Anzahl 8 erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion auf einem virtuellen Computer verarbeiteten parallelen Nachrichten 24. Dieser Grenzwert gilt separat für jede durch Warteschlangen ausgelöste Funktion auf jedem virtuellen Computer. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, wartet jeder virtuelle Computer auf Trigger und versucht, Funktionen auszuführen. Beispiel: Wenn eine Funktions-App auf drei virtuelle Computer horizontal hochskaliert wird, beträgt die standardmäßige maximale Anzahl von parallelen Instanzen einer durch Warteschlangen ausgelösten Funktion 72.

Die Batchgröße und der Schwellenwert für das Abrufen eines neuen Batches können in der Datei [host.json](functions-host-json.md#queues) konfiguriert werden. Sie können die Batchgröße auf 1 festlegen, wenn Sie die parallele Ausführung von durch Warteschlangen ausgelösten Funktionen in einer Funktions-App minimieren möchten. Diese Einstellung verhindert Parallelität nur so lange, wie Ihre Funktions-App auf einem einzelnen virtuellen Computer ausgeführt wird. 

Mit dem Warteschlangentrigger wird automatisch verhindert, dass eine Funktion Warteschlangennachrichten mehrfach verarbeitet. Daher müssen keine idempotenten Funktionen geschrieben werden.

## <a name="trigger---hostjson-properties"></a>Trigger: Eigenschaften von „host.json“

Die Datei [host.json](functions-host-json.md#queues) enthält Einstellungen, mit denen das Verhalten des Warteschlangentriggers gesteuert werden kann. Informationen zu verfügbaren Einstellungen finden Sie im Abschnitt [Einstellungen für „host.json“](#hostjson-settings).

## <a name="output"></a>Output

Verwenden Sie die Azure Queue Storage-Ausgabebindung, um Nachrichten in eine Warteschlange zu schreiben.

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

Die [C#-Funktion](functions-dotnet-class-library.md) des folgenden Beispiels erstellt eine Warteschlangennachricht für jede empfangene HTTP-Anforderung.

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

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
      "name": "return"
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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

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

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

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
      "name": "return"
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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Wenn Sie mehrere Nachrichten gleichzeitig senden möchten, können Sie ein Nachrichtenarray für die Ausgabebindung `myQueueItem` definieren. Der folgende JavaScript-Code sendet zwei Warteschlangennachrichten mit hartcodierten Werten für jede empfangene HTTP-Anforderung.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Ausgabe: Java-Beispiel

 Die Java-Funktion des folgenden Beispiels erstellt eine Warteschlangennachricht bei Auslösung durch eine HTTP-Anforderung.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Verwenden Sie die `@QueueOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert in Queue Storage geschrieben wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei „T“ für einen beliebigen nativen Java-Typ eines POJO steht.


## <a name="output---attributes"></a>Ausgabe: Attribute

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

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

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter „Trigger: Attribute“.

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Queue` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `queue` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `out` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlange im Funktionscode darstellt. Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.|
|**queueName** |**QueueName** | Der Name der Warteschlange. |
|**Verbindung** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Ausgabe: Verwendung

Verwenden Sie in C# und C#-Skripts einen Methodenparameter wie `out T paramName`, um eine einzelne Warteschlangennachricht zu schreiben. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Anstelle eines Parameters vom Typ `out` können Sie den Rückgabetyp der Methode verwenden, und `T` kann einer der folgenden Typen sein:

* Ein Objekt, das als JSON serialisierbar ist
* `string`
* `byte[]`
* [CloudQueueMessage] 

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

Verwenden Sie einen der folgenden Typen, um in C# und C#-Skripts mehrere Warteschlangennachrichten zu schreiben: 

* `ICollector<T>` oder `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

Verwenden Sie in JavaScript-Funktionen `context.bindings.<name>`, um auf die Ausgabewarteschlangennachricht zuzugreifen. Für die Nutzlast des Warteschlangenelements kann eine Zeichenfolge oder ein JSON-serialisierbares Objekt verwendet werden.


## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung |  Verweis |
|---|---|
| Warteschlange | [Warteschlangen-Fehlercodes](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabelle, Warteschlange | [Speicherfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange |  [Problembehandlung](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x beschrieben. Die nachfolgende Beispieldatei „host.json“ enthält nur die Einstellungen für Version 2.x für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

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
|maxPollingInterval|00:00:01|Das maximale Intervall zwischen Warteschlangenabfragen. Der Mindestwert lautet 00:00:00.100 (100 ms), er wird bis auf 00:01:00 (1 Min.) erhöht. |
|visibilityTimeout|00:00:00|Das Zeitintervall zwischen Wiederholungsversuchen, wenn bei der Verarbeitung einer Nachricht ein Fehler auftritt. |
|batchSize|16|Die Anzahl der Warteschlangennachrichten, die die Functions-Runtime gleichzeitig abruft und parallel verarbeitet. Wenn die zu verarbeitende Anzahl `newBatchThreshold` erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten `batchSize` plus `newBatchThreshold`. Dieser Grenzwert gilt separat für jede Funktion, die durch die Warteschlange ausgelöst wird. <br><br>Wenn Sie eine parallele Ausführung für in einer Warteschlange empfangene Nachrichten vermeiden möchten, können Sie `batchSize` auf „1“ festlegen. Diese Einstellung verhindert Parallelität jedoch nur so lange, wie Ihre Funktions-App auf einem einzelnen virtuellen Computer (VM) ausgeführt wird. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, kann jeder virtuelle Computer eine Instanz jeder durch die Warteschlange ausgelösten Funktion ausführen.<br><br>Die maximale `batchSize` beträgt 32. |
|maxDequeueCount|5|Die Anzahl der Versuche zum Verarbeiten einer Nachricht, bevor diese in die Warteschlange für nicht verarbeitete Nachrichten verschoben wird.|
|newBatchThreshold|batchSize/2|Wenn die Anzahl der gleichzeitig verarbeiteten Nachrichten auf diesen Wert sinkt, ruft die Runtime einen weiteren Batch ab.|

## <a name="next-steps"></a>Nächste Schritte

* [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Hinzufügen von Meldungen in die Warteschlange von Azure Storage mithilfe von Functions](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
