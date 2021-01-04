---
title: Azure-Sicherheitsbaseline für Security Center
description: Die Security Center-Sicherheitsbaseline enthält Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3b648168cd80920b8042f1edeacca893c21b98e2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498947"
---
# <a name="azure-security-baseline-for-security-center"></a>Azure-Sicherheitsbaseline für Security Center

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstest](../security/benchmarks/overview.md) auf Azure Security Center an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitssteuerungen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und durch die entsprechenden für Azure Security Center geltenden Empfehlungen definiert werden. Für Azure Security Center nicht relevante **Steuerungen** wurden ausgeschlossen. Die vollständige Zuordnung zwischen Azure Security Center und dem Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Security Center-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Azure Security Center ist ein zentrales Azure-Angebot. Es ist nicht möglich, ein virtuelles Netzwerk, ein Subnetz oder eine Netzwerksicherheitsgruppe direkt mit Security Center zu verknüpfen. Wenn Sie die Datensammlung für Ihre Computeressourcen aktivieren, speichert Security Center die gesammelten Daten über einen Log Analytics-Arbeitsbereich. Sie können diesen Arbeitsbereich für die Verwendung von Private Link konfigurieren, um den Zugriff auf Ihre Arbeitsbereichsdaten über einen privaten Endpunkt in Ihrem virtuellen Netzwerk zu ermöglichen. Bei aktivierter Datensammlung muss zudem der Log Analytics-Agent auf Ihren Servern bereitgestellt werden, damit Security Center Sicherheitsdaten sammeln und die betroffenen Computeressourcen schützen kann. Für eine ordnungsgemäße Funktionswiese mit Security Center müssen für den Log Analytics-Agent bestimmte Ports und Protokolle verwendet werden. Konfigurieren Sie Ihre Netzwerke so, dass ausschließlich die erforderlichen Ports und Protokolle zulässig sind. Fügen Sie außerdem lediglich zusätzliche Regeln hinzu, die Ihre Anwendung für die Ausführung über Netzwerksicherheitsgruppen benötigt.

- [Datensammlung in Azure Security Center](security-center-enable-data-collection.md)

- [Filtern des Netzwerkdatenverkehrs mit einer Netzwerksicherheitsgruppe](../virtual-network/tutorial-filter-network-traffic.md)

- [Firewallanforderungen für die Verwendung des Log Analytics-Agents](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Grundlegendes zu Azure Private Link](../private-link/private-link-overview.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Azure Security Center lässt sich nicht direkt in ein virtuelles Netzwerk integrieren, ermöglicht jedoch die Datensammlung von Servern, auf denen der in Ihren Netzwerken bereitgestellte Log Analytics-Agent konfiguriert ist. Für eine ordnungsgemäße Kommunikation müssen auf Ihren Servern, die für das Senden von Daten an Security Center konfiguriert sind, bestimmte Ports und Protokolle zulässig sein. Definieren und implementieren Sie Standardsicherheitskonfigurationen für diese Netzwerkressourcen mit Azure Policy.

Umfangreiche Azure-Bereitstellungen lassen sich auch mit Azure Blueprints vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Rollenzuweisungen und Azure Policy-Zuweisungen gemeinsam in einer Blaupausendefinition gepackt werden. Um Security Center-Konfigurationen und die zugehörigen Netzwerkressourcen einheitlich und sicher bereitzustellen, können Sie die Blaupause auch auf neue Abonnements anwenden.

- [Datensammlung in Azure Security Center](security-center-enable-data-collection.md)

- [Firewallanforderungen für die Verwendung des Log Analytics-Agents](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Azure Security Center lässt sich nicht direkt in ein virtuelles Netzwerk integrieren, ermöglicht jedoch die Datensammlung von Servern, auf denen der in Ihren Netzwerken bereitgestellte Log Analytics-Agent konfiguriert ist. Für eine ordnungsgemäße Kommunikation müssen auf Ihren Servern, die für das Senden von Daten an Security Center konfiguriert sind, bestimmte Ports und Protokolle zulässig sein. Definieren und implementieren Sie Standardsicherheitskonfigurationen für diese Netzwerkressourcen mit Azure Policy.

Verwenden Sie Ressourcentags für Netzwerksicherheitsgruppen und andere Ressourcen wie Server in Ihren Netzwerken, die für das Senden von Sicherheitsprotokollen an Azure Security Center konfiguriert sind. Für einzelne Netzwerksicherheitsgruppen-Regeln verwenden Sie das Feld „Beschreibung“, um die Regeln zu dokumentieren, die Datenverkehr aus/zu einem Netzwerk zulassen. 

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen für diese Ressourcen auszuführen. 

- [Datensammlung in Azure Security Center](security-center-enable-data-collection.md)

- [Firewallanforderungen für die Verwendung des Log Analytics-Agents](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md) 

- [Erstellen eines virtuellen Azure-Netzwerks](../virtual-network/quick-create-portal.md) 

- [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen-Regeln](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Ressourcenkonfigurationen und zum Erkennen von Änderungen bei Netzwerkressourcen, die im Zusammenhang mit Azure Security Center stehen. Erstellen Sie in Azure Monitor Warnungen, mit denen Sie bei Änderungen an wichtigen Ressourcen benachrichtigt werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aggregieren Sie Sicherheitsdaten, die von Azure Security Center generiert werden, sowie die zugehörigen Quellen mithilfe eines zentralen Log Analytics-Arbeitsbereichs. 

Konfigurieren Sie die Security Center-Datensammlung so, dass Sicherheitsdaten und Ereignisse von Ihren verbundenen Azure-Computeressourcen an einen zentralen Log Analytics-Arbeitsbereich gesendet werden. Zusätzlich zur Datensammlung sollten Sie die Funktion für einen fortlaufenden Export verwenden, um die von Security Center generierten Sicherheitswarnungen und Empfehlungen an Ihren zentralen Log Analytics-Arbeitsbereich zu streamen. Sie können die von Security Center und Ihren verbundenen Azure-Ressourcen generierten Sicherheitsdaten in Azure Monitor analysieren. 

Alternativ können Sie die von Security Center generierten Daten an Azure Sentinel oder eine SIEM-Drittanbieterlösung senden.

- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)

- [Datensammlung in Azure Security Center](security-center-enable-data-collection.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Sammeln von internen Azure Virtual Machine-Hostprotokollen mit Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Azure Monitor-Aktivitätsprotokolle sind automatisch verfügbar. Diese Protokolle enthalten alle Schreibvorgänge, die für Ihre Ressourcen (z. B. Azure Security Center) ausgeführt wurden. Diese Informationen umfassen, welche Vorgänge ausgeführt wurden, wer den Vorgang gestartet hat und wann die Vorgänge durchgeführt wurden. Senden Sie Ihre Azure-Aktivitätsprotokolle an einen Log Analytics-Arbeitsbereich, um sie zusammenzuführen und besser aufbewahren zu können.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Senden von Aktivitätsprotokollen an einen Log Analytics-Arbeitsbereich](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung und Archivierung. 

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie die von Azure Security Center und den verbundenen Quellen generierten Protokolle auf anormales Verhalten, und überprüfen Sie regelmäßig die Ergebnisse. Verwenden Sie Azure Monitor und einen Log Analytics-Arbeitsbereich, um Protokolle zu überprüfen und Abfragen für die Protokolldaten auszuführen.

Alternativ können Sie auch Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren. 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md) 

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Konfigurieren Sie Azure Monitor-Protokollwarnungen, um unerwünschte oder anormale Aktivitäten zu ermitteln, die von Ihrem Aktivitätsprotokoll oder in den von Azure Security Center generierten Daten erfasst werden. Richten Sie Aktionsgruppen ein, damit Ihre Organisation benachrichtigt wird und Maßnahmen ergreifen kann, wenn eine Protokollwarnung aufgrund von anormaler Aktivität ausgelöst wird. Verwenden Sie das Security Center-Feature „Workflowautomatisierung“, um Logik-Apps für Sicherheitswarnungen und Empfehlungen auszulösen. Über Security Center-Workflows können Benutzer benachrichtigt werden, um auf Vorfälle zu reagieren und basierend auf den Warnungsinformationen Maßnahmen für die betroffenen Ressourcen zu ergreifen.

Alternativ können Sie die von Azure Security Center generierten Daten bzw. die mit Azure Security Center in Zusammenhang stehenden Daten an Azure Sentinel senden. Azure Sentinel unterstützt Playbooks, die automatisierte Reaktionen auf Bedrohungen und sicherheitsrelevante Probleme ermöglichen.

- [Azure Security Center-Workflowautomatisierung](workflow-automation.md)

- [Verwalten von Warnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) 

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

- Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md) vertraut.

- [Protokollwarnungen in Azure Monitor](../azure-monitor/platform/alerts-unified-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. Azure Security Center verfügt über integrierte Rollen für die Funktionen „Sicherheitsleseberechtigter“ und „Sicherheitsadministrator“, mit denen Benutzer Sicherheitsrichtlinien lesen oder aktualisieren sowie Warnungen und Empfehlungen schließen bzw. verwerfen können.

- [Berechtigungen in Azure Security Center](security-center-permissions.md)

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Definieren Sie Standardverfahren für die Verwendung dedizierter Administratorkonten für die Azure-Plattform oder Azure Security Center. Verwenden Sie die Identitäts- und Zugriffsverwaltung von Azure Security Center, um die Anzahl von Administratorkonten in Azure Active Directory zu überwachen. Security Center verfügt ebenfalls über eine integrierte Rolle für die Funktion „Sicherheitsadministrator“, mit der Benutzer Sicherheitsrichtlinien aktualisieren sowie Warnungen und Empfehlungen schließen bzw. verwerfen können. Stellen Sie sicher, dass regelmäßig überprüft wird, welche Benutzer über diese Rollenzuweisung verfügen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

- [Berechtigungen in Azure Security Center](security-center-permissions.md)

- [Überwachen der Identität und des Zugriffs](security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identität und den Zugriff in Azure Security Center an.

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure Active Directory MFA für den Zugriff auf Azure Security Center und das Azure-Portal, und befolgen Sie alle Security Center-Empfehlungen für Identitäten und Zugriff. 

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Überwachen von Identität und Zugriff in Azure Security Center](security-center-identity-access.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine sichere, von Azure verwaltete Arbeitsstation (auch als Arbeitsstation mit privilegiertem Zugriff, Privileged Access Workstation (PAW) bezeichnet) für Verwaltungsaufgaben, für die erhöhte Rechte benötigt werden.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)

- [Planen einer Bereitstellung von Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte und -Überwachungsfunktionen, um verdächtige oder nicht sichere Aktivität in der Umgebung zu ermitteln. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md) 

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](security-center-identity-access.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Azure AD-Standorte, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen. 

- [Schnellstart: Konfigurieren benannter Orte in Azure Active Directory](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem, wenn Sie Azure Security Center nutzen. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert. Azure Security Center verfügt über integrierte Rollen, die wie die Rolle „Sicherheitsadministrator“ zugewiesen werden können. Mit diesen Rollen können Benutzer Sicherheitsrichtlinien aktualisieren sowie Warnungen und Empfehlungen schließen bzw. verwerfen.

- [Berechtigungen in Azure Security Center](security-center-permissions.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Azure AD-Identitäts und -Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff im Zusammenhang mit Azure Security Center kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten und Überwachungs- und Risikoereignisse, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist. 

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.  

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung 

**Leitfaden**: Mit den Azure AD Identity Protection-Funktionen können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen. 

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags, um die Nachverfolgung von Azure-Ressourcen wie dem Log Analytics-Arbeitsbereich zu verbessern, in dem vertrauliche Sicherheitsinformationen aus Azure Security Center gespeichert werden.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. Sie können die Zugriffsebene auf diejenigen Ihrer Azure-Ressourcen beschränken, die von Ihren Anwendungen und Unternehmensumgebungen gefordert werden. Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure steuern.

Azure Security Center-Daten werden standardmäßig im Security Center-Back-End-Dienst gespeichert. Wenn in Ihrer Organisation zusätzliche Anforderungen erfüllt und diese Daten auf eigenen Ressourcen gespeichert werden müssen, können Sie einen Log Analytics-Arbeitsbereich zum Speichern von Security Center-Daten, -Warnungen und -Empfehlungen konfigurieren. Bei Verwendung eines eigenen Arbeitsbereichs können Sie eine zusätzliche Trennung vornehmen, indem Sie basierend auf den Umgebungen, aus denen die Daten stammen, unterschiedliche Arbeitsbereiche konfigurieren.

- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)

- [Datensammlung in Azure Security Center](security-center-enable-data-collection.md)

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md) 

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md) 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure-Ressourcen verbinden, TLS 1.2 oder höher aushandeln können. Virtuelle Computer, die mit dem Log Analytics-Agent konfiguriert sind und Daten an Azure Security Center senden, sollten für die Verwendung von TLS 1.2 konfiguriert sein.

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung. 

- [Sicheres Senden von Daten an Log Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen 

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf Daten und Ressourcen zu steuern, die im Zusammenhang mit Azure Security Center stehen. Azure Security Center verfügt über integrierte Rollen für die Funktionen „Sicherheitsleseberechtigter“ und „Sicherheitsadministrator“, mit denen Benutzer Sicherheitsrichtlinien lesen oder aktualisieren sowie Warnungen und Empfehlungen schließen bzw. verwerfen können. Der Log Analytics-Arbeitsbereich, in dem die von Security Center erfassten Daten gespeichert werden, verfügt ebenfalls über integrierte Rollen, die zugewiesen werden können. Beispiele für diese Rollen sind „Log Analytics-Leser“ und „Log Analytics-Mitwirkender“. Sie sollten Benutzern eine Rolle zuzuweisen, die jeweils nur so viele Berechtigungen umfasst wie für die Erfüllung ihrer Aufgaben erforderlich sind. Weisen Sie die Rolle „Leser“ etwa Benutzern zu, die nur Informationen zur Sicherheitsintegrität einer Ressource anzeigen, aber keine Aktionen durchführen müssen (also beispielsweise keine Empfehlungen umsetzen oder Richtlinien bearbeiten).

- [Berechtigungen für den Azure Log Analytics-Arbeitsbereich](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Berechtigungen in Azure Security Center](security-center-permissions.md)

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Security Center verwendet einen konfigurierten Log Analytics-Arbeitsbereich, um die generierten Daten, Warnungen und Empfehlungen zu speichern. Konfigurieren Sie einen kundenseitig verwalteten Schlüssel für den Arbeitsbereich, den Sie für die Security Center-Datensammlung konfiguriert haben. Mit einem kundenseitig verwalteten Schlüssel können alle Daten, die im Arbeitsbereich gespeichert oder an den Arbeitsbereich gesendet werden, mit einem Azure Key Vault-Schlüssel verschlüsselt werden, den Sie erstellen bzw. dessen Besitzer Sie sind. 

- [Kundenseitig verwaltete Schlüssel in Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor, um Warnungen zu erstellen, wenn wichtige Azure-Ressourcen geändert werden, die im Zusammenhang mit Azure Security Center stehen. Bei diesen Änderungen kann es sich um jegliche Aktionen handeln, durch die Konfigurationen im Zusammenhang mit Security Center geändert werden (z. B. das Deaktivieren von Warnungen oder Empfehlungen sowie das Aktualisieren oder Löschen eines Datenspeichers).

- [Erstellen von Warnungen für Azure-Aktivitätsprotokollereignisse](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie ein gängiges Risikobewertungsprogramm (z. B. Common Vulnerability Scoring System) oder die von Ihrem Scan-Tool eines Drittanbieters bereitgestellten Standardrisikobewertungen.

- [NIST-Veröffentlichung: Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen im Zusammenhang mit Azure Security Center innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Sorgen Sie für entsprechende (Lese-) Berechtigungen auf Ihrem Mandanten, und zählen Sie alle Azure-Abonnements für die Ermittlung von Security Center-Ressourcen auf. 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md) 

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Verwenden Sie Tags, um die Nachverfolgung von Azure-Ressourcen wie dem Log Analytics-Arbeitsbereich zu verbessern, in dem vertrauliche Sicherheitsinformationen aus Azure Security Center gespeichert werden.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Azure Security Center-Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierfür die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können. 

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Entfernen Sie Azure-Ressourcen, die im Zusammenhang mit Azure Security Center stehen, wenn sie nicht mehr für die Inventarisierung und Überprüfung innerhalb Ihrer Organisation benötigt werden.

- [Löschen von Azure-Ressourcengruppen und -Ressourcen](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Ihren Abonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie standardmäßige Sicherheitskonfigurationen für Azure Security Center und den verbundenen Arbeitsbereich über Azure Policy. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.OperationalInsights“ und „Microsoft.Security“, um benutzerdefinierte Azure Policy-Definitionen zum Überwachen und Erzwingen der Security Center-Konfiguration sowie der Konfiguration des zugehörigen Log Analytics-Arbeitsbereichs zu erstellen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Optionen „Verweigern“ und „Bereitstellen bei fehlender Bereitstellung“, um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen. Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die für Ihre Organisation erforderliche Sicherheitskonfiguration der Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md) 

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md) 

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Dazu zählen z. B. benutzerdefinierte Azure Policy-Richtlinien, Azure Resource Manager-Vorlagen und Desired State Configuration-Skripts. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierte Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern. 

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überwachung oder Durchsetzung der Konfiguration Ihrer Azure Security Center-bezogenen Ressourcen zu erstellen. Zusätzlich können Sie Azure Automation verwenden, um Konfigurationsänderungen bereitzustellen. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Verwenden von Aliasen](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.OperationalInsights“ und „Microsoft. Security“, um benutzerdefinierte Richtlinien für Warnungen, Überwachung und Durchsetzung von Azure-Ressourcenkonfigurationen zu erstellen. Verwenden Sie die Azure Policy-Einstellungen „Überwachen“, „Verweigern“ und „Bereitstellen bei fehlender Bereitstellung“, um die Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Azure Security Center verwendet einen konfigurierten Log Analytics-Arbeitsbereich, um die generierten Daten, Warnungen und Empfehlungen zu speichern. Konfigurieren Sie einen kundenseitig verwalteten Schlüssel für den Arbeitsbereich, den Sie für die Security Center-Datensammlung konfiguriert haben. Mit einem kundenseitig verwalteten Schlüssel können alle Daten, die im Arbeitsbereich gespeichert oder an den Arbeitsbereich gesendet werden, mit einem Azure Key Vault-Schlüssel verschlüsselt werden, den Sie erstellen bzw. dessen Besitzer Sie sind. 

- [Kundenseitig verwaltete Schlüssel in Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Azure Security Center ist nicht für das Speichern oder Verarbeiten von Dateien ausgelegt. Es liegt in Ihrer Verantwortung, alle Inhalte vorab zu überprüfen, die auf computefremde Azure-Ressourcen (einschließlich Log Analytics-Arbeitsbereiche) hochgeladen werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen 

**Leitfaden**: Verwenden Sie einen Infrastructure-as-Code-Ansatz und Azure Resource Manager, um Ihre Azure Security Center-bezogenen Ressourcen in einer JSON-Vorlage (JavaScript Object Notation) bereitzustellen, die als Sicherung für ressourcenbezogene Konfigurationen verwendet werden kann.

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Resource Manager-Vorlagen für Sicherheitsressourcen](/azure/templates/microsoft.security/allversions)

- [Grundlegendes zu Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Azure Security Center verwendet einen Log Analytics-Arbeitsbereich, um die generierten Daten, Warnungen und Empfehlungen zu speichern. Sie können Azure Monitor und den von Security Center verwendeten Arbeitsbereich so konfigurieren, dass ein kundenseitig verwalteter Schlüssel aktiviert wird. Wenn Sie einen Schlüsseltresor zum Speichern Ihrer kundenseitig verwalteten Schlüssel verwenden, stellen Sie sicher, dass Ihre Schlüssel regelmäßige automatisiert gesichert werden.

- [Sichern von Key Vault-Schlüsseln](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie sicher, dass regelmäßig eine Wiederherstellung mithilfe von Azure Resource Manager-Vorlagendateien durchgeführt werden kann. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

- [Verwalten von Log Analytics-Arbeitsbereichen mithilfe von Azure Resource Manager-Vorlagen](../azure-monitor/samples/resource-manager-workspace.md)

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Dazu zählen z. B. benutzerdefinierte Azure Policy-Definitionen und Azure Resource Manager-Vorlagen. Um die Ressourcen, die Sie in Azure DevOps verwalten, zu schützen, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierten Gruppen (bei Integration in Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration in TFS) Berechtigungen erteilen oder verweigern. Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure, um kundenseitig verwaltete Schlüssel zu schützen.

Aktivieren Sie zudem das vorläufige Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.  Wenn Azure Storage zum Speichern von Azure Resource Manager-Vorlagensicherungen verwendet wird, aktivieren Sie vorläufiges Löschen, um Ihre Daten zu speichern und wiederherzustellen, wenn Blobs oder Blobmomentaufnahmen gelöscht werden. 

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Vorläufiges Löschen für Azure Storage-Blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle in Ihrer Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. 

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Abonnements außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung. 

- [Sicherheitswarnungen in Azure Security Center](security-center-alerts-overview.md) 

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Ermitteln Sie Schwachpunkte und Lücken, und passen Sie Ihren Reaktionsplan dann entsprechend an. 

- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden. 

- [Festlegen der Kontaktinformationen in Azure Security Center](security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über die Funktion „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. 

- [Konfigurieren des fortlaufenden Exports](continuous-export.md) 

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und Ihre Azure-Ressourcen noch besser zu schützen. 

- [Konfigurieren der Workflowautomatisierung in Security Center](workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen. 

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).