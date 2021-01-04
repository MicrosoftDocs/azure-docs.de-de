---
title: Was wird von Azure Monitor überwacht?
description: Eine Referenz aller Dienste und anderer Ressourcen, die von Azure Monitor überwacht werden.
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 08d30fb72398c4b43422eb21f132d5fddd5502b7
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853141"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Was wird von Azure Monitor überwacht?
In diesem Artikel werden die verschiedenen Anwendungen und Dienste beschrieben, die von Azure Monitor überwacht werden. 

## <a name="insights-and-core-solutions"></a>Erkenntnisse und wichtige Lösungen
Wichtige Erkenntnisse und Lösungen werden als Teil von Azure Monitor betrachtet und folgen den Vereinbarungen zum Support und Servicelevel für Azure. Sie werden in allen Azure-Regionen unterstützt, in denen Azure Monitor verfügbar ist.

### <a name="insights"></a>Einblicke

Mit Erkenntnissen wird eine angepasste Überwachungsoberfläche für bestimmte Anwendungen und Dienste bereitgestellt. Dabei werden sowohl Protokolle als auch Metriken erfasst und analysiert.

| Erkenntnis | BESCHREIBUNG |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Erweiterbarer APM-Dienst (Application Performance Management) zum Überwachen Ihrer Live-Webanwendung auf einer beliebigen Plattform. |
| [Azure Monitor für Container](insights/container-insights-overview.md) | Überwacht die Leistung von Containerworkloads, die entweder in Azure Container Instances oder in Managed Kubernetes-Clustern bereitgestellt sind, die im Azure Kubernetes Service (AKS) gehostet werden. |
| [Azure Monitor für Cosmos DB](insights/cosmosdb-insights-overview.md) | Bietet eine Übersicht über Gesamtleistung, Fehler, Kapazität und Betriebsintegrität aller Ihrer Azure Cosmos DB-Ressourcen auf einer vereinheitlichten interaktiven Oberfläche. |
| [Azure Monitor für Netzwerke (Vorschauversion)](insights/network-insights-overview.md) | Bietet einen umfassenden Überblick über Integrität und Metriken Ihrer gesamten Netzwerkressource. Die erweiterte Suchfunktion unterstützt Sie beim Identifizieren von Ressourcenabhängigkeiten und ermöglicht Szenarien wie das Identifizieren von Ressourcen, die Ihre Website gehostet werden, indem einfach nach dem Namen Ihrer Website gesucht wird. |
[Azure Monitor für Ressourcengruppen (Vorschauversion)](insights/resource-group-insights.md) |  Selektieren und Diagnostizieren von Problemen Ihrer individuellen Ressourcen und Bereitstellen eines Kontexts für die Integrität und Leistung der Ressourcengruppe als Ganzes. |
| [Überwachen Ihres Speicherdiensts mit Azure Monitor für Storage (Vorschauversion)](insights/storage-insights-overview.md) | Ermöglicht eine umfassende Überwachung Ihrer Azure Storage-Konten, indem eine einheitliche Ansicht der Leistung, Kapazität und Verfügbarkeit Ihrer Azure Storage-Dienste bereitgestellt wird. |
| [Azure Monitor für VMs](insights/vminsights-overview.md) | Überwacht Ihre virtuellen Azure-Computer (VM) und VM-Skalierungsgruppen bedarfsorientiert. Der Dienst analysiert die Leistung und Integrität Ihrer Windows- und Linux-VMs und überwacht deren Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen. |
| [Überwachen Ihres Schlüsseltresordiensts mit Azure Monitor für Key Vault (Vorschauversion)](./insights/key-vault-insights-overview.md) | Ermöglicht eine umfassende Überwachung Ihrer Schlüsseltresore. Zu diesem Zweck wird eine einheitliche Darstellung der Anforderungen, Leistung, Fehler und Latenz im Zusammenhang mit Key Vault bereitgestellt. |
| [Erkunden von Azure Monitor für Azure Cache for Redis (Vorschauversion)](insights/redis-cache-insights-overview.md) |  Bietet eine einheitliche, interaktive Ansicht der Leistung, Fehler, Kapazität und Betriebsintegrität insgesamt. |


### <a name="core-solutions"></a>Wichtige Lösungen

Lösungen basieren auf Protokollabfragen und Ansichten, die für eine bestimmte Anwendung oder einen bestimmten Dienst angepasst sind. Dabei werden nur Protokolle erfasst und analysiert, und sie werden im Laufe der Zeit zugunsten von Erkenntnissen eingestellt.

| Lösung | BESCHREIBUNG |
|:---|:---|
| [Agent-Integrität](insights/solution-agenthealth.md) | Analysieren der Integrität und Konfiguration von Log Analytics-Agents. |
| [Benachrichtigungsverwaltung](platform/alert-management-solution.md) | Analysieren von Warnungen, die von System Center Operations Manager, Nagios oder Zabbix erfasst werden. |
| [Dienstzuordnung](insights/service-map.md) | Automatisches Ermitteln von Anwendungskomponenten auf Windows- und Linux-Systemen und Darstellung der Kommunikation zwischen Diensten. |



## <a name="azure-services"></a>Azure-Dienste
In der folgenden Tabelle sind Azure-Dienste und die damit in Azure Monitor erfassten Daten aufgeführt. 

- Metriken: Der Dienst erfasst automatisch Metriken in Azure Monitor-Metriken. 
- Protokolle: Der Dienst unterstützt Diagnoseeinstellungen, mit denen Plattformprotokolle und -metriken in Azure Monitor-Protokollen erfasst werden können.
- Erkenntnis: Es ist eine Erkenntnis für den Dienst verfügbar, die eine angepasste Überwachungsoberfläche für den Dienst bereitstellt.

| Dienst | Metriken | Protokolle | Erkenntnis | Notizen |
|:---|:---|:---|:---|:---|
|Active Directory | Nein | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nein | Nein | Nein |  |
|Active Directory Domain Services | Nein | Ja | Nein |  |
|Aktivitätsprotokoll | Nein | Ja | Nein | |
|Erweiterter Schutz vor Bedrohungen | Nein | Nein | Nein |  |
|Advisor | Nein | Nein | Nein |  |
|AI Builder | Nein | Nein | Nein |  |
|Analysis Services | Ja | Ja | Nein |  |
|API for FHIR | Nein | Nein | Nein |  |
|API Management | Ja | Ja | Nein |  |
|App Service | Ja | Ja | Nein |  |
|AppConfig | Nein | Nein | Nein |  |
|Application Gateway | Ja | Ja | Nein |  |
|Nachweisdienst | Nein | Nein | Nein |  |
|Automation | Ja | Ja | Nein |  |
|Azure Service Manager (RDFE) | Nein | Nein | Nein |  |
|Backup | Nein | Ja | Nein |  |
|Bastion | Nein | Nein | Nein |  |
|Batch | Ja | Ja | Nein |  |
|Batch KI | Nein | Nein | Nein |  |
|Blockchain-Dienst | Nein | Ja | Nein |  |
|Blaupausen | Nein | Nein | Nein |  |
|Botdienst | Nein | Nein | Nein |  |
|Cloud Services | Ja | Ja | Nein | Agent zum Überwachen von Gastbetriebssystem und Workflows erforderlich.  |
|Cloud Shell | Nein | Nein | Nein |  |
|Cognitive Services | Ja | Ja | Nein |  |
|Container Instances | Ja | Nein | Nein |  |
|Containerregistrierung | Ja | Ja | Nein |  |
|Content Delivery Network (CDN) | Nein | Ja | Nein |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nein | Nein | Nein |  |
|Data Box | Nein | Nein | Nein |  |
|Data Catalog Gen2 | Nein | Nein | Nein |  |
|Data Explorer | Ja | Ja | Nein |  |
|Data Factory | Ja | Ja | Nein |  |
|Data Factory v2 | Nein | Ja | Nein |  |
|Datenfreigabe | Nein | Nein | Nein |  |
|Database for MariaDB | Ja | Ja | Nein |  |
|Database for MySQL | Ja | Ja | Nein |  |
|Database for PostgreSQL | Ja | Ja | Nein |  |
|Database Migration Service | Nein | Nein | Nein |  |
|Databricks | Nein | Ja | Nein |  |
|DDoS Protection | Ja | Ja | Nein |  |
|DevOps | Nein | Nein | Nein |  |
|DNS | Ja | Nein | Nein |  |
|Domänennamen | Nein | Nein | Nein |  |
|DPS | Nein | Nein | Nein |  |
|Dynamics 365 Customer Engagement | Nein | Nein | Nein |  |
|Dynamics 365 Finance and Operations | Nein | Nein | Nein |  |
|Event Grid | Ja | Nein | Nein |  |
|Event Hubs | Ja | Ja | Nein |  |
|ExpressRoute | Ja | Ja | Nein |  |
|Firewall | Ja | Ja | Nein |  |
|Front Door | Ja | Ja | Nein |  |
|Functions | Ja | Ja | Nein |  |
|HDInsight | Nein | Ja | Nein |  |
|HPC Cache | Nein | Nein | Nein |  |
|Information Protection | Nein | Ja | Nein |  |
|Intune | Nein | Ja | Nein |  |
|IoT Central | Nein | Nein | Nein |  |
|IoT Hub | Ja | Ja | Nein |  |
|Key Vault | Ja | Ja | [Ja](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nein | Nein | [Ja](insights/container-insights-overview.md)  |  |
|Load Balancer | Ja | Nein | Nein |  |
|Logic Apps | Ja | Ja | Nein |  |
|Machine Learning Service | Nein | Nein | Nein |  |
|Verwaltete Anwendungen  | Nein | Nein | Nein |  |
|Karten  | Nein | Nein | Nein |  |
|Media Services | Ja | Ja | Nein |  |
|Microsoft Managed Desktop | Nein | Nein | Nein |  |
|Microsoft PowerApps | Nein | Nein | Nein |  |
|Microsoft Social Engagement | Nein | Nein | Nein |  |
|Microsoft Stream | Ja | Ja | Nein |  |
|Migrieren | Nein | Nein | Nein |  |
|Multi-Factor Authentication | Nein | Ja | Nein |  |
|Network Watcher | Ja | Ja | Nein |  |
|Notification Hubs | Ja | Nein | Nein |  |
|Open Datasets | Nein | Nein | Nein |  |
|Richtlinie | Nein | Nein | Nein |  |
|Power Automate | Nein | Nein | Nein |  |
|Power BI Embedded | Ja | Ja | Nein |  |
|Private Link | Nein | Nein | Nein |  |
|Project Spool Communication Platform | Nein | Nein | Nein |  |
|Red Hat OpenShift | Nein | Nein | Nein |  |
|Redis Cache | Ja | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Ressourcendiagramm | Nein | Nein | Nein |  |
|Ressourcen-Manager | Nein | Nein | Nein |  |
|Retail Search – by Bing | Nein | Nein | Nein |  |
|Suchen, | Ja | Ja | Nein |  |
|Service Bus | Ja | Ja | Nein |  |
|Service Fabric | Nein | Ja | Nein | Agent zum Überwachen von Gastbetriebssystem und Workflows erforderlich.  |
|Signup Portal | Nein | Nein | Nein |  |
|Site Recovery | Nein | Ja | Nein |  |
|Spring Cloud Service | Nein | Nein | Nein |  |
|Azure Synapse Analytics | Ja | Ja | Nein |  |
|SQL-Datenbank | Ja | Ja | Nein |  |
|SQL Server Stretch Database | Ja | Ja | Nein |  |
|Stapel | Nein | Nein | Nein |  |
|Storage | Ja | Nein | [Ja](insights/storage-insights-overview.md) |  |
|Storage Cache | Nein | Nein | Nein |  |
|Speichersynchronisierungsdienste | Nein | Nein | Nein |  |
|Stream Analytics | Ja | Ja | Nein |  |
|Time Series Insights | Ja | Ja | Nein |  |
|TINA | Nein | Nein | Nein |  |
|Traffic Manager | Ja | Ja | Nein |  |
|Universal Print | Nein | Nein | Nein |  |
|Virtual Machine Scale Sets | Nein | Ja | [Ja](insights/vminsights-overview.md) | Agent zum Überwachen von Gastbetriebssystem und Workflows erforderlich. |
|Virtual Machines | Ja | Ja | [Ja](insights/vminsights-overview.md) | Agent zum Überwachen von Gastbetriebssystem und Workflows erforderlich. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network – NSG-Datenflussprotokolle | Nein | Ja | Nein |  |
|VPN Gateway | Ja | Ja | Nein |  |
|Windows Virtual Desktop | Nein | Nein | Nein |  |

## <a name="virtual-machine-agents"></a>Agents für virtuelle Computer
In der folgenden Tabelle sind die Agents aufgeführt, die Daten vom Gastbetriebssystem virtueller Computer sammeln und an Azure Monitor senden können. Jeder Agent kann verschiedene Daten sammeln und an „Metriken“ oder „Protokolle“ in Azure Monitor senden. 

Ausführliche Informationen zu den Daten, die von den einzelnen Agents gesammelt werden können, finden Sie unter [Übersicht über Azure Monitor-Agents](platform/agents-overview.md).

| Agent |  Metriken | Protokolle |
|:---|:---|:---|:---|
| [Azure Monitor-Agent (Vorschau)](platform/azure-monitor-agent-overview.md) | Ja | Ja |
| [Log Analytics-Agent](platform/log-analytics-agent.md) | Nein | Ja|
| [Diagnoseerweiterung](platform/diagnostics-extension-overview.md) | Ja | Nein |
| [Telegraf-Agent](platform/collect-custom-metrics-linux-telegraf.md) | Ja | Nein |
| [Dependency-Agent](insights/vminsights-enable-overview.md) | Nein | Ja |


## <a name="product-integrations"></a>Produktintegrationen
Die in der folgenden Tabelle aufgeführten Dienste und Lösungen speichern ihre Daten in einem Log Analytics-Arbeitsbereich, sodass sie mit anderen von Azure Monitor gesammelten Protokolldaten analysiert werden können.

| Produkt/Dienst | BESCHREIBUNG |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Verwalten von Betriebssystemupdates und Nachverfolgen von Änderungen auf Windows- und Linux-Computern. Informationen finden Sie unter [Änderungsnachverfolgung](../automation/change-tracking/overview.md) und [Updateverwaltung](../automation/update-management/overview.md). |
| [Azure Information Protection](/azure/information-protection/) | Klassifizieren und optionales Schützen von Dokumenten und E-Mails. Informationen finden Sie unter [Zentrale Berichterstellung für Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Sammeln und Analysieren von Sicherheitsereignissen und Durchführen einer Bedrohungsanalyse. Informationen finden Sie unter [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md). |
| [Azure Sentinel](../sentinel/index.yml) | Stellt eine Verbindung mit verschiedenen Quellen her, einschließlich Office 365 und Amazon Web Services CloudTrail. Lesen Sie dazu [Herstellen einer Verbindung mit Datenquellen](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Erstellen einer Diagnoseeinstellung zum Senden von Protokollen an Azure Monitor. Informationen finden Sie unter [Senden von Daten an den Speicher, an Event Hubs oder Log Analytics in Intune (Vorschauversion)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Netzwerk  | [Netzwerkleistungsmonitor](insights/network-performance-monitor.md): Überwachen der Netzwerkkonnektivität und -leistung für Dienst- und Anwendungsendpunkte.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics): Analysieren der Protokolle und Metriken von Azure Application Gateway.<br>[Traffic Analytics](../network-watcher/traffic-analytics.md): Analysiert Flussprotokolle von Network Watcher für Netzwerksicherheitsgruppen (NSGs), um Einblicke in den Datenfluss in Ihrer Azure-Cloud zu ermöglichen. |
| [Office 365](insights/solution-office-365.md) | Überwachen Ihrer Office 365-Umgebungen. Aktualisierte Version mit verbessertem Onboarding über Azure Sentinel verfügbar. |
| [Azure SQL-Analyse](insights/azure-sql.md) | Bedarfsorientiertes und abonnementübergreifendes Überwachen der Leistung von Azure SQL-Datenbanken und verwalteten SQL-Instanzen. |
| [Surface Hub](insights/surface-hubs.md) | Nachverfolgen der Integrität und Nutzung von Surface Hub-Geräten. |
| [System Center Operations Manager](/system-center/scom) | Erfassen der Daten von Operations Manager-Agents durch Herstellen einer Verbindung zwischen der jeweiligen Verwaltungsgruppe und Azure Monitor. Informationen finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](platform/om-agents.md).<br> Bewerten des Risikos und der Integrität Ihrer System Center Operations Manager-Verwaltungsgruppe mithilfe der Lösung zur [Operations Manager-Bewertung](insights/scom-assessment.md). |
| [Microsoft Teams-Räume](/microsoftteams/room-systems/azure-monitor-deploy) | Integrierte End-to-End-Verwaltung von Geräten für Microsoft Teams-Räume. |
| [Visual Studio App Center](/appcenter/) | Erstellen, Testen und Verteilen von Anwendungen und anschließendes Überwachen von Status und Nutzung. Informationen finden Sie unter [Analysieren Ihrer mobilen Apps mit App Center und Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update Compliance](/windows/deployment/update/update-compliance-get-started): Bewerten Ihrer Windows Desktop-Upgrades.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview): Ist in Configuration Manager integriert und bietet Einblicke und Informationen, mit denen fundiertere Entscheidungen zur Updatebereitschaft Ihres Windows-Clients getroffen werden können. |



## <a name="other-solutions"></a>Andere Lösungen
Für die Überwachung verschiedener Anwendungen und Dienste stehen andere Lösungen zur Verfügung, aber die aktive Entwicklung wurde eingestellt und sie sind möglicherweise nicht in allen Regionen verfügbar. Diese fallen unter die Vereinbarung zum Servicelevel für die Azure Log Analytics-Datenerfassung.

| Lösung | BESCHREIBUNG |
|:---|:---|
| [Active Directory-Integritätsprüfung](insights/ad-assessment.md) | Bewerten des Risikos und der Integrität Ihrer Active Directory-Umgebungen. |
| [Active Directory-Replikationsstatusmonitor](insights/ad-replication-status.md) | Regelmäßige Überwachung Ihrer Active Directory-Umgebung auf Replikationsfehler. |
| [Aktivitätsprotokollanalyse](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Anzeigen von Aktivitätsprotokolleinträgen |
| [DNS-Analyse (Vorschauversion)](insights/dns-analytics.md) | Erfasst, analysiert und korreliert Windows-DNS-Analyseprotokolle und -Überwachungsprotokolle sowie andere zugehörige Daten von Ihren DNS-Servern. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Sammeln, Anzeigen und Analysieren von Integritäts- und Leistungsmetriken des Cloud Foundry-Systems über mehrere Bereitstellungen hinweg. |
| [Container](insights/containers.md) | Anzeigen und Verwalten von Docker- und Windows-Containerhosts. |
| [Bedarfsgesteuerte Bewertungen](/services-hub/health/getting_started_with_on_demand_assessments) | Bewerten und Optimieren der Verfügbarkeit, Sicherheit und Leistung Ihrer lokalen, hybriden und cloudbasierten Umgebungen mit Microsoft-Technologie. |
| [SQL-Integritätsprüfung](insights/sql-assessment.md) | Bewerten des Risikos und der Integrität Ihrer SQL Server-Umgebungen.  |
| [Wire Data](insights/wire-data.md) | Konsolidierte Netzwerk- und Leistungsdaten, die von mit Windows verbundenen und mit Linux verbundenen Computern mit dem Log Analytics-Agent gesammelt werden. |

## <a name="third-party-integration"></a>Integration von Drittanbieterprodukten

| Lösung | BESCHREIBUNG |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Der ITSM-Connector ermöglicht Ihnen, Azure und ein unterstütztes ITSM-Produkt bzw. einen unterstützten ITSM-Dienst zu verbinden.  |


## <a name="resources-outside-of-azure"></a>Ressourcen außerhalb von Azure
Mithilfe der in der folgenden Tabelle aufgeführten Methoden kann Azure Monitor Daten von Ressourcen außerhalb von Azure sammeln.

| Resource | Methode |
|:---|:---|
| Anwendungen | Überwachen von Webanwendungen außerhalb von Azure mithilfe von Application Insights. Informationen finden Sie unter [Was ist Application Insights?](./app/app-insights-overview.md) |
| Virtuelle Computer | Verwenden von Agents zum Sammeln von Daten aus dem Gastbetriebssystem virtueller Computer in anderen Cloudumgebungen oder lokal. Siehe [Übersicht über Azure Monitor-Agents](platform/agents-overview.md) |
| REST-API-Client | Zum Schreiben von Daten in Azure Monitor-Protokolle und -Metriken von jedem Rest-API-Client aus stehen separate APIs zur Verfügung. Weitere Informationen finden Sie unter [Senden von Protokolldaten an Azure Monitor mit der HTTP-Datensammler-API](platform/data-collector-api.md) (für Protokolle) und [Senden benutzerdefinierter Metriken für eine Azure-Ressource an den Azure Monitor-Metrikspeicher mithilfe einer REST-API](platform/metrics-store-custom-rest-api.md) (für Metriken). |



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Azure Monitor-Datenplattform, auf der mit Erkenntnissen und Lösungen gesammelte Protokolle und Metriken gespeichert werden](platform/data-platform.md).
- Arbeiten Sie ein [Tutorial zum Überwachen einer Azure-Ressource](learn/tutorial-resource-logs.md) durch.
- Arbeiten Sie ein [Tutorial zum Schreiben einer Protokollabfrage für die Analyse von Daten in Azure Monitor-Protokollen](learn/tutorial-resource-logs.md) durch.
- Arbeiten Sie ein [Tutorial zum Erstellen eines Metrikdiagramms für die Analyse von Daten in Azure Monitor-Metriken](learn/tutorial-metrics-explorer.md) durch.

