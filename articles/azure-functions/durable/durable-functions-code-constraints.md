---
title: Codeeinschränkungen für dauerhafte Orchestratoren – Azure Functions
description: Wiedergabe- und Codeeinschränkungen für Azure Durable Functions im Zusammenhang mit Orchestrierungsfunktionen.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: dc301cf7149ad9fcd5bd5c02226afedc4df5e3ee
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833094"
---
# <a name="orchestrator-function-code-constraints"></a>Codeeinschränkungen für Orchestratorfunktionen

Durable Functions ist eine Erweiterung von [Azure Functions](../functions-overview.md), mit der Sie zustandsbehaftete Apps erstellen können. Mit einer [Orchestratorfunktion](durable-functions-orchestrations.md) können Sie die Ausführung anderer dauerhafter Funktionen innerhalb einer Funktions-App orchestrieren. Orchestratorfunktionen sind zustandsbehaftet, zuverlässig und können lange ausgeführt werden.

## <a name="orchestrator-code-constraints"></a>Einschränkungen des Orchestratorcodes

Orchestratorfunktionen nutzen [Ereignissourcing](/azure/architecture/patterns/event-sourcing), um eine zuverlässige Ausführung zu gewährleisten und den lokalen Variablenzustand beizubehalten. Mit dem [Wiedergabeverhalten](durable-functions-orchestrations.md#reliability) von Orchestratorcode sind Einschränkungen des Codetyps verbunden, der in einer Orchestratorfunktion geschrieben werden kann. Beispielsweise müssen Orchestratorfunktionen *deterministisch* sein: Eine Orchestratorfunktion wird mehrmals wiedergegeben und muss jedes Mal das gleiche Ergebnis liefern.

### <a name="using-deterministic-apis"></a>Verwenden deterministischer APIs

Dieser Abschnitt enthält einige einfache Richtlinien, die sicherstellen, dass Ihr Code deterministisch ist.

Orchestratorfunktionen können jede API in ihren Zielsprachen aufrufen. Es ist jedoch wichtig, dass Orchestratorfunktionen nur deterministische APIs aufrufen. Eine *deterministische API* ist eine API, die bei Verwendung der gleichen Eingabe immer den gleichen Wert zurückgibt, und zwar unabhängig davon, wann oder wie oft sie aufgerufen wird.

Die folgende Tabelle zeigt einige Beispiele für APIs, die *nicht* deterministisch sind und daher nicht verwendet werden sollten. Diese Einschränkungen gelten nur für Orchestratorfunktionen. Für andere Funktionstypen gelten keine Einschränkungen dieser Art.

| API-Kategorie | `Reason` | Problemumgehung |
| ------------ | ------ | ---------- |
| Datums- und Zeitangaben  | APIs, die das aktuelle Datum oder die aktuelle Uhrzeit zurückgeben, sind nicht deterministisch, da der Rückgabewert bei jeder Wiedergabe unterschiedlich ist. | Verwenden Sie die [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime)-Eigenschaft in .NET, die `currentUtcDateTime`-API in JavaScript oder die `current_utc_datetime`-API in Python, die für die Wiedergabe sicher sind. |
| GUIDs und UUIDs  | APIs, die eine zufällige GUID oder UUID zurückgeben, sind nicht deterministisch, da der generierte Wert bei jeder Wiedergabe unterschiedlich ist. | Verwenden Sie [NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) in .NET oder `newGuid` in JavaScript zur sicheren Generierung von Zufalls-GUIDs. |
| Zufallszahlen | APIs, die eine zufällige Zahlen zurückgeben, sind nicht deterministisch, da der generierte Wert bei jeder Wiedergabe unterschiedlich ist. | Verwenden Sie eine Aktivitätsfunktion, um Zufallszahlen an eine Orchestrierung zurückzugeben. Die Rückgabewerte von Aktivitätsfunktionen können immer für die Wiedergabe verwendet werden. |
| Bindungen | Eingabe- und Ausgabebindungen werden in der Regel für Eingaben/Ausgaben genutzt und sind nicht deterministisch. Eine Orchestratorfunktion darf nicht einmal die Bindungen [Orchestrierungsclient](durable-functions-bindings.md#orchestration-client) und [Entitätsclient](durable-functions-bindings.md#entity-client) direkt verwenden. | Verwenden Sie Eingabe- und Ausgabebindungen innerhalb von Client- oder Aktivitätsfunktionen. |
| Netzwerk | An Netzwerkaufrufen sind externe Systeme beteiligt. Sie sind nicht deterministisch. | Verwenden Sie für Netzwerkaufrufe Aktivitätsfunktionen. Wenn Sie in Ihrer Orchestratorfunktion einen HTTP-Aufruf ausführen müssen, können Sie auch [dauerhafte HTTP-APIs](durable-functions-http-features.md#consuming-http-apis) verwenden. |
| Blockierende APIs | Das Blockieren von APIs wie `Thread.Sleep` in .NET und ähnlichen APIs kann zu Leistungs- und Skalierungsproblemen bei Orchestratorfunktionen führen und sollte vermieden werden. Im Azure Functions-Verbrauchstarif können sie sogar zu unnötigen Gebühren für die Ausführungszeit führen. | Verwenden Sie Alternativen zum Blockieren von APIs, wenn diese verfügbar sind. Verwenden Sie beispielsweise `CreateTimer`, um Verzögerungen bei der Ausführung der Orchestrierung einzuführen. Verzögerungen durch [permanente Timer](durable-functions-timers.md) zählen nicht zur Ausführungszeit einer Orchestratorfunktion. |
| Asynchrone APIs | Orchestratorcode darf niemals einen asynchronen Vorgang starten, sofern nicht die `IDurableOrchestrationContext`-API, die `context.df`-API in JavaScript oder die `context`-API in Python verwendet wird. Beispielsweise können Sie `Task.Run`, `Task.Delay` oder `HttpClient.SendAsync` in .NET oder `setTimeout` und `setInterval` in JavaScript nicht verwenden. Das Durable Task Framework führt Orchestratorcode in einem einzelnen Thread aus. Er kann nicht mit anderen Threads interagieren, die möglicherweise von anderen asynchronen APIs aufgerufen werden. | Eine Orchestratorfunktion sollte ausschließlich dauerhafte asynchrone Aufrufe durchführen. Aktivitätsfunktionen sollten alle anderen asynchronen API-Aufrufe ausführen. |
| Asynchrone JavaScript-Funktion | JavaScript-Orchestratorfunktionen können nicht als `async` deklariert werden, da bei der Node.js-Runtime nicht garantiert ist, dass asynchrone Funktionen deterministisch sind. | Deklarieren von JavaScript-Orchestratorfunktionen als synchrone Generatorfunktionen |
| Python-Coroutinen | Sie können keine Python-Orchestratorfunktionen als Coroutinen deklarieren, d. h. mit dem `async`-Schlüsselwort deklarieren, da die Coroutinensemantik nicht dem Durable Functions-Wiedergabemodell entspricht. | Sie können Python-Orchestratorfunktionen als Generatoren deklarieren, d. h., dass Sie davon ausgehen sollten, dass die `context`-API `yield` anstelle von `await` verwendet.   |
| Threading-APIs | Das Durable Task Framework führt Orchestratorcode über einen einzelnen Thread aus und kann nicht mit anderen Threads interagieren. Die Einbringung neuer Threads in die Ausführung einer Orchestrierung kann zu einer nicht deterministischen Ausführung oder zu Deadlocks führen. | Orchestratorfunktionen sollten so gut wie nie Threading-APIs verwenden. Vermeiden Sie also beispielsweise in .NET die Verwendung von `ConfigureAwait(continueOnCapturedContext: false)`. Dadurch wird sichergestellt, dass Aufgabenfortsetzungen im ursprünglichen Synchronisierungskontext (`SynchronizationContext`) der Orchestratorfunktion ausgeführt werden. Wenn solche APIs erforderlich sind, beschränken Sie die Verwendung ausschließlich auf Aktivitätsfunktionen. |
| Statische Variablen | Die Verwendung nicht konstanter statischer Variablen sollte in Orchestratorfunktionen vermieden werden, da sich ihre Werte im Laufe der Zeit ändern können, was zu einem nicht deterministischen Laufzeitverhalten führt. | Verwenden Sie Konstanten, oder beschränken Sie die Verwendung statischer Variablen auf Aktivitätsfunktionen. |
| Umgebungsvariablen | Verwenden Sie in Orchestratorfunktionen keine Umgebungsvariablen. Deren Werte können sich im Laufe der Zeit ändern, was zu einem nicht deterministischen Laufzeitverhalten führt. | Auf Umgebungsvariablen darf nur innerhalb von Client- oder Aktivitätsfunktionen verwiesen werden. |
| Endlosschleifen | Vermeiden Sie Endlosschleifen in Orchestratorfunktionen. Da das Durable Task Framework den Ausführungsverlauf speichert, während die Orchestrierungsfunktion voranschreitet, kann eine Endlosschleife unter Umständen dazu führen, dass für eine Orchestratorinstanz der Arbeitsspeicher nicht mehr ausreicht. | Verwenden Sie für Szenarien mit Endlosschleifen APIs wie beispielsweise `ContinueAsNew` in .NET, `continueAsNew` in JavaScript oder `continue_as_new` in Python, um die Funktionsausführung neu zu starten und den vorherigen Ausführungsverlauf zu verwerfen. |

Obwohl die Anwendung dieser Einschränkungen zunächst schwierig erscheinen kann, ist sie in der Praxis leicht zu befolgen.

Das Durable Task Framework versucht, Verstöße gegen die oben genannten Regeln zu erkennen. Wenn ein Verstoß gefunden wird, löst das Framework eine **NonDeterministicOrchestrationException**-Ausnahme aus. Dieses Erkennungsverhalten erfasst jedoch nicht alle Verstöße, und Sie sollten sich nicht darauf verlassen.

## <a name="versioning"></a>Versionsverwaltung

Eine dauerhafte Orchestrierung kann ununterbrochen über mehrere Tage, Monate oder Jahre hinweg oder sogar [unendlich lange](durable-functions-eternal-orchestrations.md) ausgeführt werden. Codeaktualisierungen für Durable Functions-Apps, die sich auf noch nicht abgeschlossene Orchestrierungen auswirken, können das Wiedergabeverhalten der Orchestrierungen beeinträchtigen. Daher müssen Codeaktualisierungen sorgfältig geplant werden. Eine ausführlichere Beschreibung der Versionsverwaltung für Ihren Code finden Sie im Artikel zur [Versionsverwaltung](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Durable Tasks (Permanente Vorgänge)

> [!NOTE]
> In diesem Abschnitt werden Details der internen Implementierung für das Durable Task Framework beschrieben. Sie können Durable Functions nutzen, ohne diese Informationen zu kennen. Sie sollen Ihnen nur als Hilfe beim Verständnis des Wiedergabeverhaltens dienen.

Tasks, die in Orchestratorfunktionen auf sichere Weise warten können, werden auch als *Durable Tasks* bezeichnet. Das Durable Task Framework erstellt und verwaltet diese Tasks. Beispiele hierfür sind die Tasks, die von **CallActivityAsync**, **WaitForExternalEvent** und **CreateTimer** in .NET-Orchestratorfunktionen zurückgegeben werden.

Diese dauerhaften Aufgaben werden intern mithilfe einer Liste mit Objekten vom Typ `TaskCompletionSource` in .NET verwaltet. Während der Wiedergabe werden diese Tasks im Rahmen der Orchestratorcodeausführung erstellt. Sie werden abgeschlossen, wenn der Verteiler die entsprechenden Verlaufsereignisse aufzählt.

Die Ausführung der Tasks erfolgt synchron mit einem einzelnen Thread, bis der gesamte Verlauf wiedergegeben wurde. Für alle Durable Tasks, die am Ende der Verlaufswiedergabe nicht abgeschlossen wurden, werden entsprechende Aktionen durchgeführt. Beispielsweise kann eine Nachricht in die Warteschlange eingereiht werden, um eine Aktivitätsfunktion aufzurufen.

Die Beschreibung des Laufzeitverhaltens in diesem Abschnitt soll Ihnen helfen zu verstehen, warum eine Orchestratorfunktion `await` oder `yield` nicht in einem Task verwenden kann, der kein Durable Task ist. Es gibt zwei Gründe: Der Verteilerthread kann nicht warten, bis der Task beendet ist, und jeder Rückruf durch diesen Task könnte den Nachverfolgungsstatus der Orchestratorfunktion möglicherweise beschädigen. Es stehen einige Laufzeitüberprüfungen zur Verfügung, mit denen versucht werden kann, diese Verstöße zu erkennen.

Weitere Informationen zum Ausführen von Orchestratorfunktionen durch das Durable Task Framework finden Sie im Artikel zum [Durable Task-Quellcode auf GitHub](https://github.com/Azure/durabletask). Sehen Sie sich vor allem [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) und [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs) an.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informieren Sie sich über das Aufrufen untergeordneter Orchestrierungen.](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Versioning in Durable Functions (Azure Functions)](durable-functions-versioning.md) (Versionsverwaltung in Durable Functions [Azure Functions])
