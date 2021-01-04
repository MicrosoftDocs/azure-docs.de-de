---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356192"
---
In Azure Functions auftretende Fehler können einen der folgenden Ursprünge haben:

- Verwendung integrierter Azure Functions-[Trigger und -Bindungen](..\articles\azure-functions\functions-triggers-bindings.md)
- Aufrufe an APIs zugrunde liegender Azure-Dienste
- Aufrufe an REST-Endpunkte
- Aufrufe an Clientbibliotheken, Pakete oder Drittanbieter-APIs

Die folgenden bewährten Methoden für die Fehlerbehandlung sind wichtig, um den Verlust von Daten oder verpasste Nachrichten zu vermeiden. Empfohlene Vorgehensweisen zur Fehlerbehandlung umfassen folgende Aktionen:

- [Application Insights aktivieren](../articles/azure-functions/functions-monitoring.md)
- [Verwenden strukturierter Fehlerbehandlung](#use-structured-error-handling)
- [Design für Idempotenz](../articles/azure-functions/functions-idempotent.md)
- [Implementieren von Wiederholungsrichtlinien](#retry-policies-preview) (falls zutreffend)

### <a name="use-structured-error-handling"></a>Verwenden strukturierter Fehlerbehandlung

Das Erfassen und Protokollieren von Fehlern ist kritisch für die Überwachung der Integrität Ihrer Anwendung. Die oberste Ebene jedes Funktioncodes sollte einen try/catch-Block enthalten. Im catch-Block können Sie Fehler erfassen und protokollieren.

## <a name="retry-policies-preview"></a>Wiederholungsrichtlinien (Vorschauversion)

Eine Wiederholungsrichtlinie kann für beliebige Funktionen und beliebige Triggertypen in Ihrer Funktions-App definiert werden.  Die Wiederholungsrichtlinie führt eine Funktion so lange erneut aus, bis sie entweder erfolgreich ausgeführt oder die maximale Anzahl an Wiederholungen erreicht wurde.  Wiederholungsrichtlinien können für alle Funktionen in einer App oder für einzelne Funktionen definiert werden.  Standardmäßig werden Nachrichten von einer Funktions-App nicht wiederholt (eine Ausnahme bilden [spezielle Trigger mit eine Wiederholungsrichtlinie an der Triggerquelle](#using-retry-support-on-top-of-trigger-resilience)).  Eine Wiederholungsrichtlinie wird immer dann ausgewertet, wenn eine Ausführung zu einer nicht abgefangenen Ausnahme führt.  Es hat sich bewährt, alle Ausnahmen in Ihrem Code abzufangen und alle Fehler erneut auszulösen, die zu einem Wiederholungsversuch führen sollten.  Event Hubs- und Azure Cosmos DB-Prüfpunkte werden erst geschrieben, wenn die Wiederholungsrichtlinie für die Ausführung abgeschlossen wurde. Das bedeutet, dass die Verarbeitung auf dieser Partition so lange angehalten wird, bis der aktuelle Batch abgeschlossen ist.

### <a name="retry-policy-options"></a>Optionen für Wiederholungsrichtlinien

Die folgenden Optionen sind für das Definieren einer Wiederholungsrichtlinie verfügbar.

**maxRetryCount** ist die maximale Anzahl von Wiederholungsversuchen für eine Ausführung, bis diese endgültig als fehlerhaft gilt. Der Wert `-1` bedeutet unbegrenzte Wiederholungen. Die aktuelle Wiederholungsanzahl wird im Arbeitsspeicher der Instanz gespeichert. Es kann vorkommen, dass bei einer Instanz zwischen Wiederholungsversuchen ein Fehler auftritt.  Wenn auf einer Instanz während einer Wiederholungsrichtlinie ein Fehler auftritt, geht die Zählung der Wiederholungsversuche verloren.  Bei Instanzfehlern können Trigger wie Event Hubs, Azure Cosmos DB und Queue Storage die Verarbeitung fortsetzen und den Batch auf einer neuen Instanz wiederholen. Dabei wird die Wiederholungsanzahl auf 0 (null) zurückgesetzt.  Andere Trigger, wie z. B. HTTP und Zeitgeber, werden nicht auf einer neuen Instanz fortgesetzt.  Dies bedeutet, dass die maximale Anzahl von Wiederholungsversuchen lediglich ein Ziel ist. In einigen seltenen Fällen kann eine Ausführung häufiger als der Maximalwert oder bei Triggern wie HTTP und Zeitgebern seltener als der Maximalwert wiederholt werden.

Die **Wiederholungsstrategie** steuert das Verhalten der Wiederholungen.  Im Folgenden finden Sie zwei unterstützte Wiederholungsoptionen:

| Option | Beschreibung|
|---|---|
|**`fixedDelay`**| Zwischen den einzelnen Wiederholungsversuchen kann eine angegebene Zeitspanne vergehen.|
| **`exponentialBackoff`**| Bei der ersten Wiederholung wird die minimale Verzögerung gewartet. Bei nachfolgenden Wiederholungen wird exponentiell Zeit zur anfänglichen Dauer addiert, bis die maximale Verzögerung erreicht ist.  Das exponentielle Backoff fügt den Verzögerungen einen geringfügigen Zufallswert hinzu, um Verzögerungen in Szenarien mit hohem Durchsatz zu staffeln.|

#### <a name="app-level-configuration"></a>Konfiguration auf App-Ebene

Eine Wiederholungsrichtlinie kann [mithilfe der Datei `host.json`](../articles/azure-functions/functions-host-json.md#retry) für alle Funktionen in einer App definiert werden. 

#### <a name="function-level-configuration"></a>Konfiguration auf Funktionsebene

Eine Wiederholungsrichtlinie kann für eine bestimmte Funktion definiert werden.  Die funktionsspezifische Konfiguration hat Vorrang vor der Konfiguration auf App-Ebene.

#### <a name="fixed-delay-retry"></a>Wiederholung mit fester Verzögerung

# <a name="c"></a>[C#](#tab/csharp)

Für Wiederholungen ist das NuGet-Paket [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 erforderlich.

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Wiederholungsrichtlinie in der Datei *function.json*:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Wiederholungsrichtlinie in der Datei *function.json*:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wiederholungsrichtlinie in der Datei *function.json*:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Wiederholung mit exponentiellem Backoff

# <a name="c"></a>[C#](#tab/csharp)

Für Wiederholungen ist das NuGet-Paket [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 erforderlich.

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wiederholungsrichtlinie in der Datei *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|Eigenschaft von „function.json“  |Attributeigenschaft | Beschreibung |
|---------|---------|---------| 
|strategy|–|Erforderlich. Die Wiederholungsstrategie, die verwendet werden soll. Gültige Werte sind `fixedDelay` und `exponentialBackoff`.|
|maxRetryCount|–|Erforderlich. Die maximale Anzahl zulässiger Wiederholungen pro Funktionsausführung. `-1` bedeutet unbegrenzte Wiederholungen.|
|delayInterval|–|Die Verzögerung, die zwischen Wiederholungsversuchen bei Verwendung der Strategie `fixedDelay`.|
|MinimumIntervall|–|Die geringste Wiederholungsverzögerung bei Verwendung der Strategie `exponentialBackoff`.|
|Maximumintervall|–|Die höchste Wiederholungsverzögerung bei Verwendung der Strategie `exponentialBackoff`.| 

### <a name="retry-limitations-during-preview"></a>Einschränkungen bei Wiederholungen in der Vorschauversion

- Bei .NET-Projekten müssen Sie möglicherweise eine Version von [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) > = 3.0.23 manuell abrufen.
- Im Verbrauchstarif kann die App auf null herunterskaliert werden, während Wiederholungen für die letzten Nachrichten in einer Warteschlange ausgeführt werden.
- Im Verbrauchstarif kann die App beim Ausführen von Wiederholungen herunterskaliert werden.  Um optimale Ergebnisse zu erzielen, wählen Sie ein Wiederholungsintervall < = 00:01:00 und Wiederholungen < = 5 aus.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Verwenden der Wiederholungsunterstützung zusätzlich zur Ausfallsicherheit des Auslösers

Die Wiederholungsrichtlinie der Funktions-App ist unabhängig von den Wiederholungen oder der Resilienz des Triggervorgangs.  Die Wiederholungsrichtlinie für die Funktion wird nur zusätzlich zu einer ausfallfähigen Wiederholung verwendet.  Wenn Sie z. B. Azure Service Bus verwenden, haben Warteschlangen standardmäßig den Wert 10 für die Nachrichtenübermittlung.  Die Standardübermittlungsanzahl bedeutet, dass eine Warteschlangennachricht nach 10 Zustellungsversuchen von Service Bus als unzustellbar deklariert wird.  Sie können eine Wiederholungsrichtlinie für eine Funktion mit Service Bus-Trigger definieren, die Wiederholungen werden jedoch zusätzlich zu Service Bus-Zustellungsversuchen ausgeführt.  

Angenommen, Sie verwenden die standardmäßige Service Bus-Übermittlungsanzahl von 10 und haben eine Wiederholungsrichtlinie für die Funktion von 5 definiert.  Die Nachricht wird dann zuerst aus der Warteschlange entfernt, und der Service Bus-Übermittlungszähler wird um 1 erhöht.  Wenn bei jeder Ausführung ein Fehler aufgetreten ist, wird die Nachricht nach fünf Versuchen, dieselbe Nachricht auszulösen, als verworfen markiert.  Service Bus stellt die Nachricht sofort wieder in die Warteschlange, löst die Funktion aus und erhöht die Übermittlungsanzahl auf 2.  Schließlich wird nach 50 erfolglosen Versuchen (10 Service Bus-Übermittlungen * fünf Funktionswiederholungen pro Übermittlung) die Nachricht verworfen, und es wird eine unzustellbare Nachricht in Service Bus ausgelöst.

> [!WARNING]
> Es wird nicht empfohlen, die Übermittlungsanzahl für einen Trigger wie Service Bus-Warteschlangen auf 1 festzulegen. Dies würde bedeuten, dass die Nachricht unmittelbar nach einem Wiederholungsversuch einer einzelnen Funktion als unzustellbar erklärt wird.  Der Grund hierfür ist, dass Trigger die Resilienz bei Wiederholungsversuchen bestimmen, während die Wiederholungsrichtlinie der Funktion dem Prinzip der besten Leistung folgt und möglicherweise weniger als die gewünschte Gesamtzahl der Wiederholungen ausgeführt werden.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Trigger mit zusätzlicher Resilienz oder zusätzlichen Wiederholungen

Die folgenden Trigger unterstützen Wiederholungen an der Triggerquelle:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Warteschlange/Thema)](../articles/azure-functions/functions-bindings-service-bus.md)

Standardmäßig wiederholen die meisten Trigger Anforderungen bis zu fünfmal. Nach dem fünften Wiederholungsversuch schreibt Azure Queue Storage eine Nachricht in die [Warteschlange für nicht verarbeitbare Nachrichten](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).  Die Standard-Service Bus-Warteschlange und die Themenrichtlinie schreiben eine Nachricht nach 10 Versuchen in die [Warteschlange für unzustellbare Nachrichten](../articles/service-bus-messaging/service-bus-dead-letter-queues.md).
