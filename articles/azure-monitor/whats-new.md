---
title: Neues in der Azure Monitor-Dokumentation
description: Wichtige Aktualisierungen für die Azure Monitor-Dokumentation (monatlich aktualisiert).
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 12/04/2020
ms.openlocfilehash: 91aa76fd3d9dbb54316f32fdb319e92ca2283b1d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745773"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Neues in der Azure Monitor-Dokumentation

Dieser Artikel enthält Listen mit neuen oder signifikant aktualisierten Azure Monitor-Artikeln. Er wird jeweils in der ersten Monatswoche mit Artikelaktualisierungen aus dem Vormonat aktualisiert.

## <a name="november-2020"></a>November 2020

### <a name="general"></a>Allgemein
- [Azure Monitor-Diensteinschränkungen](service-limits.md): Für Azure Arc-Unterstützung aktualisiert.

### <a name="agents"></a>Agents
- [Übersicht über die Azure-Überwachungs-Agents](platform/agents-overview.md): Für Azure Arc-Unterstützung aktualisiert.
- [Installieren des Azure Monitor-Agents (Vorschau)](platform/azure-monitor-agent-install.md): Neuer Artikel.
- [Übersicht über den Azure Monitor-Agent (Vorschauversion)](platform/azure-monitor-agent-overview.md): Für Azure Arc-Unterstützung aktualisiert.
- [Beispiele für Resource Manager-Vorlagen für Agents in Azure Monitor](samples/resource-manager-agent.md): Für Azure Arc-Unterstützung aktualisiert.

### <a name="alerts"></a>Benachrichtigungen
- [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md): Quell-IP-Adressen für Webhooks hinzugefügt.

### <a name="application-insights"></a>Application Insights
- [Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights](app/java-in-process-agent.md): Konfigurationsbeispiel hinzugefügt.
- [React-Plug-In für das Application Insights-JavaScript-SDK](app/javascript-react-plugin.md): Abschnitt zur Verwendung von React-Hooks hinzugefügt.
- [Upgraden von Application Insights Java SDK 2.x](app/java-standalone-upgrade-from-2x.md): Neuer Artikel.
- [Versionshinweise zu „Microsoft.ApplicationInsights.SnapshotCollector“](app/snapshot-collector-release-notes.md): Neuer Artikel.

### <a name="autoscale"></a>Autoscale
- [Erste Schritte mit der Autoskalierung in Azure](platform/autoscale-get-started.md): Abschnitt zum Verschieben der Autoskalierung in eine andere Region hinzugefügt.

### <a name="data-collection"></a>Datensammlung
- [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](platform/data-collection-rule-azure-monitor-agent.md): Für Azure Arc-Unterstützung aktualisiert.
- [Datensammlungsregeln in Azure Monitor (Vorschau)](platform/data-collection-rule-overview.md): Für Azure Arc-Unterstützung aktualisiert.
- [Resource Manager-Beispielvorlagen für Datensammlungsregeln](samples/resource-manager-data-collection-rules.md): Neuer Artikel.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen
- [Verbinden von Azure mit ITSM-Tools unter Verwendung von Secure Export](platform/it-service-management-connector-secure-webhook-connections.md): Abschnitt zur Verbindungsherstellung mit ServiceNow hinzugefügt.

### <a name="logs"></a>Protokolle
- [Integrieren von Log Analytics und Excel](log-query/log-excel.md): Neuer Artikel.
- [Log Analytics-Datensicherheit](platform/data-security.md): Abschnitt zu zusätzlichen Sicherheitsfeatures hinzugefügt.
- [Log Analytics-Integration in Power BI](log-query/log-powerbi.md): Neuer Artikel.
- [Standardspalten in Azure Monitor-Protokollen](platform/log-standard-columns.md): Spalte „_SubscriptionId“ hinzugefügt.

Neue und aktualisierte Artikel infolge der Umstrukturierung von Protokollabfrageinhalten.

- [Log Analytics-Tutorial](log-query/log-analytics-tutorial.md)
- [Protokollabfragen in Azure Monitor](log-query/log-query-overview.md)
- [Übersicht über Log Analytics in Azure Monitor](log-query/log-analytics-overview.md)
- [Beispiele für Abfragen für Azure Data Explorer und Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Tutorial: Verwenden von Kusto-Abfragen in Azure Data Explorer und Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtuelle Computer

- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md): Unterstützte Regionen hinzugefügt.

Neue Artikel zur Gastintegrität von Azure Monitor für VMs (Vorschau)

- [Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](insights/vminsights-health-overview.md)
- [Warnungen beim Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](insights/vminsights-health-alerts.md)
- [Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](insights/vminsights-health-configure.md)
- [Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs mit Datensammlungsregeln (Vorschau)](insights/vminsights-health-configure-dcr.md)
- [Aktivieren des Features „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](insights/vminsights-health-enable.md)
- [Problembehandlung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](insights/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Oktober 2020

### <a name="general"></a>Allgemein
- [API-Deaktivierung in Azure Monitor](platform/operationalinsights-api-retirement.md): Neuer Artikel.

### <a name="agents"></a>Agents
- [Was wird von Azure Monitor überwacht?](monitor-reference.md): Abschnitt zu Agents hinzugefügt.

### <a name="alerts"></a>Alerts
- [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md): Abschnitt zum Diensttag hinzugefügt.
- [Beispiele für Resource Manager-Vorlagen für Metrikwarnungen](samples/resource-manager-alerts-metric.md): Parameter für Inhaltsübereinstimmung und Teststandorte hinzugefügt.
- [Behandeln von Problemen mit Azure-Metrikwarnungen](platform/alerts-troubleshoot-metric.md): Bewährte Methode für die Regelkonfiguration hinzugefügt.

### <a name="application-insights"></a>Application Insights
- [Angular-Plug-In für das Application Insights JavaScript SDK](app/javascript-angular-plugin.md): Neuer Artikel.
- [Azure Application Insights für ASP.NET Core-Anwendungen](app/asp-net-core.md): Häufig gestellte Fragen zu ILogger-Protokollen hinzugefügt.
- [Konfigurieren der Überwachung für ASP.NET mit Azure Application Insights](app/asp-net.md): Artikel umgeschrieben.
- [Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md): Tabellen mit vorab aggregierten Metriken hinzugefügt.
- [Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites](app/monitor-web-app-availability.md): Abschnitt zu Auffüllungstags für den Standort hinzugefügt.
- [Überwachen von Java-Anwendungen an einem beliebigen Standort – Azure Monitor Application Insights](app/java-standalone-config.md): Konfigurationsbeispiel hinzugefügt.
- [Überwachen von Java-Anwendungen an einem beliebigen Standort – Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md): Neuer Artikel.
- [Verwenden der Anwendungsänderungsanalyse in Azure Monitor für die Suche nach Web-App-Problemen](app/change-analysis.md): Abschnitte zu VMs und zum Aktivitätsprotokoll hinzugefügt.
  
### <a name="autoscale"></a>Autoscale
- [Erste Schritte mit der Autoskalierung in Azure](platform/autoscale-get-started.md): Abschnitt zum Verschieben der Autoskalierung in eine andere Region hinzugefügt.

### <a name="containers"></a>Container
- [Konfigurieren der PV-Überwachung mit Azure Monitor für Container](insights/container-insights-persistent-volumes.md): Neuer Artikel.
- [Verwalten des Agents für Azure Monitor für Container](insights/container-insights-manage-agent.md): Unterstützung für Kubernetes-Cluster mit Azure Arc-Aktivierung hinzugefügt.
- [Metrikwarnungen von Azure Monitor für Container](insights/container-insights-metric-alerts.md): Unterstützung für Kubernetes-Cluster mit Azure Arc-Aktivierung hinzugefügt.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen
- [ITSM-Connector – Secure Export in Azure Monitor](platform/it-service-management-connector-secure-webhook-connections.md): Abschnitt zu ServiceNow hinzugefügt.

### <a name="logs"></a>Protokolle
- [Archivieren von Daten aus dem Log Analytics-Arbeitsbereich in Azure Storage mithilfe von Logic Apps](platform/logs-export-logic-app.md): Neuer Artikel.
- [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)](platform/logs-data-export.md): Beispieltext für die REST-Anforderung für Event Hub hinzugefügt.
- [Verwalten von Nutzung und Kosten für Azure Monitor-Protokolle](platform/manage-cost-storage.md): Informationen zur Beziehung zwischen Azure Monitor-Protokollen und der Azure Security Center-Abrechnung hinzugefügt. Abfrage für die Knotenanzahl bei Verwendung des Tarifs „Pro Knoten“ hinzugefügt. 
- [Überwachen der Integrität des Log Analytics-Arbeitsbereichs in Azure Monitor](platform/monitor-workspace.md): Neuer Artikel.
- [Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)](platform/azure-data-explorer-monitor-proxy.md): Neuer Artikel.
- [Abfragen exportierter Daten aus Azure Monitor mit Azure Data Explorer (Vorschau)](platform/azure-data-explorer-query-storage.md): Neuer Artikel.

### <a name="networks"></a>Netzwerke
- [Azure Monitor für Netzwerke (Vorschau)](insights/network-insights-overview.md): Abschnitt zur Problembehandlung hinzugefügt. Abschnitt zur Konnektivität hinzugefügt.

### <a name="platform-logs"></a>Plattformprotokolle
- [Ereignisschema des Azure-Aktivitätsprotokolls](platform/activity-log-schema.md): Beschreibung der Schweregrade hinzugefügt.

### <a name="virtual-machines"></a>Virtuelle Computer
- [Ändern der Analyse in Azure Monitor für VMs](insights/vminsights-change-analysis.md): Neuer Artikel.
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md): Unterstützte Regionen hinzugefügt.
- [Gewusst wie: Aktualisieren von Azure Monitor für Container für Metriken](insights/container-insights-update-metrics.md): Unterstützung für Kubernetes-Cluster mit Azure Arc-Aktivierung hinzugefügt.



## <a name="september-2020"></a>September 2020

### <a name="general"></a>Allgemein
- [Häufig gestellte Fragen zu Azure Monitor](faq.md): Abschnitt unter OpenTelemetry hinzugefügt.

### <a name="agents"></a>Agents
- [Übersicht über den Azure Monitor-Agent](platform/azure-monitor-agent-overview.md): Entscheidungsfaktoren für den Wechsel zu einem neuen Agent hinzugefügt.
- [Übersicht über die Azure-Überwachungs-Agents](platform/agents-overview.md): Unterstützung für Windows 10 hinzugefügt.

### <a name="alerts"></a>Alerts
- [Erstellen einer Protokollwarnung anhand einer Resource Manager-Vorlage](platform/alerts-log-create-templates.md): Neuer Artikel.
- [Behandeln von Problemen mit Azure-Metrikwarnungen](platform/alerts-troubleshoot-metric.md): Abschnitt zum Export einer ARM-Vorlage für eine Metrikwarnungsregel hinzugefügt.

### <a name="application-insights"></a>Application Insights
- [Erstellen einer neuen arbeitsbereichsbasierten Application Insights-Ressource in Azure Monitor](app/create-workspace-resource.md): Bezeichnung als Vorschauversion entfernt.
- [Datenaufbewahrung und -speicherung in Azure Application Insights](app/data-retention-privacy.md): Details zur neuen Unterstützung des Schutzes vor Datenverlust bei Mac und Linux hinzugefügt.
- [EventCounters in Application Insights](app/eventcounters.md): Hinweis zu standardmäßig erfassten Leistungsindikatoren hinzugefügt.
- [Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md): Bezeichnung als Vorschauversion entfernt.
- [Migrieren einer klassischen Application Insights-Ressource in Azure Monitor zu einer arbeitsbereichsbasierten Ressource](app/convert-classic-resource.md): Neuer Artikel.
- [Überwachen von Java-Anwendungen in beliebiger Umgebung – Azure Monitor Application Insights](app/java-in-process-agent.md): Für neue Vorschauversion des Agents aktualisiert.
- [Einrichten der Web-App-Analyse für ASP.NET mit Azure Application Insights](app/asp-net.md): Artikel umgeschrieben.
- [Telemetriekanäle in Azure Application Insights](app/telemetry-channels.md): Details zur neuen Unterstützung des Schutzes vor Datenverlust bei Mac und Linux hinzugefügt.
- [Problembehandlung für den Azure Application Insights-Momentaufnahmedebugger](app/snapshot-debugger-troubleshoot.md): SSL-Abschnitt für Problembehandlung des Momentaufnahmedebuggers hinzugefügt.
- [Verwenden der Anwendungsänderungsanalyse in Azure Monitor für die Suche nach Web-App-Problemen](app/change-analysis.md): VM- und Aktivitätsprotokoll hinzugefügt.


### <a name="containers"></a>Container
- [Konfigurieren von Kubernetes-Clustern mit Azure Arc-Aktivierung mit Azure Monitor für Container](insights/container-insights-enable-arc-enabled-clusters.md): Anleitung zum Aktivieren der Überwachung per Dienstprinzipal hinzugefügt.
- [Bereitstellung und HPA-Metriken mit Azure Monitor für Container](insights/container-insights-deployment-hpa-metrics.md) – Neuer Artikel.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen
- [Azure Monitor für Azure Cache for Redis](insights/redis-cache-insights-overview.md): Bezeichnung als Vorschauversion entfernt.
- [Azure Monitor für Netzwerke (Vorschau)](insights/network-insights-overview.md): Abschnitte zur Konnektivität und zu Datenverkehr hinzugefügt.
- [ITSM-Connector – Secure Export in Azure Monitor](platform/it-service-management-connector-secure-webhook-connections.md): Neuer Artikel.
- [IT Service Management Connector in Azure Monitor](platform/itsmc-connections.md): Hinweis zu ITSM-Integrationen von Cherwell und Provance.
- [Überwachen von Key Vault mit Azure Monitor für Key Vault](insights/key-vault-insights-overview.md): Bezeichnung als Vorschauversion entfernt.

### <a name="logs"></a>Protokolle
- [Überwachen von Abfragen in Azure Monitor-Protokollabfragen](log-query/query-audit.md): Neuer Artikel.
- [Kundenseitig verwaltete Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Kunden-Lockbox hinzugefügt.
- [Azure Monitor-Protokolle: Dedizierte Cluster](log-query/logs-dedicated-clusters.md): Neuer Artikel.
- [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](platform/design-logs-deployment.md): Aktualisierung des Abschnitts zur Begrenzung der Skalierung und Erfassungsvolumenrate.
- [Protokollabfragebereich in Azure Monitor Log Analytics](log-query/scope.md): Aktualisierungen zur Einbindung von arbeitsbereichsbasierten Anwendungen.
- [Protokolle in Azure Monitor](platform/data-platform-logs.md): Aktualisierungen zur Einbindung von arbeitsbereichsbasierten Anwendungen.
- [Standardspalten in Azure Monitor-Protokolldatensätzen](platform/log-standard-columns.md): Aktualisierungen zur Einbindung von arbeitsbereichsbasierten Anwendungen.
- [Azure Monitor-Diensteinschränkungen](service-limits.md): Grenzwerte für Drosselung von Benutzerabfragen aktualisiert.
- [Verwenden von kundenseitig verwalteten Speicherkonten in Azure Monitor Log Analytics](platform/private-storage.md): Artikel umgeschrieben.
- [Anzeigen und Analysieren von Daten in Azure Log Analytics](./platform/data-platform-logs.md): Aktualisierungen zur Einbindung von arbeitsbereichsbasierten Anwendungen.


### <a name="platform-logs"></a>Plattformprotokolle
- [Ereignisschema des Azure-Aktivitätsprotokolls: Azure Monitor](platform/activity-log-schema.md): Schweregrade hinzugefügt.
- [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen](samples/resource-manager-diagnostic-settings.md): Beispiel für Azure-Speicherkonto hinzugefügt.

### <a name="visualizations"></a>Visualisierungen
- [Diagrammvisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-chart-visualizations.md): Neuer Artikel.
- [Renderer für zusammengesetzte Balken in der Azure Monitor-Arbeitsmappe](platform/workbooks-composite-bar.md): Neuer Artikel.
- [Graphvisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-graph-visualizations.md): Neuer Artikel.
- [Rastervisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-grid-visualizations.md): Neuer Artikel.
- [Wabenförmige Visualisierungen](platform/workbooks-honey-comb.md): Neuer Artikel.
- [Textvisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-text-visualizations.md): Neuer Artikel.
- [Kachelvisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-tile-visualizations.md): Neuer Artikel.
- [Strukturvisualisierungen für Azure Monitor-Arbeitsmappen](platform/workbooks-tree-visualizations.md): Neuer Artikel.




## <a name="august-2020"></a>August 2020

### <a name="general"></a>Allgemein

- [Was wird von Azure Monitor überwacht?](monitor-reference.md): Mit Informationen zum Azure Monitor-Agent aktualisiert


### <a name="agents"></a>Agents
- [Übersicht über den Azure Monitor-Agent](platform/azure-monitor-agent-overview.md): Neuer Artikel
- [Aktivieren von Azure Monitor für eine Hybridumgebung](insights/vminsights-enable-hybrid.md): Für Version des Dependency-Agents aktualisiert
- [Übersicht über die Azure-Überwachungs-Agents](platform/agents-overview.md): Informationen zum Azure Monitor-Agent und zusammengefasste Tabelle zur Betriebssystemunterstützung hinzugefügt


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Neue und aktualisierte Artikel nach der Umstrukturierung der Agent-Inhalte
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md)
- [Installieren des Log Analytics-Agents auf Linux-Computern](platform/agent-linux.md)
- [Installieren des Log Analytics-Agents auf Windows-Computern](platform/agent-windows.md)
- [Übersicht über den Log Analytics-Agent](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights für JavaScript-Web-Apps](app/javascript.md): Abschnitt mit Informationen zur Client/Server-Korrelation und -Konfiguration für die CORS-Korrelation hinzugefügt
- [Erstellen einer neuen arbeitsbereichsbasierten Application Insights-Ressource in Azure Monitor](app/create-workspace-resource.md): Funktionen von arbeitsbereichsbasierten Anwendungen hinzugefügt
- [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md): IP-Adressen für Live Metrics Stream aktualisiert
- [Überwachen von Java-Anwendungen in beliebiger Umgebung – Azure Monitor Application Insights](app/java-in-process-agent.md): Tabelle mit Informationen zur unterstützten benutzerdefinierten Telemetrie hinzugefügt
- [React Native-Plug-In für das Application Insights JavaScript SDK](app/javascript-react-native-plugin.md): Neuer Artikel
- [React-Plug-In für das Application Insights-JavaScript-SDK](app/javascript-react-plugin.md): Neuer Artikel
- [Resource Manager-Vorlagenbeispiel zum Erstellen von Azure-Funktions-Apps mit Application Insights-Überwachung](samples/resource-manager-function-app.md): Neuer Artikel
- [Resource Manager-Vorlagenbeispiele zum Erstellen von Azure App Service-Web-Apps mit Application Insights-Überwachung](samples/resource-manager-web-app.md): Neuer Artikel
- [Verwendungsanalyse mit Azure Application Insights](app/usage-overview.md): Video hinzugefügt

### <a name="autoscale"></a>Autoscale
- [Erste Schritte mit der automatischen Skalierung in Azure](platform/autoscale-get-started.md): Abschnitt zum Routing zu fehlerfreien Instanzen für App Service hinzugefügt

### <a name="data-collection"></a>Datensammlung
- [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](platform/data-collection-rule-azure-monitor-agent.md): Neuer Artikel
- [Datensammlungsregeln in Azure Monitor (Vorschau)](platform/data-collection-rule-overview.md): Neuer Artikel


### <a name="containers"></a>Container
- [Bereitstellung und HPA-Metriken mit Azure Monitor für Container](insights/container-insights-deployment-hpa-metrics.md) – Neuer Artikel.

### <a name="insights"></a>Einblicke
- [Überwachungslösungen in Azure Monitor](insights/solutions.md): Für neue Benutzeroberfläche aktualisiert
- [Netzwerkleistungsmonitor-Lösung in Azure](insights/network-performance-monitor.md): Unterstützte Arbeitsbereichsregionen hinzugefügt


### <a name="logs"></a>Protokolle
- [Häufig gestellte Fragen zu Azure Monitor](faq.md): Eintrag zum Löschen von Daten aus einem Arbeitsbereich hinzugefügt. Eintrag zu den Antworten für 502 und 503 hinzugefügt.
  - [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](platform/design-logs-deployment.md): Aktualisierung des Abschnitts zum Ratenlimit für das Datenerfassungsvolumen
- [Verwalten von Nutzung und Kosten für Azure Monitor-Protokolle](platform/manage-cost-storage.md): Aktualisierung von Nutzungsabfragen in ein effizienteres Abfrageformat
- [Optimieren von Protokollabfragen in Azure Monitor](log-query/query-optimization.md): Spezifische Werte für Leistungsindikatoren hinzugefügt
- [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen](samples/resource-manager-diagnostic-settings.md): Beispiel für Überwachungsprotokolle für Protokollabfragen hinzugefügt


### <a name="platform-logs"></a>Plattformprotokolle
- [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](platform/diagnostic-settings.md): Regionale Anforderung für Diagnoseeinstellungen hinzugefügt

### <a name="visualizations"></a>Visualisierungen
- [Übersicht über Azure Monitor-Arbeitsmappen](platform/workbooks-overview.md): Video hinzugefügt
- [Verschieben einer Azure-Arbeitsmappenvorlage in eine andere Region](platform/workbook-templates-move-region.md): Neuer Artikel
- [Verschieben einer Azure-Arbeitsmappe in eine andere Region](platform/workbooks-move-region.md): Neuer Artikel



## <a name="july-2020"></a>Juli 2020

### <a name="general"></a>Allgemein
- [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](deploy-scale.md): Inhalt zum Onboarding von Azure Monitor für VMs neu strukturiert
- [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](platform/private-link-security.md): Abschnitt zu Grenzwerten hinzugefügt

### <a name="alerts"></a>Alerts
- [Aktionsregeln (Vorschau)](platform/alerts-action-rules.md): CLI-Prozesse hinzugefügt
- [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md): Aktualisiert, um Änderungen an der Benutzeroberfläche zu berücksichtigen
- [Beispielabfragen in Azure Monitor Log Analytics](log-query/example-queries.md): Neuer Artikel
- [Behandeln von Protokollwarnungen in Azure Monitor](platform/alerts-troubleshoot-log.md): Abschnitt mit Informationen zum Warnungsregelkontingent hinzugefügt
- [Behandeln von Problemen mit Azure Monitor-Metrikwarnungen](platform/alerts-troubleshoot-metric.md): Abschnitt mit Informationen zur Warnungsregel für eine benutzerdefinierte, noch nicht ausgegebene Metrik hinzugefügt
- [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](platform/alerts-metric-overview.md): Empfehlung für die Wahl der Aggregationsgranularität hinzugefügt

### <a name="application-insights"></a>Application Insights
- [Versionshinweise für die Erweiterung für Azure-Web-Apps für Application Insights](app/web-app-extension-release-notes.md): Neuer Artikel
- [Resource Manager-Vorlagenbeispiele zum Erstellen von Application Insights-Ressourcen](samples/resource-manager-app-resource.md): Neuer Artikel
- [Behandeln von Problemen mit dem Aktivieren oder Anzeigen von Application Insights Profiler](app/profiler-troubleshooting.md): Hinweis auf einen Fehler beim Ausführen des Profilers für ASP.NET Core-Apps in Azure App Service hinzugefügt 

### <a name="containers"></a>Container
- [Erstellen von Protokollwarnungen aus Azure Monitor für Container](insights/container-insights-log-alerts.md): Neuer Artikel
- [Empfohlene Metrikwarnungen (Vorschau) aus Azure Monitor für Container](insights/container-insights-metric-alerts.md): Neuer Artikel

### <a name="logs"></a>Protokolle
- [Kundenseitig verwaltete Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Fehlermeldung und Abschnitt mit Informationen zur CMK-Konfiguration für Abfragen hinzugefügt
- [Senden von Protokolldaten an Azure Monitor mit der HTTP-Datensammler-API (Public Preview)](platform/data-collector-api.md): Python 3-Beispiel hinzugefügt
- [Optimieren von Protokollabfragen in Azure Monitor](log-query/query-optimization.md): Abschnitt hinzugefügt, in dem erläutert wird, wie sich bei Verwendung von Unterabfragen mehrere Datenscans vermeiden lassen
- [Tutorial: Erste Schritte mit Log Analytics-Abfragen](./log-query/log-analytics-tutorial.md): Video hinzugefügt

### <a name="platform-logs"></a>Plattformprotokolle
- [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](platform/diagnostic-settings.md): Video hinzugefügt
- [Beispiele für Resource Manager-Vorlagen für Azure Monitor](samples/resource-manager-samples.md): ARM-Beispiel mit Zieltyp „Protokolle“ hinzugefügt 

### <a name="solutions"></a>Lösungen
- [Überwachungslösungen in Azure Monitor](insights/solutions.md): CLI-Prozesse hinzugefügt
- [Office 365-Verwaltungslösung in Azure (Vorschau)](insights/solution-office-365.md): Deaktivierungstermin geändert

### <a name="virtual-machines"></a>Virtuelle Computer

Neue und aktualisierte Artikel infolge der Umstrukturierung der Inhalte zu Azure Monitor für VMs

- [Was ist Azure Monitor für VMs?](insights/vminsights-overview.md)
- [Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs](insights/vminsights-configure-workspace.md)
- [Verbinden von Linux-Computern mit Azure Monitor](platform/agent-linux.md)
- [Aktivieren von Azure Monitor für eine Hybridumgebung](insights/vminsights-enable-hybrid.md)
- [Aktivieren von Azure Monitor für einen einzelnen virtuellen Computer oder eine einzelne VM-Skalierungsgruppe im Azure-Portal](insights/vminsights-enable-portal.md)
- [Aktivieren von Azure Monitor für VMs mit Azure Policy](./insights/vminsights-enable-policy.md)
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md)
- [Aktivieren von Azure Monitor für VMs mithilfe von PowerShell](insights/vminsights-enable-powershell.md)
- [Aktivieren von Azure Monitor für VMs mit Resource Manager-Vorlagen](insights/vminsights-enable-resource-manager.md)
- [Aktivieren von Azure Monitor für VMs mithilfe von PowerShell](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualisierungen
- [Aktualisieren von Log Analytics-Dashboardvisualisierungen](log-query/dashboard-upgrade.md): Aktualisierungsrate aktualisiert
- [Visualisieren von Daten aus Azure Monitor](visualizations.md): Video hinzugefügt


## <a name="june-2020"></a>Juni 2020

### <a name="general"></a>Allgemein
- [Bereitstellen von Azure Monitor](deploy-scale.md): Neuer Artikel.
- [Kundenseitig verwaltete Azure Monitor-Schlüssel](platform/customer-managed-keys.md): Aktualisierte Abrechnungstypeigenschaft. Hinzugefügte PowerShell-Befehle.

### <a name="agents"></a>Agents
- [Übersicht über Log Analytics-Agent](platform/log-analytics-agent.md): Hinzugefügte Python 2-Anforderung.

### <a name="alerts"></a>Alerts
- [Aktualisieren von Warnungs- oder Aktionsregeln beim Verschieben der entsprechenden Zielressource in eine andere Azure-Region](platform/alerts-resource-move.md): Neuer Artikel.
- [Behandeln von Problemen mit Azure-Metrikwarnungen](platform/alerts-troubleshoot-metric.md): Neuer Artikel.
- [Behandeln von Aktivitätsprotokollwarnungen in Azure Monitor](platform/alerts-troubleshoot-metric.md): Neuer Artikel.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights für JavaScript-Web-Apps](app/javascript.md): Aktualisierung des JavaScript SDK-Abschnitts. Der Codeausschnitt wurde aktualisiert, um Ladefehler zu melden.
- [Konfigurieren von BYOS (Bring Your Own Storage) für Profiler und Momentaufnahmedebugger](app/profiler-bring-your-own-storage.md): Neuer Artikel.
- [Nachverfolgung eingehender Anforderungen in Azure Application Insights mit OpenCensus Python](app/opencensus-python-request.md): Aktualisierte Protokollierung und Konfiguration für OpenCensus.
- [Überwachen einer ASP.NET-Web-App mit Azure Application Insights](app/monitor-performance-live-website-now.md): Aktualisiertes Ablaufdatum für Statusmonitor v1
- [Überwachen von Node.js-Diensten mit Azure Application Insights](app/nodejs.md): Mehrere Updates, einschließlich Migration von früheren Versionen und SDK-Konfiguration.
- [Überwachen von Python-Anwendungen mit Azure Monitor (Vorschau)](app/opencensus-python.md): Hinzugefügter Abschnitt zum Konfigurieren von Azure Monitor-Exportprogrammen.
- [Überwachen von Apps ohne Codeänderungen – automatische Instrumentation für Azure Monitor Application Insights](app/codeless-overview.md): Neuer Artikel.
- [Beheben von Fehlern beim Laden des SDK für JavaScript-Webanwendungen](app/javascript-sdk-load-failure.md): Neuer Artikel.

### <a name="containers"></a>Container
- [Beenden der Überwachung Ihres Kubernetes-Hybridclusters](insights/container-insights-optout-hybrid.md): Hinzugefügter Abschnitt für Arc-aktivierte Kubernetes-Lösungen.
- [Konfigurieren von Kubernetes-Clustern mit Azure Arc-Aktivierung mit Azure Monitor für Container](insights/container-insights-enable-arc-enabled-clusters.md): Neuer Artikel.
- [Konfigurieren von Azure Red Hat OpenShift, Version 4.x mit Azure Monitor für Container](insights/container-insights-azure-redhat4-setup.md): Aktualisierte Voraussetzungen.
- [Einrichten von Azure Monitor für Containerlivedaten (Vorschau)](insights/container-insights-livedata-setup.md): Entfernter Hinweis, dass die Funktion in Azure US Government nicht verfügbar ist.

### <a name="insights"></a>Einblicke
- [FAQs: Netzwerkleistungsmonitor-Lösung in Azure](insights/network-performance-monitor-faq.md): Hinzugefügte häufig gestellte Fragen zu Azure ExpressRoute Monitor.

### <a name="logs"></a>Protokolle
- [Löschen und Wiederherstellen eines Azure Log Analytics-Arbeitsbereichs](platform/delete-workspace.md): Hinzugefügter PowerShell-Befehl. Aktualisierte Problembehandlung.
- [Verwalten von Log Analytics-Arbeitsbereichen in Azure Monitor](platform/manage-access.md): Hinzugefügtes Beispiel für unzulässige Tabellen im Azure RBAC-Abschnitt.
- [Verwalten der Nutzung und Kosten für Azure Monitor Protokolle](platform/manage-cost-storage.md): Zusätzliche Details zur Berechnung der Datengröße. Aktualisiertes Konfigurieren von Datenvolumewarnungen. Details zu Sicherheitsdaten, die von Azure Sentinel erfasst werden. Präzisierung zur Datenobergrenze.
- [Verwenden von Azure Monitor-Protokollen mit Azure Logic Apps und Power Automate](platform/logicapp-flow-connector.md): Hinzugefügte Connectorgrenzwerte

### <a name="metrics"></a>Metriken
- [Von Azure Monitor unterstützte Metriken nach Ressourcentyp](platform/metrics-supported.md): Aktualisierte SQL Server-Metriken.


### <a name="platform-logs"></a>Plattformprotokolle

- [Resource Manager-Vorlagenbeispiele für Diagnoseeinstellungen](samples/resource-manager-diagnostic-settings.md): Fix für Diagnoseeinstellung des Aktivitätsprotokolls.
- [Senden eines Azure-Aktivitätsprotokolls an einen Log Analytics-Arbeitsbereich unter Verwendung des Azure-Portals](learn/quick-collect-activity-log-portal.md): Neuer Artikel.
- [Senden eines Azure-Aktivitätsprotokolls an den Log Analytics-Arbeitsbereich per Azure Resource Manager-Vorlage](learn/quick-collect-activity-log-arm.md): Neuer Artikel.

Neue und aktualisierte Artikel aus der Neustrukturierung und Konsolidierung von Plattformprotokollinhalten

- [Archivieren von Azure-Ressourcenprotokollen in einem Speicherkonto](./platform/resource-logs.md#send-to-azure-storage)
- [Ereignisschema des Azure-Aktivitätsprotokolls](platform/activity-log-schema.md)
- [Azure-Aktivitätsprotokoll](platform/activity-log.md)
- [CLI-Beispiele für Azure Monitor](samples/cli-samples.md)
- [Beispiele zu PowerShell in Azure Monitor](samples/powershell-samples.md)
- [Exemplarische Vorgehensweise für die Azure Monitoring-REST-API](platform/rest-api-walkthrough.md)
- [Azure-Ressourcenprotokolle: Unterstützte Dienste und Schemas](./platform/resource-logs-schema.md)
- [Azure-Ressourcenprotokolle](platform/resource-logs.md)
- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](./platform/activity-log.md)
- [Erfassen von Protokollen der Azure-Plattform im Log Analytics-Arbeitsbereich in Azure Monitor](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](platform/diagnostic-settings.md)
- [Exportieren des Aktivitätsprotokolls in den Speicher oder in Azure Event Hubs](./platform/activity-log.md#legacy-collection-methods)
- [Übersicht über Protokolle der Azure-Plattform](platform/platform-logs-overview.md)
- [Streamen von Protokollen der Azure-Plattform an Azure Event Hubs](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Anzeigen von Azure-Aktivitätsprotokollereignissen in Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuelle Computer
- [Aktivieren von Azure Monitor für VMs im Azure-Portal](./insights/vminsights-enable-portal.md): Aktualisiert, um Azure Arc zu berücksichtigen.
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md): Aktualisiert, um Azure Arc zu berücksichtigen.
- [Was ist Azure Monitor für VMs?](insights/vminsights-overview.md) – Aktualisiert, um Azure Arc zu berücksichtigen.


### <a name="visualizations"></a>Visualisierungen
- [Azure Monitor Arbeitsmappen-Datenquellen](platform/workbooks-data-sources.md): Hinzugefügter Abschnitt zu Warnungen und benutzerdefinierten Endpunkten.
- [Problembehandlung von auf Azure Monitor-Arbeitsmappen basierenden Erkenntnissen](insights/troubleshoot-workbooks.md): Neuer Artikel.
- [Aktualisieren von Log Analytics-Dashboardvisualisierungen](log-query/dashboard-upgrade.md): Neuer Artikel.



## <a name="may-2020"></a>Mai 2020

### <a name="general"></a>Allgemein

- [Häufig gestellte Fragen zu Azure Monitor](faq.md): Neuer Abschnitt zu Metriken.
- [Kundenseitig verwaltete Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Verschiedene Änderungen zur Vorbereitung auf die allgemeine Verfügbarkeit.
- [Integrierte Azure Policy-Richtliniendefinitionen für Azure Monitor](./samples/policy-reference.md): Neuer Artikel.
- [Kundeneigene Speicherkonten für die Protokollerfassung in Azure Monitor](platform/private-storage.md): Neuer Artikel.
- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md): Proportionale Abrechnung für Cluster hinzugefügt.
- [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](platform/private-link-security.md): Neuer Artikel.


#### <a name="new-resource-manager-template-samples"></a>Neue Azure Resource Manager-Beispielvorlagen 
- [Beispiele für Resource Manager-Vorlagen für Azure Monitor](samples/resource-manager-samples.md)
- [Beispiele für Resource Manager-Vorlagen für Aktionsgruppen](samples/resource-manager-action-groups.md)
- [Beispiele für Resource Manager-Vorlagen für Agents](samples/resource-manager-agent.md)
- [Beispiele für Resource Manager-Vorlagen für Azure Monitor für Container](samples/resource-manager-container-insights.md)
- [Beispiele für Resource Manager-Vorlagen für Azure Monitor für VMs](samples/resource-manager-vminsights.md)
- [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen](samples/resource-manager-diagnostic-settings.md)
- [Beispiele für Resource Manager-Vorlagen für Log Analytics-Arbeitsbereiche](samples/resource-manager-workspace.md)
- [Beispiele für Resource Manager-Vorlagen für Protokollabfragen](samples/resource-manager-log-queries.md)
- [Beispiele für Resource Manager-Vorlagen für Protokollwarnungsregeln in Azure Monitor](samples/resource-manager-alerts-log.md)
- [Beispiele für Resource Manager-Vorlagen für Metrikwarnungsregeln in Azure Monitor](samples/resource-manager-alerts-metric.md)
- [Beispiele für Resource Manager-Vorlagen für Arbeitsmappen in Azure Monitor](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agents
- [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](platform/diagnostics-extension-windows-install.md): Neue Details zur Konfiguration von Diagnoseeinstellungen.
- [Übersicht über den Log Analytics-Agent](platform/log-analytics-agent.md): Unterstützte Linux-Versionen hinzugefügt.

### <a name="application-insights"></a>Application Insights

- [Überwachen von Anwendungen, die in Azure Functions mit Application Insights ausgeführt werden: Azure Monitor](app/monitor-functions.md): Neuer Artikel.
- [Überwachen Ihrer Node.js-Dienste und -Apps mit Application Insights](app/nodejs.md): Allgemeine Updates, u. a. ein neuer Abschnitt zur Migration von früheren Versionen.
- [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md): IP-Adressen für Webhooks und US Government hinzugefügt.
- [Überwachen von Anwendungen in Azure Kubernetes Service (AKS) mit Application Insights: Azure Monitor](app/kubernetes-codeless.md): Neuer Artikel.
- [Problembehandlung ohne Daten – Application Insights für .NET](app/asp-net-troubleshoot-no-data.md): Neuer Abschnitt zum Erfassen von Protokollen mit dotnet-trace hinzugefügt.
- [Verwenden der Anwendungsänderungsanalyse in Azure Monitor für die Suche nach Web-App-Problemen](app/change-analysis.md): Mehrere Updates bei der Änderungsanalyse.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Neue und aktualisierte Artikel für die Vorschau von arbeitsbereichsbasierten Anwendungen
- [Schema für arbeitsbereichsbasierte Application Insights-Ressourcen in Azure Monitor](app/apm-tables.md)
- [Erstellen einer neuen arbeitsbereichsbasierten Application Insights-Ressource in Azure Monitor](app/create-workspace-resource.md)
- [app()-Ausdruck in Azure Monitor-Abfragen](log-query/app-expression.md)
- [Protokollabfragebereich in Azure Monitor Log Analytics](log-query/scope.md)
- [Ressourcenübergreifende Abfragen mit Azure Monitor](log-query/cross-workspace-query.md)
- [Standardeigenschaften in Azure Monitor-Protokolldatensätzen](./platform/log-standard-columns.md)
- [Struktur von Azure Monitor-Protokollen](./platform/data-platform-logs.md)





### <a name="containers"></a>Container
- [Aktivieren von Azure Monitor für Container](insights/container-insights-onboard.md): Aktualisierte Firewallkonfigurationstabelle.
- [Gewusst wie: Aktualisieren von Azure Monitor für Container zum Aktivieren von Metriken](insights/container-insights-update-metrics.md): Update zur Verwendung von verwalteten Identitäten, um Metriken zu erfassen.
- [Überwachen der Kosten für Azure Monitor für Container](insights/container-insights-cost.md): Neuer Artikel.
- [Einrichten von Azure Monitor für Containerlivedaten (Vorschau)](insights/container-insights-livedata-setup.md): Unterstützung der neuen Clusterrollenbindung.

### <a name="insights"></a>Einblicke
- [Informationen zu Azure Monitor für Azure Cache for Redis (Vorschau)](insights/redis-cache-insights-overview.md): Neuer Artikel.
- [Überwachen Ihres Schlüsseltresordiensts mit Azure Monitor für Key Vault (Vorschauversion)](./insights/key-vault-insights-overview.md): Neuer Artikel.

### <a name="logs"></a>Protokolle
- [Erstellen und Konfigurieren von Log Analytics mit PowerShell](platform/powershell-workspace-configuration.md): Abschnitt zur Problembehandlung hinzugefügt.
- [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](learn/quick-create-workspace.md): Abschnitt zur Problembehandlung hinzugefügt.
- [Erstellen eines Log Analytics-Arbeitsbereichs mit Azure CLI](learn/quick-create-workspace-cli.md): Abschnitt zur Problembehandlung hinzugefügt.
- [Löschen und Wiederherstellen eines Azure Log Analytics-Arbeitsbereichs](platform/delete-workspace.md): Aktualisierte Informationen zum Wiederherstellen eines gelöschten Arbeitsbereichs.
- [Verwenden von Funktionen in Azure Monitor-Protokollabfragen](log-query/functions.md): Hinweis zu Funktionen entfernt, die keine anderen Funktionen enthalten.
- [Struktur von Azure Monitor-Protokollen](./platform/data-platform-logs.md): Eigenschaftsbeschreibungen für Application Insights-Tabelle verbessert.
- [Verwenden von Azure Monitor-Protokollen mit Azure Logic Apps und Power Automate](platform/logicapp-flow-connector.md): Abschnitt zu Einschränkungen hinzugefügt.
- [Verwenden von PowerShell zum Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs](platform/powershell-workspace-configuration.md): Abschnitt zur Problembehandlung hinzugefügt.


### <a name="metrics"></a>Metriken
- [Von Azure Monitor unterstützte Metriken nach Ressourcentyp](platform/metrics-supported.md): Angaben zu Gastmetriken und Metrikrouting verbessert. 

### <a name="solutions"></a>Lösungen
- [Optimieren Ihrer Active Directory-Umgebung mit Azure Monitor](insights/ad-assessment.md): Windows Server 2019 zu den unterstützten Versionen hinzugefügt.
- [Optimieren der SQL Server-Umgebung mit Azure Monitor](insights/sql-assessment.md): Unterstützte Versionen von SQL Server ergänzt.


### <a name="virtual-machines"></a>Virtuelle Computer
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md): Unterstützte Versionen von Ubuntu Server ergänzt. Unterstützte Regionen für Log Analytics-Arbeitsbereiche hinzugefügt.
- [Darstellen der Leistung in Diagrammen mit Azure Monitor für VMs](insights/vminsights-performance.md): Abschnitt mit Einschränkungen für nicht verfügbare Metriken hinzugefügt.

### <a name="visualizations"></a>Visualisierungen
- [Programmgesteuertes Verwalten von Arbeitsmappen:](platform/workbooks-automate.md) Resource Manager-Update zum Bereitstellen einer Arbeitsmappenvorlage hinzugefügt.
- [Gruppen in Azure Monitor-Arbeitsmappen](platform/workbooks-groups.md): Neuer Artikel.
- [Azure Monitor-Arbeitsmappen: Transformieren von JSON-Daten mit JSONPath](platform/workbooks-jsonpath.md): Neuer Artikel.


## <a name="april-2020"></a>April 2020

### <a name="general"></a>Allgemein

- [Konfiguration kundenseitig verwalteter Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Abschnitt mit Informationen zu asynchronen Vorgängen hinzugefügt.
- [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](platform/manage-access.md): Abschnitte zu benutzerdefinierten Protokollen aktualisiert.

### <a name="alerts"></a>Alerts

- [Aktionsregeln für Azure Monitor-Warnungen](platform/alerts-action-rules.md): Video hinzugefügt.
- [Überblick über Warnungen in Microsoft Azure](platform/alerts-overview.md): Video hinzugefügt.

### <a name="application-insights"></a>Application Insights

- [Anwendungsübersicht: Selektieren verteilter Anwendungen](app/app-map.md): Konfiguration für Cloudrollennamen für den Java-Agent hinzugefügt.
- [API-Referenz für den Azure Application Insights-.NET-Agent](app/status-monitor-v2-api-reference.md): API-Referenz konsolidiert.
- [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md): IP-Adressen für die App Insights- und die Log Analytics-API, Aktionsgruppen-Webhooks und Azure US Government aktualisiert.
- [Konfigurationsoptionen: Eigenständige Java-Agents für Azure Monitor Application Insights](app/java-standalone-config.md): Neuer Artikel.
- [Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights – Public Preview](app/java-in-process-agent.md): Neuer Artikel.
- [Konfigurieren eines eigenständigen Java-Agents für JVM-Argumente für Azure Monitor Application Insights](app/java-standalone-arguments.md): Neuer Artikel.
- [Lokale Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights – öffentliche Vorschau](app/java-on-premises.md): Neuer Artikel.
- [Entfernen von Application Insights in Visual Studio](app/remove-application-insights.md): Neuer Artikel.
- [Erstellen von Stichproben in Application Insights](app/sampling.md): Beispiel mit festem Prozentsatz in Python korrigiert.

### <a name="containers"></a>Container

- [Konfigurieren von Azure Red Hat OpenShift, Version 4.x mit Azure Monitor für Container](insights/container-insights-azure-redhat4-setup.md): Neuer Artikel.
- [Manuelles Beheben von ServiceNow-Synchronisierungsproblemen](platform/itsmc-resync-servicenow.md): Neuer Artikel.
- [Beenden der Überwachung Ihres Azure- und Red Hat OpenShift v4-Clusters](insights/container-insights-optout-openshift-v4.md): Neuer Artikel.
- [Beenden der Überwachung Ihres Azure Red Hat OpenShift 3-Clusters](insights/container-insights-optout-openshift-v3.md): Neuer Artikel.
- [Beenden der Überwachung Ihres Hybridclusters](insights/container-insights-optout-hybrid.md): Neuer Artikel.

### <a name="insights"></a>Einblicke

- [Überwachen von Azure Key Vault-Instanzen mit Azure Monitor für Key Vault-Instanzen (Vorschauversion)](insights/key-vault-insights-overview.md): Neuer Artikel.

### <a name="logs"></a>Protokolle

- [Azure Monitor-Diensteinschränkungen](service-limits.md): Drosselung von Benutzerabfragen hinzugefügt.
- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md): Abrechnung für Protokollcluster hinzugefügt. Kusto-Abfrage hinzugefügt, mit der Kunden, die über den Legacytarif „Pro Knoten“ verfügen, ermitteln können, ob sie zu einem Tarif vom Typ „Pro GB“ oder „Kapazitätsreservierung“ wechseln sollten.

### <a name="metrics"></a>Metriken

- [Erweiterte Funktionen von Azure Metrik-Explorer](platform/metrics-charts.md): Aggregationsabschnitt hinzugefügt.

### <a name="workbooks"></a>Arbeitsmappen

- [Programmgesteuertes Verwalten von Arbeitsmappen:](platform/workbooks-automate.md) Resource Manager-Vorlage zum Bereitstellen einer Arbeitsmappenvorlage hinzugefügt.

## <a name="march-2020"></a>März 2020

### <a name="general"></a>Allgemein

- [Azure Monitor – Übersicht](overview.md): Übersichtsvideo für Azure Monitor hinzugefügt.
- [Konfiguration kundenseitig verwalteter Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Allgemeine Aktualisierungen.
- [Azure Monitor-Datenreferenz](/azure/azure-monitor/reference/): Neue Website.

### <a name="alerts"></a>Alerts

- [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](platform/alerts-activity-log.md): Zusätzliche Erklärung der Resource Manager-Vorlage hinzugefügt.
- [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](platform/alerts-metric-overview.md): Mit Informationen zur Behördenunterstützung aktualisiert.
- [Problembehandlung bei Warnungen und Benachrichtigungen von Azure Monitor](platform/alerts-troubleshoot.md): Neuer Artikel.

### <a name="application-insights"></a>Application Insights

- [Verwalten von Application Insights-Ressourcen mithilfe von PowerShell](app/powershell.md): ARMClient-Beispiele hinzugefügt.
- [Exportieren von Telemetriedaten aus Application Insights](app/export-telemetry.md): Tabelle mit Details zur Exportstruktur hinzugefügt.
- [Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service](app/snapshot-debugger-appservice.md): Resource Manager-Vorlagenbeispiel hinzugefügt.
- [Verwalten der Nutzung und der Kosten für Application Insights](app/pricing.md): Informationen zur Warnung im Zusammenhang mit der Datenobergrenze hinzugefügt.
- [Einrichten von Azure Monitor für Ihre Python-Anwendung](app/opencensus-python.md): Standardmetriken hinzugefügt.
- [Unterstützung für Quellzuordnungen für JavaScript-Anwendungen](app/source-map-support.md): Neuer Artikel.

### <a name="containers"></a>Container

- [Häufig gestellte Fragen zu Azure Monitor](faq.md): Azure Monitor für Container aktualisiert.
- [Konfigurieren der GPU-Überwachung mit Azure Monitor für Container](insights/container-insights-gpu-monitoring.md): Neuer Artikel.

### <a name="insights"></a>Einblicke

- [Office 365-Verwaltungslösung in Azure (Vorschau)](insights/solution-office-365.md): Das Veraltungsdatum wurde aktualisiert.

### <a name="logs"></a>Protokolle

- [Optimieren von Protokollabfragen in Azure Monitor](log-query/query-optimization.md): CPU-Bedingung für die XML- und JSON-Analyse hinzugefügt.
- [Löschen und Wiederherstellen eines Azure Log Analytics-Arbeitsbereichs](platform/delete-workspace.md): Problembehandlung hinzugefügt.
- [Connector für Azure Monitor-Protokolle für Azure Logic Apps und Flow](platform/logicapp-flow-connector.md): Für neuen Azure Monitor-Connector aktualisiert.

### <a name="metrics"></a>Metriken

- [Veraltete Datenträgermetriken im Azure-Portal](platform/portal-disk-metrics-deprecation.md): Neuer Artikel.
- [Tutorial: Erstellen eines Metrikdiagramms in Azure Monitor](learn/tutorial-metrics-explorer.md): Video hinzugefügt.

### <a name="platform-logs"></a>Plattformprotokolle

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](./platform/activity-log.md): Überarbeitet, um die Erfassung des Aktivitätsprotokolls mit Diagnoseeinstellungen besser zu vermitteln.

### <a name="virtual-machines"></a>Virtuelle Computer

- [Überwachen von virtuellen Azure-Computern mit Azure Monitor](insights/monitor-vm-azure.md): Neuer Artikel.
- [Schnellstart: Überwachen eines virtuellen Azure-Computers mit Azure Monitor](learn/quick-monitor-azure-vm.md): Aktualisiert, um Azure Monitor für VMs hinzuzufügen.
- [Erstellen von Warnungen aus Azure Monitor für VMs](insights/vminsights-alerts.md): Neuer Artikel.
- [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md): Agent-Downloadlinks aktualisiert.

Allgemeine Aktualisierungen im Zusammenhang mit der allgemeinen Verfügbarkeit von Azure Monitor für VMs

- [Was ist Azure Monitor für VMs?](insights/vminsights-overview.md)
- [Häufig gestellte Fragen zu Azure Monitor für VMs in der allgemein verfügbaren Version (GA)](insights/vminsights-ga-release-faq.md) 
- [Aktivieren von Azure Monitor für VMs mit Azure Policy](./insights/vminsights-enable-policy.md) 
- [Darstellen der Leistung in Diagrammen mit Azure Monitor für VMs](insights/vminsights-performance.md)
- [Abfragen von Protokollen aus Azure Monitor für VMs](insights/vminsights-log-search.md)
- [Verwenden des Zuordnungsfeatures in Azure Monitor für VMs zum Analysieren von Anwendungskomponenten](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Visualisierungen

- [Visualisieren von Daten aus Azure Monitor](visualizations.md): Aktualisiert, um auf die geplante Einstellung des Ansicht-Designers hinzuweisen.

## <a name="february-2020"></a>Februar 2020

### <a name="agents"></a>Agents

Mehrere Updates im Rahmen der Überarbeitung des Inhalts der Diagnoseerweiterung.

- [Übersicht über Azure Monitor-Agents](platform/agents-overview.md): Die Tabellen wurden neu strukturiert, um die individuellen Features der einzelnen Agents deutlicher zu machen.
- [Übersicht zur Azure-Diagnoseerweiterung](platform/diagnostics-extension-overview.md): Vollständig überarbeitet.
- [Sammeln von Daten aus der Azure-Diagnoseerweiterung in Azure Monitor-Protokollen](platform/diagnostics-extension-logs.md): Zur Aktualisierung und Verdeutlichung allgemein überarbeitet.
- [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](platform/diagnostics-extension-windows-install.md): Neuer Artikel. 
- [Schema der Diagnoseerweiterung für Windows](platform/diagnostics-extension-schema-windows.md): Neu strukturiert.
- [Senden von Daten aus der Windows Azure-Diagnoseerweiterung an Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md): Vollständig überarbeitet und aktualisiert.
- [Speichern und Anzeigen von Diagnosedaten in Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) Vollständig überarbeitet und aktualisiert.
- [Log Analytics-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md): Verdeutlichung der Beziehung mit dem Log Analytics-Agent.
- [Log Analytics-VM-Erweiterung für Linux](../virtual-machines/extensions/oms-linux.md): Verdeutlichung der Beziehung mit dem Log Analytics-Agent.

### <a name="application-insights"></a>Application Insights

- [Verbindungszeichenfolgen](app/sdk-connection-string.md): Neuer Artikel.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor für Container

- [Integrieren von Azure Active Directory in Azure Kubernetes Service](../aks/azure-ad-integration-cli.md): Es wurde ein Hinweis für die Erstellung einer Clientanwendung zur Unterstützung eines RBAC-fähigen Kubernetes-Clusters für die Unterstützung von Azure Monitor für Container hinzugefügt.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs

- [Häufig gestellte Fragen zu Azure Monitor für VMs in der allgemein verfügbaren Version (GA)](insights/vminsights-ga-release-faq.md): Die Informationen zur Speicherung der Leistungsdaten wurden geändert.

#### <a name="office-365"></a>Office 365

- [Office 365-Verwaltungslösung in Azure (Vorschau)](insights/solution-office-365.md): Das Veraltungsdatum wurde aktualisiert.


### <a name="logs"></a>Protokolle

- [Optimieren von Protokollabfragen in Azure Monitor](log-query/query-optimization.md): Neuer Artikel.
- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md): Verbesserte Beispielabfragen zum besseren Verständnis Ihrer Nutzung.

### <a name="metrics"></a>Metriken

- [Über Diagnoseeinstellungen exportierbare Azure Monitor-Plattformmetriken](platform/metrics-supported-export-diagnostic-settings.md): Ein Abschnitt zum Ändern des Verhaltens für NULL-Werte und Nullwerte wurde hinzugefügt.

### <a name="visualizations"></a>Visualisierungen

Mehrere neue Artikel zur Umstellung vom Ansicht-Designer auf Arbeitsmappen.

- [Handbuch für den Übergang vom Azure Monitor-Ansicht-Designer zu Arbeitsmappen](platform/view-designer-conversion-overview.md): Neuer Artikel.
- [Umwandlung von Azure Monitor-Ansichts-Designer in Arbeitsmappen – Optionen](platform/view-designer-conversion-options.md): Neuer Artikel.
- [Umwandlung von Azure Monitor-Ansichts-Designer-Kacheln](platform/view-designer-conversion-tiles.md): Neuer Artikel.
- [Umwandlung von Azure Monitor-Ansichts-Designer in Arbeitsmappen – Zusammenfassung und Zugriff](platform/view-designer-conversion-access.md): Neuer Artikel.
- [Umwandlung von Ansichts-Designer in Arbeitsmappen – häufige Aufgaben](platform/view-designer-conversion-tasks.md): Neuer Artikel.
- [Beispiele für die Ansicht-Designer-Umwandlung](platform/view-designer-conversion-examples.md): Neuer Artikel.

## <a name="january-2020"></a>Januar 2020

### <a name="general"></a>Allgemein

- [Was wird von Azure Monitor überwacht?](monitor-reference.md): Neuer Artikel.

### <a name="agents"></a>Agents

- [Sammeln von Protokolldaten mit dem Log Analytics-Agent](platform/log-analytics-agent.md): Die Tabelle mit den Netzwerkfirewallanforderungen wurde aktualisiert.

### <a name="alerts"></a>Alerts

- [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md): Eine nicht mehr benötigte Einstellung für Funktionen der Version 2 wurde entfernt.
- [Erstellen einer Metrikwarnung anhand einer Resource Manager-Vorlage](platform/alerts-metric-create-templates.md): Ein Beispiel für den Parameter *ignoreDataBefore* wurde hinzugefügt.  Einschränkungen für Regeln mit mehreren Kriterien wurden hinzugefügt.
- [Erstellen und Verwalten von Warnungsregeln in Log Analytics mithilfe der REST-API](platform/api-alerts.md): Das JSON-Beispiel wurde korrigiert.

### <a name="application-insights"></a>Application Insights

- [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md): Dem Abschnitt „Verfügbarkeitstests“ wurde eine Anleitung zum Hinzufügen einer Eingangsportregel hinzugefügt, um Datenverkehr von Application Insights-Verfügbarkeitstests zuzulassen.
- [Behandeln von Problemen mit dem Aktivieren oder Anzeigen von Application Insights Profiler](app/profiler-troubleshooting.md): Die allgemeine Problembehandlung wurde aktualisiert.
- [Erstellen von Stichproben in Application Insights](app/sampling.md): Der Artikel wurde auf der Grundlage von Kundenfeedback aktualisiert und neu strukturiert, um die Lesbarkeit zu verbessern.

### <a name="data-security"></a>Datensicherheit

- [Konfiguration kundenseitig verwalteter Schlüssel in Azure Monitor](platform/customer-managed-keys.md): Neuer Artikel.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor für Container

- [Konfigurieren der Datensammlung des Azure Monitor für Container-Agents](insights/container-insights-agent-config.md): In diesem Artikel wurden Details zum Upgraden des Agents auf Azure Red Hat OpenShift sowie zusätzliche Informationen zur Unterscheidung der Methoden zum Upgraden des Agents hinzugefügt.
- [Einrichten von Warnungen für Leistungsprobleme in Azure Monitor für Container](./insights/container-insights-log-alerts.md): In diesem Artikel wurden Informationen überarbeitet und die Schritte aktualisiert, mit denen unter Verwendung von Warnungen für den Arbeitsbereichskontext eine Warnung für im Arbeitsbereich gespeicherte Leistungsdaten erstellt wird.
- [Überwachen der Leistung von Kubernetes-Clustern mit Azure Monitor für Container](insights/container-insights-analyze.md): In diesem Artikel wurde sowohl die Übersicht als auch der Analyseabschnitt im Hinblick auf die Unterstützung von Windows-Kubernetes-Clustern aktualisiert.
- [Konfigurieren von Azure Red Hat OpenShift-Clustern mit Azure Monitor für Container](insights/container-insights-azure-redhat-setup.md): In diesem Artikel wurden Details zum Upgraden des Agents auf Azure Red Hat OpenShift sowie zusätzliche Informationen zur Unterscheidung der Methoden zum Upgraden des Agents hinzugefügt.
- [Konfigurieren von Kubernetes-Hybridclustern mit Azure Monitor für Container](insights/container-insights-hybrid-setup.md): Dieser Artikel wurde mit der zusätzlichen Unterstützung von „secure port: 10250“ für cAdvisor von Kubelet aktualisiert.
- [Verwalten des Agents für Azure Monitor für Container](insights/container-insights-manage-agent.md): In diesem Artikel wurden Details im Zusammenhang mit dem Verhalten und der Konfiguration der Metrikerfassung mit Azure Red Hat OpenShift im Vergleich zu anderen Arten von Kubernetes-Clustern aktualisiert.
- [Konfigurieren des Abrufs von Prometheus-Metriken mit Azure Monitor für Container](insights/container-insights-prometheus-integration.md): In diesem Artikel wurden Details im Zusammenhang mit dem Verhalten und der Konfiguration der Metrikerfassung mit Azure Red Hat OpenShift im Vergleich zu anderen Arten von Kubernetes-Clustern aktualisiert.
- [Gewusst wie: Aktualisieren von Azure Monitor für Container zum Aktivieren von Metriken](insights/container-insights-update-metrics.md): In diesem Artikel wurden Details im Zusammenhang mit dem Verhalten und der Konfiguration der Metrikerfassung mit Azure Red Hat OpenShift im Vergleich zu anderen Arten von Kubernetes-Clustern aktualisiert.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs

- [Häufig gestellte Fragen zu Azure Monitor für VMs in der allgemein verfügbaren Version (GA)](insights/vminsights-ga-release-faq.md): In diesem Artikel wurden Informationen zum Upgraden des Arbeitsbereichs und der Agents auf die neue Version hinzugefügt.

#### <a name="office-365"></a>Office 365

- [Office 365-Verwaltungslösung in Azure (Vorschau)](insights/solution-office-365.md): In diesem Artikel wurden Details und häufig gestellte Fragen im Zusammenhang mit der Migration zur Office 365-Lösung in Azure Sentinel hinzugefügt. Der Onboardingabschnitt wurde entfernt.

### <a name="logs"></a>Protokolle

- [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](platform/manage-access.md): In diesem Artikel wurden die Informationen zu „NotActions“ aktualisiert.
- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md): In diesem Artikel wurde die Erläuterung der Berechnung des Datenvolumens im Abschnitt „Preismodell“ verbessert.
- [Verwalten von Log Analytics-Arbeitsbereichen mithilfe von Azure Resource Manager-Vorlagen](./samples/resource-manager-workspace.md): In diesem Artikel wurde die Vorlage mit neuen Tarifen aktualisiert.

### <a name="platform-logs"></a>Plattformprotokolle

- [Aktualisierung für das Erfassen und Exportieren des Azure-Aktivitätsprotokolls](./platform/activity-log.md): In diesem Artikel wurden weitere Informationen zu geänderten Eigenschaften hinzugefügt.
- [Exportieren des Aktivitätsprotokolls in den Speicher oder in Azure Event Hubs](./platform/activity-log.md#legacy-collection-methods): Dieser Artikel wurde mit Benutzeroberflächenänderungen aktualisiert. 

## <a name="december-2019"></a>Dezember 2019

### <a name="agents"></a>Agents

- [Verbinden von Linux-Computern mit Azure Monitor](platform/agent-linux.md): Neuer Artikel.

### <a name="alerts"></a>Alerts

- [Erstellen einer Metrikwarnung anhand einer Resource Manager-Vorlage](platform/alerts-metric-create-templates.md): Diesem Artikel wurde ein Beispiel eine benutzerdefinierte Metrik hinzugefügt.
- [Metrikwarnungen mit dynamischen Schwellenwerten in Azure Monitor](platform/alerts-dynamic-thresholds.md): Diesem Artikel wurde ein Abschnitt zum Interpretieren dynamischer Schwellenwertdiagramme hinzugefügt.
- [Überblick über Warnungen in Microsoft Azure](platform/alerts-overview.md): In diesem Artikel wurde die Resource Graph-Abfrage aktualisiert.
- [Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor](platform/alerts-metric-near-real-time.md): In diesem Artikel wurden die unterstützten Metriken und Dimensionen aktualisiert.
- [Wechseln der API-Einstellung für Protokollwarnungen](platform/alerts-log-api-switch.md): Diesem Artikel wurde ein Hinweis zum geänderten Warnungsnamen hinzugefügt.
- [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](platform/alerts-metric-overview.md): Diesem Artikel wurden unterstützte Ressourcentypen für die bedarfsorientierte Überwachung hinzugefügt.

### <a name="application-insights"></a>Application Insights

- [Application Insights für Workerdienstanwendungen (Anwendungen ohne HTTP)](app/worker-service.md): In diesem Artikel wurde dem C#-Code ein Standardprotokolliergrad hinzugefügt. Außerdem wurde die Paketreferenzversion aktualisiert.
- [Konfigurieren des Application Insights-SDK mit "ApplicationInsights.config" oder XML](app/configuration-with-applicationinsights-config.md): In diesem Artikel wurde der Beispielcode aktualisiert.
- [Verwalten von Application Insights-Ressourcen mithilfe von PowerShell](app/powershell.md): In diesem Artikel wurde die Resource Manager-Vorlage aktualisiert.
- [Erstellen einer Application Insights-Ressource](app/create-new-resource.md): Diesem Artikel wurde ein Hinweis zur Verwendung eines global eindeutigen Namens hinzugefügt.
- [Live Metrics Stream: Überwachung und Diagnose mit einer Latenzzeit von 1 Sekunde](app/live-stream.md): In diesem Artikel wurde die Versionsanforderung für das ASP.NET Core SDK aktualisiert.
- [Einführung in EventCounters](app/eventcounters.md): In diesem Artikel wurden Kategorie und Tabelle auf „customMetrics“ aktualisiert.
- [Untersuchen von Java-Ablaufverfolgungsprotokollen in Application Insights](app/java-trace-logs.md): Diesem Artikel wurde eine Konfiguration für den Protokollierungsschwellenwert des Java-Agents hinzugefügt.
- [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md): In diesem Artikel wurden die IP-Adressen für Live Metrics Stream aktualisiert.
- [Überwachen der Leistung von Azure App Service](app/azure-web-apps.md): In diesem Artikel wurde die Unterstützung von ASP.NET Core 3.0 hinzugefügt. 
- [Einrichten von Azure Monitor für Ihre Python-Anwendung (Vorschau)](app/opencensus-python.md): In diesem Artikel wurde dem Azure Monitor-Schema eine Verdeutlichung der OpenCensus-Python-Schemazuordnung hinzugefügt.
- [SDK-Versionshinweise – Application Insights](app/release-notes.md): Diesem Artikel wurden Hinweise für ältere Releases hinzugefügt.
- [Telemetriekanäle in Application Insights](app/telemetry-channels.md): In diesem Artikel wurde die Dauer für verworfene Daten bei einem längeren Verbindungsausfall aktualisiert.
- [Erstellen von Stichproben in Application Insights](app/sampling.md): In diesem Artikel wurde der Codeausschnitt für das benutzerdefinierte TelemetryInitializer-Element korrigiert.
- [Anleitung zur Problembehandlung sowie Fragen und Antworten zu Application Insights für Java](app/java-troubleshoot.md): In diesem Artikel wurde die Aussage entfernt, dass die Abhängigkeitssammlung in JDK 9 nicht unterstützt wird.

### <a name="insights-and-solutions"></a>Erkenntnisse und Lösungen

- [Häufig gestellte Fragen zu Azure Monitor](./faq.md): Diesem Artikel wurde eine Frage zu den Feldern „Image“ und „Name“ hinzugefügt.
- [Überwachen von Azure SQL-Datenbank mithilfe von Azure SQL-Analyse (Vorschauversion)](insights/azure-sql.md): In diesem Artikel wurde die Unterstützung für die verwaltete Instanz für Datenbankwartevorgänge aktualisiert.
- [Konfigurieren der Datensammlung des Azure Monitor für Container-Agents](insights/container-insights-agent-config.md): Diesem Artikel wurde eine Einstellung für „enrich_container_logs“ hinzugefügt.
- [Konfigurieren von Kubernetes-Hybridclustern mit Azure Monitor für Container](insights/container-insights-hybrid-setup.md): Diesem Artikel wurde ein Problembehandlungsabschnitt hinzugefügt.
- [Überwachen des Active Directory-Replikationsstatus mit Azure Monitor](insights/ad-replication-status.md): In diesem Artikel wurde die .NET Framework-Voraussetzung aktualisiert.
- [Netzwerkleistungsmonitor-Lösung in Azure](insights/network-performance-monitor.md): Diesem Artikel wurden unterstützte Regionen hinzugefügt.
- [Optimieren Ihrer Active Directory-Umgebung mit der Active Directory-Integritätsüberprüfung-Lösung in Azure Monitor](insights/ad-assessment.md): In diesem Artikel wurde die .NET Framework-Voraussetzung aktualisiert.
- [Optimieren der SQL-Umgebung mit der SQL Server-Integritätsüberprüfung-Lösung in Azure Monitor](insights/sql-assessment.md): In diesem Artikel wurde die .NET Framework-Voraussetzung aktualisiert.
- [Optimieren Ihrer Umgebung mit der Lösung zur System Center Operations Manager-Integritätsüberprüfung (Vorschauversion)](insights/scom-assessment.md): In diesem Artikel wurde die .NET Framework-Voraussetzung aktualisiert.
- [Verbinden von ITSM-Produkten/-Diensten mit dem ITSM-Connector](platform/itsmc-connections.md): In diesem Artikel wurde der Voraussetzung im Zusammenhang mit Client-ID und Clientgeheimnis New York hinzugefügt.

### <a name="logs"></a>Protokolle

- [Löschen und Wiederherstellen eines Azure Log Analytics-Arbeitsbereichs](platform/delete-workspace.md): Diesem Artikel wurde die PowerShell-Methode hinzugefügt.
- [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](platform/design-logs-deployment.md): In diesem Artikel wurde die Erfassungsrate für einen Arbeitsbereich erhöht.

### <a name="metrics"></a>Metriken

- [Über Diagnoseeinstellungen exportierbare Azure Monitor-Plattformmetriken](platform/metrics-supported-export-diagnostic-settings.md): Neuer Artikel.

### <a name="platform-logs"></a>Plattformprotokolle

Im Zuge der Neustrukturierung von Inhalten für Plattformprotokolle wurden mehrere Artikel aktualisiert. Grundlage dieser Aktualisierung ist ein neues Feature zum Konfigurieren des Aktivitätsprotokolls mit Diagnoseeinstellungen.

- [Archivieren von Azure-Ressourcenprotokollen in einem Speicherkonto](./platform/resource-logs.md#send-to-azure-storage)
- [Ereignisschema des Azure-Aktivitätsprotokolls](platform/activity-log-schema.md)
- [Azure Monitor-Diensteinschränkungen](service-limits.md)
- [Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](./platform/activity-log.md)
- [Aktualisierung für das Erfassen und Exportieren des Azure-Aktivitätsprotokolls](./platform/activity-log.md)
- [Erfassen von Azure-Aktivitätsprotokollen für Azure Active Directory-Mandanten (Legacy) in Azure Monitor](./platform/activity-log.md)
- [Erfassen von Protokollen der Azure-Plattform im Log Analytics-Arbeitsbereich in Azure Monitor](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Erstellen von Diagnoseeinstellungen in Azure mithilfe einer Resource Manager-Vorlage](./samples/resource-manager-diagnostic-settings.md)
- [Erstellen einer Diagnoseeinstellung zum Erfassen von Plattformprotokollen und Metriken in Azure](platform/diagnostic-settings.md)
- [Exportieren des Aktivitätsprotokolls in den Speicher oder in Azure Event Hubs](./platform/activity-log.md#legacy-collection-methods)
- [Übersicht über Protokolle der Azure-Plattform](platform/platform-logs-overview.md)
- [Streamen von Azure-Überwachungsdaten an einen Event Hub](platform/stream-monitoring-data-event-hubs.md)
- [Streamen von Protokollen der Azure-Plattform an Azure Event Hubs](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Schnellstartanleitungen und Tutorials

- [Tutorial: Erstellen eines Metrikdiagramms in Azure Monitor](learn/tutorial-metrics-explorer.md): Neuer Artikel.
- [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](learn/tutorial-resource-logs.md): Neuer Artikel.
- [Schnellstart: Überwachen einer Azure-Ressource mit Azure Monitor](learn/quick-monitor-azure-resource.md): Neuer Artikel.
   
## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Mitwirkung an der Azure Monitor-Dokumentation finden Sie bei Interesse im [Leitfaden für Mitwirkende an der Microsoft-Dokumentation](/contribute/).