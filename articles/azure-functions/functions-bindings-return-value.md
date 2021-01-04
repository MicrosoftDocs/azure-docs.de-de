---
title: Verwendung des Rückgabewerts einer Azure-Funktion
description: Informationen zum Verwalten von Rückgabewerten für Azure Functions
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491357"
---
# <a name="using-the-azure-function-return-value"></a>Verwenden des Rückgabewerts einer Azure-Funktion

In diesem Artikel wird erläutert, wie Rückgabewerte innerhalb einer Funktion funktionieren.

In Sprachen mit Rückgabewert können Sie eine [Ausgabebindung](./functions-triggers-bindings.md#binding-direction) einer Funktion an den Rückgabewert binden:

* Wenden Sie in einer C#-Klassenbibliothek das Attribut der Ausgabebindung auf den Rückgabewert der Methode an.
* Wenden Sie in Java die Anmerkung der Ausgabebindung auf die Funktionsmethode an.
* In anderen Sprachen legen Sie die Eigenschaft `name` in *function.json* auf `$return` fest.

Wenn mehrere Ausgabebindungen vorhanden sind, verwenden Sie den Rückgabewert für nur eine davon.

In C# und C#-Skripts können Daten alternativ mithilfe von `out`-Parametern und [Collector-Objekten](functions-reference-csharp.md#writing-multiple-output-values) an eine Ausgabebindung gesendet werden.

# <a name="c"></a>[C#](#tab/csharp)

Hier sehen Sie C#-Code, der den Rückgabewert für eine Ausgabebindung gefolgt von einem asynchronen Beispiel verwendet:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Hier sehen Sie die Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier sehen Sie den C#-Skriptcode gefolgt von einem asynchronen Beispiel:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

Hier sehen Sie die Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Der F#-Code lautet wie folgt:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Hier sehen Sie die Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In JavaScript befindet sich der Rückgabewert im zweiten Parameter für `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Hier sehen Sie die Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

Hier sehen Sie den PowerShell-Code, der den Rückgabewert für eine HTTP-Ausgabebindung verwendet:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

Hier sehen Sie die Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Dies ist der Python-Code:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Hier sehen Sie Java-Code, der den Rückgabewert für eine Ausgabebindung verwendet:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Fehlerbehandlung bei der Azure Functions-Bindung](./functions-bindings-errors.md)
