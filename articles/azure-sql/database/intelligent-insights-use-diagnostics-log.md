---
title: Leistungsdiagnoseprotokoll von Intelligent Insights
description: Intelligent Insights umfasst ein Diagnoseprotokoll für Leistungsprobleme bei Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 6720058ce6e7614284111a75a2ab3a91525df1a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488764"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Verwenden des Intelligent Insights-Diagnoseprotokolls für Leistungsprobleme bei Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Auf dieser Seite finden Sie Informationen zum Verwenden der von [Intelligent Insights](intelligent-insights-overview.md) generierten Leistungsdiagnoseprotokolle für Leistungsprobleme in Azure SQL-Datenbank und Azure SQL Managed Instance, zum Format der Protokolle und zu den Daten, die sie für Ihre benutzerdefinierten Entwicklungsanforderungen enthalten. Sie können dieses Diagnoseprotokoll an [Azure Monitor-Protokolle](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage) oder eine Drittanbieterlösung für benutzerdefinierte DevOps-Warnungs- und Berichterstellungsfunktionen senden.

> [!NOTE]
> Intelligent Insights ist eine Vorschaufunktion, die in den folgenden Regionen nicht verfügbar ist: „Europa, Westen“, „Europa, Norden“, „USA, Westen 1“ und „USA, Osten 1“.

## <a name="log-header"></a>Protokollheader

Das Diagnoseprotokoll nutzt das JSON-Standardformat zum Ausgeben von Intelligent Insights-Ergebnissen. Die genaue Kategorieeigenschaft für den Zugriff auf ein Intelligent Insights-Protokoll ist der feste Wert „SQLInsights“.

Die Kopfzeile des Protokolls ist allgemein gehalten und besteht aus dem Zeitstempel (TimeGenerated), der Ihnen anzeigt, wann ein Eintrag erstellt wurde. Sie beinhaltet außerdem eine Ressourcen-ID (ResourceId), die auf genau die Datenbank verweist, auf die sich der Eintrag bezieht. Die Kategorie (Category), Ebene (Level) und der Vorgangsname (OperationName) sind feste Eigenschaften, deren Werte sich nicht verändern. Sie deuten darauf hin, dass ein Protokolleintrag Informationen enthält und aus Intelligent Insights (SQLInsights) stammt.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Problem-ID und betroffene Datenbank

Die ID zur Bestimmung des Problems (IssueId_d) bietet eine Möglichkeit zum besonderen Nachverfolgen von Leistungsproblemen bis zu ihrer Lösung. Mehrere Ereignisdatensätze in dem Protokoll, das den Status dieses Problems angibt, weisen dieselbe Problem-ID auf.

Neben der „Issue ID“ (Problem-ID) meldet das Diagnoseprotokoll den Zeitstempel des Starts (intervalStartTime_t) und Endes (intervalEndTime_t) des jeweiligen Ereignisses, das in Zusammenhang mit einem im Diagnoseprotokoll gemeldeten Problem steht.

Die Eigenschaft „Pool für elastische Datenbanken“ (elasticPoolName_s) gibt an, zu welchem Pool für elastische Datenbanken die problematische Datenbank gehört. Wenn die Datenbank nicht zu einem Pool für elastische Datenbanken gehört, hat diese Eigenschaft keinen Wert. Die Datenbank, in der ein Problem erkannt wurde, wird in der Eigenschaft „Datenbankname“ (databaseName_s) angegeben.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Erkannte Probleme

Der nächste Abschnitt des Intelligent Insights-Leistungsprotokolls enthält Leistungsprobleme, die mithilfe integrierter künstlicher Intelligenz erkannt wurden. Die Erkenntnisse werden in den Eigenschaften im JSON-Diagnoseprotokoll angegeben. Sie bestehen aus der Kategorie eines Problems, den Auswirkungen dieses Problems, den betroffenen Abfragen und den Metriken. Möglicherweise enthalten die Erkenntniseigenschaften mehrere erkannte Leistungsprobleme.

Erkannte Leistungsprobleme werden mit der folgenden Struktur der Eigenschaft „detections_s“ gemeldet:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Erkennbare Leistungsmuster und die Details, die an das Diagnoseprotokoll ausgegeben werden, werden in der folgenden Tabelle bereitgestellt.

### <a name="detection-category"></a>Erkennungskategorie

Die Eigenschaft „Category“ (Kategorie) beschreibt die Kategorie erkennbarer Leistungsmuster. In der folgenden Tabelle finden Sie alle möglichen Kategorien erkennbarer Leistungsmuster. Nähere Informationen finden Sie unter [Troubleshoot database performance issues with Intelligent Insights (Behandeln von Problemen mit der Leistung mithilfe von Intelligent Insights)](intelligent-insights-troubleshoot-performance.md).

Je nach erkanntem Leistungsproblem unterscheiden sich die in der Diagnoseprotokolldatei ausgegebenen Details.

| Erkennbare Leistungsmuster | Ausgegebene Details |
| :------------------- | ------------------- |
| Erreichen von Ressourcengrenzwerten | <li>Betroffene Ressourcen</li><li>Abfragehashes</li><li>Prozentsatz des Ressourcenverbrauchs</li> |
| Gestiegene Workload | <li>Anzahl der Abfragen, deren Ausführung sich erhöht hat</li><li>Abfragehashes von Abfragen mit dem größten Beitrag zur Zunahme der Workload</li> |
| Hohe Arbeitsspeicherauslastung | <li>Arbeitsspeicherclerk</li> |
| Sperren | <li>Betroffene Abfragehashes</li><li>Blockierende Abfragehashes</li> |
| Erhöhter Wert für „Maximaler Grad an Parallelität“ | <li>Abfragehashes</li><li>CXP-Wartezeiten</li><li>Wartezeiten</li> |
| Seitenlatchkonflikt | <li>Abfragehashes von Abfragen, die einen Konflikt verursachen</li> |
| Fehlender Index | <li>Abfragehashes</li> |
| Neue Abfrage | <li>Abfragehash der neuen Abfragen</li> |
| Ungewöhnliche Statistik bei Wartezeiten | <li>Ungewöhnliche Wartetypen</li><li>Abfragehashes</li><li>Abfragewartezeiten</li> |
| TempDB-Konflikt | <li>Abfragehashes von Abfragen, die einen Konflikt verursachen</li><li>Anteil der Abfrage an der gesamten Wartezeit aufgrund des Seitenlatchkonflikts in der Datenbank [%]</li> |
| Mangel an DTUs im Pool für elastische Datenbanken | <li>Pool für elastische Datenbanken</li><li>Datenbank mit dem höchsten DTU-Verbrauch</li><li>Prozentsatz der vom größten Consumer genutzten Pool-DTUs</li> |
| Planregression | <li>Abfragehashes</li><li>IDs von guten Plänen</li><li>IDs von schlechten Plänen</li> |
| Änderung eines Werts der datenbankweit gültigen Konfiguration | <li>Die datenbankweit gültige Konfiguration ändert sich im Vergleich mit den Standardwerten</li> |
| Langsamer Client | <li>Abfragehashes</li><li>Wartezeiten</li> |
| Tarifdowngrade | <li>Textbenachrichtigung</li> |

### <a name="impact"></a>Auswirkung

Die Eigenschaft „Impact“ (Auswirkung) beschreibt, inwieweit ein erkanntes Verhalten zu dem Problem mit der Datenbank beigetragen hat. Die Auswirkungen liegen in einem Bereich zwischen 1 und 3. Dabei steht 3 für den höchsten Anteil, 2 für einen mittelhohen Anteil und 1 für den niedrigsten Anteil. Der Wert von „Impact“ kann entsprechend Ihren Anforderungen als Eingabe in eine benutzerdefinierte Warnungsautomatisierung verwendet werden. Die Eigenschaft „QueryHashes“ (Abfragehashes) gibt eine Liste der Abfragehashes zurück, die von einer bestimmten Erkennung betroffen waren.

### <a name="impacted-queries"></a>Betroffene Abfragen

Der nächste Abschnitt des Intelligent Insights-Protokolls enthält Informationen zu bestimmten Abfragen, die von den erkannten Leistungsproblemen betroffen waren. Diese Informationen werden als ein Array von Objekten offengelegt, das in die Eigenschaft „impact_s“ eingebettet ist. Die Eigenschaft „Impact“ (Auswirkung) besteht aus Entitäten und Metriken. Entitäten verweisen auf eine bestimmte Abfrage (Typ: Abfrage). Der eindeutige Abfragehash wird in der Eigenschaft „Value“ (Wert) angegeben. Darüber hinaus folgen auf alle offengelegten Abfragen eine Metrik und ein Wert, die zusammen auf ein erkanntes Leistungsproblem hindeuten.

Im folgenden Protokollbeispiel wurde erkannt, dass sich die Ausführungsdauer der Abfrage mit dem Hash 0x9102EXZ4 verlängert hat (Metrik: DurationIncreaseSeconds). Der Wert von 110 Sekunden weist darauf hin, dass die Ausführung genau dieser Abfrage 110 Sekunden länger gedauert hat. Da mehrere Abfragen erkannt werden können, enthält dieser bestimmte Protokollabschnitt möglicherweise mehrere Abfrageeinträge.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metriken

Die Maßeinheit für jede gemeldete Metrik wird unter der Eigenschaft „Metric“ (Metrik) mit den möglichen Werten (Sekunden, Zahl und Prozent) angegeben. Der Wert einer gemessenen Metrik wird von der Eigenschaft „Value“ (Wert) gemeldet.

Die Eigenschaft „DurationIncreaseSeconds“ gibt die Maßeinheit in Sekunden zurück. Die Maßeinheit „CriticalErrorCount“ stellt die Anzahl der Fehler in einer Zahl dar.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Fehlerursachenanalyse und Verbesserungsempfehlungen

Der letzte Teil des Intelligent Insights-Leistungsprotokolls bezieht sich auf die automatische Fehlerursachenanalyse der erkannten Leistungsminderung. Die Information erscheint in benutzerfreundlicher Sprache in der Eigenschaft „Fehlerursachenanalyse“ (rootCauseAnalysis_s). Das Protokoll enthält, wenn möglich, Verbesserungsempfehlungen.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Sie können das Intelligent Insights-Leistungsprotokoll mit [Azure Monitor-Protokolle]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) oder eine Drittanbieterlösung für benutzerdefinierte DevOps-Warnungs- und Berichterstellungsfunktionen verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Intelligent Insights](intelligent-insights-overview.md)-Konzepte.
- Erfahren Sie mehr über das [Behandeln von Leistungsproblemen mithilfe von Intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Erfahren Sie, wie Sie [Leistungsprobleme mithilfe der Azure SQL-Analyse überwachen](../../azure-monitor/insights/azure-sql.md).
- Erfahren Sie mehr über das [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../../azure-monitor/platform/platform-logs-overview.md).