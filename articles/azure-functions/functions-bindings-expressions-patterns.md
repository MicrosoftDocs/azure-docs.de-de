---
title: Azure Functions – Bindungsausdrücke und Muster
description: Erfahren Sie, wie Sie auf der Grundlage häufiger Muster verschiedene Azure Functions-Bindungsausdrücke erstellen können.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 161e3e7fbc5b343ee73142f0e968367c3cbfaa6b
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927412"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions-Muster für Bindungsausdrücke

Eines der leistungsstärksten Merkmale von [Triggern und Bindungen](./functions-triggers-bindings.md) sind *Bindungsausdrücke*. In der Datei *function.json* , in Funktionsparametern und in Code können Sie Ausdrücke verwenden, die mit Werten aus verschiedenen Quellen aufgelöst werden.

Die meisten Ausdrücke sind von geschweiften Klammern umschlossen. Beispielsweise wird in einer Trigger-Funktion für die Eingabewarteschlange `{queueTrigger}` in den Text der Warteschlangenmeldung aufgelöst. Lautet die Eigenschaft `path` für eine Blobausgabebindung `container/{queueTrigger}`, und wird die Funktion durch eine Warteschlangennachricht `HelloWorld` ausgelöst, so wird ein Blob mit dem Namen `HelloWorld` erstellt.

Arten von Bindungsausdrücken

* [App-Einstellungen](#binding-expressions---app-settings)
* [Name der Triggerdatei](#trigger-file-name)
* [Metadaten für Trigger](#trigger-metadata)
* [JSON-Nutzlasten](#json-payloads)
* [Neue GUID](#create-guids)
* [Aktuelles Datum und Uhrzeit](#current-time)

## <a name="binding-expressions---app-settings"></a>Bindungsausdrücke – App-Einstellungen

Es hat sich bewährt, Geheimnisse und Verbindungszeichenfolgen nicht in Konfigurationsdateien, sondern über App-Einstellungen zu verwalten. Dies schränkt den Zugriff auf diese Geheimnisse ein und ermöglicht das sichere Speichern von Dateien wie *function.json* in öffentlichen Repositorys zur Quellcodeverwaltung.

App-Einstellungen sind auch nützlich, wenn Sie die jeweilige Konfiguration entsprechend der Umgebung ändern möchten. Beispielsweise kann es sein, dass Sie in einer Testumgebung eine andere Warteschlange oder einen anderen Blob Storage-Container überwachen möchten.

Bindungsausdrücke für App-Einstellungen werden anders dargestellt als andere Bindungsausdrücke: Sie sind von Prozentzeichen anstatt von geschweiften Klammern umschlossen. Wenn der Pfad der Blobausgabebindung z.B. `%Environment%/newblob.txt` und der Wert der App-Einstellung `Environment` lautet `Development`, wird ein Blob im Container `Development` erstellt.

Beim lokalen Ausführen einer Funktion werden die App-Einstellungen aus der Datei *local.settings.json* verwendet.

> [!NOTE]
> Die Eigenschaft `connection` von Triggern und Bindungen ist ein Sonderfall, denn für sie werden Werte automatisch als App-Einstellungen ohne Prozentzeichen aufgelöst. 

Im folgenden Beispiel ist ein Azure Queue Storage-Trigger gezeigt, in dem die App-Einstellung `%input_queue_name%` verwendet wird, um die Warteschlange anzugeben, für die die Auslösung erfolgen werden soll.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Sie können den gleichen Ansatz auch in Klassenbibliotheken verwenden:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Name der Triggerdatei

Der `path` für einen Blob-Trigger kann ein Muster sein, mit dem Sie auf den Namen des auslösenden Blobs in anderen Bindungen und Funktionscode verweisen. Das Muster kann auch Filterkriterien enthalten, die angeben, welche Blobs einen Funktionsaufruf auslösen können.

Das Muster `path` für die Blobtriggerbindung im folgenden Beispiel ist `sample-images/{filename}`. Sie erstellt einen Bindungsausdruck mit dem Namen `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Der Ausdruck `filename` kann dann in einer Ausgabebindung verwendet werden, um den Namen des erstellten Blobs anzugeben:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Der Funktionscode hat Zugriff auf diesen Wert, da er `filename` als Parametername verwendet:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Sie haben auch die Möglichkeit, Bindungsausdrücke und -muster für Attribute in Klassenbibliotheken zu verwenden. Im folgenden Beispiel sind die Parameter des Attributkonstruktors dieselben `path`-Werte wie in den vorigen Beispielen für *function.json* : 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Sie können auch Ausdrücke für Teile des Dateinamens erstellen. Im folgenden Beispiel wird die Funktion nur für Dateinamen ausgelöst, die einem Muster entsprechen: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Weitere Informationen zum Verwenden von Ausdrücken und Mustern in der Blob-Pfadzeichenfolge finden Sie im Artikel zu [Azure Blob Storage-Bindungen](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadaten für Trigger

Viele Trigger stellen zusätzlich zur Datennutzlast (z.B. der Inhalt der Warteschlangennachricht, von der eine Funktion ausgelöst wurde) weitere Metadatenwerte bereit. Diese Werte können als Eingabeparameter in C# und F# oder als Eigenschaften für das `context.bindings`-Objekt in JavaScript verwendet werden. 

Beispielsweise unterstützt ein Azure Queue Storage-Trigger die folgenden Eigenschaften:

* QueueTrigger – Auslösen von Nachrichteninhalt, wenn gültige Zeichenfolge
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Der Zugriff auf diese Metadatenwerte ist über die *function.json* -Dateieigenschaften möglich. Angenommen, Sie verwenden einen Warteschlangentrigger und die Warteschlangennachricht enthält den Namen eines Blobs, den Sie lesen möchten. In der *function.json* -Datei können Sie die Metadateneigenschaft `queueTrigger` in der Blobeigenschaft `path` verwenden, wie im folgenden Beispiel gezeigt:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Details der Metadateneigenschaften für jeden Trigger sind im entsprechenden Referenzartikel beschrieben. Ein Beispiel finden Sie unter [Warteschlangentrigger-Metadaten](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentation ist auch im Portal auf der Registerkarte **Integrieren** im Abschnitt **Dokumentation** verfügbar, der sich unter dem Bereich für Bindungskonfigurationen befindet.  

## <a name="json-payloads"></a>JSON-Nutzlasten

Wenn eine Triggernutzlast JSON ist, finden Sie die Eigenschaften in der Konfiguration für andere Bindungen in der gleichen Funktion und im gleichen Funktionscode.

Das folgende Beispiel zeigt die Datei *function.json* für eine Webhook-Funktion, die einen Blobnamen im JSON-Format erhält: `{"BlobName":"HelloWorld.txt"}`. Eine Blobeingabebindung liest den Blob, und die HTTP-Ausgabebindung gibt den Blob-Inhalt in der HTTP-Antwort zurück. Beachten Sie, dass die Blob-Eingabebindung den Blobnamen abruft, indem sie direkt auf die `BlobName`-Eigenschaft verweist (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Damit das in C# und F# funktioniert, benötigen Sie eine Klasse, die die zu deserialisierenden Felder im folgenden Beispiel definiert:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

In JavaScript wird JSON-Deserialisierung automatisch ausgeführt.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Punktnotation

Wenn einige der Eigenschaften in Ihrer JSON-Nutzlast Objekte mit Eigenschaften sind, finden Sie diese durch die Punktnotation. Nehmen wir beispielsweise an, dass Ihr JSON wie folgt aussieht:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Sie können direkt auf `FileName` verweisen: `BlobName.FileName`. In diesem JSON-Format sieht die Eigenschaft `path` im vorherigen Beispiel folgendermaßen aus:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

In C# benötigen Sie zwei Klassen:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Erstellen von GUIDs

Der Bindungsausdruck `{rand-guid}` erstellt eine GUID. Der folgende Blobpfad in einer `function.json`-Datei erstellt einen Blob mit einem Namen wie *50710cb5-84b9 - 4D 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Die aktuelle Zeit

Der Bindungsausdruck `DateTime` wird in `DateTime.UtcNow` aufgelöst. Der folgende Blobpfad in einer `function.json`-Datei erstellt einen Blob mit einem Namen wie *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Binden zur Laufzeit

In C# und anderen .NET-Sprachen können Sie ein imperatives Bindungsmuster verwenden, im Gegensatz zu den deklarativen Bindungen in *function.json* und Attributen. Imperative Bindung eignet sich, wenn Bindungsparameter zur Laufzeit statt zur Entwurfszeit berechnet werden müssen. Weitere Informationen finden Sie in der [C#-Entwicklerreferenz ](functions-dotnet-class-library.md#binding-at-runtime) oder in der [C#-Skriptentwicklerreferenz](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Verwenden des Rückgabewerts einer Azure-Funktion](./functions-bindings-return-value.md)
