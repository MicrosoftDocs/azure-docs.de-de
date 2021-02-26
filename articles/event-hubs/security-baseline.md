---
title: Azure-Sicherheitsbaseline für Event Hubs
description: Azure-Sicherheitsbaseline für Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5c4aed7a5ee1726d7afb1a671fea1519acfa67c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806086"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-Sicherheitsbaseline für Event Hubs

Die Azure-Sicherheitsbaseline für Event Hubs enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Die Integration von Event Hubs und VNET-Dienstendpunkten ermöglicht den sicheren Zugriff auf Messagingfunktionen für Workloads, z. B. an virtuelle Netzwerke gebundene virtuelle Computer, wobei der Pfad für den Netzwerkdatenverkehr an beiden Enden geschützt ist.

Nachdem die Bindung an mindestens einen Dienstendpunkt des VNET-Subnetzes durchgeführt wurde, akzeptiert der entsprechende Event Hubs-Namespace nur noch Datenverkehr von autorisierten Subnetzen in virtuellen Netzwerken. Aus Sicht des virtuellen Netzwerks wird durch die Bindung eines Event Hubs-Namespace an einen Dienstendpunkt ein isolierter Netzwerktunnel vom Subnetz des virtuellen Netzwerks zum Messagingdienst konfiguriert. 

Sie können auch einen privaten Endpunkt erstellen, bei dem es sich um eine Netzwerkschnittstelle handelt, die Sie mithilfe des Azure-Diensts Private Link privat und sicher mit dem Azure Event Hubs-Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. 

Sie können Ihren Azure Event Hubs-Namespace auch mithilfe von Firewalls sichern. Azure Event Hubs unterstützt IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Sie können Firewallregeln über das Azure-Portal, mit Azure Resource Manager-Vorlagen oder der Azure CLI bzw Azure PowerShell festlegen.

Verwenden von Virtual Network-Dienstendpunkten mit Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Weitere Informationen finden Sie unter Integrieren von Azure Event Hubs mit Azure Private Link: https://docs.microsoft.com/azure/event-hubs/private-link-service.

Aktivieren der Integration virtueller Netzwerke und von Firewalls für den Event Hubs-Namespace: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Konfigurieren von IP-Firewallregeln für Azure Event Hubs-Namespaces: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Event Hubs-Ressourcen in Azure abzusichern. Wenn Sie virtuelle Azure-Computer verwenden, um auf Ihre Event Hubs zuzugreifen, aktivieren Sie die Datenflussprotokolle der Netzwerksicherheitsgruppen (NSGs), und senden Sie Protokolle an ein Speicherkonto für die Überwachung des Datenverkehrs.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Grundlegendes zur Netzwerksicherheit, die von Azure Security Center bereitgestellt wird: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie in den virtuellen Netzwerken, die Ihren Event Hubs zugeordnet sind, DDoS Protection Standard, um sich vor DDoS-Angriffen (Distributed Denial-of-Service) zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren von DDoS-Schutz: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

Weitere Informationen zur integrierten Threat Intelligence in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Wenn Sie virtuelle Azure-Computer verwenden, um auf Ihre Event Hubs zuzugreifen, aktivieren Sie die Datenflussprotokolle der Netzwerksicherheitsgruppen (NSGs), und senden Sie Protokolle an ein Speicherkonto für die Überwachung des Datenverkehrs. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren Sie Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

Aktivieren der NSG-Flussprotokolle: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Aktivieren von Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Wenn Sie virtuelle Azure-Computer für den Zugriff auf Event Hubs verwenden, wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt. Wenn für Ihre Organisation keine Angriffserkennung und/oder -verhinderung auf Grundlage von Nutzlastüberprüfung erforderlich ist, können Sie die integrierte Firewallfunktion von Azure Event Hubs verwenden. Sie können den Zugriff auf den Event Hubs-Namespace für einen begrenzten Bereich von IP-Adressen oder eine bestimmte IP-Adresse beschränken, indem Sie Firewallregeln verwenden.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Hinzufügen einer Firewallregel in Event Hubs für eine angegebene IP-Adresse:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center-Überwachung**: Noch nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Event Hubs-Namespaces zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.EventHub“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Event Hubs-Namespaces zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Event Hubs verwenden, z. B.:

- Event Hub sollte einen VNET-Dienstendpunkt verwenden.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Integrierte Azure-Richtlinie für Event Hubs-Namespace: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy-Beispiele für Netzwerke: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Erstellen einer Azure-Blaupause: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für virtuelle Netzwerke und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss, die mit ihren Event Hubs verknüpft sind.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Event Hubs beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Nicht zutreffend. Microsoft verwaltet die Zeitquelle von Azure-Ressourcen wie Azure Event Hubs für Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Konfigurieren Sie in Azure Monitor Protokolle im Zusammenhang mit Event Hubs innerhalb des Aktivitätsprotokolls und der Event Hub-Diagnoseeinstellungen, um Protokolle für Abfragen an einen Log Analytics-Arbeitsbereich oder für die langfristige Archivierung an ein Speicherkonto zu senden.

Konfigurieren von Diagnoseeinstellungen für Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Grundlegendes zum Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen für Ihren Azure Event Hubs-Namespace. Es gibt drei Kategorien von Diagnoseeinstellungen für Azure Event Hubs: Archivprotokolle, Betriebsprotokolle und AutoScale-Protokolle. Aktivieren Sie Betriebsprotokolle, um Informationen zu Abläufen während Event Hubs-Vorgängen zu erfassen, insbesondere zum Vorgangstyp (einschließlich Event Hub-Erstellung, verwendeter Ressourcen und Status des Vorgangs).

Außerdem können Sie Diagnoseeinstellungen des Azure-Aktivitätsprotokolls aktivieren und sie an ein Azure Storage-Konto, Event Hub oder einen Log Analytics-Arbeitsbereich senden. Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre Azure Event Hubs und andere Ressourcen ausgeführt wurden. Mit Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die für Azure Event Hubs-Namespaces durchgeführt wurden.

Aktivieren von Diagnoseeinstellungen für das Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Aktivieren von Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest, um Event Hub-bezogene Vorfälle zu erfassen und zu überprüfen.

Festlegen der Parameter für die Protokollaufbewahrung für Log Analytics-Arbeitsbereiche: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig, die sich auf Ihre Event Hubs beziehen. Verwenden Sie Log Analytics von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.
 

Weitere Informationen zum Log Analytics-Arbeitsbereich: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Ausführen benutzerdefinierter Abfragen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Konfigurieren Sie in Azure Monitor Protokolle im Zusammenhang mit Azure Event Hubs innerhalb des Aktivitätsprotokolls und der Event Hub-Diagnoseeinstellungen, um Protokolle für Abfragen an einen Log Analytics-Arbeitsbereich oder für die langfristige Archivierung an ein Speicherkonto zu senden. Verwenden Sie einen Log Analytics-Arbeitsbereich, um Warnungen zu anomalen Aktivitäten zu erstellen, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren. 

Grundlegendes zum Azure-Aktivitätsprotokoll: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Konfigurieren von Diagnoseeinstellungen für Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Erstellen von Warnungen für Log Analytics-Protokolldaten: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Noch nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Event Hub verarbeitet keine Antischadsoftwareprotokollierung.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Event Hubs verarbeitet keine Protokolle zu DNS und generiert auch keine solchen Protokolle.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Nicht zutreffend; diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind. 

Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Der Zugriff auf Steuerungsebene auf Event Hubs wird über Azure Active Directory (AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

Der Zugriff auf Datenebene auf Event Hubs wird über Azure AD mit verwalteten Identitäten oder App-Registrierungen sowie mit SAS (Shared Access Signatures) gesteuert. SAS (Shared Access Signatures) wird von den Clients verwendet, die eine Verbindung mit Ihren Event Hubs herstellen, und können jederzeit neu generiert werden.

Grundlegendes zu Shared Access Signatures (SAS) für Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Verwenden von Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Verwenden von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Microsoft Azure bietet eine integrierte Verwaltung der Zugriffssteuerung für Ressourcen und Anwendungen, basierend auf Azure Active Directory (AD). Ein wesentlicher Vorteil der Verwendung von Azure AD mit Azure Event Hubs besteht darin, dass Sie Ihre Anmeldeinformationen nicht mehr im Code speichern müssen. Stattdessen können Sie ein OAuth 2.0-Zugriffstoken von der Microsoft Identity Platform anfordern. Der Ressourcenname zum Anfordern eines Tokens lautet https:\//eventhubs.azure.net/. Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD ein Zugriffstoken an die Anwendung zurück, und die Anwendung kann dann das Zugriffstoken zum Autorisieren von Anforderungen an Azure Event Hubs verwenden.

Authentifizieren einer Anwendung mit Azure AD, um auf Azure Event Hubs-Ressourcen zuzugreifen: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Grundlegendes zu SSO mit Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure Active Directory Multi-Factor Authentication (MFA), und befolgen Sie die Empfehlungen zur Identitäts- und Zugriffsverwaltung von Azure Security Center, um Ihre Event Hub-fähigen Ressourcen zu schützen.

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Event Hub-fähigen Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Aktivieren von MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen. Wenn Sie eine zusätzliche Protokollierung benötigen, senden Sie die Risikoerkennungswarnungen von Azure Security Center an Azure Monitor, und konfigurieren Sie mithilfe von Aktionsgruppen benutzerdefinierte Warnungen/Benachrichtigungen.

Bereitstellen von Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Grundlegendes zu Azure AD-Risikoerkennungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Konfigurieren von Aktionsgruppen für benutzerdefinierte Warnungen und Benachrichtigungen: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.



Konfigurieren benannter Standorte in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem für Azure-Ressourcen wie Event Hubs. Dies ermöglicht der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) die Verwaltung vertraulicher Ressourcen.

 Erstellen und Konfigurieren einer Azure AD-Instanz: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Weitere Informationen zur Integration von Azure Event Hubs in Azure Active Directory (AAD) finden Sie unter „Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory“: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

Rotieren Sie außerdem regelmäßig die Shared Access Signatures (SAS) Ihrer Event Hubs.

Grundlegendes zur Azure AD-Berichterstellung: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Grundlegendes zu Shared Access Signatures (SAS) für Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten (Azure Active Directory) und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie die Features von Azure Active Directory Identity Protection und Risikoerkennung, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Ihren Event Hubs-fähigen Ressourcen zu konfigurieren. Sie sollten automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren.

Anzeigen riskanter Azure AD-Anmeldungen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Ausführen des Onboardings für Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Zurzeit nicht verfügbar. Kunden-Lockbox wird für Event Hubs noch nicht unterstützt.

Liste der durch Kunden-Lockbox unterstützten Dienste: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für Ressourcen Ihrer Event Hubs als Hilfe beim Nachverfolgen von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Event Hubs-Namespaces müssen durch ein virtuelles Netzwerk mit aktivierten und markierten Dienstendpunkten getrennt werden.

Sie können Ihren Azure Event Hubs-Namespace auch mithilfe von Firewalls sichern. Azure Event Hubs unterstützt IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Sie können Firewallregeln über das Azure-Portal, mit Azure Resource Manager-Vorlagen oder der Azure CLI bzw Azure PowerShell festlegen.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurieren von IP-Firewallregeln für Azure Event Hubs-Namespaces: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Erstellen und Nutzen von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Erstellen eines virtuellen Netzwerks: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Wenn Sie virtuelle Computer verwenden, um auf Ihre Event Hubs zuzugreifen, verwenden Sie virtuelle Netzwerke, Dienstendpunkte, eine Event Hubs-Firewall, Netzwerksicherheitsgruppen und Diensttags, um die Möglichkeit von Datenexfiltration zu verringern.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Event Hubs und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Konfigurieren von IP-Firewallregeln für Azure Event Hubs-Namespaces: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Grundlegendes zu Virtual Network-Dienstendpunkten mit Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrieren von Event Hubs in Azure Private Link: https://docs.microsoft.com/azure/event-hubs/private-link-service

Grundlegendes zu Netzwerksicherheitsgruppen und Diensttags: https://docs.microsoft.com/azure/virtual-network/security-overview

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Event Hubs erzwingt standardmäßig durch TLS verschlüsselte Kommunikation. Derzeit werden die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt. Die Unterstützung von TLS 1.0 und 1.1 wird jedoch branchenweit eingestellt werden. Verwenden Sie daher nach Möglichkeit TLS 1.2.

Informationen zu den Sicherheitsfeatures von Event Hubs finden Sie unter „Netzwerksicherheit“: https://docs.microsoft.com/azure/event-hubs/network-security

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen zur Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Event Hubs noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Azure Event Hubs unterstützt die Verwendung von Azure Active Directory (AD) zum Autorisieren von Anforderungen an Event Hubs-Ressourcen. Mit Azure AD können Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer oder einen Anwendungsdienstprinzipal handeln kann.

Grundlegendes zu Azure RBAC und verfügbaren Rollen für Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Event Hubs und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

Grundlegendes zum Schutz von Kundendaten in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Event Hubs unterstützt die Option zum Verschlüsseln ruhender Daten mit von Microsoft verwalteten oder kundenseitig verwalteten Schlüsseln. Mit dieser Funktion können Sie kundenseitig verwaltete Schlüssel, die zum Verschlüsseln ruhender Azure Event Hubs-Daten verwendet werden, erstellen, rotieren, deaktivieren und den Zugriff darauf widerrufen.

Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verschlüsselung von Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Event Hubs und anderen kritischen bzw. verwandten Ressourcen vorgenommen werden.

Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Nicht zutreffend. Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Event Hubs unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Microsoft führt die Patchverwaltung auf den zugrunde liegenden Systemen aus, die Event Hubs unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Nicht zutreffend. Benchmark ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Event Hubs unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend. Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Event Hubs unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich Azure Event Hubs-Namespaces) innerhalb Ihrer Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Erstellen von Abfragen mit Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Anzeigen Ihrer Azure-Abonnements: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Grundlegendes zu Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Event Hubs-Namespaces und zugehörigen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen zusätzlicher Azure-Abonnements: https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen: https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

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

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

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

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts</div>

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Azure Event Hubs-Bereitstellungen. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.EventHub“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Konfigurationen zu erstellen. Sie können auch die integrierten Richtliniendefinitionen für Azure Event Hubs verwenden, z. B.:

- In Event Hub sollten Diagnoseprotokolle aktiviert sein

- Event Hub sollte einen VNET-Dienstendpunkt verwenden

Integrierte Azure-Richtlinie für Event Hubs-Namespace: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Anzeigen verfügbarer Azure Policy-Aliase: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Richtlinien [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Event Hubs-fähigen Ressourcen zu erzwingen. 

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Weitere Informationen zu den Azure Policy-Effekten: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie für Ihre Event Hubs oder verwandte Ressourcen benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

Speichern von Code in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow

Dokumentation zu Azure Repos: https://docs.microsoft.com/azure/devops/repos/index

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.EventHub“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.EventHub“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (Überwachen), [deny] (Verweigern) und [deploy if not existent] (Bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure Event Hubs-Bereitstellungen und damit verbundene Ressourcen automatisch zu erzwingen.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Event Hubs ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um die SAS-Verwaltung (Shared Access Signature) für Ihre Azure Event Hubs-Bereitstellungen zu vereinfachen und zu schützen. Stellen Sie sicher, dass vorläufiges Löschen für Key Vault aktiviert ist.

Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurieren von kundenseitig verwalteten Schlüsseln für Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Integration mit verwalteten Azure-Identitäten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Erstellen einer Key Vault-Instanz: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Authentifizieren bei Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Zuweisen einer Key Vault-Zugriffsrichtlinie: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie für virtuelle Azure-Computer oder Webanwendungen, die in Azure App Service für den Zugriff auf Ihre Event Hubs ausgeführt werden, verwaltete Dienstidentitäten in Verbindung mit Azure Key Vault, um Azure Event Hubs-Bereitstellungen zu vereinfachen und zu schützen. Stellen Sie sicher, dass vorläufiges Löschen für Key Vault aktiviert ist.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurieren von kundenseitig verwalteten Schlüsseln für Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

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

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Führen Sie eine Vorabprüfung aller Inhalte durch, die in computefremde Azure-Ressourcen hochgeladen werden, z. B. in Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

Microsoft-Antischadsoftware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Cache for Redis) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Konfigurieren georedundanter Notfallwiederherstellung für Azure Event Hubs. Falls gesamte Azure-Regionen oder -Rechenzentren ausfallen (wenn keine Verfügbarkeitszonen verwendet werden), ist es von entscheidender Bedeutung, dass die Datenverarbeitung in einer anderen Region oder in einem anderen Rechenzentrum fortgesetzt werden kann. Daher sind georedundante Notfallwiederherstellung und Georeplikation wichtige Funktionen für jedes Unternehmen. Azure Event Hubs unterstützt die georedundante Notfallwiederherstellung und die Georeplikation auf Namespaceebene. 

Grundlegendes zu georedundanter Notfallwiederherstellung für Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Azure Event Hubs ermöglicht die Verschlüsselung ruhender Daten mit Azure Storage Service Encryption (Azure SSE). Event Hubs verwendet Azure Storage zum Speichern der Daten. Standardmäßig werden alle Daten, die mit Azure Storage gespeichert werden, durch von Microsoft verwaltete Schlüssel verschlüsselt. Wenn Sie Azure Key Vault für die Speicherung kundenseitig verwalteter Schlüsseln verwenden, stellen Sie regelmäßige automatisierte Sicherungen Ihrer Schlüssel sicher.

Stellen Sie mithilfe des folgenden PowerShell-Befehls sicher, dass regelmäßig automatisierte Sicherungen Ihrer Key Vault-Geheimnisse erstellt werden: Backup-AzKeyVaultSecret

Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verschlüsselung von ruhenden Azure Event Hubs-Daten: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Sichern von Key Vault-Geheimnissen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

 

Wiederherstellen von Key Vault-Schlüsseln in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Aktivieren von vorläufigem Löschen in Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen. Azure Event Hubs erfordert, dass für kundenseitig verwaltete Schlüssel vorläufiges Löschen und keine Bereinigung konfiguriert ist.

Konfigurieren Sie vorläufiges Löschen für das Azure Storage-Konto, das zum Erfassen von Event Hubs-Daten verwendet wird. Beachten Sie, dass dieses Feature für Azure Data Lake Storage Gen 2 noch nicht unterstützt wird.

Aktivieren des vorläufigen Löschens in Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Einrichten eines Schlüsseltresors mit Schlüsseln: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Vorläufiges Löschen für Azure Storage-Blobs: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen die Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen definiert sind.

Konfigurieren von Workflowautomatisierungen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

**Azure Security Center-Überwachung**: Ja

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

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

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.
Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).