---
title: include file
description: include file
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 7d8773cc12b50382f6f300987ec6ce504cd238af
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178659"
---
Steuert das [Stichprobenfeature in Application Insights](../articles/azure-functions/functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|isEnabled|true|Aktiviert oder deaktiviert die Stichprobenentnahme.| 
|maxTelemetryItemsPerSecond|5|Der Schwellenwert, bei dem die Stichprobenentnahme beginnt.| 