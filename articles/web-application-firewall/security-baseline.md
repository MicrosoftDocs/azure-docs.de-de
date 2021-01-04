---
title: Azure-Sicherheitsbaseline für Azure Web Application Firewall
description: Die Azure Web Application Firewall-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cb5fae022957dd5fdc56058ed08b92734929d13e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562297"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure-Sicherheitsbaseline für Azure Web Application Firewall

Diese Sicherheitsbaseline wendet Empfehlungen der [Version 1.0 des Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview-v1.md) auf die Azure Web Application Firewall an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Vergleichstest für die Azure-Sicherheit und die entsprechenden, für Azure Web Application Firewall geltenden Empfehlungen definiert werden. Nicht auf Azure Web Application Firewall anwendbare **Steuerungen** wurden ausgeschlossen. 

Sehen Sie sich die [vollständige Zuordnungsdatei der Azure Web Application Firewall-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an, um zu ermitteln, welche Gesamtzuordnung zwischen Azure Web Application Firewall und dem Vergleichstest für die Azure-Sicherheit besteht.

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Verwenden Sie Microsoft Azure Web Application Firewall (WAF) für den zentralisierten Schutz von Webanwendungen vor gängigen Exploits und Sicherheitsrisiken wie der Einschleusung von SQL-Befehlen und Cross-Site Scripting. 

- Erkennungsmodus: Verwenden Sie diesen Modus, um den Netzwerkverkehr zu ermitteln und falsch positive Ergebnisse zu verstehen und zu überprüfen. Er überwacht und protokolliert alle Bedrohungswarnungen. Stellen Sie sicher, dass Diagnose und das WAF-Protokoll ausgewählt und aktiviert sind. Beachten Sie, dass die WAF keine eingehenden Anforderungen blockiert, wenn sie im Erkennungsmodus betrieben wird.
- Schutzmodus: Blockiert Eindringversuche und Angriffe, die von den Regeln erkannt werden. Ein Angreifer erhält eine Ausnahme vom Typ „403 (nicht autorisierter Zugriff)“, und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

Erstellen Sie eine Baseline Ihres Netzwerkdatenverkehrs mit dem Erkennungsmodus der WAF. Nachdem Sie Ihre Datenverkehrsanforderungen ermittelt haben, konfigurieren Sie die WAF im Präventionsmodus, um unerwünschten Datenverkehr zu blockieren.

Gehen Sie Empfehlungen zu hohen Schweregraden aus dem Security Center für alle webfähigen Ressourcen nach, die nicht durch die WAF geschützt sind.  

- [CRS-Regelgruppen und -Regeln der Web Application Firewall](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF-Modi für Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF-Modi für Front Door](afds/afds-overview.md#waf-modes)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Verwenden Sie benutzerdefinierte Regeln mit der Azure Web Application Firewall (WAF), um Datenverkehr zuzulassen oder zu blockieren. Beispielsweise kann der gesamte Datenverkehr aus einem IP-Adressbereich blockiert werden. Konfigurieren Sie Azure WAF für die Ausführung im Schutzmodus, in dem Eindringlinge und Angriffe, die von den Regeln erkannt werden, aktiv blockiert werden. Ein Angreifer erhält eine Ausnahme vom Typ „403 (nicht autorisierter Zugriff)“, und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

- [WAF-Modi für Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF-Modi für Front Door](afds/afds-overview.md#waf-modes)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Azure Web Application Firewall (WAF) ist eine Kernkomponente der Webanwendungsschutzfunktionen von Azure. Verwenden Sie Azure WAF, um zentralisierten Schutz für Webanwendungen vor häufigen Exploits und Sicherheitsrisiken durch einen vorkonfigurierten verwalteten Regelsatz mit bekannten Angriffssignaturen aus den Top 10-Kategorien von OWASP bereitzustellen.

Passen Sie Azure WAF mit Regeln und Regelgruppen an Webanwendungsanforderungen an, um falsch positive Ergebnisse zu vermeiden. Ordnen Sie eine Azure WAF-Richtlinie für jede Website hinter einer WAF zu, um eine websitespezifische Konfiguration zu erhalten. Azure WAF unterstützt Geofilterung, Ratenbegrenzung und von Azure verwaltete Standardregelsatz-Regeln. Ferner können benutzerdefinierte Regeln so erstellt werden, dass sie die Anforderungen einer Anwendung erfüllen. 

Konfigurieren Sie die Azure WAF so, dass Sie im Präventionsmodus ausgeführt wird, nachdem Sie eine Baseline des Netzwerkdatenverkehrs im Erkennungsmodus für einen bestimmten Zeitraum erstellt haben. Die Azure WAF blockiert Eindringlinge und Angriffe, die von den Regeln im Präventionsmodus erkannt werden. Ein Angreifer erhält eine Ausnahme vom Typ „403 (nicht autorisierter Zugriff)“, und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

- [WAF-Modi für Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF-Modi für Front Door](afds/afds-overview.md#waf-modes)

- [CRS-Regelgruppen und -Regeln der Web Application Firewall](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Erstellen, Zuordnen und logisches Organisieren von Ressourcen in einem Azure-Abonnement mit Tags, um gängige Anwendungsfehlkonfigurationen zu erkennen (z. B. Apache und IIS). 

Anwenden von Regeln und Regelgruppen auf Azure Web Application Firewall (WAF)-Richtlinien, basierend auf den angewendeten Tagmetadaten.

- [WAF-Richtlinie für Application Gateway](/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [WAF-Richtlinie für Front Door](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen, die der Azure Web Application Firewall (WAF) in Ihrem Azure Application Gateway-Subnetz zugeordnet sind, sowie für alle anderen Ressourcen, die mit der Netzwerksicherheit und dem Datenverkehrsfluss in Zusammenhang stehen. Verwenden Sie für einzelne Netzwerksicherheitsgruppen-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen, die Dauer usw. für Regeln festzulegen, die Datenverkehr in ein oder aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Wählen Sie Azure PowerShell oder die Azure CLI, um basierend auf ihren Tags nach Ressourcen zu suchen oder Aktionen für diese Ressourcen auszuführen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen bzw. Erkennen von Änderungen für Netzwerkeinstellungen und -ressourcen, die mit Ihren Azure Web Application Firewall (WAF)-Bereitstellungen in Zusammenhang stehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkeinstellungen oder -ressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Erstellen Sie eine Netzwerkregel für Azure Web Application Firewall (WAF), um den Zugriff auf einen NTP-Server mit dem entsprechenden Port und Protokoll, z. B. Port 123 über UDP, zuzulassen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Konfigurieren Sie Azure Web Application Firewall (WAF)-Protokolle, die an eine zentrale Verwaltungslösung für Sicherheitsprotokolle gesendet werden sollen wie Azure Sentinel oder die SIEM-Lösung eines Drittanbieters. Diese Protokolle umfassen Azure-Aktivitäts-, Diagnose- und Echtzeitprotokolle für WAF. Diese Protokolle können dann in verschiedenen Tools wie Azure Monitor, Excel und Power BI angezeigt werden. Azure Web Application Firewall-Protokolle geben Aufschluss darüber, welche Daten die Azure WAF auswertet, abgleicht und blockiert.

Azure Sentinel verfügt über eine integrierte Azure WAF-Arbeitsmappe, die einen Überblick über die Sicherheitsereignisse in der Azure WAF bietet. Diese Arbeitsmappe umfasst Ereignisse, Übereinstimmungs- und Blockierungsregeln und alle anderen Daten, die in den Firewallprotokollen aufgezeichnet werden. Diese Telemetriedaten können verwendet werden, um die Playbookautomatisierung zu starten, um zu benachrichtigen oder auf Grundlage von mit Sentinel erfassten WAF-Ereignissen Korrekturmaßnahmen zu ergreifen.

- [Anzeigen von Aktivitätsprotokollen](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnoseprotokolle für Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Verbinden von Daten von Microsoft Web Application Firewall mit Azure Sentinel](../sentinel/connect-azure-waf.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Protokollierung in Ihren Azure Web Application Firewall (WAF)-Ressourcen für den Zugriff auf Überwachungs-, Sicherheits- und Diagnoseprotokolle. Azure Web Application Firewall bietet detaillierte Berichte zu jeder der erkannten Bedrohungen, die in den konfigurierten Diagnoseprotokollen verfügbar gemacht werden. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente.

- [Übersicht über die Protokollierung](ag/ag-overview.md#logging)

- [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

- [Übersicht über Protokolle der Azure-Plattform](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Senden Sie Azure Web Application Firewall (WAF)-Protokolle an ein benutzerdefiniertes Speicherkonto, und definieren Sie die Aufbewahrungsrichtlinie. Verwenden Sie Azure Monitor, um den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs auf Grundlage der Complianceanforderungen Ihrer Organisation festzulegen.
- [Konfigurieren der Überwachung für ein Speicherkonto](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Azure Web Application Firewall (WAF) bietet detaillierte Berichte zu jeder erkannten Bedrohung. Die Protokollierung ist in Azure-Diagnoseprotokolle integriert, die umfassende Informationen zu Vorgängen und Fehlern bieten, die zur Überwachung und Problembehandlung relevant sind. 

Azure WAF-Instanzen sind in das Security Center integriert, um Warnungen und Integritätsinformationen für die Berichtserstellung zu senden. Verwenden Sie Empfehlungen des Security Center, um ungeschützte Webanwendungen zu erkennen und diese anfälligen Ressourcen zu schützen. 

Azure Sentinel verfügt über eine integrierte Arbeitsmappe „WAF – Firewallereignisse“, die einen Überblick über die Sicherheitsereignisse in der WAF bietet. Hierzu gehören Ereignisse, abgeglichene und blockierte Regeln sowie alle anderen Daten, die in den Firewallprotokollen aufgezeichnet werden.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/index.yml) 

- [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Überwachung von Metriken und Protokollen in Azure Front Door](../frontdoor/front-door-diagnostics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure-Aktivitätsprotokolle sowie die Diagnoseeinstellungen für Ihre Azure WAF, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren. Erstellen Sie Warnungen für anormale Aktivitäten auf Grundlage von WAF-Metriken. Beispiel: wenn die blockierte Anzahl von Anforderungen den Wert „X“ überschreitet, mache „Y“.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Reagieren auf Ereignisse mit Azure Monitor-Warnungen](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. 

Azure WAF bietet zentralisierten Schutz für Ihre Webanwendungen vor gängigen Exploits und Sicherheitsrisiken und schützt Ihre Apps, indem eingehender Webdatenverkehr untersucht wird und Angriffe wie die Einschleusung von SQL-Befehlen, Cross-Site-Scripting, das Hochladen von Schadsoftware und DDoS-Angriffe unterbunden werden.

- [Bereitstellen von Azure WAF](ag/create-waf-policy-ag.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (Azure AD) umfasst integrierte Rollen, die abgefragt werden können und explizit zugewiesen werden müssen. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: In der WAF sind keine lokalen Administratorzuweisungen verfügbar. In der Umgebung können jedoch Azure Active Directory (Azure AD)-Administratorrollen vorhanden sein, die die Verwaltungskontrolle über die Azure WAF-Ressourcen ermöglichen.
Es ist eine bewährte Methode, Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten zu erstellen, die Zugriff auf Azure Web Application Firewall (WAF)-Instanzen haben. Verwenden Sie die Identitäts- und Zugriffsverwaltungsfeatures von Security Center, um die Anzahl der Administratorkonten zu überwachen.

- [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md)

- [Grundlegendes zum Erstellen von Administratorbenutzern in Azure Database for PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA), die für die Anmeldung und Konfiguration von Azure Web Application Firewall (WAF) und der zugehörigen Ressourcen konfiguriert ist. 

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Anleitung:** Konfigurieren Sie die Standortbedingung einer Richtlinie für bedingten Zugriff, und verwalten Sie Ihre benannten Standorte. 

Erstellen Sie logische Gruppierungen von IP-Adressbereichen oder Ländern und Regionen mit benannten Standorten. Beschränken Sie den Zugriff auf Ihre vertrauliche Ressourcen, z. B. Ihre Key Vault-Geheimnisse, auf die konfigurierten benannten Standorte.

- [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und in der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.
- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Überprüfen Sie den Benutzerzugriff regelmäßig, um sicherzustellen, dass nur aktive Benutzer fortwährenden Zugriff haben.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Integrieren Sie Azure AD-Anmeldeaktivitäten (Azure Active Directory) sowie Überwachungs- und Risikoereignis-Protokollquellen in beliebige SIEM- oder Überwachungstools wie Azure Sentinel.

Optimieren Sie diesen Prozess, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten (Azure AD) erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure Active Directory-Funktionen (Azure AD) zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Erfassen Sie Daten zur weiteren Untersuchung in Azure Sentinel.

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure Web Application Firewall (WAF) und der zugehörigen Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen wie Umgebungstyp und Datenvertraulichkeitsstufe, z. B. Entwicklungs-, Test- und Produktionsumgebungen. 

Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) steuern.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die eine Verbindung mit Ihrer Azure Web Application Firewall (WAF)-Instanz und den zugehörigen Ressourcen herstellen, die Aushandlung über TLS 1.2 oder höher durchführen können.

Befolgen Sie ggf. die Empfehlungen von Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) steuern.
- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verwenden Sie die Verschlüsselung ruhender Daten für alle Azure-Ressourcen, einschließlich Azure Web Application Firewall (WAF) und der zugehörigen Ressourcen. Microsoft empfiehlt, Azure die Verwaltung Ihrer Verschlüsselungsschlüssel zu erlauben. Es gibt jedoch in einigen Fällen die Möglichkeit, ihre eigenen Schlüssel zu verwalten.

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurieren der von Kunden verwalteten Verschlüsselungsschlüssel](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Konfigurieren Sie die Azure Web Application Firewall (WAF) so, dass Sie im Präventionsmodus ausgeführt wird, nachdem Sie eine Baseline des Netzwerkdatenverkehrs im Erkennungsmodus für einen vorab bestimmten Zeitraum erstellt haben. 

Die Azure WAF blockiert im Präventionsmodus Eindringlinge und Angriffe, die von den Regeln erkannt werden. Der Angreifer erhält eine Ausnahme vom Typ 403 (nicht autorisierter Zugriff), und die Verbindung wird getrennt. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

- [Übersicht über die Integration von Application Gateway in Azure Security Center](../application-gateway/application-gateway-integration-security-center.md#overview)

- [WAF-Modi für Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF-Modi für Front Door](afds/afds-overview.md#waf-modes)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (etwa Computeressourcen, Speicher, Netzwerke, Ports, Protokolle und Ähnliches) in Ihren Abonnements abzufragen oder zu ermitteln. 

Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie die Ressourcen in ihren Abonnements auf. Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Verwenden Sie Tags mit Azure Web Application Firewall (WAF)-Richtlinien. Tags können Ressourcen zugeordnet und logisch angewendet werden, um den Zugriff auf diese Ressourcen in einem Kundenabonnement zu organisieren. 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements, um die Ressourcen im Zusammenhang mit Azure WAF zu organisieren und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie auf Grundlage der Anforderungen Ihrer Organisation einen Bestand an genehmigten Ressourcen, einschließlich der Konfiguration.

Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können.
Verwenden Sie Azure Resource Graph, um Azure Web Application Firewall (WAF)-Ressourcen in den zugehörigen Abonnements abzufragen oder zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure WAF-Instanzen und die zugehörigen Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Überwachen und entfernen Sie nicht genehmigte Azure WAF-Ressourcen mit Azure Policy, um die Bereitstellung von Azure WAF oder einem bestimmten Typ von WAF (z. B. Azure WAF v1 vs V2) zu verweigern.

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

**Leitfaden**: Azure Web Application Firewall (WAF) kann verschiedenen Umgebungen über Netzwerke, Ressourcengruppen oder Abonnements zugeordnet werden, um Anwendungen mit hohem Risiko zu trennen.

- [Übersicht über Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

- [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer Azure Web Application Firewall (WAF)-Bereitstellungen.
Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Application Gateways, Ihrer virtuellen Netzwerke und Ihrer Netzwerksicherheitsgruppen zu erstellen, und verwenden Sie integrierte Richtliniendefinitionen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [deny] (verweigern) und [deploy if not exist] (bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure Web Application Firewall (WAF)-Instanz und die zugehörigen Ressourcen zu erzwingen. 

Verwenden Sie Azure Resource Manager-Vorlagen, um die Sicherheitskonfiguration der für Ihre Organisation erforderlichen Azure WAF-Ressourcen und der zugehörigen Ressourcen zu verwalten.

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Hierzu zählen beispielsweise benutzerdefinierte Azure-Richtlinien und Azure Resource Manager-Vorlagen. 

Erteilen oder verweigern Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure AD definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit Team Foundation Server, TFS) Berechtigungen.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation zu Azure Policy](../governance/policy/index.yml)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliasse im Namespace „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. 

Verwenden Sie die Azure Policy-Auswirkungen [audit] (überwachen), [deny] (verweigern) und [deploy if not exist] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation zu Azure Policy](../governance/policy/index.yml)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie Azure Key Vault, um Zertifikate sicher zu speichern. Azure Key Vault ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und SSL-Zertifikate schützen können. 

Azure Application Gateway unterstützt die Integration mit Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. 

- [Konfigurieren der TLS-Terminierung mit Key Vault-Zertifikaten mithilfe von Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Implementieren Sie Credential Scanner, um Anmeldeinformationen innerhalb von Code zu identifizieren, wodurch auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) ermutigt wird.
- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Anleitung:** Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist. Vorläufiges Löschen ermöglicht die Wiederherstellung gelöschter Schlüsseltresore und Tresorobjekte, z. B. Schlüssel, Geheimnisse und Zertifikate.

- [Verwenden des vorläufigen Löschens in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Azure Security Center-Überwachung**: Ja

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

**Leitfaden**: Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.
Markieren Sie Abonnements aussagekräftig (z. B. „Produktion“, „Nicht-Produktion“), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.
- [Siehe NIST-Veröffentlichung: „Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen“](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.
- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Verwenden Sie den Azure Security Center-Datenconnector, um die Warnungen gemäß den Anforderungen Ihrer Organisation an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.
- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

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