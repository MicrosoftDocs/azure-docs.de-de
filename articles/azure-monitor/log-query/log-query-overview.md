---
title: Protokollabfragen in Azure Monitor
description: Referenzinformationen für die in Azure Monitor verwendete Abfragesprache Kusto. Enthält zusätzliche Elemente, die spezifisch für Azure Monitor sind, und Elemente, die in Azure Monitor-Protokollabfragen nicht unterstützt werden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491391"
---
# <a name="log-queries-in-azure-monitor"></a>Protokollabfragen in Azure Monitor
Azure Monitor-Protokolle basieren auf Azure Data Explorer, und Protokollabfragen werden in der Abfragesprache Kusto (Kusto Query Language, KQL) geschrieben. Hierbei handelt es sich um eine umfangreiche Sprache, die einfach zu lesen und zu verwenden ist, sodass Sie nach einer grundlegenden Einarbeitung mit dem Schreiben von Abfragen beginnen können.

Abfragen werden beispielsweise in den folgenden Bereichen in Azure Monitor verwendet:

- [Log Analytics:](../log-query/log-analytics-overview.md) Das primäre Tool im Azure-Portal zum Bearbeiten von Protokollabfragen und interaktiven Analysieren von deren Ergebnissen. Auch wenn Sie an anderer Stelle in Azure Monitor eine Protokollabfrage verwenden möchten, schreiben und testen Sie diese normalerweise in Log Analytics, bevor Sie sie an den endgültigen Speicherort kopieren.
- [Protokollwarnungsregeln:](../platform/alerts-overview.md) Proaktives Identifizieren von Problemen anhand von Daten in Ihrem Arbeitsbereich.  Jede Warnungsregel basiert auf einer Protokollabfrage, die in regelmäßigen Abständen automatisch ausgeführt wird.  Die Ergebnisse werden überprüft, um zu ermitteln, ob eine Warnung erstellt werden soll.
- [Arbeitsmappen:](../platform/workbooks-overview.md) Einfügen der Ergebnisse von Protokollabfragen mithilfe verschiedener Visualisierungen in interaktiven visuellen Berichten im Azure-Portal.
- [Azure-Dashboards:](../learn/tutorial-logs-dashboards.md) Anheften der Ergebnisse beliebiger Abfragen in einem Azure-Dashboard, wodurch Protokoll- und Metrikdaten gemeinsam visualisiert und optional mit anderen Azure-Benutzern geteilt werden können.
- [Logic Apps:](../platform/logicapp-flow-connector.md)  Verwenden der Ergebnisse einer Protokollabfrage in einem automatisierten Workflow mithilfe von Logic Apps.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult): Verwenden der Ergebnisse einer Protokollabfrage in einem PowerShell-Skript über eine Befehlszeile oder ein Azure Automation-Runbook, das „Get-AzOperationalInsightsSearchResults“ verwendet.
- [Azure Monitor-Protokolle-API](https://dev.loganalytics.io). Abrufen von Protokolldaten aus dem Arbeitsbereich von einem beliebigen REST-API-Client.  Die API-Anforderung enthält eine Abfrage, die für Azure Monitor ausgeführt wird, um die abzurufenden Daten zu ermitteln.

## <a name="getting-started"></a>Erste Schritte
Die beste Methode für den Einstieg in das Schreiben von Protokollabfragen mithilfe von KQL bieten die verfügbaren Tutorials und Beispiele.

- [Log Analytics-Tutorial](log-analytics-tutorial.md): Tutorial zur Verwendung der Funktionen von Log Analytics, dem Tool, das Sie im Azure-Portal zum Bearbeiten und Ausführen von Abfragen verwenden. Es ermöglicht zudem das Schreiben einfacher Abfragen, ohne direkt mit der Abfragesprache zu arbeiten. Wenn Sie Log Analytics bisher noch nicht verwendet haben, beginnen Sie hier, damit Sie das Tool verstehen, das Sie dann in den anderen Tutorials und Beispielen verwenden.
- [KQL-Tutorial](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor): Einführung in grundlegende KQL-Konzepte und gängige Operatoren. Dies ist der beste Ausgangspunkt, um sich mit der Sprache selbst und der Struktur von Protokollabfragen vertraut zu machen. 
- [Beispielabfragen](example-queries.md): Beschreibung der in Log Analytics verfügbaren Beispielabfragen. Sie können die Abfragen ohne Änderungen verwenden oder als Beispiele zum Erlernen von KQL nutzen.
- [Abfragebeispiele](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor): Beispielabfragen, die eine Vielzahl verschiedener Konzepte veranschaulichen.



## <a name="reference-documentation"></a>Referenzdokumentation
Eine [Dokumentation für KQL](/azure/data-explorer/kusto/query/), einschließlich der Referenz für alle Befehle und Operatoren, ist in der Dokumentation zu Azure Data Explorer enthalten. Auch nachdem Sie sich mit der Verwendung von KQL vertraut gemacht haben, nutzen Sie die Referenz noch regelmäßig, um sich mit neuen Befehlen und Szenarien zu befassen, die Sie bisher noch nicht verwendet haben.


## <a name="language-differences"></a>Sprachunterschiede
Zwar verwendet Azure Monitor dieselbe KQL wie Azure Data Explorer, doch gibt es einige Unterschiede. In der KQL-Dokumentation sind die Operatoren angegeben, die nicht von Azure Monitor unterstützt werden oder über unterschiedliche Funktionen verfügen. Azure Monitor-spezifische Operatoren sind im Azure Monitor-Inhalt dokumentiert. In den folgenden Abschnitten finden Sie eine Liste der Unterschiede zwischen den Sprachversionen zur schnellen Übersicht.

### <a name="statements-not-supported-in-azure-monitor"></a>In Azure Monitor nicht unterstützte Anweisungen

* [Alias](/azure/kusto/query/aliasstatement)
* [Abfrageparameter](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>In Azure Monitor nicht unterstützte Funktionen

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>In Azure Monitor nicht unterstützte Operatoren

* [Clusterübergreifender Join-Vorgang](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>In Azure Monitor nicht unterstützte Plug-Ins

* [Python-Plug-In](/azure/kusto/query/pythonplugin)
* [Plug-In „sql_request“](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Zusätzliche Operatoren in Azure Monitor
Die folgenden Operatoren unterstützen für Azure Monitor spezifische Features und stehen außerhalb von Azure Monitor nicht zur Verfügung.

* [app()](app-expression.md)
* [resource()](resource-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen erhalten Sie im [Tutorial zum Schreiben von Abfragen](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Greifen Sie auf die vollständige [Referenzdokumentation für die Abfragesprache Kusto](/azure/kusto/query/) zu.

