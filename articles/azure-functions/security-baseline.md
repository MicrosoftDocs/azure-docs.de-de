---
title: Azure-Sicherheitsbaseline für Azure Functions
description: Azure-Sicherheitsbaseline für Azure Functions
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 86e13c0f57b30d2c53d9194e89ec89e7abdf1574
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187050"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure-Sicherheitsbaseline für Azure Functions

Die Azure-Sicherheitsbaseline für Azure Functions enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Integrieren Sie Ihre Azure-Funktions-Apps in ein virtuelles Azure-Netzwerk. Funktions-Apps, die im Premium-Tarif ausgeführt werden, umfassen die gleichen Hostingfunktionen wie Web-Apps in Azure App Service, wozu auch das Feature „VNET-Integration“ gehört.  Mit virtuellen Azure-Netzwerken können Sie viele Ihrer Azure-Ressourcen, z. B. Azure Functions, in einem Netzwerk platzieren, das nicht über das Internet geroutet werden kann.

- [Tutorial: Integrieren von Functions in ein virtuelles Azure-Netzwerk](./functions-create-vnet.md)

- [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Netzwerkressourcen und -konfigurationen zu schützen, die sich auf Ihre Azure-Funktions-Apps beziehen.

Aktivieren Sie bei Verwendung von Netzwerksicherheitsgruppen (NSGs) für Ihre Azure Functions-Implementierung NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Wenn Sie Ihre Azure Functions-Endpunkte in einer Produktionsumgebung umfassend schützen möchten, sollten Sie eine der folgenden Sicherheitsoptionen auf Funktions-App-Ebene implementieren:
- Aktivieren Sie für Ihre Funktions-App die App Service-Authentifizierung/Autorisierung.
- Verwenden Sie Azure API Management (APIM) zum Authentifizieren von Anforderungen.
- Stellen Sie Ihre Funktions-App in einer Azure App Service-Umgebung bereit.

Stellen Sie außerdem sicher, dass das Remotedebuggen für Ihre Produktionsinstanz von Azure Functions deaktiviert wurde. Darüber hinaus sollte per CORS (Cross-Origin Resource Sharing) nicht allen Domänen Zugriff auf Ihre Azure-Funktions-App gewährt werden. Erlauben Sie nur erforderlichen Domänen die Interaktion mit Ihrer Azure-Funktions-App.

Erwägen Sie, die Azure Web Application Firewall (WAF) im Rahmen der Netzwerkkonfiguration bereitzustellen, um eine zusätzliche Untersuchung des eingehenden Datenverkehrs zu ermöglichen. Aktivieren Sie die Diagnoseeinstellung für WAF, und erfassen Sie Protokolle in ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich. 

- [Schützen eines HTTP-Endpunkts in einer Produktionsumgebung](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken für Ihre Funktions-Apps, um sich vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten öffentlichen IP-Adressen zu verweigern.
Konfigurieren Sie außerdem ein Front-End-Gateway, z. B. Azure Web Application Firewall, um alle eingehenden Anforderungen zu authentifizieren und schädlichen Datenverkehr herauszufiltern. Azure Web Application Firewall trägt zum Schutz Ihrer Azure-Funktions-Apps bei, indem eingehender Webdatenverkehr untersucht wird und die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden. Für die Einführung einer WAF wird entweder eine App Service-Umgebung benötigt, oder Sie müssen private Endpunkte (Vorschau) verwenden. Vergewissern Sie sich, dass sich private Endpunkte nicht mehr in der Vorschauphase befinden, bevor Sie sie für Produktionsworkloads einsetzen.

- [Netzwerkoptionen von Azure Functions](./functions-networking-options.md)

- [Premium-Plan](./functions-scale.md#premium-plan)

- [Einführung in die App Service-Umgebungen](../app-service/environment/intro.md)

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](../app-service/environment/network-info.md)

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

- [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Grundlegendes zur Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../security-center/security-center-just-in-time.md)

- [Verwenden privater Endpunkte für eine Azure-Web-App (Vorschau)](../app-service/networking/private-endpoint.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Aktivieren Sie bei Verwendung von Netzwerksicherheitsgruppen (NSGs) für Ihre Azure Functions-Implementierung Flussprotokolle für die Netzwerksicherheitsgruppen, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Speicherkonto. Sie können auch Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Konfigurieren Sie ein Front-End-Gateway, z. B. Azure Web Application Firewall, um alle eingehenden Anforderungen zu authentifizieren und schädlichen Datenverkehr herauszufiltern. Azure Web Application Firewall trägt zum Schutz Ihrer Funktions-Apps bei, indem eingehender Webdatenverkehr untersucht wird und die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden. Für die Einführung einer WAF wird entweder eine App Service-Umgebung benötigt, oder Sie müssen private Endpunkte (Vorschau) verwenden. Vergewissern Sie sich, dass sich private Endpunkte nicht mehr in der Vorschauphase befinden, bevor Sie sie für Produktionsworkloads einsetzen.

Als Alternative stehen verschiedene Marketplace-Optionen wie Barracuda WAF for Azure zur Verfügung, die über den Azure Marketplace bezogen werden können. Hierzu gehören auch Angriffserkennungs- und Eindringschutzsysteme (IDS/IPS).

- [Netzwerkoptionen von Azure Functions](./functions-networking-options.md)

- [Premium-Plan](./functions-scale.md#premium-plan)

- [Einführung in die App Service-Umgebungen](../app-service/environment/intro.md)

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](../app-service/environment/network-info.md)

- [Was ist Azure Application Gateway?](../web-application-firewall/index.yml)

- [Verwenden privater Endpunkte für eine Azure-Web-App (Vorschau)](../app-service/networking/private-endpoint.md)

- [Konfiguration des Barracuda WAF-Clouddiensts](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Konfigurieren Sie ein Front-End-Gateway für Ihr Netzwerk, z. B. Azure Web Application Firewall mit End-to-End-TLS-Verschlüsselung. Für die Einführung einer WAF wird entweder eine App Service-Umgebung benötigt, oder Sie müssen private Endpunkte (Vorschau) verwenden. Vergewissern Sie sich, dass sich private Endpunkte nicht mehr in der Vorschauphase befinden, bevor Sie sie für Produktionsworkloads einsetzen.

- [Netzwerkoptionen von Azure Functions](./functions-networking-options.md)

- [Premium-Plan](./functions-scale.md#premium-plan)

- [Einführung in die App Service-Umgebungen](../app-service/environment/intro.md)

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](../app-service/environment/network-info.md)

- [Was ist Azure Application Gateway?](../web-application-firewall/index.yml)

- [Konfigurieren von End-to-End-TLS mit Application Gateway im Azure-Portal](../application-gateway/end-to-end-ssl-portal.md)

- [Verwenden privater Endpunkte für eine Azure-Web-App (Vorschau)](../app-service/networking/private-endpoint.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (z. B. „AzureAppService“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer Azure Functions-Instanz. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.Web“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Functions-Instanz zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Functions verwenden, z. B.:
- CORS sollte nicht jeder Ressource Zugriff auf Ihre Funktions-App erteilen.
- Zugriff auf Funktions-App nur über HTTPS gestatten
- In Ihrer Funktions-App sollte die neueste TLS-Version verwendet werden.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen. Dabei werden wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt. Sie können die Blaupause ganz einfach auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Nutzen Sie bei Verwendung von Netzwerksicherheitsgruppen (NSGs) für Ihre Azure Functions-Implementierung Tags für die NSGs und anderen Ressourcen, die sich auf die Netzwerksicherheit und den Datenverkehrsfluss beziehen. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen bzw. Erkennen von Änderungen für Netzwerkeinstellungen und -ressourcen, die sich auf Ihre Azure Functions-Bereitstellungen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkeinstellungen oder -ressourcen ausgelöst werden. 

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle für Zeitstempel in den Protokollen, die für Azure-Ressourcen, z. B. Azure Functions, verwendet wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Azure Functions verfügt auch über eine standardmäßig vorhandene Integration in Azure Application Insights zum Überwachen von Funktionen. Mit Application Insights werden Protokoll-, Leistungs- und Fehlerdaten erfasst. Der Dienst erkennt Leistungsanomalien automatisch und verfügt über leistungsstarke Analysetools, mit denen Sie Probleme untersuchen und nachvollziehen können, wie Ihre Funktionen verwendet werden.

Wenn Ihre Azure-Funktions-App über eine integrierte benutzerdefinierte Sicherheits- bzw. Überwachungsprotokollierung verfügt, sollten Sie die Diagnoseeinstellung „FunctionAppLogs“ aktivieren und die Protokolle an einen Log Analytics-Arbeitsbereich, Azure Event Hub oder Azure-Speicher zur Archivierung senden. 

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung eines Drittanbieters aktivieren und dafür Daten integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Azure Functions](./functions-monitoring.md)

- [Überwachen von Azure Functions mit Azure Monitor-Protokollen](./functions-monitor-log-analytics.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Wenn Ihre Azure-Funktions-App über eine integrierte benutzerdefinierte Sicherheits- bzw. Überwachungsprotokollierung verfügt, sollten Sie die Diagnoseeinstellung „FunctionAppLogs“ aktivieren und die Protokolle an einen Log Analytics-Arbeitsbereich, Azure Event Hub oder Azure-Speicher zur Archivierung senden. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Azure Functions mit Azure Monitor-Protokollen](./functions-monitor-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum für Protokolle für Log Analytics-Arbeitsbereiche, die Ihren Azure-Funktions-Apps zugeordnet sind, gemäß den Compliancevorschriften Ihrer Organisation fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure-Funktions-App, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren.

Aktivieren Sie Application Insights für Ihre Azure-Funktions-Apps, um Protokoll-, Leistungs- und Fehlerdaten zu erfassen. Sie können die mit Application Insights erfassten Telemetriedaten im Azure-Portal anzeigen.

Wenn Ihre Azure-Funktions-App über eine integrierte benutzerdefinierte Sicherheits- bzw. Überwachungsprotokollierung verfügt, sollten Sie die Diagnoseeinstellung „FunctionAppLogs“ aktivieren und die Protokolle an einen Log Analytics-Arbeitsbereich, Azure Event Hub oder Azure-Speicher zur Archivierung senden. 

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung eines Drittanbieters aktivieren und dafür Daten integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Azure Functions mit Azure Monitor-Protokollen](./functions-monitor-log-analytics.md)

- [Überwachen von Azure Functions](./functions-monitoring.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure-Funktions-App, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren. Sie können Warnungen anhand von Abfragen Ihrer Log Analytics-Arbeitsbereiche erstellen.

Aktivieren Sie Application Insights für Ihre Azure-Funktions-Apps, um Protokoll-, Leistungs- und Fehlerdaten zu erfassen. Sie können die mit Application Insights erfassten Telemetriedaten im Azure-Portal anzeigen und Warnungen dafür erstellen.

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung eines Drittanbieters aktivieren und dafür Daten integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Azure Functions mit Azure Monitor-Protokollen](./functions-monitor-log-analytics.md)

- [Aktivieren der Application Insights-Integration](./configure-monitoring.md#enable-application-insights-integration)

- [Reagieren auf Ereignisse mit Azure Monitor-Warnungen](../azure-monitor/learn/tutorial-response.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Mit Azure-Funktions-Apps werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Mit Azure-Funktions-Apps werden keine DNS-bezogenen Protokolle, auf die Benutzer zugreifen können, verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind. 

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Der Zugriff auf Steuerungsebene auf Azure Functions wird mit Azure Active Directory (AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

Der Zugriff auf Datenebene kann auf unterschiedliche Weise gesteuert werden, z. B. mit Autorisierungsschlüsseln, Netzwerkeinschränkungen und Überprüfung einer Azure AD-Identität. Autorisierungsschlüssel werden von den Clients verwendet, die Verbindungen mit Ihren Azure Functions-HTTP-Endpunkten herstellen, und können jederzeit neu erstellt werden. Diese Schlüssel werden für neue HTTP-Endpunkte standardmäßig generiert.

Für Funktions-Apps sind mehrere Bereitstellungsmethoden verfügbar, bei denen teilweise generierte Anmeldeinformationen genutzt werden. Informieren Sie sich über die Bereitstellungsmethoden, die für Ihre Anwendung verwendet werden sollen.

- [Schützen eines HTTP-Endpunkts in einer Produktionsumgebung](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Abrufen von Schlüsseln](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Bereitstellungstechnologien in Azure Functions](./functions-deployment-technologies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B: Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein. Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden, ebenso externe Konten mit Besitzerberechtigungen.

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen für den Datenzugriff auf Ihre Funktions-App zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an. Implementieren Sie einmaliges Anmelden für Ihre Azure-Funktions-Apps, indem Sie das Feature für die App Service-Authentifizierung/Autorisierung verwenden.

- [Authentifizierung und Autorisierung in Azure App Service und Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Azure-Funktions-Apps. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

- [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](../app-service/configure-authentication-provider-aad.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Azure-Funktions-Apps. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten sowie auf Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration mit Azure Sentinel oder der SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

- [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](../app-service/configure-authentication-provider-aad.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Azure-Funktions-Apps. Falls bei der Kontoanmeldung auf Steuerungsebene (Azure-Portal) ein abweichendes Anmeldeverhalten auftritt, sollten Sie Azure Active Directory (AD) Identity Protection und Risikoerkennungsfeatures verwenden, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Derzeit nicht verfügbar. Kunden-Lockbox wird für Azure Functions derzeit nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure-Funktions-Apps müssen durch ein virtuelles Netzwerk (VNET) bzw. Subnetz getrennt und entsprechend mit Tags versehen werden.

Sie können auch private Endpunkte verwenden, um eine Netzwerkisolation zu erzielen. Ein privater Azure-Endpunkt ist eine Netzwerkschnittstelle, über die basierend auf Azure Private Link eine private und sichere Verbindung mit einem Dienst hergestellt wird (z. B. HTTPS-Endpunkt einer Azure-Funktions-App). Der private Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst effektiv in Ihr VNet ein. Private Endpunkte befinden sich für Funktions-Apps, die im Premium-Tarif ausgeführt werden, in der Vorschauphase. Vergewissern Sie sich, dass sich private Endpunkte nicht mehr in der Vorschauphase befinden, bevor Sie sie für Produktionsworkloads einsetzen.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Netzwerkoptionen von Azure Functions](./functions-networking-options.md)

- [Premium-Plan](./functions-scale.md#premium-plan)

- [Was ist privater Endpunkt in Azure?](../private-link/private-endpoint-overview.md)

- [Verwenden privater Endpunkte für eine Azure-Web-App (Vorschau)](../app-service/networking/private-endpoint.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Stellen Sie ein automatisiertes Tool an den Netzwerkperimetern bereit, das die nicht autorisierte Übertragung von sensiblen Informationen überwacht und derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt. 

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Functions und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Aktivieren Sie im Azure-Portal für Ihre Azure-Funktions-Apps unter „Plattformfeatures:  Netzwerk: SSL“ die Einstellung „Nur HTTPS“, und legen Sie die Mindestversion für TLS auf 1.2 fest.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Derzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Functions derzeit nicht verfügbar. Versehen Sie Funktions-Apps, mit denen vertrauliche Informationen verarbeitet werden, mit Tags, und implementieren Sie bei Bedarf eine Drittanbieterlösung für Compliancezwecke.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf die Steuerungsebene (Azure-Portal) von Azure Functions zu steuern. 

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Functions und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Warteschlangen- und Tabellenspeicher unterstützt. Der Grund hierfür ist, dass Functions für Vorgänge wie das Verwalten von Triggern und das Ausführen von Protokollierfunktionen auf Azure Storage basiert. Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Zur Schaffung einer zusätzlichen Kontrollmöglichkeit für Verschlüsselungsschlüssel können Sie vom Kunden verwaltete Schlüssel bereitstellen, mit denen Blob- und Dateidaten verschlüsselt werden können. Diese Schlüssel müssen in Azure Key Vault vorhanden sein, damit die Funktions-App auf das Speicherkonto zugreifen kann.

- [Speicheraspekte für Azure Functions](./storage-considerations.md)

- [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Azure-Funktions-Apps für die Produktion und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Nutzen Sie ein DevSecOps-Verfahren, um sicherzustellen, dass Ihre Azure-Funktions-Apps sicher sind und während des gesamten Lebenszyklus möglichst gut geschützt bleiben. Mit DevSecOps werden das Sicherheitsteam Ihrer Organisation und die entsprechenden Funktionen in Ihre DevOps-Verfahren eingebunden, damit die Sicherheit in der Verantwortung aller Teammitglieder liegt.

Befolgen Sie außerdem die Empfehlungen aus Azure Security Center, um Ihre Azure-Funktions-Apps zu schützen.

- [Hinzufügen einer ständigen Sicherheitsüberprüfung zu Ihrer CI/CD-Pipeline](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Microsoft führt die Verwaltung von Sicherheitsrisiken für die zugrunde liegenden Systeme durch, die Azure Functions unterstützen. Sie können aber jeweils den Schweregrad, der für die Empfehlungen in Azure Security Center angegeben ist, und die Sicherheitsbewertung verwenden, um das Risiko für Ihre Umgebung zu messen. Ihre Sicherheitsbewertung basiert darauf, wie viele Security Center-Empfehlungen Sie umgesetzt haben. Sehen Sie sich jeweils den Schweregrad einer Empfehlung an, um festzulegen, welche Empfehlungen zuerst umgesetzt werden sollten.

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln.  Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen: Nicht zulässige Ressourcentypen, zulässige Ressourcentypen.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Definieren Sie genehmigte Azure-Ressourcen und genehmigte Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken. 

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen: Nicht zulässige Ressourcentypen, zulässige Ressourcentypen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Implementieren Sie für vertrauliche oder mit hohem Risiko behaftete Azure-Funktions-Apps separate Abonnements bzw. Verwaltungsgruppen, um für Isolation zu sorgen.

Stellen Sie Azure-Funktions-Apps, bei denen es sich um Hochrisiko-Apps handelt, in einem eigenen virtuellen Netzwerk (VNET) bereit. Die Umgebungssicherheit wird in Azure Functions mit VNETs erreicht. Funktionen, die unter dem Premium-Tarif oder in einer App Service-Umgebung (ASE) ausgeführt werden, können in VNETs integriert werden. Wählen Sie die beste Architektur für Ihren Anwendungsfall aus.

- [Netzwerkoptionen von Azure Functions](./functions-networking-options.md)

- [Premium-Plan](./functions-scale.md#premium-plan)

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](../app-service/environment/network-info.md)

- [Erstellen einer externen App Service-Umgebung](../app-service/environment/create-external-ase.md)

Erstellen einer internen App Service-Umgebung:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure-Funktions-App mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure-Funktions-Apps zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:
- Funktions-sollte muss verwaltete Identität verwenden.
- Remotedebuggen sollte für Funktions-Apps deaktiviert sein.
- Zugriff auf Funktions-App nur über HTTPS gestatten

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Die Bereitstellung von lokalen Funktionen ist zwar möglich, aber diese Richtlinie ist für IaaS-Computeressourcen bestimmt. Beim Bereitstellen von lokalen Funktionen sind Sie für die sichere Konfiguration Ihrer Umgebung verantwortlich.

- [Lokale Funktionen](./functions-runtime-install.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Speichern und verwalten Sie ARM-Vorlagen und benutzerdefinierte Azure-Richtliniendefinitionen auf sichere Weise per Quellcodeverwaltung.

- [Was ist „Infrastructure-as-Code“?](/azure/devops/learn/what-is-infrastructure-as-code)

- [Entwerfen von Workflows für Policy-as-Code](../governance/policy/concepts/policy-as-code.md)

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (überwachen), [deny] (verweigern) und [deploy if not existent] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie verwaltete Identitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen. Mit verwalteten Identitäten können Sie für Ihre Funktions-App die Authentifizierung bei jedem Dienst nutzen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen im Code keine Anmeldeinformationen enthalten sein.

- [Erstellen einer Key Vault-Instanz](../key-vault/secrets/quick-create-portal.md)

- [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

- [Verwenden von Key Vault-Verweisen für App Service und Azure Functions](../app-service/app-service-key-vault-references.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Ihre Azure-Funktions-App mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Functions) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Functions) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Verwenden Sie das Feature „Sichern und Wiederherstellen“, um regelmäßige Sicherungen Ihrer App zu planen. Funktions-Apps, die im Premium-Tarif ausgeführt werden, umfassen die gleichen Hostingfunktionen wie Web-Apps in Azure App Service, wozu auch das Feature „Sichern und Wiederherstellen“ gehört.

Nutzen Sie auch eine Lösung für die Quellcodeverwaltung, z. B. Azure Repos und Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Für Azure DevOps Services werden viele Azure Storage-Features genutzt, um sicherzustellen, dass bei einem Hardwarefehler, einer Dienstunterbrechung oder einer regionalen Notfallsituation die Verfügbarkeit der Daten gewährleistet ist. Darüber hinaus wendet das Azure DevOps-Team Verfahren an, um Daten vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Sichern Ihrer App in Azure](../app-service/manage-backup.md)

- [Datenverfügbarkeit](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Verwenden Sie das Feature „Sichern und Wiederherstellen“, um regelmäßige Sicherungen Ihrer App zu planen. Funktions-Apps, die im Premium-Tarif ausgeführt werden, umfassen die gleichen Hostingfunktionen wie Web-Apps in Azure App Service, wozu auch das Feature „Sichern und Wiederherstellen“ gehört. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

Nutzen Sie auch eine Lösung für die Quellcodeverwaltung, z. B. Azure Repos und Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Für Azure DevOps Services werden viele Azure Storage-Features genutzt, um sicherzustellen, dass bei einem Hardwarefehler, einer Dienstunterbrechung oder einer regionalen Notfallsituation die Verfügbarkeit der Daten gewährleistet ist. Darüber hinaus wendet das Azure DevOps-Team Verfahren an, um Daten vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Sichern Ihrer App in Azure](../app-service/manage-backup.md)

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Datenverfügbarkeit](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Stellen Sie sicher, dass mit dem Feature „Sichern und Wiederherstellen“ regelmäßige Wiederherstellungen durchgeführt werden können. Bei Verwendung eines anderen Offlinespeicherorts für die Sicherung Ihres Codes sollten Sie sich regelmäßig vergewissern, dass vollständige Wiederherstellungen möglich sind. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

- [Wiederherstellen einer App in Azure](../app-service/web-sites-restore.md)

- [Wiederherstellen einer App in Azure auf der Grundlage einer Momentaufnahme](../app-service/app-service-web-restore-snapshots.md)

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Bei Sicherungen über das Feature „Sichern und Wiederherstellen“ wird ein Azure Storage-Konto Ihres Abonnements verwendet. Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Zur Schaffung einer zusätzlichen Kontrollmöglichkeit für Verschlüsselungsschlüssel können Sie vom Kunden verwaltete Schlüssel bereitstellen, mit denen Speicherdaten verschlüsselt werden können.

Wenn Sie vom Kunden verwaltete Schlüssel verwenden, sollten Sie sicherstellen, dass in Key Vault das vorläufige Löschen aktiviert ist, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md)

- [Aktivieren des vorläufigen Löschens in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um mit Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).