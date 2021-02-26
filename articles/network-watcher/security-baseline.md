---
title: Azure-Sicherheitsbaseline für Network Watcher
description: Azure-Sicherheitsbaseline für Network Watcher
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b7dc7a9f93d6d70abec57632ffc95aea8a6bc699
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804987"
---
# <a name="azure-security-baseline-for-network-watcher"></a>Azure-Sicherheitsbaseline für Network Watcher

Die Azure-Sicherheitsbaseline für Network Watcher enthält Empfehlungen, mithilfe derer Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Nicht zutreffend; Network Watcher bietet die Möglichkeit, die Verbindung zwischen einem Endpunkt und einem virtuellen Computer zu überwachen, kann aber nicht durch ein virtuelles Netzwerk, eine Netzwerksicherheitsgruppe oder Azure Firewall gesichert werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Nicht zutreffend; Azure Network Watcher bietet die Möglichkeit, Datenflussprotokolle zu analysieren und umfassende Visualisierungen von Daten bereitzustellen, Network Watcher selbst kann jedoch keine Datenflussprotokolle oder erfassbare Pakete erzeugen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Nicht zutreffend; bei Azure Network Watcher handelt es sich nicht um eine Ressource mit möglichen Risiken durch öffentliche Netzwerke, die vor schädlichem Datenverkehr geschützt werden muss.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Nicht zutreffend; Azure Network Watcher bietet die Möglichkeit, Datenflussprotokolle zu analysieren und umfassende Visualisierungen von Daten bereitzustellen, Network Watcher selbst kann jedoch keine Datenflussprotokolle oder erfassbare Pakete erzeugen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Nicht zutreffend; bei Azure Network Watcher handelt es sich nicht um eine Ressource mit möglichen Risiken durch öffentliche Netzwerke, die vor schädlichem Datenverkehr geschützt werden muss.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Nicht zutreffend; Azure Network Watcher bietet die Möglichkeit, Datenflussprotokolle zu analysieren und umfassende Visualisierungen von Daten bereitzustellen. Network Watcher selbst ist jedoch kein Endpunkt, mit dem Sie Datenverkehr mit Diensttags oder Netzwerksicherheitsgruppen filtern oder zulassen können.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Network Watcher mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Network Watcher-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:

Bereitstellen von Network Watcher beim Erstellen virtueller Netzwerke

Network Watcher muss aktiviert sein

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Nicht zutreffend; obwohl Azure Network Watcher Tags unterstützt, steuert Network Watcher selbst nicht den Fluss von Netzwerkdatenverkehr.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll, um Änderungen an Azure Network Watcher zu überwachen. Sie haben die Möglichkeit, in Azure Monitor Warnungen zu erstellen, die bei Änderungen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Nicht umsetzbar; Microsoft verwaltet die Zeitquelle von Azure-Ressourcen, wie z. B. Azure Network Watcher, für die Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Konfigurationen und zum Erkennen von Änderungen an Ihren Azure Network Watcher-Instanzen. Anders als auf der Steuerungsebene (z. B. Azure-Portal) generiert Network Watcher selbst keine Protokolle in Zusammenhang mit dem Netzwerkdatenverkehr. Network Watcher bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Grundlegendes zu Network Watcher](./network-watcher-monitoring-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Konfigurationen und zum Erkennen von Änderungen an Ihren Azure Network Watcher-Instanzen. Anders als auf der Steuerungsebene (z. B. Azure-Portal) generiert Network Watcher selbst keine Überwachungsprotokolle. Network Watcher bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Grundlegendes zu Network Watcher](./network-watcher-monitoring-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum für Protokolle für Log Analytics-Arbeitsbereiche, die Azure Network Watcher zugeordnet sind, gemäß den Compliancevorschriften Ihrer Organisation fest.

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Konfigurationen und zum Erkennen von Änderungen an Ihren Azure Network Watcher-Instanzen. Anders als auf der Steuerungsebene (z. B. Azure-Portal) generiert Network Watcher selbst keine Protokolle in Zusammenhang mit dem Netzwerkdatenverkehr. Network Watcher bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Grundlegendes zu Network Watcher](./network-watcher-monitoring-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Sie können konfigurieren, dass Sie Warnungen auf Grundlage von Aktivitätsprotokollen im Zusammenhang mit Azure Network Watcher erhalten. Mit Azure Monitor können Sie einen Alarm so konfigurieren, dass eine E-Mail-Benachrichtigung gesendet bzw. ein Webhook oder eine Azure-Logik-App aufgerufen wird.

* [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend; in Azure Network Watcher werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend; in Azure Network Watcher werden keine Protokolle zu DNS verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie einen Bestand der Benutzerkonten, die über Administratorzugriff auf die Steuerungsebene (Azure-Portal) von Azure Network Watcher verfügen. Damit die Network Watcher-Funktionen verwendet werden kann, muss das Konto, mit dem Sie sich bei Azure anmelden, den internen Rollen „Besitzer“, „Mitwirkender“ oder „Netzwerkmitwirkender“ oder einer benutzerdefinierten Rolle zugewiesen werden, die den Aktionen für die einzelnen Network Watcher-Funktionen zugeordnet sind.

Sie können die Identitäts- und Zugriffssteuerung (IAM) im Azure-Portal für Ihr Abonnement verwenden, um die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu konfigurieren. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

* [Erforderliche RBAC-Berechtigungen (Role-Based Access Control) für die Verwendung von Network Watcher-Funktionen](./required-rbac-permissions.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure AD verfügt nicht über das Konzept von Standardkennwörtern. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Nicht zutreffend; mit einmaligem Anmelden (Single Sign-On, SSO) ist für Sicherheit und Komfort gesorgt, wenn sich Benutzer in Azure Active Directory (AD) bei benutzerdefinierten Anwendungen anmelden. Der Zugriff auf Azure Network Watcher ist bereits in Azure Active Directory integriert und erfolgt über das Azure-Portal sowie über die Azure Resource Manager-REST-API.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Privileged Access Workstation (PAW) mit Azure AD Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration Ihrer Azure Sentinel-fähigen Ressourcen berechtigt ist.

* [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

* [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie für Ihre Azure Sentinel-Instanzen Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

* [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Verwenden Sie Azure Active Directory (AD) als zentrales Authentifizierungs- und Autorisierungssystem für Azure Network Watcher. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten sowie auf Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration mit Azure Sentinel oder der SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie für abweichendes Verhalten bei der Kontoanmeldung auf Steuerungsebene (z. B. Azure-Portal) Azure AD Identity Protection und Risikoerkennungsfeatures, um automatisierte Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Bericht „Riskante Anmeldungen“ im Azure Active Directory-Portal](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht zutreffend; die Kunden-Lockbox wird von Azure Network Watcher nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Network Watcher und die zugehörigen Ressourcen und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Wenn Sie Azure VPN Gateway zum Erstellen einer sicheren Verbindung zwischen Ihrem lokalen Netzwerk und Ihren virtuellen Azure-Netzwerken verwenden, stellen Sie sicher, dass Ihr lokales Netzwerkgateway mit kompatiblen IPSec-Kommunikations- und Verschlüsselungsparametern konfiguriert wurde. Jede Fehlkonfiguration führt zu einem Verlust der Konnektivität zwischen dem lokalen Netzwerk und Azure.

* [Unterstützte IPSec-Parameter für Azure VPN Gateway](./network-watcher-diagnose-on-premises-connectivity.md)

* [Konfigurieren einer Site-to-Site-Verbindung im Azure-Portal](../vpn-gateway/tutorial-site-to-site-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Sie können die Identitäts- und Zugriffssteuerung (IAM) im Azure-Portal für Ihr Abonnement verwenden, um die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu konfigurieren. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden.

Damit die Network Watcher-Funktionen verwendet werden kann, muss das Konto, mit dem Sie sich bei Azure anmelden, den internen Rollen „Besitzer“, „Mitwirkender“ oder „Netzwerkmitwirkender“ oder einer benutzerdefinierten Rolle zugewiesen werden, die den Aktionen für die einzelnen Network Watcher-Funktionen zugeordnet sind.

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Grundlegendes zu Azure RBAC-Berechtigungen in Network Watcher](./required-rbac-permissions.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Network Watcher und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

* [Schutz der Azure-Kundendaten](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten. Network Watcher speichert Protokolle und andere Informationen in Azure Storage, wobei die Daten im Ruhezustand verschlüsselt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Azure Network Watcher und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Nicht zutreffend; Microsoft verwaltet Sicherheitsrisiken auf den zugrunde liegenden Systemen, die Azure Network Watcher unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend; Microsoft verwaltet Sicherheitsrisiken auf den zugrunde liegenden Systemen, die Azure Network Watcher unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend; Microsoft verwaltet Sicherheitsrisiken auf den zugrunde liegenden Systemen, die Azure Network Watcher unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

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

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

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

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Network Watcher mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Network Watcher-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:

* [Beim Erstellen virtueller Netzwerke Network Watcher bereitstellen](https://github.com/Azure/azure-policy/blob/master/samples/built-in-policy/deploy-network-watcher-in-vnet-regions/README.md)

* [Siehe auch: Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Network Watcher mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Network Watcher-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:

* [Beim Erstellen virtueller Netzwerke Network Watcher bereitstellen](https://github.com/Azure/azure-policy/blob/master/samples/built-in-policy/deploy-network-watcher-in-vnet-regions/README.md)

Siehe auch:

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Azure Policy-Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Auswirkungen [Audit], [Deny] und [DeployIfNotExists], um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Nicht zutreffend; es sind keine Kennwörter, Geheimnisse oder Schlüssel mit Azure Network Watcher verknüpft.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Nicht zutreffend; Azure Network Watcher nutzt keine verwalteten Identitäten.

* [Azure-Dienste mit Unterstützung verwalteter Identitäten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Nicht zutreffend Network Watcher funktioniert nicht mit Daten, die vom Benutzer hochgeladen wurden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Nicht zutreffend; Azure Network Watcher enthält selbst keine Kundendaten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden:** * Befolgen Sie die [Einsatzregeln für Penetrationstests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1), damit diese alle Anforderungen der Microsoft-Richtlinien erfüllen.

* Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).