---
title: Azure-Sicherheitsbaseline für Azure Container Registry
description: Azure-Sicherheitsbaseline für Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ccb287a63cf372ff78c750a4690188de7ccc59e6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807936"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure-Sicherheitsbaseline für Azure Container Registry

Die Azure-Sicherheitsbaseline für Azure Container Registry enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Das virtuelle Azure-Netzwerk (Azure Virtual Network) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Indem Sie den Zugriff auf Ihre private Azure-Containerregistrierung nur von einem virtuellen Azure-Netzwerk aus zulassen, gewährleisten Sie, dass nur Ressourcen im virtuellen Netzwerk Zugriff auf die Registrierung haben. Bei standortübergreifenden Szenarios können Sie außerdem Firewallregeln konfigurieren, um den Zugriff auf die Registrierung nur von bestimmten IP-Adressen zuzulassen. Konfigurieren Sie hinter einer Firewall Firewallzugriffsregeln und Diensttags für den Zugriff auf Ihre Containerregistrierung.

Beschränken Sie den Zugriff auf eine Azure-Containerregistrierung mithilfe eines virtuellen Azure-Netzwerks oder mit Firewallregeln: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Konfigurieren Sie Regeln für den Zugriff auf eine Azure-Containerregistrierung hinter einer Firewall: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Netzwerkressourcen in Azure abzusichern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Schützen von Netzwerkressourcen: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Nicht zutreffend Der Benchmark ist für Azure App Service- oder Computeressourcen vorgesehen, die Webanwendungen hosten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie den DDoS-Standardschutz für Ihre virtuellen Netzwerke, um Schutz vor DDoS-Angriffen bereitzustellen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.  Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation bereit, aktiviert Sie Threat Intelligence, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird.

Sie können den Just-in-Time-Netzwerkzugriff in Azure Security Center verwenden, um Netzwerksicherheitsgruppen so zu konfigurieren, dass die Exposition von Endpunkten auf genehmigte IP-Adressen und einen begrenzten Zeitraum eingeschränkt wird. Verwenden Sie außerdem die adaptive Netzwerkhärtung in Azure Security Center, um NSG-Konfigurationen zu empfehlen, die Ports und Quell-IPs basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken.

Konfigurieren von DDoS-Schutz: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Bereitstellen von Azure Firewall: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Grundlegendes zu integrierten Informationen zu Bedrohungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Aktivieren Sie NSG-Flussprotokolle für die NSG (Netzwerksicherheitsgruppe), die an das Subnetz angefügt wurde, mit dem Ihre Azure-Containerregistrierung geschützt wird. Sie können die NSG-Flussprotokolle in einem Azure Storage-Konto erfassen, um Flussdatensätze zu generieren. Aktivieren Sie die Azure Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren von Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt. Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

Azure Marketplace: https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Bereitstellen von Azure Firewall: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurieren von Warnungen mit Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht zutreffend Der Benchmark ist für Webanwendungen gedacht, die in Azure App Service oder in Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihre Containerregistrierung benötigen, Tags des virtuellen Netzwerkdiensts für den Azure Container Registry-Dienst, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen „AzureContainerRegistry“ im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Zulassen des Zugriffs nach Diensttag: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure-Containerregistrierungen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.ContainerRegistry“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure-Containerregistrierungen zu erstellen. 

Sie können Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelemente und Richtlinien in einer einzigen Blaupausendefinition verpackt werden. Sie können die Blaupause einfach auf neue Abonnements anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Der Kunde kann auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelemente und Richtlinien in einer einzigen Blaupausendefinition verpackt werden. Sie können die Blaupause einfach auf neue Abonnements anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Containerregistrierungen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben jedoch die Möglichkeit, die Zeitsynchronisierungseinstellungen für Ihre Computeressourcen zu verwalten.

Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von einer Azure-Containerregistrierung generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Azure Monitor erfasst Ressourcenprotokolle (früher als Diagnoseprotokolle bezeichnet) für benutzergesteuerte Ereignisse in Ihrer Registrierung. Erfassen und nutzen Sie diese Daten, um Registrierungsauthentifizierungsereignisse zu überwachen und einen vollständigen Aktivitätspfad für Registrierungsartefakte wie Pull- und Pushereignisse bereitzustellen, damit Sie Sicherheitsprobleme mit Ihrer Registrierung diagnostizieren können.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

Festlegen der Parameter für die Protokollaufbewahrung für Log Analytics-Arbeitsbereiche: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Azure Container Registry-Protokolle auf anormales Verhalten, und überprüfen Sie die Ergebnisse regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Grundlegendes zum Log Analytics-Arbeitsbereich: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Ausführen benutzerdefinierter Abfragen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Verwenden Sie den Azure Log Analytics-Arbeitsbereich zum Überwachen von und Warnen vor anomalen Aktivitäten in Sicherheitsprotokollen und -ereignissen Ihrer Azure-Containerregistrierung.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Warnungen bei Log Analytics-Protokolldaten: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend Azure Container Registry verarbeitet keine Protokolle zu Antischadsoftware und generiert auch keine solchen Protokolle.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend Azure Container Registry ist ein Endpunkt und initiiert keine Kommunikation. Der Dienst fragt DNS nicht ab.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (Azure AD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Verfolgen Sie für jede Azure-Containerregistrierung nach, ob das integrierte Administrator Konto aktiviert oder deaktiviert ist. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry-Administratorkonto: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory (Azure AD) verfolgt nicht das Konzept der Standardkennwörter. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

Wenn das Standardadministratorkonto einer Azure-Containerregistrierung aktiviert ist, werden komplexe Kennwörter automatisch erstellt und sollten rotiert werden. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

Azure Container Registry-Administratorkonto: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Erstellen Sie außerdem Prozeduren, um das integrierte Administratorkonto einer Containerregistrierung zu aktivieren. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

Grundlegendes zu Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry-Administratorkonto: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

Verwenden Sie für den individuellen Zugriff auf die Containerregistrierung eine individuelle Anmeldung, die in Azure Active Directory integriert ist.

Grundlegendes zu SSO mit Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Individuelle Anmeldung bei einer Containerregistrierung: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (Azure AD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center-Überwachung**: –

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

Erstellen und Konfigurieren einer AAD-Instanz: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

Grundlegendes zur Azure AD-Berichterstellung: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure Active Directory-Anmeldeaktivitäten (Azure AD) und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- (Security Information and Event Management) und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure Active Directory-Funktionen (Azure AD) zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. 

Anzeigen riskanter Azure AD-Anmeldungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht verfügbar. Kunden-Lockbox wird für Azure Container Registry zurzeit nicht unterstützt.

Liste der durch Kunden-Lockbox unterstützten Dienste: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Ressourcentags für die Nachverfolgung von Azure-Containerregistrierungen, die vertrauliche Informationen speichern oder verarbeiten.

Versehen Sie Containerimages oder andere Artefakte in einer Registrierung mit Tags und Versionskontrolle, und sperren Sie Images oder Repositorys, um das Nachverfolgen von Images zu unterstützen, die sensible Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Sperren von Containerimages in einer Azure-Containerregistrierung: https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Containerregistrierungen, Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden.

Ressourcen sollten durch ein virtuelles Netzwerk oder Subnetz getrennt, entsprechend gekennzeichnet und durch eine Netzwerksicherheitsgruppe (NSG) oder Azure Firewall gesichert werden.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Beschränken Sie den Zugriff auf eine Azure-Containerregistrierung mithilfe eines virtuellen Azure-Netzwerks oder mit Firewallregeln: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Erstellen einer NSG mit einer Sicherheitskonfiguration: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Bereitstellen von Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurieren von „Warnen“ oder „Warnen und ablehnen“ mit Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Stellen Sie ein automatisiertes Tool an den Netzwerkperimetern bereit, das die nicht autorisierte Übertragung von sensiblen Informationen überwacht und derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure Container Registry verbinden, TLS 1.2 oder höher aushandeln können. Für Microsoft Azure-Ressourcen wird standardmäßig die TLS 1.2-Aushandlung ausgeführt.

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

Grundlegendes zur Verschlüsselung während der Übertragung mit Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Container Registry noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie rollenbasierte Azure-Zugriffssteuerung (Azure RBAC), um den Zugriff auf Daten und Ressourcen in einer Azure-Containerregistrierung zu steuern. 

Konfigurieren von Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Rollen und Berechtigungen in Azure Container Registry: https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Wenn aus Compliancegründen für Computeressourcen erforderlich, implementieren Sie ein Drittanbietertool, z. B. eine automatisierte Lösung für die Verhinderung von Datenverlust, um Zugriffssteuerungen für Daten zu erzwingen, auch wenn Daten von einem System kopiert werden.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verwenden Sie die Verschlüsselung ruhender Daten für alle Azure-Ressourcen. Standardmäßig werden alle Daten in einer Azure-Containerregistrierung mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Grundlegendes zur Verschlüsselung ruhender Daten in Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Kundenseitig verwaltete Schlüssel in Azure Container Registry: https://aka.ms/acr/cmk



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Azure Monitor erfasst Ressourcenprotokolle (früher als Diagnoseprotokolle bezeichnet) für benutzergesteuerte Ereignisse in Ihrer Registrierung. Erfassen und nutzen Sie diese Daten, um Registrierungsauthentifizierungsereignisse zu überwachen und einen vollständigen Aktivitätspfad für Registrierungsartefakte wie Pull- und Pushereignisse bereitzustellen, damit Sie Betriebsprobleme mit Ihrer Registrierung diagnostizieren können.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Empfehlungen aus dem Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre Containerimages. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um Imagesicherheitsrisiken zu bewerten.

Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integration von Security Center in Azure Container Registry (Vorschau): https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Microsoft führt die Patchverwaltung auf den zugrunde liegenden Systemen aus, die Azure Container Registry unterstützen.

Automatisieren Sie Containerimageupdates, wenn Updates von Basisimages von einem Betriebssystem und anderen Patches erkannt werden.

Informationen zu Basisimageupdates für Azure Container Registry Tasks: https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Sie können die Drittanbieterlösung verwenden, um Anwendungsimages zu patchen.  Außerdem können Sie Azure Container Registry Tasks ausführen, um Updates von Anwendungsimages in einer Containerregistrierung auf der Grundlage von Sicherheitspatches oder anderen Updates in Basisimages zu automatisieren.

Informationen zu Basisimageaktualisierungen für ACR Tasks: https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Integrieren Sie Azure Container Registry (ACR) in Azure Security Center, um regelmäßige Scans von Containerimages auf Sicherheitsrisiken zu ermöglichen. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um regelmäßige Scans auf Sicherheitsrisiken von Images auszuführen.

Integration von Security Center in Azure Container Registry (Vorschau): https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Integrieren Sie Azure Container Registry (ACR) in Azure Security Center, um regelmäßige Scans von Containerimages auf Sicherheitsrisiken zu ermöglichen und Risiken zu klassifizieren. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um regelmäßige Scans auf Sicherheitsrisiken von Images und Risikoklassifizierung auszuführen.

Integration von Security Center in Azure Container Registry (Vorschau): https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center-Überwachung**: –

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln.  Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

Erstellen von Abfragen mit Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Anzeigen Ihrer Azure-Abonnements: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Grundlegendes zu Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Azure Container Registry verwaltet Metadaten (einschließlich Tags und Manifeste) für Images in einer Registrierung. Befolgen Sie die empfohlenen Vorgehensweisen, um Artefakte mit Tags zu versehen.

Informationen zu Registrierungen, Repositorys und Images: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Azure Container Registry verwaltet Metadaten (einschließlich Tags und Manifeste) für Images in einer Registrierung. Befolgen Sie die empfohlenen Vorgehensweisen, um Artefakte mit Tags zu versehen.

Informationen zu Registrierungen, Repositorys und Images: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Gemäß Ihren organisatorischen Anforderungen müssen Sie ein Inventar genehmigter Azure-Ressourcen erstellen.  

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Analysieren und überwachen Sie Azure Container Registry-Protokolle auf anormales Verhalten, und überprüfen Sie die Ergebnisse regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen.

Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Grundlegendes zum Log Analytics-Arbeitsbereich: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Ausführen benutzerdefinierter Abfragen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Mit Azure Automation haben Sie in Bezug auf die Bereitstellung, Abläufe und Außerbetriebnahme von Workloads und Ressourcen die volle Kontrolle.  Sie können eine eigene Lösung zum Entfernen nicht autorisierter Azure-Ressourcen implementieren. Einführung in Azure Automation: https://docs.microsoft.com/azure/automation/automation-intro


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Nutzen Sie Azure Policy, um einzuschränken, welche Dienste in Ihrer Umgebung bereitgestellt werden können.

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen.



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Verwenden Sie betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern, um die Möglichkeiten der Benutzer einzuschränken, Skripts innerhalb der Azure-Computeressourcen auszuführen.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Verwenden Sie betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern, um die Möglichkeiten der Benutzer einzuschränken, Skripts innerhalb der Azure-Computeressourcen auszuführen.

So steuern Sie z. B. die Ausführung von PowerShell-Skripts in Windows-Umgebungen: https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Software, die für den Geschäftsbetrieb erforderlich ist, aber ein höheres Risiko für das Unternehmen darstellen kann, sollte innerhalb des eigenen virtuellen Computers und/oder virtuellen Netzwerks isoliert und entweder mit einer Azure Firewall oder einer Netzwerksicherheitsgruppe ausreichend gesichert werden.

Erstellen eines virtuellen Netzwerks: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Erstellen einer NSG mit einer Sicherheitskonfiguration: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy oder Azure Security Center, um Sicherheitskonfigurationen für alle Azure-Ressourcen zu verwalten.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nutzen Sie die Azure Security Center-Empfehlung „Remediate Vulnerabilities in Security Configurations on your Virtual Machines“ (Beheben von Sicherheitsrisiken in Sicherheitskonfigurationen auf virtuellen Computern) an, um Sicherheitskonfigurationen für alle Computeressourcen zu verwalten.

Überwachen von Empfehlungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-recommendations

Umsetzen von Empfehlungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

Speichern von Code in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation zu Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend Der Benchmark betrifft hauptsächlich Computeressourcen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy, um Systemkonfigurationen zu melden, zu überwachen und zu erzwingen. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend Der Benchmark betrifft hauptsächlich Computeressourcen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen.

Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Umsetzen von Empfehlungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy: https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend Der Benchmark betrifft hauptsächlich Computeressourcen.


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

Integration mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

Verwenden einer von Azure verwalteten Identität mit Azure Container Registry Tasks: https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Konfigurieren von verwalteten Identitäten: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Verwenden einer verwalteten Identität für die Azure Container Registry-Authentifizierung: https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Einrichten von Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Verwenden Sie Microsoft Antimalware für Azure Cloud Services und Virtual Machines, um Ihre Ressourcen kontinuierlich zu überwachen und zu schützen. Verwenden Sie für Linux eine Antischadsoftware von Drittanbietern.

Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Container Registry) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage usw.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend Dieser Benchmark ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Die Daten in Ihrer Microsoft Container Registry werden immer automatisch repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Azure Container Registry kopiert Ihre Daten, sodass Sie vor geplanten und ungeplanten Ereignissen geschützt sind.

Führen Sie optional Georeplikation einer Containerregistrierung aus, um Registrierungsreplikate in mehreren Azure-Regionen zu verwalten. 

Georeplikation in Azure Container Registry: https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Sichern Sie optional Containerimages, indem Sie sie aus einer Registrierung in eine andere importieren.

Sichern Sie kundenseitig verwaltete Schlüssel in Azure Key Vault mithilfe von Azure-Befehlszeilentools oder SDKs.

Importieren von Containerimages in eine Containerregistrierung: https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Sichern von Key Vault-Schlüsseln in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Testen Sie die Wiederherstellung von kundenseitig verwalteten Schlüsseln in Azure Key Vault mithilfe von Azure-Befehlszeilentools oder SDKs.

Wiederherstellen von Azure Key Vault-Schlüsseln in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Sie können das vorläufige Löschen in Azure Key Vault aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

Aktivieren des vorläufigen Löschens in Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

Konfigurieren von Workflowautomatisierungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Struktur eines Vorfalls laut Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle zu entwickeln: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

Informationen finden Sie in der folgenden Veröffentlichung des NIST: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

Festlegen der Kontaktinformationen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

Konfigurieren des fortlaufenden Exports: https://docs.microsoft.com/azure/security-center/continuous-export

Streamen von Warnungen in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

Konfigurieren von „Workflowautomatisierung“ und Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).
