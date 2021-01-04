---
title: Trigger mit Timer für Azure Functions
description: Erfahren Sie, wie Trigger mit Timer in Azure Functions verwendet werden.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0d9852659801040d64fe4143f024fd52ffec16ee
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874082"
---
# <a name="timer-trigger-for-azure-functions"></a>Trigger mit Timer für Azure Functions

Dieser Artikel erläutert das Arbeiten mit Triggern mit Timer in Azure Functions. Mit einem Trigger mit Timer können Sie eine Funktion nach einem Zeitplan ausführen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Informationen zum manuellen Ausführen einer per Timer ausgelösten Funktion finden Sie unter [Manuelles Ausführen einer Funktion ohne HTTP-Trigger](./functions-manually-run-non-http.md).

## <a name="packages---functions-2x-and-higher"></a>Pakete: Functions 2.x oder höher

Der Zeitgebertrigger wird im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) der Version 3.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Der Zeitgebertrigger wird im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) der Version 2.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die jedes Mal ausgeführt wird, wenn die Minuten einen durch fünf teilbaren Wert haben (wird die Funktion beispielsweise um 18:57:00 Uhr gestartet, erfolgt die nächste Ausführung um 19:00:00 Uhr). Das [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)-Objekt wird an die Funktion übergeben.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Triggerbindung mit Timer in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion schreibt ein Protokoll, das angibt, ob dieser Funktionsaufruf aufgrund eines versäumten Zeitplantermins erfolgt. Das [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)-Objekt wird an die Funktion übergeben.

Bindungsdaten in der Datei *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Der C#-Skriptcode sieht wie folgt aus:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

Die folgende Beispielfunktion wird ausgelöst und alle fünf Minuten ausgeführt. Die `@TimerTrigger`-Anmerkung für die Funktion definiert den Zeitplan mit dem gleichen Zeichenfolgeformat wie [CRON-Ausdrücke](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Triggerbindung mit Timer in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion schreibt ein Protokoll, das angibt, ob dieser Funktionsaufruf aufgrund eines versäumten Zeitplantermins erfolgt. Ein[Timerobjekt](#usage) wird an die Funktion übergeben.

Bindungsdaten in der Datei *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Der JavaScript-Code sieht wie folgt aus:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Im folgenden Beispiel wird veranschaulicht, wie die Dateien *function.json* und *run.ps1* für einen Timertrigger in [PowerShell](./functions-reference-powershell.md) konfiguriert werden.

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Eine Instanz des [timer-Objekts](#usage) wird als erstes Argument an die Funktion übergeben.

# <a name="python"></a>[Python](#tab/python)

Im folgenden Beispiel wird eine Triggerbindung mit Timer verwendet, deren Konfiguration in der Datei *function.json* beschrieben ist. Die eigentliche [Python-Funktion](functions-reference-python.md), von der die Bindung genutzt wird, ist in der Datei *__init__.py* beschrieben. Das an die Funktion übergebene Objekt hat den Typ [azure.functions.TimerRequest-Objekt](/python/api/azure-functions/azure.functions.timerrequest). Mit der Funktionslogik werden Daten in die Protokolle geschrieben, um anzugeben, ob der aktuelle Aufruf aufgrund eines versäumten Zeitplantermins erfolgt.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Dies ist der Python-Code:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Der Konstruktor des Attributs nimmt einen CRON-Ausdruck oder einen `TimeSpan`-Wert an. Sie können `TimeSpan` nur verwenden, wenn die Funktionen-App in einem App Service-Plan ausgeführt wird. `TimeSpan` wird für die Nutzung oder Elastisch Premium-Funktionen nicht unterstützt.

Das folgende Beispiel zeigt einen CRON-Ausdruck:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Die `@TimerTrigger`-Anmerkung für die Funktion definiert den Zeitplan mit dem gleichen Zeichenfolgeformat wie [CRON-Ausdrücke](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `TimerTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „timerTrigger“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf „in“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die das Timerobjekt im Funktionscode darstellt. | 
|**schedule**|**ScheduleExpression**|Ein [CRON-Ausdruck](#ncrontab-expressions) oder ein [TimeSpan](#timespan)-Wert. `TimeSpan` kann nur für eine Funktionen-App verwendet werden, die in einem App Service-Plan ausgeführt wird. Sie können den Zeitplanausdruck in eine App-Einstellung einfügen und diese Eigenschaft auf den Namen der App-Einstellung festlegen, der wie in diesem Beispiel **%** -Zeichen als Wrapper verwendet: „%ScheduleAppSetting%“. |
|**runOnStartup**|**RunOnStartup**|Wenn `true`, wird die Funktion beim Starten der Laufzeit aufgerufen. Die Laufzeit startet beispielsweise, wenn die Funktionen-App nach dem Leerlauf aufgrund von Inaktivität reaktiviert wird, wenn die Funktionen-App aufgrund von Funktionsänderungen neu gestartet wird und wenn die Funktionen-App horizontal hochskaliert wird. Daher sollte **runOnStartup** selten, wenn überhaupt, auf `true` festgelegt werden, insbesondere in der Produktionsumgebung. |
|**useMonitor**|**UseMonitor**|Legen Sie diese Eigenschaft auf `true` oder `false` fest, um anzugeben, ob der Zeitplan überwacht werden soll. Durch die Überwachung des Zeitplans werden Zeitplantermine beibehalten, mit deren Hilfe sichergestellt werden kann, dass der Zeitplan richtig eingehalten wird, selbst wenn Instanzen der Funktionen-App neu gestartet werden. Wenn diese Eigenschaft nicht explizit festgelegt wird, lautet der Standardwert `true` für Zeitpläne mit einem Wiederholungsintervall von mehr als oder gleich einer Minute. Bei Zeitplänen, die mehr als einmal pro Minute ausgelöst werden, lautet der Standardwert `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Es wird empfohlen, **runOnStartup** in der Produktionsumgebung nicht auf `true` festzulegen. Bei Verwendung dieser Einstellung wird der Code zu schwer vorhersehbaren Zeiten ausgeführt. In bestimmten Produktionseinstellungen können diese zusätzlichen Ausführungen zu deutlich höheren Kosten für Anwendungen führen, die in nutzungsbasierten Tarifen gehostet werden. So wird z. B. der Trigger immer dann mit aktiviertem **runOnStartup** aufgerufen, wenn Ihre Funktions-App skaliert wird. Stellen Sie sicher, dass Sie das Produktionsverhalten Ihrer Funktionen vollständig verstehen, bevor Sie **runOnStartup** in der Produktionsumgebung aktivieren.

## <a name="usage"></a>Verwendung

Bei Aufruf einer Trigger-mit-Timer-Funktion wird ein Timerobjekt an die Funktion übergeben. Der folgende JSON-Code ist eine Beispieldarstellung des Timerobjekts.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

Die Eigenschaft `isPastDue` lautet `true`, wenn der aktuelle Funktionsaufruf später als geplant erfolgt. Beispielsweise kann ein Neustart der Funktionen-App dazu führen, dass ein Aufruf nicht erkannt wird.

## <a name="ncrontab-expressions"></a>NCRONTAB-Ausdrücke

Azure Functions verwendet die Bibliothek [NCronTab](https://github.com/atifaziz/NCrontab), um NCRONTAB-Ausdrücke zu interpretieren. Ein NCRONTAB-Ausdruck ähnelt einem CRON-Ausdruck, enthält jedoch am Anfang ein zusätzliches sechstes Feld für sekundengenaue Zeitangaben:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Jedes Feld kann einen der folgenden Werttypen aufweisen:

|type  |Beispiel  |Auslösung  |
|---------|---------|---------|
|Ein bestimmter Wert |<nobr>`0 5 * * * *`</nobr>| Ein Mal pro Stunde am Tag bei Minute 5 jeder Stunde |
|Alle Werte (`*`)|<nobr>`0 * 5 * * *`</nobr>| Jede Minute der Stunde, beginnend bei Stunde 5 |
|Ein Bereich (`-`-Operator)|<nobr>`5-7 * * * * *`</nobr>| Drei Mal pro Minute, bei den Sekunden 5 bis 7 pro Minute pro Stunde jeden Tag |
|Eine Gruppe von Werten (`,`-Operator)|<nobr>`5,8,10 * * * * *`</nobr>| Drei Mal pro Minute, bei den Sekunden 5, 8 und 10 pro Minute pro Stunde jeden Tag |
|Ein Intervallwert (`/`-Operator)|<nobr>`0 */5 * * * *`</nobr>| 12 Mal pro Stunde, bei Sekunde 0 jeder 5. Minute jeder Stunde jedes Tages |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-Beispiele

Die folgenden Beispiele zeigen NCRONTAB-Ausdrücke, die Sie für den Trigger mit Timer in Azure Functions verwenden können:

| Beispiel            | Auslösung                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | einmal alle fünf Minuten            |
| `0 0 * * * *`      | einmal zu jeder vollen Stunde      |
| `0 0 */2 * * *`    | einmal alle zwei Stunden               |
| `0 0 9-17 * * *`   | zwischen 9:00 und 17:00 Uhr jeweils einmal pro Stunde  |
| `0 30 9 * * *`     | täglich um 9:30 Uhr               |
| `0 30 9 * * 1-5`   | werktags um 9:30 Uhr           |
| `0 30 9 * Jan Mon` | jeden Montag im Januar um 9:30 |

> [!NOTE]
> Der NCRONTAB-Ausdruck erfordert das Format **six field** (sechs Felder). Die sechste Feldposition ist ein Wert für Sekunden, der am Anfang des Ausdrucks platziert wird. Cron-Ausdrücke mit fünf Feldern werden in Azure nicht unterstützt.

### <a name="ncrontab-time-zones"></a>NCRONTAB-Zeitzonen

Die Zahlen in einem CRON-Ausdruck beziehen sich auf ein Datum und eine Uhrzeit, nicht auf einen Zeitraum. Beispielsweise bezieht sich eine 5 im Feld `hour` auf 5:00 Uhr, nicht auf alle 5 Stunden.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 `TimeSpan` kann nur für eine Funktionen-App verwendet werden, die in einem App Service-Plan ausgeführt wird.

Im Gegensatz zu einem CRON-Ausdruck gibt ein `TimeSpan`-Wert das Zeitintervall zwischen den einzelnen Funktionsaufrufen an. Wenn eine Funktion abgeschlossen wird, nachdem sie länger als über das angegebene Intervall ausgeführt wurde, ruft der Timer die Funktion sofort erneut auf.

Dieser Wert wird als Zeichenfolge ausgedrückt, und das `TimeSpan`-Format ist `hh:mm:ss`, wenn `hh` kleiner ist als 24. Wenn die ersten beiden Ziffern 24 oder höher sind, ist das Format `dd:hh:mm`. Im Folgenden finden Sie einige Beispiele:

| Beispiel      | Auslösung |
|--------------|----------------|
| "01:00:00"   | stündlich     |
| "00:01:00"   | minütlich   |
| "24:00:00"   | alle 24 Tage  |
| „1.00:00:00“ | täglich      |

## <a name="scale-out"></a>Horizontales Skalieren

Wenn eine Funktionen-App auf mehrere Instanzen horizontal hochskaliert wird, wird nur eine einzige Instanz einer per Timer ausgelösten Funktion für alle Instanzen ausgeführt.

## <a name="function-apps-sharing-storage"></a>Funktionen-Apps mit gemeinsamer Nutzung von Speicher

Wenn Sie Speicherkonten für Funktions-Apps freigeben, die nicht für App Service bereitgestellt sind, müssen Sie möglicherweise jeder App explizit die Host-ID zuweisen.

| Functions-Version | Einstellung                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (oder höher)  | Umgebungsvariable `AzureFunctionsWebHost__hostid` |
| 1.x               | `id` in *host.json*                                  |

Sie können den identifizierenden Wert auslassen oder die identifizierende Konfiguration für jede Funktions-App manuell auf einen anderen Wert festlegen.

Der Trigger mit Timer verwendet eine Speichersperre, um sicherzustellen, dass nur eine Timerinstanz vorhanden ist, wenn eine Funktions-App auf mehrere Instanzen horizontal hochskaliert wird. Wenn zwei Funktions-Apps dieselbe identifizierende Konfiguration aufweisen und jede einen Trigger mit Timer verwendet, wird nur ein Timer ausgeführt.

## <a name="retry-behavior"></a>Wiederholungsverhalten

Im Gegensatz zum Warteschlangentrigger führt der Trigger mit Timer nach dem Fehlschlagen einer Funktion keine Wiederholung aus. Wenn eine Funktion fehlerhaft ist, wird sie erst beim nächsten Termin im Zeitplan erneut aufgerufen.

## <a name="troubleshooting"></a>Problembehandlung

Informationen zur Behebung bei Problemen mit dem Zeitgebertrigger finden Sie unter [Investigating and reporting issues with timer triggered functions not firing](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing) (Untersuchen und Melden von Problemen beim Auslösen zeitgesteuerter Triggerfunktionen).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zu einem Schnellstart navigieren, der einen Trigger mit Timer verwendet](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
