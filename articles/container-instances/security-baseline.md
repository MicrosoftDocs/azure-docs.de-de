---
title: Azure-Sicherheitsbaseline für Container Instances
description: Azure-Sicherheitsbaseline für Container Instances
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3f4e855c52488652b88a3b14e9817f1755167b67
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806120"
---
# <a name="azure-security-baseline-for-container-instances"></a>Azure-Sicherheitsbaseline für Container Instances

Die Azure-Sicherheitsbaseline für Container Instances enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Das virtuelle Azure-Netzwerk (Azure Virtual Network) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Integrieren Sie Ihre Containergruppen in Azure Container Instances mit einem virtuellen Azure-Netzwerk. 

* [Szenarien und Ressourcen für virtuelle Azure-Netzwerke – Azure Container Instances](./container-instances-virtual-network-concepts.md)

* [Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk](./container-instances-vnet.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Netzwerkressourcen in Azure abzusichern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Schützen von Netzwerkressourcen](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen in Azure Container Instances bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Aktivieren Sie die Diagnoseeinstellung für WAF, und erfassen Sie Protokolle in ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich.

* [Bereitstellen von Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie den DDoS-Standardschutz in Ihren virtuellen Azure-Netzwerken, um sie vor DDoS-Angriffe zu schützen. Verwenden Sie die integrierten Informationen zu Bedrohungen in Azure Security Center, um die Kommunikation mit bekannten schädlichen IP-Adressen zu verweigern. Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation bereit, aktiviert Sie Threat Intelligence, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird. Verwenden Sie den Just-in-Time-Netzwerkzugriff in Azure Security Center, um NSGs so zu konfigurieren, dass die Exposition von Endpunkten auf genehmigte IP-Adressen und einen begrenzten Zeitraum eingeschränkt wird. Verwenden Sie die adaptive Netzwerkhärtung in Azure Security Center, um NSG-Konfigurationen zu empfehlen, die Ports und Quell-IPs basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken. 

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

* [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

* [Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../security-center/security-center-just-in-time.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Wenn Sie bei der Implementierung Ihres virtuellen Netzwerks Netzwerksicherheitsgruppen (NSGs) verwenden, aktivieren Sie NSG-Datenflussprotokolle für die NSG, die an das Subnetz angefügt ist, das an Azure Container Instances delegiert wurde. Erfassen Sie die NSG-Flussprotokolle in einem Azure Storage-Konto, um Flussdatensätze zu generieren. Aktivieren Sie die Azure Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt. Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md) 



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Stellen Sie Azure Application Gateway für Webanwendungen mit aktiviertem HTTPS/SSL für vertrauenswürdige Zertifikate bereit.

* [Bereitstellen von Application Gateway](../application-gateway/quick-create-portal.md)

* [Konfigurieren von Application Gateway für die Verwendung von HTTPS](../application-gateway/create-ssl-portal.md) 

* [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](../application-gateway/overview.md)

* [Verfügbarmachen einer statischen IP-Adresse für eine Containergruppe](./container-instances-application-gateway.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern. 

Sie können auch Anwendungssicherheitsgruppen verwenden, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren. 

* [Grundlegendes zu Diensttags und deren Verwendung](../virtual-network/service-tags-overview.md) 

* [Grundlegendes zu Anwendungssicherheitsgruppen und deren Verwendung](../virtual-network/network-security-groups-overview.md#application-security-groups)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Kontrollen und Richtlinien in einer einzigen Blaupausendefinition packen. Sie können die Blaupause auf neue Abonnements anwenden sowie die Steuerung und Verwaltung durch die Versionsverwaltung optimieren. 

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für NSGs und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Containerinstanzen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben jedoch die Möglichkeit, die Zeitsynchronisierungseinstellungen für Ihre Computeressourcen zu verwalten. Führen Sie z. B. einen Zeitsynchronisierungsbefehl in einem laufenden Container aus.

* [Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen](../virtual-machines/windows/time-sync.md)

* [Ausführen eines Befehls in einer ausgeführten Azure-Containerinstanz](./container-instances-exec.md)



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von einer Azure-Containergruppe generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung.

* [Protokollierung für Containergruppen und -instanzen mit Azure Monitor-Protokollen](./container-instances-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Azure Monitor erfasst Ressourcenprotokolle (früher als Diagnoseprotokolle bezeichnet) für benutzergesteuerte Ereignisse in Ihrer Registrierung. Azure Container Instances enthält integrierte Unterstützung für das Senden von Protokollen und Ereignisdaten zu Containergruppen sowie Containerprotokollen an Azure Monitor-Protokolle.

* [Protokollierung für Containergruppen und -instanzen mit Azure Monitor-Protokollen](../container-registry/container-registry-diagnostics-audit-logs.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Nicht zutreffend Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen. 

* [Protokollierung für Containergruppen und -instanzen mit Azure Monitor-Protokollen](./container-instances-log-analytics.md)

* [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Verwenden Sie einen Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. 

* [Protokollierung für Containergruppen und -instanzen mit Azure Monitor-Protokollen](./container-instances-log-analytics.md)

* [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Stellen Sie eine eigene Antischadsoftwarelösung und Ereignissammlung bereit, wenn dies für die Ausführung in einem Container erforderlich ist. 


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Stellen Sie ggf. eine eigene Lösung bereit, um DNS-Protokolle in einem Container abzufragen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Konfigurieren Sie bei Bedarf die Konsolenprotokollierung in einer ausgeführten Containerinstanz.

* [Ausführen eines Befehls in einer ausgeführten Azure-Containerinstanz](./container-instances-exec.md)



**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (Azure AD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Wenn Sie eine Azure-Containerregistrierung mit Azure Container Instances verwenden, verfolgen Sie für jede Azure-Containerregistrierung nach, ob das integrierte Administratorkonto aktiviert oder deaktiviert ist. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Container Registry-Administratorkonto](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory (Azure AD) verfolgt nicht das Konzept der Standardkennwörter. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

Wenn Sie eine Azure-Containerregistrierung mit Azure Container Instances verwenden und das Standardadministratorkonto einer Azure-Containerregistrierung aktiviert ist, werden automatisch komplexe Kennwörter erstellt, die rotiert werden sollten. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

* [Azure Container Registry-Administratorkonto](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Wenn Sie eine Azure-Containerregistrierung mit Azure Container Instances verwenden, erstellen Sie Prozeduren zum Aktivieren des integrierten Administratorkontos einer Containerregistrierung. Deaktivieren Sie das Konto, wenn es nicht verwendet wird.

* [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md)

* [Azure Container Registry-Administratorkonto](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

* [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (Azure AD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

* [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

* [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

* [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

* [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure Active Directory-Anmeldeaktivitäten (Azure AD) und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- (Security Information and Event Management) und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure Active Directory-Funktionen (Azure AD) zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Derzeit nicht verfügbar. Kunden-Lockbox wird für Azure Container Instances derzeit nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Ressourcentags für die Nachverfolgung von Azure-Containerinstanzen, in denen vertrauliche Informationen gespeichert oder verarbeitet werden. 

Versehen Sie Containerregistrierungen mit Tags und Versionen zur Hilfe bei der Nachverfolgung von Images, in denen vertrauliche Informationen gespeichert oder verarbeitet werden.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Empfehlungen für das Tagging und die Versionsverwaltung von Containerimages](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein VNET/Subnetz getrennt, ordnungsgemäß gekennzeichnet und durch eine NSG oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden.

* [Ausführen eines Befehls in einer ausgeführten Azure-Containerinstanz](./container-instances-exec.md)

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md) 
* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von „Warnen“ oder „Warnen und ablehnen“ mit Azure Firewall](../firewall/threat-intel.md)


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Stellen Sie ein automatisiertes Tool an den Netzwerkperimetern bereit, das die nicht autorisierte Übertragung von sensiblen Informationen überwacht und derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt. Überwachen und blockieren Sie nicht autorisierte Informationsübertragungen von Azure-Dateifreigaben und anderen Volumes, die in Containerinstanzen eingebunden sind.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md) 

* [Einbinden einer Azure-Dateifreigabe in Azure Container Instances](./container-instances-volume-azure-files.md)


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Stellen Sie sicher, dass alle Clients, die eine Verbindung mit Ihren Azure-Containergruppen herstellen, TLS 1.2 oder höher aushandeln können. Für Microsoft Azure-Ressourcen wird standardmäßig die TLS 1.2-Aushandlung ausgeführt.

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

* [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Features für Datenidentifizierung, -klassifizierung und -verlustvermeidung sind für Azure Container Registry derzeit nicht verfügbar. Versehen Sie Containergruppen, mit denen möglicherweise vertrauliche Informationen verarbeitet werden, mit entsprechenden Tags, und implementieren Sie bei Bedarf eine Drittanbieterlösung für Compliancezwecke.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Azure-Zugriffssteuerung (Azure RBAC), um den Zugriff auf Daten und Ressourcen in Azure Container Instances zu steuern. 

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Standardmäßig werden alle Bereitstellungsdaten in Azure Container Instances im Ruhezustand mit von Microsoft verwalteten Schlüsseln verschlüsselt. Optional können Sie die Verschlüsselung mit Ihrem eigenen Schlüssel (vom Kunden verwalteter Schlüssel) verwalten.

* [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

* [Verschlüsseln von Bereitstellungsdaten mit Azure Container Instances](./container-instances-encrypt-data.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an Ihren Containergruppen und -instanzen ausgelöst werden. 

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Implementieren Sie Lösungen zum Überprüfen von Containerimages in einer privaten Registrierung und zum Identifizieren potenzieller Sicherheitsrisiken. Befolgen Sie die Empfehlungen von Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre in Azure Container Registry gespeicherten Containerimages. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um Imagesicherheitsrisiken zu bewerten.

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)

* [Integration von Security Center in Azure Container Registry](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Microsoft führt die Patchverwaltung auf den zugrunde liegenden Systemen aus, die ausgeführte Container unterstützen.

Verwenden Sie eine benutzerdefinierte oder Drittanbieterlösung zum Patchen von Containerimages. Wenn Sie Containerimages in Azure Container Registry speichern, führen Sie Azure Container Registry-Aufgaben aus, um Updates von Anwendungsimages in einer Containerregistrierung anhand von Sicherheitspatches oder anderen Updates in Basisbetriebssystemimages zu automatisieren.

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)

* [Informationen zu Basisimageupdates für Azure Container Registry-Aufgaben](../container-registry/container-registry-tasks-base-images.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Verwenden Sie eine benutzerdefinierte oder Drittanbieterlösung zum Patchen von Containerimages. Wenn Sie Containerimages in Azure Container Registry speichern, führen Sie Azure Container Registry-Aufgaben aus, um Updates von Anwendungsimages in einer Containerregistrierung anhand von Sicherheitspatches oder anderen Updates in Basisbetriebssystemimages zu automatisieren.

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)

* [Informationen zu Basisimageaktualisierungen für ACR-Aufgaben](../container-registry/container-registry-tasks-base-images.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Exportieren Sie die Imageüberprüfungsergebnisse in regelmäßigen Abständen, und vergleichen Sie die Ergebnisse, um sicherzustellen, dass die Sicherheitsrisiken korrigiert wurden. Wenn Sie Containerimages in Azure Container Registry speichern, integrieren Sie Ihre Registrierung in Azure Security Center, um regelmäßige Überprüfungen von Containerimages auf Sicherheitsrisiken zu ermöglichen. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um regelmäßige Scans auf Sicherheitsrisiken von Images auszuführen.

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)

* [Integration von Security Center in Azure Container Registry](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**:  Wenn Sie Containerimages in Azure Container Registry speichern, integrieren Sie Ihre Registrierung in Azure Security Center, um regelmäßige Überprüfungen von Containerimages auf Sicherheitsrisiken zu ermöglichen und Risiken zu klassifizieren. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um regelmäßige Scans auf Sicherheitsrisiken von Images und Risikoklassifizierung auszuführen.

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)

* [Integration von Security Center in Azure Container Registry](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Azure Container Instances und zugehörige Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Gemäß Ihren organisatorischen Anforderungen müssen Sie ein Inventar genehmigter Azure-Ressourcen erstellen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy](../container-registry/container-registry-azure-policy.md)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Implementieren Sie eine eigene oder eine Drittanbieterlösung, um den Bestand an Software für genehmigte Containeranwendungen zu erfassen. 

Implementieren Sie Lösungen zum Überprüfen von Containerimages in einer privaten Registrierung und zum Identifizieren potenzieller Sicherheitsrisiken. Befolgen Sie die Empfehlungen von Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre in Azure Container Registry gespeicherten Containerimages. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um Imagesicherheitsrisiken zu bewerten.

Überwachen Sie Azure Container Instances-Protokolle auf anomales Verhalten, und überprüfen Sie die Ergebnisse regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen.

* [Protokollierung für Containergruppen und -instanzen mit Azure Monitor-Protokollen](./container-instances-log-analytics.md)

* [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Sicherheitsüberlegungen für Azure Container Instances](./container-instances-image-security.md)
* [Integration von Security Center in Azure Container Registry](../security-center/defender-for-container-registries-introduction.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Mit Azure Automation haben Sie in Bezug auf die Bereitstellung, Abläufe und Außerbetriebnahme von Workloads und Ressourcen die volle Kontrolle. Sie können eine eigene Lösung zum Entfernen nicht autorisierter Azure-Ressourcen und Softwareanwendungen implementieren.

* [Einführung in Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Versehen Sie Containerregistrierungen mit Tags und Versionen, um die Nachverfolgung von Images zu ermöglichen, in denen genehmigte Anwendungen ausgeführt werden.
* [Empfehlungen für das Tagging und die Versionsverwaltung von Containerimages](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um einzuschränken, welche Dienste in Ihrer Umgebung bereitgestellt werden können.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Versehen Sie Containerregistrierungen mit Tags und Versionen, um die Nachverfolgung von Images zu ermöglichen, in denen genehmigte Anwendungen ausgeführt werden.
* [Empfehlungen für das Tagging und die Versionsverwaltung von Containerimages](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6.11: <div>Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts<br></div>

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren. 

* [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Alle Benutzer mit Zugriff auf Azure Container Instances können Skripts in Containern ausführen.

Verwalten und überprüfen Sie den Zugriff auf Azure Container Instances-Ressourcen mithilfe verschiedener Azure-Abonnements oder Verwaltungsgruppen, oder isolieren Sie Ressourcen mithilfe von virtuellen Netzwerken und NSGs oder von Azure Firewall.

* [Ausführen eines Befehls in einer ausgeführten Azure-Containerinstanz](./container-instances-exec.md)

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Software, die für den Geschäftsbetrieb erforderlich ist, aber ein höheres Risiko für das Unternehmen darstellen kann, sollte innerhalb eines eigenen virtuellen Netzwerks isoliert und mit Azure Firewall oder einer Netzwerksicherheitsgruppe ausreichend geschützt werden.

* [Bereitstellen in einem virtuellen Netzwerk – Azure Container Instances](./container-instances-vnet.md) 

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwalten Sie eine genehmigte Containergruppenkonfiguration, indem Sie eine Azure Resource Manager-Vorlage verwenden oder einen Export in eine YAML-Datei ausführen. Verwenden Sie Azure Policy, um Sicherheitskonfigurationen für die zugehörigen Azure-Ressourcen zu verwalten.

* [Containergruppen in Azure Container Instances](container-instances-container-groups.md#deployment)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Verwenden Sie eine benutzerdefinierte oder Drittanbieterlösung zum Patchen von Containerimages. Wenn Sie Containerimages in Azure Container Registry speichern, führen Sie Azure Container Registry-Aufgaben aus, um Updates von Anwendungsimages in einer Containerregistrierung anhand von Sicherheitspatches oder anderen Updates in Basisbetriebssystemimages zu automatisieren. 

* [Informationen zu Basisimageupdates für Azure Container Registry-Aufgaben](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Implementieren Sie Lösungen zum Überprüfen von Containerimages in einer privaten Registrierung und zum Identifizieren potenzieller Sicherheitsrisiken in Betriebssystemkonfigurationen. Befolgen Sie die Empfehlungen von Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre in Azure Container Registry gespeicherten Containerimages. Stellen Sie optional Lösungen von Drittanbietern aus Azure Marketplace bereit, um Imagesicherheitsrisiken zu bewerten.

Verwenden Sie eine benutzerdefinierte oder Drittanbieterlösung zum Patchen von Containerimages. Wenn Sie Containerimages in Azure Container Registry speichern, führen Sie Azure Container Registry-Aufgaben aus, um Updates von Anwendungsimages in einer Containerregistrierung anhand von Sicherheitspatches oder anderen Updates in Basisbetriebssystemimages zu automatisieren. 

* [Sicherheitsempfehlungen für die Überwachung und Überprüfung von Containern für Azure Container Instances](./container-instances-image-security.md)

* [Integration von Security Center in Azure Container Registry](../security-center/defender-for-container-registries-introduction.md)
* [Informationen zu Basisimageupdates für Azure Container Registry-Aufgaben](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Speichern und verwalten Sie ARM-Vorlagen, YAML-Dateien und benutzerdefinierte Azure-Richtliniendefinitionen auf sichere Weise per Quellcodeverwaltung.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Speichern Sie Containerimages in Azure Container Registry, und stellen Sie mithilfe von Azure RBAC sicher, dass nur autorisierte Benutzer auf die Images zugreifen können.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Grundlegendes zu Azure RBAC für Container Registry](../container-registry/container-registry-roles.md)

* [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy, um Systemkonfigurationen zu melden, zu überwachen und zu erzwingen. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen.

Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

* [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Überwachen der Konformität von Azure-Containerregistrierungen mithilfe von Azure Policy](../container-registry/container-registry-azure-policy.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Wenn Sie Containerimages in Azure Container Registry speichern, verwenden Sie Azure Security Center, um Baselineüberprüfungen für Betriebssystem- und Docker-Einstellungen für Container durchzuführen.

* [Grundlegendes zu Azure Security Center-Containerempfehlungen](../security-center/container-security.md)


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

* [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Erstellen einer Key Vault-Instanz](../key-vault/secrets/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

* [Verwenden von verwalteten Identitäten mit Azure Container Instances](./container-instances-managed-identity.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Verwenden von verwalteten Identitäten mit Azure Container Instances](./container-instances-managed-identity.md)



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für IaaS-Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Azure Container Instances) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage usw. 


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für IaaS-Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Azure Container Instances) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Aktivieren Sie Azure Backup, und konfigurieren Sie die Sicherungsquelle (z. B. eine in Containergruppen eingebundene Dateifreigabe) sowie die gewünschte Häufigkeit und Beibehaltungsdauer. 

* [Aktivieren von Azure Backup](../backup/index.yml)

* [Einbinden einer Azure-Dateifreigabe in Azure Container Instances](./container-instances-volume-azure-files.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Sichern Sie kundenseitig verwaltete Schlüssel in Azure Key Vault mithilfe von Azure-Befehlszeilentools oder SDKs.

Sichern Sie optional Containerimages, indem Sie sie aus einer Registrierung in eine andere importieren.
* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Importieren von Containerimages in eine Containerregistrierung](../container-registry/container-registry-import-images.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Testen Sie die Wiederherstellung von kundenseitig verwalteten Schlüsseln in Azure Key Vault mithilfe von Azure-Befehlszeilentools oder SDKs.

* [Wiederherstellen von Azure Key Vault-Schlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Verschlüsseln von Bereitstellungsdaten – Azure Container Instances](./container-instances-encrypt-data.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Sie können das vorläufige Löschen in Azure Key Vault aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

* [Vorläufiges Löschen für Blobspeicher](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.

* [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NIST Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (NIST-Leitfaden für den Umgang mit Computersicherheitsincidents)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) aussagekräftig, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

* [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).