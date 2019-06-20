---
title: Bindungen für Durable Functions – Azure
description: Erfahren Sie, wie Sie Trigger und Bindungen für die Durable Functions-Erweiterung für Azure Functions verwenden.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 678e370977cadae642207f91a02136404fb6c34e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60710530"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindungen für Durable Functions (Azure Functions)

Mit der Erweiterung [Durable Functions](durable-functions-overview.md) (Langlebige Funktionen) werden zwei neue Triggerbindungen eingeführt, mit denen die Ausführung von Orchestrator- und Aktivitätsfunktionen gesteuert wird. Außerdem wird eine Ausgabebindung eingeführt, die als Client für die Laufzeit von Durable Functions dient.

## <a name="orchestration-triggers"></a>Orchestrierungstrigger

Mit einem Orchestrierungstrigger können Sie langlebige Orchestratorfunktionen erstellen. Dieser Trigger unterstützt das Starten von neuen Orchestratorfunktionsinstanzen und das Fortsetzen von vorhandenen Orchestratorfunktionsinstanzen, die auf eine Aufgabe „warten“.

Wenn Sie die Visual Studio-Tools für Azure Functions verwenden, wird der Orchestrierungstrigger mit dem .NET-Attribut [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) konfiguriert.

Wenn Sie Orchestratorfunktionen in Skriptsprachen schreiben (z. B. in JavaScript- oder C#-Skripts), wird der Orchestrierungstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` ist der Name der Orchestrierung. Dies ist der Wert, den Clients verwenden müssen, wenn sie neue Instanzen dieser Orchestratorfunktion starten möchten. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

Intern fragt diese Triggerbindung eine Reihe von Warteschlangen im Standardspeicherkonto für die Funktionen-App ab. Da es sich bei diesen Warteschlangen um interne Implementierungsdetails der Erweiterung handelt, werden sie in den Bindungseigenschaften nicht explizit konfiguriert.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Orchestrierungstrigger angegeben:

* **Single-Threading**: Ein einzelner Verteilerthread wird für die gesamte Ausführung von Orchestratorfunktionen auf einer einzelnen Hostinstanz verwendet. Daher sollten Sie unbedingt sicherstellen, dass der Orchestratorfunktionscode effizient ist und keine E/A-Vorgänge durchführt. Außerdem sollten Sie sicherstellen, dass dieser Thread nur dann asynchrone Arbeitsschritte ausführt, wenn auf Aufgabentypen gewartet wird, die sich auf Durable Functions beziehen.
* **Behandlung von nicht verarbeitbaren Nachrichten:** Orchestrierungstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Orchestrierungstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Rückgabewerte werden in JSON-Code serialisiert und in der Tabelle mit dem Orchestrierungsverlauf im Azure-Tabellenspeicher beibehalten. Diese Rückgabewerte können von der Orchestrierungsclientbindung abgefragt werden, die weiter unten beschrieben wird.

> [!WARNING]
> Für Orchestratorfunktionen sollten niemals andere Eingabe- oder Ausgabebindungen als die Orchestrierungstriggerbindung verwendet werden. Wenn dies nicht beachtet wird, können sich unter Umständen Probleme mit der Erweiterung für langlebige Aufgaben ergeben, da sich diese Bindungen ggf. nicht an die Regeln für Single-Threading und E/A-Abläufe halten.

> [!WARNING]
> JavaScript-Orchestratorfunktionen sollten niemals als `async` deklariert werden.

### <a name="trigger-usage-net"></a>Verwendung von Triggern (.NET)

Die Orchestrierungstriggerbindung unterstützt sowohl Ein- als auch Ausgaben. Hier sind einige Dinge aufgeführt, die Sie in Bezug auf die Eingabe- und Ausgabeverarbeitung wissen sollten:

* **Eingaben**: .NET-Orchestrierungsfunktionen unterstützen nur [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) als Parametertyp. Deserialisierung von Eingaben direkt in die Funktionssignatur wird nicht unterstützt. Im Code muss die [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)-Methode (.NET) oder `getInput`-Methode (JavaScript) zum Abrufen von Eingaben der Orchestratorfunktion verwendet werden. Bei diesen Eingaben muss es sich um Typen handeln, für die eine JSON-Serialisierung möglich ist.
* **Ausgaben**: Orchestrierungstrigger unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein. Wenn eine .NET-Funktion `Task` oder `void` zurückgibt, wird als Ausgabe der Wert `null` gespeichert.

### <a name="trigger-sample"></a>Triggerbeispiel

Hier ist ein Beispiel dafür angegeben, wie eine sehr einfache Orchestratorfunktion vom Typ „Hallo Welt“ aussehen könnte:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Das `context`-Objekt in JavaScript stellt nicht den „DurableOrchestrationContext“ dar, sondern den [Funktionskontext als Ganzes](../functions-reference-node.md#context-object). Sie können über die `df`-Eigenschaft des `context`-Objekts auf Orchestrierungsmethoden zugreifen.

> [!NOTE]
> JavaScript-Orchestratoren sollten `return` verwenden. Die Bibliothek `durable-functions` übernimmt den Aufruf der `context.done`-Methode.

Die meisten Orchestratorfunktionen rufen Aktivitätsfunktionen auf. In diesem „Hello World“-Beispiel wird veranschaulicht, wie eine Aktivitätsfunktion aufgerufen wird:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Aktivitätstrigger

Mit dem Aktivitätstrigger können Sie Funktionen erstellen, die von Orchestratorfunktionen aufgerufen werden.

Wenn Sie Visual Studio verwenden, wird der Aktivitätstrigger mit dem .NET-Attribut [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) konfiguriert.

Wenn Sie VS Code oder das Azure-Portal zum Entwickeln verwenden, wird der Aktivitätstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` ist der Name der Aktivität. Dies ist der Wert, der von Orchestratorfunktionen verwendet wird, um diese Aktivitätsfunktion aufzurufen. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

Intern fragt diese Triggerbindung eine Warteschlange im Standardspeicherkonto für die Funktionen-App ab. Da es sich bei dieser Warteschlange um ein internes Implementierungsdetail der Erweiterung handelt, wird sie in den Bindungseigenschaften nicht explizit konfiguriert.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Aktivitätstrigger aufgeführt:

* **Threading**: Im Gegensatz zum Orchestrierungstrigger gelten für Aktivitätstrigger keine Einschränkungen in Bezug auf das Threading oder den E/A-Ablauf. Sie können wie reguläre Funktionen behandelt werden.
* **Behandlung von nicht verarbeitbaren Nachrichten**: Aktivitätstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Aktivitätstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Rückgabewerte werden in JSON-Code serialisiert und in der Tabelle mit dem Orchestrierungsverlauf im Azure-Tabellenspeicher beibehalten.

> [!WARNING]
> Das Speicher-Back-End für Aktivitätsfunktionen ist ein Implementierungsdetail, und Benutzercode sollte mit diesen Speicherentitäten nicht direkt interagieren.

### <a name="trigger-usage-net"></a>Verwendung von Triggern (.NET)

Die Aktivitätstriggerbindung unterstützt sowohl Ein- als auch Ausgaben – genau wie der Orchestrierungstrigger. Hier sind einige Dinge aufgeführt, die Sie in Bezug auf die Eingabe- und Ausgabeverarbeitung wissen sollten:

* **Eingaben**: .NET-Aktivitätsfunktionen verwenden nativ [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) als Parametertyp. Alternativ dazu kann eine Aktivitätsfunktion mit jedem Parametertyp deklariert werden, der per JSON serialisierbar ist. Bei Verwendung von `DurableActivityContext` können Sie [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) aufrufen, um die Eingabe der Aktivitätsfunktion abzurufen und zu deserialisieren.
* **Ausgaben**: Aktivitätsfunktionen unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein. Wenn eine .NET-Funktion `Task` oder `void` zurückgibt, wird als Ausgabe der Wert `null` gespeichert.
* **Metadaten**: .NET-Aktivitätsfunktionen können an einen `string instanceId`-Parameter gebunden werden, um die Instanz-ID der übergeordneten Orchestrierung abzurufen.

### <a name="trigger-sample"></a>Triggerbeispiel

Hier ist ein Beispiel dafür angegeben, wie eine sehr einfache Aktivitätsfunktion vom Typ „Hallo Welt“ aussehen könnte:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Der Standardparametertyp für die .NET-Bindung `ActivityTriggerAttribute` lautet `DurableActivityContext`. .NET-Aktivitätstrigger unterstützen aber auch die direkte Bindung an JSON-serialisierbare Typen (z. B. primitive Typen). Die gleiche Funktion kann daher wie folgt vereinfacht werden:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-Bindungen können auch als zusätzliche Parameter übergeben werden. Die gleiche Funktion kann daher wie folgt vereinfacht werden:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Übergeben von mehreren Parametern

Es ist nicht möglich, mehrere Parameter direkt an eine Aktivitätsfunktion zu übergeben. In diesem Fall wird empfohlen, ein Array von Objekten zu übergeben oder [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples)-Objekte in .NET zu verwenden.

Das folgende Beispiel verwendet neue Features von [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples), die mit [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) hinzugefügt wurden:

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Orchestrierungsclient

Mit der Bindung des Orchestrierungsclients können Sie Funktionen schreiben, die mit Orchestratorfunktionen interagieren. Beispielsweise können Sie für Orchestrierungsinstanzen folgende Aktionen durchführen:

* Starten
* Abfragen des Status
* Beenden
* Senden von Ereignissen an die Instanzen während der Ausführung
* Löschen des Instanzverlaufs

Bei Verwendung von Visual Studio können Sie die Bindung an den Orchestrierungsclient durchführen, indem Sie das .NET-Attribut [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) verwenden.

Wenn Sie Skriptsprachen (z.B. *CSX*- oder *JS*-Dateien) für die Entwicklung nutzen, wird der Orchestrierungstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der *function.json*-Datei definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`: Wird in Szenarien verwendet, in denen mehrere Funktionen-Apps dasselbe Speicherkonto nutzen, aber voneinander isoliert werden müssen. Wenn Sie hier nichts angeben, wird der Standardwert aus `host.json` verwendet. Dieser Wert muss mit dem Wert übereinstimmen, der von den Zielorchestratorfunktionen verwendet wird.
* `connectionName`: Der Name einer App-Einstellung, die eine Speicherkonto-Verbindungszeichenfolge enthält. Das Speicherkonto, für das diese Verbindungszeichenfolge steht, muss dem Speicherkonto entsprechen, das von den Zielorchestratorfunktionen verwendet wird. Wenn nichts angegeben ist, wird die Speicherkonto-Standardverbindungszeichenfolge für die Funktionen-App verwendet.

> [!NOTE]
> In den meisten Fällen ist es empfehlenswert, diese Eigenschaften wegzulassen und das Standardverhalten zu nutzen.

### <a name="client-usage"></a>Clientnutzung

In .NET-Funktionen richten Sie normalerweise eine Bindung an `DurableOrchestrationClient` ein, sodass Sie Vollzugriff auf alle Client-APIs erhalten, die von Durable Functions unterstützt werden. In JavaScript werden die gleichen APIs vom `DurableOrchestrationClient`-Objekt verfügbar gemacht, das von `getClient` zurückgegeben wird. Zu den APIs für das Clientobjekt gehören:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (derzeit nur .NET)

Alternativ können .NET-Funktionen an `IAsyncCollector<T>` gebunden werden, wobei `T` für [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) oder `JObject` steht.

Ausführliche Informationen zu diesen Vorgängen finden Sie in der API-Dokumentation zu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html).

> [!WARNING]
> Wenn Sie lokal in JavaScript entwickeln, müssen Sie die Umgebungsvariable `WEBSITE_HOSTNAME` auf `localhost:<port>` festlegen, z. B. `localhost:7071`, um Methoden mit `DurableOrchestrationClient` zu verwenden. Weitere Informationen zu dieser Anforderung finden Sie in der [Beschreibung des Problems auf GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Clientbeispiel (Visual Studio-Entwicklung)

Hier ist ein Beispiel für eine per Warteschlange ausgelöste Funktion angegeben, mit der eine „HelloWorld“-Orchestrierung gestartet wird.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Clientbeispiel (nicht Visual Studio)

Wenn Sie für die Entwicklung nicht Visual Studio nutzen, können Sie die folgende *function.json*-Datei erstellen. In diesem Beispiel wird veranschaulicht, wie Sie eine per Warteschlange ausgelöste Funktion konfigurieren, für die die langlebige Orchestrierungsclientbindung verwendet wird:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Im Folgenden sind sprachspezifische Beispiele angegeben, in denen neue Instanzen von Orchestratorfunktionen gestartet werden.

#### <a name="c-sample"></a>C#-Beispiel

Im folgenden Beispiel wird veranschaulicht, wie Sie die langlebige Orchestrierungsclientbindung nutzen, um eine neue Funktionsinstanz über eine C#-Skriptfunktion zu starten:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-Beispiel

Im folgenden Beispiel wird veranschaulicht, wie Sie die langlebige Orchestrierungsclientbindung nutzen, um eine neue Funktionsinstanz über eine JavaScript-Funktion zu starten:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Weitere Informationen zum Starten von Instanzen finden Sie unter [Instanzverwaltung](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Setzen von Prüfpunkten und Wiedergabeverhalten](durable-functions-checkpointing-and-replay.md)