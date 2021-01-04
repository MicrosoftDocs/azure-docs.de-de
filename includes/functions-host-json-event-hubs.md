---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997301"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxBatchSize|10|Die maximale Ereignisanzahl, die pro Empfangsschleife empfangen wird.|
|prefetchCount|300|Die standardmäßige Vorabrufanzahl, die vom zugrunde liegenden `EventProcessorHost` verwendet wird. Der minimal zulässige Wert ist 10.|
|batchCheckpointFrequency|1|Die Anzahl der zu verarbeitenden Ereignisbatches, bevor ein EventHub-Cursorprüfpunkt erstellt wird.|

> [!NOTE]
> Eine Referenz für „host.json“ in Azure Functions 2x und höheren Versionen finden Sie in der [host.json-Referenz für Azure Functions 2.x oder höher](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|maxBatchSize|64|Die maximale Ereignisanzahl, die pro Empfangsschleife empfangen wird.|
|prefetchCount|–|Die standardmäßige Vorabrufanzahl, die vom zugrunde liegenden `EventProcessorHost` verwendet wird| 
|batchCheckpointFrequency|1|Die Anzahl der zu verarbeitenden Ereignisbatches, bevor ein EventHub-Cursorprüfpunkt erstellt wird.| 

> [!NOTE]
> Eine Referenz für „host.json“ in Azure Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).
