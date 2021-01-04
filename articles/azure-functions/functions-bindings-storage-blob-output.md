---
title: Azure Blob Storage-Ausgabebindung für Azure Functions
description: Erfahren Sie, wie Sie Azure Blob Storage-Ausgabebindungsdaten für eine Azure-Funktion bereitstellen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 56d8078347b5de775b30c8db2c9412598070046c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998878"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob Storage-Ausgabebindung für Azure Functions

Die Ausgabebindung ermöglicht Ihnen das Ändern und Löschen von Blob Storage-Daten in einer Azure-Funktion.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-storage-blob.md).

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

In der [C#-Funktion](functions-dotnet-class-library.md) im folgenden Beispiel werden ein Blobtrigger und zwei Ausgabeblobbindungen verwendet. Die Funktion wird durch die Erstellung eines Bildblobs im Container *sample-images* ausgelöst. Sie erstellt kleine und mittelgroße Kopien der Bildblobs.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und Blobausgabebindungen in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem diese Bindungen verwendet werden. Die Funktion erstellt eine Kopie eines Textblobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und -ausgabebindungen in einer Datei *function.json* sowie [JavaScript-Skriptcode](functions-reference-node.md), der diese Bindungen verwendet. Die Funktion erstellt eine Kopie eines Blobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und -ausgabebindungen in einer Datei *function.json* sowie [Python-Code](functions-reference-python.md), der die Bindungen verwendet. Die Funktion erstellt eine Kopie eines Blobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger mit OutputBinding](#http-trigger-using-outputbinding-java)
* [Warteschlangentrigger mit Funktionsrückgabewert](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-Trigger mit OutputBinding (Java)

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der Anmerkung `HttpTrigger` einen Parameter mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung `BlobInput` liest dann die Datei und übergibt ihren Inhalt als `byte[]` an die Funktion. Die Anmerkung `BlobOutput` bindet an `OutputBinding outputItem`, das dann von der Funktion verwendet wird, um den Inhalt des Eingabeblobs in den konfigurierten Speichercontainer zu schreiben.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Warteschlangentrigger mit Funktionsrückgabewert (Java)

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der `QueueTrigger`-Anmerkung eine Nachricht mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung `BlobInput` liest dann die Datei und übergibt ihren Inhalt als `byte[]` an die Funktion. Die `BlobOutput`-Anmerkung bindet an den Rückgabewert der Funktion, der dann von der Runtime verwendet wird, um den Inhalt des Eingabeblobs in den konfigurierten Speichercontainer zu schreiben.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Verwenden Sie die `@BlobOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Funktionsparameter, deren Wert in ein Objekt in Blob Storage geschrieben wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei „T“ für einen beliebigen nativen Java-Typ oder ein POJO steht.

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Der Konstruktor des Attributs akzeptiert den Pfad des Blobs sowie einen Parameter vom Typ `FileAccess`, der angibt, ob es sich um einen Lesevorgang oder um einen Schreibvorgang handelt. Beispiel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
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

Das `@BlobOutput`-Attribut gewährt Ihnen Zugriff auf das Blob, das die Funktion ausgelöst hat. Wenn Sie ein Bytearray mit dem Attribut verwenden, legen Sie `dataType` auf `binary` fest. Weitere Detailinformationen finden Sie im [Ausgabebeispiel](#example).

---

Ein vollständiges Beispiel finden Sie unter [Ausgabebeispiel](#example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Trigger: Attribute](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Blob` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blob` festgelegt sein. |
|**direction** | – | Muss für eine Ausgabebindung auf `out` festgelegt werden. Ausnahmen sind im Abschnitt [Verwendung](#usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt.  Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.|
|**path** |**BlobPath** | Der Pfad zum Blobcontainer. |
|**connection** |**Connection**| Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.<br><br>Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [reines Blob Storage-Konto](../storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.|
|– | **zugreifen** | Gibt an, ob Sie einen Lesevorgang oder einen Schreibvorgang ausführen möchten. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Verwenden Sie in JavaScript `context.bindings.<name from function.json>`, um auf die Blobdaten zuzugreifen.

# <a name="python"></a>[Python](#tab/python)

Sie können Funktionsparameter als die folgenden Typen deklarieren, um Ausgaben in den Blob-Speicher zu schreiben:

* Zeichenfolgen als `func.Out(str)`
* Streams als `func.Out(func.InputStream)`

Weitere Detailinformationen finden Sie im [Ausgabebeispiel](#example).

# <a name="java"></a>[Java](#tab/java)

Das `@BlobOutput`-Attribut gewährt Ihnen Zugriff auf das Blob, das die Funktion ausgelöst hat. Wenn Sie ein Bytearray mit dem Attribut verwenden, legen Sie `dataType` auf `binary` fest. Weitere Detailinformationen finden Sie im [Ausgabebeispiel](#example).

---

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung |  Verweis |
|---|---|
| Blob | [Blobfehlercodes](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tabelle, Warteschlange |  [Speicherfehlercodes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange |  [Problembehandlung](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn sich Blob Storage-Daten ändern](./functions-bindings-storage-blob-trigger.md)
- [Lesen von Blob Storage-Daten, wenn eine Funktion ausgeführt wird](./functions-bindings-storage-blob-input.md)
