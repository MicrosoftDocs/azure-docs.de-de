---
title: Azure-Sicherheitsbaseline für Azure Cache for Redis
description: Azure-Sicherheitsbaseline für Azure Cache for Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 567652ec8da257e72aafa95e24af9f5680a2b6d3
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807953"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-Sicherheitsbaseline für Azure Cache for Redis

Die Azure-Sicherheitsbaseline für Azure Cache for Redis enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Stellen Sie Azure Cache for Redis in einem virtuellen Netzwerk (VNet) bereit. Ein VNet ist ein privates Netzwerk in der Cloud. Wenn eine Azure Cache for Redis-Instanz mit einem VNET konfiguriert wird, ist dieses nicht öffentlich adressierbar, und auf das VNET kann nur über virtuelle Computer und Anwendungen innerhalb des VNET zugegriffen werden.

Sie können auch Firewallregeln mit einem Start- und End-IP-Adressbereich angeben. Wenn Firewallregeln konfiguriert werden, können nur Clientverbindungen aus dem angegebenen IP-Adressbereich eine Verbindung mit dem Cache herstellen.

Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis des Typs „Premium“:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Konfigurieren von Firewallregeln für Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Wenn virtuelle Computer (VMs) im gleichen virtuellen Netzwerk wie Ihre Azure Cache for Redis-Instanz bereitgestellt werden, können Sie Netzwerksicherheitsgruppen (NSG) verwenden, um das Risiko der Datenexfiltration zu verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Die Bereitstellung über Azure Virtual Network (VNet) bietet ein höheres Maß an Sicherheit und Isolation sowohl für Ihre Azure Cache for Redis-Instanz als auch für Ihre Subnetze. Sie profitieren außerdem von Richtlinien für die Zugriffssteuerung sowie von weiteren Features zur Begrenzung des Zugriffs. Wenn eine Azure Cache for Redis-Instanz mit einem VNet bereitgestellt wird, ist sie nicht öffentlich adressierbar. Der Zugriff darauf ist nur über VMs und Anwendungen im VNet möglich.

Aktivieren Sie in den VNets mit Ihrer Azure Cache for Redis-Instanz DDoS Protection Standard, um sich vor DDoS-Angriffen (Distributed Denial-of-Service) zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis des Typs „Premium“:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Wenn virtuelle Computer (VMs) im gleichen virtuellen Netzwerk wie Ihre Azure Cache for Redis-Instanz bereitgestellt werden, können Sie Netzwerksicherheitsgruppen (NSG) verwenden, um das Risiko der Datenexfiltration zu verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Bei Verwendung von Azure Cache for Redis mit Ihren Webanwendungen, die in Azure App Service- oder Compute-Instanzen ausgeführt werden, stellen Sie alle Ressourcen innerhalb eines virtuellen Azure-Netzwerks (VNet) bereit und schützen diese mit einer Azure Web Application Firewall (WAF) für Web Application Gateway. Konfigurieren Sie für WAF die Ausführung im „Präventionsmodus“. In diesem Modus werden Eindringversuche und Angriffe blockiert, die von den Regeln erkannt werden. Der Angreifer erhält eine Ausnahme vom Typ 403 (nicht autorisierter Zugriff), und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

Wählen Sie alternativ ein Angebot in Azure Marketplace aus, das IDS-/IPS-Funktionalität mit Funktionen zur Nutzlastuntersuchung und/oder Anomalieerkennung unterstützt.

Grundlegendes zu Azure WAF-Funktionen:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Bereitstellen von Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie VNet-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen (NSG) oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Sie können auch Anwendungssicherheitsgruppen (ASG) verwenden, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Mit ASGs können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren.

VNet-Diensttags:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Anwendungssicherheitsgruppen:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Cache for Redis-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.Cache“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Cache for Redis-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:

Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden

DDoS Protection Standard sollte aktiviert sein.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen (ARM), rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen einer Azure-Blaupause:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihrer Azure Cache for Redis-Bereitstellung zugeordnet sind, um sie in einer Taxonomie logisch zu organisieren.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Nutzen Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen im Zusammenhang mit Ihren Azure Cache for Redis-Instanzen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle, die für Azure-Ressourcen wie Azure Cache for Redis verwendet wird, für Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hub-Instanz oder ein Azure-Speicherkonto. Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre Azure Cache for Redis-Instanzen auf Steuerungsebene erfolgen. Mit Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) auf Steuerungsebene ermitteln, die für Ihre Azure Cache for Redis-Instanzen stattfinden.

Aktivieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hub-Instanz oder ein Azure-Speicherkonto. Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre Azure Cache for Redis-Instanzen auf Steuerungsebene erfolgen. Mit Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) auf Steuerungsebene ermitteln, die für Ihre Azure Cache for Redis-Instanzen stattfinden.

Wenngleich durch Aktivierung von „Diagnoseeinstellungen“ Metriken verfügbar sind, ist die Überwachungsprotokollierung auf Datenebene für Azure Cache for Redis noch nicht verfügbar.

Aktivieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor die Protokollaufbewahrungsdauer für Log Analytics-Arbeitsbereiche, die Ihrer Azure Cache for Redis-Instanz zugeordnet sind, gemäß den Compliancevorschriften Ihres Unternehmens fest.

Beachten Sie, dass die Überwachungsprotokollierung auf Datenebene für Azure Cache for Redis noch nicht verfügbar ist.

Festlegen von Parametern für die Protokollaufbewahrung:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um Suchbegriffe zu ermitteln, Trends auszumachen, Muster zu analysieren und viele weitere Erkenntnisse auf Grundlage der Aktivitätsprotokolldaten zu gewinnen, die möglicherweise für Azure Cache for Redis gesammelt wurden.

Beachten Sie, dass die Überwachungsprotokollierung auf Datenebene für Azure Cache for Redis noch nicht verfügbar ist.

Aktivieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Sie können den Empfang von Warnmeldungen auf Grundlage von Metriken und Aktivitätsprotokollen im Zusammenhang mit Ihren Azure Cache for Redis-Instanzen konfigurieren. Mit Azure Monitor können Sie einen Alarm so konfigurieren, dass eine E-Mail-Benachrichtigung gesendet bzw. ein Webhook oder eine Azure-Logik-App aufgerufen wird.

Wenngleich durch Aktivierung von „Diagnoseeinstellungen“ Metriken verfügbar sind, ist die Überwachungsprotokollierung auf Datenebene für Azure Cache for Redis noch nicht verfügbar.

Konfigurieren von Benachrichtigungen für Azure Cache for Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Cache for Redis verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Cache for Redis verarbeitet keine Protokolle zu DNS und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Der Zugriff auf Steuerungsebene auf Azure Cache for Redis wird mittels Azure Active Directory (AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern. 

Der Zugriff auf Datenebene auf Azure Cache for Redis wird mittels Zugriffsschlüsseln gesteuert. Diese Schlüssel werden von den Clients verwendet, die eine Verbindung mit Ihrem Cache herstellen, und können jederzeit neu generiert werden.

Es wird nicht empfohlen, Standardkennwörter in Ihre Anwendung zu integrieren. Stattdessen können Sie Ihre Kennwörter in Azure Key Vault speichern und sie über Azure Active Directory abrufen.

Neugenerieren von Zugriffsschlüsseln für Azure Cache for Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Verwenden von Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Verwenden von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Azure Cache for Redis verwendet Zugriffsschlüssel zur Benutzerauthentifizierung und unterstützt kein einmaliges Anmelden (Single Sign-On, SSO) auf Datenebene. Der Zugriff auf die Steuerungsebene für Azure Cache for Redis ist über die REST-API verfügbar und unterstützt SSO. Legen Sie den Autorisierungsheader für Ihre Anforderungen auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abrufen.

Grundlegendes zur Rest-API für Azure Cache for Redis: https://docs.microsoft.com/rest/api/redis/

Grundlegendes zu SSO mit Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Aktivieren von MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

Bereitstellen von Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informationen zu Azure AD-Risikoerkennungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Konfigurieren Sie in der Azure Active Directory-Funktion (AAD) „Bedingter Zugriff“ benannte Standorte, um den Zugriff nur bestimmten logischen Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zu gestatten.

Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

Die Azure AD-Authentifizierung kann nicht für den direkten Zugriff auf die Azure Cache for Redis-Datenebene verwendet werden. Allerdings können Azure AD-Anmeldeinformationen für die Verwaltung auf Steuerungsebene (z. B. Azure-Portal) zum Steuern von Azure Cache for Redis-Zugriffsschlüsseln verwendet werden.


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben. 

Grundlegendes zur Azure AD-Berichterstellung: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Sie haben Zugriff auf Azure Active Directory- Anmeldeaktivitäten (AAD) sowie Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration in Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie für abweichendes Verhalten bei der Kontoanmeldung auf Steuerungsebene Azure Active Directory (AAD) Identity Protection und Risikoerkennungsfunktionen, um automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

Anzeigen riskanter Azure AD-Anmeldungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht verfügbar. Kunden-Lockbox wird für Azure Cache for Redis noch nicht unterstützt.

Liste der durch Kunden-Lockbox unterstützten Dienste:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure Cache for Redis-Instanzen müssen durch ein virtuelles Netzwerk/Subnetz getrennt und entsprechend mit Tags versehen werden. Verwenden Sie optional die Azure Cache for Redis-Firewall, um Regeln so zu definieren, dass nur Clientverbindungen aus angegebenen IP-Adressbereichen mit dem Cache möglich sind.

Erstellen zusätzlicher Azure-Abonnements:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen:

https://docs.microsoft.com/azure/governance/management-groups/create

Bereitstellen von Azure Cache for Redis in einem VNet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Konfigurieren von Firewallregeln für Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Noch nicht verfügbar. Funktionen für Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Cache for Redis noch nicht verfügbar.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Cache for Redis und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Cache for Redis verlangt standardmäßig eine mit TLS verschlüsselte Kommunikation. Derzeit werden die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt. Die Unterstützung von TLS 1.0 und 1.1 wird jedoch branchenweit eingestellt werden. Verwenden Sie daher nach Möglichkeit TLS 1.2. Wenn TLS von der Clientbibliothek oder dem Tool nicht unterstützt wird, kann die Aktivierung unverschlüsselter Verbindungen über das Azure-Portal oder über Verwaltungs-APIs vorgenommen werden. Wenn verschlüsselte Verbindungen nicht möglich sind, empfiehlt es sich, den Cache und die Clientanwendung in ein virtuelles Netzwerk einzubinden.

Grundlegendes zur Verschlüsselung während der Übertragung mit Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Grundlegendes zu erforderlichen in VNet-Cacheszenarien verwendeten Ports:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Cache for Redis noch nicht verfügbar. Versehen Sie Instanzen mit vertraulichen Informationen mit entsprechenden Tags, und implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf die Steuerungsebene von Azure Cache for Redis (d. h. das Azure-Portal) zu steuern. 

Konfigurieren von Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Cache for Redis und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Cache for Redis speichert Kundendaten im Arbeitsspeicher. Obwohl der Arbeitsspeicher durch viele von Microsoft implementierte Steuerungsfunktionen umfassend geschützt ist, wird er standardmäßig nicht verschlüsselt. Falls für Ihre Organisation erforderlich, verschlüsseln Sie Inhalte, bevor Sie sie in Azure Cache for Redis speichern.

Bei Verwenden der Azure Cache for Redis-Funktion „Redis-Datenpersistenz“ werden die Daten an ein Azure Storage-Konto gesendet, das Sie besitzen und verwalten. Sie können bei der Cacheerstellung die Persistenz auf dem Blatt „Neuer Azure Cache for Redis“ sowie für vorhandene Premium-Caches im Menü „Ressourcen“ konfigurieren.

Daten in Azure Storage werden auf transparente Weise mit der AES-256-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – ver- und entschlüsselt und sind mit dem FIPS 140-2-Standard konform. Die Azure Storage-Verschlüsselung kann nicht deaktiviert werden. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihres Speicherkontos nutzen, oder Sie können die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten.

Konfigurieren von Persistenz in Azure Cache for Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Grundlegendes zur Verschlüsselung von Azure Storage-Konten: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Grundlegendes zum Schutz von Azure-Kundendaten: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Cache for Redis und anderen kritischen bzw. verbundenen Ressourcen vorgenommen werden.

Erstellen von Warnungen für Azure-Aktivitätsprotokollereignisse:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Empfehlungen im Azure Security Center zum Schützen Ihrer Azure Cache for Redis-Instanzen und damit verbundenen Ressourcen.

Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Cache for Redis unterstützen.

Grundlegendes zu Azure Security Center-Empfehlungen: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Cache for Redis unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

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

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Cache for Redis-Instanzen und damit verbundenen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

Not allowed resource types (Unzulässige Ressourcentypen)

Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

Not allowed resource types (Unzulässige Ressourcentypen)

Zulässige Ressourcentypen

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Konfigurieren Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager (ARM) einzuschränken, indem Sie für die App zur Verwaltung von Microsoft Azure „Zugriff blockieren“ konfigurieren.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihre Azure Cache for Redis-Instanzen. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Cache“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Cache for Redis-Instanzen zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen für Ihre Azure Cache for Redis-Instanzen verwenden, z. B.:

Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden

Anzeigen verfügbarer Azure Policy-Aliase: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen oder Azure Resource Manager-Vorlagen für Ihre Azure Cache for Redis-Instanzen und damit verbundene Ressourcen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

Speichern von Code in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation zu Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Cache“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Cache“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (überwachen), [deny] (verweigern) und [deploy if not existent] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure Cache for Redis-Instanzen und damit verbundene Ressourcen automatisch zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cache for Redis-Instanzen verwendet werden, nutzen Sie „Verwaltete Dienstidentität“ in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Cache for Redis-Geheimnissen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Integration mit verwalteten Azure-Identitäten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cache for Redis-Instanzen verwendet werden, nutzen Sie „Verwaltete Dienstidentität“ in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Cache for Redis-Geheimnissen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die AAD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Konfigurieren von verwalteten Identitäten:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integration mit verwalteten Azure-Identitäten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Einrichten von Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft-Antischadsoftware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Cache for Redis) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in Azure-Ressourcen ohne Computeaufgaben hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft-Antischadsoftware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Cache for Redis) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Aktivieren Sie Redis-Persistenz. Mithilfe der Redis-Persistenz können Sie die in Redis gespeicherten Daten dauerhaft speichern. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann im Fall eines Hardwarefehlers laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen.

Sie können auch die Azure Cache for Redis-Exportfunktion verwenden. Mit der Exportfunktion können Sie die in Azure Cache for Redis gespeicherten Daten als Redis-kompatible RDB-Dateien exportieren. Sie können diese Funktion nutzen, um Daten von einer Azure Cache for Redis-Instanz zur anderen oder auf einen anderen Redis-Server zu verschieben. Während des Exportvorgangs wird auf dem virtuellen Computer, der die Azure Cache for Redis-Serverinstanz hostet, eine temporäre Datei erstellt, die dann in das angegebene Speicherkonto hochgeladen wird. Nachdem der Exportvorgang mit dem Status „Erfolg“ oder „Fehler“ abgeschlossen wurde, wird die temporäre Datei gelöscht.

Aktivieren der Redis-Persistenz:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Verwenden der Azure Cache for Redis-Exportfunktion:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Aktivieren Sie Redis-Persistenz. Mithilfe der Redis-Persistenz können Sie die in Redis gespeicherten Daten dauerhaft speichern. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann im Fall eines Hardwarefehlers laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen.

Sie können auch die Azure Cache for Redis-Exportfunktion verwenden. Mit der Exportfunktion können Sie die in Azure Cache for Redis gespeicherten Daten als Redis-kompatible RDB-Dateien exportieren. Sie können diese Funktion nutzen, um Daten von einer Azure Cache for Redis-Instanz zur anderen oder auf einen anderen Redis-Server zu verschieben. Während des Exportvorgangs wird auf dem virtuellen Computer, der die Azure Cache for Redis-Serverinstanz hostet, eine temporäre Datei erstellt, die dann in das angegebene Speicherkonto hochgeladen wird. Nachdem der Exportvorgang mit dem Status „Erfolg“ oder „Fehler“ abgeschlossen wurde, wird die temporäre Datei gelöscht.

Wenn Sie Azure Key Vault zum Speichern von Anmeldeinformationen für Ihre Azure Cache for Redis-Instanzen verwenden, stellen Sie regelmäßige automatische Sicherungen Ihrer Schlüssel sicher.

Aktivieren der Redis-Persistenz:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Verwenden der Azure Cache for Redis-Exportfunktion:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Sichern von Key Vault-Schlüsseln:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Verwenden Sie die Azure Cache for Redis-Importfunktion. Die Importfunktion kann verwendet werden, um Redis-kompatible RDB-Dateien von beliebigen Redis-Servern zu importieren, die in einer Cloud oder Umgebung ausgeführt werden, z. B. Redis unter Linux oder Windows oder bei einem Cloudanbieter wie Amazon Web Services und anderen. Das Importieren von Daten ist eine einfache Möglichkeit zum Erstellen eines Cache mit vorab aufgefüllten Daten. Während des Importvorgangs lädt Azure Cache for Redis die RDB-Dateien aus Azure Storage in den Arbeitsspeicher und fügt die Schlüssel anschließend in den Cache ein.

Testen Sie regelmäßig die Datenwiederherstellung Ihrer Azure Key Vault-Geheimnisse.

Verwenden der Azure Cache for Redis-Importfunktion:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Wiederherstellen von Key Vault-Geheimnissen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Azure Cache for Redis-Sicherungen aus der Redis-Exportfunktion und Redis-Persistenz werden in dem von Ihnen ausgewählten Azure Storage-Konto gespeichert. Daten in Azure Storage werden auf transparente Weise mit der AES-256-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – ver- und entschlüsselt und sind mit dem FIPS 140-2-Standard konform. Die Azure Storage-Verschlüsselung kann nicht deaktiviert werden. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihres Speicherkontos nutzen, oder Sie können die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten.

Grundlegendes zur Verschlüsselung von Azure Storage-Konten: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Microsoft

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

Konfigurieren von Workflowautomatisierungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Struktur eines Vorfalls laut Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle zu entwickeln:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

Informationen finden Sie in der folgenden Veröffentlichung des NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

Festlegen der Kontaktinformationen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

Konfigurieren des fortlaufenden Exports:

https://docs.microsoft.com/azure/security-center/continuous-export

Streamen von Warnungen in Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

Konfigurieren von „Workflowautomatisierung“ und Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Weitere Informationen zur Microsoft-Strategie und Ausführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).