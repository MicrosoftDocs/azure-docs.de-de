---
title: Funktionstypen und -features in der Durable Functions-Erweiterung von Azure Functions
description: Machen Sie sich mit Funktionstypen und -rollen vertraut, die in einer Durable Functions-Orchestrierung in Azure Functions die direkte Kommunikation zwischen Funktionen ermöglicht.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731113"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions-Typen und -Features (Azure Functions)

Durable Functions ist eine Erweiterung von [Azure Functions](../functions-overview.md). Sie eignet sich für die zustandsbehaftete Orchestrierung der Funktionsausführung. Eine permanente Funktion stellt eine Lösung dar, die sich aus verschiedenen Azure-Funktionen zusammensetzt. Funktionen können in einer Durable Functions-Orchestrierung unterschiedliche Rollen spielen. 

Dieser Artikel stellt eine Übersicht über die Typen von Funktionen bereit, die Sie in einer Durable Functions-Orchestrierung verwenden können. Außerdem werden häufig verwendete Muster beschrieben, mit denen Sie Funktionen verbinden können. Erfahren Sie, wie Sie mit Durable Functions Probleme bei der App-Entwicklung lösen können.

![Bild: Typen permanenter Funktionen][1]  

## <a name="types-of-durable-functions"></a>Typen von permanenten Funktionen

In Azure Functions können Sie drei Typen permanenter Funktionen verwenden: Aktivitäts-, Orchestrator- und Clientfunktionen.

### <a name="activity-functions"></a>Aktivitätsfunktionen

Aktivitätsfunktionen sind die grundlegende Arbeitseinheit in der Orchestrierung einer permanenten Funktion. Sie sind die Funktionen und Aufgaben, die im Prozess orchestriert werden. Mit einer permanenten Funktion können Sie z. B. eine Bestellung verarbeiten. Die Aufgaben würden dann die Inventarprüfung, die Abrechnung von Kundengebühren und die Erstellung einer Lieferung beinhalten. Jede Aufgabe wäre eine Aktivitätsfunktion. 

Für Aktivitätsfunktionen bestehen hinsichtlich der Arbeiten, die mit ihnen ausgeführt werden können, keine Einschränkungen. Sie können eine Aktivitätsfunktion [in jeder von Durable Functions unterstützen Sprache schreiben](durable-functions-overview.md#language-support). Das permanente Aufgabenframework garantiert, dass jede aufgerufene Aktivitätsfunktion mindestens einmal in einer Orchestrierung ausgeführt wird.

Verwenden Sie einen [Aktivitätstrigger](durable-functions-bindings.md#activity-triggers), um eine Aktivitätsfunktion auszulösen. .NET-Funktionen nehmen einen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) als Parameter entgegen. Sie können den Trigger auch an beliebige andere Objekte binden, um Eingaben an die Funktion zu übergeben. In JavaScript können Sie über die `<activity trigger binding name>`-Eigenschaft des [`context.bindings`-Objekts](../functions-reference-node.md#bindings) auf die Eingabe zugreifen.

Eine Aktivitätsfunktion kann außerdem Werte an den Orchestrator zurückgeben. Wenn Sie viele Werte von einer Aktivitätsfunktion senden oder zurückgeben, können Sie [Tupel oder Arrays](durable-functions-bindings.md#passing-multiple-parameters) verwenden. Eine Aktivitätsfunktion können Sie nur über eine Orchestrierungsinstanz auslösen. Zwar können eine Aktivitätsfunktion und eine andere Funktion (etwa eine durch HTTP ausgelöste Funktion) einen Teil des Codes gemeinsam nutzen, jedoch kann jede Funktion nur einen Trigger aufweisen.

Weitere Informationen und Beispiele finden Sie unter [Aktivitätsfunktionen](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Orchestratorfunktionen

Orchestratorfunktionen beschreiben, wie und in welcher Reihenfolge Aktionen ausgeführt werden. Außerdem wird mit ihnen die Orchestrierung in Code (C# oder JavaScript) beschrieben, wie unter [Durable Functions patterns and technical concepts (Muster und technische Konzepte von Durable Functions)](durable-functions-concepts.md) dargestellt wird. Eine Orchestrierung kann viele verschiedene Aktionstypen umfassen, z. B. [Aktivitätsfunktionen](#activity-functions), [untergeordnete Orchestrierungen](#sub-orchestrations), [das Warten auf externe Ereignisse](#external-events) und [Timer](#durable-timers). 

Eine Orchestratorfunktion muss durch einen [Orchestrierungstrigger](durable-functions-bindings.md#orchestration-triggers) ausgelöst werden.

Ein Orchestrator wird von einem [Orchestratorclient](#client-functions) gestartet. Sie können diesen über verschiedene Quellen (HTTP, Warteschlange, Ereignisstream) auslösen. Jede Orchestrierungsinstanz verfügt über einen Instanzbezeichner. Dieser kann automatisch (empfohlen) oder vom Benutzer generiert werden. Mit dem Instanzbezeichner können Sie [Orchestrierungsinstanzen verwalten](durable-functions-instance-management.md).

Weitere Informationen und Beispiele finden Sie unter [Orchestrierungstrigger](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Clientfunktionen

Clientfunktionen sind ausgelöste Funktionen, die neue Instanzen einer Orchestrierung erstellen und gleichzeitig den Einstiegspunkt für das Erstellen einer Instanz in einer Durable Functions-Orchestrierung darstellen. Sie können eine Clientfunktion über verschiedene Quellen (HTTP, Warteschlange, Ereignisstream) auslösen. Zum Schreiben einer Clientfunktion können Sie jede beliebige Sprache verwenden, die von der App unterstützt wird. 

Clientfunktionen verfügen außerdem über eine [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client), mit der sie permanente Orchestrierungen erstellen und verwalten können. 

Das einfachste Beispiel für eine Clientfunktion ist eine über HTTP ausgelöste Funktion, die eine Orchestratorfunktion startet und anschließend eine Statusüberprüfungsantwort zurückgibt. Ein Beispiel finden Sie unter [Ermittlung der HTTP-API-URL](durable-functions-http-api.md#http-api-url-discovery).

Weitere Informationen und Beispiele finden Sie unter [Orchestrierungsclient](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funktionen und Muster

In den nächsten Abschnitten werden die Features und Muster von Durable Functions-Typen beschrieben.

### <a name="sub-orchestrations"></a>Untergeordnete Orchestrierungen

Orchestratorfunktionen können nicht nur Aktivitätsfunktionen aufrufen, sondern auch andere Orchestratorfunktionen. Beispielsweise können Sie aus einer Bibliothek mit Orchestratorfunktionen eine größere Orchestrierung erstellen. Alternativ können Sie mehrere Instanzen einer Orchestratorfunktion parallel ausführen.

Weitere Informationen und Beispiele finden Sie unter [Untergeordnete Orchestrierungen](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Permanente Timer

[Durable Functions](durable-functions-overview.md) stellen *permanente Timer* bereit, die in Orchestratorfunktionen zum Implementieren von Verzögerungen oder zum Einrichten von Timeouts für asynchrone Aktionen verwendet werden können. Verwenden Sie permanente Timer in Orchestratorfunktionen anstelle von `Thread.Sleep` und `Task.Delay` (C#) oder `setTimeout()` und `setInterval()` (JavaScript).

Weitere Informationen und Beispiele finden Sie unter [Permanente Timer](durable-functions-timers.md).

### <a name="external-events"></a>Externe Ereignisse

Orchestratorfunktionen können auf externe Ereignisse warten, um eine Orchestrierungsinstanz zu aktualisieren. Dieses Feature von Durable Functions ist oft bei der Verarbeitung von Benutzerinteraktionen oder anderer externer Rückrufe nützlich.

Weitere Informationen und Beispiele finden Sie unter [Externe Ereignisse](durable-functions-external-events.md).

### <a name="error-handling"></a>Fehlerbehandlung

Verwenden Sie Code zum Implementieren von Durable Functions-Orchestrierungen. Sie können die Fehlerbehandlungsfeatures der Programmiersprache verwenden. Muster wie `try`/`catch` können in Ihrer Orchestrierung eingesetzt werden. 

Durable Functions ist außerdem mit integrierten Wiederholungsrichtlinien ausgestattet. Eine Aktion kann Aktivitäten bei Ausnahmen automatisch verzögern und wiederholen. Sie können Wiederholungen zur Behandlung vorübergehender Ausnahmen einsetzen, ohne dabei die Orchestrierung abzubrechen.

Weitere Informationen und Beispiele finden Sie unter [Fehlerbehandlung](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Funktionsübergreifende App-Kommunikation

Obwohl eine permanente Orchestrierung im Kontext einer einzelnen Funktions-App ausgeführt wird, können Sie Muster zum Koordinieren von Orchestrierungen für mehrere Funktions-Apps verwenden. Auch wenn die App-übergreifende Kommunikation über HTTP erfolgt, ermöglicht es die Verwendung des permanenten Frameworks für jede Aktivität, trotzdem einen übergreifenden permanenten Prozess zwischen zwei Apps aufrecht zu erhalten.

Die folgenden Beispiele veranschaulichen die funktionsübergreifende App-Orchestrierung in C# und JavaScript. In jedem Beispiel wird die externe Orchestrierung von einer Aktivität gestartet. Eine andere Aktivität ruft den Status ab und gibt diesen zurück. Der Orchestrator fährt erst dann fort, wenn als Status `Complete` festgelegt wird.

Im Folgenden sehen Sie einige Beispiele für die funktionsübergreifende App-Orchestrierung:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihre erste permanente Funktion in [C#](durable-functions-create-first-csharp.md) oder [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Weitere Informationen zu Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
