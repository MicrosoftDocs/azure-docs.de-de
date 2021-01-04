---
title: Azure-Sicherheitsbaseline für Service Fabric
description: Die Service Fabric-Sicherheitsbaseline enthält Anleitungen und Ressourcen für die Vorgehensweise bei der Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9eab908d2767bcdc9305daa625c70be9b63b9756
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485545"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Azure-Sicherheitsbaseline für Service Fabric

Die Azure-Sicherheitsbaseline für Service Fabric enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).



## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Stellen Sie sicher, dass auf alle Virtual Network-Subnetzbereitstellungen eine Netzwerksicherheitsgruppe mit Netzwerkzugriffssteuerungen angewendet wurde, die für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind.

* [Bereitstellen von Azure Firewall mit einer Vorlage](../firewall/deploy-template.md)

* [Verwenden von Netzwerkisolation und -sicherheit mit Azure Service Fabric](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [Netzwerkmuster für Service Fabric](./service-fabric-patterns-networking.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und setzen Sie die Empfehlungen zum Netzwerkschutz für das virtuelle Netzwerk, das Subnetz und die Netzwerksicherheitsgruppe um, die für den Schutz Ihres Azure Service Fabric-Clusters genutzt werden. Aktivieren Sie NSG-Flussprotokolle (Netzwerksicherheitsgruppe), und senden Sie Protokolle an ein Azure Storage-Konto, um den Datenverkehr zu überwachen. Sie können NSG-Flussprotokolle auch an einen Azure Log Analytics-Arbeitsbereich senden und Azure Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Azure Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics](../network-watcher/traffic-analytics.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Stellen Sie ein Front-End-Gateway bereit, um für Benutzer, Geräte oder andere Anwendungen einen zentralen Eingangspunkt bereitzustellen. Azure API Management kann direkt mit Service Fabric integriert werden. So können Sie den Zugriff auf Back-End-Dienste schützen, DOS-Angriffe durch Drosselung verhindern und API-Schlüssel, JWT-Tokens, Zertifikate und andere Anmeldeinformationen überprüfen.

Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Aktivieren Sie die Diagnoseeinstellung für WAF, und erfassen Sie Protokolle in ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich.

* [Service Fabric mit Azure API Management-Übersicht](./service-fabric-api-management-overview.md)

* [Integrieren von API Management in ein internes VNET mit Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie als Schutz vor DDoS-Angriffen den Azure-DDoS-Standardschutz für das virtuelle Netzwerk, in dem Ihr Azure Service Fabric-Cluster bereitgestellt wurde. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

* [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie NSG-Flussprotokolle für die NSG (Netzwerksicherheitsgruppe), die an das Subnetz angefügt wurde, mit dem Ihr Azure Service Fabric-Cluster geschützt wird. Erfassen Sie die NSG-Flussprotokolle in einem Azure Storage-Konto, um Flussdatensätze zu generieren. Aktivieren Sie die Azure Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

* [Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt. Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Stellen Sie Azure Application Gateway für Webanwendungen mit aktiviertem HTTPS/SSL für vertrauenswürdige Zertifikate bereit.

* [Bereitstellen von Application Gateway](../application-gateway/quick-create-portal.md)

* [Konfigurieren von Application Gateway für die Verwendung von HTTPS](../application-gateway/create-ssl-portal.md)

* [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](../application-gateway/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen (NSGs) zu definieren, die an das Subnetz angefügt sind, in dem Ihr Azure Service Fabric-Cluster bereitgestellt wurde. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

* [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

* [Netzwerk](./service-fabric-best-practices-networking.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkressourcen Ihres Azure Service Fabric-Clusters. Verwenden Sie Azure Policy-Aliasse in den Namespaces „Microsoft.ServiceFabric“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihres Azure Service Fabric-Clusters zu erstellen.

Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Kontrollen und Richtlinien in einer einzigen Blaupausendefinition packen. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss, die Ihrem Azure Service Fabric-Cluster zugeordnet sind. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (CLI) verwenden, um Ressourcen anhand ihrer Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Service Fabric-Bereitstellungen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure Service Fabric-Clusterkomponenten. Sie können die Zeitsynchronisierung für Ihre Computebereitstellungen aktualisieren.

* [Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen](../virtual-machines/windows/time-sync.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Sie können für Ihren Azure Service Fabric-Cluster das Onboarding für Azure Monitor durchführen, um die vom Cluster generierten Sicherheitsdaten zu aggregieren. Sehen Sie sich hierzu Beispiele für Diagnoseprobleme und die entsprechenden Lösungen mit Service Fabric an.

* [Einrichten von Azure Monitor-Protokollen für einen Cluster](./service-fabric-diagnostics-oms-setup.md)

* [Tutorial: Überwachen von Windows-Containern unter Service Fabric mit Azure Monitor-Protokollen](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Diagnostizieren häufiger Szenarios mit Service Fabric](./service-fabric-diagnostics-common-scenarios.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Azure Monitor für den Service Fabric-Cluster, und verweisen Sie dafür auf einen Log Analytics-Arbeitsbereich. Hierdurch werden dann alle relevanten Clusterinformationen und Betriebssystemmetriken für alle Azure Service Fabric-Clusterknoten protokolliert.

* [Einrichten von Azure Monitor-Protokollen für einen Cluster](./service-fabric-diagnostics-oms-setup.md)

* [Tutorial: Überwachen von Windows-Containern unter Service Fabric mit Azure Monitor-Protokollen](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Leistungsüberwachung mit Azure Monitor-Protokollen](./service-fabric-diagnostics-oms-agent.md)

* [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Führen Sie ein Onboarding für den Azure Service Fabric-Cluster in Azure Monitor durch. Stellen Sie sicher, dass für den verwendeten Log Analytics-Arbeitsbereich der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.

* [Einrichten von Azure Monitor-Protokollen für einen Cluster](./service-fabric-diagnostics-oms-setup.md)

* [Tutorial: Überwachen von Windows-Containern unter Service Fabric mit Azure Monitor-Protokollen](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Leistungsüberwachung mit Azure Monitor-Protokollen](./service-fabric-diagnostics-oms-agent.md)

* [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Führen Sie ein Onboarding für den Azure Service Fabric-Cluster in Azure Monitor durch. Stellen Sie sicher, dass für den verwendeten Log Analytics-Arbeitsbereich der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.

* [Einrichten von Azure Monitor-Protokollen für einen Cluster](./service-fabric-diagnostics-oms-setup.md)

* [Tutorial: Überwachen von Windows-Containern unter Service Fabric mit Azure Monitor-Protokollen](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Leistungsüberwachung mit Azure Monitor-Protokollen](./service-fabric-diagnostics-oms-agent.md)

* [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Verwenden Sie Abfragen für den Azure Log Analytics-Arbeitsbereich, um Azure Service Fabric-Protokolle abzufragen.

* [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie den Azure Log Analytics-Arbeitsbereich zum Überwachen von und Warnen vor anomalen Aktivitäten in Sicherheitsprotokollen und -ereignissen Ihres Azure Service Fabric-Clusters.

* [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Standardmäßig ist Windows Defender unter Windows Server 2016 installiert. Sehen Sie sich in der Dokumentation zu Ihrer Antischadsoftware-Anwendung die Konfigurationsregeln an, falls Sie Windows Defender nicht nutzen. Windows Defender wird unter Linux nicht unterstützt.

* Weitere Informationen finden Sie unter [Windows Defender Antivirus auf Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016).

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung für die DNS-Protokollierung.

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Führen Sie die manuelle Konfiguration der Konsolenprotokollierung jeweils für die einzelnen Knoten durch.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie den Datensatz für das lokale Administratorkonto, das während der Clusterbereitstellung des Azure Service Fabric-Clusters erstellt wird, sowie für alle anderen von Ihnen erstellten Konten. Wenn außerdem die Azure AD-Integration verwendet wird, verfügt Azure AD über integrierte Rollen, die explizit zugewiesen werden müssen und daher abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten durchzuführen, die Mitglieder von Verwaltungsgruppen sind.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Beim Bereitstellen eines Clusters müssen Sie in Azure neue Kennwörter für das Webportal erstellen. Es müssen keine Standardkennwörter geändert werden, aber Sie können unterschiedliche Kennwörter für den Webportalzugriff angeben.

* [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](./service-fabric-cluster-creation-via-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Integrieren Sie die Authentifizierung für Azure Service Fabric mit Azure Active Directory. Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

* [Einrichten der Azure Active Directory-Clientauthentifizierung](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

* [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie PAWs (Privileged Access Workstation) mit MFA (mehrstufige Authentifizierung), die für die Anmeldung und Konfiguration Ihrer Azure Service Fabric-Cluster und der zugehörigen Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

* [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem, um den Zugriff auf Verwaltungsendpunkte von Azure Service Fabric-Clustern zu schützen. AAD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in AAD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

* [Erstellen und Konfigurieren einer AAD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Einrichten von Azure Active Directory für die Clientauthentifizierung](./service-fabric-cluster-creation-setup-aad.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Verwenden Sie die Azure Active Directory-Authentifizierung (AAD) mit Ihrem Azure Service Fabric-Cluster. AAD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie AAD-Anmelde- und Überwachungsprotokolle (Azure Active Directory), um zu überwachen, ob versucht wird, auf deaktivierte Konten zuzugreifen. Diese Protokolle können in alle SIEM-/Überwachungstools von Drittanbietern integriert werden.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für AAD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Azure Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht verfügbar; Kunden-Lockbox wird für Azure Service Fabric noch nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für Ressourcen Ihrer Azure Service Fabric-Cluster als Hilfe beim Nachverfolgen von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein VNET/Subnetz getrennt, ordnungsgemäß gekennzeichnet und durch eine NSG oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden. Implementieren Sie für VMs, auf denen vertrauliche Informationen gespeichert oder verarbeitet werden, Richtlinien und Verfahren, mit denen Sie sie bei Nichtverwendung deaktivieren können.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von „Warnen“ oder „Warnen und ablehnen“ mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Stellen Sie ein automatisiertes Tool an den Netzwerkperimetern bereit, das die nicht autorisierte Übertragung von sensiblen Informationen überwacht und derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Microsoft hat eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung, um die Sicherheit von Kundendaten innerhalb von Azure zu

gewährleisten.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure-Ressourcen verbinden, TLS 1.2 oder höher aushandeln können.

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

* [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Szenarien für die Clustersicherheit in Service Fabric](./service-fabric-cluster-security.md)

* [TLS-Konfiguration](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Kennzeichnen Sie den Cluster und die zugehörigen Ressourcen mit Tags als vertraulich, wenn mit Azure Service Fabric-Clustern vertrauliche Informationen gespeichert oder verarbeitet werden. Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verwenden Sie die Verschlüsselung ruhender Daten für alle Azure-Ressourcen. Microsoft empfiehlt, Azure die Verwaltung Ihrer Verschlüsselungsschlüssel zu erlauben. Es gibt jedoch in einigen Fällen die Möglichkeit, ihre eigenen Schlüssel zu verwalten.

* [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

* [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault über das Azure-Portal](../storage/common/customer-managed-keys-configure-key-vault.md)

* [Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Windows](./service-fabric-enable-azure-disk-encryption-windows.md)

* [Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Linux](./service-fabric-enable-azure-disk-encryption-linux.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an wichtigen Azure-Ressourcen ausgegeben werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Führen Sie Service Fabric Fault Analysis Service und Chaosdienste regelmäßig aus, um Fehler im gesamten Cluster zu simulieren, um die Stabilität und Zuverlässigkeit Ihrer Dienste bewerten zu können.

Befolgen Sie die Empfehlungen aus dem Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre Azure-VMs und Containerimages.

Verwenden Sie für die Durchführung von Sicherheitsrisikobewertungen für Netzwerkgeräte und Webanwendungen eine Drittanbieterlösung. Verwenden Sie bei der Durchführung von Remote-Scans kein einzelnes, unbefristetes Administratorkonto. Ziehen Sie die Implementierung der JIT-Bereitstellungsmethodik für das Scan-Konto in Erwägung. Anmeldeinformationen für das Scan-Konto sollten geschützt, überwacht und nur für die Überprüfung von Sicherheitsrisiken verwendet werden.

* [Einführung in den Fault Analysis Service](./service-fabric-testability-overview.md)

* [Auslösen von kontrolliertem Chaos in Service Fabric-Clustern](./service-fabric-controlled-chaos.md)

* [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Aktivieren Sie automatische Upgrades für Betriebssystemimages in den VM-Skalierungsgruppen Ihres Azure Service Fabric-Clusters.

Verwenden Sie alternativ den manuellen Trigger für Betriebssystemimageupgrades Ihrer Skalierungsgruppe, um Betriebssystempatches zu testen, bevor sie in der Produktion verwendet werden. Beachten Sie, dass die Option eines manuellen Triggers kein integriertes Rollback bietet. Überwachen Sie Betriebssystempatches mithilfe der Updateverwaltung in Azure Automation.

* [Konfiguration des automatischen Betriebssystemupgrades für virtuelle Azure-Computer](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Aktualisieren von virtuellen Computern auf das aktuelle Skalierungsgruppenmodell](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Übersicht über die Updateverwaltung](../automation/update-management/overview.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Aktivieren Sie automatische Upgrades für Betriebssystemimages in den VM-Skalierungsgruppen Ihres Azure Service Fabric-Clusters. Patch Orchestration Application (POA) ist eine alternative Lösung für Service Fabric-Cluster, die außerhalb von Azure gehostet werden. POA kann mit Azure-Clustern verwendet werden. Dafür fällt allerdings etwas zusätzlicher Hostingaufwand an.

* [Konfiguration des automatischen Betriebssystemupgrades für virtuelle Azure-Computer](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Patchen des Windows-Betriebssystems in Ihrem Service Fabric-Cluster](./service-fabric-patch-orchestration-application.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Exportieren Sie die Scanergebnisse in regelmäßigen Abständen und vergleichen Sie die Ergebnisse, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn Sie die vom Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwenden, können Sie in das Portal der ausgewählten Lösung wechseln, um die historischen Scandaten anzuzeigen.

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie ein gängiges Risikobewertungsprogramm (z. B. Common Vulnerability Scoring System) oder die von Ihrem Scantool eines Drittanbieters bereitgestellten Standardrisikobewertungen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Definieren Sie genehmigte Azure-Ressourcen und genehmigte Software für Computeressourcen.

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

Not allowed resource types (Unzulässige Ressourcentypen)

Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um Clusterknoten auf nicht genehmigte Softwareanwendungen zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle), einschließlich Azure Service Fabric-Cluster, in Ihren Abonnements abzufragen bzw. zu ermitteln. Entfernen Sie alle nicht genehmigten Azure-Ressourcen, die Sie finden. Implementieren Sie für Azure Service Fabric-Clusterknoten eine Drittanbieterlösung, um nicht genehmigte Software zu entfernen bzw. entsprechende Warnungen zu generieren.

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Implementieren Sie für Azure Service Fabric-Clusterknoten eine Drittanbieterlösung, um zu verhindern, dass nicht autorisierte Software ausgeführt wird.

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um einzuschränken, welche Dienste in Ihrer Umgebung bereitgestellt werden können.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Implementieren Sie für Azure Service Fabric-Clusterknoten eine Drittanbieterlösung, um zu verhindern, dass Dateien eines nicht autorisierten Typs ausgeführt werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Verwenden Sie betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern, um die Möglichkeiten der Benutzer einzuschränken, Skripts innerhalb der Azure-Computeressourcen auszuführen.

* [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Software, die für den Geschäftsbetrieb erforderlich ist, aber ein höheres Risiko für das Unternehmen darstellen kann, sollte innerhalb des eigenen virtuellen Computers und/oder virtuellen Netzwerks isoliert und entweder mit einer Azure Firewall oder einer Netzwerksicherheitsgruppe ausreichend gesichert werden.

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.ServiceFabric“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihres Service Fabric-Clusters zu erstellen.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Betriebssystemimages von Azure Service Fabric werden von Microsoft verwaltet und gewartet. Der Kunde ist verantwortlich für die Implementierung sicherer Konfigurationen für das Betriebssystem Ihrer Clusterknoten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Richtlinien „[deny]“ (Verweigern) und „[deploy if not exist]“ (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure Service Fabric-Cluster und die zugehörigen Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Betriebssystemimages von Azure Service Fabric-Clustern werden von Microsoft verwaltet und gewartet. Der Kunde ist verantwortlich für die Implementierung der Zustandskonfiguration auf Betriebssystemebene.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Wenn benutzerdefinierte Images verwendet werden, sollten Sie durch die Nutzung der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) sicherstellen, dass nur autorisierte Benutzer auf die Images zugreifen können. Speichern Sie Containerimages in Azure Container Registry, und verwenden Sie Azure RBAC, um sicherzustellen, dass nur autorisierte Benutzer auf die Images zugreifen können.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Grundlegendes zu Azure RBAC für Container Registry](../container-registry/container-registry-roles.md)

* [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.ServiceFabric“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.ServiceFabric“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihres Service Fabric-Clusters zu erstellen.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen für Betriebssystem- und Docker-Einstellungen für Container durchzuführen.

* [Grundlegendes zu Azure Security Center-Containerempfehlungen](../security-center/container-security.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

* [Verwenden verwalteter Identitäten für Azure mit Service Fabric](./concepts-managed-identity.md)

* [Konfigurieren von Unterstützung der verwalteten Identität für einen neuen Service Fabric-Cluster](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [Nutzen der verwalteten Identität einer Service Fabric-Anwendung für den Zugriff auf Azure-Dienste](./how-to-managed-identity-service-fabric-app-code.md)

* [KeyVaultReference-Unterstützung für Service Fabric-Anwendungen (Vorschauversion)](./service-fabric-keyvault-references.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwaltete Identitäten können in von Azure bereitgestellten Service Fabric-Clustern verwendet werden sowie für Anwendungen, die als Azure-Ressourcen bereitgestellt wurden. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Verwenden verwalteter Identitäten für Azure mit Service Fabric](./concepts-managed-identity.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Bei Verwendung von Code zu Ihrer Azure Service Fabric-Bereitstellung können Sie Credential Scanner implementieren, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Verwenden Sie Azure Key Vault, um Service Fabric-Clusterzertifikate automatisch zu rotieren.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Rotation für Zertifikate](./cluster-security-certificate-management.md#certificate-rotation)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Standardmäßig ist Windows Defender Antivirus unter Windows Server 2016 installiert. Die Benutzeroberfläche wird bei einigen SKUs standardmäßig installiert, aber dies ist keine erforderliche Komponente.

Sehen Sie sich in der Dokumentation zu Ihrer Antischadsoftware-Anwendung die Konfigurationsregeln an, falls Sie Windows Defender nicht nutzen. Windows Defender wird unter Linux nicht unterstützt.

* Weitere Informationen finden Sie unter [Windows Defender Antivirus auf Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016).

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind. Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Service Fabric) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für andere Ressourcen als Computeressourcen bestimmt, die für die Speicherung von Daten ausgelegt sind. Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Service Fabric) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Der Sicherungs- und Wiederherstellungsdienst in Service Fabric ermöglicht die einfache und automatische Sicherung von Informationen, die in zustandsbehafteten Diensten gespeichert sind. Die regelmäßige Sicherung von Anwendungsdaten ist von grundlegender Bedeutung für den Schutz vor Datenverlust und Nichtverfügbarkeit von Diensten. Service Fabric bietet einen optionalen Dienst für Sicherungen und Wiederherstellungen, mit dem Sie die regelmäßige Sicherung der statusbehafteten zuverlässigen Dienste (einschließlich der Actordienste) konfigurieren können, ohne zusätzlichen Code schreiben zu müssen. Damit wird auch das Wiederherstellen der zuvor erstellten Sicherungen vereinfacht.

* [Regelmäßiges Sichern und Wiederherstellen in einem Azure Service Fabric-Cluster](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Aktivieren Sie einen Dienst für die Wiederherstellung von Sicherungen in Ihrem Service Fabric-Cluster, und erstellen Sie Sicherungsrichtlinien, um zustandsbehaftete Dienste bei Bedarf regelmäßig zu sichern. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

* [Regelmäßiges Sichern und Wiederherstellen in einem Azure Service Fabric-Cluster](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [Grundlegendes zur Konfiguration der regelmäßigen Sicherung in Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie die Durchführungsfähigkeit für die Wiederherstellung über den Wiederherstellungsdienst für Sicherungen sicher, indem Sie regelmäßig die Informationen zur Sicherungskonfiguration und zu den verfügbaren Sicherungen überprüfen. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

* [Grundlegendes zur Konfiguration der regelmäßigen Sicherung in Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Wiederherstellen von Sicherungsdaten in Azure Service Fabric](./service-fabric-backup-restore-service-trigger-restore.md)

* [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Bei Sicherungen über den Dienst zum Wiederherstellen von Sicherungen in Service Fabric verwenden Sie ein Azure Storage-Konto Ihres Abonnements. Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Zur Schaffung einer zusätzlichen Kontrollmöglichkeit für Verschlüsselungsschlüssel können Sie vom Kunden verwaltete Schlüssel bereitstellen, mit denen Speicherdaten verschlüsselt werden können.

Wenn Sie vom Kunden verwaltete Schlüssel verwenden, sollten Sie sicherstellen, dass in Key Vault das vorläufige Löschen aktiviert ist, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

* [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md)

* [Aktivieren des vorläufigen Löschens in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle in Ihrer Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Abonnements außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

* [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [Leitfaden des National Institute of Standards and Technology (NIST) für das Testen, Training und Ausführen von Programmen für IT-Pläne und -Funktionen](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

* [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).