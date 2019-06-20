---
title: include file
description: include file
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178660"
---
Konfigurationseinstellungen für [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

```json
{
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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Aufgabenhubnamen müssen mit einem Buchstaben beginnen und bestehen nur aus Buchstaben und Ziffern. Wenn nicht angegeben, lautet der standardmäßige Aufgabenhubname für eine Funktionen-App **DurableFunctionsHub**. Weitere Informationen finden Sie unter [Aufgabenhubs in Durable Functions (Azure Functions)](../articles/azure-functions/durable-functions-task-hubs.md).

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternative Namen für [Aufgabenhubs](../articles/azure-functions/durable-functions-task-hubs.md) können zum Isolieren von mehreren Durable Functions-Anwendungen verwendet werden, auch wenn sie dasselbe Speicher-Back-End verwenden.|
|controlQueueBatchSize|32|Die Anzahl der aus der Steuerelement-Warteschlange jeweils abzurufenden Nachrichten.|
|partitionCount |4|Die Anzahl der Partitionen für die Steuerelement-Warteschlange. Kann ein positiver Integerwert zwischen 1 und 16 sein.|
|controlQueueVisibilityTimeout |5 Minuten|Das Sichtbarkeitstimeout von aus der Steuerelement-Warteschlange entfernten Nachrichten.|
|workItemQueueVisibilityTimeout |5 Minuten|Das Sichtbarkeitstimeout von aus der Warteschlange für Arbeitselemente (work item queue) entfernten Nachrichten.|
|maxConcurrentActivityFunctions |Zehnfache Anzahl der Prozessoren auf dem aktuellen Computer|Die maximale Anzahl von Aktivitätsfunktionen, die gleichzeitig auf einer einzelnen Hostinstanz verarbeitet werden können.|
|maxConcurrentOrchestratorFunctions |Zehnfache Anzahl der Prozessoren auf dem aktuellen Computer|Die maximale Anzahl von Orchestratorfunktionen, die gleichzeitig auf einer einzelnen Hostinstanz verarbeitet werden können.|
|maxQueuePollingInterval|30 Sekunden|Das maximale Abrufintervall der Steuerelement- und Arbeitselement-Warteschlangen im Format *hh:mm:ss*. Höhere Werte können zu höherer Latenz bei der Nachrichtenverarbeitung führen. Niedrigere Werte können aufgrund verstärkter Speichertransaktionen zu höheren Speicherkosten führen.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Der Name der App-Einstellung mit der Azure Storage-Verbindungszeichenfolge, die zum Verwalten der zugrunde liegenden Azure Storage-Ressourcen verwendet wird.|
|trackingStoreConnectionStringName||Der Name einer Verbindungszeichenfolge, die für die Verlaufs- und Instanzentabellen verwendet wird. Wenn diese Eigenschaft nicht angegeben ist, wird die Verbindung `azureStorageConnectionStringName` verwendet.|
|trackingStoreNamePrefix||Das für die Verlaufs- und Instanzentabellen zu verwendende Präfix, wenn `trackingStoreConnectionStringName` angegeben ist. Wenn diese Eigenschaft nicht festgelegt ist, wird `DurableTask` als Standardwert für das Präfix verwendet. Wenn `trackingStoreConnectionStringName` nicht angegeben ist, wird in den Verlaufs- und Instanzentabellen der Wert `hubName` als Präfix verwendet und jede Einstellung für `trackingStoreNamePrefix` wird ignoriert.|
|traceInputsAndOutputs |false|Ein Wert, der angibt, ob die Eingaben und Ausgaben von Funktionsaufrufen nachverfolgt werden. Das Standardverhalten beim Nachverfolgen von Ereignissen zur Funktionsausführung besteht darin, die Anzahl der Bytes in die serialisierten Eingaben und Ausgaben für Funktionsaufrufe einzuschließen. Dieses Verhalten ermöglicht minimale Informationen zu den Eingaben und Ausgaben, ohne die Protokolle zu überfrachten oder irrtümlich vertrauliche Informationen verfügbar zu machen. Wenn diese Eigenschaft auf TRUE festgelegt wird, werden bei der standardmäßigen Funktionsprotokollierung die gesamten Inhalte der Eingaben und Ausgaben der Funktionen protokolliert.|
|logReplayEvents|false|Ein Wert, der angibt, ob Orchestrierungswiedergabeereignisse in Application Insights geschrieben werden.|
|eventGridTopicEndpoint ||Die URL eines benutzerdefinierten Azure Event Grid-Themenendpunkts. Wenn diese Eigenschaft festgelegt ist, werden Benachrichtigungsereignisse zum Orchestrierungslebenszyklus an diesem Endpunkt veröffentlicht. Diese Eigenschaft unterstützt die Auflösung von App-Einstellungen.|
|eventGridKeySettingName ||Der Name der App-Einstellung, die den Schlüssel für die Authentifizierung mit dem benutzerdefinierten Azure Event Grid-Thema unter `EventGridTopicEndpoint` enthält.|
|eventGridPublishRetryCount|0|Die Anzahl der Wiederholungsversuche, wenn bei der Veröffentlichung im Event Grid-Thema Fehler auftreten.|
|eventGridPublishRetryInterval|5 Minuten|Das Wiederholungsintervall der Event Grid-Veröffentlichung im Format *hh:mm:ss*.|
|eventGridPublishEventTypes||Eine Liste von Ereignistypen, die in Event Grid veröffentlicht werden sollen. Wenn diese Eigenschaft nicht angegeben ist, werden alle Ereignistypen veröffentlicht. Zulässige Werte sind `Started`, `Completed`, `Failed` und `Terminated`.|

Viele dieser Einstellungen werden zur Optimierung der Leistung verwendet. Weitere Informationen finden Sie unter [Leistung und Skalierbarkeit](../articles/azure-functions/durable-functions-perf-and-scale.md).