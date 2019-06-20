---
title: Protokolle in Azure Monitor | Microsoft-Dokumentation
description: Beschreibt Protokolle in Azure Monitor, die für eine erweiterte Analyse von Überwachungsdaten verwendet werden.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 897f2eef0a52838d6190cb85a6a7f4492250935b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244857"
---
# <a name="logs-in-azure-monitor"></a>Protokolle in Azure Monitor

> [!NOTE]
> Alle von Azure Monitor gesammelten Daten gehören einem von zwei Grundtypen an: Metriken und Protokollen. Dieser Artikel beschreibt Protokolle. Eine ausführliche Beschreibung von Metriken finden Sie unter [Metriken in Azure Monitor](data-platform-metrics.md), und ein Vergleich beider Typen ist unter [Von Azure Monitor gesammelte Überwachungsdaten](data-platform.md) enthalten.

Protokolle in Azure Monitor sind besonders zum Ausführen komplexer Analysen für Daten aus einer Vielzahl von Quellen nützlich. Dieser Artikel beschreibt, wie Protokolle in Azure Monitor strukturiert sind und was Sie mit den Daten tun können. Außerdem werden verschiedene Datenquellen genannt, die Daten in Protokollen speichern.

> [!NOTE]
> Es ist wichtig, zwischen Azure Monitor-Protokollen und Quellen von Protokolldaten in Azure zu unterscheiden. Beispielsweise werden Ereignisse auf Abonnementebene in Azure in ein [Aktivitätsprotokoll](activity-logs-overview.md) geschrieben, das Sie über das Azure Monitor-Menü anzeigen können. Die meisten Ressourcen schreiben Betriebsinformationen in ein [Diagnoseprotokoll](diagnostic-logs-overview.md), das Sie an verschiedene Speicherorte weiterleiten können. Azure Monitor-Protokolle ist eine Protokolldatenplattform, die Aktivitätsprotokolle und Diagnoseprotokolle zusammen mit anderen Überwachungsdaten erfasst, um umfassende Analysen für die gesamte Gruppe von Ressourcen zu ermöglichen.

## <a name="what-are-azure-monitor-logs"></a>Was sind Azure Monitor-Protokolle?

Protokolle in Azure Monitor enthalten verschiedene Arten von Daten, die in Datensätzen mit unterschiedlichen Eigenschaften für jeden Typ organisiert sind. Protokolle können numerische Werte wie Azure Monitor-Metriken enthalten, liefern aber typischerweise Textdaten mit detaillierten Beschreibungen. Außerdem unterscheiden sie sich dadurch von Metrikdaten, dass sie unterschiedliche Strukturen aufweisen und häufig nicht in regelmäßigen Abständen gesammelt werden. Telemetriedaten wie etwa Ereignisse und Ablaufverfolgungen werden als Azure Monitor-Protokolle zusätzlich zu Leistungsdaten gespeichert, die alle zur Analyse kombiniert werden können.

Ein häufiger Typ von Protokolleintrag ist ein Ereignis, das sporadisch erfasst wird. Ereignisse werden von einer Anwendung oder einem Dienst erstellt und enthalten typischerweise genügend Informationen, um selbst einen vollständigen Kontext zu liefern. Beispielsweise kann ein Ereignis anzeigen, dass eine bestimmte Ressource erstellt oder geändert, ein neuer Host als Reaktion auf erhöhten Datenverkehr gestartet oder ein Fehler in einer Anwendung festgestellt wurde.

 Da das Format der Daten variieren kann, können Anwendungen benutzerdefinierte Protokolle mit der von ihnen benötigten Struktur erstellen. Metrikdaten können sogar in Protokollen gespeichert werden, um sie mit anderen Überwachungsdaten für Trend- und andere Datenanalysen zu kombinieren.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Welche Möglichkeiten bieten Azure Monitor-Protokolle?
In der folgenden Tabelle sind die unterschiedlichen Optionen zur Nutzung von Protokollen in Azure Monitor aufgeführt.


|  |  |
|:---|:---|
| Analysieren | Verwenden von [Log Analytics](../log-query/get-started-portal.md) im Azure-Portal zum Schreiben von [Protokollabfragen](../log-query/log-query-overview.md) und interaktives Analysieren von Protokolldaten mithilfe der leistungsstarken Daten-Explorer-Analyse-Engine.<br>Verwenden der [Application Insights Analytics-Konsole](../app/analytics.md) im Azure-Portal zum Schreiben von Protokollabfragen und interaktives Analysieren von Protokolldaten aus Application Insights. |
| Visualisieren | Anheften der als Tabellen oder Diagramme gerenderten Abfrageergebnisse an ein [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md).<br>Erstellen einer [Arbeitsmappe](../app/usage-workbooks.md), um mehrere Sätze von Daten in einem interaktiven Bericht zu kombinieren. <br>Exportieren der Ergebnisse einer Abfrage nach [Power BI](powerbi.md), um verschiedene Visualisierungen zu verwenden und sie mit Benutzern außerhalb von Azure zu teilen.<br>Exportieren der Ergebnisse einer Abfrage nach [Grafana](grafana-plugin.md), um dessen Dashboards zu nutzen und sie mit anderen Quellen zu kombinieren.|
| Warnung | Konfigurieren einer [Protokollwarnungsregel](alerts-log.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](action-groups.md) ausführt, wenn die Ergebnisse der Abfrage mit einem bestimmten Ergebnis übereinstimmen.<br>Konfigurieren einer [Metrikwarnungsregel](alerts-metric-logs.md) für bestimmte Protokolldaten, die als Metriken extrahiert werden. |
| Gerätehandle | Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit [PowerShell-Cmdlets](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine benutzerdefinierte Anwendung mit der [REST-API](https://dev.loganalytics.io/). |
| Export | Erstellen eines Workflows zum Abrufen von Protokolldaten und kopieren der Daten an einen externen Speicherort mithilfe von [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Wie sind Daten in Azure Monitor-Protokollen strukturiert?
Die von Azure Monitor-Protokolle erfassten Daten werden in einem [Log Analytics-Arbeitsbereich](../platform/manage-access.md) gespeichert. Sie können in Ihrem Abonnement [mehrere Arbeitsbereiche erstellen](manage-access.md#determine-the-number-of-workspaces-you-need), um verschiedene Sätze von Protokolldaten zu verwalten. Jeder Arbeitsbereich enthält mehrere Tabellen, in denen jeweils Daten aus einer bestimmten Quelle gespeichert werden. Zwar weisen alle Tabellen [einige gemeinsame Eigenschaften](log-standard-properties.md) auf, doch hat jede auch eindeutige Eigenschaften, die von der Art der darin gespeicherten Daten abhängen. Ein neuer Arbeitsbereich weist einen Standardsatz von Tabellen auf, und es werden weitere Tabellen von verschiedenen Überwachungslösungen und anderen Diensten hinzugefügt, die in den Arbeitsbereich schreiben.

Protokolldaten aus Application Insights verwenden dieselbe Log Analytics-Engine wie Arbeitsbereiche, werden aber für jede überwachte Anwendung getrennt gespeichert. Jede Anwendung verfügt über einen Standardsatz von Tabellen zum Speichern von Daten wie Anwendungsanforderungen, Ausnahmen und Seitenansichten.

Protokollabfragen verwenden entweder Daten aus einem Log Analytics-Arbeitsbereich oder einer Application Insights-Anwendung. Sie können eine [ressourcenübergreifende Abfrage](../log-query/cross-workspace-query.md) verwenden, um Anwendungsdaten zusammen mit anderen Protokolldaten zu analysieren oder um Abfragen zu erstellen, die mehrere Arbeitsbereiche oder Anwendungen umfassen.

![Arbeitsbereiche](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Protokollabfragen
Daten in Azure Monitor-Protokollen werden mit einer [Protokollabfrage](../log-query/log-query-overview.md) abgerufen, die mit der [Abfragesprache Kusto](../log-query/get-started-queries.md) erstellt wurde, mit der Sie gesammelte Daten schnell abrufen, konsolidieren und analysieren können. Verwenden Sie [Log Analytics](../log-query/portals.md), um Protokollabfragen im Azure-Portal zu schreiben und zu testen. Dadurch können Sie interaktiv mit Ergebnissen arbeiten oder sie an ein Dashboard anheften, um sie mit anderen Visualisierungstools anzuzeigen.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Öffnen Sie [Log Analytics über Application Insights](../app/analytics.md), um Application Insights-Daten zu analysieren.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Sie können Protokolldaten auch mithilfe der [Log Analytics-API](https://dev.loganalytics.io/documentation/overview) und der [Application Insights-REST-API](https://dev.applicationinsights.io/documentation/overview) abrufen.


## <a name="sources-of-azure-monitor-logs"></a>Quellen von Azure Monitor-Protokollen
Azure Monitor kann Protokolldaten aus einer Vielzahl von Quellen sammeln, sowohl innerhalb von Azure als auch aus lokalen Ressourcen. Die folgenden Tabellen enthalten die unterschiedlichen Datenquellen, die von verschiedenen Ressourcen bereitgestellt werden, die Daten in Azure Monitor-Protokolle schreiben. Jeder Eintrag umfasst einen Link zu ausführlichen Informationen zur erforderlichen Konfiguration.

### <a name="azure-tenant-and-subscription"></a>Azure-Mandant und -Abonnement

| Daten | BESCHREIBUNG |
|:---|:---|
| Azure Active Directory-Überwachungsprotokolle | Über Diagnoseeinstellungen für jedes Verzeichnis konfiguriert. Informationen finden Sie unter [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Aktivitätsprotokolle | Standardmäßig separat gespeichert und können für Warnungen nahezu in Echtzeit verwendet werden. Installieren Sie die Lösung für die Aktivitätsprotokollanalyse, um in den Log Analytics-Arbeitsbereich zu schreiben. Informationen finden Sie unter [Erfassen und Analysieren von Azure-Aktivitätsprotokollen in Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure-Ressourcen

| Daten | BESCHREIBUNG |
|:---|:---|
| Ressourcendiagnose | Konfigurieren von Diagnoseeinstellungen zum Schreiben in Diagnosedaten, einschließlich Metriken in einen Log Analytics-Arbeitsbereich. Informationen finden Sie unter [Streamen von Azure-Diagnoseprotokollen an Log Analytics](diagnostic-logs-stream-log-store.md). |
| Überwachungslösungen | Überwachungslösungen schreiben gesammelte Daten in ihren Log Analytics-Arbeitsbereich. Eine Liste der Lösungen finden Sie unter [Ausführliche Informationen zu Datensammlungen für Verwaltungslösungen in Azure](../insights/solutions-inventory.md). Ausführliche Informationen zum Installieren und Verwenden von Lösungen finden Sie unter [Überwachungslösungen in Azure Monitor](../insights/solutions.md). |
| metrics | Senden von Plattformmetriken für Azure Monitor-Ressourcen an einen Log Analytics-Arbeitsbereich, um Protokolldaten über längere Zeiträume aufzubewahren und um mithilfe der [Abfragesprache Kusto](/azure/kusto/query/) komplexe Analysen mit anderen Datentypen durchzuführen. Informationen finden Sie unter [Streamen von Azure-Diagnoseprotokollen an Log Analytics](diagnostic-logs-stream-log-store.md). |
| Azure Table Storage | Sammeln von Daten aus Azure Storage, wohin einige Azure-Ressourcen Überwachungsdaten schreiben. Informationen finden Sie unter [Verwenden von Azure Blob Storage für IIS und Azure Table Storage für Ereignisse mit Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Daten | BESCHREIBUNG |
|:---|:---|
|  Agent-Datenquellen | Datenquellen, die von [Windows](agent-windows.md)- und [Linux](../learn/quick-collect-linux-computer.md)-Agents erfasst werden, enthalten Ereignisse, Leistungsdaten und benutzerdefinierte Protokolle. Eine Liste der Datenquellen und Details zur Konfiguration finden Sie unter [Agent-Datenquellen in Azure Monitor](data-sources.md). |
| Überwachungslösungen | Überwachungslösungen schreiben von Agents gesammelte Daten in ihren Log Analytics-Arbeitsbereich. Eine Liste der Lösungen finden Sie unter [Ausführliche Informationen zu Datensammlungen für Verwaltungslösungen in Azure](../insights/solutions-inventory.md). Ausführliche Informationen zum Installieren und Verwenden von Lösungen finden Sie unter [Überwachungslösungen in Azure Monitor](../insights/solutions.md). |
| System Center Operations Manager | Verbinden der Operations Manager-Verwaltungsgruppe mit Azure Monitor zum Sammeln der Ereignis- und Leistungsdaten von lokalen Agents in Protokollen. Ausführliche Informationen zu dieser Konfiguration finden Sie unter [Verbinden von Operations Manager mit Log Analytics](om-agents.md). |


### <a name="applications"></a>ANWENDUNGEN

| Daten | BESCHREIBUNG |
|:---|:---|
| Anforderungen und Ausnahmen | Ausführliche Daten zu Anwendungsanforderungen und -ausnahmen sind in den Tabellen _requests_, _pageViews_ und _exceptions_ enthalten. Aufrufe von [externen Komponenten](../app/asp-net-dependencies.md) sind in der Tabelle _dependencies_ enthalten. |
| Nutzung und Leistung | Leistungsdaten für die Anwendung finden Sie in den Tabellen _requests_, _browserTimings_ und _performanceCounters_. Daten für [benutzerdefinierte Metriken](../app/api-custom-events-metrics.md#trackevent) sind in der Tabelle _customMetrics_ enthalten.|
| Ablaufverfolgungsdaten | Ergebnisse der [verteilten Ablaufverfolgung](../app/distributed-tracing.md) sind in der Tabelle _traces_ gespeichert. |
| Verfügbarkeitstests | Zusammenfassungsdaten aus [Verfügbarkeitstests](../app/monitor-web-app-availability.md) sind in der Tabelle _availabilityResults_ gespeichert. Ausführliche Daten aus diesen Tests befinden sich in einem separatem Speicher, und es kann über Application Insights im Azure-Portal darauf zugegriffen werden. |

### <a name="insights"></a>Einblicke

| Daten | BESCHREIBUNG |
|:---|:---|
| Azure Monitor für Container | Von [Azure Monitor für Container](../insights/container-insights-overview.md) gesammelte Bestands-und Leistungsdaten. Eine Liste der Tabellen finden Sie unter [Details zur Datensammlung in Containern](../insights/container-insights-log-search.md#container-records). |
| Azure Monitor für VMs | Von [Azure Monitor für VMs](../insights/vminsights-overview.md) gesammelte Zuordnungs-und Leistungsdaten. Ausführliche Informationen zum Abfragen dieser Daten finden Sie unter [Abfragen von Protokollen aus Azure Monitor für VMs](../insights/vminsights-log-search.md). |

### <a name="custom"></a>Benutzerdefiniert 

| Daten | BESCHREIBUNG |
|:---|:---|
| REST-API | Schreiben von Daten in einen Log Analytics-Arbeitsbereich von einem beliebigen REST-Client. Ausführliche Informationen finden Sie unter [Senden von Protokolldaten an Azure Monitor mit der HTTP-Datensammler-API](data-collector-api.md).
| Logik-App | Schreiben von Daten in einen Log Analytics-Arbeitsbereich von einem Logik-App-Workflow mit der Aktion **Datensammler von Azure Log Analytics**. |

### <a name="security"></a>Sicherheit

| Daten | BESCHREIBUNG |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) speichert Daten, die in einem Log Analytics-Arbeitsbereich gesammelt werden, wo sie mit anderen Protokolldaten analysiert werden können. Ausführliche Informationen zur Konfiguration von Arbeitsbereichen finden Sie unter [Datensammlung in Azure Security Center](../../security-center/security-center-enable-data-collection.md). |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) speichert Daten aus Datenquellen in einem Log Analytics-Arbeitsbereich. Lesen Sie dazu [Herstellen einer Verbindung mit Datenquellen](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Azure Monitor-Datenplattform](data-platform.md).
- Informieren Sie sich über [Metriken in Azure Monitor](data-platform-metrics.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](data-sources.md).
