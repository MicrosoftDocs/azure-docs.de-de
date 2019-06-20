---
title: Muster und technische Konzepte von Durable Functions in Azure Functions
description: Erfahren Sie, wie Sie mit der Durable Functions-Erweiterung in Azure Functions die Vorzüge zustandsbehafteter Codeausführung in der Cloud nutzen können.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 95ec6a863f951a8c26abd865041c68df333a4e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071345"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Muster und technische Konzepte von Durable Functions (Azure Functions)

Durable Functions ist eine Erweiterung von [Azure Functions](../functions-overview.md) und [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Mithilfe von Durable Functions können Sie zustandsbehaftete Funktionen in einer serverlosen Umgebung schreiben. Die Erweiterung verwaltet Status, Prüfpunkte und Neustarts für Sie. 

Dieser Artikel bietet Ihnen ausführliche Informationen über das Verhalten der Erweiterung Durable Functions für Azure Functions und häufige Implementierungsmuster. Diese Informationen unterstützen Sie dabei, zu bestimmen, wie Sie Durable Functions zur Lösung von Entwicklungsproblemen verwenden können.

> [!NOTE]
> Durable Functions ist eine fortgeschrittene Erweiterung für Azure Functions und nicht für alle Anwendungen geeignet. In diesem Artikel wird vorausgesetzt, dass Sie mit den Konzepten von [Azure Functions](../functions-overview.md) und den Herausforderungen serverloser Anwendungsentwicklung bestens vertraut sind.

## <a name="patterns"></a>Muster

In diesem Abschnitt werden einige typische Anwendungsmuster beschrieben, die von Durable Functions profitieren können.

### <a name="chaining"></a>Muster 1: Funktionsverkettung

Beim Muster der Funktionsverkettung wird eine Abfolge von Funktionen in einer bestimmten Reihenfolge ausgeführt. Bei diesem Muster wird die Ausgabe einer Funktion als Eingabe einer weiteren Funktion verwendet.

![Ein Diagramm des Funktionsverkettungsmusters](./media/durable-functions-concepts/function-chaining.png)

Mithilfe von Durable Functions können Sie das Funktionsverkettungsmuster präzise wie im folgenden Beispiel dargestellt implementieren:

#### <a name="c-script"></a>C#-Skript

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Es gibt feine Unterschiede zwischen dem Schreiben einer vorkompilierten permanenten Funktion in C# und dem Schreiben einer vorkompilierten permanenten Funktion in dem C#-Skript, das in diesem Beispiel gezeigt wird. In einer vorkompilierten C#-Funktion müssen permanente Parameter mit den jeweiligen Attributen versehen werden. Ein Beispiel ist das `[OrchestrationTrigger]`-Attribut für den `DurableOrchestrationContext`-Parameter. Wenn in einer vorkompilierten permanenten C#-Funktion die Parameter nicht ordnungsgemäß mit Attributen versehen sind, kann die Runtime die Variablen nicht in die Funktion einfügen, und ein Fehler tritt auf. Weitere Beispiele finden Sie in den [Azure-Functions-Durable-Extension-Beispielen auf GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

In diesem Beispiel sind die Werte `F1`, `F2`, `F3` und `F4` die Namen weiterer Funktionen in der Funktions-App. Sie können die Ablaufsteuerung mithilfe normaler imperativer Codierungskonstrukte implementieren. Der Code wird von oben nach unten ausgeführt. Der Code kann bestehende sprachliche Ablaufsteuerungssemantik wie Bedingungsanweisungen und Schleifen umfassen. Sie können Logik zur Fehlerbehandlung in `try`/`catch`/`finally`-Blöcken einschließen.

Sie können den `context`-Parameter [DurableOrchestrationContext] \(.NET\) und das `context.df`-Objekt (JavaScript) verwenden, um andere Funktionen anhand des Namens aufzurufen, Parameter zu übergeben und Funktionsausgaben zurückzugeben. Jedes Mal, wenn der Code `await` (C#) oder `yield` (JavaScript) aufruft, erstellt das Durable Functions Framework Prüfpunkte zum Status der aktuellen Funktionsinstanz. Wenn der Prozess oder die VM mitten in der Ausführung neu gestartet wird, wird die Funktionsinstanz ab dem vorherigen `await`- bzw. `yield`-Aufruf fortgesetzt. Weitere Informationen finden Sie im nächsten Abschnitt, Muster 2: Auffächern auswärts/einwärts.

> [!NOTE]
> Das `context`-Objekt in JavaScript stellt gesamten den [Funktionskontext](../functions-reference-node.md#context-object) dar, nicht nur den [DurableOrchestrationContext]-Parameter.

### <a name="fan-in-out"></a>Muster 2: Auffächern auswärts/einwärts

Beim Muster Auffächern auswärts/einwärts werden mehrere Funktionen parallel ausgeführt und anschließend auf den Abschluss aller gewartet. Häufig werden die von den Funktionen zurückgegebenen Ergebnisse einer Aggregation unterzogen.

![Ein Diagramm des Musters „Auffächern auswärts/einwärts“](./media/durable-functions-concepts/fan-out-fan-in.png)

Bei normalen Funktionen kann das Auffächern auswärts erfolgen, indem die Funktion mehrere Nachrichten an eine Warteschlange sendet. Das Auffächern zurück nach innen ist wesentlich schwieriger. Für das Auffächern nach innen schreiben Sie in einer normalen Funktion Code, der nachverfolgt, wann die von der Warteschlange ausgelösten Funktionen enden und speichern dann ihre Ausgaben. 

Die Erweiterung Durable Functions wird diesem Muster mit relativ einfachem Code gerecht:

#### <a name="c-script"></a>C#-Skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Das Auffächern nach außen ist auf mehrere Instanzen der `F2`-Funktion verteilt. Die Arbeit wird mithilfe einer dynamischen Aufgabenliste nachverfolgt. Die .NET-API `Task.WhenAll` oder die JavaScript-API `context.df.Task.all` wird aufgerufen, um auf den Abschluss der aufgerufenen Funktionen zu warten. Anschließend werden die Ausgaben der `F2`-Funktion aus der dynamischen Aufgabenliste aggregiert und an die `F3`-Funktion übergeben.

Die automatische Prüfpunkterstellung, die beim `await`- oder `yield`-Aufruf für `Task.WhenAll` bzw. `context.df.Task.all` erfolgt, stellt sicher, dass ein potentieller Absturz oder Neustart während der Ausführung keinen Neustart bereits abgeschlossener Aufgaben erfordert.

### <a name="async-http"></a>Muster 3: Asynchrone HTTP-APIs

Das Muster „asynchrone HTTP-APIs“ eignet sich für das Problem, den Status von Vorgängen mit langer Ausführungsdauer mit externen Clients zu koordinieren. Ein gängiges Verfahren zum Implementieren dieses Musters besteht darin, die Vorgänge mit langer Ausführungsdauer von einem HTTP-Aufruf auslösen zu lassen. Leiten Sie dann den Client zu einem Statusendpunkt um, den der Client abfragt, um herauszufinden, wenn der Vorgang abgeschlossen ist.

![Ein Diagramm des HTTP-API-Musters](./media/durable-functions-concepts/async-http-api.png)

Durable Functions bietet integrierte APIs, die den Code vereinfachen, den Sie für die Interaktion mit langen Funktionsausführungen schreiben. Die Schnellstartbeispiele für Durable Functions ([C#](durable-functions-create-first-csharp.md) und [JavaScript](quickstart-js-vscode.md)) zeigen einen einfachen REST-Befehl, den Sie zum Starten neuer Orchestratorfunktionsinstanzen verwenden können. Nach dem Start einer Instanz macht die Erweiterung Webhook-HTTP-APIs verfügbar, die den Status der Orchestratorfunktion abfragen. 

Das folgende Beispiel zeigt REST-Befehle zum Starten eines Orchestrators und zum Abfragen seines Status. Zur besseren Übersichtlichkeit werden im Beispiel einige Details weggelassen.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Da der Status von der Durable Functions-Laufzeit verwaltet wird, müssen Sie keinen eigenen Mechanismus zur Statusnachverfolgung implementieren.

Die Durable Functions-Erweiterung verfügt über integrierte Webhooks, die Orchestrierungen mit langer Ausführungsdauer verwalten. Sie können dieses Muster selbst implementieren, indem Sie Ihre eigenen Funktionstrigger (wie etwa HTTP, eine Warteschlange oder Azure Event Hubs) und die `orchestrationClient`-Bindung verwenden. Sie können beispielsweise eine Warteschlangennachricht verwenden, um die Beendigung auszulösen. Sie könnten auch anstelle der integrierten Webhooks, die einen generierten Schlüssel für die Authentifizierung verwenden, einen HTTP-Trigger verwenden, der durch eine Azure Active Directory-Authentifizierungsrichtlinie geschützt ist.

Hier finden Sie einige Beispiele zur Verwendung des HTTP-API-Musters:

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

In .NET ist der `starter`-Parameter von [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) ein Wert aus der `orchestrationClient`-Ausgabebindung, die Teil der Durable Functions-Erweiterung ist. In JavaScript wird dieses Objekt durch Aufrufen von `df.getClient(context)` zurückgegeben. Diese Objekte stellen Methoden bereit, mit denen neue oder vorhandene Orchestratorfunktionsinstanzen gestartet, beendet und abgefragt sowie Ereignisse an sie gesendet werden können.

In den vorherigen Beispielen nimmt eine über HTTP ausgelöste Funktion einen `functionName`-Wert von der eingehenden URL entgegen und übergibt den Wert an [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Die Bindungs-API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) gibt dann eine Antwort zurück, die einen `Location`-Header und zusätzliche Informationen zur Instanz enthält. Sie können die Informationen später verwenden, um den Status der gestarteten Instanz abzurufen oder sie zu beenden.

### <a name="monitoring"></a>Muster 4: Überwachen

Das Überwachen-Muster bezieht sich auf einen flexiblen, wiederkehrenden Vorgang in einem Workflow. Ein Beispiel besteht im Abfragen, bis bestimmte Bedingungen erfüllt sind. Sie können einen normalen [Timertrigger](../functions-bindings-timer.md) für ein einfaches Szenario verwenden, beispielsweise einen periodischen Bereinigungsauftrag. Sein Intervall ist jedoch statisch, und die Verwaltung der Instanzlebensdauer wird komplex. Mithilfe von Durable Functions können Sie flexible Wiederholungsintervalle erstellen, die Lebensdauer von Aufgaben verwalten und mehrere Überwachungsprozesse aus einer einzelnen Orchestrierung erstellen.

Ein Beispiel für das Überwachen-Muster besteht in der Umkehrung des früheren asynchronen HTTP-API-Szenarios. Anstatt einen Endpunkt für einen externen Client freizugeben, um einen lang laufenden Vorgang zu überwachen, belegt der lang laufende Monitor einen externen Endpunkt und wartet dann auf einen Zustandswechsel.

![Ein Diagramm des Überwachen-Musters](./media/durable-functions-concepts/monitor.png)

Mit ein paar Codezeilen können Sie Durable Functions dazu verwenden, mehrere Monitore zu erstellen, die beliebige Endpunkte beobachten. Die Monitore können die Ausführung beenden, wenn eine Bedingung erfüllt ist, oder der [DurableOrchestrationClient](durable-functions-instance-management.md) kann die Monitore beenden. Sie können das `wait`-Intervall eines Monitors auf der Grundlage einer spezifischen Bedingung (z.B. exponentielles Backoff) ändern. 

Der folgende Code implementiert einen einfachen Monitor:

#### <a name="c-script"></a>C#-Skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Wenn eine Anforderung empfangen wird, wird eine neue Orchestrierungsinstanz für diese Auftrags-ID erstellt. Die Instanz fragt den Status ab, bis eine Bedingung erfüllt ist und die Schleife beendet wird. Ein permanenter Timer steuert das Abrufintervall. Anschließend können weitere Arbeitsschritte ausgeführt werden, oder die Orchestrierung wird beendet. Wenn `context.CurrentUtcDateTime` (.NET) oder `context.df.currentUtcDateTime` (JavaScript) den Wert von `expiryTime` überschreitet, wird der Monitor beendet.

### <a name="human"></a>Muster 5: Benutzerinteraktion

Viele automatisierte Prozesse enthalten eine Form der Benutzerinteraktion. Das Einbeziehen von Menschen in einen automatisierten Prozess ist schwierig, da Personen nicht im gleichen hohen Maß verfügbar und reaktionsfähig sind wie Clouddienste. Ein automatisierter Prozess kann dem mithilfe von Timeouts und Kompensationscode Rechnung tragen.

Ein Genehmigungsprozess ist ein Beispiel für einen Geschäftsprozesses, der Benutzerinteraktion umfasst. Beispielsweise kann für eine Spesenabrechnung, die einen bestimmten Betrag überschreitet, die Genehmigung eines Vorgesetzten erforderlich sein. Wenn der Vorgesetzte die Spesenabrechnung nicht innerhalb von 72 Stunden genehmigt (vielleicht weil er im Urlaub ist), wird ein Eskalationsverfahren wirksam, um die Genehmigung von einer anderen Person (z.B. dem Vorgesetzten des Vorgesetzten) zu erhalten.

![Ein Diagramm des Musters „Benutzerinteraktion“](./media/durable-functions-concepts/approval.png)

Sie können das Muster aus diesem Beispiel mithilfe einer Orchestrierungsfunktion implementieren. Der Orchestrator verwendet einen [permanenten Timer](durable-functions-timers.md), um die Genehmigung anzufordern. Der Orchestrator führt die Eskalation aus, wenn das Timeout auftritt. Der Orchestrator wartet auf ein [externes Ereignis](durable-functions-external-events.md), beispielsweise eine Benachrichtigung, die durch eine Benutzerinteraktion generiert wird.

In diesen Beispielen wird ein Genehmigungsprozess erstellt, um das Muster der Benutzerinteraktion zu veranschaulichen:

#### <a name="c-script"></a>C#-Skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Rufen Sie zum Erstellen des permanenten Timers `context.CreateTimer` (.NET) oder `context.df.createTimer` (JavaScript) auf. Die Benachrichtigung wird von `context.WaitForExternalEvent` (.NET) oder `context.df.waitForExternalEvent` (JavaScript) empfangen. Anschließend wird `Task.WhenAny` (.NET) oder `context.df.Task.any` (JavaScript) aufgerufen, um zu entscheiden, ob eine Eskalation erfolgt (das Timeout tritt zuerst auf) oder die Genehmigung verarbeitet wird (die Genehmigung wird vor dem Timeout empfangen).

Ein externer Client kann die Ereignisbenachrichtigung entweder über die [integrierten HTTP-APIs](durable-functions-http-api.md#raise-event) oder die [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_)-API einer anderen Funktion an eine wartende Orchestratorfunktion senden:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="pattern-6-aggregator-preview"></a>Muster 6: Aggregator (Vorschauversion)

Beim sechsten Muster geht es um Aggregierung von Ereignisdaten über einen bestimmten Zeitraum in einer einzigen, adressierbaren *Entität*. In diesem Muster können die aggregierten Daten aus mehreren Quellen stammen, in Batches geliefert werden und über lange Zeiträume verteilt sein. Der Aggregator muss möglicherweise Aktionen für Ereignisdaten durchführen, wenn er diese empfängt, und es kann sein, dass externe Daten die aggregierten Daten abfragen müssen.

![Aggregatordiagramm](./media/durable-functions-concepts/aggregator.png)

Das Schwierige an der Implementierung dieses Musters mit normalen, zustandslosen Funktionen ist die Tatsache, dass das Steuern der Parallelität zur Herausforderung wird. Sie müssen sich nicht nur um mehrere Threads kümmern, die gleichzeitig dieselben Daten anpassen, sondern Sie müssen auch sicherstellen, dass der Aggregator immer nur auf einer VM ausgeführt wird.

Wenn Sie eine [Durable Entity-Funktion](durable-functions-preview.md#entity-functions) verwenden, können Sie dieses Muster ohne Probleme als einzelne Funktion implementieren.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityClassName = "Counter")] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Clients können *Vorgänge* mit der`orchestrationClient`-Bindung für eine Entity-Funktion in eine Warteschlange einreihen.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

So ähnlich können Clients den Status einer Entity-Funktion mit Methoden in der `orchestrationClient`-Bindung abfragen.

> [!NOTE]
> Entity-Funktionen sind momentan nur in der [Vorschauversion von Durable Functions 2.0](durable-functions-preview.md) verfügbar.

## <a name="the-technology"></a>Die Technologie

Im Hintergrund baut die Erweiterung Durable Functions auf dem [Durable Task Framework](https://github.com/Azure/durabletask) auf, einer Open Source-Bibliothek auf GitHub, die zum Erstellen von permanenten Aufgabenorchestrierungen verwendet wird. Wie Azure Functions die serverlose Weiterentwicklung von Azure WebJobs ist, so ist Durable Functions die serverlose Weiterentwicklung von Durable Task Framework. Microsoft und andere Unternehmen verwenden das Durable Task Framework intensiv zum Automatisieren unternehmenswichtiger Prozesse. Es ist wie geschaffen für die serverlose Azure Functions-Umgebung.

### <a name="event-sourcing-checkpointing-and-replay"></a>Ereignisherkunftsermittlung, Prüfpunkterstellung und Wiedergabe

Orchestratorfunktionen verwalten ihren Ausführungsstatus zuverlässig mithilfe eines als [Ereignissourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) bezeichneten Entwurfsmusters. Anstatt den aktuellen Status einer Orchestrierung direkt zu speichern, verwendet die Erweiterung Durable Functions einen ausschließlich zum Anfügen bestimmten Speicher, um die vollständige Reihe der Aktionen aufzuzeichnen, die von der Funktionsorchestrierung durchgeführt werden. Ein reiner Anfügespeicher bietet viele Vorteile im Vergleich mit dem „Abladen“ des gesamten Laufzeitstatus. Zu den Vorteilen zählen die gesteigerte Leistung, Skalierbarkeit und Reaktionsfähigkeit. Sie erhalten außerdem endgültige Konsistenz von Transaktionsdaten und vollständige Überwachungspfade sowie vollständigen Verlauf. Die Überwachungspfade unterstützen zuverlässige kompensierende Aktionen.

Durable Functions verwendet Ereignissourcing transparent. Im Hintergrund gibt der `await`-Operator (C#) oder `yield`-Operator (JavaScript) in einer Orchestratorfunktion die Steuerung des Orchestratorthreads an den Durable Task Framework-Verteiler zurück. Der Verteiler committet dann alle neuen Aktionen, die die Orchestratorfunktion geplant hat (z.B. Aufrufen mindestens einer untergeordneten Funktion oder Planen eines permanenten Timers) in den Speicher. Dieser transparente Commitvorgang wird dem Ausführungsverlauf der Orchestrierungsinstanz angefügt. Der Verlauf wird in einer Speichertabelle gespeichert. Dann fügt die Commitaktion Nachrichten an eine Warteschlange an, um die eigentliche Arbeit zu planen. An diesem Punkt kann die Orchestratorfunktion aus dem Arbeitsspeicher entladen werden. 

Die Abrechnung für die Orchestratorfunktion wird beendet, wenn Sie den Verbrauchstarif für Azure Functions verwenden. Wenn weitere Aufgaben bewältigt werden müssen, wird die Funktion neu gestartet und ihr Status wiederhergestellt.

Wenn eine Orchestrierungsfunktion weitere Aufgaben ausführen muss (z.B. wird eine Antwortnachricht empfangen, oder ein permanenter Timer läuft ab), wird der Orchestrator reaktiviert und führt erneut die gesamte Funktion von Beginn an neu aus, um den lokalen Status wiederherzustellen. 

Wenn der Code während dieser Wiedergabe versucht, eine Funktion aufzurufen (oder eine andere asynchrone Aktion auszuführen), zieht Durable Task Framework den Ausführungsverlauf der aktuellen Orchestrierung zu Rate. Wenn festgestellt wird, dass die [Aktivitätsfunktion](durable-functions-types-features-overview.md#activity-functions) bereits ausgeführt wurde und ein Ergebnis erbracht hat, wird dieses Funktionsergebnis wiedergegeben und der Orchestratorcode weiter ausgeführt. Die Wiedergabe wird fortgesetzt, bis der Funktionscode abgeschlossen ist oder geplante neue asynchrone Arbeit ansteht.

### <a name="orchestrator-code-constraints"></a>Einschränkungen des Orchestratorcodes

Mit dem Wiedergabeverhalten von Orchestratorcode sind Einschränkungen des Codetyps verbunden, der in einer Orchestratorfunktion geschrieben werden kann. Orchestratorcode muss z.B. deterministisch sein, da er viele Male wiedergegeben wird und immer dasselbe Ergebnis produzieren muss. Eine vollständige Liste der Einschränkungen finden Sie unter [Einschränkungen des Orchestratorcodes](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Überwachung und Diagnose

Die Erweiterung Durable Functions gibt automatisch strukturierte Nachverfolgungsdaten an [Application Insights](../functions-monitoring.md) aus, wenn Sie Ihre Funktions-App mit einem Azure Application Insights-Instrumentierungsschlüssel eingerichtet haben. Sie können diese Nachverfolgungsdaten verwenden, um die Aktionen und den Status Ihrer Orchestrierungen zu überwachen.

Hier ist ein Beispiel für die Anzeige der Durable Functions-Nachverfolgungsereignisse im Application Insights-Portal, wenn [Application Insights – Analyse](../../application-insights/app-insights-analytics.md) verwendet wird:

![Application Insights-Abfrageergebnisse](./media/durable-functions-concepts/app-insights-1.png)

Sie finden nützliche strukturierte Daten im Feld `customDimensions` in jedem Protokolleintrag. Hier ist ein Beispiel für einen vollständig aufgeklappten Eintrag:

![Das customDimensions-Feld in einer Application Insights-Abfrage](./media/durable-functions-concepts/app-insights-2.png)

Aufgrund des Wiedergabeverhaltens des Durable Task Framework-Verteilers können Sie davon ausgehen, redundante Protokolleinträge für wiedergegebene Aktionen zu sehen. Redundante Protokolleinträge können Ihnen helfen, das Wiedergabeverhalten der Kern-Engine zu verstehen. Der Artikel [Diagnostics in Durable Functions (Azure Functions)](durable-functions-diagnostics.md) (Diagnose in Durable Functions [Azure Functions]) zeigt Beispiele für Abfragen, die Wiedergabeprotokolle ausfiltern, sodass Sie nur die „Echtzeitprotokolle“ sehen.

## <a name="storage-and-scalability"></a>Speicherung und Skalierbarkeit

Die Erweiterung Durable Functions verwendet Warteschlangen, Tabellen und Blobs in Azure Storage, um Ausführungsverlaufsstatus und Triggerfunktionsausführung persistent zu speichern. Sie können das Standardspeicherkonto für die Funktions-App nutzen oder ein separates Speicherkonto konfigurieren. Abhängig von den Grenzwerten für den Speicherdurchsatz sollten Sie ggf. ein separates Konto verwenden. Der Orchestratorcode, den Sie schreiben, interagiert nicht mit den Entitäten in diesen Speicherkonten. Das Durable Task Framework verwaltet die Entitäten direkt als Implementierungsdetail.

Orchestratorfunktionen planen Aktivitätsfunktionen und empfangen ihre Antworten über interne Warteschlangennachrichten. Wenn eine Funktions-App im Azure Functions-Verbrauchstarif ausgeführt wird, werden diese Warteschlangen vom [Azure Functions-Skalierungscontroller](../functions-scale.md#how-the-consumption-and-premium-plans-work) überwacht. Neue Compute-Instanzen werden nach Bedarf hinzugefügt. Beim horizontalen Hochskalieren über mehrere virtuelle Computer kann eine Orchestratorfunktion auf einem virtuellen Computer ausgeführt werden, während Aktivitätsfunktionen, die von der Orchestratorfunktion aufgerufen werden, auf mehreren anderen virtuellen Computern ausgeführt werden. Weitere Informationen über das Skalierungsverhalten von Durable Functions finden Sie unter [Performance and scale in Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md) (Leistung und Skalierung in Durable Functions [Azure Functions]).

Der Ausführungsverlauf für Orchestratorkonten wird in Tabellenspeicher gespeichert. Immer dann, wenn eine Instanz auf einem bestimmten virtuellen Computer aktiviert wird, ruft der Orchestrator ihren Ausführungsverlauf vom Tabellenspeicher ab, damit er ihren lokalen Zustand wiederherstellen kann. Ein komfortabler Aspekt des Vorliegens des Verlaufs in Tabellenspeicher liegt darin, dass Sie Tools wie [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) verwenden können, um den Verlauf Ihrer Orchestrierungen anzuzeigen.

Speicher-Blobs werden hauptsächlich als Leasingmechanismus zum Koordinieren der übergreifenden horizontalen Skalierung von Orchestrierungsinstanzen über mehrere VMs hinweg verwendet. Speicher-Blobs speichern Daten für umfangreiche Nachrichten, die nicht direkt in Tabellen oder Warteschlangen gespeichert werden können.

![Screenshot von Azure Storage-Explorer](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Es ist zwar einfach und bequem, den Ausführungsverlauf im Tabellenspeicher angezeigt zu bekommen, Sie sollten aber keine Abhängigkeiten von dieser Tabelle einrichten. Die Tabelle kann sich im Rahmen der Weiterentwicklung der Erweiterung Durable Functions ändern.

## <a name="known-issues"></a>Bekannte Probleme

Alle bekannten Probleme sollten in der Liste der [GitHub-Probleme](https://github.com/Azure/azure-functions-durable-extension/issues) nachverfolgt werden. Wenn Sie auf ein Problem stoßen, das in GitHub nicht zu finden ist, öffnen Sie ein neues Problem. Schließen Sie eine ausführliche Beschreibung des Problems ein.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Durable Functions finden Sie im Artikel zu [Funktionstypen und Features von Durable Functions](durable-functions-types-features-overview.md). 

Erste Schritte:

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Durable Function](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
