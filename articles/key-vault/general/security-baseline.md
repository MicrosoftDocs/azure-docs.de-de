---
title: Azure-Sicherheitsbaseline für Key Vault
description: Azure-Sicherheitsbaseline für Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7d116010d5e6fea5e1ba8a66431b874194506283
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843450"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure-Sicherheitsbaseline für Key Vault

Die Azure-Sicherheitsbaseline für Key Vault enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Anleitung:** Integrieren Sie Azure Key Vault in Azure Private Link. 

Mit dem Azure Private Link-Dienst können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste (z. B. Azure Key Vault) sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Integrieren von Key Vault in Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Anleitung:** Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre mit Key Vault konfigurierten Ressourcen in Azure zu schützen. 

Weitere Informationen zu der von Azure Security Center bereitgestellten Netzwerksicherheit: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Anleitung:** Aktivieren Sie Azure DDoS Protection Standard in den virtuellen Azure-Netzwerken, die Ihren Key Vault-Instanzen zugeordnet sind, um diese vor verteilten Denial-of-Service-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

 
Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Bedrohungserkennung für die Azure-Dienstebene in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Anleitung:** Azure Key Vault verwendet keine Netzwerksicherheitsgruppen (NSG), und die Datenflussprotokolle für Azure Key Vault werden nicht erfasst. Verwenden Sie stattdessen Azure Private Link, um Ihre Azure Key Vault-Instanzen zu schützen, und aktivieren Sie Diagnoseeinstellungen, um Metriken und Überwachungsereignisse aufzuzeichnen.

Integrieren von Key Vault in Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault-Protokollierung: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Anleitung:** Diese Anforderung kann durch Konfigurieren von Advanced Threat Protection (ATP) für Azure Key Vault erfüllt werden. ATP bietet zusätzliche Sicherheitsanalysen für Daten. Dieses Tool erkennt potenziell schädliche Versuche, auf Azure Key Vault-Konten zuzugreifen oder diese missbräuchlich zu nutzen.

Wenn Azure Security Center anomale Aktivitäten erkennt, werden Warnungen angezeigt. Außerdem wird der Administrator des Abonnements per E-Mail über die Details der verdächtigen Aktivität informiert und erhält Empfehlungen, wie er die identifizierten Bedrohungen untersuchen und beheben kann.

Einrichten von Advanced Threat Protection für Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Anleitung:** Verwenden Sie für Ressourcen, die Zugriff auf Ihre Azure Key Vault-Instanzen benötigen, Azure-Diensttags für Azure Key Vault, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Übersicht über Azure-Diensttags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Anleitung:** Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Key Vault-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.KeyVault“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Key Vault-Instanzen zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Key Vault verwenden, z. B.:

Key Vault sollte einen VNET-Dienstendpunkt verwenden.

Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-Beispiele:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Schnellstart: Definieren und Zuweisen einer Blaupause im Portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Anleitung:** Verwenden Sie Tags für Ressourcen im Zusammenhang mit der Netzwerksicherheit und dem Datenfluss für Ihre Azure Key Vault-Instanzen, um Metadaten und eine logische Organisation bereitzustellen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen anhand ihrer Tags zu suchen oder Aktionen auszuführen.

Verwenden von Tags zum Organisieren von Azure-Ressourcen:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Anleitung:** Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Key Vault-Instanzen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Anleitung:** Nicht zutreffend. Microsoft verwaltet die Zeitquelle von Azure-Ressourcen wie Azure Key Vault für Zeitstempel in den Protokollen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Anleitung:** Erfassen Sie Protokolle über Azure Monitor, um die von Azure Key Vault generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Azure Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

Azure Key Vault-Protokollierung:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Schnellstart: Ausführen des Onboardings für Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Anleitung:** Aktivieren Sie Diagnoseeinstellungen für Ihre Azure Key Vault-Instanzen für den Zugriff auf Überwachungs-, Sicherheits- und Diagnoseprotokolle. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente.

Azure Key Vault-Protokollierung:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Anleitung:** Legen Sie in Azure Monitor den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs, der für die Azure Key Vault-Protokolle verwendet wird, gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

Ändern des Datenaufbewahrungszeitraums: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Anleitung:** Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und prüfen Sie die Ergebnisse für Ihre durch Azure Key Vault geschützten Ressourcen regelmäßig. Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren. 

Schnellstart: Ausführen des Onboardings für Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Erste Schritte mit Log Analytics in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Erste Schritte mit Protokollabfragen in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Anleitung:** Aktivieren Sie Advanced Threat Protection (ATP) für Key Vault in Azure Security Center. Aktivieren Sie die Diagnoseeinstellungen in Azure Key Vault, und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich. Integrieren Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel, da er eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitstellt. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden.

Schnellstart: Ausführen des Onboardings für Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Reagieren auf Ereignisse mit Azure Monitor-Warnungen:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Anleitung:** Nicht zutreffend. Azure Key Vault verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Anleitung:** Nicht zutreffend. Azure Key Vault verarbeitet keine Protokolle zu DNS und erzeugt auch keine.


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Anleitung:** Verwalten Sie den Bestand Ihrer in Azure Active Directory registrierten Anwendungen sowie alle Benutzerkonten, die Zugriff auf Ihre Azure Key Vault-Schlüssel, -Geheimnisse und -Zertifikate haben. Sie können das Azure-Portal oder PowerShell verwenden, um den Key Vault Zugriff abzufragen und abzustimmen. Verwenden Sie den folgenden Befehl, um den Zugriff in PowerShell anzuzeigen:

(Get-AzResource -ResourceId [KeyVaultResourceID]).Properties.AccessPolicies

Registrieren einer Anwendung mit Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Schützen des Zugriffs auf einen Schlüsseltresor:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Nicht zutreffend. Azure Key Vault verfügt nicht über das Konzept von Standardkennwörtern, da die Authentifizierung über Active Directory bereitgestellt und mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) geschützt wird.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Anleitung:** Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten, die Zugriff auf Ihre Azure Key Vault-Instanzen haben. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center (derzeit in der Vorschauphase), um die Anzahl der aktiven Administratorkonten zu überwachen.

Überwachen der Identität und des Zugriffs (Vorschauversion):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Anleitung:** Verwenden Sie einen Azure-Dienstprinzipal in Verbindung mit AppId, TenantID und ClientSecret, um Ihre Anwendung nahtlos zu authentifizieren und das Token abzurufen, das für den Zugriff auf Ihre Azure Key Vault-Geheimnisse verwendet wird.

Dienst-zu-Dienst-Authentifizierung in Azure Key Vault mithilfe von .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Anleitung:** Aktivieren Sie Azure Active Directory Multi-Factor Authentication, und befolgen Sie die Empfehlungen zur Identitäts- und Zugriffsverwaltung von Azure Security Center (derzeit in der Vorschauphase), um Ihre Event Hub-fähigen Ressourcen zu schützen.

Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen der Identität und des Zugriffs (Vorschauversion):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit Azure AD Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration Key Vault-fähiger Ressourcen konfiguriert ist. 

Privileged Access Workstations: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (Azure AD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen. Wenn Sie eine zusätzliche Protokollierung benötigen, senden Sie die Risikoerkennungswarnungen von Azure Security Center an Azure Monitor, und konfigurieren Sie mithilfe von Aktionsgruppen benutzerdefinierte Warnungen/Benachrichtigungen.

Aktivieren Sie Advanced Threat Protection (ATP) für Azure Key Vault, um Warnungen für verdächtige Aktivitäten zu generieren.

Bereitstellen von Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Einrichten von Advanced Threat Protection für Azure Key Vault (Vorschau): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Warnungen für Azure Key Vault (Vorschau): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory-Risikoerkennungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Erstellen und Verwalten von Aktionsgruppen im Azure-Portal: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Anleitung:** Konfigurieren Sie die Standortbedingung einer Richtlinie für bedingten Zugriff, und verwalten Sie Ihre benannten Standorte. Mithilfe von benannten Standorten können Sie logische Gruppierungen von IP-Adressbereichen oder Ländern und Regionen erstellen. Sie können den Zugriff auf vertrauliche Ressourcen, z. B. Ihre Key Vault-Geheimnisse, auf die konfigurierten benannten Standorte beschränken.

Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory? https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Azure-Ressourcen wie Key Vault. Dies ermöglicht die Verwendung der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) zum Verwalten vertraulicher Ressourcen.

 

Schnellstart: Erstellen eines neuen Mandanten in Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie die Azure Active Directory-Protokolle (Azure AD), um veraltete Konten mit administrativen Azure Key Vault-Rollen zu ermitteln. Verwenden Sie zusätzlich Azure AD-Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen, die für den Zugriff auf Azure Key Vault verwendet werden könnten, und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff sollte regelmäßig überprüft werden (z. B. alle 90 Tage), um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

Dokumentation zu Azure Active Directory-Berichten und -Überwachung:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Was sind Azure AD-Zugriffsüberprüfungen?

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Anleitung:** Aktivieren Sie die Diagnoseeinstellungen für Azure Key Vault und Azure Active Directory, und senden Sie alle Protokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie die gewünschten Warnungen (z. B. Versuche, auf deaktivierte Geheimnisse zuzugreifen) in Log Analytics.

Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrieren aus der alten Key Vault-Lösung: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Anleitung:** Verwenden Sie die Features Azure Active Directory Identity Protection und Risikoerkennung, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Ihren durch Azure Key Vault geschützten Ressourcen zu konfigurieren. Sie sollten automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren. 

Bericht „Risikoanmeldungen“ im Azure Active Directory-Portal: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Anleitung: Konfigurieren und Aktivieren von Risikorichtlinien: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Anleitung:** Nicht zutreffend. Kunden-Lockbox wird für Azure Key Vault nicht unterstützt.

Unterstützte Dienste und Szenarien bei allgemeiner Verfügbarkeit: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Anleitung:** Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen zu Ressourcen, für die Azure Key Vault aktiviert wurde, speichern oder verarbeiten. 

Verwenden von Tags zum Organisieren von Azure-Ressourcen: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Anleitung:** Sie können den Zugriff auf Azure Key Vault schützen, indem Sie VNET-Dienstendpunkte verwenden, die zum Einschränken des Zugriffs auf bestimmte Subnetze konfiguriert sind.

Nachdem Firewallregeln in Kraft sind, können Sie Vorgänge auf der Datenebene von Azure Key Vault nur ausführen, wenn Ihre Anforderungen aus zulässigen Subnetzen oder IP-Adressbereichen stammen. Dies gilt auch für den Zugriff auf Azure Key Vault aus dem Azure-Portal. Obwohl Sie im Azure-Portal zu einem Schlüsseltresor navigieren können, können Sie möglicherweise keine Schlüssel, Geheimnisse oder Zertifikate auflisten, wenn Ihr Clientcomputer nicht in der Zulassungsliste enthalten ist. Dies wirkt sich auch auf die Azure Key Vault-Auswahl und andere Azure-Dienste aus. Sie können möglicherweise eine Liste von Schlüsseltresoren anzeigen, aber keine Schlüssel auflisten, wenn Firewallregeln Ihren Clientcomputer blockieren.

Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

VNET-Dienstendpunkte für Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Anleitung:** Alle in Azure Key Vault gespeicherte Daten werden als vertraulich eingestuft. Verwenden Sie Zugriffssteuerungen für die Azure Key Vault-Datenebene, um den Zugriff auf Azure Key Vault-Geheimnisse zu steuern. Sie können auch die integrierte Firewall von Key Vault verwenden, um den Zugriff auf Netzwerkebene zu steuern. Aktivieren Sie zum Überwachen des Zugriffs auf Azure Key Vault die Key Vault-Diagnoseeinstellungen, und senden Sie die Protokolle an ein Azure Storage-Konto oder einen Log Analytics-Arbeitsbereich.

Schützen des Zugriffs auf einen Schlüsseltresor: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault-Protokollierung: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Anleitung:** Der gesamte Datenverkehr zu Azure Key Vault für Authentifizierung, Verwaltung und Datenebenenzugriff wird verschlüsselt und über den HTTPS-Port 443 gesendet. (Gelegentlich fällt aber auch HTTP-Datenverkehr (Port 80) für CRL an.) Azure Key Vault lässt weiterhin die Erfassung von TLS 1.1-und TLS 1.0-Daten zu. Daten können per Konfiguration auf Clientseite auf TLS 1.2 beschränkt werden.



Zugreifen auf Azure Key Vault hinter einer Firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Anleitung:** Nicht zutreffend. Alle Daten in Azure Key Vault (Geheimnisse, Schlüssel und Zertifikate) werden als vertraulich eingestuft.


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Schützen Sie den Zugriff auf die Verwaltungs- und Datenebene Ihrer Azure Key Vault-Instanzen.

Schützen des Zugriffs auf einen Schlüsseltresor:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Anleitung:** Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Key Vault und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Was ist Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Schutz der Azure-Kundendaten:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Anleitung:** Alle verwalteten Objekte (Schlüssel, Zertifikate und Geheimnisse) werden im Ruhezustand in Azure Key Vault verschlüsselt.

Unterstützende Dokumentation:

- [Tabelle für Verschlüsselungsmodell und Schlüsselverwaltung](../../security/fundamentals/encryption-atrest.md)


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Anleitung:** Sie können die Azure Key Vault-Analyse-Lösung in Azure Monitor verwenden, um Ereignisprotokolle der Azure Key Vault-Überwachung zu überprüfen.

Azure Key Vault-Analyse-Lösung in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Anleitung:** Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Key Vault unterstützen.


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Anleitung:** Nicht zutreffend. Microsoft führt die Patchverwaltung auf den zugrunde liegenden Systemen aus, die Key Vault unterstützen.

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Anleitung:** Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Key Vault unterstützen.

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die Standardrisikobewertungen (Secure Score), die von Azure Security Center bereitgestellt werden.

Verbessern des Secure Score in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Anleitung:** Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Azure Key Vault-Instanzen) innerhalb Ihres Abonnements abzufragen und zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und durchlaufen Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements.

Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Testers:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Abrufen von Abonnements, auf die das aktuelle Konto Zugriff hat:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Anleitung:** Wenden Sie Tags auf Ihre Azure Key Vault-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Anleitung:** Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Key Vault-Instanzen und zugehörigen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen eines zusätzlichen Azure-Abonnements:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen zum Organisieren und Verwalten von Ressourcen:

https://docs.microsoft.com/azure/governance/management-groups/create

Verwenden von Tags zum Organisieren von Azure-Ressourcen: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Definieren Sie eine Liste mit den genehmigten Azure-Ressourcen und der genehmigten Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Anleitung:** Verwenden Sie Azure-Richtlinien, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Testers: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist Azure allgemein und für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Anleitung:** Verwenden Sie Azure-Richtlinien, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-Beispiele: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager (ARM) einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dies kann das Erstellen und Ändern von Ressourcen innerhalb einer Hochsicherheitsumgebung (z. B. mit Key Vault-Konfiguration) verhindern.

Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Anleitung:** Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.KeyVault“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Key Vault-Instanzen zu erstellen. Sie können auch integrierte Azure Policy-Definitionen für Azure Key Vault verwenden, z. B.:

Key Vault-Objekte müssen wiederherstellbar sein

Bereitstellen von Diagnoseeinstellungen für Key Vault im Log Analytics-Arbeitsbereich

Diagnoseprotokolle in Key Vault sollten aktiviert sein.

Key Vault sollte einen VNET-Dienstendpunkt verwenden.

Bereitstellen von Diagnoseeinstellungen für Key Vault in Event Hub

Nutzen Sie die Empfehlungen von Azure Security Center als Konfigurationsbaseline für den Schutz Ihrer Azure Key Vault-Instanzen.

Anzeigen verfügbarer Azure Policy-Aliase:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Ressourcen mit aktiviertem Azure Key Vault zu erzwingen. 

Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Grundlegendes zu Azure Policy-Auswirkungen: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie für Ihre Ressourcen mit aktiviertem Azure Key Vault benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

Speichern von Code in Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Dokumentation zu Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Anleitung:** Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.KeyVault“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen zu diesen zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Anleitung:** Verwenden Sie Azure Security Center, um Baselineüberprüfungen der mit Azure Key Vault geschützten Ressourcen durchzuführen. 

  

Umsetzen von Empfehlungen in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Anleitung:** Nicht zutreffend. Diese Benchmark ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Integration mit verwalteten Azure-Identitäten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen. 

  

* [Integrieren mit verwalteten Azure-Identitäten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Erstellen einer Key Vault-Instanz](quick-create-portal.md)

* [Authentifizieren bei Key Vault](authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.  
  
 Einrichten von Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Anleitung:** Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Key Vault) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

Inhalte, die in nicht für Compute ausgelegte Azure-Ressourcen wie Azure Key Vault hochgeladen oder an diese gesendet werden, sollten im Voraus überprüft werden. Microsoft kann in diesen Fällen nicht auf Ihre Daten zugreifen.

Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen. Microsoft kümmert sich um Antischadsoftware für die zugrunde liegende Plattform.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Anleitung:** Stellen Sie mithilfe der folgenden PowerShell-Befehle sicher, dass regelmäßig automatisierte Sicherungen Ihrer Key Vault-Zertifikate, -Schlüssel und -Geheimnisse und der verwalteten Speicherkonten erstellt werden:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Optional können Sie Ihre Key Vault-Sicherungen in Azure Backup speichern.

Sichern von Key Vault-Zertifikaten: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Sichern von Key Vault-Schlüsseln: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Sichern per Key Vault verwalteter Speicherkonten: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Sichern von Key Vault-Geheimnissen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Aktivieren von Azure Backup: https://docs.microsoft.com/azure/backup



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Anleitung:** Erstellen Sie mithilfe der folgenden PowerShell-Befehle Sicherungen Ihrer Key Vault-Zertifikate, -Schlüssel und -Geheimnisse und der verwalteten Speicherkonten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Optional können Sie Ihre Key Vault-Sicherungen in Azure Backup speichern.

Sichern von Key Vault-Zertifikaten: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Sichern von Key Vault-Schlüsseln: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Sichern per Key Vault verwalteter Speicherkonten: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Sichern von Key Vault-Geheimnissen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Aktivieren von Azure Backup: https://docs.microsoft.com/azure/backup



**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Anleitung:** Führen Sie mithilfe der folgenden PowerShell-Befehle regelmäßig Datenwiederherstellungen Ihrer Key Vault-Zertifikate, -Schlüssel und -Geheimnisse und der verwalteten Speicherkonten durch:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Wiederherstellen von Key Vault-Zertifikaten: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Wiederherstellen von Key Vault-Schlüsseln: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Wiederherstellen per Key Vault verwalteter Speicherkonten: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Wiederherstellen von Key Vault-Geheimnissen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist. Vorläufiges Löschen ermöglicht die Wiederherstellung gelöschter Schlüsseltresore und Tresorobjekte wie Schlüssel, Geheimnisse und Zertifikate. 

Verwenden des vorläufigen Löschens für Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. Ziel dieser Prozesse sollte der Schutz sensibler Systeme sein, z. B. solcher, die Key Vault-Geheimnisse nutzen.

Konfigurieren von Workflowautomatisierungen in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Struktur eines Vorfalls laut Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle zu entwickeln: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand. Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren. Dies gilt insbesondere für solche, die vertrauliche Daten wie Azure Key Vault-Geheimnisse verarbeiten.


**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Anleitung:** Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure Key Vault-Instanzen und die zugehörigen Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

Informationen finden Sie in der folgenden Veröffentlichung des NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

Festlegen der Kontaktinformationen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Anleitung:** Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken bei Ressourcen zu identifizieren, die Azure Key Vault nutzen. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren.  Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. 

 

Konfigurieren des fortlaufenden Exports: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Streamen von Warnungen in Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Anleitung:** Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Ressourcen mit Azure Key Vault noch besser zu schützen. 

 

Konfigurieren von „Workflowautomatisierung“ und Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Anleitung:** Sie sollten keine direkten Penetrationstests für den Azure Key Vault-Dienst ausführen, es wird jedoch empfohlen, Ihre Azure-Ressourcen zu testen, die Key Vault verwenden, um die Sicherheit der Geheimnisse sicherzustellen.

Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Weitere Informationen zur Microsoft-Strategie und Ausführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-Überwachung:** Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../../security/benchmarks/security-baselines-overview.md).