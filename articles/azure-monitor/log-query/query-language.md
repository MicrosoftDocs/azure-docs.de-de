---
title: Protokollabfragen in Azure Monitor | Microsoft-Dokumentation
description: Verweise auf Ressourcen zum Erlernen des Schreibens von Protokollabfragen in Azure Monitor
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: 6fae245e4d72f7f6f7809d85da17023deb1518e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560852"
---
# <a name="azure-monitor-log-queries"></a>Protokollabfragen in Azure Monitor
Azure Monitor-Protokolle basieren auf Azure Data Explorer, und Azure Monitor-Protokollabfragen verwenden eine Version der gleichen Abfragesprache Kusto. Die [Dokumentation über die Abfragesprache Kusto](/azure/kusto/query) enthält alle Informationen zur Sprache und ist die wichtigste Ressource zum Schreiben von Azure Monitor-Protokollabfragen. Diese Seite enthält Links zu anderen Ressourcen, die Sie beim Erlernen des Schreibens von Abfragen unterstützen und die Unterschiede der Sprachversionen bei der Azure Monitor-Implementierung erläutern.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Erste Schritte

- [Erste Schritte mit Azure Monitor Log Analytics](get-started-portal.md) erläutert das Schreiben von Abfragen und das Arbeiten mit Ergebnissen im Azure-Portal.
- [Erste Schritte mit Azure Monitor-Protokollabfragen](get-started-queries.md) erläutert das Schreiben von Abfragen mit Azure Monitor-Protokolldaten.

## <a name="concepts"></a>Konzepte
- [Analysieren von Protokolldaten in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) gibt einen kurzen Überblick über Protokollabfragen und beschreibt, wie Azure Monitor-Protokolldaten strukturiert sind.
- [Anzeigen und Analysieren von Protokolldaten in Azure Monitor](../../azure-monitor/log-query/portals.md) erklärt die Portale, in denen Sie Protokollabfragen erstellen und ausführen.

## <a name="reference"></a>Verweis

- [Referenz zur Abfragesprache](/azure/kusto/query) ist die vollständige Sprachreferenz für die Abfragesprache Kusto.
- [Azure Monitor-Protokollabfragesprache – Unterschiede](data-explorer-difference.md) beschreibt die Unterschiede zwischen den Versionen der Abfragesprache Kusto.
- [Standardeigenschaften in Azure Monitor-Protokolldatensätzen](../../azure-monitor/platform/log-standard-properties.md) beschreibt Standardeigenschaften aller Azure Monitor-Protokolldaten.
- [Ausführen ressourcenübergreifender Protokollabfragen in Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) beschreibt, wie Sie Protokollabfragen schreiben, die Daten aus mehreren Log Analytics-Arbeitsbereichen und Application Insights-Anwendungen verwenden.


## <a name="examples"></a>Beispiele

- [Beispiele für Azure Monitor-Protokollabfragen](examples.md) enthält Beispielabfragen mit Azure Monitor-Protokolldaten.



## <a name="lessons"></a>Lektionen

- [Arbeiten mit Zeichenfolgen in Azure Monitor-Protokollabfragen](string-operations.md) beschreibt das Arbeiten mit Zeichenfolgendaten.
- [Arbeiten mit Werten für Datum und Uhrzeit in Azure Monitor-Protokollabfragen](datetime-operations.md) beschreibt das Arbeiten mit Daten für Datum und Uhrzeit. 
- [Aggregationen in Azure Monitor-Protokollabfragen](aggregations.md) und [Erweiterte Aggregationen in Azure Monitor-Protokollabfragen](advanced-aggregations.md) erläutern, wie Sie Daten aggregieren und zusammenfassen.
- [Joins in Azure Monitor-Protokollabfragen](joins.md) beschreibt, wie Sie Daten aus mehreren Tabellen verknüpfen.
- [Arbeiten mit JSON und Datenstrukturen in Azure Monitor-Protokollabfragen](json-data-structures.md) beschreibt, wie Sie JSON-Daten analysieren.
- [Schreiben erweiterter Protokollabfragen in Azure Monitor](advanced-query-writing.md) erläutert Strategien zum Erstellen komplexer Abfragen und die Wiederverwendung von Code.
- [Erstellen von Grafiken und Diagrammen aus Azure Monitor-Protokollabfragen](charts.md) beschreibt, wie Sie Daten aus einer Protokollabfrage visualisieren.

## <a name="cheatsheets"></a>Spickzettel

-  [Von SQL zur Azure Monitor-Protokollabfrage](sql-cheatsheet.md) hilft Benutzern, die bereits mit SQL vertraut sind.
-  [Von Splunk zur Azure Monitor-Protokollabfrage](splunk-cheatsheet.md) hilft Benutzern, die bereits mit Splunk vertraut sind.
 
## <a name="next-steps"></a>Nächste Schritte

- Greifen Sie auf die vollständige [Referenzdokumentation für die Abfragesprache Kusto](/azure/kusto/query/) zu.
