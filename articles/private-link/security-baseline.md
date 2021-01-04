---
title: Azure-Sicherheitsbaseline für Azure Private Link
description: Die Azure Private Link-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 636bc3c64dcd15ead36a9a4e969e802d49895dca
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522242"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Azure-Sicherheitsbaseline für Azure Private Link

Diese Sicherheitsbaseline wendet Empfehlungen des [Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview.md) auf Azure Private Link an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure Private Link geltenden Empfehlungen definiert werden. Nicht auf Azure Private Link anwendbare **Steuerungen** wurden ausgeschlossen. Die gesamte Zuordnung zwischen Azure Private Link und Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Virtual Network-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und zum Erkennen von Änderungen an Netzwerkressourcen im Zusammenhang mit Azure Private Link. 

Erstellen Sie in Azure Monitor Warnungen, die bei Änderungen an wichtigen Ressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle mithilfe von Azure Monitor, um von Netzwerkressourcen wie Private Link-Endpunkten, virtuellen Netzwerken und Netzwerksicherheitsgruppen generierte Sicherheitsdaten zu aggregieren. 

Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung.

Außerdem sollten Sie Daten für Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters auf Grundlage der Geschäftsanforderungen der Organisation aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Protokollierung und Überwachung für Private Link](private-link-overview.md#logging-and-monitoring)

- [Diagnoseprotokollierung für eine Netzwerksicherheitsgruppe](../virtual-network/virtual-network-nsg-manage-log.md)

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Azure Monitor-Aktivitätsprotokolle, die die für Private Link-Ressourcen ausgeführten Vorgänge protokollieren, z. B. wer den Vorgang gestartet hat, wann der Vorgang durchgeführt wurde, wie der Status des Vorgangs lautet sowie andere nützliche Überwachungsinformationen. 

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Protokollierung und Überwachung für Private Link](private-link-overview.md#logging-and-monitoring)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie für Protokolle im Zusammenhang mit Private Link den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs entsprechend der Compliancevorschriften Ihrer Organisation in Azure Monitor fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung der Protokolle.

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen.

Eine weitere Option besteht darin, die Daten für Azure Sentinel oder eine SIEM-Drittanbieterlösung zu aktivieren und zu integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Security Center mit einem konfigurierten Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Sie sollten die Daten für Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters auf Grundlage der Geschäftsanforderungen Ihrer Organisation aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwenden Sie die integrierten Administratorrollen von Azure Active Directory, die explizit zugewiesen und abgefragt werden können. Führen Sie das Azure AD-PowerShell-Modul aus, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltungsfeatures von Security Center, um die Anzahl der Administratorkonten zu überwachen.

Aktivieren Sie außerdem den Just-In-Time-/Just-Enough-Zugriff mithilfe von privilegierten Rollen für Microsoft-Dienste von Azure Active Directory Privileged Identity Management und Azure Resource Manager.

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit SSO mit Azure Active Directory, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren.

- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit MFA, die für die Anmeldung und Konfiguration von Azure-Netzwerkressourcen konfiguriert ist.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Nutzen Sie das Risikoerkennungsfeature von Azure Active Directory, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen. Erfassen Sie die Risikoerkennungswarnungen von Security Center an Azure Monitor, und konfigurieren Sie mithilfe von Aktionsgruppen benutzerdefinierte Warnungen oder Benachrichtigungen.

- [Was ist Azure Active Directory Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurieren von Aktionsgruppen für benutzerdefinierte Warnungen und Benachrichtigungen](../azure-monitor/platform/action-groups.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und in der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.  

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie außerdem Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Verwenden Sie die Anmeldeaktivität, die Überwachung und die Quellen für Risikoereignisprotokolle von Azure AD für die Integration mit beliebigen SIEM-/Überwachungstools.

Optimieren Sie diesen Prozess, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie die Risiko- und Identitätsschutzfeatures von Azure AD, um automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten für Ihre Netzwerkressourcen zu konfigurieren. 

Erfassen Sie Daten zur weiteren Untersuchung in Azure Sentinel.

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. 

Schränken Sie das Maß an Zugriff auf Ihre Azure-Ressourcen mit Ihren Anwendungen und Unternehmensumgebungen basierend auf den Geschäftsanforderungen ein. 

Steuern Sie den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC).

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die in virtuellen Netzwerken eine Verbindung mit Ihren Azure-Ressourcen herstellen, TLS 1.2 oder höher verwenden können. Private Link wirkt sich nicht auf die zugrunde liegenden Protokolle aus. Wenden Sie bewährte Methoden für andere von Kunden verwendete Angebote an.

Befolgen Sie ggf. die Empfehlungen von Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie rollenbasierte Zugriffssteuerung (RBAC) in Azure, um den Zugriff auf Daten und Ressourcen zu steuern, und verwenden Sie andernfalls dienstspezifische Zugriffssteuerungsmethoden.

- [Lernen Sie die integrierten Rollen von Azure anhand einer kurzen Beschreibung und der eindeutigen ID kennen.](../role-based-access-control/built-in-roles.md)

Rufen Sie den PowerShell-CMD-Befehl „Get-AzRoleDefinition“ oder „az role definition list“ auf, um eine Liste der Rollen in Ihrer Umgebung abzurufen.

Überprüfen Sie Ihre Optionen, um die Sichtbarkeit Ihres Diensts mithilfe der Einstellung „Sichtbarkeit“ in Private Link zu steuern. Diese Sichtbarkeitseinstellungen bestimmen, ob ein Benutzer eine Verbindung zu Ihrem Dienst herstellen kann. 

Konfigurieren Sie Ihren Dienst als privat, um die Nutzung nur über Ihre anderen virtuellen Netzwerke zu erlauben (nur Berechtigungen der rollenbasierten Zugriffssteuerung von Azure). Schränken Sie die Sichtbarkeit auf wenige vertrauenswürdige Abonnements ein, oder konfigurieren Sie sie als öffentlich, sodass alle Azure-Abonnements Verbindungen über der Private Link-Dienst anfordern können.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Eigenschaften des Private Link-Diensts](private-link-service-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Aktivitätsprotokollwarnungen von Azure Monitor, um Warnungen zu erstellen, die ausgelöst werden, wenn Änderungen an wichtigen Azure-Ressourcen wie Private Link-Diensten und -Endpunkten vorgenommen werden. 

- [Diagnoseprotokollierung für eine Netzwerksicherheitsgruppe](private-link-overview.md#logging-and-monitoring)

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph zum Abfragen und Ermitteln aller Netzwerkressourcen wie Private Link-Dienste und Endpunkte in Ihren Abonnements. 

Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen mithilfe von Metadaten an, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements, um Private Link und die zugehörigen Ressourcen zu organisieren und nachzuverfolgen. 

Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und Software für Computeressourcen gemäß den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie außerdem Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Dies kann bei Umgebungen mit hohen Sicherheitsanforderungen hilfreich sein, z. B. Umgebungen mit Speicherkonten.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen

**Leitfaden**: Der Kunde kann die Erstellung oder Verwendung von Ressourcen mit Azure Policy den Unternehmensrichtlinien entsprechend verhindern. Sie können einen eigenen Prozess zum Entfernen nicht autorisierter Ressourcen implementieren.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure-Netzwerkressourcen sowie die integrierten Azure Policy-Definitionen zu erstellen.

Azure Resource Manager kann die Vorlage im JSON-Format (JavaScript Object Notation) exportieren. Dieses Artefakt sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen Ihrer Organisation erfüllen.

Implementieren Sie Empfehlungen von Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Resource Manager-Vorlagen und Azure Policy, um Azure-Ressourcen im Zusammenhang mit Private Link und zugehörigen Ressourcen sicher zu konfigurieren.  

Azure Resource Manager-Vorlagen sind JSON-basierte Dateien, die zum Bereitstellen von Azure-Ressourcen verwendet werden, und alle benutzerdefinierten Vorlagen müssen sicher in einem Coderepository gespeichert und verwaltet werden. 

Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

- [Erstellen von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Beispiele für Azure Resource Manager-Vorlagen für private Endpunkte](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten, z. B. benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen und Desired State Configuration-Skripts. 

Sie können bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure AD definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit Team Foundation Server) Berechtigungen erteilen oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. 

Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure-Ressourcen zu erstellen. 

Verwenden Sie außerdem die integrierten Richtliniendefinitionen, die im Zusammenhang mit spezifischen unter Ihren Abonnements verwalteten Ressourcen stehen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Aliase](../governance/policy/concepts/definition-structure.md#aliases)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy zum Überwachen von Azure-Ressourcenkonfigurationen. Azure Policy kann beispielsweise für die Ermittlung von Ressourcen verwendet werden, die nicht mit einem privaten Endpunkt konfiguriert wurden.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Integrierte Beispielrichtlinien von Azure Policy für Private Link](../governance/policy/samples/built-in-policies.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Private Link-Dienste, -Endpunkte und zugehörige Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von Azure Private Link-Endpunkten und zugehöriger Ressourcen verwendet werden können. 

Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen.

- [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Beispiele für Azure Resource Manager-Vorlagen für private Endpunkte](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

- [Einführung in Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Private Link-Dienste, -Endpunkte und zugehörige Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von Azure Private Link-Endpunkten und zugehöriger Ressourcen verwendet werden können.  

Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen.  

Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

- [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Beispiele für Azure Resource Manager-Vorlagen für private Endpunkte](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

- [Einführung in Azure Automation](../automation/automation-intro.md)

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie sicher, dass Sie dazu fähig sind, regelmäßig Bereitstellungen von Azure Resource Manager-Vorlagen in ein isoliertes Abonnement gemäß Ihrer Geschäftsanforderungen durchzuführen. 

Überprüfen Sie außerdem die Wiederherstellung von gesicherten kundenseitig verwalteten Schlüsseln.

- [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Verwenden Sie Azure DevOps zum Speichern und Verwalten Ihres Codes wie z. B. Azure Resource Manager-Vorlagen. 

Sie können bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure AD definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit Team Foundation Server) den Zugriff auf diese Ressourcen gewähren oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. 

Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Incidents gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung oder Verwaltung von Incidents von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie Abonnements deutlich mithilfe von Tags (z. B. Produktion oder Nicht-Produktion), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren. Dies ist insbesondere für die Verarbeitung vertraulicher Daten wichtig.  

Der Kunde ist dafür verantwortlich, die Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung zu priorisieren, in der der Incident aufgetreten ist.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. 

Überprüfen Sie Incidents, nachdem sie aufgetreten sind, um sicherzustellen, dass die Probleme gelöst wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Warnungen und Empfehlungen von Security Center mithilfe des fortlaufenden Exportfeatures, um Risiken für Azure-Ressourcen zu identifizieren. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. 

Verwenden Sie außerdem den Security Center-Datenconnector, um die Warnungen gemäß der Anforderungen Ihrer Geschäftsvorgänge an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie das Workflowautomatisierungsfeature in Security Center, um die Reaktion automatisch mit Logic Apps für Sicherheitswarnungen und Empfehlungen auszulösen und Ihre Azure-Ressourcen zu schützen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Einsatzregeln von Microsoft, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. 

Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).