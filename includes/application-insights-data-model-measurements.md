---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177849"
---
Auflistung von benutzerdefinierten Messungen. Verwenden Sie diese Auflistung, um benannte, dem Telemetrieelement zugeordnete Messungen zu melden. Typische Anwendungsfälle:
- Größe der Nutzlast der Abhängigkeitstelemetrie
- Anzahl von Warteschlangenelementen, die von der Anforderungstelemetrie verarbeitet wurden
- Zeit, die der Kunde zum Durchführen des Schritts im Assistenten für die Ereignistelemetrie benötigt hat

[Benutzerdefinierte Messungen](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) können in der Anwendungsanalyse abgefragt werden:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Benutzerdefinierte Messungen werden dem Telemetrieelement zugeordnet, zu dem sie gehören. Sie unterliegen dem Sampling mit dem Telemetrieelement, das diese Messungen enthält. Verwenden Sie [Metriktelemetrie](../articles/azure-monitor/app/api-custom-events-metrics.md), um eine Messung nachzuverfolgen, die einen Wert besitzt, der von anderen Telemetrietypen unabhängig ist.

Maximale Schlüssellänge: 150
