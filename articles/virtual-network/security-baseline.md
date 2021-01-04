---
title: Azure-Sicherheitsbaseline für Virtual Network
description: Die Sicherheitsbaseline für Virtual Network enthält Schritt-für-Schritt-Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Vergleichstest für die Azure-Sicherheit.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 36be61fd65db7ea02a3baec4b519a13231c420ec
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514440"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Azure-Sicherheitsbaseline für Virtual Network

Diese Sicherheitsbaseline wendet Empfehlungen der [Version 1.0 des Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview-v1.md) auf Azure Virtual Network an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Vergleichstest für die Azure-Sicherheit und die entsprechenden, für Azure Virtual Network geltenden Empfehlungen definiert werden. Nicht auf Azure Virtual Network anwendbare **Steuerungen** wurden ausgeschlossen.

Die gesamte Zuordnung zwischen Azure Virtual Network und dem Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Virtual Network-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden** : Verwenden Sie Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Netzwerkressourcen in Azure abzusichern. 

Senden Sie Flussprotokolle von Netzwerksicherheitsgruppen an einen Log Analytics-Arbeitsbereich, und verwenden Sie Traffic Analytics, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu erhalten. Traffic Analytics bietet die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln. 

Verwenden Sie Azure Monitor-Protokolle, um Einblicke in Ihre Umgebung zu geben. Es sollte ein Arbeitsbereich verwendet werden, um die Daten zu sortieren und zu analysieren. Zudem können Sie andere Azure-Dienste wie Application Insights und Security Center integrieren. 

Wählen Sie Ressourcenprotokolle aus, um sie an ein Azure-Speicherkonto oder einen Event Hub zu senden. Zum Analysieren der Protokolle kann auch eine andere Plattform verwendet werden. 

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Grundlegendes zu der von Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden** : Aktivieren Sie den DDoS-Standardschutz (Distributed Denial of Service) in den virtuellen Azure-Netzwerken, um sie vor DDoS-Angriffen zu schützen.

Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation mit aktivierter, auf Threat Intelligence basierender Filterung bereit, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird.

Verwenden Sie Bedrohungsschutzfunktionen von Azure Security Center, um die Kommunikation mit bekannten schädlichen IP-Adressen zu erkennen.

Wenden Sie die Empfehlungen zur adaptiven Netzwerkhärtung in Azure Security Center an, um Netzwerksicherheitsgruppenkonfigurationen zu empfehlen, die Ports und IP-Quelladressen basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken. 

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](manage-ddos-protection.md)

- [Threat Intelligence-gestütztes Filtern für Azure Firewall](../firewall/threat-intel.md)

- [Bedrohungsschutz in Security Center](/azure/security-center/threat-protection)

- [Adaptive Netzwerkhärtung in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden** : Verwenden Sie zusätzlich zu den gängigen Tools für die Paketerfassung eine VPN Gateway-Paketerfassung. 

Darüber hinaus können Sie Agent-basierte Lösungen oder NVA-Lösungen in Betracht ziehen, die über unsere im Azure Marketplace verfügbaren Paketbroker-Partnerlösungen Funktionen für Terminalzugangspunkt (Terminal Access Point, TAP) oder Netzwerksichtbarkeit bieten.

- [Konfigurieren der Paketerfassung für VPN-Gateways](../vpn-gateway/packet-capture.md) 

- [Partner für virtuelles Netzwerkgerät](https://azure.microsoft.com/solutions/network-appliances)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden** : Verwenden Sie eine in Ihrem virtuellen Netzwerk bereitgestellte Azure Firewall mit aktivierter Threat Intelligence. Verwenden Sie auf Azure Firewall-Threat Intelligence basierende Filterung, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. 

Sie können auch ein passendes Angebot aus Azure Marketplace auswählen, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden** : Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Diensttags können anstelle von spezifischen IP-Adressen verwendet werden, wenn Sie Sicherheitsregeln erstellen. Lassen Sie den Datenverkehr für den entsprechenden Dienst zu, oder verweigern Sie ihn, wenn Sie den Diensttagnamen (z. B. ApiManagement) im entsprechenden Feld „Quelle“ oder „Ziel“ einer Regel angeben. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Verwenden Sie Anwendungssicherheitsgruppen, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren. Dies gibt Ihnen die Möglichkeit, virtuelle Computer zu gruppieren und basierend auf diesen Gruppen Netzwerksicherheitsregeln zu definieren.

- [Grundlegendes zu Diensttags und deren Verwendung](service-tags-overview.md)

- [Grundlegendes zu Anwendungssicherheitsgruppen und deren Verwendung](/azure/virtual-network/security-overview#application-security-groups)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden** : Definieren und implementieren Sie Standard-Sicherheitskonfigurationen für Netzwerkressourcen mit Azure Policy, und überprüfen Sie die integrierten Netzwerkrichtlinien-Definitionen auf ihre Implementierung.

Informieren Sie sich mithilfe der Standardrichtlinie für Security Center, die verfügbare Sicherheitsempfehlungen enthält, die sich auf Ihre virtuellen Netzwerke beziehen.

Verwenden Sie Azure Blueprints, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem Sie wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, Zuweisungen für rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) und Richtlinien gemeinsam in einer Blaupausendefinition packen. Azure-Blaupausen können auf neue Abonnements angewendet werden und Steuerung und Verwaltung durch Versionsverwaltung optimieren. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network) 

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

- [Aktivieren der Überwachung in Azure Security Center](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden** : Verwenden Sie Tags für Netzwerksicherheitsgruppen und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie das Feld „Beschreibung“, um geschäftliche Anforderungen, Dauer und weitere Informationen für Regeln anzugeben, die Datenverkehr in ein oder aus einem Netzwerk für einzelne Regeln von Netzwerksicherheitsgruppen zulassen.
Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Wählen Sie Azure PowerShell oder die Azure CLI, um basierend auf ihren Tags nach Ressourcen zu suchen oder Aktionen für diese Ressourcen auszuführen.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Erstellen eines virtuellen Netzwerks](quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden** : Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und zum Erkennen von Änderungen an Ihrem virtuellen Netzwerk. Erstellen Sie in Azure Monitor Warnungen, die bei Änderungen an wichtigen Ressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](/azure/azure-monitor/platform/activity-log-view)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden** : Aktivieren Sie Azure Monitor für den Zugriff auf Ihre Überwachungs- und Aktivitätsprotokolle, was Ereignisquelle, Datum, Benutzer, Zeitstempel, Quell- und Zieladressen sowie andere nützliche Elemente einschließt. 

Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung.
Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](/azure/azure-monitor/platform/activity-log-view)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden** : Aktivieren Sie Azure Monitor für den Zugriff auf Ihre Überwachungs- und Aktivitätsprotokolle, was Ereignisquelle, Datum, Benutzer, Zeitstempel, Quell- und Zieladressen sowie andere nützliche Elemente einschließt.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](/azure/azure-monitor/platform/activity-log-view)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden** : Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung bei der Aufbewahrung von Sicherheitsprotokollen.

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden** : Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie Log Analytics-Arbeitsbereiche von Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung. 

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

- [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/get-started-portal.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden** : Verwenden Sie Security Center mit einem Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung zur Benachrichtigung aktivieren und integrieren.

- [Verwalten von Warnungen in Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden** : Implementieren Sie eine Drittanbieterlösung aus Azure Marketplace für die DNS-Protokollierungslösung entsprechend den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden** : Verwenden Sie die integrierten Administratorrollen von Azure Active Directory, die explizit zugewiesen werden und Abfragen ausführen können. 

Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden** : Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Security Center, um die Anzahl der Administratorkonten zu überwachen.

Aktivieren Sie Just-in-Time- oder Just-Enough-Zugriff, indem Sie mit Azure AD Privileged Identity Management verwaltete privilegierte Rollen für Microsoft-Dienste und Azure Resource Manager verwenden. 

- [Weitere Informationen zu Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden** : Verwenden Sie SSO mit Azure Active Directory (Azure AD), anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Verwenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung von Security Center.

- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden** : Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden** : Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung von und den Zugriff auf Azure-Netzwerkressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden** : Nutzen Sie die Risikoerkennung von Azure Active Directory, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen. 

Erfassen Sie die Risikoerkennungswarnungen von Security Center an Azure Monitor, und konfigurieren Sie mithilfe von Aktionsgruppen benutzerdefinierte Warnungen oder Benachrichtigungen.

- [Grundlagen der Risikoerkennung in Security Center (verdächtige Aktivitäten)](/azure/active-directory/reports-monitoring/concept-risk-events) 

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Konfigurieren von Aktionsgruppen für benutzerdefinierte Warnungen und Benachrichtigungen](../azure-monitor/platform/action-groups.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden** : Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden** : Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Dienste. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und in der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.  

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden** : Verwenden Sie Azure Active Directory (Azure AD), um Protokolle zum Ermitteln von veralteten Konten bereitzustellen. 

Zugriffsüberprüfungen für Azure-Identitäten können durchgeführt werden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff sollte regelmäßig überprüft werden, um sicherzustellen, dass nur aktive Benutzer weiterhin Zugriff haben.

- [Grundlegendes zur Azure AD-Berichterstellung](/azure/active-directory/reports-monitoring/)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden** : Integrieren Sie ausgehend von Ihrem Zugriff Protokollquellen für Azure AD-Anmeldeaktivitäten (Azure Active Directory), Überwachung und Risikoereignisse in beliebige SIEM- oder Überwachungstools. 

Optimieren Sie diesen Prozess, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Im Log Analytics-Arbeitsbereich können alle gewünschten Benachrichtigungen konfiguriert werden.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden** : Verwenden Sie die Risiko- und Identitätsschutzfeatures von Azure AD, um automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten für Ihr virtuelles Netzwerk zu konfigurieren. Erfassen Sie Daten zur weiteren Untersuchung in Azure Sentinel.

- [Anzeigen riskanter Azure AD-Anmeldungen](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden** : Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die Verbindungen mit Azure-Ressourcen in Ihren virtuellen Netzwerken herstellen, TLS 1.2 oder höher verwenden können. Befolgen Sie die Empfehlungen von Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung. 

Microsoft bietet mehrere Optionen, die von Kunden zum Schutz von Daten während der Übertragung intern innerhalb des Azure-Netzwerks und extern über das Internet bis zum Endbenutzer genutzt werden können. Hierzu gehören die Kommunikation über virtuelle private Netzwerke (mit IPsec-/IKE-Verschlüsselung), TLS 1.2 (Transport Layer Security) oder höher (über Azure-Komponenten wie Application Gateway oder Azure Front Door), direkt auf den virtuellen Azure-Computern vorhandene Protokolle (etwa Windows IPsec oder SMB) und vieles mehr.

Darüber hinaus ist für den gesamten Azure-Datenverkehr zwischen Azure-Rechenzentren die „standardmäßige Verschlüsselung“ mit MACsec (ein IEEE-Standard in der Sicherungsschicht) aktiviert, um Vertraulichkeit und Integrität von Kundendaten sicherzustellen.

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Shared

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Zugriffs auf Ressourcen 

**Leitfaden** : Verwalten Sie den Zugriff auf Daten und Ressourcen mithilfe der rollenbasierten Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC). Verwenden Sie andernfalls dienstspezifische Methoden zur Zugriffssteuerung. 

Wählen Sie integrierte Rollen wie Besitzer, Mitwirkender oder Netzwerkmitwirkender, und weisen Sie die Rolle dem entsprechenden Bereich zu. Beispielsweise können Sie jeder dieser Rollen eine Teilmenge der virtuellen Netzwerkfunktionen mit den für virtuelle Netzwerke erforderlichen spezifischen Berechtigungen zuweisen. 

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Planen virtueller Netzwerke](virtual-network-vnet-plan-design-arm.md#permissions)

- [Überprüfen der in Azure integrierten Rollen](../role-based-access-control/built-in-roles.md#networking)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure Monitor mit den Azure-Aktivitätsprotokollen, um Warnungen zu erstellen, die bei Änderungen an wichtigen Azure-Ressourcen ausgegeben werden, wie Virtual Networks und Netzwerksicherheitsgruppen.

- [Diagnoseprotokollierung für eine Netzwerksicherheitsgruppe](virtual-network-nsg-manage-log.md)

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden** : Verwenden Sie Azure Resource Graph zum Abfragen und Ermitteln aller Netzwerkressourcen wie Virtual Networks und Subnetzen in Ihren Abonnements. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md) 

- [Anzeigen Ihrer Azure-Abonnements](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden** : Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden** : Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements, um Ihr virtuelles Netzwerk und die zugehörigen Ressourcen zu organisieren und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](/azure/billing/billing-create-subscription) 

- [Erstellen von Verwaltungsgruppen](/azure/governance/management-groups/create) 

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden** : Sie müssen einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß Ihren organisatorischen Anforderungen erstellen.

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen) 

- Zulässige Ressourcentypen 

Fragen Sie in Umgebungen mit hoher Sicherheit, etwa solchen mit Azure-Speicherkonten, mit Azure Resource Graph Ressourcen innerhalb der Abonnements ab, oder ermitteln Sie sie. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden** : Verhindern Sie die Erstellung oder Verwendung von Ressourcen mithilfe von Azure Policy nach Maßgabe der Unternehmensrichtlinien. Implementieren Sie Prozesse für das Entfernen nicht autorisierter Ressourcen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden** : Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen) 

- Zulässige Ressourcentypen 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden** : Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die „Microsoft Azure Management“ App konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure-Netzwerkressourcen zu erstellen, und verwenden Sie außerdem die integrierten Azure Policy-Definitionen.

Exportieren Sie beliebige Ihrer Buildvorlagen mit Azure Resource Manager in JSON-Form (JavaScript Object Notation), und überprüfen Sie sie, um sicherzustellen, dass die Konfigurationen den Sicherheitsanforderungen Ihrer Organisation mindestens entsprechen oder sie übertreffen.

Implementieren Sie Empfehlungen von Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen.

- [Anzeigen verfügbarer Azure Policy-Aliase](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure Resource Manager-Vorlagen und Azure-Richtlinien, um Azure-Ressourcen sicher zu konfigurieren, die dem virtuellen Netzwerk und den damit zusammenhängenden Ressourcen zugeordnet sind.  Azure Resource Manager-Vorlagen sind JSON-basierte (JavaScript Object Notation) Dateien, die zum Bereitstellen von virtuellen Computern mit Azure-Ressourcen verwendet werden. Microsoft führt die Verwaltung der Basisvorlagen durch.  

Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um durchgängig sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

- [Erstellen von Azure Resource Manager-Vorlagen](../virtual-machines/windows/ps-template.md) 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Azure Resource Manager-Vorlagenbeispiele für virtuelle Netzwerke](template-samples.md)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden** : Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten, z. B. benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen, Skripts für die Desired State Configuration. usw.

Sie müssen über Berechtigungen für den Zugriff auf die in Azure DevOps verwalteten Ressourcen wie Code, Builds und Arbeitsnachverfolgungen verfügen. Die meisten Berechtigungen werden über integrierte Sicherheitsgruppen erteilt. Sie können bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure AD definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit Team Foundation Server) Berechtigungen erteilen oder verweigern.

- [Speichern von Code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden** : Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zum Überwachen oder Durchsetzen der Netzwerkkonfiguration Ihrer Azure-Ressourcen und aller integrierten Richtliniendefinitionen im Zusammenhang mit bestimmten Ressourcen zu erstellen. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Aliase](../governance/policy/concepts/definition-structure.md#aliases)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden** : Verwenden Sie Security Center, um Baselineüberprüfungen Ihres Azure Virtual Networks und der zugehörigen Ressourcen durchzuführen. Nutzen Sie Azure Policy, um Azure-Ressourcenkonfigurationen zu überwachen und ggf. zu benachrichtigen.

- [Umsetzen von Empfehlungen in Security Center](../security-center/security-center-remediate-recommendations.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Integrierte Azure Policy-Beispiele für virtuelle Netzwerke](/azure/virtual-network/policy-samples)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden** : Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Ihre in einem Azure Virtual Network gehosteten Azure-Ressourcen zu vereinfachen und zu schützen.

- [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Erstellen einer Key Vault-Instanz](/azure/key-vault/quick-create-portal) 

- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](/azure/key-vault/managed-identity)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden** : Verwenden Sie Azure Resource Manager, um ein virtuelles Netzwerk und die zugehörigen Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von virtuellen Netzwerken und zugehörigen Ressourcen verwendet werden können.  Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen.

- [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-Vorlagenbeispiele für virtuelle Netzwerke](template-samples.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

- [Einführung in Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung** : Zurzeit nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden** : Verwenden Sie Azure Resource Manager, um ein virtuelles Netzwerk und die zugehörigen Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von virtuellen Netzwerken und zugehörigen Ressourcen verwendet werden können. Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

- [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-Vorlagenbeispiele für virtuelle Netzwerke](template-samples.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

- [Einführung in Azure Automation](../automation/automation-intro.md)

- [Sichern von Schlüsseltresorschlüsseln in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden** : Führen Sie regelmäßig Bereitstellungen von Azure Resource Manager-Vorlagen in einem isolierten Abonnement durch, und überprüfen Sie die Wiederherstellung der gesicherten von Kunden verwalteten Schlüssel.

- [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden** : Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Dazu zählen z. B. benutzerdefinierte Azure Policy-Definitionen und Azure Resource Manager-Vorlagen. 

Erteilen oder verweigern Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure AD definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit Team Foundation Server) Berechtigungen.  

Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um kundenseitig verwaltete Schlüssel zu schützen.   

Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.  

- [Speichern von Code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Vorläufiges Löschen für Azure Storage-Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.  

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie Abonnements deutlich mithilfe von Tags (z. B. Produktion oder Nicht-Produktion), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren. Dies ist insbesondere für die Verarbeitung vertraulicher Daten wichtig.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden** : Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden** : Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Sicherheitskontaktinformationen in Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden** : Exportieren Sie Ihre Warnungen und Empfehlungen von Security Center mithilfe des Features Continuous Export, um Risiken für Azure-Ressourcen zu identifizieren. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. 

Sie können auch den Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung** : Ja

**Verantwortlichkeit** : Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden** : Verwenden Sie das Workflowautomatisierungsfeature in Security Center, um die Reaktion automatisch mithilfe von „Logic Apps“ für Sicherheitswarnungen und Empfehlungen auszulösen und Ihre Azure-Ressourcen zu schützen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden** : Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung** : Nicht verfügbar

**Verantwortlichkeit** : Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](/azure/security/benchmarks/overview).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).
