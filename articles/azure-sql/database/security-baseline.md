---
title: Azure-Sicherheitsbaseline
description: Azure-Sicherheitsbaseline für Azure SQL-Datenbank und Azure SQL Managed Instance
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f9930f42e7d76820674405a98ef53489b91f31f5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985687"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Azure-Sicherheitsbaseline für Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Die Azure-Sicherheitsbaseline für Azure SQL-Datenbank enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Sie können Azure Private Link aktivieren, um den Zugriff über einen privaten Endpunkt Ihres virtuellen Netzwerks auf Azure PaaS-Dienste (z. B. SQL-Datenbank) sowie auf in Azure gehostete Kunden-/Partnerdienste zu ermöglichen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert.

Wenn Sie Datenverkehr für Azure SQL-Datenbank zulassen möchten, verwenden Sie die SQL-Diensttags, um den ausgehenden Datenverkehr über Netzwerksicherheitsgruppen zu ermöglichen.

Mithilfe von VNET-Regeln kann Azure SQL-Datenbank nur Verbindungen akzeptieren, die von ausgewählten Subnetzen innerhalb eines virtuellen Netzwerks stammen.

Einrichten von Private Link für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Verwenden von VNET-Dienstendpunkten und -Regeln für Server:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und setzen Sie die Empfehlungen zum Netzwerkschutz für das Subnetz um, in dem Ihre Instanz von Azure SQL-Datenbank bereitgestellt wurde.

Aktivieren Sie für virtuelle Azure-Computer (VMs), die eine Verbindung mit Ihrer Instanz von Azure SQL-Datenbank herstellen, NSG-Flowprotokolle für die NSGs (Netzwerksicherheitsgruppen), mit denen diese VMs geschützt sind. Senden Sie die Protokolle anschließend zur Überwachung des Datenverkehrs an ein Azure Storage-Konto.

Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Aktivieren und Verwenden von Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken, die Ihren Azure SQL-Datenbank-Instanzen zugeordnet sind, um diese vor verteilten Denial-of-Service-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren von DDoS-Schutz:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Grundlegendes zu integrierten Informationen zu Bedrohungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Aktivieren Sie für virtuelle Azure-Computer (VMs), die eine Verbindung mit Ihrer Instanz von Azure SQL-Datenbank herstellen, NSG-Flowprotokolle für die NSGs (Netzwerksicherheitsgruppen), mit denen diese VMs geschützt sind. Senden Sie die Protokolle dann zur Überwachung des Datenverkehrs an ein Azure Storage-Konto. Aktivieren Sie Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren von Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Aktivieren von Advanced Threat Protection (ATP) für Azure SQL-Datenbank.  Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken sowie Angriffen durch Einschleusung von SQL-Befehlen und anomalen Datenbankzugriffs- und -abfragemustern. Warnungen von Advanced Threat Protection sind auch in Azure Security Center integriert.

Verstehen und Verwenden von Advanced Threat Protection für Azure SQL-Datenbank: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Bei Verwendung von Dienstendpunkten für Azure SQL-Datenbank ist eine ausgehende Verbindung zu den öffentlichen IP-Adressen von Azure SQL-Datenbank erforderlich: Netzwerksicherheitsgruppen (NSGs) müssen für IP-Adressen der Azure SQL-Datenbank geöffnet werden, um Verbindungen zuzulassen. Sie erreichen dies, indem Sie NSG-Diensttags für Azure SQL-Datenbank verwenden.

Grundlegendes zu Diensttags mit Dienstendpunkten für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Grundlegendes zu Diensttags und Verwenden von Diensttags:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Netzwerksicherheitskonfigurationen für Ihre Instanzen von Azure SQL-Datenbank mit Azure Policy. Sie können den Namespace „Microsoft.Sql“ verwenden, um benutzerdefinierte Richtliniendefinitionen zu definieren, oder die integrierten Richtliniendefinitionen, die für den Servernetzwerkschutz ausgelegt sind. Ein Beispiel für eine geeignete integrierte Netzwerksicherheitsrichtlinie für einen Server ist: „SQL-Datenbank sollte einen VNET-Dienstendpunkt verwenden.“

Verwenden Sie Azure Blueprints, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) und Richtlinien gemeinsam in einer Blaupausendefinition zusammengefasst werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihren Server beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben aber die Möglichkeit, die Zeitsynchronisierung für Ihre Computebereitstellungen zu aktualisieren.

Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie die Überwachung für Azure SQL-Datenbank, um Datenbankereignisse nachzuverfolgen und diese in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto, Ihrem Log Analytics-Arbeitsbereich oder in Event Hubs zu schreiben.

Darüber hinaus können Sie Azure SQL-Diagnosetelemetriedaten an Azure SQL-Analyse streamen. Dies ist eine Cloudlösung, die die Leistung von Azure SQL-Datenbank und Azure SQL Managed Instance bedarfsgesteuert und abonnementübergreifend überwacht. Die Lösung ermöglicht Ihnen das Erfassen und Visualisieren von Leistungsmetriken für Azure SQL-Datenbank und verfügt über integrierte intelligente Funktionen für die Behandlung von Leistungsproblemen.

Einrichten der Überwachung für Ihre Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Sammeln von Plattformprotokollen und -metriken mit Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Streamen von Diagnosedaten an Azure SQL-Analyse:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Überwachung auf Ihrem Server, und wählen Sie einen Speicherort für die Überwachungsprotokolle (Azure Storage, Log Analytics oder Event Hub) aus.

Aktivieren der Überwachung für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie den Aufbewahrungszeitraum für Protokolle beim Speichern Ihrer Azure SQL-Datenbank-Protokolle in einem Log Analytics-Arbeitsbereich gemäß den Konformitätsbestimmungen Ihrer Organisation fest.

Festlegen von Parametern für die Protokollaufbewahrung:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie Advanced Threat Protection von Azure Security Center, um für Ihre Instanz von Azure SQL-Datenbank Warnungen vor ungewöhnlichen Aktivitäten zu erhalten. Alternativ können Sie Warnungen basierend auf Metrikwerten oder Einträgen des Azure-Aktivitätsprotokolls für Ihre Instanzen von Azure SQL-Datenbank konfigurieren.

Grundlegendes zu Advanced Threat Protection und zu Warnungen für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Konfigurieren von benutzerdefinierten Warnungen für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Verwenden Sie Advanced Threat Protection von Azure Security Center für Azure SQL-Datenbank, um die Überwachung auf anomale Aktivitäten zu ermöglichen und entsprechende Warnungen zu erhalten. Aktivieren Sie Azure Defender für SQL für Ihre SQL-Datenbanken. Azure Defender für SQL umfasst Funktionen zur Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank darstellen können.

Grundlegendes zu Advanced Threat Protection und zu Warnungen für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

So aktivieren Sie Azure Defender für SQL für Azure SQL-Datenbank

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Verwalten von Warnungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Für Azure SQL-Datenbank wird die Antischadsoftwarelösung von Microsoft auf der zugrunde liegenden Plattform verwaltet.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Die DNS-Protokollierung gilt nicht für Azure SQL-Datenbank.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend. Die Befehlszeilenüberwachung gilt nicht für Azure SQL-Datenbank.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (Azure AD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory verfügt nicht über das Konzept der Standardkennwörter. Wir empfehlen Ihnen, beim Bereitstellen einer Instanz von Azure SQL-Datenbank die Option zum Integrieren der Authentifizierung in Azure Active Directory auszuwählen.

Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Grundlegendes zu Identität und Zugriff in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Nicht zutreffend. Sie können zwar die Integration der Azure Active Directory-Authentifizierung in Azure SQL-Datenbank konfigurieren, aber einmaliges Anmelden wird nicht unterstützt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (Azure AD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

Aktivieren von MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert ist.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivieren von MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie Advanced Threat Protection für Azure SQL-Datenbank, um Anomalien bei Aktivitäten zu erkennen, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen.

Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Lesen Sie die Informationen zu Advanced Threat Protection-Warnungen und potenziellen Warnungen:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Portal und die Azure-Ressourcenverwaltung nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Erstellen Sie einen Azure Active Directory-Administrator (AAD) für Ihren Server.

Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

So erstellen und konfigurieren Sie eine Azure AD-Instanz

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Konfigurieren Sie die AAD-Authentifizierung (Azure Active Directory) mit Azure SQL, und erstellen Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten. Senden Sie die Überwachungsprotokolle und Anmeldeprotokolle anschließend an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) Identity Protection und die Risikoerkennung, um automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

Anzeigen von Azure AD-Risikoanmeldungen:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, wird per Azure-Kunden-Lockbox eine Benutzeroberfläche bereitgestellt, auf der Kunden Anforderungen des Zugriffs auf Kundendaten prüfen und dann genehmigen oder ablehnen können.

Grundlegendes zu Kunden-Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein VNET/Subnetz getrennt, korrekt gekennzeichnet und mit einer NSG oder Azure Firewall geschützt werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen isoliert werden. Verwenden Sie Private Link. Stellen Sie Azure SQL-Datenbank in Ihrem VNET bereit, und stellen Sie eine private Verbindung her, indem Sie private Endpunkte verwenden.

Erstellen zusätzlicher Azure-Abonnements:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen:

https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Einrichten von Private Link für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Kennzeichnen Sie die Datenbank und die zugehörigen Ressourcen mit Tags als vertraulich, wenn mit Datenbanken in Azure SQL-Datenbank vertrauliche Informationen gespeichert oder verarbeitet werden. Konfigurieren Sie Private Link in Verbindung mit Diensttags für Netzwerksicherheitsgruppen auf Ihren Azure SQL-Datenbankinstanzen, um die Exfiltration von vertraulichen Informationen zu verhindern.

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Konfigurieren von Private Link und Netzwerksicherheitsgruppen zur Verhinderung der Exfiltration von Daten auf Ihren Instanzen von Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure SQL-Datenbank schützt Ihre Daten, indem diese bei der Übertragung mit Transport Layer Security verschlüsselt werden. SQL-Datenbank erzwingt die Verschlüsselung (SSL/TLS) jederzeit für alle Verbindungen. Hierdurch wird sichergestellt, dass alle Daten während der Übertragung zwischen Client und Server verschlüsselt werden, und zwar unabhängig von der Einstellung für „Encrypt“ oder „TrustServerCertificate“ in der Verbindungszeichenfolge.

Grundlegendes zu Azure SQL Encryption während der Übertragung:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Verwenden Sie die Azure SQL-Datenbank-Funktion zur Datenermittlung und -klassifizierung. Die Datenermittlung und -klassifizierung verfügt über erweiterte Funktionen für Azure SQL-Datenbank zum Ermitteln, Klassifizieren, Bezeichnen und Schützen der vertraulichen Daten in Ihren Datenbanken.

Verwenden der Datenermittlung und -klassifizierung für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD), um den Zugriff auf Instanzen von Azure SQL-Datenbank zu authentifizieren und zu steuern.

Integrieren von Azure SQL-Datenbank in Azure Active Directory für die Authentifizierung:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Steuern des Zugriffs in Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure SQL-Datenbank und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Transparente Datenverschlüsselung (Transparent Data Encryption, TDE) trägt zum Schutz von Azure SQL-Datenbank, einer verwalteten Azure SQL-Instanz und Azure Data Warehouse vor der Bedrohung durch schädliche Offlineaktivitäten durch die Verschlüsselung von ruhenden Daten bei. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind. TDE ist standardmäßig für alle neu bereitgestellten Datenbanken in SQL-Datenbank und SQL Managed Instance aktiviert. Der TDE-Verschlüsselungsschlüssel kann entweder von Microsoft oder vom Kunden verwaltet werden.

Verwalten von Transparent Data Encryption und Verwenden Ihrer eigenen Verschlüsselungsschlüssel:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure SQL-Datenbank und anderen kritischen bzw. verwandten Ressourcen vorgenommen werden.

Erstellen von Warnungen für Azure-Aktivitätsprotokollereignisse:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Aktivieren Sie Azure Defender für SQL für Azure SQL-Datenbank, und halten Sie sich an die Azure Security Center-Empfehlungen zur Durchführung von Sicherheitsrisikobewertungen auf Ihren Servern.

Durchführen von Sicherheitsrisikobewertungen für Azure SQL-Datenbank-Instanzen:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

So aktivieren Sie Azure Defender für SQL

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Nicht zutreffend. Diese Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Aktivieren Sie regelmäßig wiederkehrende Überprüfungen für Ihre Instanzen von Azure SQL-Datenbank. Hierdurch wird eine Sicherheitsrisikobewertung konfiguriert, um einmal pro Woche eine Überprüfung Ihrer Datenbank durchzuführen. Eine Zusammenfassung der Scanergebnisse wird an die angegebenen E-Mail-Adressen gesendet. Vergleichen Sie die Ergebnisse, um sicherzustellen, dass die Sicherheitsrisiken beseitigt wurden.

Exportieren eines Berichts zur Sicherheitsrisikobewertung in Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die Standardrisikobewertungen (Secure Score), die von Azure Security Center bereitgestellt werden.

Grundlegendes zu Secure Score von Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Azure SQL-Datenbank) innerhalb Ihrer Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

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

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen zusätzlicher Azure-Abonnements:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen:

https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Definieren Sie eine Liste mit den genehmigten Azure-Ressourcen und der genehmigten Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Anwendungen vorgesehen, die auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für App Service- oder Computeressourcen vorgesehen, auf denen Desktop- oder Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy- oder Azure Security Center-Empfehlungen für Azure SQL-Datenbank, um Sicherheitskonfigurationen für alle Azure-Ressourcen zu verwalten.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [deploy if not exist] ([Bereitstellen, falls nicht vorhanden]), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Code sicher zu speichern und zu verwalten.

Speichern von Code in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation zu Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.SQL“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Nutzen Sie Azure Security Center, um Baselineüberprüfungen für Azure SQL-Datenbank durchzuführen.

Umsetzen von Empfehlungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie Azure Key Vault zum Speichern von Verschlüsselungsschlüsseln für Transparent Data Encryption (TDE) von Azure SQL-Datenbank.

Schützen von vertraulichen Daten, die unter Azure SQL-Datenbank gespeichert sind, und Speichern der Verschlüsselungsschlüssel in Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AAD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Tutorial: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Konfigurieren von verwalteten Identitäten:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Implementieren Sie Credential Scanner, um Anmeldeinformationen in Ihrem Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Einrichten von Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure SQL-Datenbank usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Um Ihr Unternehmen vor Datenverlusten zu schützen, werden von Azure SQL-Datenbank automatisch wöchentliche vollständige Datenbanksicherungen, alle 12 Stunden differenzielle Datenbanksicherungen und alle 5 bis 10 Minuten Transaktionsprotokollsicherungen durchgeführt. Die Sicherungen werden für alle Dienstebenen mindestens 7 Tage im RA-GRS-Speicher vorgehalten. Alle Dienstebenen außer „Basic“ unterstützen konfigurierbare Aufbewahrungszeiträume von bis zu 35 Tagen für die Point-in-Time-Wiederherstellung.

Zur Einhaltung unterschiedlicher Konformitätsanforderungen können Sie verschiedene Aufbewahrungszeiträume für wöchentliche, monatliche oder jährliche Sicherungen auswählen. Der Speicherverbrauch hängt von der ausgewählten Häufigkeit der Sicherungen und von den Aufbewahrungsdauern ab.

Grundlegendes zu Sicherungen und Geschäftskontinuität mit Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Azure SQL-Datenbank erstellt automatisch die Datenbanksicherungen, für die eine Speicherdauer von mindestens 7 und maximal 35 Tagen gilt. Es wird georedundanter Azure-Speicher mit Lesezugriff (RA-GRS) verwendet, um sicherzustellen, dass diese auch dann beibehalten werden, wenn das Rechenzentrum nicht verfügbar ist. Diese Sicherungskopien werden automatisch erstellt. Aktivieren Sie bei Bedarf langfristige georedundante Sicherungen für Ihre Azure SQL-Datenbank-Instanzen.

Stellen Sie beim Verwenden der vom Kunden verwalteten Schlüssel für Transparent Data Encryption sicher, dass Ihre Schlüssel gesichert werden.

Grundlegendes zu Sicherungen in Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Sichern von Key Vault-Schlüsseln in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Stellen Sie regelmäßig sicher, dass die Inhalte in Azure Backup wiederhergestellt werden können. Testen Sie die Wiederherstellung von Inhalten ggf. in einem isolierten VLAN. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

Wiederherstellen von Key Vault-Schlüsseln in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Wiederherstellen von Azure SQL-Datenbank-Sicherungen per Point-in-Time-Wiederherstellung:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Aktivieren Sie das vorläufige Löschen in Azure Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

Aktivieren des vorläufigen Löschens in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen die Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen definiert sind.

Konfigurieren von Workflowautomatisierungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Sicherheitswarnungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

Informationen hierzu finden Sie in der folgenden Veröffentlichung des NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben.

Festlegen der Kontaktinformationen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

Konfigurieren des fortlaufenden Exports:

https://docs.microsoft.com/azure/security-center/continuous-export

Streamen von Warnungen in Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

Konfigurieren von „Workflowautomatisierung“ und Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../../security/benchmarks/security-baselines-overview.md).