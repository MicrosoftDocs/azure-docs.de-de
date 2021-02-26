---
title: Behandeln von externen Ereignissen in Durable Functions – Azure
description: Es wird beschrieben, wie Sie externe Ereignisse in der Erweiterung Durable Functions für Azure Functions behandeln.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: c08306edcea02a9207ab5a15eb62b7fffc2ecb44
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576328"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Behandeln von externen Ereignissen in Durable Functions (Azure Functions)

Orchestratorfunktionen können warten und auf externe Ereignisse lauschen. Das Feature [Durable Functions](durable-functions-overview.md) ist oft hilfreich für die Behandlung menschlicher Interaktion oder anderer externer Trigger.

> [!NOTE]
> Externe Ereignisse sind unidirektionale, asynchrone Vorgänge. Sie sind nicht geeignet für Situationen, in denen der Client, der das Ereignis sendet, eine synchrone Antwort von der Orchestratorfunktion benötigt.

## <a name="wait-for-events"></a>Warten auf Ereignisse

Mit den Methoden [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy) (.NET), `waitForExternalEvent` (JavaScript) und `wait_for_external_event` (Python) der [Orchestrierungstriggerbindung](durable-functions-bindings.md#orchestration-trigger) kann eine Orchestratorfunktion asynchron auf ein externes Ereignis warten und lauschen. Die lauschende Orchestratorfunktion deklariert den *Namen* des Ereignisses und die *Form der Daten*, die sie zu empfangen erwartet.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

Im vorangehenden Beispiel wird auf ein bestimmtes einzelnes Ereignis gelauscht und bei Empfang eine entsprechende Aktion ausgeführt.

Sie können wie im folgenden Beispiel, in dem auf eine von drei möglichen Ereignisbenachrichtigungen gewartet wird, auf mehrere Ereignisse gleichzeitig lauschen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

Im vorherigen Beispiel wird auf *alle* von mehreren Ereignissen gelauscht. Es ist auch möglich, auf *alle* Ereignisse zu warten.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

Wenn in .NET die Ereignisnutzlast nicht in den erwarteten Typ `T` konvertiert werden kann, wird eine Ausnahme ausgelöst.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` wartet unbegrenzt auf Eingaben.  Die Funktionen-App kann während des Wartens problemlos entladen werden. Wenn ein Ereignis für diese Orchestrierungsinstanz eingeht, wird sie automatisch aktiviert und verarbeitet das Ereignis sofort.

> [!NOTE]
> Wenn Ihre Funktions-App den Verbrauchstarif verwendet, fallen keine Kosten an, während eine Orchestratorfunktion auf einen Task von `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript) oder `wait_for_external_event` (Python) wartet, und zwar unabhängig davon, wie lange sie wartet.

## <a name="send-events"></a>Senden von Ereignisse

Mit der Methode [RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy) (.NET) oder `raiseEventAsync` (JavaScript) kann ein externes Ereignis an eine Orchestrierung gesendet werden. Diese Methoden werden durch die Bindung [Orchestrierungsclient](durable-functions-bindings.md#orchestration-client) verfügbar gemacht. Sie können auch die integrierte [HTTP-API zum Auslösen eines Ereignisses](durable-functions-http-api.md#raise-event) verwenden, um ein externes Ereignis an eine Orchestrierung zu senden.

Ein ausgelöstes Ereignis enthält eine *Instanz-ID*, einen Ereignisnamen (*eventName*) und Ereignisdaten (*eventData*) als Parameter. Diese Ereignisse werden von Orchestratorfunktionen mithilfe der API `WaitForExternalEvent` (.NET) oder `waitForExternalEvent` (JavaScript) behandelt. Der Ereignisname (*eventName*) muss sowohl auf der Sende- als auch auf der Empfangsseite übereinstimmen, damit das Ereignis verarbeitet wird. Die Ereignisdaten müssen außerdem JSON-serialisierbar sein.

Intern wird durch die Mechanismen von „raise event“ eine Nachricht in die Warteschlange eingereiht, die von der wartenden Orchestratorfunktion aufgegriffen wird. Falls die Instanz nicht auf den angegebenen *Ereignisnamen* wartet, wird die Ereignisnachricht einer In-Memory-Warteschlange hinzugefügt. Wenn die Orchestrierungsinstanz später auf den *Ereignisnamen* lauscht, überprüft sie die Warteschlange auf Ereignisnachrichten.

> [!NOTE]
> Ist keine Orchestrierungsinstanz mit der angegebenen *Instanz-ID* vorhanden, wird die Ereignisnachricht verworfen.

Im Folgenden finden Sie eine durch eine Warteschlange ausgelöste Beispielfunktion, die ein „Genehmigungs“-Ereignis an eine Orchestratorfunktionsinstanz sendet. Die Orchestrierungsinstanz-ID stammt aus dem Text der Warteschlangennachricht.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Intern fügt `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) oder `raise_event` (Python) eine Nachricht in die Warteschlange ein, die von der wartenden Orchestratorfunktion erfasst wird. Falls die Instanz nicht auf den angegebenen *Ereignisnamen* wartet, wird die Ereignisnachricht einer In-Memory-Warteschlange hinzugefügt. Wenn die Orchestrierungsinstanz später auf den *Ereignisnamen* lauscht, überprüft sie die Warteschlange auf Ereignisnachrichten.

> [!NOTE]
> Ist keine Orchestrierungsinstanz mit der angegebenen *Instanz-ID* vorhanden, wird die Ereignisnachricht verworfen.

### <a name="http"></a>HTTP

Im Anschluss finden Sie ein Beispiel für eine HTTP-Anforderung, durch die ein Genehmigungsereignis für eine Orchestrierungsinstanz ausgelöst wird. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

In diesem Fall ist die Instanz-ID als *MyInstanceId* hartcodiert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Fehlerbehandlung implementiert wird](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Human interaction in Durable Functions - Phone verification sample](durable-functions-phone-verification.md) (Menschliche Interaktion in Durable Functions – Telefonüberprüfungsbeispiel)