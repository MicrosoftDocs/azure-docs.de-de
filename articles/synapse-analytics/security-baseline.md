---
title: Azure-Sicherheitsbaseline für Azure Synapse Analytics
description: Die Synapse Analytics-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Azure-Sicherheitsvergleichstest angegeben sind.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 972788e04e12553e7c05f30cc246c6fb562031a4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461366"
---
# <a name="azure-security-baseline-for-azure-synapse-analytics"></a>Azure-Sicherheitsbaseline für Azure Synapse Analytics

Die Azure-Sicherheitsbaseline für Azure Synapse Analytics enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Sichern Sie Ihre Azure SQL Server zu einem virtuellen Netzwerk mittels Private Link. Mit Azure Private Link können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste zugreifen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst verläuft über das Microsoft-Backbone-Netzwerk.

Wenn Sie eine Verbindung mit Ihrem Synapse SQL-Pool herstellen, können Sie alternativ den Bereich der ausgehenden Verbindung auf die SQL-Datenbank einschränken, indem Sie eine Netzwerksicherheitsgruppe verwenden. Deaktivieren Sie den gesamten Datenverkehr von Azure-Diensten zur SQL-Datenbank über den öffentlichen Endpunkt, indem Sie die Option „Azure-Dienste zulassen“ auf „AUS“ festlegen. Vergewissern Sie sich, dass in den Firewallregeln keine öffentlichen IP-Adressen 0zugelassen werden.

* [Grundlegendes zu Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Grundlegendes zu Private Link für Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Erstellen eines virtuellen Netzwerks](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Wenn Sie eine Verbindung mit Ihrem dedizierten SQL-Pool herstellen und die Flussprotokolle der Netzwerksicherheitsgruppen (NSGs) aktiviert haben, werden Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto gesendet.

Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Aktivieren der NSG-Flussprotokolle](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Aktivieren und Verwenden von Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Synapse SQL. ATP erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese ausgenutzt werden sollen. ATP kann ferner verschiedene Warnungen auslösen, z. B. „Potenzielle SQL-Einfügung“ oder „Zugriff von ungewöhnlichem Ort aus“. ATP ist Teil des Advanced Data Security-Angebots (ADS) und ist über das zentrale SQL ADS-Portal verfügbar und kann dort auch verwaltet werden.

Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken, die Ihrem Azure Synapse SQL zugeordnet sind, um dieses vor verteilten Denial-of-Service-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

* [Grundlegendes zu ATP für Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Aktivieren von Advanced Data Security für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [ADS-Übersicht](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Konfigurieren von DDoS-Schutz](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Wenn Sie eine Verbindung mit Ihrem dedizierten SQL-Pool herstellen und die Flussprotokolle der Netzwerksicherheitsgruppen (NSGs) aktiviert haben, senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch Flussprotokolle an einen Log Analytics-Arbeitsbereich senden oder an Event Hubs streamen. Aktivieren Sie Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

* [Aktivieren der NSG-Flussprotokolle](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Aktivieren von Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Synapse SQL. ATP erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese ausgenutzt werden sollen. ATP kann ferner verschiedene Warnungen auslösen, z. B. „Potenzielle SQL-Einfügung“ oder „Zugriff von ungewöhnlichem Ort aus“. ATP ist Teil des Advanced Data Security-Angebots (ADS) und ist über das zentrale SQL ADS-Portal verfügbar und kann dort auch verwaltet werden. Warnungen von ATP sind auch in Azure Security Center integriert.

* [Grundlegendes zu ATP für Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Bei Verwendung von Dienstendpunkten für Ihren dedizierten SQL-Pool ist eine ausgehende Verbindung mit den öffentlichen IP-Adressen von Azure SQL-Datenbank erforderlich: Netzwerksicherheitsgruppen (NSGs) müssen für IP-Adressen der Azure SQL-Datenbank geöffnet werden, um Verbindungen zuzulassen. Sie erreichen dies, indem Sie NSG-Diensttags für Azure SQL-Datenbank verwenden.

* [Grundlegendes zu Diensttags mit Dienstendpunkten für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [Grundlegendes zu Diensttags und Verwenden von Diensttags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Netzwerksicherheitskonfigurationen für Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool mit Azure Policy. Sie können den Namespace „Microsoft.Sql“ verwenden, um benutzerdefinierte Richtliniendefinitionen zu definieren, oder die integrierten Richtliniendefinitionen, die für den Netzwerkschutz in Bezug auf Azure SQL-Datenbank/-Server ausgelegt sind. Ein Beispiel für eine geeignete integrierte Netzwerksicherheitsrichtlinie für Azure SQL-Datenbank-Server ist: „SQL Server sollte einen VNET-Dienstendpunkt verwenden“.

Verwenden Sie Azure Blueprints, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Erstellen einer Azure-Blaupause](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihren dedizierten SQL-Pool beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Erstellen von Warnungen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben aber die Möglichkeit, die Zeitsynchronisierung für Ihre Computebereitstellungen zu aktualisieren.

* [Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Eine Überwachungsrichtlinie kann für eine spezifische Datenbank oder als Standardserverrichtlinie in Azure (das Azure Synapse hostet) definiert werden. Eine Serverrichtlinie wird auf alle vorhandenen und neu erstellten Datenbanken auf dem Server angewendet.

Wenn die Serverüberwachung aktiviert ist, gilt sie immer für die Datenbank. Die Datenbank wird unabhängig von den Datenbanküberwachungseinstellungen überwacht.

Wenn Sie die Überwachung aktivieren, können Sie diese in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto, Ihrem Log Analytics-Arbeitsbereich oder in Event Hubs schreiben.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Einrichten der Überwachung für Ihre Azure SQL-Ressourcen](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Durchführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Überwachung auf der Azure SQL-Serverebene für Ihren dedizierten SQL-Pool, und wählen Sie einen Speicherort für die Überwachungsprotokolle aus (Azure Storage, Log Analytics oder Event Hubs).

Die Überwachung kann sowohl auf der Datenbank- als auch auf der Serverebene aktiviert werden, und es wird vorgeschlagen, sie nur auf Serverebene zu aktivieren, es sei denn, Sie müssen eine gesonderte Datensenke oder Aufbewahrung für eine bestimmte Datenbank konfigurieren.

* [Aktivieren der Überwachung für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [Aktivieren der Überwachung für Ihren Server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [Unterschiede bei Überwachungsrichtlinien auf Serverebene und Datenbankebene](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie beim Speichern von Protokollen im Zusammenhang mit Ihrem dedizierten SQL-Pool in einem Speicherkonto, Log Analytics-Arbeitsbereich oder Event Hubs den Aufbewahrungszeitraum gemäß den Compliancebestimmungen Ihrer Organisation fest.

* [Verwalten des Azure Blob Storage-Lebenszyklus](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Festlegen der Protokollaufbewahrungsparameter in einem Log Analytics-Arbeitsbereich](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Erfassen von Streamingereignissen in Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie Advanced Threat Protection für Azure SQL-Datenbank in Verbindung mit Azure Security Center, um Warnungen vor ungewöhnlichen Aktivitäten im Zusammenhang mit Ihrer SQL-Datenbank zu erhalten. Alternativ können Sie Warnungen basierend auf Metrikwerten oder Einträgen des Azure-Aktivitätsprotokolls für Ihre SQL-Datenbank konfigurieren.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Grundlegendes zu Advanced Threat Protection und zu Warnungen für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Aktivieren von Advanced Data Security für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Konfigurieren von benutzerdefinierten Warnungen für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0&preserve-view=true)

* [Durchführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure SQL-Datenbank in Verbindung mit Azure Security Center, um die Überwachung auf anomale Aktivitäten zu ermöglichen und entsprechende Warnungen zu erhalten. ATP ist Teil des Advanced Data Security-Angebots (ADS) und ist im Portal über das zentrale SQL ADS verfügbar und kann dort auch verwaltet werden. ADS umfasst die Funktionen zur Ermittlung und Klassifizierung sensibler Daten, Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank darstellen können.

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren.

* [Grundlegendes zu Advanced Threat Protection und zu Warnungen für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Aktivieren von Advanced Data Security für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Verwalten von Warnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Durchführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht verfügbar. Für Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool wird die Antischadsoftwarelösung von Microsoft auf der zugrunde liegenden Plattform verwaltet.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht verfügbar. Ressourcen erzeugen keine DNS-Protokolle im Zusammenhang mit Ihrem dedizierten SQL-Pool.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht verfügbar. Die Befehlszeilenüberwachung gilt nicht für Azure Synapse SQL.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Die Authentifizierung von Benutzern erfolgt entweder mit Azure Active Directory oder mit SQL-Authentifizierung.

Wenn Sie Azure SQL erstmals bereitstellen, legen Sie eine Administratoranmeldung und ein zugehöriges Kennwort für diese Anmeldung fest. Dieses administrative Konto wird als „Serveradministrator“ bezeichnet. Sie können die Administratorkonten für eine Datenbank identifizieren, indem Sie das Azure-Portal öffnen und zur Registerkarte „Eigenschaften“ Ihres Servers oder Ihrer verwalteten Instanz navigieren. Sie können auch ein Azure AD-Administratorkonto mit vollständigen Administratorberechtigungen konfigurieren. Dies ist erforderlich, wenn Sie Azure Active Directory-Authentifizierung aktivieren möchten.

Verwenden Sie für Verwaltungsvorgänge die integrierten Azure-Rollen, die explizit zugewiesen werden müssen. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

* [Authentifizierung für SQL-Datenbank](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [Erstellen von Konten für Benutzer ohne Administratorberechtigungen](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [Verwenden eines Azure Active Directory-Kontos für die Authentifizierung](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

* [Verwalten vorhandener Anmeldungen und Administratorkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Integrierte Azure-Rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory verfügt nicht über das Konzept der Standardkennwörter. Wir empfehlen Ihnen, beim Bereitstellen eines dedizierten SQL-Pools die Option zum Integrieren der Authentifizierung in Azure Active Directory auszuwählen. Bei dieser Authentifizierungsmethode gibt der Benutzer einen Benutzerkontonamen an und fordert an, dass der Dienst die Anmeldeinformationen verwendet, die in Azure Active Directory (Azure AD) gespeichert sind.

* [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Grundlegendes zur Authentifizierung in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung von Azure Security Center, um die Anzahl der Administratorkonten zu überwachen, die sich über Azure Active Directory anmelden.

Öffnen Sie das Azure-Portal, und navigieren Sie zur Registerkarte „Eigenschaften“ Ihres Servers oder Ihrer verwalteten Instanz, um die Administratorkonten für eine Datenbank zu identifizieren.

* [Grundlegendes zu Identität und Zugriff im Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Verwalten vorhandener Anmeldungen und Administratorkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie eine Azure-App-Registrierung (Dienstprinzipal), um ein Token abzurufen, das für die Interaktion mit Ihrem Data Warehouse auf der Steuerungsebene (Azure-Portal) mittels API-Aufrufen verwendet werden kann.

* [Aufrufen von Azure-REST-APIs mit Postman](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrieren Ihrer Clientanwendung mit Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Synapse SQL-REST-API-Informationen](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Überwachen von Identität und Zugriff in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Grundlegendes zu MFA in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit Multi-Factor Authentication (MFA, mehrstufige Authentifizierung), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert ist.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie Advanced Threat Protection für Azure SQL-Datenbank in Verbindung mit Azure Security Center, um Anomalien bei Aktivitäten zu erkennen, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen.

Bei der SQL Server-Überwachung können Sie Serverüberwachungen erstellen, die Serverüberwachungsspezifikationen für Ereignisse auf Serverebene und Datenbanküberwachungsspezifikationen für Ereignisse auf Datenbankebene enthalten. Überwachte Ereignisse können in die Ereignisprotokolle oder Überwachungsdateien geschrieben werden.

* [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Lesen von Informationen zu Advanced Threat Protection-Warnungen und potenziellen Warnungen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [Grundlegendes zu Anmeldungen und Benutzerkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [Grundlegendes zur SQL Server-Überwachung](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15&preserve-view=true)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Portal und die Azure-Ressourcenverwaltung nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Erstellen Sie einen Azure Active Directory-Administrator (AD) für den Azure SQL-Datenbank-Server in Ihrem dedizierten SQL-Pool.

* [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Erstellen und Konfigurieren einer Azure AD-Instanz](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure Active Directory, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

Wenn Sie die SQL-Authentifizierung verwenden, erstellen Sie eigenständige Datenbankbenutzer in der Datenbank. Stellen Sie sicher, dass Sie mindestens einem Datenbankbenutzer eine benutzerdefinierte Datenbankrolle mit spezifischen Berechtigungen zuweisen, die der jeweiligen Benutzergruppe entspricht.

* [Verwenden von Zugriffsüberprüfungen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Grundlegendes zu Anmeldungen und Benutzerkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Konfigurieren Sie die Azure Active Directory-Authentifizierung (AD) mit Azure SQL, und aktivieren Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten, wobei Sie die Überwachungsprotokolle und Anmeldeprotokolle anschließend an einen Log Analytics-Arbeitsbereich senden lassen. Konfigurieren Sie die gewünschten Warnungen in Log Analytics.

Wenn Sie die SQL-Authentifizierung verwenden, erstellen Sie eigenständige Datenbankbenutzer in der Datenbank. Stellen Sie sicher, dass Sie mindestens einem Datenbankbenutzer eine benutzerdefinierte Datenbankrolle mit spezifischen Berechtigungen zuweisen, die der jeweiligen Benutzergruppe entspricht.

* [Verwenden von Zugriffsüberprüfungen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Grundlegendes zu Anmeldungen und Benutzerkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Funktionen zum Identitätsschutz und zur Risikoerkennung von Azure Active Directory (Azure AD) können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel integrieren und erfassen.

Wenn Sie die SQL-Authentifizierung verwenden, erstellen Sie eigenständige Datenbankbenutzer in der Datenbank. Stellen Sie sicher, dass Sie mindestens einem Datenbankbenutzer eine benutzerdefinierte Datenbankrolle mit spezifischen Berechtigungen zuweisen, die der jeweiligen Benutzergruppe entspricht.

* [Anzeigen von Azure AD-Risikoanmeldungen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [Grundlegendes zu Anmeldungen und Benutzerkonten in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: In Supportszenarien, bei denen Microsoft auf Daten im Zusammenhang mit der Azure SQL-Datenbank in Ihrem dedizierten SQL-Pool zugreifen muss, bietet Kunden-Lockbox in Azure eine Oberfläche, auf der Sie Anforderungen von Datenzugriff prüfen und dann genehmigen oder ablehnen können.

* [Grundlegendes zu Kunden-Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Datenermittlung und -klassifizierung sind in Azure Synapse SQL integriert. Sie bietet erweiterte Funktionen zum Ermitteln, Klassifizieren und Bezeichnen vertraulicher Daten in Ihren Datenbanken und zum Erstellen von Berichten zu diesen.

* [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Grundlegendes zur Datenermittlung und -klassifizierung](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein virtuelles Netzwerk oder Subnetz getrennt, entsprechend gekennzeichnet und in einer Netzwerksicherheitsgruppe oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen isoliert werden. Verwenden Sie Private Link. Stellen Sie Ihren Azure SQL Server in einem virtuellen Netzwerk bereit, und stellen Sie eine sichere Verbindung mittels Private Link her.

* [Erstellen zusätzlicher Azure-Abonnements](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Erstellen von Verwaltungsgruppen](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Einrichten von Private Link für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Kennzeichnen Sie jede Azure SQL-Datenbank in Ihrem dedizierten SQL-Pool, die vertrauliche Informationen speichert oder verarbeitet, und ihre zugehörigen Ressourcen mithilfe von Tags als vertraulich. Konfigurieren Sie Private Link in Verbindung mit Diensttags für Netzwerksicherheitsgruppen (NSG) auf Ihren Azure SQL-Datenbankinstanzen, um die Exfiltration von vertraulichen Informationen zu verhindern.

Zusätzlich erkennen Advanced Threat Protection für Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädigende Zugriffs- oder Exploitversuche auf Datenbanken hinweisen.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Konfigurieren von Private Link und Netzwerksicherheitsgruppen zur Verhinderung der Exfiltration von Daten auf Ihren Instanzen von Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Grundlegendes zu Advanced Threat Protection für Azure SQL-Datenbank](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Grundlegendes zum Schutz von Kundendaten in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure SQL-Datenbank schützt Ihre Daten, indem diese bei der Übertragung mit Transport Layer Security verschlüsselt werden. SQL Server erzwingt die Verschlüsselung (SSL/TLS) jederzeit für alle Verbindungen. Hierdurch wird sichergestellt, dass alle Daten während der Übertragung zwischen Client und Server verschlüsselt werden, und zwar unabhängig von der Einstellung für „Encrypt“ oder „TrustServerCertificate“ in der Verbindungszeichenfolge.

* [Grundlegendes zu Azure SQL-Verschlüsselung während der Übertragung](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Verwenden Sie die Azure Synapse SQL-Datenbank-Funktion zur Datenermittlung und -klassifizierung. Die Datenermittlung und -klassifizierung verfügen über erweiterte Funktionen für Azure SQL-Datenbank zum Ermitteln, Klassifizieren, Bezeichnen und Schützen der vertraulichen Daten in Ihren Datenbanken.

Die Datenermittlung und -klassifizierung ist Teil des Advanced Data Security-Angebots. Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf und die Verwaltung von Datenermittlung und -klassifizierung ist über das zentrale SQL ADS-Portal möglich.

Außerdem können Sie eine Richtlinie für die dynamische Datenmaskierung (DDM) im Azure-Portal einrichten. Von der DDM-Empfehlungs-Engine werden bestimmte Felder Ihrer Datenbank als potenzielle Felder mit vertraulichen Daten angegeben, bei denen es sich um gute Kandidaten für die Maskierung handelt.

* [Verwenden der Datenermittlung und -klassifizierung für Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Grundlegendes zur dynamischen Datenmaskierung für Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf Azure SQL-Datenbanken in Ihrem dedizierten SQL-Pool zu verwalten.

Die Autorisierung wird durch die Rollenmitgliedschaften und Berechtigungen auf Objektebene der Datenbank Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen.

* [Integrieren von Azure SQL Server in Azure Active Directory für die Authentifizierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Steuern des Zugriffs in Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Grundlegendes zu Authentifizierung und Autorisierung in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht verfügbar. Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Synapse SQL und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Transparent Data Encryption (TDE) ist ein zusätzlicher Schutz für Azure Synapse SQL vor der Bedrohung durch bösartige Offlineaktivitäten, indem ruhende Daten verschlüsselt werden. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind. In Azure ist die TDE standardmäßig so eingerichtet, dass der DEK durch ein integriertes Serverzertifikat geschützt ist. Alternativ können Sie vom Kunden verwaltete transparente Datenverschlüsselung verwenden, was auch als BYOK-Unterstützung (Bring Your Own Key) für TDE bezeichnet wird. In diesem Szenario ist die TDE-Schutzvorrichtung, die den DEK verschlüsselt, ein kundenseitig verwalteter asymmetrischer Schlüssel, der in einer kundeneigenen und kundenseitig verwalteten Azure Key Vault-Instanz – dem cloudbasierten externen Schlüsselverwaltungssystem von Azure – gespeichert ist und diesen Schlüsseltresor niemals verlässt.

* [Grundlegendes zu dienstseitig verwalteter transparenter Datenverschlüsselung](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [Grundlegendes zu kundenseitig verwalteter transparenter Datenverschlüsselung (TDE)](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Aktivieren von TDE mithilfe Ihres eigenen Schlüssels](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Synapse SQL-Pools und anderen kritischen bzw. verwandten Ressourcen vorgenommen werden.

Darüber hinaus können Sie Warnungen für Datenbanken in Ihrem SQL Synapse-Pool mithilfe des Azure-Portals einrichten. Warnungen können Ihnen eine E-Mail senden oder einen Webhook aufrufen, wenn bei einer bestimmten Metrik (beispielsweise bei der Datenbankgröße oder bei der CPU-Auslastung) der Schwellenwert erreicht wird.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Erstellen von Warnungen für Azure SQL Synapse](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Aktivieren Sie Advanced Data Security, und halten Sie sich an die Azure Security Center-Empfehlungen zur Durchführung von Sicherheitsrisikobewertungen in Ihren Azure SQL-Datenbanken.

* [Durchführen von Sicherheitsrisikobewertungen auf Ihren Instanzen von Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Aktivieren von Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Die Sicherheitsrisikobewertung ist ein in Azure Synapse SQL integrierter Überprüfungsdienst. Der Dienst verwendet eine Wissensdatenbank mit Regeln zur Kennzeichnung von Sicherheitsrisiken. Er hebt Abweichungen von bewährten Methoden hervor (beispielsweise Fehlkonfigurationen, zu hohe Berechtigungen oder ungeschützte vertrauliche Daten). Die Sicherheitsrisikobewertung kann über das zentrale SQL Advanced Data Security-Portal (ADS) geöffnet und verwaltet werden.

* [Verwalten und Exportieren von Überprüfungen zur Sicherheitsrisikobewertung im SQL ADS-Portal](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die Standardrisikobewertungen (Secure Score), die von Azure Security Center bereitgestellt werden.

Datenermittlung und -klassifizierung sind in Azure Synapse SQL integriert. Sie bietet erweiterte Funktionen zum Ermitteln, Klassifizieren und Bezeichnen vertraulicher Daten in Ihren Datenbanken und zum Erstellen von Berichten zu diesen.

* [Grundlegendes zum Secure Score des Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [Grundlegendes zur Datenermittlung und -klassifizierung](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Obwohl klassische Azure-Ressourcen über Azure Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Anzeigen Ihrer Azure-Abonnements](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true)

* [Grundlegendes zu Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

* [Erstellen zusätzlicher Azure-Abonnements](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Erstellen von Verwaltungsgruppen](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Definieren Sie eine Liste genehmigter Azure-Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Erstellen von Abfragen mit Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

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

* [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Anwendungen vorgesehen, die auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Alle Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool, die für den Geschäftsbetrieb erforderlich sind, aber ein höheres Risiko für das Unternehmen darstellen können, sollten innerhalb des eigenen virtuellen Computers und/oder virtuellen Netzwerks isoliert und entweder mit einer Azure Firewall oder einer Netzwerksicherheitsgruppe ausreichend abgesichert werden.

* [Erstellen eines virtuellen Netzwerks](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Sql“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration von Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure-Datenbanken/-Server verwenden, z. B.:
- Bereitstellen von Bedrohungserkennung auf SQL-Servern
- SQL Server sollte einen VNET-Dienstendpunkt verwenden.

* [Anzeigen verfügbarer Azure Policy-Aliase](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true)

* [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Grundlegendes zu Azure Policy-Auswirkungen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [Dokumentation zu Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Nicht verfügbar. Azure Synapse SQL verfügt über keine konfigurierbaren Sicherheitseinstellungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Security Center, um Baselineüberprüfungen für alle Ressourcen im Zusammenhang mit Ihrem dedizierten SQL-Pool durchzuführen.

* [Umsetzen von Empfehlungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Transparent Data Encryption (TDE) mit vom kundenseitig verwalteten Schlüsseln in Azure Key Vault erlaubt die Verschlüsselung des automatisch generierten Datenbank-Verschlüsselungsschlüssels (Database Encryption Key, DEK) mit einem kundenseitig verwalteten asymmetrischen Schlüssel, der als TDE-Schutzvorrichtung bezeichnet wird. Dies wird in der Regel auch als „Bring Your Own Key-Unterstützung“ (BYOK) für Transparent Data Encryption bezeichnet. Im BYOK-Szenario wird die TDE-Schutzvorrichtung in einer Instanz von Azure Key Vault gespeichert, die dem Kunden gehört und von ihm verwaltet wird. Stellen Sie außerdem sicher, dass das vorläufige Löschen in Azure Key Vault aktiviert ist.

* [Aktiveren von TDE mithilfe eines kundenseitig verwalteten Schlüssels aus Azure Key Vault](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [Aktivieren des vorläufigen Löschens in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Tutorial: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure SQL](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [Konfigurieren von verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Implementieren Sie Credential Scanner, um Anmeldeinformationen in Ihrem Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Synapse SQL) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure SQL Server usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

* [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Momentaufnahmen Ihres dedizierten SQL-Pools werden im Verlauf des Tages automatisch erstellt, und die generierten Wiederherstellungspunkte sind für sieben Tage verfügbar. Dieser Aufbewahrungszeitraum kann nicht geändert werden. Ein dedizierter SQL-Pool unterstützt eine RPO (Recovery Point Objective) von acht Stunden. Sie können das Data Warehouse in der primären Region anhand einer beliebigen Momentaufnahme wiederherstellen, die in den vergangenen sieben Tagen erstellt wurde. Beachten Sie, dass Sie Momentaufnahmen ggf. auch manuell auslösen können.

* [Sicherung und Wiederherstellung im dedizierten SQL-Pool](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Momentaufnahmen Ihres Data Warehouse werden im Verlauf des Tages automatisch erstellt, und die generierten Wiederherstellungspunkte sind für sieben Tage verfügbar. Dieser Aufbewahrungszeitraum kann nicht geändert werden. Ein dedizierter SQL-Pool unterstützt eine RPO (Recovery Point Objective) von acht Stunden. Sie können das Data Warehouse in der primären Region anhand einer beliebigen Momentaufnahme wiederherstellen, die in den vergangenen sieben Tagen erstellt wurde. Beachten Sie, dass Sie Momentaufnahmen ggf. auch manuell auslösen können.

Wenn Sie einen kundenseitig verwalteten Schlüssel verwenden, um Ihren Datenbank-Verschlüsselungsschlüssel zu verschlüsseln, stellen Sie sicher, dass Ihr Schlüssel gesichert wird.

* [Sicherung und Wiederherstellung im dedizierten SQL-Pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Sichern von Azure Key Vault-Schlüsseln](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der kundenseitig verwalteten Schlüssel

**Leitfaden**: Testen Sie regelmäßig Ihre Wiederherstellungspunkte, um sicherzustellen, dass Ihre Momentaufnahmen gültig sind. Um einen vorhandenen dedizierten SQL-Pool aus einem Wiederherstellungspunkt wiederherzustellen, können Sie entweder das Azure-Portal oder die PowerShell verwenden. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

* [Wiederherstellen von Azure Key Vault-Schlüsseln](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

* [Sicherung und Wiederherstellung im dedizierten SQL-Pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Wiederherstellen eines vorhandenen dedizierten SQL-Pools](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: In Azure SQL-Datenbank können Sie eine Einzel- oder Pooldatenbank mit einer Richtlinie für die Langzeitaufbewahrung (Long-Term Retention, LTR) konfigurieren, um die Datenbanksicherungen automatisch in separaten Azure Blob Storage-Containern bis zu 10 Jahre lang aufzubewahren. Daten in Azure Storage werden auf transparente Weise mit der AES-256-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – ver- und entschlüsselt und sind mit dem FIPS 140-2-Standard konform.

Standardmäßig werden Daten in einem Speicherkonto mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihrer Daten nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten. Wenn Sie Ihre eigenen Schlüssel mit Key Vault verwalten, stellen Sie sicher, dass vorläufiges Löschen aktiviert ist.

* [Verwalten der langfristigen Aufbewahrung von Sicherungen in Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [Aktivieren des vorläufigen Löschens in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center-Überwachung**: –

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen die Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen definiert sind.

* [Leitfaden zu Planung und Betrieb](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

* [Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [Informationen hierzu finden Sie in der folgenden Veröffentlichung des NIST: des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft wendet sich unter den für Sicherheitsvorfälle angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben.

* [Festlegen der Kontaktinformationen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Streamen von Warnungen in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Anleitung**: * [Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.).

* Unter [diesem Link](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) finden Sie weitere Informationen zur Microsoft-Strategie und zur Durchführung von Red Teaming und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](/azure/security/benchmarks/overview).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).
