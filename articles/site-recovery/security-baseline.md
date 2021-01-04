---
title: Azure-Sicherheitsbaseline für Site Recovery
description: Die Sicherheitsbaseline für Site Recovery umfasst Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Azure-Sicherheitsvergleichstest angegeben sind.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 860bc9440547a4c62336ccc573859ec56b3400b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533858"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Azure-Sicherheitsbaseline für Site Recovery

Diese Sicherheitsbaseline wendet Empfehlungen von [Version 1.0 des Azure-Sicherheitsvergleichstests](../security/benchmarks/overview-v1.md) auf Site Recovery an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Site Recovery geltenden Empfehlungen definiert werden. Nicht auf Site Recovery anwendbare **Kontrollen** wurden ausgeschlossen. 

Die vollständige Zuordnung von Site Recovery zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Site Recovery-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Microsoft Azure Site Recovery unterstützt nicht die Bereitstellung in eine Azure Virtual Network-Instanz. Konfigurieren Sie den Site Recovery-Dienst mit einem privaten Azure-Endpunkt, um die sichere Kommunikation über Ihr Netzwerk zu erzwingen.

- [Unterstützung privater Verbindungen für Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Der Site Recovery-Dienst unterstützt Diensttags, mit denen Kunden den Datenverkehr für spezifische Dienste und Ports freigeben können. Kunden müssen das Diensttag „AzureSiteRecovery“ in ihrer Firewall oder Netzwerksicherheitsgruppe zulassen, um den ausgehenden Zugriff auf den Site Recovery-Dienst zuzulassen.

- [Ausgehende Konnektivität mithilfe von Diensttags](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Ressourcentags für Netzwerksicherheitsgruppen und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Für einzelne Netzwerksicherheitsgruppen-Regeln verwenden Sie das Feld „Beschreibung“, um die Regeln zu dokumentieren, die Datenverkehr aus und zu einem Netzwerk zulassen. 

Fügen Sie eine der integrierten Azure Policy-Definitionen für das Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden. 

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen für diese Ressourcen auszuführen. 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md) 

- [Erstellen eines virtuellen Azure-Netzwerks](../virtual-network/quick-create-portal.md) 

- [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen-Regeln](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Überwachen Sie alle Änderungen an Netzwerkressourcenkonfigurationen bezüglich des Site Recovery-Diensts mithilfe von Azure-Aktivitätsprotokollen. Erstellen Sie Warnungen in Azure Monitor, die Sie benachrichtigen, wenn Änderungen an wichtigen Ressourcen vorgenommen werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Überwachungsprotokollierung der Azure-Aktivitätsprotokolle, und senden Sie die Protokolle zur Archivierung an einen Log Analytics-Arbeitsbereich, ein Azure Storage-Konto oder eine Azure Event Hub-Instanz.

Verwenden Sie die Daten aus Azure-Aktivitätsprotokollen, um Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) zu ermitteln, die für Ihre Azure-Ressourcen durchgeführt werden.

Erfassen Sie Site Recovery-Protokolle in Azure Monitor, um generierte Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor zum Abfragen und Ausführen von Analysen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung. Außerdem können Sie auch Daten in Azure Sentinel oder einer SIEM-Lösung (Security Information & Event Management) eines Drittanbieters aktivieren und integrieren.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Site Recovery mit Azure Monitor-Protokollen](monitor-log-analytics.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Überwachungsprotokollierung der Azure-Aktivitätsprotokolle, und senden Sie die Protokolle zur Archivierung an einen Log Analytics-Arbeitsbereich, ein Azure Storage-Konto oder eine Azure Event Hub-Instanz. 

Verwenden Sie die Daten aus Azure-Aktivitätsprotokollen, um Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) zu ermitteln, die für Ihre Azure-Ressourcen durchgeführt werden.

Erfassen Sie Site Recovery-Protokolle mit Azure Monitor, um generierte Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor zum Abfragen und Ausführen von Analysen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung. Aktivieren und Integrieren Sie Daten in Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Überwachen von Site Recovery mit Azure Monitor-Protokollen](monitor-log-analytics.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie die Protokollaufbewahrungsdauer für Log Analytics-Arbeitsbereiche für Ihre Azure Recovery Services-Tresore mithilfe von Azure Monitor entsprechend der Compliancevorschriften Ihrer Organisation fest. 

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. 

Führen Sie Abfragen in Log Analytics aus, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und Erkenntnisse aus den Aktivitätsprotokolldaten zu erfassen, die aus Recovery Services-Tresoren abgerufen werden.

- [Überwachen von Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Überwachen Sie von Azure Site Recovery replizierte Computer mithilfe von Azure Monitor-Protokollen und Log Analytics. Verwenden Sie Log Analytics in Azure Monitor zum Schreiben und Testen von Protokollabfragen sowie zum interaktiven Analysieren von Protokolldaten. Azure Monitor erfasst Aktivitäts- und Ressourcenprotokolle sowie andere Überwachungsdaten. 

Visualisieren Sie Protokollergebnisse, fragen Sie diese ab, und konfigurieren Sie Warnungen, um Maßnahmen auf Grundlage der überwachten Daten zu ergreifen. Richten Sie Warnungen für einen Log Analytics-Arbeitsbereich in Azure Sentinel ein, da somit eine SOAR-Lösung (Security Orchestration Automated Response, Sicherheitsorchestrierung mit automatisierter Reaktion) bereitgestellt wird. Dadurch können automatisierte Lösungen wie Playbooks erstellt und zum Beheben von Sicherheitsproblemen verwendet werden. Erstellen Sie benutzerdefinierte Protokollwarnungen in Ihrem Log Analytics-Arbeitsbereich mithilfe von Azure Monitor. 

- [Überwachen von Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Standardmäßig werden keine Rollen zugewiesen. Sie müssen explizit nach geschäftlichen Bedarf zugewiesen werden. Alle Rollenzuweisungen können mit der PowerShell-Befehlszeilenschnittstelle oder mit Azure Active Directory (Azure AD) überprüft werden, um Konten zu ermitteln, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltungsfeatures von Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie Empfehlungen von Security Center oder integrierte Azure-Richtlinien verwenden, um den Überblick über dedizierte administrative Konten zu bewahren, zum Beispiel: 

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein. 
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden. 

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Erstellen Sie einen Prozess zur Nachverfolgung der Identität und Zugriffssteuerung für administrative Konten, und überprüfen Sie diesen regelmäßig.

- [Verwenden von Azure Security Center zum Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Nutzen Sie die Azure-App-Registrierung mit einem Dienstprinzipal, um ein Token abzurufen, das dazu verwendet werden kann, über API-Aufrufe mit Ihren Recovery Services-Tresoren zu interagieren.

- [Aufrufen von Azure-REST-APIs mit Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrieren Ihrer Clientanwendung mit Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Recovery Services](/rest/api/recoveryservices)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung von Azure AD, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung von Security Center. 
- [Planen einer Bereitstellung von Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine sichere, von Azure verwaltete Arbeitsstation (auch bekannt als Arbeitsstation mit privilegiertem Zugriff) mit mehrstufiger Azure-Authentifizierung für administrative Aufgaben und zum Durchführen privilegierter Aktionen für Site Recovery-Ressourcen.

- [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie das PIM-Feature (Privileged Identity Management) von Azure AD zum Generieren von Protokollen und Warnungen, wenn verdächtige oder unsichere Aktivitäten in der Umgebung auftreten.
Mit dem Risikoerkennungsfeature von Azure AD können Sie Warnungen und Berichte zum Verhalten von Risikobenutzern anzeigen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen, Regionen oder Ländern zuzulassen.
- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure AD als zentrales Authentifizierungs- und Autorisierungssystem für Site Recovery. Azure AD schützt Daten mithilfe von starker Verschlüsselung für ruhende Daten und während der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet. 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Verwenden Sie Azure AD-Protokolle, um veraltete Konten zu ermitteln. 

Verwalten Sie Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen mit den Identitäts- und Zugriffsüberprüfungen von Azure AD auf effiziente Weise. 

Erstellen Sie einen Prozess zur regulären Überprüfung des Benutzerzugriffs, um sicherzustellen, dass nur Benutzer mit abgeschlossenen Zugriffsüberprüfungen über langfristigen Zugriff verfügen. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Verwenden Sie Azure AD als zentrales Authentifizierungs- und Autorisierungssystem für Site Recovery-Ressourcen. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und in der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.

Sie verfügen über Zugriff auf die Azure AD-Anmeldeaktivitäten, die Überwachung und die Quellen der Risikoereignisprotokolle, was es Ihnen ermöglicht, diese mit Azure Sentinel oder einem beliebigen SIEM- oder Überwachungstool zu integrieren, das im Azure Marketplace verfügbar ist.

Optimieren Sie diesen Prozess weiter, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in einem Log Analytics-Arbeitsbereich konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure AD als zentrales Authentifizierungs- und Autorisierungssystem für Ihre Recovery Services-Tresore. 

Nutzen Sie die Azure AD Identity Protection-Features für die Erkennung des Anmeldeverhaltens von Konten und zum Konfigurieren von automatisierten Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten. Erfassen Sie außerdem Daten zur weiteren Untersuchung in Azure Sentinel.

- [Bericht „Riskante Anmeldungen“ im Azure Active Directory-Portal](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements oder Verwaltungsgruppen zu Entwicklungs-, Test- und Produktionszwecken für Recovery Services-Tresore. Trennen Sie die Ressourcen mithilfe eines virtuellen Netzwerks oder eines Subnetzes voneinander, kennzeichnen Sie diese ordnungsgemäß, und sichern Sie sie mit einer Netzwerksicherheitsgruppe oder mit Azure Firewall. 

Deaktivieren Sie VMs (virtuelle Computer), die vertrauliche Daten speichern oder verarbeiten, wenn diese nicht verwendet werden. Implementieren Sie Richtlinien und Verfahren, um daraus einen regelmäßigen Prozess zu machen. 

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Übersicht über Site Recovery](site-recovery-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Verwenden Sie eine private Verbindung oder einen privaten Endpunkt, Netzwerksicherheitsgruppen und Diensttags, um Möglichkeiten für eine Datenexfiltration aus den Site Recovery-fähigen VMs zu mindern.

Microsoft verwaltet die zugrunde liegende Plattform, die von Site Recovery verwendet wird, behandelt alle Kundeninhalte vertraulich und schützt vor Verlust und Offenlegung von Kundendaten. Microsoft hat eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und verwaltet diese, um die Sicherheit der Kundendaten in Azure zu gewährleisten. 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

- [Replizieren virtueller Computer mit privaten Azure-Endpunkten](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replizieren von virtuellen Computern mit Azure Site Recovery-Diensttags](azure-to-azure-about-networking.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Site Recovery verwendet einen sicheren HTTPS-Kanal, der von den Azure-Workloadservern bis zu den in einem Recovery Services-Tresor gehosteten Site Recovery-Diensten mit AES 256 (Advanced Encryption Standard) verschlüsselt ist.

Die folgenden TLS-Versionen werden aktuell in den Regionen unterstützt, die zum Ende von 2019 verfügbar waren: TLS 1.0, TLS 1.1 und TLS 1.2. TLS 1.2 ist die einzige unterstützte TLS-Version für neue Regionen.

- [Grundlegendes zur Verschlüsselung während der Übertragung für Azure Site Recovery](physical-azure-set-up-source.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Die Features für die Datenidentifizierung, -klassifizierung und -verlustverhinderung sind noch nicht für Site Recovery verfügbar. 

Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Microsoft verwaltet die zugrunde liegende Plattform, die von Site Recovery verwendet wird, behandelt alle Kundeninhalte vertraulich und schützt vor Verlust und Offenlegung von Kundendaten. Außerdem hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und verwaltet diese, um die Sicherheit der Kundendaten in Azure zu gewährleisten. 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zum Verwalten des Zugriffs auf Daten und Ressourcen bezüglich Site Recovery-Ressourcen. 

Trennen Sie Arbeitsaufgaben mithilfe der rollenbasierten Zugriffssteuerung von Azure, und gewähren Sie die entsprechenden erforderlichen Zugriffsberechtigungen. Verwenden Sie die integrierten Site Recovery-Rollen, um Site Recovery-Verwaltungsvorgänge zu steuern.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten von Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Aktivieren Sie die doppelte Verschlüsselung mit plattformseitig und kundenseitig verwalteten Schlüsseln. Diese Funktion ist in Site Recovery verfügbar. 

Site Recovery unterstützt die Verschlüsselung von ruhenden Daten. Für Azure IaaS-Workloads werden die Daten im Ruhezustand mit der Speicherdienstverschlüsselung (SSE) verschlüsselt. 

Bei der Verwendung eines mit einem kundenseitig verwalteten Schlüssel verschlüsselten Recovery Services-Tresors verfügt nur der Kunde über Zugriff auf den Verschlüsselungsschlüssel. Microsoft speichert nie eine Kopie, verfügt über keinen Zugriff auf den Schlüssel und entschlüsselt nicht die Daten, die vom primären Standort an den Notfallwiederherstellungsstandort übermittelt werden. 

- [Unterstützung kundenseitig verwalteter Schlüssel für Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit Azure-Aktivitätsprotokollen zum Erstellen von Warnungen, wenn Änderungen an wichtigen Ressourcen vorgenommen werden. Zu diesen Ressourcen können Produktionsinstanzen von Recovery Services-Tresoren, Ressourcen des Site Recovery-Diensts sowie verwandte Ressourcen gehören.
- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph zum Abfragen oder Ermitteln aller Ressourcen in Ihren Abonnements, einschließlich Recovery Services-Tresore. Stellen Sie entsprechende Leseberechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Recovery Services-Tresore und andere verwandte Ressourcen an, die mit Metadaten von Site Recovery verwendet werden, um sie logisch in einer Taxonomie zu sortieren.
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tags, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Site Recovery (Recovery Services-Tresore) und andere verwandte Ressourcen zu organisieren und zu überwachen. 

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen: 

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen entsprechend der geschäftlichen Anforderungen des Kunden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen: 

- Not allowed resource types (Unzulässige Ressourcentypen) 
- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in den Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen) 
- Zulässige Ressourcentypen

Zur Einhaltung Ihrer Unternehmensstandards und Vereinbarungen zum Servicelevel müssen Sie mit der Erstellung und Verwaltung von Richtlinien in Azure vertraut sein.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren. Dies kann das Erstellen und Ändern von Ressourcen innerhalb einer Hochsicherheitsumgebung verhindern.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihren Recovery Services-Tresor mit Azure Policy. 

Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration der Recovery Services-Tresorressourcen des Site Recovery-Diensts zu erstellen.
- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um durchgängig sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.
- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Repos, um Ihren Code sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen für Ihre Recovery Services-Tresore und verwandte Ressourcen verwenden.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. 

Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.RecoveryServices“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. 

Verwenden Sie die Azure Policy-Auswirkungen [audit] (überwachen), [deny] (verweigern) und [deploy if not exist] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.
- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Kunden sollten Site Recovery-Geheimnisse mit der Integration mit Azure Key Vault verwalten und die Notfallwiederherstellung für Azure Disk Encryption-fähige VMs aktivieren. 

- [Erstellen eines Schlüsseltresors](../key-vault/general/quick-create-portal.md)

- [Authentifizieren bei Azure Key Vault](../key-vault/general/authentication.md)

- [Zuweisen einer Azure Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

- [Aktivieren der Notfallwiederherstellung für Azure Disk Encryption-fähige VMs mithilfe von Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Site Recovery unterstützt vom System verwaltete Identitäten nur, wenn ein Kunde die vom System verwaltete Identität im Recovery Services-Tresor aktivieren kann. Die gleiche Methodik gilt für Ressourcen, die im Notfallwiederherstellungsangebot verwendet werden, um die Zugriffsbegrenzung zu definieren. 

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. 

Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Aktivieren der vom System verwalteten Identität für Recovery Services-Tresore](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

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

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host aktiviert, der die Azure-Dienste (z. B. Site Recovery) unterstützt. Das Feature wird jedoch nicht für Ihre Inhalte ausgeführt. Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage.

Mithilfe der Bedrohungserkennung für Datendienste in Security Center können Sie Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde.

- [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

- [Grundlegendes zur Bedrohungserkennung für Datendienste in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Site Recovery verwendet intern ein Azure Storage-Konto, um den Zustand der Notfallwiederherstellungslösung entsprechend der Konfiguration durch die Kunden in ihren Workloads zu verwalten.

Alle Speicherressourcen werden von den Metadaten von Site Recovery-Diensten mit der Konfiguration des folgenden Typs verwendet: Georedundanter Speicher mit Lesezugriff (RA-GRS). Speicherkonten mit einem Typ über GRS (z. B. RAGRS, RAG-ZRS) replizieren Ihre Daten in einer sekundären Region (Hunderte Meilen vom primären Standort der Quelldaten entfernt), um die Notfallwiederherstellung während Ausfällen weiterhin für Kunden bereitzustellen.

Hierfür ist das Site Recovery-Team zuständig. Kunden können Key Vault-Schlüssel in Azure sichern.

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Testen Sie Wiederherstellungen von gesicherten kundenseitig verwalteten Schlüsseln regelmäßig.

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Ruhende Daten werden mithilfe der Speicherdienstverschlüsselung mit IaaS-basierten VMs von Azure verschlüsselt. Aktivieren von vorläufigem Löschen in Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Vorläufiges Löschen für Azure Storage-Blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. 

Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Priorisieren Sie, welche Warnungen zuerst untersucht werden sollen anhand des von Security Center zugewiesenen Schweregrads für Warnungen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Abonnements aussagekräftig (z. B. „Produktion“, „Nicht-Produktion“), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan nach Bedarf.

- [Siehe NIST-Veröffentlichung: „Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen“](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. 

Erstellen Sie einen Prozess zur Überprüfung von Incidents, nachdem sie aufgetreten sind, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. 

Verwenden Sie den Security Center-Datenconnector zum Streamen der Warnungen an Azure Sentinel.
- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.
- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](../security/benchmarks/overview.md) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).