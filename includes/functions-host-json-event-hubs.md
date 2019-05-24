---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131707"
---
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
|prefetchCount|–|Das standardmäßige PrefetchCount, das von dem zugrunde liegenden EventProcessorHost verwendet wird.| 
|batchCheckpointFrequency|1|Die Anzahl der zu verarbeitenden Ereignisbatches, bevor ein EventHub-Cursorprüfpunkt erstellt wird.| 
