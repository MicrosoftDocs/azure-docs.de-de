---
title: Aufgabenhubs in Durable Functions – Azure
description: Sie erfahren, was ein Aufgabenhub in der Erweiterung Durable Functions für Azure Functions ist. Informationen zum Konfigurieren von Aufgabenhubs.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730265"
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

Alle diese Ressourcen werden automatisch im standardmäßigen Azure Storage-Konto erstellt, wenn Orchestrator- oder Aktivitätsfunktionen ausgeführt werden oder ihre Ausführung geplant wird. Im Artikel [Performance and Scale](durable-functions-perf-and-scale.md) (Leistung und Skalierung) wird erläutert, wie diese Ressourcen verwendet werden.

## <a name="task-hub-names"></a>Aufgabenhubnamen

Aufgabenhubs werden über den Namen identifiziert, der in der Datei *host.json* deklariert ist, wie im folgenden Beispiel gezeigt:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

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

Aufgabenhubs können auch mithilfe von App-Einstellungen konfiguriert werden, wie in der folgenden Beispieldatei *host.json* gezeigt:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

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

Hier sehen Sie ein vorkompiliertes C#-Beispiel zum Schreiben einer Funktion, die eine [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) verwendet, um mit einem als App-Einstellung konfigurierten Aufgabenhub zu arbeiten:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Das folgende Beispiel zeigt die erforderliche Konfiguration für JavaScript. Die Aufgabenhubeigenschaft in der Datei `function.json` wird über eine App-Einstellung festgelegt:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Aufgabenhubnamen müssen mit einem Buchstaben beginnen und bestehen nur aus Buchstaben und Ziffern. Wenn kein Name angegeben ist, wird der Standardname **DurableFunctionsHub** verwendet.

> [!NOTE]
> Die einzelnen Aufgabenhubs werden über den Namen unterschieden, wenn in einem freigegebenen Speicherkonto mehrere Aufgabenhubs vorhanden sind. Wenn sich mehrere Funktions-Apps ein freigegebenes Speicherkonto teilen, müssen Sie für die einzelnen Aufgabenhubs in der Datei *host.json* explizit unterschiedliche Namen konfigurieren. Andernfalls konkurrieren die verschiedenen Funktions-Apps miteinander um Nachrichten, was zu einem undefinierten Verhalten führen kann.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Versioning in Durable Functions (Azure Functions)](durable-functions-versioning.md) (Versionsverwaltung in Durable Functions [Azure Functions])
