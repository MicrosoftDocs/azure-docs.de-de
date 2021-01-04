---
title: Azure-Sicherheitsbaseline für Azure Database for PostgreSQL – Hyperscale (Citus)
description: Die Sicherheitsbaseline für Azure Database for PostgreSQL mit Hyperscale (Citus) enthält Anleitungen und Ressourcen für die Vorgehensweise bei der Implementierung der Sicherheitsempfehlungen aus dem Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 324a2acbf069e8e23246b99a7d79a3195f737a56
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492300"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure-Sicherheitsbaseline für Azure Database for PostgreSQL – Hyperscale (Citus)

Die Azure-Sicherheitsbaseline für Azure Database for PostgreSQL mit Hyperscale (Citus) enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Die Azure Database for PostgreSQL-Serverfirewall verhindert jeglichen Zugriff auf Ihren Hyperscale (Citus)-Koordinatorknoten, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Zum Konfigurieren der Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Serverebene erstellen.

- [Firewallregeln in Azure Database for PostgreSQL – Hyperscale (Citus)](./concepts-hyperscale-firewall-rules.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkeinstellungen und Netzwerkressourcen, die Ihren Azure Database for PostgreSQL-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Database for PostgreSQL-Instanzen zu erstellen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Außerdem können Sie Protokolle über Azure Monitor erfassen, um die von Hyperscale (Citus) generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Speicherkonten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachung und Optimierung von Azure Database for PostgreSQL – Hyperscale (Citus)](./concepts-hyperscale-monitoring.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Hyperscale (Citus) stellt Metriken für jeden Knoten in einer Servergruppe bereit. Die Metriken bieten Einblicke in das Verhalten der unterstützenden Ressourcen. Jede Metrik wird mit einer Frequenz von einer Minute ausgegeben und verfügt über einen Verlauf von bis zu 30 Tagen.

Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Außerdem können Sie Protokolle über Azure Monitor erfassen, um die von Hyperscale (Citus) generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Speicherkonten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren. 

- [Überwachung und Optimierung von Azure Database for PostgreSQL – Hyperscale (Citus)](./concepts-hyperscale-monitoring.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs, der für die Hyperscale (Citus)-Protokolle verwendet wird, gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Azure-Ressourcenprotokolle](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle Ihrer Hyperscale (Citus)-Instanzen auf anormales Verhalten. Verwenden Sie Log Analytics von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Tutorial: Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen für Hyperscale (Citus), und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich. Sie können eine Warnung konfigurieren und erhalten, die auf Überwachungsmetriken für Ihre Azure-Dienste basiert. Verwenden Sie Log Analytics von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

Integrieren Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel, da er eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitstellt. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden.

- [Überwachung und Optimierung von Azure Database for PostgreSQL – Hyperscale (Citus)](./howto-hyperscale-alert-on-metric.md)

- [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-log.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie einen Bestand der Benutzerkonten, die über Administratorzugriff auf die Steuerungsebene (Azure-Portal) Ihrer Hyperscale (Citus)-Instanzen verfügen. Verwalten Sie außerdem einen Bestand der Administratorkonten, die Zugriff auf die Datenebene (in der Datenbank selbst) Ihrer Hyperscale (Citus)-Instanzen haben.

Hyperscale (Citus) unterstützt keine integrierte rollenbasierte Zugriffssteuerung. Sie können jedoch benutzerdefinierte Rollen auf Basis bestimmter Ressourcenanbietervorgänge erstellen.

Die PostgreSQL-Engine verwendet außerdem Rollen, um den Zugriff auf Datenbankobjekte zu steuern, und eine neu erstellte Hyperscale (Citus)-Servergruppe verfügt über mehrere vordefinierte Rollen. Verwenden Sie zum Ändern von Benutzerberechtigungen PostgreSQL-Standardbefehle mithilfe eines Tools wie PgAdmin oder psql.

- [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md) 

- [Microsoft.DBforPostgreSQL](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Sicherheit in Azure Database for PostgreSQL – Einzelserver](./concepts-security.md#access-management)

- [Erstellen von Benutzern in Azure Database for PostgreSQL: Hyperscale (Citus)](./howto-hyperscale-create-users.md)

- [Herstellen einer Verbindung mit der Datenbank mithilfe von psql](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure AD verfügt nicht über das Konzept von Standardkennwörtern. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsverfahren für die Verwendung dedizierter Administratorkonten, die Zugriff auf Ihre Hyperscale (Citus)-Instanzen haben. Die Administratorkonten für die Verwaltung der Azure-Ressource sind mit Azure Active Directory verknüpft. Es gibt auch lokale Serveradministratorkonten, die innerhalb der Hyperscale (Citus)-Servergruppe vorhanden sind, mit denen Datenbankzugriffsberechtigungen verwaltet werden können. Verwenden Sie die Identitäts- und Zugriffsverwaltung von Azure Security Center, um die Anzahl der Administratorkonten in Azure Active Directory zu überwachen.

- [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md) 

- [Erstellen von Benutzern in Azure Database for PostgreSQL: Hyperscale (Citus)](./howto-hyperscale-create-users.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Für Zugriff auf das Azur-Portal aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Portal und Azure Resource Manager nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem zum Verwalten von PostgreSQL-Ressourcen. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Benutzer innerhalb einer Hyperscale (Citus)-Servergruppe können nicht direkt an Azure Active Directory-Konten gebunden werden. Verwenden Sie zum Ändern von Benutzerberechtigungen für den Zugriff auf Datenbankobjekte PostgreSQL-Standardbefehle mit Tools wie PgAdmin oder psql.

- [Ändern von Berechtigungen für Benutzerrollen](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [Erstellen und Konfigurieren einer AAD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie sowohl den Zugriff von Benutzern auf die lokale Datenbank, als auch den Zugriff von Benutzer der über Azure Active Directory erfolgt, um PostgreSQL-Ressourcen zu verwalten, und stimmen Sie diese miteinander ab.

Überprüfen Sie für Benutzer mit Zugriff zum Verwalten von Azure-Datenbankressourcen die Azure Active Directory-Protokolle (AD), um veraltete Konten zu ermitteln. Verwenden Sie zusätzlich Azure-Identitätszugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen, die für den Zugriff auf Hyperscale (Citus) verwendet werden könnten, und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff sollte regelmäßig überprüft werden (z. B. alle 90 Tage), um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

- [Datenbankrollen](https://www.postgresql.org/docs/current/database-roles.html)

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: In Azure Active Directory (AD) haben Sie Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist. 

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren. 

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Features zum Identitätsschutz und zur Risikoerkennung von Azure Active Directory können Sie automatische Antworten auf erkannte verdächtige Aktionen auf der Azure Active Directory-Ebene (AD) konfigurieren. Sie können automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren.

Außerdem können Sie Protokolle zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Was ist Azure Active Directory Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Zurzeit nicht verfügbar; Kunden-Lockbox wird für Hyperscale (Citus) noch nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags, um die Nachverfolgung von Hyperscale (Citus)-Instanzen oder ähnlichen Ressourcen, die vertrauliche Informationen speichern oder verarbeiten, zu unterstützen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Verwenden Sie eine Kombination aus verwaltenden Rollen und Firewallregeln, um den Netzwerkzugriff auf Ihre Azure Database for PostgreSQL-Instanzen zu isolieren und einzuschränken.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Firewallregeln in Azure Database for PostgreSQL – Hyperscale (Citus)](./concepts-hyperscale-firewall-rules.md)

- [Rollen in Hyperscale (Citus)](./howto-hyperscale-create-users.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verbindungen von Clientanwendungen mit dem Hyperscale (Citus)-Koordinatorknoten erfordern Transport Layer Security 1.2 (TLS). Durch das Erzwingen von TLS-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln.

Bei allen Azure Database for PostgreSQL-Servern, die über das Azure-Portal bereitgestellt werden, ist die Erzwingung von TLS-Verbindungen standardmäßig aktiviert.

In einigen Fällen erfordern Anwendungen von Drittanbietern eine lokale Zertifikatdatei, die aus der Zertifikatdatei (CER) einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) generiert wurde, um eine sichere Verbindung herzustellen.

- [Konfigurieren von TLS in Azure Database for PostgreSQL – Hyperscale (Citus)](./concepts-hyperscale-ssl-connection-security.md)

- [Anwendungen, die eine Zertifikatüberprüfung für TLS-Verbindungen erfordern](./concepts-hyperscale-ssl-connection-security.md)



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf die Hyperscale (Citus)-Steuerungsebene (z. B. Azure-Portal) zu steuern. Azure RBAC wirkt sich nicht auf Benutzerberechtigungen in der Datenbank aus.

Verwenden Sie zum Ändern von Benutzerberechtigungen auf Datenbankebene PostgreSQL-Standardbefehle mithilfe eines Tools wie PgAdmin oder psql.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Erstellen von Benutzern in Azure Database for PostgreSQL: Hyperscale (Citus)](./howto-hyperscale-create-users.md)


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**:  
Mindestens einmal pro Tag erstellt Azure Database for PostgreSQL – Hyperscale (Citus) Sicherungsmomentaufnahmen der Datendateien und des Datenbanktransaktionsprotokolls. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums wiederherstellen. (Der Aufbewahrungszeitraum beträgt derzeit für alle Cluster 35 Tage.) Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet. Das PostgreSQL Hyperscale (Citus)-Angebot verwendet von Microsoft verwaltete Schlüssel für die Verschlüsselung.

- [Sicherung und Wiederherstellung in Azure Database for PostgreSQL: Hyperscale (Citus)](./concepts-hyperscale-backup.md)



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Hyperscale (Citus) und anderen entscheidenden oder verknüpften Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Zurzeit nicht verfügbar; Azure Security Center unterstützt noch keine Sicherheitsrisikobewertung für Azure Database for PostgreSQL – Hyperscale (Citus).

- [Funktionsabdeckung für Azure PaaS-Ressourcen](../security-center/features-paas.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Hyperscale (Citus)-Instanzen) innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Hyperscale (Citus)-Instanzen und andere zugehörige Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Hyperscale (Citus)-Instanzen und zugehörigen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Verwenden Sie eine Azure-Richtlinie, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie eine Azure-Richtlinie, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie eine Azure-Richtlinie, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dadurch können die Erstellung von und Änderungen an Ressourcen in einer Hochsicherheitsumgebung, z. B. Instanzen von Hyperscale (Citus), die vertrauliche Informationen enthält, verhindert werden.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Hyperscale (Citus)-Instanzen mit Azure Policy. Verwenden Sie Azure Policy, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure Database for PostgreSQL-Instanzen zu erstellen.

Außerdem hat Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen. 

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md) 



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.  Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die Sicherheitskonfiguration der von Ihrer Organisation benötigten Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen für Hyperscale (Citus)-Instanzen oder zugehörige Ressourcen verwenden.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.  Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die Sicherheitskonfiguration der von Ihrer Organisation benötigten Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DBforPostgreSQL“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie Azure Policy ([Überwachen], [Verweigern] und [Bereitstellen, falls nicht vorhanden]), um Konfigurationen für Ihre Azure Database for PostgreSQL Instanzen und zugehörige Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Azure Database for PostgreSQL – Hyperscale (Citus) unterstützt derzeit keine direkt verwalteten Identitäten. Beim Erstellen des Azure Database for PostgreSQL-Servers müssen Sie Anmeldeinformationen für einen Administratorbenutzer angeben. Sie können zusätzliche Benutzerrollen in der Oberfläche des Azure-Portals erstellen.

- [Erstellen einer Azure Database for PostgreSQL-Instanz: Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md#create-an-azure-database-for-postgresql---hyperscale-citus)

- [Erstellen zusätzlicher Benutzerrollen](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Hyperscale (Citus)) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in Azure-Ressourcen ohne Computeaufgaben hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Bei der Option Hyperscale (Citus) für Azure Database for PostgreSQL werden automatisch Sicherungskopien für jeden Knoten erstellt und in einem lokal redundanten Speicher gespeichert. Mithilfe von Sicherungskopien können Sie den Stand Ihres Hyperscale (Citus)-Clusters zu einem bestimmten Zeitpunkt wiederherstellen.

- [Sicherung und Wiederherstellung in Azure Database for PostgreSQL: Hyperscale (Citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Mindestens einmal pro Tag erstellt Azure Database for PostgreSQL Sicherungsmomentaufnahmen der Datendateien und des Datenbanktransaktionsprotokolls. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums wiederherstellen. Der Aufbewahrungszeitraum beträgt derzeit für alle Cluster 35 Tage. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

In Azure-Regionen, die Verfügbarkeitszonen unterstützen, werden Sicherungsmomentaufnahmen in drei Verfügbarkeitszonen gespeichert. Solange mindestens eine Verfügbarkeitszone online ist, kann der Hyperscale (Citus)-Cluster wiederhergestellt werden.

- [Sicherung und Wiederherstellung in Azure Database for PostgreSQL: Hyperscale (Citus)](./concepts-hyperscale-backup.md)


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: In Azure Database for PostgreSQL wird beim Wiederherstellen eines Hyperscale (Citus)-Clusters aus den Sicherungskopien der ursprünglichen Knoten ein neuer Cluster erstellt. Sie können den Stand eines Clusters zu einem beliebigen Zeitpunkt innerhalb der letzten 35 Tage wiederherstellen. Beim Wiederherstellungsprozess wird ein neuer Cluster in derselben Azure-Region, demselben Abonnement und derselben Ressourcengruppe wie der ursprüngliche Cluster erstellt. Die neue Clusterkonfiguration ist in der Anzahl der Knoten, der Anzahl von virtuellen Kernen, der Speichergröße und den Benutzerrollen identisch mit der ursprünglichen Clusterkonfiguration.

Die Firewalleinstellungen und PostgreSQL-Serverparameter der ursprünglichen Servergruppe bleiben nicht erhalten. Sie werden auf die Standardwerte zurückgesetzt. Die Firewall verhindert alle Verbindungen. Sie müssen diese Einstellungen nach der Wiederherstellung manuell anpassen.

- [Sicherung und Wiederherstellung in Azure Database for PostgreSQL: Hyperscale (Citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Gelöschte Hyperscale (Citus)-Cluster können nicht wiederhergestellt werden. Wenn Sie den Cluster löschen, werden alle Knoten, die zu diesem Cluster gehören, gelöscht und können nicht wiederhergestellt werden. Administratoren können Verwaltungssperren nutzen, um Clusterressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen.

- [Sicherung und Wiederherstellung in Azure Database for PostgreSQL: Hyperscale (Citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

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

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).