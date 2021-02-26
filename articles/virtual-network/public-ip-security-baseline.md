---
title: Azure-Sicherheitsbaseline für Azure Public IP
description: Die Sicherheitsbaseline für Azure Public IP enthält Schritt-für-Schritt-Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Vergleichstest für die Azure-Sicherheit.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cba362004e764ffbbc734fb06c8898f380ffdf88
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806917"
---
# <a name="azure-security-baseline-for-azure-public-ip"></a>Azure-Sicherheitsbaseline für Azure Public IP

Diese Sicherheitsbaseline wendet Empfehlungen der [Version 1.0 des Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview.md) auf Azure Public IP an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Vergleichstest für die Azure-Sicherheit und die entsprechenden, für Azure Public IP geltenden Empfehlungen definiert werden. Für Azure Public IP nicht relevante **Kontrollen** wurden ausgeschlossen.  Beachten Sie, dass von Azure Public IP-Instanzen keine Kundendaten gespeichert werden.

Die vollständige Zuordnung zwischen Azure Public IP und dem Azure-Sicherheitsvergleichstest finden Sie in der [Datei mit der vollständigen Zuordnung der Sicherheitsbaseline für Azure Public IP](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Azure Public IP-Instanzen können Tags zugewiesen werden. Verwenden Sie Ressourcentags für Netzwerksicherheitsgruppen und andere Ressourcen im Zusammenhang mit Netzwerksicherheit.  Verwenden Sie beliebige der integrierten taggingbezogenen Azure Policy-Definitionen (beispielsweise „Tag und zugehörigen Wert erzwingen“), um sicherzustellen, dass alle Ressourcen mit Tags erstellt und Sie auf nicht markierte Ressourcen aufmerksam gemacht werden.  

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen für diese Ressourcen auszuführen. 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md) 

- [Erstellen eines virtuellen Azure-Netzwerks](quick-create-portal.md) 

- [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen-Regeln](tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll, um Konfigurationen zu überwachen und Änderungen an Ihren Public IP-Instanzen zu erkennen. Von Public IP selbst werden nur auf der Steuerungsebene (z. B. im Azure-Portal) Protokolle im Zusammenhang mit Netzwerkdatenverkehr generiert.

Public IP bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.

Stattdessen können Sie Daten in Azure Sentinel oder in einer SIEM-Drittanbieterlösung aktivieren und integrieren.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll, um Konfigurationen zu überwachen und Änderungen für Ihre Public IP-Instanzen zu erkennen. Von Public IP selbst werden nur auf der Steuerungsebene (z. B. im Azure-Portal) Überwachungsprotokolle generiert. Public IP bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie mithilfe von Azure Monitor den Protokollaufbewahrungszeitraum für die Log Analytics-Arbeitsbereiche, die Public IP-Instanzen zugeordnet sind, gemäß den Complianceanforderungen Ihrer Organisation fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Public IP bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk. 

Verwenden Sie das Azure-Aktivitätsprotokoll, um Konfigurationen zu überwachen und Änderungen für Ihre Public IP-Instanzen zu erkennen. 

Von Public IP selbst werden nur auf der Steuerungsebene (z. B. im Azure-Portal) Protokolle im Zusammenhang mit Netzwerkdatenverkehr generiert.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Konfigurieren Sie Ihre Warnungen auf der Grundlage von Aktivitätsprotokollen im Zusammenhang mit Public IP. Verwenden Sie Azure Monitor, um einen Alarm zu konfigurieren, durch den eine E-Mail-Benachrichtigung gesendet oder ein Webhook oder eine Azure-Logik-App aufgerufen wird.

- [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwenden Sie die Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung), um den Zugriff auf Azure-Ressourcen mit Rollenzuweisungen zu verwalten. Weisen Sie diese Rollen Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zu. 

Verwenden Sie Tools wie die Azure-Befehlszeilenschnittstelle, Azure PowerShell oder das Azure-Portal, um vordefinierte integrierte Azure-Rollen für bestimmte Ressourcen zu inventarisieren oder abzufragen.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. 

Ermöglichen Sie Just-In-Time-Zugriff mithilfe von Azure Active Directory (Azure AD) Privileged Identity Management (PIM) und Azure Resource Manager. 

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure Active Directory Multi-Factor Authentication, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Privileged Access Workstation (PAW) mit Azure AD Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration Ihrer Azure Sentinel-fähigen Ressourcen berechtigt ist.

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (Azure AD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Überprüfen Sie Azure AD-Risikoerkennungen auf Warnungen und Berichte zum Verhalten von Risikobenutzern, und ergreifen Sie entsprechende Maßnahmen.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Ermitteln Sie veraltete Konten anhand von Protokollen in Azure Active Directory (Azure AD). 

Verwenden Sie Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass Benutzer genehmigten und unterbrechungsfreien Zugriff haben.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Implementieren Sie eine Integration für ein beliebiges SIEM-/Überwachungstool auf der Grundlage Ihres Zugriffs auf Quellen mit Protokollen im Zusammenhang mit Azure AD-Anmeldeaktivitäten (Azure Active Directory) sowie mit der Überwachung und mit Risikoereignissen.
Optimieren Sie diesen Prozess, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich. 

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen (Azure Active Directory) zum Identitätsschutz können Sie automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Erfassen Sie bei Bedarf Daten zur weiteren Untersuchung in Azure Sentinel, um geschäftliche Anforderungen zu erfüllen.
- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (etwa Computeressourcen, Speicher, Netzwerke, Ports, Protokolle und Ähnliches) in Ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

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

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

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

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Public IP mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien für die Überwachung oder Erzwingung der Netzwerkkonfiguration Ihrer Azure Public IP-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Public IP mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien für die Überwachung oder Erzwingung der Netzwerkkonfiguration Ihrer Azure Public IP-Instanzen zu erstellen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Azure Policy-Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Auswirkungen [Audit], [Deny] und [DeployIfNotExists], um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

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

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen. 

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).