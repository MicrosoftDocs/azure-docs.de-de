---
title: Azure-Sicherheitsbaseline für API Management
description: Azure-Sicherheitsbaseline für API Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62b4629d258f8779dc53b4b8e00684d3b793291d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183752"
---
# <a name="azure-security-baseline-for-api-management"></a>Azure-Sicherheitsbaseline für API Management

Die Azure-Sicherheitsbaseline für API Management enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Azure API Management kann in einem virtuellen Azure-Netzwerk (VNET) bereitgestellt werden, damit der Dienst auf Back-End-Dienste im Netzwerk zugreifen kann. Das Entwicklerportal und das API Management-Gateway können so konfiguriert werden, dass darauf entweder über das Internet (extern) oder nur vom VNET (intern) aus zugegriffen werden kann.
- Extern: Auf das API Management-Gateway und Entwicklerportal kann vom öffentlichen Internet über einen externen Lastenausgleich zugegriffen werden. Das Gateway kann auf Ressourcen innerhalb des öffentlichen Netzwerks zugreifen.
- Intern: Auf das API Management-Gateway und Entwicklerportal kann nur aus dem virtuellen Netzwerk heraus über einen internen Lastenausgleich zugegriffen werden. Das Gateway kann auf Ressourcen innerhalb des öffentlichen Netzwerks zugreifen.

Ein- und ausgehender Datenverkehr im Subnetz, in dem API Management bereitgestellt wurde, kann mithilfe einer Netzwerksicherheitsgruppe gesteuert werden.

* [Verwenden von Azure API Management mit virtuellen Netzwerken](./api-management-using-with-vnet.md)

* [Verwenden von Azure API Management mit einem internen virtuellen Netzwerk](./api-management-using-with-internal-vnet.md)

* [Integrieren von API Management in ein internes VNET mit Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Ein- und ausgehender Datenverkehr im Subnetz, in dem API Management bereitgestellt wurde, kann mit Netzwerksicherheitsgruppen (NSGs) gesteuert werden. Stellen Sie eine NSG in Ihrem API Management-Subnetz bereit, und aktivieren Sie NSG-Flussprotokolle. Senden Sie Protokolle für die Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Vorsicht: Beim Konfigurieren einer NSG im API Management-Subnetz müssen einige Ports geöffnet sein. Wenn diese Ports nicht verfügbar sind, funktioniert API Management möglicherweise nicht ordnungsgemäß und kann möglicherweise nicht mehr aufgerufen werden.

* [Verwenden von Azure API Management mit virtuellen Netzwerken: Allgemeine Probleme mit der Netzwerkkonfiguration](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Konfigurieren Sie API Management als Schutz für kritische Web/HTTP-APIs in einem virtuellen Netzwerk (VNET) im internen Modus und zusätzlich eine Azure Application Gateway-Instanz. Application Gateway ist ein PaaS-Dienst. Das Gateway fungiert als Reverseproxy und verfügt über L7-Lastenausgleich, Routing, Web Application Firewall (WAF) und andere Dienste.

Die Kombination aus einer Bereitstellung von API Management in einem internen VNET und dem Application Gateway-Front-End ermöglicht die folgenden Szenarien:
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen aller APIs sowohl für interne als auch für externe Consumer
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen eines Teils der APIs für externe Consumer
- Schaffen einer Möglichkeit, mit der der Zugriff auf API Management über das öffentliche Internet ein- und ausgeschaltet werden kann

Hinweis: Diese Funktion ist auf den Ebenen „Premium“ und „Developer“ von API Management verfügbar.

* [Integrieren von API Management in ein internes VNET mit Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Dokumentation zu Azure Application Gateway](../application-gateway/index.yml)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Konfigurieren Sie API Management in einem virtuellen Netzwerk (VNET) im internen Modus und zusätzlich eine Azure Application Gateway-Instanz. Application Gateway ist ein PaaS-Dienst. Das Gateway fungiert als Reverseproxy und verfügt über L7-Lastenausgleich, Routing, Web Application Firewall (WAF) und andere Dienste.

Die Kombination aus einer Bereitstellung von API Management in einem internen VNET und dem Application Gateway-Front-End ermöglicht die folgenden Szenarien:
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen aller APIs sowohl für interne als auch für externe Consumer
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen eines Teils der APIs für externe Consumer
- Schaffen einer Möglichkeit, mit der der Zugriff auf API Management über das öffentliche Internet ein- und ausgeschaltet werden kann

Hinweis: Diese Funktion ist auf den Ebenen „Premium“ und „Developer“ von API Management verfügbar.

Aktivieren Sie Azure DDoS Protection Standard in dem VNET, das Ihrer API Management-Bereitstellung zugeordnet ist, um für den Schutz vor verteilten Denial-of-Service-Angriffen (DDoS) zu sorgen.

Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

* [Integrieren von API Management in ein internes VNET mit Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Dokumentation zu Azure Application Gateway](../application-gateway/index.yml)

* [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](../ddos-protection/manage-ddos-protection.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Ein- und ausgehender Datenverkehr im Subnetz, in dem API Management bereitgestellt wurde, kann mit Netzwerksicherheitsgruppen (NSGs) gesteuert werden. Stellen Sie eine NSG in Ihrem API Management-Subnetz bereit, und aktivieren Sie NSG-Flussprotokolle. Senden Sie Protokolle für die Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Vorsicht: Beim Konfigurieren einer NSG im API Management-Subnetz müssen einige Ports geöffnet sein. Wenn diese Ports nicht verfügbar sind, funktioniert API Management möglicherweise nicht ordnungsgemäß und kann möglicherweise nicht mehr aufgerufen werden.

* [Verwenden von Azure API Management mit virtuellen Netzwerken: Allgemeine Probleme mit der Netzwerkkonfiguration](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Konfigurieren Sie API Management in einem virtuellen Netzwerk (VNET) im internen Modus und zusätzlich eine Azure Application Gateway-Instanz. Application Gateway ist ein PaaS-Dienst. Das Gateway fungiert als Reverseproxy und verfügt über L7-Lastenausgleich, Routing, Web Application Firewall (WAF) und andere Dienste. Application Gateway WAF ermöglicht den Schutz vor häufigen Sicherheitsexploits und -risiken und kann in den beiden folgenden Modi ausgeführt werden:
- Erkennungsmodus: Überwacht und protokolliert alle Bedrohungswarnungen. Sie können die Protokollierung von Diagnosedaten für Application Gateway im Abschnitt „Diagnose“ aktivieren. Sie müssen sicherstellen, dass das WAF-Protokoll ausgewählt und aktiviert ist. Im Erkennungsmodus werden eingehende Anforderungen von der Web Application Firewall nicht blockiert.
- Schutzmodus: Blockiert Eindringversuche und Angriffe, die die Regeln erkennen. Der Angreifer erhält eine Ausnahme vom Typ 403 (nicht autorisierter Zugriff), und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

Die Kombination aus einer Bereitstellung von API Management in einem internen VNET und dem Application Gateway-Front-End ermöglicht die folgenden Szenarien:
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen aller APIs sowohl für interne als auch für externe Consumer
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen eines Teils der APIs für externe Consumer
- Schaffen einer Möglichkeit, mit der der Zugriff auf API Management über das öffentliche Internet ein- und ausgeschaltet werden kann

Hinweis: Diese Funktion ist auf den Ebenen „Premium“ und „Developer“ von API Management verfügbar.

* [Integrieren von API Management in ein internes VNET mit Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Stellen Sie API Management zum Verwalten des Datenverkehrs, der an Web/HTTP-APIs fließt, im externen oder internen Modus in einem virtuellen Netzwerk (VNET) bereit, das der App Service-Umgebung zugeordnet ist.

Konfigurieren Sie im internen Modus eine Azure Application Gateway-Instanz, die API Management vorgeschaltet ist. Application Gateway ist ein PaaS-Dienst. Das Gateway fungiert als Reverseproxy und verfügt über L7-Lastenausgleich, Routing, Web Application Firewall (WAF) und andere Dienste. Application Gateway WAF bietet Schutz vor häufigen Sicherheitsexploits und Sicherheitsrisiken.

Die Kombination aus einer Bereitstellung von API Management in einem internen VNET und dem Application Gateway-Front-End ermöglicht die folgenden Szenarien:
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen aller APIs sowohl für interne als auch für externe Consumer
- Verwenden von nur einer API Management-Ressource zum Verfügbarmachen eines Teils der APIs für externe Consumer
- Schaffen einer Möglichkeit, mit der der Zugriff auf API Management über das öffentliche Internet ein- und ausgeschaltet werden kann

Hinweis: Diese Funktion ist auf den Ebenen „Premium“ und „Developer“ von API Management verfügbar.

* [Veröffentlichen interner APIs für externe Benutzer](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Verwenden von Azure API Management mit virtuellen Netzwerken](./api-management-using-with-vnet.md)

* [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

* [Dokumentation zu Azure Application Gateway](../application-gateway/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags für virtuelle Netzwerke (VNET), um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen (NSGs) zu definieren, die in Ihren API Management-Subnetzen verwendet werden. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Vorsicht: Beim Konfigurieren einer NSG im API Management-Subnetz müssen einige Ports geöffnet sein. Wenn diese Ports nicht verfügbar sind, funktioniert API Management möglicherweise nicht ordnungsgemäß und kann möglicherweise nicht mehr aufgerufen werden.

* [Diensttags in virtuellen Netzwerken](../virtual-network/service-tags-overview.md)

* [Für API Management erforderliche Ports](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer Azure API Management-Bereitstellungen. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.ApiManagement“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien für die Überprüfung oder Erzwingung der Netzwerkkonfiguration Ihrer Azure API Management-Bereitstellungen und der zugehörigen Ressourcen zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für virtuelle Azure-Netzwerke verwenden, z. B.:
- DDoS Protection Standard sollte aktiviert sein.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen. Dabei werden wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt. Sie können die Blaupause ganz einfach auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Für einzelne NSG-Regeln können Sie das Feld „Beschreibung“ verwenden, um geschäftliche Anforderungen bzw. deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die Ihren Azure API Management-Bereitstellungen zugeordnet sind. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure API Management.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Abfragen und Analysen durchzuführen, Protokolle zur langfristigen Speicherung/Archivierung oder Offlineanalyse an Azure Storage zu senden oder Protokolle über Azure Event Hubs in eine andere Analyselösung in Azure und an anderen Orten zu exportieren. Bei Azure API Management werden Protokolle und Metriken standardmäßig in Azure Monitor ausgegeben. Die Ausführlichkeit der Protokollierung kann für den gesamten Dienst und pro API konfiguriert werden.

Zusätzlich zu Azure Monitor kann Azure API Management auch in einen oder mehrere Azure Application Insights-Dienste integriert werden. Protokollierungseinstellungen für Application Insights können entweder pro Dienst oder pro API konfiguriert werden.

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung (Security Incident and Event Management) eines Drittanbieters aktivieren und dafür Daten integrieren.

* [Erstellen von Diagnoseeinstellungen im Azure-Portal](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management](./api-management-howto-log-event-hubs.md)

* [Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights](./api-management-howto-app-insights.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf Steuerungsebene die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle. Senden Sie Aktivitätsprotokolle zu Berichterstellungs- und Analysezwecken an einen Log Analytics-Arbeitsbereich, zur langfristigen sicheren Aufbewahrung an Azure Storage und zum Exportieren in andere Analyselösungen in Azure und an anderen Orten an Azure Event Hubs. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihren Azure API Management-Dienst durchgeführt werden.

Für die Überwachungsprotokollierung auf Datenebene enthalten Diagnoseprotokolle umfassende Informationen zu Vorgängen und Fehlern, die für die Überwachung und Problembehandlung relevant sind. Diagnoseprotokolle unterscheiden sich von Aktivitätsprotokollen. Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre Azure-Ressourcen ausgeführt wurden. Diagnoseprotokolle bieten Einblick in Vorgänge, die Ihre Ressource selbst ausgeführt hat.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Überwachen von veröffentlichten APIs](./api-management-howto-use-azure-monitor.md#activity-logs)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung.

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Archivieren von Azure-Ressourcenprotokollen in einem Speicherkonto](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Azure API Management gibt fortlaufend Protokolle und Metriken für Azure Monitor aus, sodass Sie nahezu in Echtzeit Erkenntnisse zum Status und zur Integrität Ihrer APIs erhalten. Mit Azure Monitor und Log Analytics-Arbeitsbereichen können Sie Warnungen überprüfen, abfragen, visualisieren, weiterleiten, archivieren und konfigurieren sowie Aktionen für Metriken und Protokolle durchführen, die von API Management und den zugehörigen Ressourcen bereitgestellt werden. Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und prüfen Sie die Ergebnisse regelmäßig.

Optional können Sie API Management in Azure Application Insights integrieren und als primäres oder sekundäres Tool für die Überwachung, Nachverfolgung, Berichterstellung und Benachrichtigung nutzen.

* [Überwachen von veröffentlichten APIs](./api-management-howto-use-azure-monitor.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights](./api-management-howto-app-insights.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure API Management-Instanzen, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren. Sie können Warnungen anhand von Abfragen Ihrer Log Analytics-Arbeitsbereiche erstellen.

Erstellen Sie Metrikwarnungen, damit Sie benachrichtigt werden, wenn etwas Unerwartetes passiert. Beispielsweise können Sie Benachrichtigungen erhalten, wenn Ihre Azure API Management-Instanz für einen bestimmten Zeitraum die erwartete Spitzenkapazität überschritten hat oder wenn in einem festgelegten Zeitraum eine bestimmte Anzahl von nicht autorisierten Gatewayanforderungen oder Fehlern aufgetreten ist.

Optional können Sie API Management in Azure Application Insights integrieren und als primäres oder sekundäres Tool für die Überwachung, Nachverfolgung, Berichterstellung und Benachrichtigung nutzen.

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung eines Drittanbieters aktivieren und dafür Daten integrieren.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Überwachen von veröffentlichten APIs](./api-management-howto-use-azure-monitor.md#activity-logs)

* [Konfigurieren einer Warnungsregel für Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

* [Kapazität einer Azure API Management-Instanz](./api-management-capacity.md)

* [Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights](./api-management-howto-app-insights.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Azure API Management verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Azure API Management verarbeitet keine DNS-bezogenen Protokolle, auf die Benutzer zugreifen können, und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie einen Bestand an Konten, für die Administratorzugriff auf die Steuerungsebene (Azure-Portal) von Azure API Management besteht.

Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. API Management basiert auf der rollenbasierten Zugriffssteuerung, um eine differenzierte Zugriffsverwaltung für API Management-Dienste und -Entitäten zu ermöglichen.

Darüber hinaus enthält API Management in seinem Benutzersystem auch die integrierte Gruppe „Administratoren“. Mit Gruppen wird in API Management die Sichtbarkeit von APIs im Entwicklerportal gesteuert. Die Mitglieder der Gruppe „Administratoren“ können alle APIs anzeigen.

Befolgen Sie die Empfehlungen aus Azure Security Center, die sich auf die Verwaltung und Pflege von Administratorkonten beziehen.

* [Verwenden der rollenbasierten Zugriffssteuerung in Azure API Management](./api-management-role-based-access-control.md)

* [Abrufen einer Liste mit Benutzern unter einer Azure API Management-Instanz](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Abrufen einer Liste mit Benutzern, die in Azure AD einer Verzeichnisrolle zugewiesen sind, mit PowerShell](/powershell/module/az.resources/get-azroleassignment)

* [Abrufen einer Verzeichnisrollendefinition in Azure AD mit PowerShell](/powershell/module/az.resources/get-azroledefinition)

* [Empfehlungen für Identität und Zugriff](../security-center/recommendations-reference.md#recs-identity)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure API Management verfügt nicht über ein Konzept mit Standardkennwörtern und -schlüsseln.

Für Azure API Management-Abonnements, die eine Möglichkeit zum Schützen des Zugriffs auf APIs darstellen, wird aber ein Paar mit Abonnementschlüsseln generiert. Kunden können diese Abonnementschlüssel jederzeit neu generieren.

* [Abonnements in Azure API Management](./api-management-subscriptions.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Azure API Management kann so konfiguriert werden, dass Azure Active Directory als Identitätsanbieter zum Authentifizieren von Benutzern im Entwicklerportal genutzt wird, um von den SSO-Funktionen von Azure AD profitieren zu können. Nach der Konfiguration können neue Benutzer des Entwicklerportals auswählen, ob sie den Standardanmeldeprozess nutzen, indem sie sich zuerst per Azure AD authentifizieren und nach Abschluss des Vorgangs dann den Anmeldeprozess im Portal durchführen.

* [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](./api-management-howto-aad.md)

Alternativ kann der Anmelde- bzw. Registrierungsvorgang per Delegierung auch weiter angepasst werden. Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Dadurch besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.

* [Delegieren von Benutzerregistrierung und Produktabonnierung](./api-management-howto-setup-delegation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

* [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure AD als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Konfigurieren Sie Ihr Entwicklerportal für Azure API Management, um Entwicklerkonten per Azure Active Directory zu authentifizieren.

Konfigurieren Sie Ihre Azure API Management-Instanz so, dass Ihre APIs über das OAuth 2.0-Protokoll mit Azure Active Directory (AD) geschützt sind.

* [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](./api-management-howto-aad.md)

* [Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management](./api-management-howto-protect-backend-with-aad.md)

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln von veralteten Konten. Kunden können Zugriffsüberprüfungen für Azure-Identitäten verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über den benötigten Zugriff verfügen.

Kunden können einen Bestand an Benutzerkonten für API Management verwalten und den Zugriff nach Bedarf abstimmen. In API Management sind Entwickler die Consumer der APIs, die per API Management verfügbar gemacht werden. Neu erstellte Entwicklerkonten sind standardmäßig „Aktiv“ und werden der Gruppe „Entwickler“ zugewiesen. Entwicklerkonten mit dem Status „Aktiv“ können sämtliche APIs aufrufen, die sie abonniert haben.

Administratoren können benutzerdefinierte Gruppen erstellen oder externe Gruppen in zugeordneten Azure Active Directory-Mandanten verwenden. Benutzerdefinierte und externe Gruppen können gemeinsam mit Systemgruppen verwendet werden, um API-Produkte für Entwickler sichtbar zu machen und ihnen den Zugriff auf die API-Produkte zu ermöglichen.

* [Verwalten von Benutzerkonten in Azure API Management](./api-management-howto-create-or-invite-developers.md)

* [Abrufen einer Liste mit API Management-Benutzern](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Erstellen und Verwenden von Gruppen für Entwicklerkonten in Azure API Management](./api-management-howto-create-groups.md)

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Konfigurieren Sie Ihre Azure API Management-Instanz so, dass Entwicklerkonten authentifiziert werden, indem Azure Active Directory als Identitätsanbieter in Azure API Management verwendet wird.

Konfigurieren Sie Ihre Azure API Management-Instanz so, dass Ihre APIs über das OAuth 2.0-Protokoll mit Azure Active Directory (AD) geschützt sind.

Konfigurieren Sie die JWT-Überprüfungsrichtlinie für eingehende API-Anforderungen, um für ein zulässiges Token das Vorhandensein und die Gültigkeit zu erzwingen.

Erstellen Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten, und senden Sie die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie die gewünschten Warnungen in Log Analytics. Darüber hinaus können Sie für den Log Analytics-Arbeitsbereich das Onboarding für Azure Sentinel oder die SIEM-Lösung eines Drittanbieters durchführen.

Konfigurieren Sie die erweiterte Überwachung mit API Management, indem Sie die Richtlinie `log-to-eventhub` verwenden, alle zusätzlichen Kontextinformationen erfassen, die für die Sicherheitsanalyse benötigt werden, und die Daten an Azure Sentinel oder die SIEM-Lösung eines Drittanbieters senden.

* [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](./api-management-howto-aad.md)

* [Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management](./api-management-howto-protect-backend-with-aad.md)

* [API Management-Richtlinien für die Zugriffsbeschränkung](./api-management-access-restriction-policies.md)

* [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Überwachen von APIs mit Azure API Management, Event Hubs und Moesif](./api-management-log-to-eventhub-sample.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Falls bei der Kontoanmeldung auf Steuerungsebene (Azure-Portal) ein abweichendes Anmeldeverhalten auftritt, sollten Sie Azure Active Directory (AD) Identity Protection und Risikoerkennungsfeatures verwenden, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Derzeit nicht verfügbar. Kunden-Lockbox wird für Azure API Management derzeit nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure API Management-Instanzen müssen durch ein virtuelles Netzwerk (VNET) bzw. Subnetz getrennt und entsprechend mit Tags versehen werden.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Verwenden von Azure API Management mit virtuellen Netzwerken](./api-management-using-with-vnet.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Derzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure API Management derzeit nicht verfügbar.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure API Management und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Aufrufe auf Verwaltungsebene erfolgen über Azure Resource Manager und TLS. Ein gültiges JSON Web Token (JWT) ist erforderlich. Aufrufe auf Datenebene können mit TLS und einem der unterstützten Authentifizierungsmechanismen (z. B. Clientzertifikat oder JWT) geschützt werden.

* [Schutz von Daten](#data-protection)

* [Verwalten von Protokollen und Verschlüsselungen in Azure API Management](./api-management-howto-manage-protocols-ciphers.md)

* [Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management](./api-management-howto-protect-backend-with-aad.md)

* [Schützen des SPA-Back-Ends mit OAuth 2.0, Azure Active Directory B2C und Azure API Management](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Derzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure API Management derzeit nicht verfügbar. Versehen Sie Azure API Management-Dienste, die vertrauliche Informationen verarbeiten, mit Tags, und implementieren Sie bei Bedarf für Compliancezwecke eine Drittanbieterlösung.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung, um den Zugriff auf Azure API Management zu steuern. Azure API Management basiert auf der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure, um eine differenzierte Zugriffsverwaltung für API Management-Dienste und -Entitäten (etwa APIs und Richtlinien) zu ermöglichen.

* [Verwenden der rollenbasierten Zugriffssteuerung in Azure API Management](./api-management-role-based-access-control.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure API Management und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Vertrauliche Daten, z. B. Zertifikate, Schlüssel und als geheim gekennzeichnete Werte, werden mit vom Dienst verwalteten Schlüsseln für die jeweilige Dienstinstanz verschlüsselt. Alle Verschlüsselungsschlüssel gelten pro Dienstinstanz und werden vom Dienst verwaltet.

* [Schutz von Daten](#data-protection)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Azure-Funktions-Apps für die Produktion und an anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

* [Überwachen von veröffentlichten APIs](./api-management-howto-use-azure-monitor.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Derzeit nicht verfügbar. Die Sicherheitsrisikobewertung in Azure Security Center ist für Azure API Management derzeit nicht verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Überprüfen Sie die verfügbaren Sicherheitsfunktionen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

* [Sicherheitskontrollen für API Management]()

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Derzeit nicht verfügbar. Die Sicherheitsrisikobewertung in Azure Security Center ist für Azure API Management derzeit nicht verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Kunden sollten die für sie verfügbaren Sicherheitsfunktionen überprüfen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

* [Sicherheitskontrollen für API Management]()

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Ihren Abonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

* [Verwenden der rollenbasierten Zugriffssteuerung in Azure API Management](./api-management-role-based-access-control.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihren Azure API Management-Dienst. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ApiManagement“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure API Management-Dienste zu erstellen.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihre Azure API Management-Dienste. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ApiManagement“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration von Azure API Management-Instanzen zu erstellen. Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie bei Verwendung von benutzerdefinierten Azure-Richtliniendefinitionen Azure DevOps oder Azure Repos, um die Konfiguration Ihres Azure API Management-Diensts sicher zu speichern und zu verwalten.

* [Speichern von Dateien in Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihre Azure API Management-Dienste. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ApiManagement“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration von Azure API Management-Instanzen zu erstellen. Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie das Azure API Management DevOps Resource Kit, um die Konfigurationsverwaltung für Azure API Management durchzuführen.

Definieren und implementieren Sie zusätzlich mit Azure Policy Standardsicherheitskonfigurationen für Ihre Azure API Management-Dienste. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ApiManagement“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration von Azure API Management-Instanzen zu erstellen. Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Nutzen Sie Key Vault zum Verwalten von Zertifikaten, und legen Sie die automatische Rotation fest. Falls Sie mit Azure Key Vault das benutzerdefinierte SSL-Domänenzertifikat verwalten, sollten Sie sicherstellen, dass es in Key Vault nicht als Geheimnis, sondern als Zertifikat eingefügt wird.

* [Konfigurieren eines benutzerdefinierten Domänennamens](./configure-custom-domain.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie die von Azure Active Directory (AD) generierte verwaltete Dienstidentität, um zuzulassen, dass Ihre API Management-Instanz einfach und sicher auf andere mit Azure AD geschützte Ressourcen wie Azure Key Vault zugreifen kann.

* [Verwenden von verwalteten Identitäten in Azure API Management](./api-management-howto-use-managed-service-identity.md)

* [Authentifizierung mit verwalteter Identität](./api-management-authentication-policies.md#ManagedIdentity)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure API Management) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure API Management) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure API Management) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Indem Sie Ihre APIs über Azure API Management veröffentlichen und verwalten, profitieren Sie von Fehlertoleranz- und Infrastrukturfunktionen, die Sie sonst erst manuell entwickeln, implementieren und verwalten müssten. Für API Management wird die Bereitstellung in mehreren Regionen unterstützt, um die Datenebene gegen regionale Ausfälle abzusichern, ohne dass es zu betriebsbezogenem Mehraufwand kommt.

Die API Management-Features zur Sicherung und Wiederherstellung von Diensten enthalten die notwendigen Bausteine für die Implementierung einer Notfallwiederherstellungsstrategie. Sicherungs- und Wiederherstellungsvorgänge können manuell oder automatisiert durchgeführt werden.

* [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](./api-management-howto-deploy-multi-region.md)

* [Implementieren der Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Aufrufen des API Management-Sicherungsvorgangs](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [Aufrufen des API Management-Wiederherstellungsvorgangs](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Bei Sicherungs- und Wiederherstellungsvorgängen von Azure API Management wird eine vollständige Systemsicherung und -wiederherstellung durchgeführt.

Verwaltete Identitäten können für das Abrufen von Zertifikaten aus Azure Key Vault für benutzerdefinierte API Management-Domänennamen verwendet werden. Sichern Sie alle Zertifikate, die in Azure Key Vault gespeichert werden.

* [Implementieren der Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Sichern von Azure Key Vault-Zertifikaten](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Überprüfen Sie die Sicherungen, indem Sie testweise eine Wiederherstellung des Diensts und der Zertifikate aus Sicherungskopien durchführen.

* [Aufrufen des API Management-Wiederherstellungsvorgangs](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Wiederherstellen von Azure Key Vault-Zertifikaten](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Bei Azure API Management werden Sicherungen in Azure Storage-Konten geschrieben, die sich im Besitz des Kunden befinden. Befolgen Sie die Sicherheitsempfehlungen für Azure Storage, um Ihre Sicherungen zu schützen.

* [Implementieren der Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Sicherheitsempfehlungen für Blob Storage](../storage/blobs/security-recommendations.md)

Aktivieren Sie das vorläufige Löschen in Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

* [Aktivieren des vorläufigen Löschens in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

* [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Nutzen Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Anleitung**: * [Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.).

* Unter [diesem Link](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) finden Sie weitere Informationen zur Microsoft-Strategie und zur Durchführung von Red Teaming und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).