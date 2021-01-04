---
title: Azure-Sicherheitsbaseline für Azure Database for MariaDB
description: Azure-Sicherheitsbaseline für Azure Database for MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 043a2481647076abb5a6e242ffa2ea8b3f282c4d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637165"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure-Sicherheitsbaseline für Azure Database for MariaDB

Die Azure-Sicherheitsbaseline für Azure Database for MariaDB enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Konfigurieren Sie Private Link für Azure Database for MariaDB mit privaten Endpunkten. Private Link ermöglicht das Herstellen von Verbindungen mit verschiedenen PaaS-Diensten in Azure über einen privaten Endpunkt. Im Prinzip macht Azure Private Link Azure-Dienste in Ihrem privaten virtuellen Netzwerk (VNET) verfügbar. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und der MariaDB-Instanz verläuft über das Microsoft-Backbone-Netzwerk.

Alternativ können Sie VNET-Dienstendpunkte verwenden, um den Netzwerkzugriff auf Ihre Azure Database for MariaDB-Implementierungen zu schützen und einzuschränken. VNET-Regeln sind eine Firewallsicherheitsfunktion, die steuert, ob Azure Database for MariaDB Nachrichten zulässt, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden.

Sie können Azure Database for MariaDB auch mit Firewallregeln schützen. Die Serverfirewall verhindert jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Zum Konfigurieren der Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Serverebene erstellen.

Konfigurieren von Private Link für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Erstellen und Verwalten von VNET-Dienstendpunkten und VNET-Regeln im Azure Database for MariaDB-Server: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Konfigurieren von Azure Database for MariaDB-Firewallregeln: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Wenn Ihr Azure Database for MariaDB-Server auf einem privaten Endpunkt geschützt ist, können Sie virtuelle Computer im gleichen virtuellen Netzwerk bereitstellen. Sie können eine Netzwerksicherheitsgruppe (NSG) verwenden, um das Risiko einer Datenexfiltrierung zu verringern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Konfigurieren von Private Link für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Aktivieren und Verwenden von Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Verwenden Sie Advanced Threat Protection für Azure Database for MariaDB. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.

Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken für Ihre Azure Database for MariaDB-Instanzen, um sich vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren von Advanced Threat Protection für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Konfigurieren von DDoS-Schutz: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Wenn Ihr Azure Database for MariaDB-Server auf einem privaten Endpunkt geschützt ist, können Sie virtuelle Computer im gleichen virtuellen Netzwerk bereitstellen. Sie können dann eine Netzwerksicherheitsgruppe (NSG) konfigurieren, um das Risiko einer Datenexfiltrierung zu verringern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Aktivieren und Verwenden von Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Verwenden Sie Advanced Threat Protection für Azure Database for MariaDB. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.
Konfigurieren von Advanced Threat Protection für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihre Azure Database for MariaDB-Instanzen benötigen, VNET-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. SQL.WestUs) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.
Hinweis: Azure Database for MariaDB verwendet das Diensttag „Microsoft.Sql“.

Weitere Informationen zur Verwendung von Diensttags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview Grundlegendes zur Verwendung von Diensttags für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkeinstellungen und Netzwerkressourcen, die Ihren Azure Database for MariaDB-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.DBforMariaDB“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Database for MariaDB-Instanzen zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen verwenden, die sich auf das Netzwerk oder Ihre Azure Database for MariaDB-Instanzen beziehen, z. B.:

- DDoS Protection Standard sollte aktiviert sein.

- Privater Endpunkt muss für MariaDB-Server aktiviert sein

- MariaDB-Server muss einen VNET-Dienstendpunkt verwenden

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-Beispiele für Netzwerke: https://docs.microsoft.com/azure/governance/policy/samples/

Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Ressourcen im Zusammenhang mit der Netzwerksicherheit und dem Datenfluss für Ihre MariaDB-Instanzen, um Metadaten und eine logische Organisation bereitzustellen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Database for MariaDB-Instanzen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.
Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view Erstellen von Warnungen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle, die für Azure-Ressourcen wie Azure Database for MariaDB verwendet wird, für Zeitstempel in den Protokollen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen, Serverprotokolle und Erfassungsprotokolle, um die von Ihren Azure Database for MariaDB-Instanzen generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.
Konfigurieren von Serverprotokollen für Azure Database for MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Konfigurieren von Überwachungsprotokollen für Azure Database for MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen für Ihre Azure Database for MariaDB-Instanzen für den Zugriff auf Überwachungs-, Sicherheits- und Diagnoseprotokolle. Stellen Sie sicher, dass das MariaDB-Überwachungsprotokoll explizit aktiviert wird. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente. Sie können auch die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll aktivieren und die Protokolle an denselben Log Analytics-Arbeitsbereich oder dasselbe Speicherkonto senden.

Konfigurieren von Serverprotokollen für Azure Database for MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/concepts-server-logs Konfigurieren von Überwachungsprotokollen für Azure Database for MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Konfigurieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs, der für die Azure Database for MariaDB-Protokolle verwendet wird, gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.
Festlegen der Parameter für die Protokollaufbewahrung für Log Analytics-Arbeitsbereiche: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period Speichern von Ressourcenprotokollen in einem Azure-Speicherkonto: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle Ihrer MariaDB-Instanzen auf anormales Verhalten. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Weitere Informationen zum Log Analytics-Arbeitsbereich: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Ausführen benutzerdefinierter Abfragen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für MariaDB. Advanced Threat Protection für Azure Database for MariaDB erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen.

Außerdem können Sie Serverprotokolle und Diagnoseeinstellungen für MariaDB aktivieren und Protokolle an einen Log Analytics-Arbeitsbereich senden. Integrieren Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel, da er eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitstellt. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden.

Aktivieren von Advanced Threat Protection für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Konfigurieren von Serverprotokollen für MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Konfigurieren von Überwachungsprotokollen für MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. MariaDB verarbeitet oder erzeugt keine Protokolle zu Antischadsoftware.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. MariaDB verarbeitet oder erzeugt keine Protokolle zu DNS.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend. Dieser Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie eine Inventur der Benutzerkonten, die über Administratorzugriff auf die Verwaltungsebene (Azure-Portal/Azure Resource Manager) Ihrer MariaDB-Instanzen verfügen. Verwalten Sie außerdem eine Inventur der Administratorkonten, die Zugriff auf die Datenebene Ihrer MariaDB-Instanzen haben. (Beim Erstellen des MariaDB-Servers geben Sie Anmeldeinformationen für einen Administratorbenutzer ein. Mit diesem Administrator können Sie zusätzliche MariaDB-Benutzer erstellen.)

Grundlegendes zur Zugriffsverwaltung für MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Grundlegendes zu integrierten Azure-Rollen für Azure-Abonnements: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory verfügt nicht über das Konzept der Standardkennwörter.

Bei der Erstellung von MariaDB-Ressourcen erzwingt Azure die Erstellung eines Administratorbenutzers mit einem sicheren Kennwort. Nachdem die MariaDB-Instanz erstellt wurde, können Sie jedoch das erste erstellte Serveradministratorkonto verwenden, um weitere Benutzer zu erstellen und diesen Administratorzugriff zu gewähren. Stellen Sie beim Erstellen dieser Konten sicher, dass Sie für jedes Konto ein anderes, sicheres Kennwort konfigurieren.

Erstellen zusätzlicher Konten für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsverfahren für die Verwendung dedizierter Administratorkonten, die Zugriff auf Ihre MariaDB-Instanzen haben. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Grundlegendes zu Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Der Zugriff auf die MariaDB-Datenebene wird über Identitäten gesteuert, die in der Datenbank gespeichert sind, wobei SSO nicht unterstützt wird. Der Zugriff auf die MariaDB-Steuerungsebene ist über die REST-API verfügbar und unterstützt SSO. Legen Sie den Autorisierungsheader für Ihre Anforderungen auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abrufen.

Grundlegendes zur REST-API für Azure Database for MariaDB: https://docs.microsoft.com/rest/api/mariadb/

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für MariaDB, um Warnungen für verdächtige Aktivitäten zu generieren.

Darüber hinaus können Sie Azure AD Privileged Identity Management (PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung verwenden. Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen.

Einrichten von Advanced Threat Protection für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Bereitstellen von Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informationen zu Azure AD-Risikoerkennungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf Azure-Ressourcen wie MariaDB ausschließlich für bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. AAD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in AAD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

Die Azure AD-Authentifizierung kann nicht für den direkten Zugriff auf die MariaDB-Datenebene verwendet werden. Allerdings können Azure AD-Anmeldeinformationen für die Verwaltung auf der Verwaltungsebene (z. B. Azure-Portal) zum Steuern von MariaDB-Administratorkonten verwendet werden.

Aktualisieren des Administratorkennworts für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie die Azure Active Directory-Protokolle, um veraltete Konten zu ermitteln, die u. U. MariaDB-Administratorrollen enthalten können. Verwenden Sie zusätzlich Azure-Identitätszugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen, die für den Zugriff auf MariaDB verwendet werden könnten, und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff sollte regelmäßig überprüft werden (z. B. alle 90 Tage), um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

Grundlegendes zur Azure AD-Berichterstellung: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für MariaDB und Azure Active Directory, und senden Sie alle Protokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie gewünschte Warnungen (z. B. fehlgeschlagene Authentifizierungsversuche) im Log Analytics-Arbeitsbereich.

Konfigurieren von Serverprotokollen für MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Konfigurieren von Überwachungsprotokollen für MariaDB und Zugreifen auf diese: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für MariaDB. Advanced Threat Protection für Azure Database for MariaDB erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen.

Verwenden Sie Azure Active Directory Identity Protection und Risikoerkennungsfeatures, um automatische Antworten auf erkannte verdächtige Aktionen zu konfigurieren. Sie können automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren.

Aktivieren von Advanced Threat Protection für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Anzeigen riskanter Azure AD-Anmeldungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Durchführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht zutreffend. Kunden-Lockbox wird für Azure Database for MariaDB nicht unterstützt.

Liste der durch Kunden-Lockbox unterstützten Dienste: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags, um die Nachverfolgung von Azure Database for MariaDB-Instanzen oder ähnlichen Ressourcen, die vertrauliche Informationen speichern oder verarbeiten, zu unterstützen.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Verwenden Sie eine Kombination von Private Link, Dienstendpunkten und/oder MariaDB-Firewallregeln, um den Netzwerkzugriff auf Ihre MariaDB-Instanzen zu isolieren und einzuschränken.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurieren von Private Link für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Konfigurieren von Dienstendpunkten für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Konfigurieren von Firewallregeln für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Wenn Sie Azure-VMs für den Zugriff auf MariaDB-Instanzen verwenden, können Sie Private Link, MariaDB-Netzwerkkonfigurationen, Netzwerksicherheitsgruppen und Diensttags verwenden, um die Möglichkeit einer Datenexfiltrierung zu verringern.

Microsoft verwaltet die zugrunde liegende Infrastruktur für MariaDB und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Mindern der Datenexfiltrierung für Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Database for MariaDB unterstützt die Verbindung Ihres Azure Database for MariaDB-Servers mit Clientanwendungen über TLS (Transport Layer Security ehemals Secure Sockets Layer, SSL). Durch das Erzwingen von TLS-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln. Stellen Sie im Azure-Portal sicher, dass „SSL-Verbindung erzwingen“ für alle MariaDB-Instanzen aktiviert ist.

Konfigurieren der Verschlüsselung während der Übertragung für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Database for MariaDB noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC, Role-Based Access Control), um den Zugriff auf die Verwaltungsebene von Azure Database for MariaDB (Azure-Portal/Azure Resource Manager) zu steuern. Verwenden Sie für den Zugriff auf die Datenebene (in der Datenbank selbst) SQL-Abfragen, um Benutzer zu erstellen und Benutzerberechtigungen zu konfigurieren.

Konfigurieren von Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Konfigurieren des Benutzerzugriffs mit SQL für MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Dieser Leitfaden ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für MariaDB und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Der Azure Database for MariaDB-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (mit Ausnahme von temporären Dateien, die während der Ausführung von Abfragen erstellt werden). Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.

Grundlegendes zur Verschlüsselung von ruhenden Daten für MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Database for MariaDB und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

Erstellen von Warnungen für Azure-Aktivitätsprotokollereignisse: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Zurzeit nicht verfügbar. Azure Security Center unterstützt noch keine Sicherheitsrisikobewertung für Azure Database for MariaDB-Server.


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

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

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Database for MariaDB-Server unterstützen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Azure Database for MariaDB-Server) innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Erstellen von Abfragen mit Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Anzeigen Ihrer Azure-Abonnements: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Grundlegendes zu Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure Database for MariaDB-Server und andere zugehörige Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie ggf. Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Database for MariaDB-Servern und zugehörigen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts</div>

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dadurch können die Erstellung von und Änderungen an Ressourcen in einer Hochsicherheitsumgebung, z. B. Azure Database for MariaDB Server, die vertrauliche Informationen enthält, verhindert werden.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



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

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure Database for MariaDB-Instanzen mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DBforMariaDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Database for MariaDB-Server zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen verwenden, die sich auf Ihre Azure Database for MariaDB-Server beziehen, z. B.:

- Georedundante Sicherung muss für Azure Database for MariaDB aktiviert sein

Anzeigen verfügbarer Azure Policy-Aliase: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen für Azure Database for MariaDB-Server oder zugehörige Ressourcen verwenden.

[Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DBforMariaDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DBforMariaDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie Azure Policy ([Überwachen], [Verweigern] und [Bereitstellen, falls nicht vorhanden]), um Konfigurationen für Ihre Azure Database for MariaDB-Instanzen und zugehörige Ressourcen automatisch zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Azure Database for MariaDB Server ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Database for MariaDB-Servergeheimnissen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Integration mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Azure Database for MariaDB-Server unterstützen zurzeit keine Azure Active Directory-Authentifizierung für den Zugriff auf Datenbanken.  Beim Erstellen des Azure Database for MariaDB-Servers geben Sie Anmeldeinformationen für einen Administratorbenutzer ein. Mit diesem Administrator können Sie zusätzliche MariaDB-Benutzer erstellen.  

Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Azure Database for MariaDB Server ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um Anmeldeinformationen für Azure Database for MariaDB-Server zu speichern und abzurufen.  Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Konfigurieren von verwalteten Identitäten: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm Integrieren mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity



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

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Database for MariaDB) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB-Server usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Database for MariaDB) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Azure Database for MariaDB erstellt vollständige, differenzielle und Transaktionsprotokollsicherungen.  Azure Database for MariaDB erstellt automatisch Sicherungen und speichert diese in einem lokal redundanten oder georedundanten Speicher, der von einem Benutzer konfiguriert wurde. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.  Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Grundlegendes zu Sicherungen für MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-backup

Grundlegendes zur MariaDB-Erstkonfiguration: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Azure Database for MariaDB erstellt automatisch Sicherungen und speichert diese in einem lokal redundanten oder georedundanten Speicher, der von einem Benutzer konfiguriert wurde. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen.  Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

Stellen Sie sicher, dass Ihre Schlüssel regelmäßig automatisch gesichert werden, wenn Sie Key Vault für die clientseitige Datenverschlüsselung für Daten verwenden, die im MariaDB-Server gespeichert sind.

Grundlegendes zu Sicherungen für MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-backup

Sichern von Key Vault-Schlüsseln: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Führen Sie in Azure Database for MariaDB eine Wiederherstellung aus den Sicherungen des ursprünglichen Servers aus, um Sicherungen regelmäßig zu überprüfen. Es gibt zwei Arten der Wiederherstellung: Point-in-Time-Wiederherstellung und Geowiederherstellung. Die Point-in-Time-Wiederherstellung ist für beide Sicherungsredundanzoptionen verfügbar. Es wird ein neuer Server in derselben Region wie der ursprüngliche Server erstellt. Die Geowiederherstellung ist nur verfügbar, wenn Sie Ihren Server für georedundanten Speicher konfiguriert haben. Hierbei können Sie den Server in einer anderen Region wiederherstellen.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

Grundlegendes zu Sicherung und Wiederherstellung in Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Azure Database for MariaDB erstellt vollständige, differenzielle und Transaktionsprotokollsicherungen. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Grundlegendes zu Sicherung und Wiederherstellung in Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-backup


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Struktur eines Vorfalls laut Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle zu entwickeln: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand. 

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- Sicherheitswarnungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Verwenden von Tags zum Organisieren von Azure-Ressourcen: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- Informationen finden Sie in der folgenden Veröffentlichung des NIST: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- Festlegen der Kontaktinformationen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- Konfigurieren des fortlaufenden Exports: https://docs.microsoft.com/azure/security-center/continuous-export
- Streamen von Warnungen in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen.
    

Konfigurieren von „Workflowautomatisierung“ und Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).