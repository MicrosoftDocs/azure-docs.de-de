---
title: host.json-Referenz für Azure Functions 2.x
description: Referenzdokumentation für die host.json-Datei von Azure Functions mit der v2 Runtime.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: ddd3b0889eedd55f809dbb57b2ef41a2ae3f9c94
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521392"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>host.json-Referenz für Azure Functions 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Die Metadatendatei *host.json* enthält globale Konfigurationsoptionen, die sich auf alle Funktionen einer Funktionen-App auswirken. In diesem Artikel werden die verfügbaren Einstellungen für die v2 Runtime aufgelistet.  

> [!NOTE]
> Dieser Artikel gilt für Azure Functions 2.x.  Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

Weitere Konfigurationsoptionen Ihrer Funktions-App werden in den [Anwendungseinstellungen](functions-app-settings.md) verwaltet.

Einige Einstellungen in host.json werden nur bei lokaler Ausführung in der [local.settings.json](functions-run-local.md#local-settings-file)-Datei verwendet.

## <a name="sample-hostjson-file"></a>host.json-Beispieldatei

Für die folgenden *host.json*-Beispieldateien sind alle möglichen Optionen angegeben.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

In den folgenden Abschnitten dieses Artikels werden die einzelnen allgemeinen Eigenschaften erläutert. Alle Eigenschaften sind optional, sofern nicht anders angegeben.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Diese Einstellung ist ein untergeordnetes Element von [logging](#logging).

Steuert das [Stichprobenfeature in Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> Protokollsampling kann dazu führen, dass einige Ausführungen möglicherweise nicht auf dem Application Insights-Blatt für Überwachen angezeigt werden.

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|isEnabled|true|Aktiviert oder deaktiviert die Stichprobenentnahme.| 
|maxTelemetryItemsPerSecond|5|Der Schwellenwert, bei dem die Stichprobenentnahme beginnt.| 

## <a name="cosmosdb"></a>cosmosDb

Die Konfigurationseinstellung finden Sie in [Cosmos DB-Trigger und -Bindungen](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Die Konfigurationseinstellung finden Sie in [Bindungen für Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Die Konfigurationseinstellung finden Sie in [Event Hub-Trigger und -Bindungen](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Erweiterungen

Eigenschaft, die ein Objekt zurückgibt, das alle die bindungsspezifischen-Einstellungen enthält, z.B. [http](#http) und [eventhub](#eventhub).

## <a name="functions"></a>functions

Eine Liste der Funktionen, die vom Auftragshost ausgeführt werden. Ein leeres Array bedeutet, dass alle Funktionen ausgeführt werden. Nur bei [lokaler Ausführung](functions-run-local.md) für die Verwendung vorgesehen. In Funktions-Apps in Azure sollten Sie anstelle dieser Einstellung die in [How to disable functions in Azure Functions](disable-function.md) (Deaktivieren von Funktionen in Azure Functions) aufgeführten Schritte ausführen, um bestimmte Funktionen zu deaktivieren.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Gibt die Timeoutdauer für alle Funktionen an. Bei einem serverlosen Verbrauchsplan liegt der gültige Bereich zwischen 1 Sekunde und 10 Minuten, wobei der Standardwert bei 5 Minuten liegt. Bei einem App Service-Plan gibt es keine allgemeine Beschränkung, und der Standardwert hängt von der Version der Runtime ab. In Version 2.x ist der Standardwert für einen App Service-Plan 30 Minuten. In Version 1.x ist er *null*, was keinen Timeout angibt.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Konfigurationseinstellungen für [Host Health Monitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor) (Überwachung der Hostintegrität).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|enabled|true|Gibt an, ob die Funktion aktiviert ist. | 
|healthCheckInterval|10 Sekunden|Das Zeitintervall zwischen den regelmäßigen Integritätsüberprüfungen im Hintergrund. | 
|healthCheckWindow|2 Minuten|Ein variables Zeitfenster, das in Zusammenhang mit der `healthCheckThreshold`-Einstellung verwendet wird.| 
|healthCheckThreshold|6|Maximale Anzahl von fehlerhaften Integritätsüberprüfungen, bevor ein Neustart des Hosts initiiert wird.| 
|counterThreshold|0,80|Der Schwellenwert, an dem ein Leistungsindikator als fehlerhaft betrachtet wird.| 

## <a name="http"></a>http

Die Konfigurationseinstellung finden Sie in [HTTP-Trigger und -Bindungen](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>logging

Steuert das Protokollierungsverhalten der Funktions-App, einschließlich Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definiert, welche Stufe der Dateiprotokollierung aktiviert ist.  Optionen sind `never`, `always`, `debugOnly`. |
|logLevel|–|Objekt, das das Filtern der Protokollkategorie nach Funktionen in der App definiert. Version 2.x entspricht bei der Filterung der Protokollkategorie dem Layout von ASP.NET Core. Dadurch können Sie die Protokollierung nach bestimmten Funktionen filtern. Weitere Informationen finden Sie unter [Protokollfilterung](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) in der Dokumentation zu ASP.NET Core. |
|console|–| Die [Konsolen](#console)protokollierungseinstellung. |
|applicationInsights|–| Die [applicationInsights](#applicationinsights)-Einstellung. |

## <a name="console"></a>console

Diese Einstellung ist ein untergeordnetes Element von [logging](#logging). Sie steuert die Konsolenprotokollierung, wenn nicht im Debugmodus.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|isEnabled|false|Aktiviert oder deaktiviert die Konsolenprotokollierung.| 

## <a name="queues"></a>queues

Die Konfigurationseinstellungen finden Sie in [Trigger und Bindungen der Speicherwarteschlange](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Die Konfigurationseinstellung finden Sie in [SendGrid-Trigger und -Bindungen](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Die Konfigurationseinstellung finden Sie in [Service Bus-Trigger und -Bindungen](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>singleton

Konfigurationseinstellungen für das Singleton-Sperrverhalten. Weitere Informationen finden Sie unter [GitHub-Problem zur Singleton-Unterstützung](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|lockPeriod|00:00:15|Der Zeitraum, für den Sperren auf Funktionsebene gelten. Die Sperren werden automatisch verlängert.| 
|listenerLockPeriod|00:01:00|Der Zeitraum, für den Listenersperren gelten.| 
|listenerLockRecoveryPollingInterval|00:01:00|Das Zeitintervall für die Wiederherstellung der Listenersperre, wenn eine Listenersperre nicht beim Start abgerufen werden konnte.| 
|lockAcquisitionTimeout|00:01:00|Die maximale Zeitspanne, in der die Laufzeit versucht, eine Sperre abzurufen.| 
|lockAcquisitionPollingInterval|–|Das Intervall zwischen den Versuchen, eine Sperre abzurufen.| 

## <a name="version"></a>version

Die Versionszeichenfolge `"version": "2.0"` ist für Funktions-App mit der v2-Runtime als Ziel erforderlich.

## <a name="watchdirectories"></a>watchDirectories

Eine Reihe von [Verzeichnissen mit freigegebenem Code](functions-reference-csharp.md#watched-directories), die auf Änderungen überwacht werden sollte.  Dadurch wird sichergestellt, dass Änderungen am Code in diesen Verzeichnissen von Ihren Funktionen übernommen werden.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

Verwaltete Abhängigkeit ist eine Previewfunktion (Vorschaufeature), die derzeit nur mit PowerShell-basierten Funktionen unterstützt wird. Sie ermöglicht, dass Abhängigkeiten automatisch vom Dienst verwaltet werden können. Ist die enabled-Eigenschaft auf „true“ festgelegt, wird die Datei [requirements.psd1](functions-reference-powershell.md#dependency-management) verarbeitet. Abhängigkeiten werden aktualisiert, wenn irgendwelche Nebenversionen veröffentlicht werden.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Aktualisieren der host.json-Datei](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Weitere Informationen finden Sie unter den globalen Einstellungen in Umgebungsvariablen](functions-app-settings.md)
