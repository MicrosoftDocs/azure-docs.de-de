---
title: Azure-Sicherheitsbaseline für Azure Database for MySQL
description: Die Azure Database for MySQL-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 477228f607f620f22ffab3a3435ebd850df0324a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492528"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Azure-Sicherheitsbaseline für Azure Database for MySQL

Die Azure-Sicherheitsbaseline für Azure Database for MySQL enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Konfigurieren Sie Private Link für Azure Database for MySQL mit privaten Endpunkten. Private Link ermöglicht das Herstellen von Verbindungen mit verschiedenen PaaS-Diensten in Azure über einen privaten Endpunkt. Im Prinzip macht Azure Private Link Azure-Dienste in Ihrem privaten virtuellen Netzwerk (VNET) verfügbar. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und der MySQL-Instanz verläuft über das Microsoft-Backbone-Netzwerk.

Alternativ können Sie VNET-Dienstendpunkte verwenden, um den Netzwerkzugriff auf Ihre Azure Database for MySQL-Implementierungen zu schützen und einzuschränken. VNET-Regeln sind eine Firewallsicherheitsfunktion, die steuert, ob Ihr Azure Database for MySQL-Server Nachrichten zulässt, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden.

Sie können auch Ihren Azure Database for MySQL-Server mit Firewallregeln schützen. Die Serverfirewall verhindert jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Zum Konfigurieren der Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Serverebene erstellen.

- [Konfigurieren von Private Link für Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Erstellen und Verwalten von VNET-Dienstendpunkten und VNET-Regeln in Azure Database for MySQL](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [Konfigurieren von Azure Database for MySQL-Firewallregeln](howto-manage-firewall-using-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Wenn Ihre Azure Database for MySQL-Instanz auf einem privaten Endpunkt geschützt ist, können Sie virtuelle Computer im gleichen virtuellen Netzwerk bereitstellen. Sie können eine Netzwerksicherheitsgruppe (NSG) verwenden, um das Risiko einer Datenexfiltrierung zu verringern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Konfigurieren von Private Link für Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Verwenden Sie Advanced Threat Protection für Azure Database for MySQL. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.

Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken für Ihre Azure Database for MySQL-Instanzen, um sich vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

- [Konfigurieren von Advanced Threat Protection für Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Wenn Ihre Azure Database for MySQL-Instanz auf einem privaten Endpunkt geschützt ist, können Sie virtuelle Computer im gleichen virtuellen Netzwerk bereitstellen. Sie können dann eine Netzwerksicherheitsgruppe (NSG) konfigurieren, um das Risiko einer Datenexfiltrierung zu verringern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Verwenden Sie Advanced Threat Protection für Azure Database for MySQL. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.

- [Konfigurieren von Advanced Threat Protection für Azure Database for MySQL](howto-database-threat-protection-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihre Azure Database for MySQL-Instanzen benötigen, VNET-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. SQL.WestUs) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Hinweis: Azure Database for MySQL verwendet das Diensttag „Microsoft.Sql“.

- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

- [Grundlegendes zur Verwendung von Diensttags für Azure Database for MySQL](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkeinstellungen und Netzwerkressourcen, die Ihren Azure Database for MySQL-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.DBforMySQL“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Database for MySQL-Instanzen zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen verwenden, die sich auf das Netzwerk oder Ihre Azure Database for MySQL-Instanzen beziehen, z. B.:

- DDoS Protection Standard sollte aktiviert sein.

- Erzwingen einer SSL-Verbindung muss für MySQL-Datenbankserver aktiviert sein

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/index.md)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Ressourcen im Zusammenhang mit der Netzwerksicherheit und dem Datenfluss für Ihre Azure Database for MySQL-Instanzen, um Metadaten und eine logische Organisation bereitzustellen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen für das Tagging, z. B. **Tag und zugehörigen Wert erzwingen**, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Database for MySQL-Instanzen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle, die für Azure-Ressourcen wie Azure Database for MySQL verwendet wird, für Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen, Serverprotokolle und Erfassungsprotokolle, um die von Ihren Azure Database for MySQL-Instanzen generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

- [Grundlegendes zu Serverprotokollen für Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen für Ihre Azure Database for MySQL-Instanzen für den Zugriff auf Protokolle mit Metriken für Überwachung, Abfragetempo und MySQL. Stellen Sie sicher, dass das MySQL-Überwachungsprotokoll explizit aktiviert wird. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente. Sie können auch die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll aktivieren und die Protokolle an denselben Log Analytics-Arbeitsbereich oder dasselbe Speicherkonto senden.

- [Grundlegendes zu Serverprotokollen für Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Konfigurieren von und Zugreifen auf Protokolle zu langsamen Abfragen für Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Konfigurieren von und Zugreifen auf Überwachungsprotokolle für Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs, der für die Azure Database for MySQL-Protokolle verwendet wird, gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Azure-Ressourcenprotokolle](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle Ihrer Azure Database for MySQL-Instanzen auf anormales Verhalten. Verwenden Sie Log Analytics von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Tutorial: Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für Azure Database for MySQL. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.

Außerdem können Sie Serverprotokolle und Diagnoseeinstellungen für MySQL aktivieren und Protokolle an einen Log Analytics-Arbeitsbereich senden. Integrieren Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel, da er eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitstellt. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden.

- [Aktivieren von Advanced Threat Protection für Azure Database for MySQL (Vorschau)](howto-database-threat-protection-portal.md)

- [Grundlegendes zu Serverprotokollen für Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Konfigurieren von und Zugreifen auf Protokolle zu langsamen Abfragen für Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Konfigurieren von und Zugreifen auf Überwachungsprotokolle für Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-log.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Database for MySQL verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Database for MySQL verarbeitet keine Protokolle zu DNS und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie einen Bestand der Benutzerkonten, die über Administratorzugriff auf die Verwaltungsebene (Azure-Portal) Ihrer Azure Database for MySQL-Instanzen verfügen. Verwalten Sie außerdem einen Bestand der Administratorkonten, die Zugriff auf die Datenebene (in der Datenbank selbst) Ihrer Azure Database for MySQL-Instanzen haben. (Beim Erstellen des MySQL-Servers geben Sie Anmeldeinformationen für einen Benutzer mit Administratorrechten ein. Mit diesem Administratorkonto können Sie zusätzliche MySQL-Benutzer erstellen.)

Azure Database for MySQL unterstützt keine integrierte rollenbasierte Zugriffssteuerung. Sie können jedoch benutzerdefinierte Rollen auf Basis bestimmter Ressourcenanbieteroptionen erstellen.

- [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md) 

- [Grundlegendes zu Vorgängen bei Azure Database for MySQL-Ressourcenanbietern](../role-based-access-control/resource-provider-operations.md#microsoftdbformysql)

- [Grundlegendes zur Zugriffsverwaltung für Azure Database for MySQL](concepts-security.md#access-management)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

Bei der Erstellung von Azure Database for MySQL-Ressourcen erzwingt Azure die Erstellung eines administrativen Benutzerkontos mit einem sicheren Kennwort. Nachdem die MySQL-Instanz erstellt wurde, können Sie jedoch das erste erstellte Serveradministratorkonto verwenden, um weitere Benutzer zu erstellen und diesen Administratorzugriff zu gewähren. Stellen Sie beim Erstellen dieser Konten sicher, dass Sie für jedes Konto ein anderes, sicheres Kennwort konfigurieren.

- [Erstellen zusätzlicher Konten für Azure Database for MySQL](howto-create-users.md)

- [Aktualisieren des Administratorkennworts](howto-create-manage-server-portal.md#update-admin-password)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten, die Zugriff auf Ihre Azure Database for MySQL-Instanzen haben. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

- [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md)

- [Grundlegendes zum Erstellen von Administratorbenutzern in Azure Database for MySQL](howto-create-users.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Die Anmeldung bei Azure Database for MySQL wird sowohl über Angaben für Benutzername/Kennwort, die direkt in der Datenbank konfiguriert werden, als auch über eine Azure Active Directory-Identität (AAD) und Verwendung eines Azure AD-Tokens für die Herstellung einer Verbindung unterstützt. Bei Verwenden eines Azure AD-Tokens werden verschiedene Methoden unterstützt, wie z. B. ein Azure AD-Benutzer, eine Azure AD-Gruppe oder eine Azure AD-Anwendung, die eine Verbindung mit der Datenbank herstellt.

Getrennt davon ist der Zugriff auf die Steuerungsebene von MySQL über die REST-API verfügbar, wobei einmaliges Anmelden unterstützt wird. Legen Sie den Autorisierungsheader für Ihre Anforderungen auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abrufen.

- [Verwenden von Azure Active Directory für die Authentifizierung bei Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Grundlegendes zur REST-API für Azure Database for MySQL](/rest/api/mysql/)

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center. Wenn Sie Azure AD-Token für die Anmeldung bei Ihrer Datenbank verwenden, können Sie für Datenbankanmeldungen eine mehrstufige Authentifizierung anfordern.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Verwenden von Azure Active Directory für die Authentifizierung bei Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für Azure Database for MySQL, um Warnungen bei verdächtigen Aktivitäten zu generieren.

Darüber hinaus können Sie Azure AD Privileged Identity Management (PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung verwenden.

Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen.

- [Konfigurieren von Advanced Threat Protection für Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Portal und Azure Resource Manager nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

Für die Anmeldung bei Azure Database for MySQL wird empfohlen, Azure AD zu verwenden und ein Azure AD-Token für das Herstellen einer Verbindung zu verwenden. Bei Verwenden eines Azure AD-Tokens werden verschiedene Methoden unterstützt, wie z. B. ein Azure AD-Benutzer, eine Azure AD-Gruppe oder eine Azure AD-Anwendung, die eine Verbindung mit der Datenbank herstellt.

Azure AD-Anmeldeinformationen können auch für die Verwaltung auf Verwaltungsebene (z. B. Azure-Portal) zum Steuern von MySQL-Administratorkonten verwendet werden.

- [Verwenden von Azure Active Directory für die Authentifizierung bei Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie die Azure Active Directory-Protokolle, um veraltete Konten zu ermitteln, die u. U. Azure Database for MySQL-Administratorrollen enthalten können. Verwenden Sie zusätzlich Azure-Identitätszugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen, die für den Zugriff auf Azure Database for MySQL verwendet werden könnten, und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff sollte regelmäßig überprüft werden (z. B. alle 90 Tage), um sicherzustellen, dass nur die gewünschten Benutzer weiterhin Zugriff haben.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure Database for MySQL und Azure Active Directory, und senden Sie alle Protokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie gewünschte Warnungen (z. B. fehlgeschlagene Authentifizierungsversuche) in Log Analytics.

- [Konfigurieren von und Zugreifen auf Protokolle zu langsamen Abfragen für Azure Database for MySQL](./howto-configure-server-logs-in-portal.md)

- [Konfigurieren von und Zugreifen auf Überwachungsprotokolle für Azure Database for MySQL](./howto-configure-audit-logs-portal.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für Azure Database for MySQL, um Warnungen bei verdächtigen Aktivitäten zu generieren.

Verwenden Sie Azure Active Directory Identity Protection- und Risikoerkennungsfeatures, um automatische Antworten auf erkannte verdächtige Aktionen zu konfigurieren. Sie können automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren.

Außerdem können Sie Protokolle zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Konfigurieren von Advanced Threat Protection für Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Was ist Azure Active Directory Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht zutreffend. Kunden-Lockbox wird für Azure Database for MySQL nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags, um die Nachverfolgung von Azure Database for MySQL-Instanzen oder ähnlichen Ressourcen, die vertrauliche Informationen speichern oder verarbeiten, zu unterstützen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Verwenden Sie eine Kombination von Private Link, Dienstendpunkten und/oder MySQL-Firewallregeln, um den Netzwerkzugriff auf Ihre Azure Database for MySQL-Instanzen zu isolieren und einzuschränken.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Konfigurieren von Private Link für Azure Database for MySQL](concepts-data-access-security-private-link.md)

- [Erstellen und Verwalten von VNET-Dienstendpunkten und VNET-Regeln in Azure Database for MySQL](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [Konfigurieren von Azure Database for MySQL-Firewallregeln](concepts-firewall-rules.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Wenn Sie Azure-VMs für den Zugriff auf Azure Database for MySQL-Instanzen verwenden, können Sie Private Link, MySQL-Netzwerkkonfigurationen, Netzwerksicherheitsgruppen und Diensttags verwenden, um die Möglichkeit einer Datenexfiltrierung zu verringern.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Database for MySQL und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

- [Minimieren des Risikos einer Datenexfiltration für Azure Database for MySQL](concepts-data-access-security-private-link.md#data-exfiltration-prevention)

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Database for MySQL unterstützt die Verbindung Ihres MySQL-Servers mit Clientanwendungen unter Verwendung von Secure Sockets Layer (SSL). Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird. Stellen Sie im Azure-Portal sicher, dass „SSL-Verbindung erzwingen“ für alle Azure Database for MySQL-Instanzen standardmäßig aktiviert ist.

Derzeit werden die TLS-Versionen TLS 1.0, TLS 1.1, TLS 1.2 für Azure Database for MySQL unterstützt.

- [Konfigurieren der Verschlüsselung während der Übertragung für Azure Database for MySQL](concepts-ssl-connection-security.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Database for MySQL noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf die Azure Database for MySQL-Steuerungsebene (z. B. Azure-Portal) zu steuern. Verwenden Sie für den Zugriff auf die Datenebene (in der Datenbank selbst) SQL-Abfragen, um Benutzer zu erstellen und Benutzerberechtigungen zu konfigurieren. Azure RBAC wirkt sich nicht auf Benutzerberechtigungen in der Datenbank aus.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Konfigurieren des Benutzerzugriffs mit SQL für Azure Database for MySQL](howto-create-users.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Database for MySQL und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Der Azure Database for MySQL-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (mit Ausnahme von temporären Dateien, die während der Ausführung von Abfragen erstellt werden). Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.

Datenverschlüsselung mit vom Kunden verwalteten Schlüsseln für Azure Database for MySQL ermöglicht Ihnen BYOK (Bring Your Own Key) für den Schutz von Daten im Ruhezustand. Derzeit müssen Sie den Zugriff anfordern, um diese Funktion verwenden zu können. Wenden Sie sich zu diesem Zweck an:

AskAzureDBforMySQL@service.microsoft.com

- [Grundlegendes zur Verschlüsselung von ruhenden Daten für Azure Database for MySQL](concepts-security.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln für Azure Database for MySQL](concepts-data-encryption-mysql.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Database for MySQL und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Empfehlungen in Azure Security Center zum Schützen Ihrer Azure Database for MySQL-Instanzen und der damit verbundenen Ressourcen.

Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Database for MySQL unterstützen.

- [Grundlegendes zu Azure Security Center-Empfehlungen](../security-center/recommendations-reference.md)

- [Funktionsabdeckung für Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Database for MySQL unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Azure Database for MySQL-Instanzen) innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure Database for MySQL-Instanzen und andere zugehörige Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie ggf. Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Database for MySQL-Instanzen und zugehörigen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dadurch können die Erstellung von und Änderungen an Ressourcen in einer Hochsicherheitsumgebung, z. B. Instanzen von Azure Database for MySQL, die vertrauliche Informationen enthält, verhindert werden.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure Database for MySQL-Instanzen mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace **Microsoft.DBforMySQL**, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Database for MySQL-Instanzen zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen verwenden, die sich auf Ihre Azure Database for MySQL-Instanzen beziehen, z. B.:

Erzwingen einer SSL-Verbindung muss für MySQL-Datenbankserver aktiviert sein

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen für Azure Database for MySQL-Instanzen oder zugehörige Ressourcen verwenden.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DBforMySQL“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace **Microsoft.DBforMySQL**, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie Azure Policy ([Überwachen], [Verweigern] und [Bereitstellen, falls nicht vorhanden]), um Konfigurationen für Ihre Azure Database for MySQL-Instanzen und zugehörige Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Azure Database for MySQL-Instanzen ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Database for MySQL-Geheimnissen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

- [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Azure Database for MySQL-Instanzen unterstützen zurzeit keine Azure Active Directory-Authentifizierung für den Zugriff auf Datenbanken.  Beim Erstellen der Azure Database for MySQL-Instanz geben Sie Anmeldeinformationen für einen Administratorbenutzer ein. Dieser Administrator dient zum Erstellen zusätzlicher Datenbankbenutzer.  

Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Azure Database for MySQL-Instanzen ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um Anmeldeinformationen für Azure Database for MySQL-Instanzen zu speichern und abzurufen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Database for SQL) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Database for MySQL) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in Azure-Ressourcen ohne Computeaufgaben hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Database for MySQL) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: –

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Azure Database for MySQL nimmt Sicherungen der Datendateien und der Transaktionsprotokolle vor. Abhängig von der unterstützten maximalen Speichergröße werden entweder vollständige oder differenzielle Sicherungen (Server mit maximal 4 TB Speicher) oder Sicherungsmomentaufnahmen (Server mit bis zu 16 TB Speicher) angelegt. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

- [Grundlegendes zu Sicherungen für Azure Database for MySQL](concepts-backup.md)

- [Grundlegendes zur Erstkonfiguration für Azure Database for MySQL](tutorial-design-database-using-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Azure Database for MySQL erstellt automatisch Serversicherungen und speichert diese je nach Wahl des Benutzers entweder in lokalem oder georedundantem Speicher. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden. 

Wenn Sie Azure Key Vault zum Speichern von Anmeldeinformationen für Ihre Azure Database for MySQL-Instanzen verwenden, stellen Sie regelmäßige automatische Sicherungen Ihrer Schlüssel sicher. 

- [Grundlegendes zu Sicherungen für Azure Database for MySQL](howto-restore-server-portal.md) 

- [Sichern von Key Vault-Schlüsseln](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der kundenseitig verwalteten Schlüssel

**Leitfaden**: Wenn in Azure Database for MySQL eine Wiederherstellung durchgeführt wird, wird aus den Sicherungen des ursprünglichen Servers ein neuer Server erstellt. Es gibt zwei Arten der Wiederherstellung: Point-in-Time-Wiederherstellung und Geowiederherstellung. Die Point-in-Time-Wiederherstellung ist für beide Sicherungsredundanzoptionen verfügbar. Es wird ein neuer Server in derselben Region wie der ursprüngliche Server erstellt. Die Geowiederherstellung ist nur verfügbar, wenn Sie Ihren Server für georedundanten Speicher konfiguriert haben. Hierbei können Sie den Server in einer anderen Region wiederherstellen.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

Testen Sie regelmäßig die Datenwiederherstellung Ihrer Azure Database for MySQL-Instanzen.

- [Grundlegendes zu Sicherung und Wiederherstellung in Azure Database for MySQL](concepts-backup.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Mit Azure Database for MySQL werden vollständige, differenzielle und Transaktionsprotokollsicherungen erstellt. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

- [Grundlegendes zu Sicherung und Wiederherstellung in Azure Database for MySQL](concepts-backup.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

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

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie unter diesem Link.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).