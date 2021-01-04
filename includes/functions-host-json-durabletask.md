---
title: include file
description: include file
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6a862a051d0040ac99746d81f10ae63d5af7545f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013722"
---
Konfigurationseinstellungen für [Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md).

> [!NOTE]
> Alle Hauptversionen von Durable Functions werden für alle Versionen der Azure Functions-Runtime unterstützt. Das Schema der host.json-Konfiguration unterscheidet sich jedoch abhängig geringfügig, abhängig von der verwendeten Version der Azure Functions-Runtime und Durable Functions-Erweiterung. Die folgenden Beispiele sind für die Verwendung mit Azure Functions 2.0 und 3.0 vorgesehen. Wenn Sie Azure Functions 1.0 verwenden, sind in beiden Beispielen die verfügbaren Einstellungen gleich. Der Abschnitt „durableTask“ von „host.json“ muss jedoch in den Stamm der host.json-Konfiguration und nicht als Feld unterhalb von „extensions“ eingefügt werden.

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "controlQueueBatchSize": 32,
      "partitionCount": 4,
      "controlQueueVisibilityTimeout": "00:05:00",
      "workItemQueueVisibilityTimeout": "00:05:00",
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10,
      "maxQueuePollingInterval": "00:00:30",
      "azureStorageConnectionStringName": "AzureWebJobsStorage",
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "traceInputsAndOutputs": false,
      "logReplayEvents": false,
      "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
      "eventGridKeySettingName":  "EventGridKey",
      "eventGridPublishRetryCount": 3,
      "eventGridPublishRetryInterval": "00:00:30",
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

Aufgabenhubnamen müssen mit einem Buchstaben beginnen und bestehen nur aus Buchstaben und Ziffern. Wenn nicht angegeben, lautet der standardmäßige Aufgabenhubname für eine Funktionen-App **DurableFunctionsHub**. Weitere Informationen finden Sie unter [Aufgabenhubs in Durable Functions (Azure Functions)](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternative Namen für [Aufgabenhubs](../articles/azure-functions/durable/durable-functions-task-hubs.md) können zum Isolieren von mehreren Durable Functions-Anwendungen verwendet werden, auch wenn sie dasselbe Speicher-Back-End verwenden.|
|controlQueueBatchSize|32|Die Anzahl der aus der Steuerelement-Warteschlange jeweils abzurufenden Nachrichten.|
|controlQueueBufferThreshold|256|Die Anzahl der Steuerelement-Warteschlangen Nachrichten, die gleichzeitig im Arbeitsspeicher gepuffert werden können. Zu diesem Zeitpunkt wartet der Verteiler, bevor zusätzliche Nachrichten aus der Warteschlange entfernt werden.|
|partitionCount |4|Die Anzahl der Partitionen für die Steuerelement-Warteschlange. Kann ein positiver Integerwert zwischen 1 und 16 sein.|
|controlQueueVisibilityTimeout |5 Minuten|Das Sichtbarkeitstimeout von aus der Steuerelement-Warteschlange entfernten Nachrichten.|
|workItemQueueVisibilityTimeout |5 Minuten|Das Sichtbarkeitstimeout von aus der Warteschlange für Arbeitselemente (work item queue) entfernten Nachrichten.|
|maxConcurrentActivityFunctions |Zehnfache Anzahl der Prozessoren auf dem aktuellen Computer|Die maximale Anzahl von Aktivitätsfunktionen, die gleichzeitig auf einer einzelnen Hostinstanz verarbeitet werden können.|
|maxConcurrentOrchestratorFunctions |Zehnfache Anzahl der Prozessoren auf dem aktuellen Computer|Die maximale Anzahl von Orchestratorfunktionen, die gleichzeitig auf einer einzelnen Hostinstanz verarbeitet werden können.|
|maxQueuePollingInterval|30 Sekunden|Das maximale Abrufintervall der Steuerelement- und Arbeitselement-Warteschlangen im Format *hh:mm:ss*. Höhere Werte können zu höherer Latenz bei der Nachrichtenverarbeitung führen. Niedrigere Werte können aufgrund verstärkter Speichertransaktionen zu höheren Speicherkosten führen.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Der Name der App-Einstellung mit der Azure Storage-Verbindungszeichenfolge, die zum Verwalten der zugrunde liegenden Azure Storage-Ressourcen verwendet wird.|
|trackingStoreConnectionStringName||Der Name einer Verbindungszeichenfolge, die für die Verlaufs- und Instanzentabellen verwendet wird. Wird kein Wert angegeben, wird die Verbindung `connectionStringName` (Durable 2.x) oder `azureStorageConnectionStringName` (Durable 1.x) verwendet.|
|trackingStoreNamePrefix||Das für die Verlaufs- und Instanzentabellen zu verwendende Präfix, wenn `trackingStoreConnectionStringName` angegeben ist. Wenn diese Eigenschaft nicht festgelegt ist, wird `DurableTask` als Standardwert für das Präfix verwendet. Wenn `trackingStoreConnectionStringName` nicht angegeben ist, wird in den Verlaufs- und Instanzentabellen der Wert `hubName` als Präfix verwendet und jede Einstellung für `trackingStoreNamePrefix` wird ignoriert.|
|traceInputsAndOutputs |false|Ein Wert, der angibt, ob die Eingaben und Ausgaben von Funktionsaufrufen nachverfolgt werden. Das Standardverhalten beim Nachverfolgen von Ereignissen zur Funktionsausführung besteht darin, die Anzahl der Bytes in die serialisierten Eingaben und Ausgaben für Funktionsaufrufe einzuschließen. Dieses Verhalten ermöglicht minimale Informationen zu den Eingaben und Ausgaben, ohne die Protokolle zu überfrachten oder irrtümlich vertrauliche Informationen verfügbar zu machen. Wenn diese Eigenschaft auf TRUE festgelegt wird, werden bei der standardmäßigen Funktionsprotokollierung die gesamten Inhalte der Eingaben und Ausgaben der Funktionen protokolliert.|
|logReplayEvents|false|Ein Wert, der angibt, ob Orchestrierungswiedergabeereignisse in Application Insights geschrieben werden.|
|eventGridTopicEndpoint ||Die URL eines benutzerdefinierten Azure Event Grid-Themenendpunkts. Wenn diese Eigenschaft festgelegt ist, werden Benachrichtigungsereignisse zum Orchestrierungslebenszyklus an diesem Endpunkt veröffentlicht. Diese Eigenschaft unterstützt die Auflösung von App-Einstellungen.|
|eventGridKeySettingName ||Der Name der App-Einstellung, die den Schlüssel für die Authentifizierung mit dem benutzerdefinierten Azure Event Grid-Thema unter `EventGridTopicEndpoint` enthält.|
|eventGridPublishRetryCount|0|Die Anzahl der Wiederholungsversuche, wenn bei der Veröffentlichung im Event Grid-Thema Fehler auftreten.|
|eventGridPublishRetryInterval|5 Minuten|Das Wiederholungsintervall der Event Grid-Veröffentlichung im Format *hh:mm:ss*.|
|eventGridPublishEventTypes||Eine Liste von Ereignistypen, die in Event Grid veröffentlicht werden sollen. Wenn diese Eigenschaft nicht angegeben ist, werden alle Ereignistypen veröffentlicht. Zulässige Werte sind `Started`, `Completed`, `Failed` und `Terminated`.|
|useAppLease|true|Wird diese Einstellung auf `true` festgelegt, müssen Apps vor der Verarbeitung von Aufgabenhubnachrichten eine Bloblease auf App-Ebene abrufen. Weitere Informationen finden Sie in der Dokumentation zur [Notfallwiederherstellung und geografischen Verteilung](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md). Verfügbar ab v2.3.0
|useLegacyPartitionManagement|true|Wird diese Einstellung auf `false` festgelegt, wird ein Partitionsverwaltungsalgorithmus verwendet, der bei der horizontalen Skalierung die Wahrscheinlichkeit einer doppelten Funktionsausführung reduziert.  Verfügbar ab v2.3.0 Der Standardwert wird in einer zukünftigen Version in `false` geändert.|
|useGracefulShutdown|false|(Preview) Ordnungsgemäßes Herunterfahren aktivieren, um die Wahrscheinlichkeit zu verringern, dass beim Herunterfahren von Hosts Funktionsausführungen in Prozessen fehlschlagen.|

Viele dieser Einstellungen werden zur Optimierung der Leistung verwendet. Weitere Informationen finden Sie unter [Leistung und Skalierbarkeit](../articles/azure-functions/durable/durable-functions-perf-and-scale.md).