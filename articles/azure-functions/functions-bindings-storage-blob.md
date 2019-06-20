---
title: Azure Blob Storage-Bindungen für Azure Functions
description: Hier erfahren Sie, wie Sie Azure Blob Storage-Trigger und -Bindungen in Azure Functions verwenden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: f54da6e350b2cf9027b6e9e02ace2a90e292e1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472344"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob Storage-Bindungen für Azure Functions

In diesem Artikel erfahren Sie, wie Sie Azure Blob Storage-Bindungen in Azure Functions verwenden. Azure Functions unterstützt Trigger-, Eingabe- und Ausgabebindungen für Blobs. Der Artikel enthält für jede Bindung einen Abschnitt:

* [Blobtrigger](#trigger)
* [Blobeingabebindung](#input)
* [Blobausgabebindung](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Verwenden Sie den Event Grid-Trigger anstelle des Blobspeichertriggers für reine Blobspeicherkonten, für eine hohe Anzahl oder zum Verringern der Wartezeit. Weitere Informationen finden Sie im Abschnitt [Trigger](#trigger).

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Blobspeicherbindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), Version 2.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die Blobspeicherbindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), Version 3.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Der Blobspeichertrigger startet eine Funktion, wenn ein neues oder aktualisiertes Blob erkannt wird. Der Blob-Inhalt wird als Eingabe für die Funktion bereitgestellt.

Der [Event Grid-Trigger](functions-bindings-event-grid.md) verfügt über eine integrierte Unterstützung für [Blobereignisse](../storage/blobs/storage-blob-event-overview.md) und kann auch verwendet werden, um eine Funktion zu starten, wenn ein neuer oder aktualisierter Blob erkannt wird. Ein Beispiel finden Sie die im Tutorial [Ändern der Imagegröße mit Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

Verwenden Sie Event Grid anstelle der Blobspeichertrigger für die folgenden Szenarien:

* Blob-Speicherkonten
* Unterstützung einer hohen Anzahl
* Minimieren der Latenz

### <a name="blob-storage-accounts"></a>Blob-Speicherkonten

[Blobspeicherkonten](../storage/common/storage-account-overview.md#types-of-storage-accounts) werden für die Blobeingabe- und -ausgabebindungen jedoch nicht für Blobtrigger unterstützt. Blob Storage-Trigger erfordern ein allgemeines Speicherkonto.

### <a name="high-scale"></a>Unterstützung einer hohen Anzahl

Als „hohe Anzahl“ können Container mit mehr als 100.000 Blobs oder Speicherkonten mit mehr als 100 Blobupdates pro Sekunde lose definiert werden.

### <a name="latency-issues"></a>Wartezeitprobleme

Wenn Ihre Funktions-App im Verbrauchsplan enthalten ist, kann es möglicherweise bis zu 10 Minuten dauern, bis neue Blobs verarbeitet werden, nachdem eine Funktionen-App in den Leerlauf gewechselt ist. Um dies Wartezeit zu vermeiden, können Sie zu einem App Service-Plan wechseln, für den „Always On“ aktiviert ist. Sie können auch einen [Event Grid-Trigger](functions-bindings-event-grid.md) mit Ihrem Blobspeicherkonto verwenden. Ein Beispiel finden Sie im [Event Grid-Tutorial](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Queue Storage-Trigger

Neben Event Grid ist eine weitere Alternative zur Verarbeitung von Blobs des Queue Storage-Triggers, der jedoch keine integrierte Unterstützung für Blobereignisse bietet. Sie müssten beim Erstellen oder Aktualisieren von Blobs Warteschlangennachrichten erstellen. Ein Beispiel – ausgehend davon, dass dies der Fall ist – finden Sie weiter unten in diesem Artikel im [Beispiel für Blobeingabebindungen](#input---example).

## <a name="trigger---example"></a>Trigger: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#trigger---c-example)
* [C#-Skript (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger: C#-Beispiel

Mit der [C#-Funktion](functions-dotnet-class-library.md) im folgenden Beispiel wird ein Protokoll geschrieben, wenn im Container `samples-workitems` ein Blob hinzugefügt oder aktualisiert wird.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#trigger---blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zum Attribut `BlobTrigger` finden Sie unter [Trigger: Attribute](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Trigger: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer Datei *function.json* und [Python-Code](functions-reference-python.md), der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im `samples-workitems`-[Container ](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) ein Blob hinzugefügt oder aktualisiert wird.

Bindungsdaten in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#trigger---blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#trigger---configuration), in dem diese Eigenschaften erläutert werden.

C#-Skriptcode für die Bindung an `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

C#-Skriptcode für die Bindung an `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Trigger: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer Datei *function.json* und [JavaScript-Code](functions-reference-node.md), der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im Container `samples-workitems` ein Blob hinzugefügt oder aktualisiert wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#trigger---blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#trigger---configuration), in dem diese Eigenschaften erläutert werden.

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Trigger: Beispiel für Python

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer Datei *function.json* und [Python-Code](functions-reference-python.md), der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im `samples-workitems`-[Container ](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) ein Blob hinzugefügt oder aktualisiert wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#trigger---blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#trigger---configuration), in dem diese Eigenschaften erläutert werden.

Dies ist der Python-Code:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Trigger: Java-Beispiel

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer Datei *function.json* und [Java-Code](functions-reference-java.md), der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im Container `myblob` ein Blob hinzugefügt oder aktualisiert wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Dies ist der Java-Code:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Trigger: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Blobtrigger zu konfigurieren:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Der Konstruktor des Attributs akzeptiert eine Pfadzeichenfolge, die den zu überwachenden Container angibt, und optional ein [Blobnamensmuster](#trigger---blob-name-patterns). Hier sehen Sie ein Beispiel:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
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
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Das zu verwendende Speicherkonto wird anhand von Folgendem bestimmt (in der angegebenen Reihenfolge):

* Die Eigenschaft `Connection` des Attributs `BlobTrigger`.
* Das Attribut `StorageAccount`, das auf den gleichen Parameter angewendet wird wie das Attribut `BlobTrigger`.
* Das Attribut `StorageAccount`, das auf die Funktion angewendet wird.
* Das Attribut `StorageAccount`, das auf die Klasse angewendet wird.
* Das Standardspeicherkonto für die Funktions-App (App-Einstellung „AzureWebJobsStorage“).

## <a name="trigger---configuration"></a>Trigger: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `BlobTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blobTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. Ausnahmen sind im Abschnitt [Verwendung](#trigger---usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt. |
|**path** | **BlobPath** |Der zu überwachende [Container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).  Kann ein [Blobnamensmuster](#trigger---blob-name-patterns) sein. |
|**Verbindung** | **Connection** | Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.<br><br>Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [Blobspeicherkonto](../storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger: Verwendung

In C#- und C#-Skripts können Sie die folgenden Parametertypen für das auslösende Blob verwenden:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Ein als JSON serialisierbares POCO
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

Eine Bindung mit `string`, `Byte[]` oder POCO wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](#trigger---concurrency-and-memory-usage) weiter unten in diesem Artikel.

Verwenden Sie in JavaScript `context.bindings.<name from function.json>`, um auf die Eingabeblobdaten zuzugreifen.

## <a name="trigger---blob-name-patterns"></a>Trigger: Blobnamensmuster

Ein Blobnamensmuster kann in der Eigenschaft `path` (in *function.json*) oder im Konstruktor des Attributs `BlobTrigger` angegeben werden. Das Namensmuster kann ein [Filter oder Bindungsausdruck](./functions-bindings-expressions-patterns.md) sein. Die folgenden Abschnitte enthalten einige Beispiele.

### <a name="get-file-name-and-extension"></a>Abrufen von Dateiname und Erweiterung

Das folgende Beispiel zeigt, wie Sie den Blobdateinamen und die Erweiterung separat binden:

```json
"path": "input/{blobname}.{blobextension}",
```
Wenn das Blob *original-Blob1.txt* heißt, lauten die Werte der Variablen `blobname` und `blobextension` im Funktionscode *original-Blob1* und *txt*.

### <a name="filter-on-blob-name"></a>Filtern nach Blobname

Im folgenden Beispiel wird der Trigger nur für Blobs im Container `input` ausgelöst, deren Name mit „original-“ beginnt:

```json
"path": "input/original-{name}",
```

Wenn der Blobname *original-Blob1.txt* lautet, hat die Variable `name` im Funktionscode den Wert `Blob1`.

### <a name="filter-on-file-type"></a>Filtern nach Dateityp

Im folgenden Beispiel wird der Trigger nur für *PNG-Dateien* ausgelöst:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtern nach geschweiften Klammern in Dateinamen

Wenn in Dateinamen nach geschweiften Klammern gesucht werden soll, müssen doppelte Klammern verwendet werden. Im folgenden Beispiel wird nach Blobs gefiltert, deren Name geschweifte Klammern enthält:

```json
"path": "images/{{20140101}}-{name}",
```

Wenn der Blobname *{20140101}-soundfile.mp3* lautet, erhält die Variable `name` im Funktionscode den Wert *soundfile.mp3*.

## <a name="trigger---metadata"></a>Trigger: Metadaten

Der Blobtrigger stellt mehrere Metadateneigenschaften bereit. Diese Eigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Diese Werte haben die gleiche Semantik wie der Typ [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet).

|Eigenschaft  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Der Pfad des auslösenden Blobs.|
|`Uri`|`System.Uri`|Der Blob-URI für den primären Speicherort|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Die Systemeigenschaften des Blobs |
|`Metadata` |`IDictionary<string,string>`|Die benutzerdefinierten Metadaten für das Blob|

Beispielsweise protokollieren die folgenden C#-Skript- und JavaScript-Beispiele den Pfad zum auslösenden Blob, einschließlich des Containers:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Trigger: Blobbelege

Die Azure Functions-Runtime stellt sicher, dass Blobtriggerfunktionen für ein neues oder aktualisiertes Blob nicht mehrmals aufgerufen werden. Zu diesem Zweck wird mittels Verwaltung der *Blobbelege* ermittelt, ob eine bestimmte Blobversion verarbeitet wurde.

Azure Functions speichert Blobbelege in einem Container mit dem Namen *azure-webjobs-hosts* im Azure Storage-Konto für Ihre Funktions-App (per App-Einstellung `AzureWebJobsStorage` definiert). Ein Blobbeleg enthält die folgenden Informationen:

* Die ausgelöste Funktion („ *&lt;Funktions-App-Name>* .Functions. *&lt;Funktionsname>* “, z. B.: „MyFunctionApp.Functions.CopyBlob“)
* Containername
* Blobtyp ("BlockBlob" oder "PageBlob")
* Blobname
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

Um eine erneute Verarbeitung eines Blobs zu erzwingen, können Sie den Blobbeleg für dieses Blob manuell aus dem Container *azure-webjobs-hosts* löschen. Auch wenn eine erneute Verarbeitung eventuell nicht sofort ausgeführt wird, erfolgt sie doch garantiert zu einem späteren Zeitpunkt.

## <a name="trigger---poison-blobs"></a>Trigger: Nicht verarbeitbare Blobs

Wenn bei Ausführung einer Blobtriggerfunktion für ein Blob ein Fehler auftritt, versucht Azure Functions standardmäßig bis zu fünfmal, diese Funktion für ein bestimmtes Blob auszuführen.

Wenn bei allen fünf Versuchen Fehler auftreten, fügt Azure Functions der Storage-Warteschlange *webjobs-blobtrigger-poison* eine Nachricht hinzu. Die Warteschlangennachricht für nicht verarbeitbare Blobs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *&lt;Funktionen-App-Name>* .Functions. *&lt;Funktionsname>* )
* BlobType ("BlockBlob" oder "PageBlob")
* ContainerName
* BlobName
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger: Nebenläufigkeit und Arbeitsspeichernutzung

Der Blobtrigger verwendet intern eine Warteschlange, sodass die maximal zulässige Anzahl gleichzeitiger Funktionsaufrufe durch die [Warteschlangenkonfiguration in „host.json“](functions-host-json.md#queues) gesteuert wird. Durch die Standardeinstellungen wird die Anzahl gleichzeitiger Aufrufe auf 24 beschränkt. Dieser Grenzwert gilt separat für jede Funktion, die einen Blobtrigger verwendet.

[Der Verbrauchstarif](functions-scale.md#how-the-consumption-and-premium-plans-work) schränkt eine Funktions-App auf einem virtuellen Computer (VM) auf 1,5 GB an Arbeitsspeicher ein. Der Arbeitsspeicher wird von jeder parallel ausgeführten Funktionsinstanz und durch die Functions-Runtime selbst verwendet. Wenn eine durch einen Blob ausgelöste Funktion das gesamte Blob in den Arbeitsspeicher lädt, entspricht der maximal von dieser Funktion verwendete Arbeitsspeicher nur für Blobs dem 24 * der maximalen Blobgröße. Beispiel: Eine Funktions-App, die drei Funktionen mit Blobtrigger umfasst und die Standardeinstellungen verwendet, weist pro VM eine maximale Nebenläufigkeit von 3 * 24 = 72 Funktionsaufrufen auf.

JavaScript- und Java-Funktionen laden das gesamte Blob in den Arbeitsspeicher, C#-Funktionen zeigen dieses Verhalten bei einer Bindung an `string`, `Byte[]` oder POCO.

## <a name="trigger---polling"></a>Trigger: Abruf

Wenn der überwachte Blobcontainer mehr als 10.000 Blobs (Summe aller Container) enthält, überprüft die Functions-Runtime Protokolldateien auf neue oder geänderte Blobs. Dieser Vorgang kann zu Verzögerungen führen. Eine Funktion wird unter Umständen erst mehrere Minuten nach der Bloberstellung oder noch später ausgelöst.

> [!WARNING]
> Außerdem erfolgt das [Erstellen von Storage-Protokollen auf bestmögliche Weise](/rest/api/storageservices/About-Storage-Analytics-Logging). Es gibt keine Garantie, dass alle Ereignisse erfasst werden. Unter bestimmten Umständen können Protokolle fehlen.
> 
> Wenn eine schnellere oder zuverlässigere Blobverarbeitung erforderlich ist, sollten Sie beim Erstellen des Blobs eine [Warteschlangennachricht](../storage/queues/storage-dotnet-how-to-use-queues.md) erstellen. Verwenden Sie dann einen [Warteschlangentrigger](functions-bindings-storage-queue.md) anstelle eines Blobtriggers für die Verarbeitung des Blobs. Eine andere Möglichkeit ist die Verwendung von Event Grid. Weitere Informationen finden Sie im Tutorial [Automatisieren der Größenänderung von hochgeladenen Bildern mit Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Eingabe

Verwenden Sie eine Blobspeicher-Eingabebindung zum Lesen von Blobs.

## <a name="input---example"></a>Eingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#input---c-example)
* [C#-Skript (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Eingabe – C#-Beispiel

Das folgende Beispiel enthält eine [C#-Funktion](functions-dotnet-class-library.md), bei der ein Warteschlangentrigger und eine Eingabeblobbindung verwendet werden. Die Warteschlangennachricht enthält den Namen des Blobs, und die Funktion protokolliert die Größe des Blobs.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="input---c-script-example"></a>Eingabe: C#-Skriptbeispiel

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Eingabe: JavaScript-Beispiel

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Eingabe: Beispiel für Python

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
      "name": "$return",
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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Dies ist der Python-Code:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Eingabe: Java-Beispiele

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger: Suchen des Blobnamens in einer Abfragezeichenfolge](#http-trigger-look-up-blob-name-from-query-string-java)
* [Warteschlangentrigger: Empfangen des Blobnamens aus einer Warteschlangennachricht](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>HTTP-Trigger: Suchen des Blobnamens in einer Abfragezeichenfolge (Java)

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der Anmerkung ```HttpTrigger``` einen Parameter mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung ```BlobInput``` liest dann die Datei und übergibt ihren Inhalt als ```byte[]``` an die Funktion.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Warteschlangentrigger: Empfangen des Blobnamens aus einer Warteschlangennachricht (Java)

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der ```QueueTrigger```-Anmerkung eine Nachricht mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung ```BlobInput``` liest dann die Datei und übergibt ihren Inhalt als ```byte[]``` an die Funktion.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Verwenden Sie die `@BlobInput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert aus einem Blob empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

## <a name="input---attributes"></a>Eingabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Der Konstruktor des Attributs akzeptiert den Pfad des Blobs sowie einen Parameter vom Typ `FileAccess`, der angibt, ob es sich um einen Lesevorgang oder um einen Schreibvorgang handelt. Beispiel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Trigger: Attribute](#trigger---attributes).

## <a name="input---configuration"></a>Eingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Blob` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blob` festgelegt sein. |
|**direction** | – | Muss auf `in` festgelegt sein. Ausnahmen sind im Abschnitt [Verwendung](#input---usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt.|
|**path** |**BlobPath** | Der Pfad des Blobs. |
|**Verbindung** |**Connection**| Der Name einer App-Einstellung, die die [Speicherverbindungszeichenfolge](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.<br><br>Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [reines Blob Storage-Konto](../storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.|
|– | **zugreifen** | Gibt an, ob Sie einen Lesevorgang oder einen Schreibvorgang ausführen möchten. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Eingabe: Verwendung

In C#- und C#-Skripts können Sie die folgenden Parametertypen für die Blobeingabebindung verwenden:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

Eine Bindung mit `string` oder `Byte[]` wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](#trigger---concurrency-and-memory-usage) weiter oben in diesem Artikel.

Verwenden Sie in JavaScript `context.bindings.<name from function.json>`, um auf die Blobdaten zuzugreifen.

## <a name="output"></a>Output

Verwenden Sie Blobspeicher-Ausgabebindungen, um Blobs zu schreiben.

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

In der [C#-Funktion](functions-dotnet-class-library.md) im folgenden Beispiel werden ein Blobtrigger und zwei Ausgabeblobbindungen verwendet. Die Funktion wird durch die Erstellung eines Bildblobs im Container *sample-images* ausgelöst. Sie erstellt kleine und mittelgroße Kopien der Bildblobs.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Ausgabe: Beispiel für Python

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

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Dies ist der Python-Code:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Ausgabe: Java-Beispiele

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger mit OutputBinding](#http-trigger-using-outputbinding-java)
* [Warteschlangentrigger mit Funktionsrückgabewert](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-Trigger mit OutputBinding (Java)

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der Anmerkung ```HttpTrigger``` einen Parameter mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung ```BlobInput``` liest dann die Datei und übergibt ihren Inhalt als ```byte[]``` an die Funktion. Die Anmerkung ```BlobOutput``` bindet an ```OutputBinding outputItem```, das dann von der Funktion verwendet wird, um den Inhalt des Eingabeblobs in den konfigurierten Speichercontainer zu schreiben.

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

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der ```QueueTrigger```-Anmerkung eine Nachricht mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung ```BlobInput``` liest dann die Datei und übergibt ihren Inhalt als ```byte[]``` an die Funktion. Die ```BlobOutput```-Anmerkung bindet an den Rückgabewert der Funktion, der dann von der Runtime verwendet wird, um den Inhalt des Eingabeblobs in den konfigurierten Speichercontainer zu schreiben.

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

## <a name="output---attributes"></a>Ausgabe: Attribute

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

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Trigger: Attribute](#trigger---attributes).

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Blob` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blob` festgelegt sein. |
|**direction** | – | Muss für eine Ausgabebindung auf `out` festgelegt werden. Ausnahmen sind im Abschnitt [Verwendung](#output---usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt.  Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.|
|**path** |**BlobPath** | Der Pfad zum Blobcontainer. |
|**Verbindung** |**Connection**| Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.<br><br>Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [reines Blob Storage-Konto](../storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.|
|– | **zugreifen** | Gibt an, ob Sie einen Lesevorgang oder einen Schreibvorgang ausführen möchten. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Ausgabe: Verwendung

In C#- und C#-Skript können Sie sich an die folgenden Typen binden, um Blobs zu schreiben:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Erfordert die in-Bindung `direction` in *function.json* oder `FileAccess.Read` in einer C#-Klassenbibliothek. Allerdings können Sie das Containerobjekt verwenden, das die Runtime für Schreibvorgänge bereitstellt (z.B. zum Hochladen von Blobs in den Container).

<sup>2</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

In asynchronen Funktionen verwenden Sie den Rückgabewert oder `IAsyncCollector` anstelle eines `out`-Parameters.

Eine Bindung mit `string` oder `Byte[]` wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](#trigger---concurrency-and-memory-usage) weiter oben in diesem Artikel.


Verwenden Sie in JavaScript `context.bindings.<name from function.json>`, um auf die Blobdaten zuzugreifen.

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung |  Verweis |
|---|---|
| Blob | [Blobfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tabelle, Warteschlange |  [Speicherfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange |  [Problembehandlung](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nächste Schritte

* [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
