---
title: Singletons für Durable Functions – Azure
description: Es wird beschrieben, wie Sie Singletons in der Erweiterung „Durable Functions“ für Azure Functions verwenden.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c032ba046668310ff71d067d22a805fc6446667c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683800"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Orchestratoren in Durable Functions (Azure Functions)

Für Hintergrundaufträge müssen Sie häufig sicherstellen, dass nur jeweils eine Instanz eines bestimmten Orchestrators ausgeführt wird. Dies ist in [Durable Functions](durable-functions-overview.md) möglich, indem einem Orchestrator bei der Erstellung eine spezifische Instanz-ID zugewiesen wird.

## <a name="singleton-example"></a>Singleton-Beispiel

In den folgenden C#- und JavaScript-Beispielen wird eine HTTP-Triggerfunktion veranschaulicht, mit der eine Orchestrierung für einen Singleton-Hintergrundauftrag erstellt wird. Der Code stellt sicher, dass für eine angegebene Instanz-ID nur eine Instanz vorhanden ist.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

Die Datei „function.json“ sieht wie folgt aus:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Der JavaScript-Code sieht wie folgt aus:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Standardmäßig handelt es sich bei Instanz-IDs um zufällig generierte GUIDs. Aber in diesem Fall wird die Instanz-ID in den Routendaten von der URL übergeben. Der Code ruft [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) oder `getStatus` (JavaScript) auf, um zu überprüfen, ob bereits eine Instanz mit der angegebenen ID ausgeführt wird. Ist dies nicht der Fall, wird eine Instanz mit dieser ID erstellt.

> [!WARNING]
> Wenn Sie lokal in JavaScript entwickeln, müssen Sie die Umgebungsvariable `WEBSITE_HOSTNAME` auf `localhost:<port>` festlegen, z. B. `localhost:7071`, um Methoden mit `DurableOrchestrationClient` zu verwenden. Weitere Informationen zu dieser Anforderung finden Sie in der [Beschreibung des Problems auf GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> In diesem Beispiel gibt es eine potenzielle Racebedingung. Wenn zwei Instanzen von **HttpStartSingle** gleichzeitig ausgeführt werden, sind beide Funktionsaufrufe erfolgreich, aber nur eine Orchestrierungsinstanz wird gestartet. Je nach Ihren Anforderungen kann dies unerwünschte Nebenwirkungen haben. Aus diesem Grund muss sichergestellt werden, dass diese Triggerfunktion nicht von zwei Anforderungen gleichzeitig ausgeführt werden kann.

Die Implementierungsdetails der Orchestratorfunktion sind hier nicht so wichtig. Es kann eine reguläre Orchestratorfunktion sein, die gestartet und abgeschlossen wird, oder es kann eine Orchestratorfunktion mit unendlicher Ausführung sein (also eine [endlose Orchestrierung](durable-functions-eternal-orchestrations.md)). Der wichtigste Punkt hierbei ist, dass nur jeweils eine Instanz ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Aufrufen von untergeordneten Orchestrierungen](durable-functions-sub-orchestrations.md)
