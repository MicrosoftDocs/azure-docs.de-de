---
title: Diagnose in Durable Functions – Azure
description: Es wird beschrieben, wie Sie mit der Erweiterung „Durable Functions“ für Azure Functions Probleme diagnostizieren.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 167f697d4928d88114a30739a1d39a576c87ac84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126650"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnose in Durable Functions in Azure

Es gibt mehrere Möglichkeiten, um Probleme mithilfe von [Durable Functions](durable-functions-overview.md) zu diagnostizieren. Einige dieser Optionen sind für reguläre Funktionen identisch, und andere gelten nur für Durable Functions.

## <a name="application-insights"></a>Application Insights

Es wird empfohlen, [Application Insights](../../azure-monitor/app/app-insights-overview.md) zum Durchführen der Diagnose und Überwachung in Azure Functions zu verwenden. Dasselbe gilt für Durable Functions. Eine Übersicht darüber, wie Sie Application Insights in Ihrer Funktionen-App nutzen, finden Sie unter [Überwachen von Azure Functions](../functions-monitoring.md).

Die Erweiterung „Durable Functions“ von Azure Functions gibt auch *Nachverfolgungsereignisse* aus, mit denen Sie die End-to-End-Ausführung einer Orchestrierung verfolgen können. Sie können sie ermitteln und abfragen, indem Sie im Azure-Portal das Tool [Application Insights Analytics](../../azure-monitor/app/analytics.md) verwenden.

### <a name="tracking-data"></a>Nachverfolgungsdaten

Jedes Lebenszyklusereignis einer Orchestrierungsinstanz bewirkt, dass in Application Insights ein Nachverfolgungsereignis in die Sammlung **traces** geschrieben wird. Dieses Ereignis enthält die Nutzlast **customDimensions** mit mehreren Feldern.  Feldnamen wird immer `prop__` vorangestellt.

* **hubName**: Der Name des Aufgabenhubs, unter dem Ihre Orchestrierungen ausgeführt werden.
* **appName**: Der Name der Funktions-App Dies ist nützlich, wenn mehrere Funktionen-Apps dieselbe Application Insights-Instanz gemeinsam nutzen.
* **slotName**: Der [Bereitstellungsslot](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/), in dem die aktuelle Funktions-App ausgeführt wird. Dies ist nützlich, wenn Sie Bereitstellungsslots nutzen, um Ihre Orchestrierungen mit einer Version zu versehen.
* **functionName**: Der Name der Orchestrator- oder Aktivitätsfunktion.
* **functionType**: Der Typ der Funktion, z. B. **Orchestrator** oder **Activity**.
* **instanceId**: Die eindeutige ID der Orchestrierungsinstanz.
* **state**: Der Lebenszyklus-Ausführungsstatus der Instanz. Folgende Werte sind gültig:
  * **Scheduled**: Die Ausführung der Funktion wurde geplant, aber noch nicht gestartet.
  * **Started**: Die Ausführung der Funktion wurde gestartet, sie wurde aber noch nicht erwartet oder abgeschlossen.
  * **Awaited**: Der Orchestrator hat Arbeit geplant und wartet darauf, dass sie abgeschlossen wird.
  * **Listening**: Der Orchestrator lauscht auf eine externe Ereignisbenachrichtigung.
  * **Completed**: Die Funktion wurde erfolgreich abgeschlossen.
  * **Failed**: Bei der Funktion ist ein Fehler aufgetreten.
* **reason**: Zusätzliche Daten zum Nachverfolgungsereignis. Wenn eine Instanz beispielsweise auf eine externe Ereignisbenachrichtigung wartet, wird in diesem Feld der Name des Ereignisses angegeben, auf das gewartet wird. Wenn eine Funktion fehlgeschlagen ist, sind hier die Fehlerdetails enthalten.
* **isReplay**: Ein boolescher Wert, der angibt, ob das Nachverfolgungsereignis für die wiedergegebene Ausführung bestimmt ist.
* **extensionVersion**: Die Version der Durable Task-Erweiterung. Dies ist besonders wichtig, wenn mögliche Fehler der Erweiterung gemeldet werden. Instanzen mit langer Ausführungsdauer melden unter Umständen mehrere Versionen, wenn während der Ausführung ein Update durchgeführt wird.
* **sequenceNumber**: Die Ausführungssequenznummer für ein Ereignis. In Kombination mit dem Zeitstempel können die Ereignisse dadurch nach Ausführungszeit sortiert werden. *Beachten Sie, dass diese Zahl auf null zurückgesetzt wird, wenn der Host neu gestartet wird, während die Instanz ausgeführt wird. Daher ist es wichtig, immer zuerst nach dem Zeitstempel zu sortieren, dann nach „sequenceNumber“.*

Der Ausführlichkeitsgrad der Nachverfolgungsdaten, die an Application Insights ausgegeben werden, kann im Abschnitt `logger` (Functions 1.x) oder `logging` (Functions 2.x) der Datei `host.json` konfiguriert werden.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Standardmäßig werden alle ohne Wiedergabe definierten Nachverfolgungsereignisse ausgegeben. Die Datenmenge kann reduziert werden, indem `Host.Triggers.DurableTask` auf `"Warning"` oder `"Error"` festgelegt wird. In diesem Fall werden Nachverfolgungsereignisse nur für außergewöhnliche Situationen ausgegeben.

Zum Aktivieren der Ausgabe der Wiedergabeereignisse mit ausführlicher Orchestrierung kann `LogReplayEvents` wie im Folgenden gezeigt in der Datei `host.json` unter `durableTask` auf `true` festgelegt werden:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Standardmäßig werden für die Application Insights-Telemetriedaten von der Azure Functions-Laufzeit Stichproben erstellt, um zu verhindern, dass Daten zu häufig ausgegeben werden. Dies kann dazu führen, dass Nachverfolgungsinformationen verloren gehen, wenn viele Lebenszyklusereignisse in kurzer Zeit auftreten. Im [Artikel zur Azure Functions-Überwachung](../functions-monitoring.md#configure-sampling) wird beschrieben, wie Sie dieses Verhalten konfigurieren.

### <a name="single-instance-query"></a>Abfrage für einzelne Instanzen

Mit der folgenden Abfrage werden Verlaufsdaten für die Nachverfolgung einer Einzelinstanz der [Hello Sequence](durable-functions-sequence.md)-Funktionsorchestrierung angezeigt. Sie wurde mit der [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference) geschrieben. Die Wiedergabeausführung wird herausgefiltert, sodass nur der *logische* Ausführungspfad angezeigt wird. Ereignisse können angeordnet werden, indem nach `timestamp` und `sequenceNumber` sortiert wird, wie in der folgenden Abfrage gezeigt:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Das Ergebnis ist eine Liste mit Nachverfolgungsereignissen, die den Ausführungspfad der Orchestrierung anzeigt, z.B. alle Aktivitätsfunktionen, in aufsteigender Reihenfolge nach Ausführungszeit sortiert.

![Application Insights-Abfrage](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Instanz-Zusammenfassungsabfrage

Mit der folgenden Abfrage wird der Status aller Orchestrierungsinstanzen angezeigt, die in einem angegebenen Zeitraum ausgeführt wurden.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Das Ergebnis ist eine Liste mit Instanz-IDs und dem aktuellen Laufzeitstatus.

![Application Insights-Abfrage](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Protokollierung

Es ist wichtig, das Wiedergabeverhalten von Orchestratoren zu beachten, wenn Protokolle direkt über eine Orchestratorfunktion geschrieben werden. Sehen Sie sich beispielsweise die folgende Orchestratorfunktion an:

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Die sich ergebenden Protokolldaten sehen dann in etwa wie folgt aus:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Beachten Sie Folgendes: In den Protokollen ist zwar angegeben, dass F1, F2 und F3 aufgerufen werden, aber diese Funktionen werden *nur* beim ersten Auftreten aufgerufen. Nachfolgende Aufrufe während der Wiedergabe werden übersprungen, und die Ausgaben werden für die Orchestratorlogik wiedergegeben.

Wenn Sie nur die Anmeldung für die Ausführung ohne Wiedergabe durchführen möchten, können Sie einen bedingten Ausdruck schreiben, damit die Anwendung nur erfolgt, sofern `IsReplaying` auf `false` festgelegt ist. Hier ist das obige Beispiel angegeben, aber es enthält jetzt Wiedergabeprüfungen.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Nach dieser Änderung lautet die Protokollausgabe wie folgt:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Benutzerdefinierter Status

Mit dem benutzerdefinierten Orchestrierungsstatus können Sie einen benutzerdefinierten Statuswert für Ihre Orchestratorfunktion festlegen. Dieser Status wird über die HTTP-Statusabfrage-API oder die `DurableOrchestrationClient.GetStatusAsync`-API angegeben. Der benutzerdefinierte Orchestrierungsstatus ermöglicht eine umfassendere Überwachung für Orchestratorfunktionen. Der Orchestratorfunktionscode kann z.B. `DurableOrchestrationContext.SetCustomStatus`-Aufrufe zum Aktualisieren des Status für einen Vorgang mit langer Ausführungsdauer enthalten. Ein Client, z.B. eine Webseite oder ein anderes externes System, kann dann für die HTTP-Statusabfrage-APIs in regelmäßigen Abständen eine Abfrage nach umfangreicheren Statusinformationen durchführen. Nachfolgend ist ein Beispiel unter Verwendung von `DurableOrchestrationContext.SetCustomStatus` aufgeführt:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Während der Ausführung der Orchestrierung können externe Clients diesen benutzerdefinierten Status abrufen:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Clients erhalten folgende Antwort:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Die Nutzlast des benutzerdefinierten Status ist auf UTF-16-JSON-Text mit einer Größe von 16 KB beschränkt, da dieser in eine Azure Table Storage-Spalte passen muss. Für eine größere Nutzlast können Sie externen Speicher verwenden.

## <a name="debugging"></a>Debuggen

Azure Functions unterstützt das direkte Debuggen des Funktionscodes, und diese Unterstützung gilt auch für Durable Functions – unabhängig davon, ob die Ausführung in Azure oder lokal erfolgt. Beim Debuggen sollten aber einige Verhaltensweisen beachtet werden:

* **Wiedergabe**: Orchestratorfunktionen werden regelmäßig wiederholt, wenn neue Eingaben empfangen werden. Dies bedeutet Folgendes: Eine einzelne *logische* Ausführung einer Orchestratorfunktion kann dazu führen, dass derselbe Breakpoint mehrfach erreicht wird. Dies gilt vor allem, wenn er sich im Anfangsbereich des Funktionscodes befindet.
* **Await**: Bei jedem `await`-Ausdruck wird die Steuerung an den Durable Task Framework-Verteiler zurückgegeben. Wenn dies das erste Mal ist, dass ein bestimmtes `await`-Element auftritt, wird die zugeordnete Aufgabe *nie* fortgesetzt. Aus diesem Grund ist das *Überspringen* des Wartezustands (F10 in Visual Studio) nicht möglich. Das Überspringen funktioniert nur, wenn eine Aufgabe wiedergegeben wird.
* **Messagingtimeouts**: In Durable Functions werden intern Warteschlangennachrichten für die Ausführung von Orchestrator- und Aktivitätsfunktionen verwendet. In einer Umgebung mit mehreren VMs kann das Unterbrechen des Debuggens über längere Zeiträume dazu führen, dass eine andere VM die Nachricht aufnimmt. Das Ergebnis wäre dann eine doppelte Ausführung. Dieses Verhalten tritt auch für reguläre Warteschlangentriggerfunktionen auf, aber es ist wichtig, dies in diesem Zusammenhang zu erwähnen, da es sich bei den Warteschlangen um ein Implementierungsdetail handelt.

> [!TIP]
> Gehen Sie wie folgt vor, wenn Sie beim Festlegen von Breakpoints erreichen möchten, dass nur bei Nichtwiedergabeausführungen unterbrochen wird: Legen Sie einen bedingten Breakpoint fest, bei dem nur eine Unterbrechung auftritt, wenn `IsReplaying` den Wert `false` hat.

## <a name="storage"></a>Storage

Durable Functions speichert den Status standardmäßig in Azure Storage. Dies bedeutet, dass Sie den Status Ihrer Orchestrierungen mit Tools wie [Microsoft Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) untersuchen können.

![Screenshot von Azure Storage-Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Dies ist hilfreich beim Debuggen, da Sie genau sehen, in welchem Zustand sich eine Orchestrierung befindet. Nachrichten in den Warteschlangen können ebenfalls untersucht werden, um zu ermitteln, welche Arbeitsschritte ausstehen (oder ggf. hängen).

> [!WARNING]
> Es ist zwar bequem, den Ausführungsverlauf im Tabellenspeicher angezeigt zu bekommen, aber Sie sollten es vermeiden, Abhängigkeiten von dieser Tabelle einzurichten. Dies kann sich im Rahmen der Weiterentwicklung der Erweiterung Durable Functions ändern.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Verwendung von langlebigen Timern](durable-functions-timers.md)
