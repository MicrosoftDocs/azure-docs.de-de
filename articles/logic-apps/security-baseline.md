---
title: Azure-Sicherheitsbaseline für Logic Apps
description: Die Logic Apps-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b625a81ff328e6760ec0ad8d9acb5735f402df73
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455629"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Azure-Sicherheitsbaseline für Logic Apps

Die Azure-Sicherheitsbaseline für Logic Apps enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Im „globalen“ mehrinstanzenfähigen Logic Apps-Dienst ausgeführte Connectors werden von Microsoft bereitgestellt und verwaltet. Diese Connectors stellen Auslöser und Aktionen für den Zugriff auf Clouddienste und lokale Systeme bereit, z. B. Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint u. a. Sie können das Diensttag AzureConnectors verwenden, wenn Sie Regeln für Ihre Netzwerksicherheitsgruppe oder Azure Firewall festlegen, um den Zugriff auf zusammengehörige Ressourcen zu erlauben.

Für Logik-Apps, die Direktzugriff auf Ressourcen in einem virtuellen Azure-Netzwerk benötigen, können Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE) erstellen, in der Sie Ihre Logik-Apps auf dedizierten Ressourcen erstellen, bereitstellen und ausführen können. Einige virtuelle Azure-Netzwerke verwenden private Endpunkte (Azure Private Link), um den Zugriff auf PaaS-Dienste von Azure wie Azure Storage, Azure Cosmos DB oder Azure SQL-Datenbank sowie auf Partner- oder auf Kundendienste zu ermöglichen, die in Azure gehostet werden. Falls Ihre Logik-Apps Zugriff auf virtuelle Netzwerke mit privaten Endpunkten benötigen, müssen diese Logik-Apps in einer ISE erstellt, bereitgestellt und ausgeführt werden.

Beim Erstellen Ihrer ISE können Sie auswählen, ob interne oder externe Zugriffsendpunkte verwendet werden sollen. Ihre Auswahl bestimmt, ob Anforderungs- oder Webhooktrigger für Logik-Apps in Ihrer ISE Aufrufe von außerhalb Ihres virtuellen Netzwerks empfangen können. Interne und externe Zugriffsendpunkte bestimmen auch, ob Sie den Ausführungsverlauf Ihrer Logik-App, einschließlich der Ein- und Ausgaben für eine Ausführung, sowohl innerhalb als auch außerhalb Ihres virtuellen Netzwerks einsehen können.

Stellen Sie sicher, dass alle Bereitstellungen von Subnetzen im virtuellen Netzwerk im Zusammenhang mit Ihrer ISE einer Netzwerksicherheitsgruppe mit Netzwerkzugriffssteuerungen zugeordnet wurden, die für die vertrauenswürdigen Ports und Quellen Ihrer Anwendung spezifisch sind. Wenn Sie Ihre Logik-Apps in einer ISE bereitstellen, verwenden Sie Azure Private Link. Mit Azure Private Link können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen. Bei einem spezifischen Anwendungsfall können Sie diese Anforderung alternativ durch Implementierung von Azure Firewall erfüllen. Um die Komplexität beim Festlegen von Sicherheitsregeln zu reduzieren, verwenden Sie Diensttags, die Gruppen von IP-Adresspräfixen für einen bestimmten Azure-Dienst darstellen.

- [Grundlegendes zu Connectors für Logic Apps](../connectors/apis-list.md)

- [Grundlegendes zu Diensttags in Azure](../virtual-network/service-tags-overview.md)

- [Grundlegendes zum Zugriff auf Ressourcen virtueller Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Grundlegendes zu VNET-Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Grundlegendes zu Azure Private Link](../private-link/private-link-overview.md)

- [Grundlegendes zum ISE-Endpunktzugriff](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Aktivieren des Zugriffs für die ISE](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Wenn Sie Logik-Apps in einer Integrationsdienstumgebung (ISE) ausführen, die einen externen Zugriffspunkt verwendet, können Sie mithilfe einer Netzwerksicherheitsgruppe (NSG) das Risiko der Datenexfiltration verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Grundlegendes zum ISE-Endpunktzugriff](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Wenn Ihre Logik-App einen anforderungsbasierten Trigger verwendet, der eingehende Aufrufe oder Anforderungen empfängt, z. B. den Trigger „Anforderung“ oder „Webhook“, können Sie den Zugriff so einschränken, dass nur autorisierte Clients die Logik-App aufrufen können.

Wenn Sie Logik-Apps in einer Integrationsdienstumgebung (ISE) ausführen, aktivieren Sie im zu Ihrer ISE gehörigen virtuellen Netzwerk DDoS Protection Standard, um sich vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation bereit, aktiviert Sie Threat Intelligence, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird.

Verwenden Sie den Just-in-Time-Netzwerkzugriff in Azure Security Center, um NSGs so zu konfigurieren, dass die Exposition von Endpunkten auf genehmigte IP-Adressen und einen begrenzten Zeitraum eingeschränkt wird.

Verwenden Sie die adaptive Netzwerkhärtung in Azure Security Center, um NSG-Konfigurationen zu empfehlen, die Ports und Quell-IPs basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken.

- [Absichern eingehender Aufrufe an Logic Apps](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Einschränken eingehender IP-Adressen](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

- [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Grundlegendes zur Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../security-center/security-center-just-in-time.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Wenn Sie Logik-Apps in einer Integrationsdienstumgebung (ISE) ausführen, die einen externen Zugriffspunkt verwendet, können Sie mithilfe einer Netzwerksicherheitsgruppe (NSG) das Risiko der Datenexfiltration verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Um weiteren Schutz und Informationen zum Netzwerkverkehr zu erhalten, können Sie auf Zugriffsprotokolle verweisen, die nur dann generiert werden, wenn Sie sie für jede Application Gateway-Instanz aktiviert haben. Mithilfe dieses Protokolls können Sie Application Gateway-Zugriffsmuster anzeigen und wichtige Informationen analysieren. Dazu gehören die IP des Aufrufers, die angeforderte URL, die Antwortlatenz, der Rückgabecode sowie die ein- und ausgehenden Bytes.

Andernfalls können Sie eine Marketplace-Lösung eines Drittanbieters nutzen, um diese Anforderung zu erfüllen.

- [Grundlegendes zum ISE-Endpunktzugriff](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Integrieren von API Management in ein internes VNET mit Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Grundlegendes zu WAF-Zugriffsprotokollen](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.  Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Wenn Sie Logik-Apps in einer Integrationsdienstumgebung (ISE) ausführen, stellen Sie Azure Application Gateway bereit.

- [Integrieren von API Management in ein internes VNET mit Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Konfigurieren von Application Gateway für die Verwendung von HTTPS](../application-gateway/create-ssl-portal.md) 

- [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](../application-gateway/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihre Azure Logic Apps-Instanzen benötigen, VNET-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. LogicApps, LogicAppsManagement) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Logic Apps-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces Microsoft.Logic und Microsoft.Network, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Logic Apps-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:

- In Logic Apps müssen Diagnoseprotokolle aktiviert sein

- DDoS Protection Standard sollte aktiviert sein.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Liste mit Azure Policy-Definitionen für Logic Apps](./policy-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Nutzen Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen im Zusammenhang mit Ihren Azure Logic Apps-Instanzen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle für Zeitstempel in den Protokollen, die für Azure-Ressourcen, z. B. Azure Logic Apps, verwendet wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Um während der Laufzeit umfangreichere Debuginformationen zu Ihren Logik-Apps zu erhalten, können Sie Azure Monitor-Protokolle einrichten und verwenden, um Informationen zu Laufzeitdaten und -ereignissen aufzuzeichnen und zu speichern, z. B. Auslöser-, Ausführungs- und Aktionsereignisse in einem Log Analytics-Arbeitsbereich. Azure Monitor hilft Ihnen bei der Überwachung Ihrer Cloud- und lokalen Umgebungen, sodass Sie deren Verfügbarkeit und Leistung einfacher sicherstellen können. Mithilfe von Azure Monitor-Protokollen können Sie Protokollabfragen erstellen, die Ihnen helfen, diese Informationen zu sammeln und zu überprüfen. Sie können diese Diagnosedaten auch mit anderen Azure-Diensten verwenden, z. B. Azure Storage und Azure Event Hubs.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Um während der Laufzeit umfangreichere Debuginformationen zu Ihren Logik-Apps zu erhalten, können Sie Azure Monitor-Protokolle einrichten und verwenden, um Informationen zu Laufzeitdaten und -ereignissen aufzuzeichnen und zu speichern, z. B. Auslöser-, Ausführungs- und Aktionsereignisse in einem Log Analytics-Arbeitsbereich. Azure Monitor hilft Ihnen bei der Überwachung Ihrer Cloud- und lokalen Umgebungen, sodass Sie deren Verfügbarkeit und Leistung einfacher sicherstellen können. Mithilfe von Azure Monitor-Protokollen können Sie Protokollabfragen erstellen, die Ihnen helfen, diese Informationen zu sammeln und zu überprüfen. Sie können diese Diagnosedaten auch mit anderen Azure-Diensten verwenden, z. B. Azure Storage und Azure Event Hubs.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Nachdem Sie eine Logik-App erstellt und ausgeführt haben, können Sie den Ausführungsstatus der Logik-App, den Ausführungsverlauf, den Triggerverlauf und die Leistung überprüfen. Für die Ereignisüberwachung in Echtzeit und die Durchführung eines umfassenderen Debuggens richten Sie die Diagnoseprotokollierung für Ihre Logik-App mithilfe der Azure Monitor-Protokolle ein. Dieser Azure-Dienst hilft Ihnen bei der Überwachung Ihrer Cloud- und lokalen Umgebungen, sodass Sie deren Verfügbarkeit und Leistung leichter sicherstellen können. Sie können dann Ereignisse suchen und anzeigen, z. B. Triggereignisse, Ausführungsereignisse und Aktionsereignisse. Durch das Speichern dieser Informationen in Azure Monitor-Protokollen können Sie Protokollabfragen erstellen, die Ihnen helfen, diese Informationen zu finden und zu analysieren. Sie können diese Diagnosedaten auch mit anderen Azure-Diensten verwenden, z. B. Azure Storage und Azure Event Hubs.

Legen Sie in Azure Monitor die Protokollaufbewahrungsdauer für Azure Logic Apps-Arbeitsbereiche, die Ihrer Azure Logic Apps-Instanz zugeordnet sind, gemäß den Compliancevorschriften Ihres Unternehmens fest.

- [Überwachen des Ausführungsstatus, Überprüfen des Auslöserverlaufs und Einrichten von Benachrichtigungen für Azure Logic Apps](monitor-logic-apps.md)

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Zum Einrichten der Protokollierung für Ihre Logik-App können Sie beim Erstellen Ihrer Logik-App Log Analytics aktivieren, oder Sie können die Logic Apps-Verwaltungslösung in Ihrem Log Analytics-Arbeitsbereich für vorhandene Logik-Apps installieren. Diese Lösung bietet aggregierte Informationen für Ihre Logik-App-Ausführungen und umfasst spezifische Details wie Status, Ausführungszeit, Status der erneuten Übermittlung und Korrelations-IDs. Um dann die Protokollierung zu aktivieren und Abfragen für diese Informationen zu erstellen, richten Sie Azure Monitor-Protokolle ein.

Sie können auch die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll aktivieren und die Protokolle an einen Log Analytics-Arbeitsbereich senden. Führen Sie Abfragen in Log Analytics durch, um Suchbegriffe zu ermitteln, Trends auszumachen, Muster zu analysieren und viele weitere Erkenntnisse auf Grundlage der Aktivitätsprotokolldaten zu gewinnen, die möglicherweise für Azure Logic Apps gesammelt wurden.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

- [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Erfassen und Analysieren von Azure-Aktivitätsprotokollen in Log Analytics in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center mit Log Analytics für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht umsetzbar. In Azure Logic Apps werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht umsetzbar. In Azure Logic Apps werden keine DNS-bezogenen Protokolle verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Zum mühelosen Zugreifen auf andere Ressourcen, die von Azure Active Directory (Azure AD) geschützt werden, und Authentifizieren Ihrer Identität ohne Anmeldung kann Ihre Logik-App eine verwaltete Identität (früher als verwaltete Dienstidentität, Managed Service Identity, MSI bezeichnet) anstelle von Anmeldeinformationen oder Geheimnissen verwenden. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen.

Jeder Anforderungsendpunkt einer Logik-App enthält in der URL des Endpunkts eine Shared Access Signature (SAS). Wenn Sie die Endpunkt-URL eines anforderungsbasierten Triggers für andere Parteien freigeben, können Sie Rückruf-URLs mit bestimmten Schlüsseln und Ablaufdaten generieren. So können Sie nahtlos rollierende Schlüssel bereitstellen oder den Zugriff für das Auslösen Ihrer Logik-App auf einen bestimmten Zeitraum einschränken.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](create-managed-service-identity.md)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Schützen des Zugriffs und der Daten in Azure Logic Apps mit SAS](logic-apps-securing-a-logic-app.md#sas)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: In Azure Active Directory und Azure Logic Apps gibt es nicht das Konzept von Standardkennwörtern.

Bei Verwendung der Standardauthentifizierung müssen Sie einen Benutzernamen und ein Kennwort angeben. Stellen Sie beim Erstellen dieser Anmeldeinformationen sicher, dass Sie für die Authentifizierung ein sicheres Kennwort konfigurieren.

Wenn Sie Infrastructure-as-Code nutzen, vermeiden Sie die Speicherung von Kennwörtern im Code. Verwenden stattdessen Azure Key Vault zum Speichern und Abrufen von Anmeldeinformationen.

- [Absichern von und Zugreifen auf Daten in Logic Apps](logic-apps-securing-a-logic-app.md)

- [Festlegen und Abrufen eines Geheimnisses aus Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie eine Azure-App-Registrierung (Dienstprinzipal), um ein Token abzurufen, das der Interaktion mit Ihren Recovery Services-Tresoren über API-Aufrufe dient.

Bei vielen Connectors müssen Sie zuerst eine Verbindung mit dem Zieldienst oder -system herstellen und Anmeldeinformationen zur Authentifizierung oder andere Konfigurationsdetails bereitstellen, bevor Sie einen Auslöser oder eine Aktion in Ihrer Logik-App verwenden können. Beispielsweise müssen Sie eine Verbindung mit einem Twitter-Konto autorisieren, damit Sie auf Daten zugreifen oder Beiträge in Ihrem Namen posten können.

Bei Connectors, die Azure AD-OAuth (Azure Active Directory) verwenden, bedeutet das Erstellen einer Verbindung die Anmeldung bei dem Dienst (z. B. Office 365, Salesforce oder GitHub), wobei das Zugriffstoken verschlüsselt und sicher in einem Azure-Geheimnisspeicher gespeichert wird. Andere Connectors (z.B. FTP und SQL) erfordern eine Verbindung mit Konfigurationsdetails wie Serveradresse, Benutzername und Kennwort. Diese Verbindungskonfigurationsdetails werden ebenfalls verschlüsselt und sicher gespeichert.

- [Aufrufen von Azure-REST-APIs mit Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrieren Ihrer Clientanwendung bei Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [API-Informationen zu Workflowauslösern](/rest/api/logic/workflowtriggers)

- [Grundlegendes zur Connectorkonfiguration](../connectors/apis-list.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

Jeder Anforderungsendpunkt einer Logik-App enthält außerdem in der URL des Endpunkts eine Shared Access Signature (SAS). Sie können Ihre Logik-App so einschränken, dass nur Anforderungen von bestimmten IP-Adressen akzeptiert werden.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Grundlegendes zum Einschränken eingehender IP-Adressen in Logic Apps](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie für Ihre Azure Logic Apps-Instanzen Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Sofern in Logic Apps unterstützt, verwenden Sie eine verwaltete Identität, um einfach auf andere Ressourcen zuzugreifen, die von Azure Active Directory (Azure AD) geschützt werden. Authentifizieren Sie Ihre Identität, ohne sich anzumelden, anstatt mit Anmeldeinformationen oder Geheimnissen. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen.

Azure Logic Apps unterstützt sowohl systemseitig zugewiesene als auch benutzerseitig zugewiesene verwaltete Identitäten. Ihre Logik-App kann entweder die vom System zugewiesene Identität oder eine einzelne vom Benutzer zugewiesene Identität (die Sie für eine Gruppe von Logik-Apps gemeinsam verwenden können) verwenden, aber nicht beide. Derzeit unterstützen nur spezifische integrierte Trigger und Aktionen verwaltete Identitäten, nicht aber verwaltete Connectors oder Verbindungen, z. B.:

-  HTTP

-  Azure-Funktionen

-  Azure API Management

-  Azure App Services 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Verwenden Sie für Ihre Azure Logic Apps-Instanzen Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten sowie auf Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration mit Azure Sentinel oder der SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen. 

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Zurzeit nicht verfügbar. Kunden-Lockbox wird für Azure Logic Apps noch nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Im „globalen“ mehrinstanzenfähigen Logic Apps-Dienst ausgeführte Connectors werden von Microsoft bereitgestellt und verwaltet. Diese Connectors stellen Auslöser und Aktionen für den Zugriff auf Clouddienste und lokale Systeme bereit, z. B. Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint u. a.

Für Logik-Apps, die Direktzugriff auf Ressourcen in einem virtuellen Azure-Netzwerk benötigen, können Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE) erstellen, in der Sie Ihre Logik-Apps auf dedizierten Ressourcen erstellen, bereitstellen und ausführen können. Einige virtuelle Azure-Netzwerke verwenden private Endpunkte (Azure Private Link), um den Zugriff auf Azure-PaaS-Dienste wie Azure Storage, Azure Cosmos DB oder Azure SQL-Datenbank sowie auf Partnerdienste oder auf Kundendienste zu ermöglichen, die in Azure gehostet werden. Falls Ihre Logik-Apps Zugriff auf virtuelle Netzwerke mit privaten Endpunkten benötigen, müssen diese Logik-Apps in einer ISE erstellt, bereitgestellt und ausgeführt werden.

Beim Erstellen Ihrer ISE können Sie auswählen, ob interne oder externe Zugriffsendpunkte verwendet werden sollen. Ihre Auswahl bestimmt, ob Anforderungs- oder Webhooktrigger für Logik-Apps in Ihrer ISE Aufrufe von außerhalb Ihres virtuellen Netzwerks empfangen können.

Implementieren Sie außerdem eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. Sie können die Zugriffsebene auf diejenigen Ihrer Azure-Ressourcen beschränken, die von Ihren Anwendungen und Unternehmensumgebungen gefordert werden. Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Azure-Zugriffssteuerung (Azure RBAC) regeln.

- [Grundlegendes zu Connectors für Logic Apps](../connectors/apis-list.md)

- [Zugreifen auf Ressourcen virtueller Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md) 

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md) 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Zurzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind noch nicht für Azure Logic Apps verfügbar.

Nutzen Sie eine Drittanbieterlösung von Azure Marketplace an Netzwerkperimetern, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt. 

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Logic Apps und hat strenge Steuerungen implementiert, um Verluste oder Offenlegung von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. In Azure Logic Apps werden alle Daten bei der Ausführung einer Logik-App während der Übertragung mithilfe von TLS (Transport Layer Security) sowie im Ruhezustand verschlüsselt. Wenn Sie den Ausführungsverlauf Ihrer Logik-App anzeigen, authentifiziert Azure Logic Apps Ihren Zugriff und stellt dann Links zu den Ein- und Ausgaben für die Anforderungen und Antworten für jede Ausführung bereit. Bei Aktionen, die Kennwörter, Geheimnisse, Schlüssel oder andere sensible Informationen verarbeiten, sollten Sie jedoch verhindern, dass andere Personen diese Daten einsehen und darauf zugreifen. Wenn Ihre Logik-App beispielsweise ein Geheimnis aus Azure Key Vault erhält, das bei der Authentifizierung einer HTTP-Aktion verwendet werden soll, sollten Sie dieses Geheimnis ausblenden.

Der Anforderungsauslöser unterstützt für eingehende Aufrufe ausschließlich Transport Layer Security (TLS) 1.2. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure-Ressourcen verbinden, TLS 1.2 oder höher aushandeln können. Den HTTP-Connector verwendende ausgehende Aufrufe unterstützen Transport Layer Security (TLS) 1.0, 1.1 und 1.2. 

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

- [Schützen des Zugriffs und der Daten in Azure Logic Apps – Zugriff auf anforderungsbasierte Trigger für eingehende Aufrufe](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Schützen des Zugriffs und der Daten in Azure Logic Apps – Zugriff auf Dienste und Systeme, die von Logik-Apps aus aufgerufen werden](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Grundlegendes zur Verschlüsselung ruhender Daten mit Azure](../security/fundamentals/encryption-atrest.md)

- [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: In Azure Logic Apps verfügen viele Auslöser und Aktionen über Einstellungen, die Sie aktivieren können, um Eingaben, Ausgaben oder beides zu schützen, indem Sie diese Daten im Ausführungsverlauf einer Logik-App verschleiern.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Logic Apps und hat strenge Steuerungen implementiert, um Verluste oder Offenlegung von Kundendaten zu verhindern.

- [Absichern des Zugriffs auf Ausführungsverlaufsdaten](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Sie können nur bestimmten Benutzern oder Gruppen erlauben, bestimmte Aufgaben auszuführen, wie z. B. das Verwalten, Bearbeiten und Anzeigen von Logik-Apps. Um die jeweiligen Berechtigungen zu steuern, verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, RBAC), damit Sie den Mitgliedern in Ihrem Azure-Abonnement benutzerdefinierte oder integrierte Rollen zuweisen können:

- Logik-App-Mitwirkender: Ermöglicht Ihnen die Verwaltung von Logik-Apps, aber nicht die Änderung des App-Zugriffs.
- Logik-App-Operator: Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps, die Sie aber nicht bearbeiten oder aktualisieren können.

Um zu verhindern, dass andere Personen Ihre Logik-App ändern oder löschen, können Sie Azure-Ressourcensperren verwenden. Diese Funktion verhindert, dass andere Personen Produktionsressourcen ändern oder löschen.

- [Schützen des Zugriffs auf Azure Logic Apps-Vorgänge](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Logic Apps und hat strenge Steuerungen implementiert, um Verluste oder Offenlegung von Kundendaten zu verhindern.

- [Schutz der Azure-Kundendaten](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Logic Apps nutzt Azure Storage zum Speichern und automatischen Verschlüsseln von ruhenden Daten. Diese Verschlüsselung schützt Ihre Daten und unterstützt Sie beim Einhalten der Sicherheits- und Complianceanforderungen Ihrer Organisation. Azure Storage verwendet standardmäßig von Microsoft verwaltete Schlüssel, um Ihre Daten zu verschlüsseln.

Wenn Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE) zum Hosten Ihrer Logik-Apps erstellen und mehr Kontrolle über die von Azure Storage verwendeten Verschlüsselungsschlüssel haben möchten, können Sie mit Azure Key Vault Ihren eigenen Schlüssel einrichten, verwenden und verwalten. Diese Funktion wird auch als „Bring Your Own Key“ (BYOK) bezeichnet, und Ihr Schlüssel wird als „vom Kunden verwalteter Schlüssel“ bezeichnet.

- [Verschlüsseln ruhender Daten für Integrationsdienstumgebungen in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen in Azure Logic Apps und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Nicht zutreffend, da Microsoft Sicherheitsrisiken auf den zugrunde liegenden Systemen verwaltet, die Azure Logic Apps unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend, da Microsoft Sicherheitsrisiken auf den zugrunde liegenden Systemen verwaltet, die Azure Logic Apps unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend, da Microsoft Sicherheitsrisiken auf den zugrunde liegenden Systemen verwaltet, die Azure Logic Apps unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln.  Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

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

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Legen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß Ihren organisatorischen Anforderungen an.

Hinweis: Aufgrund der Daten- und Datenschutzrichtlinien von Google können Sie den Gmail-Connector nur mit von Google genehmigten Diensten nutzen. Diese Situation ist in Bewegung und könnte sich in Zukunft auf andere Google-Connectors auswirken.

- [Liste aller Logic Apps-Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Grundlegendes zu Problemen mit und Einschränkungen für Gmail-Connectors](/connectors/gmail/#known-issues-and-limitations)

- [Weitere Informationen zur Datenschutzrichtlinie von Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken. 

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Ressourcen im Zusammenhang mit Ihren Logik-Apps, die für den Geschäftsbetrieb erforderlich sind, aber ein höheres Risiko für das Unternehmen darstellen können, sollten innerhalb des eigenen virtuellen Computers und/oder virtuellen Netzwerks isoliert und entweder mit einer Azure Firewall oder einer Netzwerksicherheitsgruppe ausreichend abgesichert werden.

Logik-Apps, die für den Geschäftsbetrieb erforderlich sind, aber ein höheres Risiko für die Organisation darstellen können, sollten nach Möglichkeit mittels gesonderter Ressourcengruppen mit spezifischen Berechtigungen und Azure RBAC-Grenzen isoliert werden.

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md) 

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md) 

- [Absichern des Zugriffs auf Logic Apps über Azure RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure Logic Apps-Instanzen mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace Microsoft.Logic, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Logic Apps-Instanz zu erstellen. Sie können z. B. andere daran hindern, Verbindungen mit Ressourcen herzustellen oder zu verwenden, für die Sie den Zugriff einschränken möchten.

Außerdem bietet Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen.

Verwenden Sie ferner abgesicherte Parameter, um sensible Daten und Geheimnisse zu schützen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Blockieren der von Connectors in Azure Logic Apps erstellten Verbindungen](block-connections-connectors.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Bereitstellen von Azure Resource Manager-Vorlagen für Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Grundlegendes zu sicheren Aktionsparametern](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Sicherheitsempfehlungen für Parameter](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure Logic Apps-Instanzen mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace Microsoft.Logic, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Logic Apps-Instanz zu erstellen. Sie können z. B. andere daran hindern, Verbindungen mit Ressourcen herzustellen oder zu verwenden, für die Sie den Zugriff einschränken möchten.

Außerdem bietet Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen.

Stellen Sie außerdem sicher, dass Daten im Ausführungsverlauf mittels Obfuskation abgesichert werden.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Blockieren der von Connectors in Azure Logic Apps erstellten Verbindungen](block-connections-connectors.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Bereitstellen von Azure Resource Manager-Vorlagen für Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Absichern des Zugriffs auf Ein- und Ausgaben von Ausführungsverläufen](logic-apps-securing-a-logic-app.md#obfuscate)

- [Absichern des Zugriffs auf Parametereingaben](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Sicherheitsempfehlungen für Parameter](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

Außerdem bietet Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace Microsoft.Logic, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure-Ressourcen zu erstellen. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace Microsoft.Logic, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Auswirkungen [Audit], [Deny] und [DeployIfNotExists], um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Sichern Sie Ein- und Ausgaben im Ausführungsverlauf von Logik-Apps mittels Obfuskation ab. Bei Bereitstellungen in verschiedenen Umgebungen sollten Sie die Werte in der Workflowdefinition Ihrer Logik-App parametrisieren, die je nach Umgebung variieren. Auf diese Weise können Sie hartcodierte Daten vermeiden, indem Sie eine Azure Resource Manager-Vorlage verwenden, um Ihre Logikanwendung bereitzustellen, sensible Daten durch die Definition abgesicherter Parameter zu schützen und diese Daten als separate Eingaben durch die Parameter der Vorlage mithilfe einer Parameterdatei zu übergeben. Mit Key Vault können Sie sensible Daten speichern und abgesicherte Vorlagenparameter verwenden, die diese Werte bei der Bereitstellung aus Key Vault abrufen. Anschließend können Sie auf den Schlüsseltresor und Geheimnisse in Ihrer Parameterdatei verweisen. 

Wenn Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE) zum Hosten Ihrer Logik-Apps erstellen und mehr Kontrolle über die von Azure Storage verwendeten Verschlüsselungsschlüssel haben möchten, können Sie mit Azure Key Vault Ihren eigenen Schlüssel einrichten, verwenden und verwalten. Diese Funktion wird auch als „Bring Your Own Key“ (BYOK) bezeichnet, und Ihr Schlüssel wird als „vom Kunden verwalteter Schlüssel“ bezeichnet.

- [Absichern von Ein- und Ausgaben im Ausführungsverlauf von Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Sicherheitsempfehlungen für Parameter](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Absichern des Zugriffs auf Parametereingaben in Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Übergeben sicherer Parameterwerte während der Bereitstellung mithilfe von Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Zum mühelosen Zugreifen auf andere Ressourcen, die von Azure Active Directory (Azure AD) geschützt werden, und Authentifizieren Ihrer Identität ohne Anmeldung kann Ihre Logik-App eine verwaltete Identität (früher als verwaltete Dienstidentität, Managed Service Identity, MSI bezeichnet) anstelle von Anmeldeinformationen oder Geheimnissen verwenden. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen.

Derzeit unterstützen nur spezifische integrierte Trigger und Aktionen verwaltete Identitäten, nicht aber verwaltete Connectors oder Verbindungen, z. B.:

- HTTP
- Azure-Funktionen
- Azure API Management
- Azure App Services

- [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Sichern Sie Ein- und Ausgaben im Ausführungsverlauf von Logik-Apps mittels Obfuskation ab. Bei Bereitstellungen in verschiedenen Umgebungen sollten Sie die Werte in der Workflowdefinition Ihrer Logik-App parametrisieren, die je nach Umgebung variieren. Auf diese Weise können Sie hartcodierte Daten vermeiden, indem Sie eine Azure Resource Manager-Vorlage verwenden, um Ihre Logikanwendung bereitzustellen, sensible Daten durch die Definition abgesicherter Parameter zu schützen und diese Daten als separate Eingaben durch die Parameter der Vorlage mithilfe einer Parameterdatei zu übergeben. Mit Key Vault können Sie sensible Daten speichern und abgesicherte Vorlagenparameter verwenden, die diese Werte bei der Bereitstellung aus Key Vault abrufen. Anschließend können Sie auf den Schlüsseltresor und Geheimnisse in Ihrer Parameterdatei verweisen. 

Sie können auch Credential Scanner implementieren, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Absichern von Ein- und Ausgaben im Ausführungsverlauf von Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Sicherheitsempfehlungen für Parameter](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Absichern des Zugriffs auf Parametereingaben in Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Übergeben sicherer Parameterwerte während der Bereitstellung mithilfe von Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Logic Apps) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host aktiviert, der die Azure-Dienste (z. B. Azure Backup) unterstützt. Das Feature wird jedoch nicht für Ihre Inhalte ausgeführt. 

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage usw. 

Mithilfe der Bedrohungserkennung für Datendienste in Azure Security Center können Sie Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde. 

- [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

- [Grundlegendes zur Bedrohungserkennung für Datendienste in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Implementieren Sie eine Lösung zur Notfallwiederherstellung (Disaster Recorvery, DR), sodass Sie Daten schützen, die Ressourcen zur Unterstützung wichtiger Geschäftsfunktionen schnell wiederherstellen und den Betrieb aufrechterhalten können, um die Geschäftskontinuität (Business Continuity, BC) zu gewährleisten.

Bei dieser Notfallwiederherstellungsstrategie wird die primäre Logik-App für ein Failover an eine Standby- oder Sicherungs-Logik-App an einem alternativen Standort eingerichtet, an dem auch Azure Logic Apps verfügbar ist. Auf diese Weise kann die sekundäre Datenbank die Aufgaben übernehmen, wenn die primäre Datenbank Verluste, Unterbrechungen oder Ausfälle erleidet. Diese Strategie erfordert, dass Ihre sekundäre Logik-App und abhängige Ressourcen am alternativen Standort bereits bereitgestellt wurden und bereit sind.

Zusätzlich sollten Sie die Ihrer Logik-App zugrunde liegende Workflowdefinition zu einer Azure Resource Manager-Vorlage erweitern. In dieser Vorlage sind die Infrastruktur, die Ressourcen, die Parameter und weitere Informationen für die Bereitstellung Ihrer Logik-App definiert.

- [Weitere Informationen zu Business Continuity & Disaster Recovery für Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatisieren der Bereitstellung für Azure Logic Apps durch Verwenden von Azure Resource Manager-Vorlagen](logic-apps-azure-resource-manager-templates-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Implementieren Sie eine Lösung zur Notfallwiederherstellung (Disaster Recorvery, DR), sodass Sie Daten schützen, die Ressourcen zur Unterstützung wichtiger Geschäftsfunktionen schnell wiederherstellen und den Betrieb aufrechterhalten können, um die Geschäftskontinuität (Business Continuity, BC) zu gewährleisten.

Bei dieser Notfallwiederherstellungsstrategie wird die primäre Logik-App für ein Failover an eine Standby- oder Sicherungs-Logik-App an einem alternativen Standort eingerichtet, an dem auch Azure Logic Apps verfügbar ist. Auf diese Weise kann die sekundäre Datenbank die Aufgaben übernehmen, wenn die primäre Datenbank Verluste, Unterbrechungen oder Ausfälle erleidet. Diese Strategie erfordert, dass Ihre sekundäre Logik-App und abhängige Ressourcen am alternativen Standort bereits bereitgestellt wurden und bereit sind.

Zusätzlich sollten Sie die Ihrer Logik-App zugrunde liegende Workflowdefinition zu einer Azure Resource Manager-Vorlage erweitern. In dieser Vorlage sind die Infrastruktur, die Ressourcen, die Parameter und weitere Informationen für die Bereitstellung Ihrer Logik-App definiert.

Jeder Anforderungsendpunkt einer Logik-App enthält in der URL des Endpunkts eine Shared Access Signature (SAS). Wenn Sie Azure Key Vault zur Speicherung Ihrer Geheimnisse verwenden, stellen Sie regelmäßige automatische Sicherungen Ihrer Schlüssel und URLs sicher.

- [Weitere Informationen zu Business Continuity & Disaster Recovery für Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatisieren der Bereitstellung für Azure Logic Apps durch Verwenden von Azure Resource Manager-Vorlagen](logic-apps-azure-resource-manager-templates-overview.md)

- [Schützen des Zugriffs und der Daten in Azure Logic Apps mit SAS](logic-apps-securing-a-logic-app.md#sas)

- [Sichern von Key Vault-Schlüsseln](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Bei dieser Notfallwiederherstellungsstrategie wird die primäre Logik-App für ein Failover an eine Standby- oder Sicherungs-Logik-App an einem alternativen Standort eingerichtet, an dem auch Azure Logic Apps verfügbar ist. Auf diese Weise kann die sekundäre Datenbank die Aufgaben übernehmen, wenn die primäre Datenbank Verluste, Unterbrechungen oder Ausfälle erleidet. Diese Strategie erfordert, dass Ihre sekundäre Logik-App und abhängige Ressourcen am alternativen Standort bereits bereitgestellt wurden und bereit sind.

Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln. Beachten Sie, dass dies nur für Logik-Apps gilt, die in Integrationsdienstumgebungen (ISE) ausgeführt werden.

- [Weitere Informationen zu Business Continuity & Disaster Recovery für Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Bei dieser Notfallwiederherstellungsstrategie wird die primäre Logik-App für ein Failover an eine Standby- oder Sicherungs-Logik-App an einem alternativen Standort eingerichtet, an dem auch Azure Logic Apps verfügbar ist. Auf diese Weise kann die sekundäre Datenbank die Aufgaben übernehmen, wenn die primäre Datenbank Verluste, Unterbrechungen oder Ausfälle erleidet. Diese Strategie erfordert, dass Ihre sekundäre Logik-App und abhängige Ressourcen am alternativen Standort bereits bereitgestellt wurden und bereit sind. 

Schützen Sie gesicherte kundenseitig verwaltete Schlüssel. Beachten Sie, dass dies nur für Logik-Apps gilt, die in Integrationsdienstumgebungen (ISE) ausgeführt werden.

Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.

- [Weitere Informationen zu Business Continuity & Disaster Recovery für Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. 

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand. 

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).