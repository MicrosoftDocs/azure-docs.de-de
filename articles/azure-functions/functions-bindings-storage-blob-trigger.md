---
title: Azure Blob Storage-Trigger für Azure Functions
description: Erfahren Sie, wie Sie eine Azure-Funktion ausführen, wenn sich Azure Blob Storage-Daten ändern.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 45393f116149f6cf16763d2d7033f8425df235bf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998834"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Blob Storage-Trigger für Azure Functions

Der Blobspeichertrigger startet eine Funktion, wenn ein neues oder aktualisiertes Blob erkannt wird. Der Blobinhalt wird als [Eingabe für die Funktion](./functions-bindings-storage-blob-input.md) bereitgestellt.

Der Azure Blob Storage-Trigger erfordert ein universelles Speicherkonto. V2-Speicherkonten mit [hierarchischen Namespaces](../storage/blobs/data-lake-storage-namespace.md) werden ebenfalls unterstützt. Um ein Blobkonto zu verwenden, oder wenn Ihre Anwendung spezielle Anforderungen hat, überprüfen Sie die Alternativen zur Verwendung dieses Triggers.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Abruf

Der Abruf funktioniert als Kombination aus dem Überprüfen von Protokollen und dem Ausführen regelmäßiger Containerscans. Blobs werden jeweils in Gruppen von 10.000 gescannt, wobei zwischen den Intervallen ein Fortsetzungstoken verwendet wird.

> [!WARNING]
> Außerdem erfolgt das [Erstellen von Storage-Protokollen auf bestmögliche Weise](/rest/api/storageservices/About-Storage-Analytics-Logging). Es gibt keine Garantie, dass alle Ereignisse erfasst werden. Unter bestimmten Umständen können Protokolle fehlen.
> 
> Wenn eine schnellere oder zuverlässigere Blobverarbeitung erforderlich ist, sollten Sie beim Erstellen des Blobs eine [Warteschlangennachricht](../storage/queues/storage-dotnet-how-to-use-queues.md) erstellen. Verwenden Sie dann einen [Warteschlangentrigger](functions-bindings-storage-queue.md) anstelle eines Blobtriggers für die Verarbeitung des Blobs. Eine andere Möglichkeit ist die Verwendung von Event Grid. Weitere Informationen finden Sie im Tutorial [Automatisieren der Größenänderung von hochgeladenen Bildern mit Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternativen

### <a name="event-grid-trigger"></a>Event Grid-Trigger

Der [Event Grid-Trigger](functions-bindings-event-grid.md) bietet auch integrierte Unterstützung für [Blobereignisse](../storage/blobs/storage-blob-event-overview.md). Verwenden Sie Event Grid anstelle der Blobspeichertrigger für die folgenden Szenarien:

- **Reine Blobspeicherkonten**: [Reine Blobspeicherkonten](../storage/common/storage-account-overview.md#types-of-storage-accounts) werden für die Blobeingabe- und -ausgabebindungen, jedoch nicht für Blobtrigger unterstützt.

- **Hohe Anzahl**: Als „hohe Anzahl“ können Container mit mehr als 100.000 Blobs oder Speicherkonten mit mehr als 100 Blobupdates pro Sekunde lose definiert werden.

- **Minimieren der Latenzzeit**: Wenn Ihre Funktions-App im Verbrauchsplan enthalten ist, kann es möglicherweise bis zu 10 Minuten dauern, bis neue Blobs verarbeitet werden, nachdem eine Funktionen-App in den Leerlauf gewechselt ist. Um dies Wartezeit zu vermeiden, können Sie zu einem App Service-Plan wechseln, für den „Always On“ aktiviert ist. Sie können auch einen [Event Grid-Trigger](functions-bindings-event-grid.md) mit Ihrem Blobspeicherkonto verwenden. Ein Beispiel finden Sie im [Event Grid-Tutorial](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Ein Beispiel für ein Event Grid finden Sie im Tutorial [Ändern der Imagegröße mit Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

### <a name="queue-storage-trigger"></a>Queue Storage-Trigger

Ein weiterer Ansatz für die Verarbeitung von Blobs besteht im Schreiben von Warteschlangennachrichten, die Blobs entsprechen, die erstellt oder geändert werden, und der anschließenden Verwendung eines [Queue Storage-Triggers](./functions-bindings-storage-queue.md), um die Verarbeitung zu starten.

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Mit der [C#-Funktion](functions-dotnet-class-library.md) im folgenden Beispiel wird ein Protokoll geschrieben, wenn im Container `samples-workitems` ein Blob hinzugefügt oder aktualisiert wird.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zum Attribut `BlobTrigger` finden Sie unter [Attribute und Anmerkungen](#attributes-and-annotations).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer *function.json*-Datei sowie Code, der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im `samples-workitems`-[Container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) ein Blob hinzugefügt oder aktualisiert wird.

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

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#configuration), in dem diese Eigenschaften erläutert werden.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#configuration), in dem diese Eigenschaften erläutert werden.

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Blobtriggerbindung in einer Datei *function.json* und [Python-Code](functions-reference-python.md), der die Bindung verwendet. Die Funktion schreibt ein Protokoll, wenn im `samples-workitems`-[Container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) ein Blob hinzugefügt oder aktualisiert wird.

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

Die Zeichenfolge `{name}` im Blobtriggerpfad `samples-workitems/{name}` erstellt einen [Bindungsausdruck](./functions-bindings-expressions-patterns.md), den Sie im Funktionscode verwenden können, um auf den Dateinamen des auslösenden Blobs zuzugreifen. Weitere Informationen finden Sie unter [Blobnamensmuster](#blob-name-patterns) weiter unten in diesem Artikel.

Weitere Informationen zu den Dateieigenschaften von *function.json* finden Sie im Abschnitt [Konfiguration](#configuration), in dem diese Eigenschaften erläutert werden.

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Diese Funktion schreibt ein Protokoll, wenn im `myblob`-Container ein Blob hinzugefügt oder aktualisiert wird.

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

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Blobtrigger zu konfigurieren:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Der Konstruktor des Attributs akzeptiert eine Pfadzeichenfolge, die den zu überwachenden Container angibt, und optional ein [Blobnamensmuster](#blob-name-patterns). Hier sehen Sie ein Beispiel:

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

  Ein vollständiges Beispiel finden Sie unter [Triggerbeispiel](#example).

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

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Das `@BlobTrigger`-Attribut wird verwendet, um Ihnen Zugriff auf das Blob zu gewähren, das die Funktion ausgelöst hat. Weitere Detailinformationen finden Sie im [Triggerbeispiel](#example).

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `BlobTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blobTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. Ausnahmen sind im Abschnitt [Verwendung](#usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt. |
|**path** | **BlobPath** |Der zu überwachende [Container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).  Kann ein [Blobnamensmuster](#blob-name-patterns) sein. |
|**connection** | **Connection** | Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.<br><br>Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [Blobspeicherkonto](../storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie über `context.bindings.<NAME>` auf Blobdaten zu, wobei `<NAME>` mit dem in *function.json* definierten Wert übereinstimmt.

# <a name="python"></a>[Python](#tab/python)

Greifen Sie über den als [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python) typisierten Parameter auf Blob-Daten zu. Weitere Detailinformationen finden Sie im [Triggerbeispiel](#example).

# <a name="java"></a>[Java](#tab/java)

Das `@BlobTrigger`-Attribut wird verwendet, um Ihnen Zugriff auf das Blob zu gewähren, das die Funktion ausgelöst hat. Weitere Detailinformationen finden Sie im [Triggerbeispiel](#example).

---

## <a name="blob-name-patterns"></a>Blobnamensmuster

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

Wenn der Blobname *original-Blob1.txt* lautet, hat die Variable `name` im Funktionscode den Wert `Blob1.txt`.

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

## <a name="metadata"></a>Metadaten

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Metadaten sind in Python nicht verfügbar.

# <a name="java"></a>[Java](#tab/java)

Metadaten sind in Java nicht verfügbar.

---

## <a name="blob-receipts"></a>BLOB-Zugänge

Die Azure Functions-Runtime stellt sicher, dass Blobtriggerfunktionen für ein neues oder aktualisiertes Blob nicht mehrmals aufgerufen werden. Zu diesem Zweck wird mittels Verwaltung der *Blobbelege* ermittelt, ob eine bestimmte Blobversion verarbeitet wurde.

Azure Functions speichert Blobbelege in einem Container mit dem Namen *azure-webjobs-hosts* im Azure Storage-Konto für Ihre Funktions-App (per App-Einstellung `AzureWebJobsStorage` definiert). Ein Blobbeleg enthält die folgenden Informationen:

* Die ausgelöste Funktion („ *&lt;Funktions-App-Name>* .Functions. *&lt;Funktionsname>* “, z. B.: „MyFunctionApp.Functions.CopyBlob“)
* Containername
* Blobtyp ("BlockBlob" oder "PageBlob")
* Blobname
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

Um eine erneute Verarbeitung eines Blobs zu erzwingen, können Sie den Blobbeleg für dieses Blob manuell aus dem Container *azure-webjobs-hosts* löschen. Auch wenn eine erneute Verarbeitung eventuell nicht sofort ausgeführt wird, erfolgt sie doch garantiert zu einem späteren Zeitpunkt. Für eine sofortige erneute Verarbeitung kann das *ScanInfo*-Blob in *azure-webjobs-hosts/blobscaninfo* aktualisiert werden. Alle Blobs mit einem Zeitstempel der letzten Änderung, der nach der `LatestScan`-Eigenschaft liegt, werden erneut überprüft.

## <a name="poison-blobs"></a>Nicht verarbeitbare Blobs

Wenn bei Ausführung einer Blobtriggerfunktion für ein Blob ein Fehler auftritt, versucht Azure Functions standardmäßig bis zu fünfmal, diese Funktion für ein bestimmtes Blob auszuführen.

Wenn bei allen fünf Versuchen Fehler auftreten, fügt Azure Functions der Storage-Warteschlange *webjobs-blobtrigger-poison* eine Nachricht hinzu. Die maximale Anzahl von Wiederholungen ist konfigurierbar. Für die Verarbeitung nicht verarbeitbarer Blobs und der dazugehörigen Nachrichtenwarteschlange wird die gleiche MaxDequeueCount-Einstellung verwendet. Die Warteschlangennachricht für nicht verarbeitbare Blobs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *&lt;Funktionen-App-Name>* .Functions. *&lt;Funktionsname>* )
* BlobType ("BlockBlob" oder "PageBlob")
* ContainerName
* BlobName
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

## <a name="concurrency-and-memory-usage"></a>Parallelität und Arbeitsspeichernutzung

Der Blobtrigger verwendet intern eine Warteschlange, sodass die maximal zulässige Anzahl gleichzeitiger Funktionsaufrufe durch die [Warteschlangenkonfiguration in „host.json“](functions-host-json.md#queues) gesteuert wird. Durch die Standardeinstellungen wird die Anzahl gleichzeitiger Aufrufe auf 24 beschränkt. Dieser Grenzwert gilt separat für jede Funktion, die einen Blobtrigger verwendet.

[Der Verbrauchstarif](functions-scale.md#how-the-consumption-and-premium-plans-work) beschränkt den Arbeitsspeicher einer Funktions-App auf einer VM auf 1,5 GB. Der Arbeitsspeicher wird von jeder parallel ausgeführten Funktionsinstanz und durch die Functions-Runtime selbst verwendet. Wenn eine durch einen Blob ausgelöste Funktion das gesamte Blob in den Arbeitsspeicher lädt, entspricht der maximal von dieser Funktion verwendete Arbeitsspeicher nur für Blobs dem 24 * der maximalen Blobgröße. Beispiel: Eine Funktions-App, die drei Funktionen mit Blobtrigger umfasst und die Standardeinstellungen verwendet, weist pro VM eine maximale Nebenläufigkeit von 3 * 24 = 72 Funktionsaufrufen auf.

JavaScript- und Java-Funktionen laden das gesamte Blob in den Arbeitsspeicher, C#-Funktionen zeigen dieses Verhalten bei einer Bindung an `string` oder `Byte[]`.

## <a name="next-steps"></a>Nächste Schritte

- [Lesen von Blob Storage-Daten, wenn eine Funktion ausgeführt wird](./functions-bindings-storage-blob-input.md)
- [Schreiben von Blob Storage-Daten aus einer Funktion](./functions-bindings-storage-blob-output.md)
