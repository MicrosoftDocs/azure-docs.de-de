---
title: include file
description: include file
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: ff5d04a2923f16c763e1529ecb365f60d6275ca2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026312"
---
### <a name="general-query-limits"></a>Allgemeine Abfragegrenzwerte

| Begrenzung | BESCHREIBUNG |
|:---|:---|
| Abfragesprache | Azure Monitor verwendet dieselbe [Kusto-Abfragesprache](/azure/kusto/query/) wie Azure Data Explorer. Weitere Informationen finden Sie unter [Azure Monitor – Unterschiede in der Protokollabfragesprache](/azure/data-explorer/kusto/query/) für KQL-Sprachelemente, die in Azure Monitor nicht unterstützt werden. |
| Azure-Regionen | Protokollabfragen können zu einem übermäßigen Aufwand führen, wenn sich die Daten über Log Analytics-Arbeitsbereiche in mehreren Azure-Regionen erstrecken. Weitere Informationen finden Sie unter [Abfragegrenzwerte](../articles/azure-monitor/log-query/scope.md#query-scope-limits). |
| Ressourcenübergreifende Abfragen | Die maximale Anzahl von Application Insights-Ressourcen und Log Analytics-Arbeitsbereichen in einer einzelnen Abfrage ist auf 100 beschränkt.<br>Ressourcenübergreifende Abfrage wird im View Designer nicht unterstützt.<br>Eine ressourcenübergreifende Abfrage in Protokollwarnungen wird in der neuen scheduledQueryRules-API unterstützt.<br>Ausführliche Informationen finden Sie unter [Ressourcenübergreifende Abfragelimits](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits). |

### <a name="user-query-throttling"></a>Drosselung von Benutzerabfragen
In Azure Monitor gelten mehrere Drosselungsgrenzwerte zum Schutz vor einer übermäßige Anzahl von Abfragen. Dieses Verhalten kann die System-Back-End-Ressourcen überlasten und die Reaktionsfähigkeit des Diensts gefährden. Die folgenden Grenzwerte sind darauf ausgelegt, Kunden vor Unterbrechungen zu schützen und eine konsistente Dienstebene zu gewährleisten. Die Drosselung und die Grenzwerte für Benutzer betreffen nur Szenarien mit übermäßigem Verbrauch und sind bei normaler Verwendung in der Regel nicht relevant.


| "Measure" | Grenzwert pro Benutzer | BESCHREIBUNG |
|:---|:---|:---|
| Gleichzeitige Abfragen | 5 | Wenn für den Benutzer bereits fünf Abfragen ausgeführt werden, werden alle neuen Abfragen in eine Parallelitätswarteschlange pro Benutzer eingereiht. Wenn eine der ausgeführten Abfragen beendet wird, wird die nächste Abfrage aus der Warteschlange abgerufen und gestartet. Das bezieht sich nicht auf Abfragen aus Warnungsregeln.
| Zeit in der Parallelitätswarteschlange | 3 Minuten | Wenn sich eine Abfrage länger als drei Minuten in der Warteschlange befindet, ohne zu starten, wird sie mit einer HTTP-Fehlerantwort mit dem Code 429 beendet. |
| Gesamtanzahl von Abfragen in der Parallelitätswarteschlange | 200 | Sobald die Anzahl von Abfragen in der Warteschlange 200 erreicht, werden alle weiteren Abfragen mit dem HTTP-Fehlercode 429 abgelehnt. Diese Zahl gilt zusätzlich zu den fünf Abfragen, die gleichzeitig ausgeführt werden können. |
| Abfragerate | 200 Abfragen pro 30 Sekunden | Dies ist die Gesamtrate, mit der Abfragen von einem einzelnen Benutzer an alle Arbeitsbereiche übermittelt werden können.  Dieser Grenzwert gilt für programmgesteuerte Abfragen und für Abfragen, die von Visualisierungskomponenten wie Azure-Dashboards und der Zusammenfassungsseite für den Log Analytics-Arbeitsbereich initiiert wurden. |

- Optimieren Sie Ihre Abfragen wie unter [Optimieren von Protokollabfragen in Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md) beschrieben.
- Dashboards und Arbeitsmappen können mehrere Abfragen in einer einzelnen Ansicht enthalten, die bei jedem Laden oder Aktualisieren einen Burst von Abfragen generieren. Zerlegen Sie sie ggf. in mehrere Ansichten, die bedarfsgesteuert geladen werden. 
- Erwägen Sie, in Power BI anstelle von unformatierten Protokollen nur aggregierte Ergebnisse zu extrahieren.