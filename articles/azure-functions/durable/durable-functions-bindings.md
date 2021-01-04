---
title: Bindungen für Durable Functions – Azure
description: Erfahren Sie, wie Sie Trigger und Bindungen für die Durable Functions-Erweiterung für Azure Functions verwenden.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 899bc3fdc94b8232acd3edf3e0cbab3c481ff8f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013723"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindungen für Durable Functions (Azure Functions)

Mit der Erweiterung [Durable Functions](durable-functions-overview.md) (Langlebige Funktionen) werden zwei neue Triggerbindungen eingeführt, mit denen die Ausführung von Orchestrator- und Aktivitätsfunktionen gesteuert wird. Außerdem wird eine Ausgabebindung eingeführt, die als Client für die Laufzeit von Durable Functions dient.

## <a name="orchestration-trigger"></a>Orchestrierungstrigger

Mit einem Orchestrierungstrigger können Sie [langlebige Orchestratorfunktionen](durable-functions-types-features-overview.md#orchestrator-functions) erstellen. Dieser Trigger unterstützt das Starten von neuen Orchestratorfunktionsinstanzen und das Fortsetzen von vorhandenen Orchestratorfunktionsinstanzen, die auf eine Aufgabe „warten“.

Wenn Sie die Visual Studio-Tools für Azure Functions verwenden, wird der Orchestrierungstrigger mit dem .NET-Attribut [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute?view=azure-dotnet) konfiguriert.

Wenn Sie Orchestratorfunktionen in Skriptsprachen schreiben (z. B. in JavaScript- oder C#-Skripts), wird der Orchestrierungstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` ist der Name der Orchestrierung, den Clients verwenden müssen, wenn sie neue Instanzen dieser Orchestratorfunktion starten möchten. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

Intern fragt diese Triggerbindung eine Reihe von Warteschlangen im Standardspeicherkonto für die Funktionen-App ab. Da es sich bei diesen Warteschlangen um interne Implementierungsdetails der Erweiterung handelt, werden sie in den Bindungseigenschaften nicht explizit konfiguriert.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Orchestrierungstrigger angegeben:

* **Single-Threading**: Ein einzelner Verteilerthread wird für die gesamte Ausführung von Orchestratorfunktionen auf einer einzelnen Hostinstanz verwendet. Daher sollten Sie unbedingt sicherstellen, dass der Orchestratorfunktionscode effizient ist und keine E/A-Vorgänge durchführt. Außerdem sollten Sie sicherstellen, dass dieser Thread nur dann asynchrone Arbeitsschritte ausführt, wenn auf Aufgabentypen gewartet wird, die sich auf Durable Functions beziehen.
* **Behandlung von nicht verarbeitbaren Nachrichten:** Orchestrierungstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Orchestrierungstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Rückgabewerte werden in JSON-Code serialisiert und in der Tabelle mit dem Orchestrierungsverlauf im Azure-Tabellenspeicher beibehalten. Diese Rückgabewerte können von der Orchestrierungsclientbindung abgefragt werden, die weiter unten beschrieben wird.

> [!WARNING]
> Für Orchestratorfunktionen sollten niemals andere Eingabe- oder Ausgabebindungen als die Orchestrierungstriggerbindung verwendet werden. Wenn dies nicht beachtet wird, können sich unter Umständen Probleme mit der Erweiterung für langlebige Aufgaben ergeben, da sich diese Bindungen ggf. nicht an die Regeln für Single-Threading und E/A-Abläufe halten. Wenn Sie andere Bindungen verwenden möchten, fügen Sie sie einer Aktivitätsfunktion hinzu, die von der Orchestratorfunktion aufgerufen wird.

> [!WARNING]
> JavaScript-Orchestratorfunktionen sollten niemals als `async` deklariert werden.

### <a name="trigger-usage-net"></a>Verwendung von Triggern (.NET)

Die Orchestrierungstriggerbindung unterstützt sowohl Ein- als auch Ausgaben. Hier sind einige Dinge aufgeführt, die Sie in Bezug auf die Eingabe- und Ausgabeverarbeitung wissen sollten:

* **Eingaben**: .NET-Orchestrierungsfunktionen unterstützen nur `DurableOrchestrationContext` als Parametertyp. Deserialisierung von Eingaben direkt in die Funktionssignatur wird nicht unterstützt. Im Code muss die Methode `GetInput<T>` (.NET) oder `getInput` (JavaScript) zum Abrufen von Eingaben der Orchestratorfunktion verwendet werden. Bei diesen Eingaben muss es sich um Typen handeln, für die eine JSON-Serialisierung möglich ist.
* **Ausgaben**: Orchestrierungstrigger unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein. Wenn eine .NET-Funktion `Task` oder `void` zurückgibt, wird als Ausgabe der Wert `null` gespeichert.

### <a name="trigger-sample"></a>Triggerbeispiel

Der folgende Beispielcode zeigt, wie eine sehr einfache Orchestratorfunktion vom Typ „Hello World“ aussehen könnte:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

#### <a name="javascript-functions-20-only"></a>JavaScript (nur Functions 2.0)

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
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

#### <a name="javascript-functions-20-only"></a>JavaScript (nur Functions 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Aktivitätstrigger

Mit dem Aktivitätstrigger können Sie Funktionen erstellen, die von Orchestratorfunktionen aufgerufen werden, die als [Aktivitätsfunktionen](durable-functions-types-features-overview.md#activity-functions) bezeichnet werden.

Wenn Sie Visual Studio verwenden, wird der Aktivitätstrigger mit dem .NET-Attribut `ActivityTriggerAttribute` konfiguriert.

Wenn Sie VS Code oder das Azure-Portal zum Entwickeln verwenden, wird der Aktivitätstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` ist der Name der Aktivität. Dieser Wert ist der Name, der von Orchestratorfunktionen verwendet wird, um diese Aktivitätsfunktion aufzurufen. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

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

* **Eingaben**: .NET-Aktivitätsfunktionen verwenden nativ `DurableActivityContext` als Parametertyp. Alternativ dazu kann eine Aktivitätsfunktion mit jedem Parametertyp deklariert werden, der per JSON serialisierbar ist. Bei Verwendung von `DurableActivityContext` können Sie `GetInput<T>` aufrufen, um die Eingabe der Aktivitätsfunktion abzurufen und zu deserialisieren.
* **Ausgaben**: Aktivitätsfunktionen unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein. Wenn eine .NET-Funktion `Task` oder `void` zurückgibt, wird als Ausgabe der Wert `null` gespeichert.
* **Metadaten**: .NET-Aktivitätsfunktionen können an einen `string instanceId`-Parameter gebunden werden, um die Instanz-ID der übergeordneten Orchestrierung abzurufen.

### <a name="trigger-sample"></a>Triggerbeispiel

Der folgende Beispielcode zeigt, wie eine sehr einfache Aktivitätsfunktion vom Typ „Hello World“ aussehen könnte:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableActivityContext` anstelle von `IDurableActivityContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

Der Standardparametertyp für die .NET-Bindung `ActivityTriggerAttribute` lautet `IDurableActivityContext`. .NET-Aktivitätstrigger unterstützen aber auch die direkte Bindung an JSON-serialisierbare Typen (z. B. primitive Typen). Die gleiche Funktion kann daher wie folgt vereinfacht werden:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (nur Functions 2.0)

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


### <a name="using-input-and-output-bindings"></a>Verwenden von Eingabe- und Ausgabebindungen

Sie können neben der Aktivitätstriggerbindung auch reguläre Eingabe- und Ausgabebindungen verwenden. Beispielsweise können Sie die Eingabe in die Aktivitätsbindung verwenden und eine Nachricht mithilfe der EventHub-Ausgabebindung an einen EventHub senden:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestrierungsclient

Mit der Bindung des Orchestrierungsclients können Sie Funktionen schreiben, die mit Orchestratorfunktionen interagieren. Diese Funktionen werden manchmal auch als [Clientfunktionen](durable-functions-types-features-overview.md#client-functions)bezeichnet. Beispielsweise können Sie für Orchestrierungsinstanzen folgende Aktionen durchführen:

* Starten
* Abfragen des Status
* Beenden
* Senden von Ereignissen an die Instanzen während der Ausführung
* Löschen des Instanzverlaufs

Bei Verwendung von Visual Studio können Sie die Bindung an den Orchestrierungsclient durchführen, indem Sie das .NET-Attribut `OrchestrationClientAttribute` für Durable Functions 1.0 verwenden. Ab Durable Functions 2.0 können Sie mithilfe des .NET-Attributs `DurableClientAttribute` eine Bindung mit dem Orchestrierungsclient herstellen.

Wenn Sie Skriptsprachen (z.B. *CSX*- oder *JS*-Dateien) für die Entwicklung nutzen, wird der Orchestrierungstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

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

In .NET-Funktionen richten Sie normalerweise eine Bindung an `IDurableOrchestrationClient` ein, sodass Sie Vollzugriff auf alle Orchestrierungsclient-APIs erhalten, die von Durable Functions unterstützt werden. In den älteren Durable Functions 2.x-Releases erfolgt stattdessen eine Bindung an die `DurableOrchestrationClient`-Klasse. In JavaScript werden die gleichen APIs vom Objekt zur Verfügung gestellt, das von `getClient` zurückgegeben wird. Zu den APIs für das Clientobjekt gehören:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativ können .NET-Funktionen an `IAsyncCollector<T>` gebunden werden, wobei `T``StartOrchestrationArgs` oder `JObject` ist.

Weitere Informationen zu diesen Vorgängen finden Sie in der API-Dokumentation zu `IDurableOrchestrationClient`.

### <a name="client-sample-visual-studio-development"></a>Clientbeispiel (Visual Studio-Entwicklung)

Hier ist ein Beispiel für eine per Warteschlange ausgelöste Funktion angegeben, mit der eine „HelloWorld“-Orchestrierung gestartet wird.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Der vorherige JSON-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `orchestrationClient` anstelle von `durableClient` als Triggertyp verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

Im Folgenden sind sprachspezifische Beispiele angegeben, in denen neue Instanzen von Orchestratorfunktionen gestartet werden.

#### <a name="c-script-sample"></a>C#-Skriptbeispiel

Im folgenden Beispiel wird veranschaulicht, wie Sie die langlebige Orchestrierungsclientbindung nutzen, um eine neue Funktionsinstanz über eine durch die Warteschlange ausgelöste C#-Skriptfunktion zu starten:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

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

## <a name="entity-trigger"></a>Entitätstrigger

Entitätstrigger ermöglichen es Ihnen, [Entitätsfunktionen](durable-functions-entities.md) zu erstellen. Dieser Trigger unterstützt die Verarbeitung von Ereignissen für eine bestimmte Entitätsinstanz.

Wenn Sie die Visual Studio-Tools für Azure Functions verwenden, wird der Entitätstrigger mit dem .NET-Attribut `EntityTriggerAttribute` konfiguriert.

> [!NOTE]
> Entitätstrigger sind ab Durable Functions 2.x verfügbar.

Intern fragt diese Triggerbindung eine Reihe von Warteschlangen im Standardspeicherkonto für die Funktionen-App ab. Da es sich bei diesen Warteschlangen um interne Implementierungsdetails der Erweiterung handelt, werden sie in den Bindungseigenschaften nicht explizit konfiguriert.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Entitätstrigger aufgeführt:

* **Singlethread**: Ein einzelner Verteilerthread wird zum Verarbeiten von Vorgängen für eine bestimmte Entität verwendet. Wenn mehrere Nachrichten gleichzeitig an eine einzelne Entität gesendet werden, werden die Vorgänge einzeln verarbeitet.
* **Behandlung von nicht verarbeitbaren Nachrichten**: Entitätstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Entitätstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Entitätsfunktionen unterstützen keine Rückgabewerte. Es gibt bestimmte APIs, die zum Speichern des Zustands oder zum Zurückgeben von Werten an Orchestrierungen verwendet werden können.

Alle Zustandsänderungen, die während der Ausführung an einer Entität vorgenommen werden, werden nach Abschluss der Ausführung automatisch persistent gespeichert.

### <a name="trigger-usage-net"></a>Verwendung von Triggern (.NET)

Jede Entitätsfunktion weist den Parametertyp `IDurableEntityContext` auf, der über die folgenden Member verfügt:

* **EntityName**: Der Name der derzeit ausgeführten Entität.
* **EntityKey**: Der Schlüssel der derzeit ausgeführten Entität.
* **EntityId**: Die ID der derzeit ausgeführten Entität.
* **OperationName**: Der Name des aktuellen Vorgangs.
* **HasState**: Gibt an, ob die Entität vorhanden ist, also über einen Zustand verfügt. 
* **GetState\<TState>()** : Ruft den aktuellen Zustand der Entität ab. Dieses Element wird erstellt und auf `default<TState>` initialisiert, falls es noch nicht vorhanden ist. Der `TState`-Parameter muss ein primitiver oder JSON-serialisierbarer Typ sein. 
* **GetState\<TState>(initfunction)** : Ruft den aktuellen Zustand der Entität ab. Falles es noch nicht vorhanden ist, wird dieses Element durch Aufrufen des angegebenen `initfunction`-Parameters erstellt. Der `TState`-Parameter muss ein primitiver oder JSON-serialisierbarer Typ sein. 
* **SetState(arg)** : Erstellt oder aktualisiert den Zustand der Entität. Der `arg`-Parameter muss ein JSON-serialisierbares Objekt oder ein Primitiv sein.
* **DeleteState()** : Löscht den Zustand der Entität. 
* **GetInput\<TInput>()** : Ruft die Eingabe für den aktuellen Vorgang ab. Der `TInput`-Typparameter muss ein primitiver oder JSON-serialisierbarer Typ sein.
* **Return(arg)** : Gibt einen Wert an die Orchestrierung zurück, die den Vorgang aufgerufen hat. Der `arg`-Parameter muss ein primitives oder JSON-serialisierbares Objekt sein.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input)** : Sendet eine unidirektionale Nachricht an eine Entität. Der `operation`-Parameter muss eine Zeichenfolge ungleich bzw. nicht Null sein, der optionale `scheduledTimeUtc`-Parameter muss ein UTC-Datums-/Uhrzeitwert sein, zu dem der Vorgang aufgerufen werden soll, und der `input`-Parameter muss ein primitives oder JSON-serialisierbares Objekt sein.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : Startet eine neue Orchestrierung. Der `input`-Parameter muss ein primitives oder JSON-serialisierbares Objekt sein.

Auf das an die Entitätsfunktion übergebene `IDurableEntityContext`-Objekt kann mithilfe der asynchronen lokalen `Entity.Current`-Eigenschaft zugegriffen werden. Diese Vorgehensweise ist praktisch, wenn Sie das klassenbasierte Programmiermodell verwenden.

### <a name="trigger-sample-c-function-based-syntax"></a>Triggerbeispiel (C#-funktionsbasierte Syntax)

Der folgende Code ist ein Beispiel für eine einfache Entität vom Typ *Counter*, die als dauerhafte Funktion implementiert wird. Diese Funktion definiert drei Vorgänge (`add`, `reset` und `get`), die jeweils auf einem ganzzahligen Zustand basieren.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Weitere Informationen zur funktionsbasierten Syntax sowie zu deren Verwendung finden Sie unter [Funktionsbasierte Syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Triggerbeispiel (C#-klassenbasierte Syntax)

Beim folgenden Beispiel handelt es sich um eine äquivalente Implementierung der Entität `Counter` unter Verwendung von Klassen und Methoden:

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Der Zustand dieser Entität ist ein Objekt vom Typ `Counter`. Dieses enthält ein Feld, in dem der aktuelle Wert des Zählers gespeichert wird. Um dieses Objekt dauerhaft im Speicher aufzubewahren, wird es von der Bibliothek [Json.NET](https://www.newtonsoft.com/json) serialisiert und deserialisiert. 

Weitere Informationen zur klassenbasierten Syntax sowie zu deren Verwendung finden Sie unter [Definieren von Entitätsklassen](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Die Methode für den Funktionseinstiegspunkt mit dem Attribut `[FunctionName]`*muss* bei Verwendung von Entitätsklassen als `static` deklariert werden. Nicht statische Einstiegspunktmethoden können zu einer Mehrfachinitialisierung von Objekten und möglicherweise zu anderen undefinierten Verhaltensweisen führen.

Entitätsklassen verfügen über spezielle Mechanismen für die Interaktion mit Bindungen und .NET-Abhängigkeitsinjektion (Dependency Injection). Weitere Informationen finden Sie unter [Entitätskonstruktion](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Triggerbeispiel (JavaScript)

Der folgende Code ist ein Beispiel für eine einfache Entität vom Typ *Counter*, die als dauerhafte Funktion in JavaScript implementiert wird. Diese Funktion definiert drei Vorgänge (`add`, `reset` und `get`), die jeweils auf einem ganzzahligen Zustand basieren.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Dauerhafte Entitäten sind ab JavaScript-Version **1.3.0** des `durable-functions`-npm-Pakets verfügbar.

## <a name="entity-client"></a>Entitätsclient

Die Entitätsclientbindung ermöglicht es Ihnen, [Entitätsfunktionen](#entity-trigger) asynchron auszulösen. Diese Funktionen werden manchmal auch als [Clientfunktionen](durable-functions-types-features-overview.md#client-functions)bezeichnet.

Bei Verwendung von Visual Studio können Sie die Bindung an den Entitätsclient vornehmen, indem Sie das .NET-Attribut `DurableClientAttribute` verwenden.

> [!NOTE]
> `[DurableClientAttribute]` kann auch verwendet werden, um eine Bindung mit dem [Orchestrierungsclient](#orchestration-client) herzustellen.

Wenn Sie Skriptsprachen (z.B. *CSX*- oder *JS*-Dateien) für die Entwicklung nutzen, wird der Entitätstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`: Wird in Szenarien verwendet, in denen mehrere Funktionen-Apps dasselbe Speicherkonto nutzen, aber voneinander isoliert werden müssen. Wenn Sie hier nichts angeben, wird der Standardwert aus `host.json` verwendet. Dieser Wert muss mit dem Wert übereinstimmen, der von den Zielentitätsfunktionen verwendet wird.
* `connectionName`: Der Name einer App-Einstellung, die eine Speicherkonto-Verbindungszeichenfolge enthält. Das Speicherkonto, für das diese Verbindungszeichenfolge steht, muss dem Speicherkonto entsprechen, das von den Zielentitätsfunktionen verwendet wird. Wenn nichts angegeben ist, wird die Speicherkonto-Standardverbindungszeichenfolge für die Funktionen-App verwendet.

> [!NOTE]
> In den meisten Fällen ist es empfehlenswert, die optionalen Eigenschaften nicht zu verwenden und das Standardverhalten zu nutzen.

### <a name="entity-client-usage"></a>Syntax des Entitätsclients

In .NET-Funktionen richten Sie normalerweise eine Bindung mit `IDurableEntityClient` ein, sodass Sie Vollzugriff auf alle Client-APIs erhalten, die von Durable Entities unterstützt werden. Sie können auch eine Bindung mit der `IDurableOrchestrationClient`-Schnittstelle herstellen, die den Zugriff auf Client-APIs für Entitäten und Orchestrierungen ermöglicht. Zu den APIs für das Clientobjekt gehören:

* **ReadEntityStateAsync\<T>** : Liest den Zustand einer Entität. Es gibt eine Antwort zurück, die anzeigt, ob die Zielentität vorhanden ist, und wenn dies der Fall ist, ihren Zustand.
* **SignalEntityAsync**: Sendet eine unidirektional Nachricht an eine Entität und wartet darauf, dass diese in die Warteschlange eingereiht wird.
* **ListEntitiesAsync**: Abfragen für den Zustand mehrerer Entitäten. Entitäten können nach *Namen* und *letzter Vorgangszeit* abgefragt werden.

Die Zielentität muss nicht vor dem Senden eines Signals erstellt werden. Der Entitätszustand kann aus der Entitätsfunktion heraus erstellt werden, die das Signal verarbeitet.

> [!NOTE]
> Es ist wichtig zu verstehen, dass die vom Client gesendeten „Signale“ einfach in die Warteschlange eingereiht werden, um zu einem späteren Zeitpunkt asynchron verarbeitet zu werden. Insbesondere `SignalEntityAsync` tätigt in der Regel eine Rückgabe, bevor die Entität auch nur den Betrieb überhaupt aufgenommen hat, und es ist nicht möglich, den Rückgabewert zurückzubekommen oder Ausnahmen zu beobachten. Wenn strengere Garantien erforderlich sind (z. B. für Workflows), sollten *Orchestratorfunktionen* verwendet werden, die auf den Abschluss von Entitätsvorgängen warten und die Rückgabewerte verarbeiten sowie Ausnahmen überwachen können.

### <a name="example-client-signals-entity-directly---c"></a>Beispiel: Client sendet Signal direkt an Entität – C#

Dies ist ein Beispiel für eine von der Warteschlange ausgelöste Funktion, die eine Entität „Counter“ aufruft.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Beispiel: Client sendet Signal über Schnittstelle an Entität – C#

Aufgrund der besseren Typüberprüfung empfehlen wir Ihnen, nach Möglichkeit [über Schnittstellen auf Entitäten zuzugreifen](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces). Nehmen wir beispielsweise an, dass die zuvor erwähnte `Counter`-Entität eine `ICounter`-Schnittstelle implementiert hat, die wie folgt definiert ist:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Der Clientcode kann dann `SignalEntityAsync<ICounter>` verwenden, um einen typsicheren Proxy zu generieren:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Der `proxy`-Parameter ist eine dynamisch generierte Instanz von `ICounter`, die den Aufruf von `Add` intern in den entsprechenden (nicht typisierten) Aufruf von `SignalEntityAsync` übersetzt.

> [!NOTE]
> Die `SignalEntityAsync`-APIs stellen unidirektionale Vorgänge dar. Wenn eine Entitätsschnittstelle `Task<T>` zurückgibt, ist der Wert des `T`-Parameters immer NULL oder `default`.

Insbesondere ergibt es keinen Sinn, ein Signal an den `Get`-Vorgang zu senden, weil kein Wert zurückgegeben wird. Stattdessen können Clients entweder `ReadStateAsync` verwenden, um direkt auf den Zählerzustand zuzugreifen, oder eine Orchestratorfunktion starten, mit der der `Get`-Vorgang aufgerufen wird.

### <a name="example-client-signals-entity---javascript"></a>Beispiel: Client sendet Signal direkt an Entität – JavaScript

Dies ist ein Beispiel für eine von der Warteschlange ausgelöste Funktion, die eine Entität „Counter“ in JavaScript signalisiert.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Dauerhafte Entitäten sind ab JavaScript-Version **1.3.0** des `durable-functions`-npm-Pakets verfügbar.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Einstellungen für „host.json“

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Integrierte in HTTP API-Referenz für Instanzverwaltung](durable-functions-http-api.md)
