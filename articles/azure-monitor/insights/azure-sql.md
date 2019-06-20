---
title: Azure SQL-Analyse-Lösung in Azure Monitor | Microsoft-Dokumentation
description: Die Azure SQL-Analyse-Lösung hilft Ihnen bei der Verwaltung Ihrer Azure SQL-Datenbanken.
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 0617dc617309d49cdc7c8cddd4e91619b873b914
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785682"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Überwachen von Azure SQL-Datenbank mithilfe von Azure SQL-Analyse (Vorschauversion)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-Analyse ist eine erweiterte Cloudüberwachungslösung zum bedarfsorientierten und abonnementübergreifenden Überwachen der Leistung von Azure SQL-Datenbanken, Pools für elastische Datenbanken und verwalteten Instanzen über eine zentrale Benutzeroberfläche. Die Lösung erfasst und visualisiert wichtige Azure SQL-Datenbank-Leistungsmetriken und umfasst integrierte Intelligenz für die Problembehandlung der Leistung.

Anhand von mit der Lösung erfassten Metriken können Sie benutzerdefinierte Überwachungsregeln und -warnungen erstellen. Die Lösung hilft Ihnen beim Erkennen von Problemen auf jeder Ebene Ihres Anwendungsstapels. Sie verwendet Azure-Diagnosemetriken zusammen mit Azure Monitor-Ansichten, um Daten zu allen Azure SQL-Datenbanken, Pools für elastische Datenbanken und Datenbanken in verwalteten Instanzen in einem einzigen Log Analytics-Arbeitsbereich darzustellen. Azure Monitor unterstützt Sie beim Erfassen, Korrelieren und Visualisieren strukturierter und nicht strukturierter Daten.

Einen Überblick über die praktische Verwendung der Azure SQL-Analyse und über typische Verwendungsszenarien finden Sie im eingebetteten Video:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Verbundene Quellen

Azure SQL-Analyse ist eine Cloudüberwachungslösung, die das Streaming von Diagnosetelemetriedaten für Azure SQL-Datenbanken unterstützt: Einzeldatenbanken, Pooldatenbanken und Datenbanken der verwalteten Instanz. Da die Lösung keine Agents für die Verbindung mit Azure Monitor verwendet, wird die Überwachung von lokal oder auf virtuellen Computern gehosteten SQL Server-Instanzen nicht unterstützt. Entsprechende Informationen finden Sie in der folgenden Kompatibilitätstabelle.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| [Azure-Diagnose](../platform/collect-azure-metrics-logs.md) | **Ja** | Azure-Metrik- und Protokolldaten werden direkt von Azure an Azure Monitor-Protokolle gesendet. |
| [Azure-Speicherkonto](../platform/collect-azure-metrics-logs.md) | Nein | Azure Monitor liest keine Daten aus einem Speicherkonto. |
| [Windows-Agents](../platform/agent-windows.md) | Nein | Direkte Windows-Agents werden von der Lösung nicht verwendet. |
| [Linux-Agents](../learn/quick-collect-linux-computer.md) | Nein | Direkte Linux-Agents werden von der Lösung nicht verwendet. |
| [System Center Operations Manager-Verwaltungsgruppe](../platform/om-agents.md) | Nein | Es wird von der Lösung keine direkte Verbindung vom Operations Manager-Agent zu Azure Monitor verwendet. |

## <a name="configuration"></a>Konfiguration
Fügen Sie mithilfe des unter [Hinzufügen von Azure Monitor-Lösungen aus dem Lösungskatalog](../../azure-monitor/insights/solutions.md) beschriebenen Prozesses Ihrem Log Analytics-Arbeitsbereich die Azure SQL-Analyse-Lösung (Vorschau) hinzu.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Konfigurieren von Azure SQL-Datenbanken, Pools für elastische Datenbanken und verwalteten Instanzen zum Streamen von Diagnosetelemetriedaten

Nachdem Sie die Azure SQL-Analyse-Lösung in Ihrem Arbeitsbereich erstellt haben, müssen Sie **jede Ressource konfigurieren**, für die Sie zur Überwachung die zugehörige Diagnosetelemetrie an die Lösung streamen möchten. Befolgen Sie dazu die detaillierten Anweisungen auf dieser Seite:

- Aktivieren Sie die Azure-Diagnose für Ihre Azure SQL-Datenbank, um [Diagnosetelemetriedaten an die Azure SQL-Analyse zu streamen](../../sql-database/sql-database-metrics-diag-logging.md).

Die oben abgebildete Seite enthält auch Anweisungen zum Aktivieren der Unterstützung für die Überwachung mehrerer Azure-Abonnements über einen einzelnen Azure SQL-Analysearbeitsbereich als zentrale Konsole.

## <a name="using-the-solution"></a>Verwenden der Lösung

Wenn Sie die Lösung zu Ihrem Arbeitsbereich hinzufügen, wird die Kachel „Azure SQL Analytics“ zu Ihrem Arbeitsbereich hinzugefügt und erscheint in der Übersicht. Klicken Sie auf den Link „Zusammenfassung anzeigen“, um den Kachelinhalt zu laden.

![Zusammenfassungskachel „Azure SQL Analytics“](./media/azure-sql/azure-sql-sol-tile-01.png)

Nach dem Laden zeigt die Kachel die Anzahl von Azure SQL-Datenbanken, Pools für elastische Datenbanken, verwaltete Instanzen und Datenbanken in verwalteten Instanzen an, von denen die Lösung Diagnosetelemetriedaten empfängt.

![Kachel „Azure SQL Analytics“](./media/azure-sql/azure-sql-sol-tile-02.png)

Die Lösung bietet zwei separate Ansichten: eine für die Überwachung von Azure SQL-Datenbanken und Pools für elastische Datenbanken und eine andere für die Überwachung der verwalteten Instanz und Datenbanken in verwalteten Instanzen.

Zum Anzeigen des Überwachungsdashboards der Azure SQL-Analyse für Azure SQL-Datenbanken und Pools für elastische Datenbanken klicken Sie in den oberen Bereich der Kachel. Zum Anzeigen des Überwachungsdashboards der Azure SQL-Analyse für Azure SQL-Datenbanken und Pool für elastische Datenbanken klicken Sie in den oberen Bereich der Kachel.

### <a name="viewing-azure-sql-analytics-data"></a>Anzeigen von Azure SQL Analytics-Daten

Das Dashboard umfasst die Übersicht über alle Datenbanken, die aus verschiedenen Perspektiven überwacht werden. Damit verschiedene Perspektiven funktionieren, müssen Sie die richtigen Metriken oder Protokolle auf Ihren SQL-Ressourcen aktivieren, die dann an den Log Analytics-Arbeitsbereich gestreamt werden.

Hinweis: Wenn einige Metriken oder Protokolle nicht nach Azure Monitor gestreamt werden, werden die Kacheln in der Lösung nicht mit Überwachungsinformationen gefüllt.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Ansicht für Azure SQL-Datenbank und Pools für elastische Datenbanken

Nach Auswahl der Kachel „Azure SQL-Analyse“ für die Datenbank wird das Überwachungsdashboard angezeigt.

![Azure SQL-Analyse – Übersicht](./media/azure-sql/azure-sql-sol-overview.png)

Die Auswahl einer Kachel öffnet einen Drilldown-Bericht zur entsprechenden Perspektive. Nach Auswahl der Perspektive wird der Drilldownbericht geöffnet.

![Azure SQL-Analyse – Zeitlimits](./media/azure-sql/azure-sql-sol-metrics.png)

Jede Perspektive in dieser Ansicht bietet Zusammenfassungen zum Abonnement, Server, Pool für elastische Datenbanken und zur Datenbankebene. Darüber hinaus zeigt jede Perspektive auf der rechten Seite eine Perspektive an, die für den Bericht spezifisch ist. Durch die Auswahl eines Abonnements, Servers, Anwendungspools oder einer Datenbank aus der Liste wird der Drilldown fortgesetzt.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Ansicht für eine verwaltete Instanz und Datenbanken in einer verwalteten Instanz

Nach Auswahl der Kachel „Azure SQL-Analyse“ für die Datenbank wird das Überwachungsdashboard angezeigt.

![Azure SQL-Analyse – Übersicht](./media/azure-sql/azure-sql-sol-overview-mi.png)

Die Auswahl einer Kachel öffnet einen Drilldown-Bericht zur entsprechenden Perspektive. Nach Auswahl der Perspektive wird der Drilldownbericht geöffnet.

Die Auswahl der Kachel „Verwaltete Instanz“ zeigt Details zur Auslastung der verwalteten Instanz, darin enthaltene Datenbanken und Telemetriedaten zu den für die gesamte Instanz ausgeführten Abfragen.

![Azure SQL-Analyse – Zeitlimits](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektiven

In der folgenden Tabelle werden Perspektiven erläutert, die für zwei Versionen des im Dashboards unterstützt werden: eine für Azure SQL-Datenbank und Pools für elastische Datenbanken, und die andere für eine verwaltete Instanz.

| Perspektive | BESCHREIBUNG | Unterstützung für SQL-Datenbank und Pools für elastische Datenbanken | Unterstützung für die verwaltete Instanz |
| --- | ------- | ----- | ----- |
| Ressource nach Typ | Perspektive, die alle überwachten Ressourcen zählt. | Ja | Ja |
| Einblicke | Stellt einen hierarchischen Drilldown in die Leistung in Intelligent Insights bereit. | Ja | Ja |
| Errors | Stellt den hierarchischen Drilldown in SQL-Fehler bereit, die in den Datenbanken aufgetreten sind. | Ja | Ja |
| Zeitlimits | Stellt den hierarchischen Drilldown in SQL-Zeitlimits bereit, die in den Datenbanken aufgetreten sind. | Ja | Nein |
| Blockierungen | Stellt den hierarchischen Drilldown in SQL-Blockierungen bereit, die in den Datenbanken aufgetreten sind. | Ja | Nein |
| Datenbankwartevorgänge | Stellt den hierarchischen Drilldown in SQL-Wartestatistiken auf Datenbankebene bereit. Enthält Zusammenfassungen der gesamten Wartezeit sowie die Wartezeit pro Wartetyp. |Ja | Ja |
| Abfragedauer | Stellt den hierarchischen Drilldown in die Statistiken zur Abfrageausführung bereit, z.B. Abfragedauer, CPU-Auslastung, Daten-E/A-Auslastung und Protokoll-E/A-Auslastung. | Ja | Ja |
| Abfragewartevorgänge | Stellt den hierarchischen Drilldown in die Statistiken zu Abfragewartevorgängen nach Wartekategorie bereit. | Ja | Ja |

### <a name="intelligent-insights-report"></a>Intelligent Insights-Bericht

[Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) von Azure SQL-Datenbank informiert Sie über die Leistung all Ihrer Azure SQL-Datenbanken. Alle erfassten Intelligent Insights-Daten können visuell dargestellt und über die Insights Perspektive aufgerufen werden.

![Azure SQL-Analyse – Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Berichte zu Pools für elastische Datenbanken und Datenbanken

Sowohl Pools für elastische Datenbanken als auch SQL-Datenbanken verfügen über eigene bestimmte Berichte, die alle Daten anzeigen, die für die Ressource im angegebenen Zeitraum gesammelt werden.

![Azure SQL-Analyse –Datenbank](./media/azure-sql/azure-sql-sol-database.png)

![Pool für elastische Azure SQL-Datenbanken](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Abfrageberichte

Durch die Abfragedauer- und Abfragewartevorgänge-Perspektive können Sie die Leistung von Abfragen über den Abfragebericht korrelieren. Dieser Bericht vergleicht die Abfrageleistung in verschiedenen Datenbanken und erleichtert das Ermitteln von Datenbanken, die die ausgewählte Abfrage im Vergleich zu langsamen Datenbanken ordnungsgemäß ausführen.

![Azure SQL-Analyse – Abfragen](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Berechtigungen

Für die Verwendung von Azure SQL-Analyse benötigen Benutzer mindestens die Rolle „Leser“ in Azure. Diese Rolle erlaubt es Benutzern jedoch nicht, den Abfragetext anzuzeigen oder automatische Optimierungsaktionen durchzuführen. Rollen mit umfassenderen Berechtigungen in Azure, die eine Nutzung der Lösung in vollem Umfang ermöglichen, sind „Besitzer“, „Mitwirkender“, „SQL-DB-Mitwirkender“ und „SQL Server-Mitwirkender“. Sie sollten auch erwägen, im Portal eine benutzerdefinierte Rolle mit spezifischen Berechtigungen zu erstellen, die nur erforderlich sind, um Azure SQL-Analyse zu verwenden, aber keinen Zugriff auf die Verwaltung anderer Ressourcen bieten.

### <a name="creating-a-custom-role-in-portal"></a>Erstellen einer benutzerdefinierten Rolle im Portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Da einige Organisationen eine strenge Berechtigungssteuerung in Azure erzwingen, können Sie das folgende PowerShell-Skript verwenden. Es ermöglicht das Erstellen der benutzerdefinierten Rolle „SQL Analytics Monitoring Operator“ im Azure-Portal. Sie umfasst nur die mindestens erforderlichen Lese- und Schreibberechtigungen, um Azure SQL-Analyse in vollem Umfang zu nutzen.

Ersetzen Sie „{SubscriptionId}“ im Skript unten durch Ihre Azure-Abonnement-ID, und führen Sie das Skript aus, während Sie mit der Rolle „Besitzer“ oder „Mitwirkender“ in Azure angemeldet sind.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Nachdem die neue Rolle erstellt wurde, weisen Sie sie allen Benutzern zu, denen Sie benutzerdefinierte Berechtigungen zum Verwenden von Azure SQL-Analyse gewähren möchten.

## <a name="analyze-data-and-create-alerts"></a>Analysieren von Daten und Erstellen von Warnungen

Die Datenanalyse in Azure SQL-Analyse basiert auf der [Log Analytics-Sprache](../log-query/get-started-queries.md) für Ihre benutzerdefinierten Abfragen und Berichte. Eine Beschreibung der von der Datenbankressource für benutzerdefinierte Abfragen gesammelten Daten finden Sie unter [Verfügbare Metriken und Protokolle](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatisierte Warnungen in der Lösung basieren auf dem Schreiben einer Log Analytics-Abfrage, die eine Warnung auslöst, wenn eine Bedingung erfüllt ist. Im Folgenden finden Sie mehrere Beispiele für Log Analytics-Abfragen, für die in der Lösung Warnungen eingerichtet werden können.

### <a name="creating-alerts-for-azure-sql-database"></a>Erstellen von Warnungen für Azure SQL-Datenbank

Sie können problemlos [Warnungen](../platform/alerts-metric.md) mit den Daten erstellen, die aus Azure SQL-Datenbank-Ressourcen stammen. Im Folgenden finden Sie einige nützliche [Protokollabfragen](../log-query/log-query-overview.md), die Sie mit einer Protokollwarnung verwenden können:

#### <a name="high-cpu-on-azure-sql-database"></a>Hohe CPU-Auslastung für Azure SQL-Datenbank

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass überwachte Datenbanken grundlegende Metriken an die Lösung streamen.
> - Ersetzen Sie den MetricName-Wert „cpu_percent“ durch „dtu_consumption_percent“ um stattdessen hohe DTU-Ergebnisse zu erhalten.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Hohe CPU-Auslastung in Pools für elastische Datenbanken in Azure SQL-Datenbank

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass überwachte Datenbanken grundlegende Metriken an die Lösung streamen.
> - Ersetzen Sie den MetricName-Wert „cpu_percent“ durch „dtu_consumption_percent“ um stattdessen hohe DTU-Ergebnisse zu erhalten.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Durchschnittlicher Speicherverbrauch für Azure SQL-Datenbank in der letzten Stunde bei über 95%

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass überwachte Datenbanken grundlegende Metriken an die Lösung streamen.
> - Diese Abfrage erfordert die Einrichtung einer Warnungsregel, die eine Warnung auslöst, wenn Ergebnisse (> 0 Ergebnisse) von der Abfrage vorhanden sind, die angeben, dass die Bedingung in einigen Datenbanken vorhanden ist. Die Ausgabe ist eine Liste von Datenbankressourcen, die im definierten Zeitbereich den Speicherschwellenwert überschreiten.
> - Die Ausgabe ist eine Liste von Datenbankressourcen, die im definierten Zeitbereich den Speicherschwellenwert überschreiten.

#### <a name="alert-on-intelligent-insights"></a>Warnung in Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass überwachte Datenbanken ein SQLInsights-Diagnoseprotokoll an die Lösung streamen.
> - Diese Abfrage erfordert die Einrichtung einer Warnungsregel, die mit der gleichen Häufigkeit wie „alert_run_interval“ ausgeführt wird, um Duplikate zu vermeiden. Die Regel muss für das Auslösen der Warnung eingerichtet werden, wenn Ergebnisse (> 0 Ergebnisse) von der Abfrage vorhanden sind.
> - Passen Sie den Wert „alert_run_interval“ an, um den Zeitbereich festzulegen, für den überprüft wird, ob die Bedingung in Datenbanken aufgetreten ist, die zum Streamen des SQLInsights-Protokolls an die Lösung konfiguriert sind.
> - Passen Sie „insights_string“ so an, dass die Ausgabe des Texts der Insights-Analyse der Grundursache erfasst wird. Der gleiche Text wird auf der Benutzeroberfläche der Lösung angezeigt, die sie in den vorhandenen Erkenntnissen nutzen können. Alternativ können Sie die folgende Abfrage verwenden, um den Text aller in Ihrem Abonnement generierten Einblicke anzuzeigen. Verwenden Sie die Ausgabe der Abfrage, um die unterschiedlichen Zeichenfolgen für die Einrichtung von Warnungen für Insights zu nutzen.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Erstellen von Warnungen für eine verwaltete Instanz

#### <a name="managed-instance-storage-is-above-90"></a>Speicherverbrauch der verwalteten Instanz bei über 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass für die überwachte verwaltete Instanz das Streaming des ResourceUsageStats-Protokolls an die Lösung aktiviert ist.
> - Diese Abfrage erfordert die Einrichtung einer Warnungsregel, die eine Warnung auslöst, wenn Ergebnisse (> 0 Ergebnisse) von der Abfrage vorhanden sind, die angeben, dass die Bedingung in der verwalteten Instanz vorhanden ist. Die Ausgabe ist der prozentuale Speicherverbrauch in der verwalteten Instanz.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Durchschnittlicher CPU-Verbrauch der verwalteten Instanz in der letzten 1 Stunde über 95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Voraussetzung für die Einrichtung dieser Warnung ist, dass für die überwachte verwaltete Instanz das Streaming des ResourceUsageStats-Protokolls an die Lösung aktiviert ist.
> - Diese Abfrage erfordert die Einrichtung einer Warnungsregel, die eine Warnung auslöst, wenn Ergebnisse (> 0 Ergebnisse) von der Abfrage vorhanden sind, die angeben, dass die Bedingung in der verwalteten Instanz vorhanden ist. Die Ausgabe ist die durchschnittliche CPU-Auslastung der verwalteten Instanz im definierten Zeitraum, angegeben in Prozent.

### <a name="pricing"></a>Preise

Obwohl die Lösung kostenlos verwendet werden kann, fallen für die Nutzung von Diagnosetelemetriedaten über die kostenlosen Einheiten hinaus, die für die Datenerfassung jeden Monat zugeteilt werden, Gebühren an. Weitere Informationen finden Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/monitor). Die kostenlosen Einheiten der bereitgestellten Datenerfassung ermöglichen die kostenlose Überwachung von mehreren Datenbanken pro Monat. Beachten Sie, dass bei einer größeren Anzahl aktiver Datenbanken mit umfangreicheren Workloads mehr Daten als bei Datenbanken im Leerlauf erfasst werden. Sie können problemlos Ihre Datenerfassungsnutzung in der Lösung überwachen, indem Sie den OMS-Arbeitsbereich im Navigationsmenü der Azure SQL-Analyse auswählen und dann „Nutzungs- und geschätzte Kosten“ auswählen.

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie [Protokollabfragen](../log-query/log-query-overview.md) in Azure Monitor, um detaillierte Azure SQL-Daten anzuzeigen.
- [Erstellen eigener Dashboards](../learn/tutorial-logs-dashboards.md), die Azure SQL-Daten anzeigen
- [Erstellen von Warnungen](../platform/alerts-overview.md), wenn bestimmte Azure SQL-Ereignisse auftreten
