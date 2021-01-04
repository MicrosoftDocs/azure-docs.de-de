---
title: Azure-Sicherheitsbaseline für Azure Firewall
description: Die Azure Firewall-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 764b10d1af2a037c4d5285ae42669ea2345779e0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660507"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Azure-Sicherheitsbaseline für Azure Firewall

Bei dieser Sicherheitsbaseline werden Empfehlungen des [Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview.md) auf Azure Firewall angewendet. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und durch die entsprechenden, für Azure Firewall geltenden Empfehlungen definiert werden. Nicht auf Azure Firewall anwendbare **Steuerungen** wurden ausgeschlossen. Sehen Sie sich die [vollständige Zuordnungsdatei der Azure Firewall-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an, um zu ermitteln, welche Gesamtzuordnung zwischen Azure Firewall und dem Vergleichstest für die Azure-Sicherheit besteht.

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Azure Firewall ist zur Protokollierung von Datenverkehr, der von der Firewall verarbeitet wird, in Azure Monitor integriert.

Verwenden Sie zusätzlich Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Netzwerkressourcen zu schützen, die sich auf Azure Firewall beziehen.

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Aktivieren Sie das Filtern auf Basis von Threat Intelligence, damit Sie vor Datenverkehr von und zu bekannten schädlichen IP-Adressen und Domänen gewarnt werden und dieser Datenverkehr abgelehnt wird. Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit Sie vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen gewarnt werden und dieser Datenverkehr abgelehnt wird.

- [Threat Intelligence-gestütztes Filtern für Azure Firewall](threat-intel.md)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Auf einer Azure Firewall-Instanz steht ein Diensttag für eine Gruppe von IP-Adresspräfixen und soll die Komplexität bei der Erstellung von Sicherheitsregeln verringern.

Azure Firewall-Diensttags können im Zielfeld für Netzwerkregeln verwendet werden und definieren die Netzwerkzugriffssteuerungen von Azure Firewall. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen.

Darüber hinaus werden auch vom Kunden definierte Tags, z. B. IP-Adressgruppen, unterstützt und können in einer Netzwerk- oder Anwendungsregel verwendet werden. FQDN-Tags in Anwendungsregeln werden unterstützt, um den erforderlichen ausgehenden Netzwerkdatenverkehr über die Firewall zuzulassen.

Beachten Sie, dass Sie kein eigenes Diensttag erstellen und auch nicht angeben können, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

 

- [Azure Firewall-Diensttags](service-tags.md)

- [Verfügbare Diensttags](../virtual-network/service-tags-overview.md#available-service-tags)

- [IP-Adressgruppen in Azure Firewall](ip-groups.md)

- [Übersicht über FQDN-Tags](fqdn-tags.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Azure Policy wird für Azure Firewall noch nicht vollständig unterstützt. Azure Firewall Manager kann verwendet werden, um eine Standardisierung der Sicherheitskonfigurationen zu erzielen.

Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelemente und Richtlinien in einer einzigen Blaupausendefinition verpacken. Sie können die Blaupause auf neue Abonnements anwenden sowie die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

- [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und Erkennen von Änderungen an Ihren Azure Firewall-Ressourcen. Erstellen Sie in Azure Monitor Warnungen, die bei Änderungen an wichtigen Ressourcen ausgelöst werden.

- [Überwachen von Azure Firewall-Protokollen und -Metriken](./firewall-diagnostics.md)

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen für Azure Firewall. Kunden müssen eine Netzwerkregel erstellen, um diesen Zugriff zuzulassen, oder sie erstellen eine solche Regel für einen Zeitserver, den sie in ihrer Umgebung verwenden.

- [NTP-Serverzugriff](protect-windows-virtual-desktop.md#additional-considerations)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Sie können Protokolldaten aktivieren und das Onboarding für Azure Sentinel oder eine SIEM-Drittanbieterlösung durchführen, um die zentrale Sicherheitsprotokollverwaltung für verschiedene Protokolle zu ermöglichen.

Aktivitätsprotokolle können genutzt werden, um Vorgänge in Azure Firewall zu überprüfen und Aktionen für Ressourcen zu überwachen. Das Aktivitätsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Ressourcen, jedoch keine Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Azure Firewall verfügt auch über die folgenden Diagnoseprotokolle, um Informationen zu Kundenanwendungen und Netzwerkregeln bereitzustellen.

Anwendungsregelprotokoll: Jede neue Verbindung, die einer Ihrer konfigurierten Anwendungsregeln entspricht, führt zu einem Protokoll für die akzeptierte/abgelehnte Verbindung.

Netzwerkregelprotokoll: Jede neue Verbindung, die einer Ihrer konfigurierten Netzwerkregeln entspricht, führt zu einem Protokoll für die akzeptierte/abgelehnte Verbindung.

Hinweis: Beide Protokolle können nur dann in einem Speicherkonto gespeichert, an Event Hubs gestreamt bzw. an Azure Monitor-Protokolle gesendet werden, wenn sie für jede Azure Firewall-Instanz einer Umgebung aktiviert wurden.

- [Azure Firewall-Protokolle](logs-and-metrics.md)

Liste mit Ressourcenaktionen in Aktivitätsprotokollen: Vorgänge für Azure Resource Manager-Ressourcenanbieter

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle können genutzt werden, um Vorgänge in Azure Firewall zu überprüfen und Aktionen für Ressourcen zu überwachen. Das Aktivitätsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE) für Azure-Ressourcen, aber keine Lesevorgänge (GET). Azure Firewall verfügt auch über die folgenden Diagnoseprotokolle, um Informationen zu Kundenanwendungen und Netzwerkregeln bereitzustellen. 

Anwendungsregelprotokoll: Jede neue Verbindung, die einer Ihrer konfigurierten Anwendungsregeln entspricht, führt zu einem Protokoll für die akzeptierte/abgelehnte Verbindung.

Netzwerkregelprotokoll: Jede neue Verbindung, die einer Ihrer konfigurierten Netzwerkregeln entspricht, führt zu einem Protokoll für die akzeptierte/abgelehnte Verbindung.

Beachten Sie, dass beide Protokolle nur dann in einem Speicherkonto gespeichert, an Event Hubs gestreamt bzw. an Azure Monitor-Protokolle gesendet werden können, wenn sie für jede Azure Firewall-Instanz einer Umgebung aktiviert wurden.

- [Azure Firewall-Protokolle](logs-and-metrics.md)

- [Liste mit Ressourcenaktionen in Aktivitätsprotokollen](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Der Aufbewahrungszeitraum für einen Log Analytics-Arbeitsbereich kann gemäß den Compliancevorschriften Ihrer Organisation in Azure Monitor festgelegt werden. Die Datenaufbewahrung kann für alle Arbeitsbereiche auf 30 bis 730 Tage (2 Jahre) festgelegt werden. Dies richtet sich jeweils nach dem gewählten Tarif.

Es gibt drei Optionen für die Aufbewahrung von gespeicherten Protokollen:

Speicherkonten eignen sich am besten für Protokolle, die eine längere Zeit gespeichert und bei Bedarf überprüft werden.

Event Hubs sind eine hervorragende Möglichkeit für die Integration in andere SIEM-Tools (Security Information and Event Management), um Warnungen für Ihre Ressourcen zu erhalten.

Azure Monitor-Protokolle eignen sich am besten für eine allgemeine Echtzeitüberwachung Ihrer Anwendung oder zum Beobachten von Trends.

- [Azure Firewall-Protokolle und -Metriken](logs-and-metrics.md)

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Azure Firewall ist zum Anzeigen und Analysieren von Firewallprotokollen in Azure Monitor integriert. Protokolle können an Log Analytics, Azure Storage oder Event Hubs gesendet werden. Sie können in Log Analytics oder von anderen Tools, wie Excel und Power BI, analysiert werden. Es gibt einige unterschiedliche Arten von Azure Firewall-Protokollen.

Aktivitätsprotokolle können genutzt werden, um Vorgänge in Azure Firewall zu überprüfen und Aktionen für Ressourcen zu überwachen. Das Aktivitätsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE) für Ressourcen, aber keine Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Azure Firewall verfügt auch über Diagnoseprotokolle, um Informationen zu Kundenanwendungen und Netzwerkregeln bereitzustellen.

Anwendungsregelprotokolle werden jeweils erstellt, wenn eine neue Verbindung, die einer Ihrer konfigurierten Anwendungsregeln entspricht, zu einem Protokoll für die akzeptierte/abgelehnte Verbindung führt. 

Netzwerkregelprotokolle werden jeweils erstellt, wenn eine neue Verbindung, die einer Ihrer konfigurierten Netzwerkregeln entspricht, zu einem Protokoll für die akzeptierte/abgelehnte Verbindung führt.

Beachten Sie, dass beide Protokolle nur dann in einem Speicherkonto gespeichert, an Event Hubs gestreamt bzw. an Azure Monitor-Protokolle gesendet werden können, wenn sie für jede Azure Firewall-Instanz einer Umgebung aktiviert wurden.

Azure Monitor-Protokolle eignen sich am besten für eine allgemeine Echtzeitüberwachung Ihrer Anwendung oder zum Beobachten von Trends.

- [Azure Firewall-Protokolle und -Metriken](logs-and-metrics.md)

- [Diagnoseprotokolle](logs-and-metrics.md#diagnostic-logs)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center mit einem Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. 

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren. 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure AD umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Sie können außerdem einen Just-in-Time- oder Just-Enough-Zugriff aktivieren, indem Sie mit Azure AD Privileged Identity Management verwaltete privilegierte Rollen für Microsoft-Dienste und Azure Resource Manager verwenden.

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA), die für die Anmeldung und Konfiguration von Azure Firewall und der zugehörigen Ressourcen konfiguriert sind. 

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten. 

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md) 

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen. 

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert. 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist. 

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren. 

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen. 

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure Firewall und der zugehörigen Ressourcen, die vertrauliche Informationen speichern oder verarbeiten. 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. Sie können die Zugriffsebene auf die Azure Firewall-Ressourcen beschränken, die von Ihren Anwendungen und Unternehmensumgebungen angefordert werden. Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Azure-Zugriffssteuerung regeln. 

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Nutzen Sie eine Drittanbieterlösung von Azure Marketplace an Netzwerkperimetern, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt. 

Bei der zugrundeliegenden Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung. 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die eine Verbindung mit Ihrer Azure Firewall-Instanz und den zugehörigen Ressourcen herstellen, die Aushandlung über TLS 1.2 oder höher durchführen können. 

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung. 

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Verwenden Sie ein Drittanbietertool für die aktive Ermittlung, um alle vertraulichen Informationen zu identifizieren, die über Azure Firewall und die zugehörigen Ressourcen in Azure gespeichert werden, und aktualisieren Sie den Bestand an vertraulichen Informationen der Organisation.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, Azure RBAC) in Azure, um den Zugriff auf Azure Firewall und die zugehörigen Ressourcen zu steuern.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verwenden Sie die Verschlüsselung ruhender Daten auf allen Azure-Ressourcen mit Azure Firewall und den zugehörigen Ressourcen. Microsoft empfiehlt, Azure die Verwaltung Ihrer Verschlüsselungsschlüssel zu erlauben. Es gibt jedoch in einigen Fällen die Möglichkeit, ihre eigenen Schlüssel zu verwalten. 

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurieren der von Kunden verwalteten Verschlüsselungsschlüssel](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen in Azure Firewall ausgegeben werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Azure Firewall und die zugehörigen Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren. 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements, um Azure Firewall und die zugehörigen Ressourcen zu organisieren und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie gemäß den Anforderungen Ihrer Organisation einen Bestand an genehmigten Azure Firewall-Ressourcen, einschließlich der Konfiguration.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Azure Firewall-Ressourcen in den zugehörigen Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure Firewall-Instanzen und die zugehörigen Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Implementieren Sie Ihren eigenen Prozess zur Entfernung von nicht genehmigten Azure Firewall-Instanzen und der zugehörigen Ressourcen. Sie können auch eine Drittanbieterlösung nutzen, um nicht genehmigte Azure Firewall-Instanzen und die zugehörigen Ressourcen zu identifizieren.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um einzuschränken, welche Dienste in Ihrer Umgebung bereitgestellt werden können.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren. 

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Anwendungen, die für Geschäftsvorgänge ggf. erforderlich sind, oder Umgebungen mit variierenden Risikoprofilen für die Organisation sollten isoliert und durch separate Azure Firewall-Instanzen voneinander getrennt werden.

- [Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](deploy-cli.md)

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Mit Azure Resource Manager ist es möglich, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen bzw. übertreffen.

Sie können auch Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

Azure Policy wird derzeit nicht vollständig unterstützt. 

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen für Ihre Azure Firewall-Instanz und die zugehörigen Ressourcen zu erzwingen.  Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die Sicherheitskonfiguration der von Ihrer Organisation benötigten Azure Firewall-Instanz und der zugehörigen Ressourcen zu verwalten.

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Hierzu zählen beispielsweise benutzerdefinierte Azure-Richtlinien und Azure Resource Manager-Vorlagen. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierte Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Firewall und die zugehörigen Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure Firewall-Ressourcen zu erstellen. Sie können auch auf integrierte Richtliniendefinitionen zurückgreifen, die sich auf Ihre spezifischen Ressourcen beziehen.  

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Aliase](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie jeden Dienst, der die Azure AD-Authentifizierung unterstützt, für Azure Resource Manager authentifizieren. Außerdem können sie mit der API, dem Azure-Portal, der CLI und PowerShell verwendet werden.

- [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Azure Firewall und die zugehörigen Ressourcen in eine JSON-Vorlage (JavaScript Object Notation) zu exportieren, die als Sicherung für Azure Firewall und die zugehörigen Konfigurationen genutzt werden kann.  Sie können die Azure Firewall-Konfiguration auch über das Azure-Portal mit der Funktion „Vorlage exportieren“ von Azure Firewall exportieren.  Verwenden Sie Azure Automation, um die Sicherungsskripts automatisch auszuführen.

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Bereitstellen von Azure Firewall mit einer Vorlage](deploy-template.md)

- [Referenz zur Vorlage „Microsoft.Network/azureFirewalls“](/azure/templates/microsoft.network/azurefirewalls)

- [Grundlegendes zu Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Azure Firewall und die zugehörigen Ressourcen in eine JSON-Vorlage (JavaScript Object Notation) zu exportieren, die als Sicherung für Azure Firewall und die zugehörigen Konfigurationen genutzt werden kann.  Sie können die Azure Firewall-Konfiguration auch über das Azure-Portal mit der Funktion „Vorlage exportieren“ von Azure Firewall exportieren.

- [Bereitstellen von Azure Firewall mit einer Vorlage](deploy-template.md)

- [Referenz zur Vorlage „Microsoft.Network/azureFirewalls“](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie sicher, dass regelmäßig eine Wiederherstellung mithilfe von Azure Resource Manager-Vorlagendateien durchgeführt werden kann.  

- [Bereitstellen von Azure Firewall mit einer Vorlage](deploy-template.md)

- [Referenz zur Vorlage „Microsoft.Network/azureFirewalls“](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Hierzu zählen beispielsweise benutzerdefinierte Azure-Richtlinien und Azure Resource Manager-Vorlagen. Um die Ressourcen, die Sie in Azure DevOps verwalten, zu schützen, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierten Gruppen (bei Integration in Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration in TFS) Berechtigungen erteilen oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. 

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand. 

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung. 

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf. 

- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden. 

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln.

Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen. 

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen. 

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).