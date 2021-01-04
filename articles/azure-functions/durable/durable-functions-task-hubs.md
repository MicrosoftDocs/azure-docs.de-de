---
title: Aufgabenhubs in Durable Functions – Azure
description: Sie erfahren, was ein Aufgabenhub in der Erweiterung Durable Functions für Azure Functions ist. Informationen zum Konfigurieren von Aufgabenhubs.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009516"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Aufgabenhubs in Durable Functions (Azure Functions)

Ein *Aufgabenhub* in [Durable Functions](durable-functions-overview.md) ist ein logischer Container für Azure Storage-Ressourcen, die zur Orchestrierung verwendet werden. Orchestrator- und Aktivitätsfunktionen können nur miteinander interagieren, wenn sie zum selben Aufgabenhub gehören.

Wenn sich mehrere Funktions-Apps ein Speicherkonto teilen, *muss* jede Funktions-App mit einem separaten Aufgabenhubnamen konfiguriert werden. Ein Speicherkonto kann mehrere Aufgabenhubs enthalten. Das folgende Diagramm veranschaulicht einen Aufgabenhub pro Funktions-App in freigegebenen und dedizierten Speicherkonten.

![Diagramm mit freigegebenen und dedizierten Speicherkonten](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-Ressourcen

Ein Aufgabenhub umfasst folgende Speicherressourcen:

* Mindestens eine Steuerwarteschlange.
* Eine Arbeitselement-Warteschlange.
* Eine Verlaufstabelle.
* Eine Instanzentabelle.
* Einen Speichercontainer, der mindestens ein Lease-Blob enthält.
* Ein Speichercontainer, der große Nachrichtennutzlasten enthält, falls zutreffend.

Alle diese Ressourcen werden automatisch im standardmäßigen Azure Storage-Konto erstellt, wenn Orchestrator-, Entitäts- oder Aktivitätsfunktionen ausgeführt werden oder ihre Ausführung geplant wird. Im Artikel [Performance and Scale](durable-functions-perf-and-scale.md) (Leistung und Skalierung) wird erläutert, wie diese Ressourcen verwendet werden.

## <a name="task-hub-names"></a>Aufgabenhubnamen

Aufgabenhubs werden anhand eines Namens identifiziert, der den folgenden Regeln entspricht:

* Enthält ausschließlich alphanumerische Zeichen.
* Beginnen mit einem Buchstaben.
* Ist mindestens 3 Zeichen und höchstens 45 Zeichen lang.

Der Name des Aufgabenhubs wird in der Datei *host.json* deklariert, wie im folgenden Beispiel gezeigt:

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Aufgabenhubs können auch mithilfe von App-Einstellungen konfiguriert werden, wie in der folgenden Beispieldatei `host.json` gezeigt:

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Der Name des Aufgabenhubs wird auf den Wert der App-Einstellung `MyTaskHub` festgelegt. Die folgende Datei `local.settings.json` veranschaulicht, wie die Einstellungen für `MyTaskHub` als `samplehubname` definiert werden:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Der folgende Code veranschaulicht das Schreiben einer Funktion, die eine [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) verwendet, um mit einem als App-Einstellung konfigurierten Aufgabenhub zusammenzuarbeiten:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Das vorherige C#-Beispiel gilt für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Aufgabenhubeigenschaft in der Datei `function.json` wird über eine App-Einstellung festgelegt:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

Die Aufgabenhubeigenschaft in der Datei `function.json` wird über eine App-Einstellung festgelegt:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Aufgabenhubnamen müssen mit einem Buchstaben beginnen und bestehen nur aus Buchstaben und Ziffern. Wenn kein Name angegeben ist, wird ein Aufgabenhub-Standardname verwendet, wie in der folgenden Tabelle gezeigt:

| Version der Durable-Erweiterung | Aufgabenhub-Standardname |
| - | - |
| 2.x | Bei der Bereitstellung in Azure wird der Aufgabenhubname vom Namen der _Funktions-App_ abgeleitet. Bei der Ausführung außerhalb von Azure lautet der Standardname des Aufgabenhubs `TestHubName`. |
| 1.x | Der Standardname des Aufgabenhubs für alle Umgebungen lautet `DurableFunctionsHub`. |

Weitere Informationen zu den Unterschieden zwischen den Erweiterungsversionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

> [!NOTE]
> Die einzelnen Aufgabenhubs werden über den Namen unterschieden, wenn in einem freigegebenen Speicherkonto mehrere Aufgabenhubs vorhanden sind. Wenn sich mehrere Funktions-Apps ein freigegebenes Speicherkonto teilen, müssen Sie für die einzelnen Aufgabenhubs in der Datei *host.json* explizit unterschiedliche Namen konfigurieren. Andernfalls konkurrieren die verschiedenen Funktions-Apps miteinander um Nachrichten, was zu undefiniertem Verhalten führen kann, einschließlich Orchestrierungen, die unerwartet im Zustand `Pending` oder `Running` „stecken bleiben“.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zur Verarbeitung der Orchestrierungsversionsverwaltung](durable-functions-versioning.md)
