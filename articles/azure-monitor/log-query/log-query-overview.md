---
title: Analysieren von Protokolldaten in Azure Monitor | Microsoft-Dokumentation
description: Sie benötigen eine Protokollabfrage, um Protokolldaten aus Azure Monitor abzurufen.  In diesem Artikel wird beschrieben, wie neue Protokollabfragen in Azure Monitor verwendet werden. Er enthält Konzepte, die Ihnen vor dem Erstellen bekannt sein sollten.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: 1cb3946a93cbeff6a9b95e0a21edbf0523b53d5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203610"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analysieren von Protokolldaten in Azure Monitor

Die von Azure Monitor gesammelten Protokolldaten werden in einem Log Analytics-Arbeitsbereich gespeichert, der auf dem [Azure Daten-Explorer](/azure/data-explorer) basiert. Er sammelt Telemetriedaten aus einer Vielzahl von Quellen und verwendet zum Abrufen und Analysieren der Daten die vom Daten-Explorer verwendete [Abfragesprache Kusto](/azure/kusto/query).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>Protokollabfragen

Sie benötigen eine Protokollabfrage, um Protokolldaten beliebiger Art aus Azure Monitor abzurufen.  Wenn Sie [Daten im Portal analysieren](portals.md), eine [Warnungsregel konfigurieren](../platform/alerts-metric.md), die Sie bei einer bestimmten Bedingung benachrichtigt, oder Daten mithilfe der [Azure Monitor-Protokolle-API](https://dev.loganalytics.io/) abrufen, verwenden Sie jeweils eine Abfrage, um die gewünschten Daten anzugeben.  In diesem Artikel wird beschrieben, wie Protokollabfragen in Azure Monitor verwendet werden. Er enthält Konzepte, die Ihnen vor dem Erstellen bekannt sein sollten.

## <a name="where-log-queries-are-used"></a>Orte, an denen Protokollabfragen verwendet werden

Dies sind einige der verschiedenen Möglichkeiten, wie Sie Abfragen in Azure Monitor verwenden können:

- **Portal.** Sie können im [Azure-Portal](portals.md) eine interaktive Analyse von Protokolldaten durchführen.  Dadurch können Sie Ihre Abfrage bearbeiten und die Ergebnisse in einer Vielzahl von Formaten und Visualisierungen analysieren.  
- **Warnungsregeln.** [Warnungsregeln](../platform/alerts-overview.md) identifizieren proaktiv Probleme durch die Daten in Ihrem Arbeitsbereich.  Jede Warnungsregel basiert auf einer Protokollsuche, die in regelmäßigen Abständen automatisch ausgeführt wird.  Die Ergebnisse werden überprüft, um zu ermitteln, ob eine Warnung erstellt werden soll.
- **Dashboards.** Sie können die Ergebnisse beliebiger Abfragen in einem [Azure-Dashboard](../learn/tutorial-logs-dashboards.md) anheften, was es Ihnen ermöglicht, Protokoll- und Metrikdaten gemeinsam zu visualisieren und optional mit anderen Azure-Benutzern zu teilen. 
- **Ansichten.**  Sie können mit [Ansicht-Designer](../platform/view-designer.md) Visualisierungen von Daten erstellen, die in Benutzerdashboards einbezogen werden sollen.  Protokollabfragen stellen die von [Kacheln](../platform/view-designer-tiles.md) und [Visualisierungsparts](../platform/view-designer-parts.md) in jeder Ansicht verwendeten Daten bereit.  

- **Export:**  Wenn Sie Protokolldaten aus Azure Monitor nach Excel oder [Power BI](../platform/powerbi.md) importieren, erstellen Sie eine Protokollabfrage, um die zu exportierenden Daten zu definieren.
- **PowerShell.** Sie können ein PowerShell-Skript über eine Befehlszeile oder ein Azure Automation-Runbook ausführen, das Protokolldaten mithilfe von [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) aus Azure Monitor abruft.  Dieses Cmdlet erfordert eine Abfrage, um die abzurufenden Daten festzulegen.
- **Azure Monitor-Protokolle-API.**  Die [Azure Monitor-Protokolle-API](../platform/alerts-overview.md) ermöglicht einem beliebigen REST-API-Client, Protokolldaten aus dem Arbeitsbereich abzurufen.  Die API-Anforderung enthält eine Abfrage, die für Azure Monitor ausgeführt wird, um die abzurufenden Daten zu ermitteln.

![Protokollsuchvorgänge](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Schreiben Sie eine Abfrage.
Azure Monitor verwendet [eine Version der Abfragesprache Kusto](get-started-queries.md), um Protokolldaten auf vielfältige Weise abzurufen und zu analysieren.  Sie beginnen in der Regel mit grundlegenden Abfragen und arbeiten sich dann zu erweiterten Funktionen weiter, wenn Ihre Anforderungen komplexer werden.

Die Grundstruktur einer Abfrage ist eine Quelltabelle, gefolgt von einer Reihe von Operatoren, die durch einen senkrechten Strich (`|`) getrennt sind.  Sie können mehrere Operatoren verketten, um die Daten einzugrenzen und erweiterte Funktionen auszuführen.

Beispiel: Sie möchten die zehn Computer mit den meisten Fehlerereignissen über den letzten Tag suchen.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Oder Sie möchten Computer suchen, die über den letzten Tag keinen Takt aufwiesen.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Ein anderes Beispiel ist ein Liniendiagramm mit der Prozessorauslastung für jeden Computer in der letzten Woche.

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Durch diese kurzen Beispiele können Sie sehen, dass unabhängig von der Art der Daten, die denen Sie arbeiten, die Struktur der Abfrage ähnlich ist.  Sie können sie in abgegrenzte Schritte unterteilen, bei denen die resultierenden Daten eines Befehls über die Pipeline an den nächsten Befehl gesendet werden.

Sie können Daten auch Log Analytics-Arbeitsbereiche übergreifend in Ihrem Abonnement abfragen.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Aufbau von Azure Monitor-Protokolldaten
Wenn Sie eine Abfrage erstellen, legen Sie zuerst fest, welche Tabellen die gesuchten Daten aufweisen. Verschiedene Arten von Daten werden in jedem [Log Analytics-Arbeitsbereich](../learn/quick-create-workspace.md) auf dedizierte Tabellen aufgeteilt.  Die Dokumentation für die verschiedenen Datenquellen enthält den Namen des Datentyps, der von der einzelnen Datenquelle erstellt wird, und eine Beschreibung jeder seiner einzelnen Eigenschaften.  Für viele Abfragen werden nur Daten aus einer einzelnen Tabelle benötigt, andere greifen jedoch möglicherweise auf verschiedene Optionen zurück, um Daten aus mehreren Tabellen einzuschließen.

Zwar speichert [Application Insights](../app/app-insights-overview.md) Anwendungsdaten wie Anforderungen, Ausnahmen, Ablaufverfolgungen und Nutzung in Azure Monitor-Protokollen, sie werden aber auf einer anderen Partition als die anderen Protokolldaten gespeichert. Sie können für den Zugriff auf diese Daten die gleiche Abfragesprache verwenden, müssen aber für den Zugriff die [Application Insights-Konsole](../app/analytics.md) oder die [Application Insights-REST-API](https://dev.applicationinsights.io/) verwenden. Sie können [ressourcenübergreifende Abfragen](../log-query/cross-workspace-query.md) verwenden, um Daten aus Application Insights mit anderen Protokolldaten in Azure Monitor zu kombinieren.


![Tabellen](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über die Verwendung von [Log Analytics zum Erstellen und Bearbeiten von Protokollsuchen](../log-query/portals.md).
- Sehen Sie sich ein [Tutorial zum Schreiben von Abfragen](../log-query/get-started-queries.md) mit der neuen Abfragesprache an.
