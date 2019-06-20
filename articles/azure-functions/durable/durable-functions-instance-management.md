---
title: Verwalten von Instanzen in Durable Functions – Azure
description: Es wird beschrieben, wie Instanzen in der Erweiterung Durable Functions für Azure Functions verwaltet werden.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730768"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Verwalten von Instanzen in Durable Functions in Azure

Wenn Sie die [Durable Functions](durable-functions-overview.md)-Erweiterung für Azure Functions verwenden oder damit beginnen möchten, stellen Sie sicher, dass Sie den optimalen Nutzen daraus ziehen. Sie können Ihre Durable Functions-Orchestrierungsinstanzen optimieren, wenn Sie mehr über ihre Verwaltung erfahren. In diesem Artikel wird jeder Instanzenverwaltungsvorgang im Detail erläutert.

Sie können Instanzen z.B. starten und beenden, und Sie können Instanzen abfragen, einschließlich der Möglichkeit, alle Instanzen und Instanzen mit Filtern abzufragen. Darüber hinaus können Sie Ereignisse an Instanzen senden, auf den Abschluss der Orchestrierung warten und HTTP-Management-Webhook-URLs abrufen. Dieser Artikel behandelt auch andere Verwaltungsvorgänge, einschließlich des Zurückspulens von Instanzen, Löschen des Instanzverlaufs und Löschen eines Aufgabenhubs.

In Durable Functions stehen Ihnen für die Implementierung jedes dieses Verwaltungsvorgänge Optionen zur Verfügung. Dieser Artikel enthält Beispiele, in denen die [Azure Functions Core Tools](../functions-run-local.md) sowohl für .NET (C#) als auch JavaScript verwendet werden.

## <a name="start-instances"></a>Starten von Instanzen

Es ist wichtig, eine Instanz der Orchestrierung starten zu können. Dies geschieht im Allgemeinen, wenn Sie eine Durable Functions-Bindung im Trigger einer anderen Funktion verwenden.

Die [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)-Methode der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse (.NET) oder die `startNew`-Methode der `DurableOrchestrationClient`-Klasse (JavaScript) startet eine neue Instanz. Sie erwerben Instanzen dieser Klasse mithilfe der `orchestrationClient`-Bindung. Intern stellt diese Methode eine Nachricht in die Steuerwarteschlange, die dann den Start einer Funktion mit dem angegebenen Namen auslöst, der die `orchestrationTrigger`-Bindung verwendet.

Dieser asynchrone Vorgang wird abgeschlossen, wenn der Orchestrierungsprozess erfolgreich geplant wurde. Der Orchestrierungsprozess sollte innerhalb von 30 Sekunden gestartet werden. Wenn es länger dauert, wird eine `TimeoutException` ausgelöst.

> [!WARNING]
> Wenn Sie lokal in JavaScript entwickeln, legen Sie die Umgebungsvariable `WEBSITE_HOSTNAME` auf `localhost:<port>` fest (z.B. `localhost:7071`), um `DurableOrchestrationClient`-Methoden zu verwenden. Weitere Informationen zu dieser Anforderung finden Sie in der [Beschreibung des Problems auf GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Die Parameter für [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) lauten wie folgt:

* **Name**: Der Name der zu planenden Orchestratorfunktion.
* **Eingabe**: Alle JSON-serialisierbaren Daten, die als Eingabe an die Orchestratorfunktion übergeben werden sollen.
* **InstanceId**: (Optional) Die eindeutige ID der Instanz. Ohne Angabe dieses Parameters verwendet die Methode eine zufällige ID.

Hier ist ein einfaches Beispiel für C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

Die Parameter für `startNew` lauten wie folgt:

* **Name**: Der Name der zu planenden Orchestratorfunktion.
* **InstanceId**: (Optional) Die eindeutige ID der Instanz. Ohne Angabe dieses Parameters verwendet die Methode eine zufällige ID.
* **Eingabe**: (Optional) Alle JSON-serialisierbaren Daten, die als Eingabe an die Orchestratorfunktion übergeben werden sollen.

Hier sehen Sie ein einfaches JavaScript-Beispiel:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Verwenden Sie einen zufälligen Bezeichner für die Instanz-ID. Dies gewährleistet eine gleichmäßige Lastenverteilung, wenn Sie Orchestratorfunktionen mehrere virtuelle Computer übergreifend skalieren. Wenn die ID aus einer externen Quelle stammen muss oder bei der Implementierung des [Singleton-Orchestrator](durable-functions-singletons.md)-Musters sollten Sie nicht zufällige Instanz-IDs verwenden.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Instanz auch direkt mit dem Befehl `durable start-new` von [Azure Functions Core Tools](../functions-run-local.md) starten. Hierfür werden die folgenden Parameter verwendet:

* **`function-name` (erforderlich)** : Der Name der zu startenden Funktion.
* **`input` (optional)** : Die Eingabe für die Funktion, entweder inline oder über eine JSON-Datei. Fügen Sie bei Dateien dem Pfad zur Datei mit `@` ein Präfix hinzu, z.B. `@path/to/file.json`.
* **`id` (optional)** : Die ID der Orchestrierungsinstanz. Ohne Angabe dieses Parameters verwendet der Befehl eine zufällige ID.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert ist AzureWebJobsStorage.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert ist DurableFunctionsHub. Sie können diesen Wert auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festlegen.

> [!NOTE]
> Bei Core Tools-Befehlen wird davon ausgegangen, dass Sie sie im Stammverzeichnis einer Funktions-App ausführen. Wenn Sie den `connection-string-setting`- und `task-hub-name`-Parameter explizit angeben, können Sie die Befehle von jedem beliebigen Verzeichnis aus ausführen. Sie können diese Befehle zwar ausführen, ohne dass ein Funktions-App-Host ausgeführt wird, aber möglicherweise stellen Sie fest, dass Sie einige Auswirkungen nur dann beobachten können, wenn der Host ausgeführt wird. Beispielsweise reiht der Befehl `start-new` eine Startmeldung in die Warteschlange für den Zielaufgabenhub ein, die Orchestrierung wird jedoch nicht ausgeführt, bis ein Funktions-App-Hostprozess ausgeführt wird, der die Nachricht verarbeiten kann.

Der folgende Befehl startet die Funktion HelloWorld und übergibt dieser den Inhalt der Datei `counter-data.json`:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Abfragen von Instanzen

Im Rahmen Ihres Aufwands zum Verwalten von Orchestrierungen müssen Sie wahrscheinlich Informationen über den Status einer Orchestrierungsinstanz sammeln (z.B., ob sie normal abgeschlossen wurde oder ein Fehler aufgetreten ist).

Die [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)-Methode der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse (.NET) oder `getStatus`-Methode der `DurableOrchestrationClient`-Klasse (JavaScript) fragt den Status einer Orchestrierungsinstanz ab.

Die Methode akzeptiert die Parameter `instanceId` (erforderlich), `showHistory` (optional), `showHistoryOutput` (optional) und `showInput` (optional, nur .NET).

* **`showHistory`** : Bei Festlegung auf `true` enthält die Antwort den Ausführungsverlauf.
* **`showHistoryOutput`** : Bei Festlegung auf `true` enthält der Ausführungsverlauf Aktivitätsausgaben.
* **`showInput`** : Bei Festlegung auf `false` enthält die Antwort nicht die Eingabe der Funktion. Standardwert: `true`. (Nur .NET)

Die Methode gibt ein JSON-Objekt mit den folgenden Eigenschaften zurück:

* **Name**: Der Name der Orchestratorfunktion.
* **InstanceId**: Die Instanz-ID der Orchestrierung (muss mit der Eingabe `instanceId` identisch sein).
* **CreatedTime**: Der Zeitpunkt, zu dem die Ausführung der Orchestratorfunktion gestartet wurde.
* **LastUpdatedTime**: Der Zeitpunkt, zu dem der letzte Prüfpunkt für die Orchestrierung erstellt wurde.
* **Eingabe**: Die Eingabe der Funktion als JSON-Wert. Dieses Feld wird nicht aufgefüllt, wenn `showInput` FALSE ist.
* **CustomStatus**: Der benutzerdefinierter Orchestrierungsstatus im JSON-Format.
* **Ausgabe**: Die Ausgabe der Funktion als JSON-Wert (wenn die Funktion abgeschlossen wurde). Wenn ein Fehler bei der Orchestratorfunktion auftritt, enthält diese Eigenschaft die Fehlerdetails. Wenn die Orchestratorfunktion beendet wurde, enthält diese Eigenschaft den Grund für die Beendigung (sofern vorhanden).
* **RuntimeStatus**: Einer der folgenden Werte:
  * **Pending**: Die Instanz wurde geplant, ihre Ausführung aber noch nicht gestartet.
  * **Running**: Die Instanz wurde gestartet.
  * **Completed**: Die Instanz wurde normal abgeschlossen.
  * **ContinuedAsNew**: Die Instanz hat sich selbst mit einem neuen Verlauf neu gestartet. Dies ist ein vorübergehender Status.
  * **Failed**: Bei der Instanz ist ein Fehler aufgetreten.
  * **Terminated**: Die Instanz wurde plötzlich beendet.
* **History**: Der Ausführungsverlauf der Orchestrierung. Dieses Feld wird nur gefüllt, wenn `showHistory` auf `true` festgelegt ist.

Diese Methode gibt `null` zurück, wenn die Instanz nicht vorhanden ist, oder ihre Ausführung noch nicht gestartet wurde.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Es ist auch möglich, den Status der Orchestrierung direkt über den Befehl `durable get-runtime-status` von [Azure Functions Core Tools](../functions-run-local.md) abzurufen. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`show-input` (optional)** : Bei Festlegung auf `true` enthält die Antwort die Eingabe der Funktion. Standardwert: `false`.
* **`show-output` (optional)** : Bei Festlegung auf `true` enthält die Antwort die Ausgabe der Funktion. Standardwert: `false`.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl ruft den Status (einschließlich Ein- und Ausgaben) einer Instanz mit der Orchestrierungsinstanz-ID 0ab8c55a66644d68a3a8b220b12d209c ab. Dabei wird vorausgesetzt, dass Sie den Befehl `func` im Stammverzeichnis der Funktions-App ausführen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Sie können den Befehl `durable get-history` zum Abrufen des Ausführungsverlaufs einer Orchestrierungsinstanz verwenden. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in „host.json“ festgelegt werden.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Abfragen aller Instanzen

Anstatt jeweils eine Instanz in Ihrer Orchestrierung abzufragen, finden Sie es möglicherweise effizienter, alle auf einmal abzufragen.

Sie können die `GetStatusAsync`-Methode (.NET) oder `getStatusAll`-Methode (JavaScript) verwenden, um die Status aller Orchestrierungsinstanzen abzufragen. In .NET können Sie ein `CancellationToken`-Objekt übergeben, falls Sie die Methode abbrechen möchten. Die Methode gibt Objekte mit denselben Eigenschaften wie die `GetStatusAsync`-Methode mit Parametern zurück.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Es ist auch möglich, Instanzen direkt über den Befehl `durable get-instances` von [Azure Functions Core Tools](../functions-run-local.md) abzufragen. Hierfür werden die folgenden Parameter verwendet:

* **`top` (optional)** : Dieser Befehl unterstützt Paging. Dieser Parameter entspricht der Anzahl der pro Anforderung abgerufenen Instanzen. Der Standardwert ist 10.
* **`continuation-token` (optional)** : Ein Token, das angibt, welche Seite oder welcher Abschnitt der Instanzen abgerufen werden soll. Jede Ausführung von `get-instances` gibt ein Token an den nächsten Satz von Instanzen zurück.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Abfragen von Instanzen mit Filtern

Was ist, wenn Sie nicht alle Informationen benötigen, die eine standardmäßige Instanzenabfrage bieten kann? Wenn Sie z.B. nur den Zeitpunkt der Erstellung der Orchestrierung oder den Laufzeitstatus der Orchestrierung erfahren möchten? Sie können die Abfrage durch Anwenden von Filtern eingrenzen.

Mit der `GetStatusAsync`-Methode (.NET) oder `getStatusBy`-Methode (JavaScript) können Sie eine Liste von Orchestrierungsinstanzen abrufen, die einer Gruppe von vordefinierten Filtern entsprechen.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

In den Azure Functions Core Tools können Sie auch den Befehl `durable get-instances` mit Filtern verwenden. Zusätzlich zu den oben genannten Parametern `top`, `continuation-token`, `connection-string-setting` und `task-hub-name` können Sie drei Filterparameter (`created-after`, `created-before` und `runtime-status`) verwenden.

* **`created-after` (optional)** : Ruft die nach diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen ab. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`created-before` (optional)** : Ruft die vor diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen ab. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`runtime-status` (optional)** : Rufen Sie die Instanzen mit einem bestimmten Status ab (z.B. „Ausgeführt“ oder „Abgeschlossen“). Es können mehrere Status angegeben werden (Leerzeichen als Trennzeichen).
* **`top` (optional)** : Die Anzahl pro Anforderung abgerufener Instanzen. Der Standardwert ist 10.
* **`continuation-token` (optional)** : Ein Token, das angibt, welche Seite oder welcher Abschnitt der Instanzen abgerufen werden soll. Jede Ausführung von `get-instances` gibt ein Token an den nächsten Satz von Instanzen zurück.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Wenn Sie keine Filter angeben (`created-after`, `created-before` oder `runtime-status`), ruft der Befehl einfach unabhängig von Laufzeitstatus oder Erstellungszeit `top`-Instanzen ab.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Beenden von Instanzen

Wenn die Ausführung einer Orchestrierungsinstanz zu lange dauert, oder Sie sie einfach aus beliebigem Grund vor dem Abschluss beenden müssen, können Sie sie beenden.

Sie können die [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)-Methode der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse (.NET) oder die `terminate`-Methode der `DurableOrchestrationClient`-Klasse (JavaScript) verwenden. Die beiden Parameter sind eine `instanceId` und eine `reason`-Zeichenfolge, die in Protokolle und den Instanzenstatus geschrieben werden. Die Ausführung einer beendeten Instanz endet, sobald sie den nächsten `await`-Punkt (.NET) oder `yield`-Punkt (JavaScript) erreicht, oder sofort, wenn sie sich bereits an einem `await`-Punkt oder `yield`-Punkt befindet.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Die Instanzbeendigung wird momentan nicht weitergegeben. Aktivitätsfunktionen und untergeordnete Orchestrierungen werden unabhängig davon, ob Sie die Orchestrierungsinstanz beendet haben, durch die sie aufgerufen wurden, bis zum Ende ausgeführt.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Orchestrierungsinstanz auch direkt mit dem Befehl `durable terminate` von [Azure Functions Core Tools](../functions-run-local.md) beenden. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der zu beendenden Orchestrierungsinstanz.
* **`reason` (optional)** : Der Grund für die Beendigung.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl beendet die Orchestrierungsinstanz mit der ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Senden von Ereignissen an Instanzen

In einigen Szenarien müssen Ihre Orchestratorfunktionen warten und auf externe Ereignisse lauschen können. Dies schließt [Überwachungsfunktionen](durable-functions-concepts.md#monitoring) und Funktionen ein, die auf [menschliche Interaktion](durable-functions-concepts.md#human) warten.

Senden Sie Ereignisbenachrichtigungen mit der [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)-Methode der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse (.NET) oder `raiseEvent`-Methode der `DurableOrchestrationClient`-Klasse (JavaScript) an aktive Instanzen. Instanzen, die auf einen Aufruf von [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) oder `waitForExternalEvent` (JavaScript) warten, können diese Ereignisse verarbeiten.

Die Parameter für [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) und `raiseEvent` (JavaScript) lauten wie folgt:

* **InstanceId**: Die eindeutige ID der Instanz.
* **EventName**: Der Name des zu sendenden Ereignisses.
* **EventData**: Eine JSON-serialisierbare Nutzlast, die an die Instanz gesendet werden soll.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Wenn keine Orchestrierungsinstanz mit der angegebenen Instanz-ID vorhanden ist, oder die Instanz nicht auf den angegebenen Ereignisnamen wartet, wird die Ereignisnachricht verworfen. Weitere Informationen zu diesem Verhalten finden Sie unter [Expected behavior when unexpected event is received?](https://github.com/Azure/azure-functions-durable-extension/issues/29) (Erwartetes Verhalten, wenn ein unerwartetes Ereignis empfangen wird?).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können ein Ereignis für eine Orchestrierungsinstanz auch direkt mit dem Befehl `durable raise-event` von [Azure Functions Core Tools](../functions-run-local.md) auslösen. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`event-name` (optional)** : Der Name des auszulösenden Ereignisses. Der Standardwert lautet `$"Event_{RandomGUID}"`.
* **`event-data` (optional)** : Die an die Orchestrierungsinstanz zu sendenden Daten. Dies kann der Pfad zu einer JSON-Datei sein, oder Sie können die Daten direkt in der Befehlszeile eingeben.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Warten auf den Abschluss der Orchestrierung

Bei lange andauernden Orchestrierungen möchten Sie vielleicht warten und die Ergebnisse einer Orchestrierung erhalten. In diesen Fällen ist es auch sinnvoll, ein Timeout für die Orchestrierung definieren zu können. Wenn das Timeout überschritten wird, sollte der Status der Orchestrierung anstelle der Ergebnisse zurückgegeben werden.

Die [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse macht eine [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_)-API in .NET verfügbar. Sie können diese API verwenden, um synchron die tatsächliche Ausgabe einer Orchestrierungsinstanz zu erhalten. In JavaScript macht die `DurableOrchestrationClient`-Klasse eine `waitForCompletionOrCreateCheckStatusResponse`-API für den gleichen Zweck verfügbar. Wenn keine Werte festgelegt werden, wird für die Methoden ein Standardwert von 10 Sekunden für `timeout` und von 1 Sekunde für `retryInterval` verwendet.  

Hier ist ein Beispiel für eine HTTP-Triggerfunktion angegeben, mit dem die Nutzung dieser API veranschaulicht wird:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Rufen Sie die Funktion mit der folgenden Zeile auf. Verwenden Sie 2 Sekunden für das Timeout und 0,5 Sekunden für das Wiederholungsintervall:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Je nach Zeitraum, der zum Abrufen der Antwort aus der Orchestrierungsinstanz erforderlich ist, gibt es zwei Fälle:

* Die Orchestrierungsinstanzen werden innerhalb des definierten Zeitraums (hier: 2 Sekunden) abgeschlossen, und die Antwort ist die tatsächliche Orchestrierungsinstanzausgabe, die synchron bereitgestellt wird:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Die Orchestrierungsinstanzen können nicht innerhalb des definierten Timeouts abgeschlossen werden, und die Antwort ist die Standardantwort, die unter [Ermittlung der HTTP-API-URL](durable-functions-http-api.md) beschrieben ist:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Das Format der Webhook-URLs kann in Abhängigkeit davon variieren, welche Version des Azure Functions-Hosts Sie ausführen. Das obige Beispiel gilt für den Azure Functions 2.x-Host.

## <a name="retrieve-http-management-webhook-urls"></a>Abrufen von HTTP-Management-Webhook-URLs

Sie können ein externes System zum Überwachen oder Auslösen von Ereignissen bei einer Orchestrierung verwenden. Externe Systeme können mit Durable Functions über die Webhook-URLs kommunizieren, die Teil der in [HTTP-APIs in Durable Functions (Azure Functions)](durable-functions-http-api.md) beschriebenen Standardantwort sind. Allerdings kann auf die Webhook-URLs auch programmgesteuert im Orchestrierungsclient oder in einer Aktivitätsfunktion zugegriffen werden. Verwenden Sie hierzu die [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)-Methode der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse (.NET) oder die `createHttpManagementPayload`-Methode der `DurableOrchestrationClient`-Klasse (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) und `createHttpManagementPayload` verfügen über einen Parameter:

* **instanceId**: Die eindeutige ID der Instanz.

Die Methoden geben eine Instanz von [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) bzw. ein Objekt (JavaScript) mit den folgenden Zeichenfolgeneigenschaften zurück:

* **Id:** Die Instanz-ID der Orchestrierung (muss mit der Eingabe `InstanceId` identisch sein).
* **StatusQueryGetUri**: Status-URL der Orchestrierungsinstanz
* **SendEventPostUri**: URL der Orchestrierungsinstanz für die „Ereignisauslösung“
* **TerminatePostUri**: URL der Orchestrierungsinstanz für die „Beendigung“
* **RewindPostUri**: URL der Orchestrierungsinstanz für den „Rücklauf“

Aktivitätsfunktionen können zum Überwachen oder Auslösen von Ereignissen in einer Orchestrierung eine Instanz dieser Objekte an externe Systeme senden:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Zurückspulen von Instanzen (Vorschau)

Wenn bei einer Orchestrierung aus unerwartetem Grund Fehler auftreten, können Sie die Instanz mithilfe einer für diesen Zweck erstellten API auf einen fehlerfreien früheren Zustand *zurückspulen*.

> [!NOTE]
> Diese API ist nicht als Ersatz für ordnungsgemäße Richtlinien zur Fehlerbehandlung und Wiederholung gedacht. Sie soll nur in Fällen verwendet werden, in denen Orchestrierungsinstanzen aus unerwarteten Gründen ausfallen. Weitere Informationen zu Richtlinien für Fehlerbehandlung und Wiederholung finden Sie im Thema [Fehlerbehandlung](durable-functions-error-handling.md).

Setzen Sie die Orchestrierung mit der [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_)-API (.NET) oder `rewindAsync`-API (JavaScript) in den Zustand *Wird ausgeführt* zurück. Führen Sie die Aktivität oder untergeordnete Orchestrierung, die den Orchestrierungsfehler verursacht hat, erneut aus.

Stellen Sie sich als Beispiel einen Workflow vor, der eine Reihe von [menschlichen Genehmigungen](durable-functions-concepts.md#human) umfasst. Angenommen, eine Reihe von Aktivitätsfunktionen benachrichtigt jemanden, dass seine Genehmigung erforderlich ist, und wartet in Echtzeit auf die Antwort. Stellen Sie sich vor, dass nachdem alle Genehmigungsaktivitäten Antworten erhalten haben oder abgelaufen sind, aufgrund einer Fehlkonfiguration einer Anwendung, z.B. einer ungültige Datenbank-Verbindungszeichenfolge, ein Fehler bei einer anderen Aktivität auftritt. Das Ergebnis ist ein Orchestrierungsfehler tief im Workflow. Mit der `RewindAsync`-API (.NET) oder `rewindAsync`-API (JavaScript) kann ein Anwendungsadministrator den Konfigurationsfehler beheben und die fehlerhafte Orchestrierung zum Zustand unmittelbar vor dem Fehler zurückspulen. Keiner der Schritte, die menschliche Interaktion erforderten, muss erneut genehmigt werden, und die Orchestrierung kann jetzt erfolgreich abgeschlossen werden.

> [!NOTE]
> Die Funktion *Zurückspulen* unterstützt kein Zurückspulen von Orchestrierungsinstanzen, die permanente Timer verwenden.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Orchestrierungsinstanz auch direkt mit dem Befehl `durable rewind` von [Azure Functions Core Tools](../functions-run-local.md) zurückspulen. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`reason` (optional)** : Der Grund für das Zurückspulen der Orchestrierungsinstanz.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Löschen des Instanzverlaufs

Um alle Daten im Zusammenhang mit einer Orchestrierung zu entfernen, können Sie den Instanzverlauf löschen. Sie möchten beispielsweise ggf. vorhandene Azure Table-Zeilen und große Nachrichtenblobs entfernen. Verwenden Sie hierzu die [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)-API.

> [!NOTE]
> Die `PurgeInstanceHistoryAsync`-API ist derzeit nur für C# verfügbar.

 Diese Methode weist zwei Überladungen auf. Bei der ersten wird der Verlauf durch die ID der Orchestrierungsinstanz gelöscht:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Das zweite Beispiel zeigt eine per Timer ausgelöste Funktion, die den Verlauf für alle Orchestrierungsinstanzen löscht, die nach Ablauf der angegebenen Zeitspanne abgeschlossen wurden. In diesem Fall werden die Daten für alle Instanzen entfernt, die vor mindestens 30 Tagen abgeschlossen wurden. Die Ausführung der Funktion ist einmal pro Tag um 24 Uhr geplant:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> Damit die zeitausgelöste Funktionsausführung erfolgreich verläuft, ist der Laufzeitstatus **Abgeschlossen**, **Beendet** oder **Fehler** erforderlich.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können den Verlauf einer Orchestrierungsinstanz mit dem Befehl `durable purge-history` von [Azure Functions Core Tools](../functions-run-local.md) löschen. Ähnlich wie im zweiten C#-Beispiel im vorherigen Abschnitt wird der Verlauf für alle Orchestrierungsinstanzen gelöscht, die während eines angegebenen Zeitintervalls erstellt wurden. Sie können gelöschte Instanzen weiter nach dem Laufzeitstatus filtern. Der Befehl verfügt über mehrere Parameter:

* **`created-after` (optional)** : Löscht den Verlauf der nach diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`created-before` (optional)** : Löscht den Verlauf der vor diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`runtime-status` (optional)** : Löschen Sie den Verlauf von Instanzen mit einem bestimmten Status (z.B. „Ausgeführt“ oder „Abgeschlossen“). Es können mehrere Status angegeben werden (Leerzeichen als Trennzeichen).
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl löscht den Verlauf aller fehlerhaften Instanzen, die vor dem 14. November 2018 um 19:35 Uhr (UTC) erstellt wurden.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Löschen eines Aufgabenhubs

Mithilfe des Befehls `durable delete-task-hub` von [Azure Functions Core Tools](../functions-run-local.md) können Sie alle Speicherartefakte löschen, die einem bestimmten Aufgabenhub zugeordnet sind. Dies schließt Azure Storage-Tabellen, Warteschlangen und Blobs ein. Der Befehl verfügt über zwei Parameter:

* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl löscht alle Azure Storage-Daten, die dem Aufgabenhub `UserTest` zugeordnet sind.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [HTTP APIs in Durable Functions (Azure Functions)](durable-functions-http-api.md) (HTTP-APIs in Durable Functions [Azure Functions])
