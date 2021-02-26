---
title: Azure-Sicherheitsbaseline für Backup
description: Azure-Sicherheitsbaseline für Backup
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 77af95f2decbfb81ed930591b03f71fb71780def
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805247"
---
# <a name="azure-security-baseline-for-backup"></a>Azure-Sicherheitsbaseline für Backup

Die Azure-Sicherheitsbaseline für Backup enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Nicht umsetzbar. Es ist nicht möglich, ein virtuelles Netzwerk, ein Subnetz oder eine Netzwerksicherheitsgruppe mit einem Recovery Services-Tresor zu verknüpfen. Wenn Sie einen virtuellen Azure-Computer sichern, werden die Daten über den Azure-Backbone übertragen. Beim Sichern eines lokalen Computers wird ein verschlüsselter Tunnel mit einem spezifischen Endpunkt in Azure erstellt. Anschließend werden die Daten vor der Übertragung über den verschlüsselten Tunnel mithilfe von Anmeldeinformationen verschlüsselt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Nicht umsetzbar. Es ist nicht möglich, ein virtuelles Netzwerk, ein Subnetz oder eine Netzwerksicherheitsgruppe mit einem Recovery Services-Tresor zu verknüpfen. Wenn Sie einen virtuellen Azure-Computer sichern, werden die Daten über den Azure-Backbone übertragen. Beim Sichern eines lokalen Computers wird ein verschlüsselter Tunnel mit einem spezifischen Endpunkt in Azure erstellt. Anschließend werden die Daten vor der Übertragung über den verschlüsselten Tunnel mithilfe von Anmeldeinformationen verschlüsselt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Die von Azure Backup verwendeten Endpunkte (einschließlich des Microsoft Azure Recovery Services-Agents) werden alle von Microsoft verwaltet. Sie sind für alle zusätzlichen Steuerfunktionen verantwortlich, die Sie für Ihre lokalen Systeme bereitstellen möchten.

- [Netzwerk und Zugriffsunterstützung](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Nicht umsetzbar. Es ist nicht möglich, ein virtuelles Netzwerk, ein Subnetz oder eine Netzwerksicherheitsgruppe mit einem Recovery Services-Tresor zu verknüpfen. Wenn Sie einen virtuellen Azure-Computer sichern, werden die Daten über den Azure-Backbone übertragen. Beim Sichern eines lokalen Computers wird ein verschlüsselter Tunnel mit einem spezifischen Endpunkt in Azure erstellt. Anschließend werden die Daten vor der Übertragung über den verschlüsselten Tunnel mithilfe von Anmeldeinformationen verschlüsselt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Die von Azure Backup verwendeten Endpunkte (einschließlich des Microsoft Azure Recovery Services-Agents) werden alle von Microsoft verwaltet. Sie sind für alle zusätzlichen Steuerfunktionen verantwortlich, die Sie für Ihre lokalen Systeme bereitstellen möchten.

- [Netzwerk und Zugriffsunterstützung](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Wenn Sie den Mars-Agent auf einem virtuellen Azure-Computer verwenden, verwenden Sie in ihrer Netzwerksicherheitsgruppe (NSG) oder Azure-Firewall das Diensttag „AzureBackup“, um den ausgehenden Zugriff auf Azure Backup zuzulassen.

- [Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern](./backup-sql-server-database-azure-vms.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Nicht umsetzbar. Die von Azure Backup verwendeten Endpunkte (einschließlich des Microsoft Azure Recovery Services-Agents) werden alle von Microsoft verwaltet.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Wenn Sie den MARS-Agent auf einem virtuellen Azure-Computer verwenden, ordnen Sie diese VM einer Netzwerksicherheitsgruppe zu, und geben Sie in der Beschreibung die Geschäftsanforderungen für die Regel an.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Wenn Sie den MARS-Agent auf einem virtuellen Azure-Computer verwenden, der durch eine Netzwerksicherheitsgruppe oder eine Azure-Firewall geschützt wird, überwachen Sie die Konfiguration der NSG oder Firewall mit dem Azure-Aktivitätsprotokoll. Sie haben die Möglichkeit, in Azure Monitor Warnungen zu erstellen, die bei Änderungen an diesen Ressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Nicht umsetzbar. Microsoft verwaltet die Zeitquelle von Azure-Ressourcen, z. B.Azure Backup, für die Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen ausgeführt werden.

Außerdem können Sie Protokolle über Azure Monitor erfassen, um die von Azure Backup generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Speicherkonten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Verwenden von Diagnoseeinstellungen für Recovery Services-Tresore](./backup-azure-diagnostic-events.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen ausgeführt werden.

Außerdem sendet Azure Backup Diagnoseereignisse, die gesammelt und dann für die Analyse, Warnung und Berichterstellung verwendet werden können. Diagnoseereignisse für Recovery Services-Tresore lassen sich im Azure-Portal konfigurieren. Sie können ein oder mehrere Diagnoseereignisse an ein Speicherkonto, eine Event Hubs-Instanz oder einen Log Analytics-Arbeitsbereich senden.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Verwenden von Diagnoseeinstellungen für Recovery Services-Tresore](./backup-azure-diagnostic-events.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum für Protokolle für Log Analytics-Arbeitsbereiche, die Ihren Azure Recovery Services-Tresoren zugeordnet sind, gemäß den Compliancevorschriften Ihrer Organisation fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Azure Backup stellt integrierte Überwachungs-und Warnungsfunktionen in einem Recovery Services-Tresor bereit. Diese Funktionen sind ohne zusätzliche Verwaltungsinfrastruktur verfügbar. Sie können den Umfang Ihrer Überwachung und Berichterstellung auch mit Azure Monitor erweitern.

Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Außerdem können Sie Abfragen in Log Analytics durchführen, um Suchbegriffe zu ermitteln, Trends auszumachen, Muster zu analysieren und viele weitere Erkenntnisse auf Grundlage der Aktivitätsprotokolldaten zu gewinnen, die möglicherweise für Recovery Services-Tresore gesammelt wurden.

- [Überwachen von Azure Backup-Workloads](./backup-azure-monitoring-built-in-monitor.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Azure Backup stellt integrierte Überwachungs-und Warnungsfunktionen in einem Recovery Services-Tresor bereit. Diese Funktionen sind ohne zusätzliche Verwaltungsinfrastruktur verfügbar. Sie können den Umfang Ihrer Überwachung und Berichterstellung auch mit Azure Monitor erweitern.

Bei Warnungen handelt es sich in erster Linie um Szenarios, in denen Benutzer benachrichtigt werden, damit Sie entsprechende Maßnahmen ergreifen können. Im Abschnitt zu Sicherungswarnungen finden Sie die in Azure Backup generierten Warnungen. Diese Warnungen werden vom Dienst definiert, und Sie können keine benutzerdefinierten Warnungen erstellen.

Integrieren Sie einen Log Analytics-Arbeitsbereich in Azure Sentinel, eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden. Darüber hinaus können Sie benutzerdefinierte Protokollwarnungen in Ihrem Log Analytics-Arbeitsbereich mithilfe von Azure Monitor erstellen.

- [Überwachen von Azure Backup-Workloads](./backup-azure-monitoring-built-in-monitor.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht umsetzbar. In Azure Backup werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht umsetzbar. In Azure Backup werden keine DNS-bezogenen Protokolle verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

Unterstützende Dokumentation:

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure AD verfügt nicht über das Konzept von Standardkennwörtern. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B: Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein. Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden, ebenso externe Konten mit Besitzerberechtigungen.

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie eine Azure-App-Registrierung (Dienstprinzipal), um ein Token abzurufen, das der Interaktion mit Ihren Recovery Services-Tresoren über API-Aufrufe dient.

- [Aufrufen von Azure-REST-APIs mit Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrieren Ihrer Clientanwendung mit Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Recovery Services](/rest/api/recoveryservices/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Wenn Sie wichtige Vorgänge in Azure Backup ausführen, müssen Sie eine im Azure-Portal verfügbare Sicherheits-PIN eingeben. Das Aktivieren der Azure AD Multi-Factor Authentication sorgt so für eine zusätzliche Sicherheitsebene. Nur autorisierte Benutzer mit gültigen Azure-Anmeldeinformationen, die über ein zweites Gerät authentifiziert sind, können auf das Azure-Portal zugreifen.

- [Sicherheitsfeatures für den Schutz von Hybridsicherungen mit Azure Backup](./backup-azure-security-feature.md)

- [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Privileged Access Workstation mit Azure AD Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration Ihrer Azure Backup-fähigen Ressourcen konfiguriert ist.

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Azure Backup-Instanzen. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

- [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](../app-service/configure-authentication-provider-aad.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Azure Backup-Instanzen. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten sowie auf Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration mit Azure Sentinel oder der SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Recovery Services-Tresore. Falls abweichendes Anmeldeverhalten bei der Kontoanmeldung auf Steuerungsebene (dem Azure-Portal) auftritt, verwenden Sie Azure AD Identity Protection und Risikoerkennungsfeatures, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](../app-service/configure-authentication-provider-aad.md)

- [Bericht „Riskante Anmeldungen“ im Azure Active Directory-Portal](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Zurzeit nicht verfügbar. Die Kunden-Lockbox wird für Azure Backup noch nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Wenn Sie Azure-IaaS-VMs sichern, bietet Azure Backup unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen von Originaldaten. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert.

Implementieren Sie zu Entwicklungs-, Test- und Produktionszwecken jeweils separate Abonnements und/oder Verwaltungsgruppen für Recovery Services-Tresore. Ressourcen sollten durch ein VNET/Subnetz getrennt, ordnungsgemäß gekennzeichnet und durch eine NSG oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden. Implementieren Sie für VMs, auf denen vertrauliche Informationen gespeichert oder verarbeitet werden, Richtlinien und Verfahren, mit denen Sie sie bei Nichtverwendung deaktivieren können.

Unterstützende Dokumentation:

- [Azure Backup – Übersicht](./backup-overview.md)

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Zurzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind noch nicht für Azure Backup verfügbar.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Backup und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Der Sicherungsdatenverkehr von den Servern zum Recovery Services-Tresor wird über eine sichere HTTPS-Verbindung übertragen und bei der Speicherung im Tresor mit Advanced Encryption Standard 256 (AES) verschlüsselt.

- [Verschlüsselung in Azure Backup](./backup-encryption.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Zurzeit nicht verfügbar. Features für die Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind noch nicht für Azure Backup verfügbar.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Backup und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

Azure Backup bietet drei integrierte Rollen, um Vorgänge der Sicherungsverwaltung zu steuern: Sicherungsmitwirkender, Sicherungsoperator und Benutzer mit Leseberechtigung für Sicherungsfunktionen. Sie können die integrierten Backup-Rollen zu Aktionen der Sicherungsverwaltung zuordnen.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Verwenden der rollenbasierten Zugriffssteuerung in Azure zum Verwalten von Azure Backup-Wiederherstellungspunkten](./backup-rbac-rs-vault.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Backup und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Schutz der Azure-Kundendaten](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Backup unterstützt die Verschlüsselung von ruhenden Daten. Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase. Für Cloudworkloads werden die Daten im Ruhezustand mit der Speicherdienstverschlüsselung verschlüsselt. Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt.

Wenn Sie mithilfe des MARS-Agents oder eines Recovery Services-Tresors, der mit einem vom Kunden verwalteten Schlüssel verschlüsselt wurde, eine Sicherung erstellen, haben Sie nur Zugriff auf den Verschlüsselungsschlüssel. Microsoft bewahrt keine Kopie auf und hat keinen Zugriff auf den Schlüssel. Wenn der Schlüssel verlegt wird, kann Microsoft die gesicherten Daten nicht wiederherstellen.

- [Verschlüsselung in Azure Backup](./backup-encryption.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Azure Recovery Services-Produktionstresoren und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Noch nicht verfügbar. Die Beurteilung von Sicherheitsrisiken im Azure Security Center ist noch nicht für Azure Backup verfügbar.

Die zugrunde liegende Plattform wird von Microsoft überprüft und gepatcht. Überprüfen Sie die für Azure Backup verfügbaren Sicherheitsfunktionen, um Sicherheitsrisiken im Zusammenhang mit der Dienstkonfiguration zu minimieren.

- [Sicherheitsfeatures für Azure Backup]()

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

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

**Leitfaden**: Zurzeit nicht verfügbar. Sicherheitskonfigurationen für Azure Backup werden im Azure Security Center noch nicht unterstützt.

- [Features für Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle) in ihren Abonnements abzufragen bzw. zu ermitteln.  Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

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

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen: Nicht zulässige Ressourcentypen, zulässige Ressourcentypen.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Definieren Sie genehmigte Azure-Ressourcen und genehmigte Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

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

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen: Nicht zulässige Ressourcentypen, zulässige Ressourcentypen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts</div>

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

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

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihren Recovery Services-Tresor mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Recovery Services-Tresore zu erstellen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Auswirkungen [Audit], [Deny] und [DeployIfNotExists], um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Wenn Sie den MARS-Agent einrichten, speichern Sie die Verschlüsselungspassphrase in Azure Key Vault.

- [Erstellen einer Key Vault-Instanz](../key-vault/secrets/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Nicht umsetzbar. Verwaltete Identitäten werden für Azure Backup nicht unterstützt.

- [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht umsetzbar. Diese Empfehlung bezieht sich auf Computeressourcen. Microsoft Antimalware ist auf dem zugrunde liegenden Host aktiviert, der die Azure-Dienste (z. B. Azure Backup) unterstützt. Das Feature wird jedoch nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host aktiviert, der die Azure-Dienste (z. B. Azure Backup) unterstützt. Das Feature wird jedoch nicht für Ihre Inhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage.

Mithilfe der Bedrohungserkennung für Datendienste in Azure Security Center können Sie Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde.

- [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

- [Grundlegendes zur Bedrohungserkennung für Datendienste in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Nicht umsetzbar. Diese Empfehlung bezieht sich auf Ressourcen, die gesichert werden, nicht auf Azure Backup selbst.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Lokal redundanter Speicher (Locally Redundant Storage, LRS) repliziert Ihre Daten dreimal in einer Speicherskalierungseinheit in einem Datencenter. (Es werden also drei Kopien Ihrer Daten erstellt.) Alle Kopien der Daten befinden sich in derselben Region. LRS ist eine kostengünstige Option, Ihre Daten vor lokalen Hardwarefehlern zu schützen. Georedundanter Speicher (GRS) ist die standardmäßige und die empfohlene Replikationsoption. GRS repliziert Ihre Daten in einer sekundären Region, die mehrere hundert Kilometer vom primären Speicherort der Quelldaten entfernt ist. GRS führt zu höheren Kosten als LRS, bietet aber eine höhere Dauerhaftigkeit Ihrer Daten (auch im Falle eines regionalen Ausfalls).

Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

- [Azure Backup – Übersicht](./backup-overview.md)

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Verschlüsselung von Sicherungsdaten mit vom Kunden verwalteten Schlüsseln](./backup-encryption.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase. Für virtuelle Azure-Computer werden die Daten mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) im Ruhezustand verschlüsselt. Sie können das vorläufige Löschen in Key Vault aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Vorläufiges Löschen für Azure Storage-Blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [„Computer Security Incident Handling Guide“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden:** Befolgen Sie die [Einsatzregeln für Penetrationtests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1), damit diese alle Anforderungen der Microsoft-Richtlinien erfüllen.

- [Unter diesem Link](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) finden Sie weitere Informationen zur Microsoft-Strategie für und zur Durchführung von Red Teaming und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).