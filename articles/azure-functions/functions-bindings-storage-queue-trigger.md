---
title: Azure Queue Storage-Trigger für Azure Functions
description: Erfahren Sie, wie Sie eine Azure-Funktion ausführen, wenn sich Azure Queue Storage-Daten ändern.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 95560801d4132735435e4d45e8a588476636ec38
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001234"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Queue Storage-Trigger für Azure Functions

Der Queue Storage-Trigger führt eine Funktion aus, wenn Azure Queue Storage Nachrichten hinzugefügt werden.

## <a name="encoding"></a>Codieren

Funktionen erwarten eine *Base64*-codierte Zeichenfolge. Alle Anpassungen am Codierungstyp (um Daten als *Base64*-codierte Zeichenfolge vorzubereiten) müssen im aufrufenden Dienst implementiert werden.

## <a name="example"></a>Beispiel

Verwenden Sie den Warteschlangentrigger, um eine Funktion zu starten, wenn bei einer Warteschlange ein neues Element eingeht. Die Warteschlangennachricht wird als Eingabe für die Funktion bereitgestellt.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

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

Im Abschnitt [Verwendung](#usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#message-metadata) erläutert.

# <a name="java"></a>[Java](#tab/java)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

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

Im Abschnitt [Verwendung](#usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#message-metadata) erläutert.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende Beispiel zeigt, wie Sie eine an eine Funktion übergebene Warteschlangennachricht mittels Trigger lesen.

Der Trigger einer Speicherwarteschlange wird in der Datei *function.json* definiert, wobei `type` auf `queueTrigger` festgelegt ist.

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Der Code in der Datei *Run.ps1* deklariert einen Parameter als `$QueueItem`, was es Ihnen ermöglicht, die Warteschlangennachricht in ihrer Funktion zu lesen.

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt, wie Sie eine an eine Funktion übergebene Warteschlangennachricht mittels Trigger lesen.

Der Trigger einer Speicherwarteschlange ist in *function.json* definiert, wobei *type* auf `queueTrigger` festgelegt ist.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Der Code in *_\_init_\_.py* deklariert einen Parameter als `func.QueueMessage`, was es Ihnen ermöglicht, die Warteschlangennachricht in Ihrer Funktion zu lesen.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

 ---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Warteschlangentrigger zu konfigurieren:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

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

  Sie können die Eigenschaft `Connection` festlegen, um die App-Einstellung anzugeben, die die zu verwendende Verbindungszeichenfolge des Speicherkontos enthält, wie im folgenden Beispiel gezeigt:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Ein vollständiges Beispiel finden Sie unter [Beispiel](#example).

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

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Die `QueueTrigger`-Anmerkung gewährt Ihnen Zugriff auf die Warteschlange, die die Funktion auslöst. Im folgenden Beispiel wird die Warteschlangennachricht über den Parameter `message` für die Funktion verfügbar gemacht.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Eigenschaft    | BESCHREIBUNG |
|-------------|-----------------------------|
|`name`       | Deklariert den Parameternamen in der Funktionssignatur. Wenn die Funktion ausgelöst wird, enthält der Wert dieses Parameters den Inhalt der Warteschlangennachricht. |
|`queueName`  | Deklariert den Warteschlangennamen im Speicherkonto. |
|`connection` | Verweist auf die Speicherkonto-Verbindungszeichenfolge. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `QueueTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | –| Muss auf `queueTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction**| – | Nur in der Datei *function.json*. Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – |Der Name der Variablen, die die Nutzlast des Warteschlangenelements im Funktionscode enthält.  |
|**queueName** | **QueueName**| Der Name der abzufragenden Warteschlange. |
|**connection** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Runtime nach einer App-Einstellung namens „MyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Zugriff auf die Nachrichtendaten mittels eines Methodenparameters wie `string paramName`. Eine Bindung kann mit folgenden Typen erstellt werden:

* Objekt – Die Functions-Runtime deserialisiert eine JSON-Nutzlast in eine Instanz einer beliebigen Klasse, die in Ihrem Code definiert ist.
* `string`
* `byte[]`
* [CloudQueueMessage]

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Zugriff auf die Nachrichtendaten mittels eines Methodenparameters wie `string paramName`. `paramName` ist der Wert, der in der Eigenschaft `name` von *function.json* angegeben wird. Eine Bindung kann mit folgenden Typen erstellt werden:

* Objekt – Die Functions-Runtime deserialisiert eine JSON-Nutzlast in eine Instanz einer beliebigen Klasse, die in Ihrem Code definiert ist. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Wenn Sie versuchen, eine Bindung an `CloudQueueMessage` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

# <a name="java"></a>[Java](#tab/java)

Die [QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable&preserve-view=true)-Anmerkung gewährt Ihnen Zugriff auf die Warteschlangennachricht, die die Funktion ausgelöst hat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Nutzlast des Warteschlangenelements ist über `context.bindings.<NAME>` verfügbar, wobei `<NAME>` dem in *function.json* definierten Namen entspricht. Falls es sich um eine JSON-Nutzlast handelt, wird der Wert in ein Objekt deserialisiert.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Greifen Sie auf die Warteschlangennachricht über einen Zeichenfolgenparameter zu, der mit dem Namen übereinstimmt, der durch den `name`-Parameter der Bindung in der Datei *function.json* festgelegt ist.

# <a name="python"></a>[Python](#tab/python)

Greifen Sie über den Parameter, der als [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage?view=azure-python&preserve-view=true) typisiert ist, auf die Warteschlangennachricht zu.

---

## <a name="message-metadata"></a>Metadaten von Nachrichten

Der Warteschlangentrigger stellt mehrere [Metadateneigenschaften](./functions-bindings-expressions-patterns.md#trigger-metadata) bereit. Diese Eigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Die Eigenschaften sind Elemente der [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)-Klasse.

|Eigenschaft|type|BESCHREIBUNG|
|--------|----|-----------|
|`QueueTrigger`|`string`|Die Warteschlangennutzlast (bei einer gültigen Zeichenfolge). Handelt es sich bei der Warteschlangennutzlast um eine Zeichenfolge, hat `QueueTrigger` den gleichen Wert wie die Variable, die durch die Eigenschaft `name` in *function.json* benannt wird.|
|`DequeueCount`|`int`|Gibt an, wie oft diese Nachricht aus der Warteschlange entfernt wurde.|
|`ExpirationTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht abläuft.|
|`Id`|`string`|ID der Warteschlangennachricht|
|`InsertionTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht der Warteschlange hinzugefügt wurde.|
|`NextVisibleTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht als Nächstes sichtbar wird.|
|`PopReceipt`|`string`|Die POP-Bestätigung der Nachricht.|

## <a name="poison-messages"></a>Nicht verarbeitbare Nachrichten

Falls eine Funktion des Warteschlangentriggers nicht erfolgreich ausgeführt werden kann, versucht Azure Functions für eine bestimmte Warteschlangennachricht bis zu fünf Mal (einschließlich des ersten Versuchs), die Funktion auszuführen. Sind alle fünf Versuche nicht erfolgreich, fügt die Functions-Laufzeit einer Warteschlange namens *&lt;Name der Originalwarteschlange>-poison* eine Nachricht hinzu. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist.

Überprüfen Sie zur manuellen Behandlung nicht verarbeitbarer Nachrichten den Wert [dequeueCount](#message-metadata) der Warteschlangennachricht.

## <a name="polling-algorithm"></a>Abrufalgorithmus

Der Warteschlangentrigger implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.

Der Algorithmus verwendet die folgende Logik:

- Wenn eine Nachricht gefunden wird, wartet die Runtime zwei Sekunden und prüft dann, ob eine andere Nachricht vorhanden ist.
- Wenn keine Nachricht gefunden wird, wartet sie ungefähr vier Sekunden, bevor der Versuch wiederholt wird.
- Nach aufeinander folgenden fehlgeschlagenen Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit, standardmäßig eine Minute, erreicht ist.
- Die maximale Wartezeit kann über die `maxPollingInterval`-Eigenschaft in der Datei [host.json](functions-host-json-v1.md#queues) konfiguriert werden.

Bei der lokalen Entwicklung ist das maximale Abrufintervall standardmäßig auf zwei Sekunden eingestellt.

Im Hinblick auf die Abrechnung ist die Zeit, die für das Abrufen der Runtime aufgewendet wird, „kostenlos“ und wird Ihrem Konto nicht angerechnet.

## <a name="concurrency"></a>Parallelität

Wenn mehrere Warteschlangennachrichten warten, ruft der Warteschlangentrigger einen Batch mit Nachrichten ab und ruft parallel Funktionsinstanzen zur Verarbeitung auf. Standardmäßig ist die Batchgröße 16. Wenn die zu verarbeitende Anzahl 8 erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion auf einem virtuellen Computer verarbeiteten parallelen Nachrichten 24. Dieser Grenzwert gilt separat für jede durch Warteschlangen ausgelöste Funktion auf jedem virtuellen Computer. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, wartet jeder virtuelle Computer auf Trigger und versucht, Funktionen auszuführen. Beispiel: Wenn eine Funktions-App auf drei virtuelle Computer horizontal hochskaliert wird, beträgt die standardmäßige maximale Anzahl von parallelen Instanzen einer durch Warteschlangen ausgelösten Funktion 72.

Die Batchgröße und der Schwellenwert für das Abrufen eines neuen Batches können in der Datei [host.json](functions-host-json.md#queues) konfiguriert werden. Sie können die Batchgröße auf 1 festlegen, wenn Sie die parallele Ausführung von durch Warteschlangen ausgelösten Funktionen in einer Funktions-App minimieren möchten. Diese Einstellung verhindert Parallelität nur so lange, wie Ihre Funktions-App auf einem einzelnen virtuellen Computer ausgeführt wird. 

Mit dem Warteschlangentrigger wird automatisch verhindert, dass eine Funktion Warteschlangennachrichten mehrmals gleichzeitig verarbeitet.

## <a name="hostjson-properties"></a>Eigenschaften von „host.json“

Die Datei [host.json](functions-host-json.md#queues) enthält Einstellungen, mit denen das Verhalten des Warteschlangentriggers gesteuert werden kann. Informationen zu verfügbaren Einstellungen finden Sie im Abschnitt [Einstellungen für „host.json“](functions-bindings-storage-queue-output.md#hostjson-settings).

## <a name="next-steps"></a>Nächste Schritte

- [Schreiben von Queue Storage-Nachrichten (Ausgabebindung)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
