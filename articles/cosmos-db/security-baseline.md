---
title: Azure-Sicherheitsbaseline für Cosmos DB
description: Azure-Sicherheitsbaseline für Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 961dae4cd35c7529a5661d1ddc7d0d1f6e4db634
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029114"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure-Sicherheitsbaseline für Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Die Azure-Sicherheitsbaseline für Cosmos DB enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Durch Verwendung von Azure Private Link können Sie eine Verbindung mit einem Azure Cosmos-Konto über einen privaten Endpunkt herstellen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können das Risiko der Datenexfiltration auch verringern, indem Sie einen strengen Satz an Ausgangsregeln für eine Netzwerksicherheitsgruppe (NSG) konfigurieren und diese NSG Ihrem Subnetz zuordnen.

Sie können auch Dienstendpunkte verwenden, um Ihr Azure Cosmos-Konto zu sichern. Indem Sie einen Dienstendpunkt aktivieren, können Sie Azure Cosmos-Konten so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz eines virtuellen Azure-Netzwerks zugelassen wird. Sobald der Azure Cosmos DB-Dienstendpunkt aktiviert ist, können Sie den Zugriff auf ein Azure Cosmos-Konto mit Verbindungen von einem Subnetz in ein virtuelles Netzwerk einschränken.

Sie können die in Ihrem Azure Cosmos-Konto gespeicherten Daten auch mithilfe von IP-Firewalls sichern. Azure Cosmos DB unterstützt die IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Sie können eine IP-Firewall im Azure Cosmos-Konto über das Azure-Portal, mit Azure Resource Manager-Vorlagen oder der Azure CLI bzw Azure PowerShell festlegen.

Übersicht über Azure Private Link: https://docs.microsoft.com/azure/private-link/private-link-overview

Konfigurieren eines privaten Endpunkts für Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Erstellen einer Netzwerksicherheitsgruppe mit einer Sicherheitskonfiguration: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Konfigurieren der IP-Firewall in Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Netzwerkressourcen, die sich auf Ihr Azure Cosmos-Konto beziehen, abzusichern.

Wenn virtuelle Computer im gleichen virtuellen Netzwerk wie Ihr Azure Cosmos-Konto bereitgestellt werden, können Sie eine Netzwerksicherheitsgruppe (NSG) verwenden, um das Risiko der Datenexfiltration zu verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Informationen zu der von Azure Security Center bereitgestellten Netzwerksicherheit: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Verwenden Sie das Feature CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen), um einer Webanwendung, die in einer Domäne ausgeführt wird, den Zugriff auf Ressourcen in einer anderen Domäne zu ermöglichen. In Webbrowser ist eine Sicherheitseinschränkung implementiert, die als „Same Origin Policy“ bekannt ist und verhindert, dass eine Webseite APIs in einer anderen Domäne aufruft. CORS bietet jedoch eine sichere Methode, um der Ursprungsdomäne den Aufruf von APIs in einer anderen Domäne zu erlauben. Nachdem Sie die CORS-Unterstützung für Ihr Azure Cosmos-Konto aktiviert haben, werden nur authentifizierte Anforderungen ausgewertet, um festzustellen, ob sie entsprechend den angegebenen Regeln zulässig sind.

Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Cosmos DB. ATP für Azure Cosmos DB bietet eine zusätzliche Ebene von Security Intelligence, die ungewöhnliche und möglicherweise schädliche Versuche, auf Azure Cosmos DB-Konten zuzugreifen oder diese unbefugt zu nutzen, erkennt. Mit dieser Schutzebene können Sie Bedrohungen begegnen und diese in zentrale Sicherheitsüberwachungssysteme integrieren.

Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken für Ihre Azure Cosmos DB-Instanzen, um sich vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren von Advanced Threat Protection für Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Konfigurieren von DDoS-Schutz: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informationen zur integrierten Threat Intelligence in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Aktivieren Sie Netzwerksicherheitsgruppen-Flussprotokolle (NSG), und senden Sie Protokolle an ein Speicherkonto, um den Datenverkehr zu überwachen. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Cosmos DB. ATP für Azure Cosmos DB bietet eine zusätzliche Ebene von Security Intelligence, die ungewöhnliche und möglicherweise schädliche Versuche, auf Azure Cosmos DB-Konten zuzugreifen oder diese unbefugt zu nutzen, erkennt. Mit dieser Schutzebene können Sie Bedrohungen begegnen und diese in zentrale Sicherheitsüberwachungssysteme integrieren. 

Konfigurieren von Advanced Threat Protection für Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht zutreffend; diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihr Azure Cosmos-Konto benötigen, Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (z. B. „AzureCosmosDB“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Weitere Informationen zur Verwendung von Diensttags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Netzwerkressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.DocumentDB“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Cosmos DB-Instanzen zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Cosmos DB verwenden, z. B.:

- Advanced Threat Protection für Cosmos DB-Konten bereitstellen

- Cosmos DB sollte einen VNET-Dienstendpunkt verwenden

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen. Dabei werden wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt. Sie können die Blaupause ganz einfach auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihrer Azure Cosmos DB-Bereitstellung zugeordnet sind, um sie in einer Taxonomie logisch zu organisieren.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Cosmos DB-Instanzen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden. 

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle, die für Azure-Ressourcen wie Azure Cosmos DB verwendet wird, für Zeitstempel in den Protokollen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von Azure Cosmos DB generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Speicherkonten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren. 

Aktivieren von Diagnoseprotokollen für Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure Cosmos DB, und senden Sie die Protokolle an denselben Log Analytics-Arbeitsbereich oder dasselbe Speicherkonto. Diagnoseeinstellungen in Azure Cosmos DB werden zum Sammeln von Ressourcenprotokollen verwendet. Diese Protokolle werden pro Anforderung erfasst und werden auch als „Datenebenenprotokolle“ bezeichnet. Einige Beispiele für Datenebenenvorgänge sind „delete“, „insert“ und „read“. Sie können auch die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll aktivieren und die Protokolle an denselben Log Analytics-Arbeitsbereich senden.

Aktivieren von Diagnoseeinstellungen für Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Aktivieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor die Protokollaufbewahrungsdauer für Log Analytics-Arbeitsbereiche, die Ihrer Azure Cosmos DB-Instanz zugeordnet sind, gemäß den Compliancevorschriften Ihres Unternehmens fest.

Festlegen von Parametern für die Protokollaufbewahrung: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Sie können Abfragen in Log Analytics durchführen. Dies ist ein Arbeitsbereich, in dem Sie Suchbegriffe ermitteln, Trends ausmachen, Muster analysieren und viele weitere Erkenntnisse auf Grundlage der Azure Cosmos DB-Protokolle gewinnen können, die Sie gesammelt haben.

Ausführen von Abfragen für Azure Cosmos DB in Log Analytics-Arbeitsbereichen: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Aktivieren Sie Advanced Threat Protection für Azure Cosmos DB in Azure Security Center, um anomale Aktivitäten in Sicherheitsprotokollen und -ereignissen zu überwachen. Aktivieren Sie die Diagnoseeinstellungen in Azure Cosmos DB, und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich.

 

Integrieren Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel, da er eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitstellt. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden. Darüber hinaus können Sie benutzerdefinierte Protokollwarnungen in Ihrem Log Analytics-Arbeitsbereich mithilfe von Azure Monitor erstellen.

Liste der Bedrohungsschutzwarnungen für Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend; Azure Cosmos DB verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend; Azure Cosmos DB verarbeitet keine Protokolle zu DNS und erzeugt auch keine.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Sie können die Identitäts- und Zugriffssteuerung (Identity and Access control, IAM) im Azure-Portal verwenden, um die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) zu konfigurieren und den Bestand an Azure Cosmos DB-Ressourcen zu verwalten. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden.

Azure Cosmos DB bietet für gängige Verwaltungsszenarien eine integrierte Azure RBAC. Eine Person mit einem Profil in Azure Active Directory (AD) kann diese Azure-Rollen Benutzern, Gruppen, Dienstprinzipalen oder verwalteten Identitäten zuweisen, um den Zugriff auf Ressourcen und Vorgänge für Azure Cosmos DB-Ressourcen zu gewähren oder zu verweigern.

Sie können auch das Azure AD PowerShell-Modul verwenden, um Ad-hoc-Abfragen zum Ermitteln von Konten durchzuführen, die Mitglieder von Verwaltungsgruppen sind. 

Darüber hinaus können einige Aktionen in Azure Cosmos DB mit Azure Active Directory und kontospezifischen Primärschlüsseln gesteuert werden.  Verwenden Sie die Kontoeinstellung „disableKeyBasedMetadataWriteAccess“, um den Schlüsselzugriff zu steuern.

Informationen zur rollenbasierten Zugriffssteuerung in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Erstellen Sie Ihre eigenen benutzerdefinierten Rollen mithilfe von Azure Cosmos DB-Aktionen (Microsoft.DocumentDB-Namespace): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Erstellen einer neuen Rolle in Azure Active Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Abrufen einer Verzeichnisrolle in Azure Active Directory mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure Active Directory mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Beschränken des Benutzerzugriffs ausschließlich auf Datenvorgänge: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Das Konzept von Standardkennwörtern oder leeren Kennwörtern gibt es in Bezug auf Azure AD oder Azure Cosmos DB nicht. Stattdessen verwendet Azure Cosmos DB zwei Arten von Schlüsseln, um Benutzer zu authentifizieren und den Zugriff auf Daten und Ressourcen zu ermöglichen: Primärschlüssel und Ressourcentoken. Die Schlüssel können jederzeit neu generiert werden.

Informationen zum sicheren Zugriff auf Daten in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Erneutes Generieren von Azure Cosmos DB-Schlüsseln: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Programmgesteuerter Zugriff auf Schlüssel mit Azure Active Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Nicht zutreffend; Azure Cosmos DB unterstützt keine Administratorkonten.  Der gesamte Zugriff ist in Azure Active Directory und die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) integriert.



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Azure Cosmos DB verwendet zwei Arten von Schlüsseln zur Benutzerauthentifizierung und unterstützt kein einmaliges Anmelden (Single Sign-On, SSO) auf Datenebene. Der Zugriff auf die Steuerungsebene für Cosmos DB ist über die REST-API verfügbar und unterstützt SSO. Legen Sie zum Authentifizieren den Autorisierungsheader für Ihre Anforderungen auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abrufen.

Informationen zur REST-API für Azure Database for Cosmos DB: https://docs.microsoft.com/rest/api/cosmos-db/

Informationen zu SSO mit Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Cosmos DB. ATP für Azure Cosmos DB bietet eine zusätzliche Ebene von Security Intelligence, die ungewöhnliche und möglicherweise schädliche Versuche, auf Azure Cosmos DB-Konten zuzugreifen oder diese unbefugt zu nutzen, erkennt. Mit dieser Schutzebene können Sie Bedrohungen begegnen und diese in zentrale Sicherheitsüberwachungssysteme integrieren. 

Außerdem können Sie Azure Active Directory (AD) Privileged Identity Management (PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung verwenden.

Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen.

Bereitstellen von Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informationen zu Azure AD-Risikoerkennungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Anleitung:** Konfigurieren Sie die Standortbedingung einer Richtlinie für bedingten Zugriff, und verwalten Sie Ihre benannten Standorte. Mithilfe von benannten Standorten können Sie logische Gruppierungen von IP-Adressbereichen oder Ländern und Regionen erstellen. Sie können den Zugriff auf vertrauliche Ressourcen, z. B. Ihre Azure Cosmos DB-Instanzen, auf die konfigurierten benannten Standorte beschränken.

Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Erstellen und Konfigurieren einer Azure Active Directory-Instanz: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln von veralteten Konten. Außerdem können Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Erstellen Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten, und senden Sie die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich, wo Sie die gewünschten Warnungen konfigurieren können.

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Advanced Threat Protection (ATP) für Azure Cosmos DB. ATP für Azure Cosmos DB bietet eine zusätzliche Ebene von Security Intelligence, die ungewöhnliche und möglicherweise schädliche Versuche, auf Azure Cosmos DB-Konten zuzugreifen oder diese unbefugt zu nutzen, erkennt. Mit dieser Schutzebene können Sie Bedrohungen begegnen und diese in zentrale Sicherheitsüberwachungssysteme integrieren. 

Verwenden Sie Azure AD Identity Protection und die Risikoerkennung, um automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Protokolle zur weiteren Untersuchung in Azure Sentinel erfassen.

Anzeigen riskanter Azure Active Directory-Anmeldungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Kunden-Lockbox ist für Cosmos DB nicht anwendbar.  Microsoft-Mitarbeiter können auch mit Kundenberechtigungen nicht auf Datenressourcen zugreifen.

Liste der durch Kunden-Lockbox unterstützten Dienste: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure Cosmos DB-Instanzen, die vertrauliche Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure Cosmos DB-Instanzen sind durch ein virtuelles Netzwerk/Subnetz getrennt, entsprechend gekennzeichnet und durch eine Netzwerksicherheitsgruppe (NSG) oder Azure Firewall gesichert. Azure Cosmos DB-Instanzen, die vertrauliche Daten speichern, müssen isoliert werden. Durch Verwendung von Azure Private Link können Sie eine Verbindung mit einem Azure Cosmos DB-Instanzkonto über einen privaten Endpunkt herstellen. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in einem Subnetz innerhalb Ihres virtuellen Netzwerks. Sie können dann den Zugriff auf die ausgewählten privaten IP-Adressen einschränken. 

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Konfigurieren eines privaten Endpunkts für Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Erstellen einer Netzwerksicherheitsgruppe mit einer Sicherheitskonfiguration: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Sie können Advanced Threat Protection für Azure Cosmos DB konfigurieren. Dies erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen. Es kann die folgenden Warnungen auslösen:

- Zugriff von einem ungewöhnlichen Ort

- Ungewöhnliche Datenextraktion

Wenn Sie virtuelle Computer verwenden, um auf Ihre Azure Cosmos DB-Instanzen zuzugreifen, verwenden Sie Private Link, eine Firewall, Netzwerksicherheitsgruppen und Diensttags, um die Möglichkeit von Datenexfiltration zu verringern. Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Cosmos DB und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Konfigurieren von Advanced Threat Protection für Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Informationen zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Alle Verbindungen mit Azure Cosmos DB unterstützen HTTPS. Azure Cosmos DB unterstützt auch TLS1.2. Es ist möglich, eine serverseitige TLS-Mindestversion zu erzwingen. Wenden Sie sich zu diesem Zweck an [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).

Übersicht über Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für die automatische Datenidentifizierung und -klassifizierung und zum Schutz vor Datenverlust sind für Azure Cosmos DB noch nicht verfügbar. Sie können jedoch die Azure Cognitive Search-Integration für die Klassifizierung und die Datenanalyse verwenden. Oder implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Indizieren von Azure Cosmos DB-Daten mit Azure Cognitive Search: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json

Informationen zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Azure Cosmos DB bietet für gängige Verwaltungsszenarien eine integrierte rollenbasierte Zugriffssteuerung in Azure (Azure RBAC). Eine Person mit einem Profil in Azure Active Directory kann diese Azure-Rollen Benutzern, Gruppen, Dienstprinzipalen oder verwalteten Identitäten zuweisen, um den Zugriff auf Ressourcen und Vorgänge für Azure Cosmos DB-Ressourcen zu gewähren oder zu verweigern. Rollenzuweisungen sind auf den Zugriff auf Steuerungsebene beschränkt, was den Zugriff auf Konten, Datenbanken, Container und Angebote (Durchsatz) für Azure Cosmos umfasst.

Implementieren von Azure RBAC in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Cosmos DB und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Informationen zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Alle in Cosmos DB gespeicherten Benutzerdaten werden standardmäßig im Ruhezustand verschlüsselt. Es gibt keine Möglichkeit, dies auszuschalten. Azure Cosmos DB verwendet AES-256-Verschlüsselung in allen Regionen, in denen das Konto ausgeführt wird.

Standardmäßig verwaltet Microsoft die Schlüssel, die zum Verschlüsseln der Daten in Ihrem Azure Cosmos-Konto verwendet werden. Sie können optional eine zweite Verschlüsselungsschicht mit Ihren eigenen Schlüsseln hinzufügen.

Informationen zur Verschlüsselung ruhender Daten mit Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Informationen zur Schlüsselverwaltung für die Verschlüsselung ruhender Daten mit Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an Produktionsinstanzen von Azure Cosmos DB ausgegeben werden.

Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Empfehlungen von Azure Security Center für Ihre Azure Cosmos DB-Instanzen. 

Microsoft führt auf den zugrunde liegenden Hosts, die Ihre Azure Cosmos DB-Instanzen unterstützen, Systempatching durch und verwaltet Sicherheitsrisiken. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Verfügbare unterstützte Features in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie das Azure-Portal oder Azure Resource Graph, um alle Ressourcen (nicht beschränkt auf Azure Cosmos DB, sondern beispielsweise auch Computeressourcen, andere Speicher, Netzwerke, Ports, Protokolle usw.) in Ihren Abonnements zu ermitteln.  Stellen Sie sicher, dass Sie über entsprechende Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

Erstellen von Abfragen mit Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Anzeigen Ihrer Azure-Abonnements: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Informationen zur rollenbasierten Zugriffssteuerung in Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure Cosmos DB-Instanzen und verwandte Ressourcen mit Metadaten an, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Welche Azure Cosmos DB-Ressourcen Tags unterstützen: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen wie u. a. Azure Cosmos DB-Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend; diese Baseline ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen und Azure als Ganzes vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen) 

- Zulässige Ressourcentypen

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Anleitung:** Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dies kann das Erstellen und Ändern von Ressourcen innerhalb einer Hochsicherheitsumgebung verhindern.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Cosmos DB-Instanzen mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DocumentDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Cosmos DB-Instanz zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Cosmos DB verwenden, z. B.:

- Advanced Threat Protection für Cosmos DB-Konten bereitstellen

- Cosmos DB sollte einen VNET-Dienstendpunkt verwenden

Anzeigen verfügbarer Azure Policy-Aliase: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie für Ihre Cosmos DB-Ressourcen oder für verwandte Ressourcen benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

Dokumentation zu Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DocumentDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DocumentDB“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (Überwachen), [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure Cosmos DB-Instanzen und die zugehörigen Ressourcen automatisch zu erzwingen. 

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cosmos DB-Instanzen verwendet werden, nutzen Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Cosmos DB-Geheimnissen zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Integration mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cosmos DB-Instanzen verwendet werden, nutzen Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Azure Cosmos DB-Geheimnissen zu vereinfachen und zu schützen.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Konfigurieren von verwalteten Identitäten: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integration mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Einrichten von Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Es liegt in ihrer Verantwortung, alle Dateien, die in computefremde Azure-Ressourcen hochgeladen werden, vorab zu scannen. Dies gilt auch für Azure Cosmos DB. Microsoft kann nicht auf Kundendaten zugreifen und kann daher keine Antischadsoftwarescans von Kundeninhalten in Ihrem Namen durchführen.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend; Benchmark ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste aktiviert, wird aber nicht für Kundeninhalte ausgeführt.


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Azure Cosmos DB erstellt alle vier Stunden Momentaufnahmen Ihrer Daten. Alle Sicherungskopien werden in einem Speicherdienst getrennt gespeichert, und diese Sicherungen werden zum besseren Schutz vor regionalen Ausfällen global repliziert. Es werden jeweils nur die letzten zwei Momentaufnahmen aufbewahrt. Aber wenn der Container bzw. die Datenbank gelöscht wird, bewahrt Azure Cosmos DB die vorhandenen Momentaufnahmen des angegebenen Containers bzw. der Datenbank 30 Tage lang auf. Wenden Sie sich an den Azure-Support, um eine Wiederherstellung von einer Sicherung durchzuführen.

Informationen zu automatisierten Sicherungen in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Falls Sie Ihre Datenbank oder Ihr Container gelöscht wird, können Sie ein Supportticket erstellen oder sich an den Azure-Support wenden, um die Daten aus automatischen Onlinesicherungen wiederherstellen zu lassen. Der Azure-Support steht nur in ausgewählten Tarifen wie Standard und Developer sowie in höheren Tarifen zur Verfügung. Für die Wiederherstellung einer bestimmten Momentaufnahme der Sicherung setzt Azure Cosmos DB voraus, dass die Daten für die Dauer des Sicherungszyklus dieser Momentaufnahme verfügbar sind. 

Wenn Sie Key Vault zum Speichern von Anmeldeinformationen für Ihre Cosmos DB-Instanzen verwenden, führen Sie regelmäßige automatische Sicherungen Ihrer Schlüssel durch.

Informationen zu automatisierten Sicherungen in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Wiederherstellen von Daten in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Sichern von Key Vault-Schlüsseln: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Falls Sie Ihre Datenbank oder Ihr Container gelöscht wird, können Sie ein Supportticket erstellen oder sich an den Azure-Support wenden, um die Daten aus automatischen Onlinesicherungen wiederherstellen zu lassen. Der Azure-Support steht nur in ausgewählten Tarifen wie Standard und Developer sowie in höheren Tarifen zur Verfügung. Für die Wiederherstellung einer bestimmten Momentaufnahme der Sicherung setzt Azure Cosmos DB voraus, dass die Daten für die Dauer des Sicherungszyklus dieser Momentaufnahme verfügbar sind.

Testen Sie die Wiederherstellung Ihrer Geheimnisse, die mithilfe von PowerShell in Azure Key Vault gespeichert wurden. Mit dem Cmdlet „Restore-AzureKeyVaultKey“ wird ein Schlüssel im angegebenen Schlüsseltresor erstellt. Bei diesem Schlüssel handelt es sich um ein Replikat des gesicherten Schlüssels in der Eingabedatei, der denselben Namen hat wie der ursprüngliche Schlüssel.

Informationen zu automatisierten Sicherungen in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Wiederherstellen von Daten in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Wiederherstellen von Azure Key Vault-Geheimnissen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Da alle in Cosmos DB gespeicherten Benutzerdaten sowohl ruhend als auch während der Übertragung verschlüsselt werden, müssen Sie keine Maßnahmen ergreifen. Anders ausgedrückt: Die Verschlüsselung ruhender Daten ist standardmäßig „aktiviert“. Es gibt keine Möglichkeit, dies ein- oder auszuschalten. Azure Cosmos DB verwendet AES-256-Verschlüsselung in allen Regionen, in denen das Konto ausgeführt wird.

Aktivieren Sie das vorläufige Löschen in Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

Informationen zur Datenverschlüsselung in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Aktivieren des vorläufigen Löschens in Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

Sie können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Vorfälle zu entwickeln: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Konfigurieren von Workflowautomatisierungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Struktur eines Vorfalls laut Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

Informationen finden Sie in der folgenden Veröffentlichung des NIST: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

Festlegen der Kontaktinformationen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

Konfigurieren des fortlaufenden Exports: https://docs.microsoft.com/azure/security-center/continuous-export

Streamen von Warnungen in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

Konfigurieren von Workflowautomatisierung und Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).
