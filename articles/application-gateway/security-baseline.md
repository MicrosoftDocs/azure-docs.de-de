---
title: Azure-Sicherheitsbaseline für Azure Application Gateway
description: Azure-Sicherheitsbaseline für Azure Application Gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 49b806834783c3ecd77d9500d9d002877f4d2388
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396973"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azure-Sicherheitsbaseline für Azure Application Gateway

Die Azure-Sicherheitsbaseline für Azure Application Gateway enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden** : Stellen Sie sicher, dass alle Virtual Network-Azure Application Gateway-Bereitstellungen von Subnetzen einer Netzwerksicherheitsgruppe (NSG) zugeordnet wurden, deren Netzwerkzugriffssteuerungen für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. NSGs werden zwar in Azure Application Gateway unterstützt, es gibt jedoch einige Einschränkungen und Anforderungen, die befolgt werden müssen, damit Ihre NSG und Azure Application Gateway erwartungsgemäß funktionieren.

* [Application Gateway – Konfigurationsübersicht](./configuration-overview.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden** : Aktivieren Sie NSG-Flowprotokolle für die NSGs, die Ihren Azure Application Gateway-Subnetzen zugeordnet sind, und senden Sie die Protokolle für eine Überprüfung des Datenverkehrs an ein Speicherkonto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Hinweis: Unter Umständen wird zugelassener Datenverkehr nicht in den NSG-Flowprotokollen angezeigt, die Ihren Azure Application Gateway-Subnetzen zugeordnet sind. Entspricht Ihre Konfiguration dem folgenden Szenario, wird zulässiger Datenverkehr nicht in Ihren NSG-Flussprotokollen angezeigt:
- Sie haben Application Gateway v2 bereitgestellt.
- Im Application Gateway-Subnetz ist eine NSG vorhanden.
- Sie haben NSG-Flussprotokolle für diese NSG aktiviert.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

* [Häufig gestellte Fragen zu Application Gateway](./application-gateway-faq.md#diagnostics-and-logging)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden** : Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Die Web Application Firewall (WAF) ist ein Dienst (Feature von Azure Application Gateway), der zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und häufigen Sicherheitsrisiken bietet. Die Azure WAF trägt zum Schutz Ihrer Azure App Service-Web-Apps bei, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden. Die WAF basiert auf den Regeln der OWASP-Kernregelsätze (Open Web Application Security Project) der Versionen 3.1 (nur WAF_v2), 3.0 und 2.2.9.

* [Azure Application Gateway-Features](./features.md)

* [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden** : Aktivieren Sie in den Azure Virtual Network-Netzwerken, die Ihren Produktionsinstanzen von Azure Application Gateway zugeordnet sind, den Standardschutz vor DDoS-Angriffen. Verwenden Sie die integrierten Informationen zu Bedrohungen in Azure Security Center, um die Kommunikation mit bekannten schädlichen IP-Adressen zu verweigern.

* [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden** : Aktivieren Sie NSG-Flowprotokolle für die NSGs, die Ihren Azure Application Gateway-Subnetzen zugeordnet sind, und senden Sie die Protokolle für eine Überprüfung des Datenverkehrs an ein Speicherkonto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Hinweis: Unter Umständen wird zugelassener Datenverkehr nicht in den NSG-Flowprotokollen angezeigt, die Ihren Azure Application Gateway-Subnetzen zugeordnet sind. Entspricht Ihre Konfiguration dem folgenden Szenario, wird zulässiger Datenverkehr nicht in Ihren NSG-Flussprotokollen angezeigt:
- Sie haben Application Gateway v2 bereitgestellt.
- Im Application Gateway-Subnetz ist eine NSG vorhanden.
- Sie haben NSG-Flussprotokolle für diese NSG aktiviert.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

* [Häufig gestellte Fragen zu Application Gateway](./application-gateway-faq.md#diagnostics-and-logging)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden** : Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Die Web Application Firewall (WAF) ist ein Dienst (Feature von Azure Application Gateway), der zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und häufigen Sicherheitsrisiken bietet. Die Azure WAF trägt zum Schutz Ihrer Azure App Service-Web-Apps bei, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden. Die WAF basiert auf den Regeln der OWASP-Kernregelsätze (Open Web Application Security Project) der Versionen 3.1 (nur WAF_v2), 3.0 und 2.2.9.

Als Alternative stehen verschiedene Marketplace-Optionen wie Barracuda WAF for Azure zur Verfügung, die über den Azure Marketplace bezogen werden können. Hierzu gehören auch Angriffserkennungs- und Eindringschutzsysteme (IDS/IPS).

* [Azure Application Gateway-Features](./features.md)

* [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Konfiguration des Barracuda WAF-Clouddiensts](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden** : Stellen Sie Azure Application Gateway für Webanwendungen mit aktiviertem HTTPS/SSL für vertrauenswürdige Zertifikate bereit.

* [Bereitstellen von Application Gateway](./quick-create-portal.md)

* [Konfigurieren von Application Gateway für die Verwendung von HTTPS](./create-ssl-portal.md)

* [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](./overview.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden** : Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Namen des Diensttags (z. B. „GatewayManager“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder blockieren. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Sie müssen eingehenden Internetdatenverkehr für die NSGs, die Ihren Azure Application Gateway-Subnetzen zugeordnet sind, über die TCP-Ports 65503–65534 (für die Application Gateway v1-SKU) und 65200–65535 (für die v2-SKU) zulassen, indem Sie „Any“ (Beliebig) als Zielsubnetz und das Diensttag „GatewayManager“ als Quelle angeben. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von Azure-Zertifikaten geschützt (gesperrt). Externe Entitäten, einschließlich der Kunden dieses Gateways, können nicht auf diesen Endpunkten kommunizieren.

* [Grundlegendes zu Diensttags und deren Verwendung](../virtual-network/service-tags-overview.md)

* [Application Gateway – Konfigurationsübersicht](./configuration-overview.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden** : Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer Azure Application Gateway-Bereitstellungen. Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Application Gateway- und Azure Virtual Network-Instanzen sowie Ihrer NSGs zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause ganz einfach auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden** : Verwenden Sie Tags für NSGs, die Ihrem Azure Application Gateway-Subnetz zugeordnet sind, sowie für alle anderen Ressourcen, die mit der Netzwerksicherheit und dem Datenverkehrsfluss in Zusammenhang stehen. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden** : Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen bzw. Erkennen von Änderungen für Netzwerkeinstellungen und -ressourcen, die mit Ihren Azure Application Gateway-Bereitstellungen in Zusammenhang stehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkeinstellungen oder -ressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden** : Microsoft verwaltet die Zeitquelle für Azure-Ressourcen wie Azure App Service.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden** : Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mithilfe der Daten aus Azure-Aktivitätsprotokollen können Sie für jeden Schreibvorgang (PUT, POST, DELETE) bestimmen, wer was wann getan hat. Diese Schreibvorgänge müssen auf Steuerungsebene für Ihre Azure Application Gateway-Instanz und die dazugehörigen Ressourcen (die dem Schutz den Azure Application Gateway-Subnetzes dienen), z. B. NSGs, ausgeführt worden sein.

Zusätzlich zu den Aktivitätsprotokollen können Sie Diagnoseeinstellungen für Ihre Azure Application Gateway-Bereitstellungen konfigurieren. Diagnoseeinstellungen dienen dazu, den Export von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu streamen (Speicherkonten, Event Hubs und Log Analytics).

Azure Application Gateway verfügt auch über eine native Integration mit Azure Application Insights. Mit Application Insights werden Protokoll-, Leistungs- und Fehlerdaten erfasst. Application Insights erkennt automatisch Leistungsanomalien und verfügt über leistungsstarke Analysetools, mit denen Sie Probleme untersuchen und nachvollziehen können, wie Ihre Web-Apps verwendet werden. Sie können den fortlaufenden Export aktivieren, um Telemetriedaten aus Application Insights an einen zentralen Ort zu exportieren. So können Daten auch über den Standardaufbewahrungszeitraum hinaus aufbewahrt werden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

* [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md)

* [Konfigurieren des fortlaufenden Exports](../azure-monitor/app/export-telemetry.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden** : Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mithilfe der Daten aus Azure-Aktivitätsprotokollen können Sie für jeden Schreibvorgang (PUT, POST, DELETE) bestimmen, wer was wann getan hat. Diese Schreibvorgänge müssen auf Steuerungsebene für Ihre Azure Application Gateway-Instanz und die dazugehörigen Ressourcen (die dem Schutz den Azure Application Gateway-Subnetzes dienen), z. B. NSGs, ausgeführt worden sein.

Zusätzlich zu den Aktivitätsprotokollen können Sie Diagnoseeinstellungen für Ihre Azure Application Gateway-Bereitstellungen konfigurieren. Diagnoseeinstellungen dienen dazu, den Export von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu streamen (Speicherkonten, Event Hubs und Log Analytics).

Azure Application Gateway verfügt auch über eine native Integration mit Azure Application Insights. Mit Application Insights werden Protokoll-, Leistungs- und Fehlerdaten erfasst. Application Insights erkennt automatisch Leistungsanomalien und verfügt über leistungsstarke Analysetools, mit denen Sie Probleme untersuchen und nachvollziehen können, wie Ihre Web-Apps verwendet werden. Sie können den fortlaufenden Export aktivieren, um Telemetriedaten aus Application Insights an einen zentralen Ort zu exportieren. So können Daten auch über den Standardaufbewahrungszeitraum hinaus aufbewahrt werden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

* [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md)

* [Konfigurieren des fortlaufenden Exports](../azure-monitor/app/export-telemetry.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden** : Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden** : Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure Application Gateway-Instanz, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren.

Rufen Sie mit Azure Monitor für Netzwerke eine umfassende Ansicht der Integrität und der Metriken aller bereitgestellten Netzwerkressourcen auf, einschließlich Ihrer Azure Application Gateway-Instanzen.

Optional können Sie auch Azure Sentinel oder die SIEM-Lösung eines Drittanbieters aktivieren und dafür Daten integrieren.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

* [Azure Monitor für Netzwerke (Vorschau)](../azure-monitor/insights/network-insights-overview.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden** : Stellen Sie die v2-SKU der Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Die Web Application Firewall (WAF) ist ein Dienst (Feature von Azure Application Gateway), der zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und häufigen Sicherheitsrisiken bietet. Die Azure WAF trägt zum Schutz Ihrer Azure App Service-Web-Apps bei, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden. Die WAF basiert auf den Regeln der OWASP-Kernregelsätze (Open Web Application Security Project) der Versionen 3.1 (nur WAF_v2), 3.0 und 2.2.9.

Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure WAF, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren. Sie können Warnungen anhand von Abfragen Ihrer Log Analytics-Arbeitsbereiche erstellen.

Rufen Sie mit Azure Monitor für Netzwerke eine umfassende Ansicht der Integrität und der Metriken aller bereitgestellten Netzwerkressourcen auf, einschließlich Ihrer Azure Application Gateway-Instanzen. In der Konsole von Azure Monitor für Netzwerke können Sie Benachrichtigungen für Azure Application Gateway anzeigen und erstellen.

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

* [Azure Monitor für Netzwerke (Vorschau)](../azure-monitor/insights/network-insights-overview.md)

* [Reagieren auf Ereignisse mit Azure Monitor-Warnungen](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden** : Stellen Sie die Azure Web Application Firewall v2 (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Die Web Application Firewall (WAF) ist ein Dienst (Feature von Azure Application Gateway), der zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und häufigen Sicherheitsrisiken bietet. Die Azure WAF trägt zum Schutz Ihrer Azure App Service-Web-Apps bei, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden.

Konfigurieren Sie Diagnoseeinstellungen für Ihre Azure Application Gateway-Bereitstellungen. Diagnoseeinstellungen dienen dazu, den Export von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu streamen (Speicherkonten, Event Hubs und Log Analytics).

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden** : Nicht zutreffend. In Azure Application Gateway werden DNS-bezogene Protokolle, auf die Benutzer zugreifen können, weder verarbeitet noch generiert.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden** : Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden** : Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden** : Der Zugriff auf Steuerungsebene auf Azure Application Gateway wird über Azure Active Directory (AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden** : Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden** : Rufen Sie mithilfe einer Azure-App-Registrierung (Dienstprinzipal) ein Token ab, mit dem Sie über API-Aufrufe mit Ihren Azure Application Gateway-Instanzen interagieren können.

* [Aufrufen von Azure-REST-APIs mit Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrieren Ihrer Clientanwendung mit Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Recovery Services](/rest/api/recoveryservices/)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden** : Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden** : Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden** : Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

* [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

* [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden** : Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden** : Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. AAD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in AAD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

* [Erstellen und Konfigurieren einer AAD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden** : Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

* [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden** : Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden** : Mit Azure AD Identity Protection und Risikoschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen konfigurieren, die im Zusammenhang mit Benutzeridentitäten stehen. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden** : Nicht zutreffend. Die Kunden-Lockbox wird von Azure Application Gateway nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden** : Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden** : Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Stellen Sie sicher, dass alle Virtual Network-Azure Application Gateway-Bereitstellungen von Subnetzen einer Netzwerksicherheitsgruppe (NSG) zugeordnet wurden, deren Netzwerkzugriffssteuerungen für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. NSGs werden zwar in Azure Application Gateway unterstützt, es gibt jedoch einige Einschränkungen und Anforderungen, die befolgt werden müssen, damit Ihre NSG und Azure Application Gateway erwartungsgemäß funktionieren.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Application Gateway – Konfigurationsübersicht](./configuration-overview.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden** : Stellen Sie sicher, dass alle Virtual Network-Azure Application Gateway-Bereitstellungen von Subnetzen einer Netzwerksicherheitsgruppe (NSG) zugeordnet wurden, deren Netzwerkzugriffssteuerungen für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. Beschränken Sie ausgehenden Datenverkehr nur auf vertrauenswürdige Ziele, um das Risiko einer Datenexfiltration zu senken. NSGs werden zwar in Azure Application Gateway unterstützt, es gibt jedoch einige Einschränkungen und Anforderungen, die befolgt werden müssen, damit Ihre NSG und Azure Application Gateway erwartungsgemäß funktionieren.

* [Application Gateway – Konfigurationsübersicht](./configuration-overview.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden** : Konfigurieren Sie End-to-End-Verschlüsselung mit TLS für Ihre Azure Application Gateway-Instanzen.

* [Konfigurieren von End-to-End-TLS mit Application Gateway im Azure-Portal](./end-to-end-ssl-portal.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden** : Nicht zutreffend. Ruhende Kundendaten werden in Azure Application Gateway nicht gespeichert.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Application Gateway und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden** : Steuern Sie mit der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) den Zugriff auf die Steuerungsebene von Azure Application Gateway (Azure-Portal).

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden** : Nicht zutreffend. Kundendaten werden in Azure Application Gateway nicht gespeichert.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden** : Erstellen Sie mithilfe von Azure Monitor und dem Azure-Aktivitätsprotokoll Warnungen für den Fall, dass Änderungen an den Produktionsinstanzen von Azure Application Gateway und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden** : Noch nicht verfügbar. Die Beurteilung von Sicherheitsrisiken im Azure Security Center ist noch nicht für Azure Application Gateway verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Überprüfen Sie die für Azure Application Gateway verfügbaren Sicherheitsfunktionen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

* [Verfügbarkeit von Features in Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden** : Noch nicht verfügbar. Die Beurteilung von Sicherheitsrisiken im Azure Security Center ist noch nicht für Azure Application Gateway verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Überprüfen Sie die für Azure Application Gateway verfügbaren Sicherheitsfunktionen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

* [Verfügbarkeit von Features in Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden** : Noch nicht verfügbar. Die Beurteilung von Sicherheitsrisiken im Azure Security Center ist noch nicht für Azure Application Gateway verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Überprüfen Sie die für Azure Application Gateway verfügbaren Sicherheitsfunktionen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

* [Verfügbarkeit von Features in Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden** : Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden** : Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden** : Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden** : Definieren Sie zugelassene Azure-Ressourcen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden** : Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden** : Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden** : Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Stellen Sie sicher, dass alle Virtual Network-Azure Application Gateway-Bereitstellungen von Subnetzen einer Netzwerksicherheitsgruppe (NSG) zugeordnet wurden, deren Netzwerkzugriffssteuerungen für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. NSGs werden zwar in Azure Application Gateway unterstützt, es gibt jedoch einige Einschränkungen und Anforderungen, die befolgt werden müssen, damit Ihre NSG und Azure Application Gateway erwartungsgemäß funktionieren.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Application Gateway – Konfigurationsübersicht](./configuration-overview.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden** : Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer Azure Application Gateway-Bereitstellungen. Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Application Gateway- und Azure Virtual Network-Instanzen sowie Ihrer NSGs zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden** : Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden** : Wenn Sie benutzerdefinierte Azure-Richtliniendefinitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden** : Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden** : Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (überwachen), [deny] (verweigern) und [deploy if not existent] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden** : Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden** : Verwenden Sie verwaltete Identitäten, um Ihre Azure Application Gateway-Instanz mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Verwenden Sie Azure Key Vault, um Zertifikate sicher zu speichern. Azure Key Vault ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und SSL-Zertifikate schützen können. Azure Application Gateway unterstützt die Integration mit Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. Diese Unterstützung ist auf die Application Gateway v2-SKU beschränkt.

* [Konfigurieren der TLS-Terminierung mit Key Vault-Zertifikaten mithilfe von Azure PowerShell](./configure-keyvault-ps.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden** : Verwenden Sie verwaltete Identitäten, um Ihre Azure Application Gateway-Instanz mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Verwenden Sie Azure Key Vault, um Zertifikate sicher zu speichern. Azure Key Vault ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und SSL-Zertifikate schützen können. Azure Application Gateway unterstützt die Integration mit Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. Diese Unterstützung ist auf die Application Gateway v2-SKU beschränkt.

* [Konfigurieren der TLS-Terminierung mit Key Vault-Zertifikaten mithilfe von Azure PowerShell](./configure-keyvault-ps.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden** : Stellen Sie die Azure Web Application Firewall v2 (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Die Web Application Firewall (WAF) ist ein Dienst (Feature von Azure Application Gateway), der zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und häufigen Sicherheitsrisiken bietet. Die Azure WAF trägt zum Schutz Ihrer Azure App Service-Web-Apps bei, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden.

Konfigurieren Sie Diagnoseeinstellungen für Ihre Azure Application Gateway-Bereitstellungen. Diagnoseeinstellungen dienen dazu, den Export von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu streamen (Speicherkonten, Event Hubs und Log Analytics).

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](./application-gateway-diagnostics.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden** : Nicht zutreffend. Kundendaten werden in Azure Application Gateway nicht gespeichert.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Nicht verfügbar

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden** : Wenn Sie die Azure Web Application Firewall (WAF) verwenden, können Sie WAF-Richtlinien konfigurieren. Eine WAF-Richtlinie besteht aus zwei Arten von Sicherheitsregeln: benutzerdefinierte, vom Kunden erstellte Regeln und in Azure verwaltete, vorkonfigurierte Regelsätze. Von Azure verwaltete Regelsätze bieten eine einfache Möglichkeit zum Bereitstellen von Schutz vor allgemeinen Sicherheitsbedrohungen. Da Azure diese Regelsätze verwaltet, werden die Regeln zum Schutz vor neuen Angriffssignaturen aktualisiert, wenn dies erforderlich ist.

* [WAF-Richtlinie](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Shared

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden** : Kundendaten werden in Azure Application Gateway nicht gespeichert. Wenn Sie jedoch benutzerdefinierte Azure-Richtliniendefinitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Code sicher zu speichern und zu verwalten.

Für Azure DevOps Services werden viele Azure Storage-Features genutzt, um sicherzustellen, dass bei einem Hardwarefehler, einer Dienstunterbrechung oder einer regionalen Notfallsituation die Verfügbarkeit der Daten gewährleistet ist. Darüber hinaus wendet das Azure DevOps-Team Verfahren an, um Daten vor dem versehentlichen oder böswilligen Löschen zu schützen.

* [Datenverfügbarkeit](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden** : Sichern Sie die von Kunden verwalteten Zertifikate in Azure Key Vault.

* [Backup-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden** : Testen Sie die Wiederherstellung von gesicherten, von Kunden verwalteten Zertifikaten.

* [Restore-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Anleitung:** Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist. Vorläufiges Löschen ermöglicht die Wiederherstellung gelöschter Schlüsseltresore und Tresorobjekte, z. B. Schlüssel, Geheimnisse und Zertifikate.

* [Verwenden des vorläufigen Löschens in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden** : Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden** : Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden** : Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden** : Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden** : 

* [Befolgen Sie die Einsatzregeln von Microsoft, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).