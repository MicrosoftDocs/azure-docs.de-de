---
title: Azure-Sicherheitsbaseline für Event Grid
description: Die Event Grid-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 575742ab0d886a69dc5acccf4a65ac8c91b5a871
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341034"
---
# <a name="azure-security-baseline-for-event-grid"></a>Azure-Sicherheitsbaseline für Event Grid

Die Azure-Sicherheitsbaseline für Microsoft Azure Event Grid enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können. Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können. Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Sie können private Endpunkte verwenden, um den Eingang von Ereignissen direkt aus Ihrem virtuellen Netzwerk in Ihre Event Grid-Themen und -Domänen sicher über einen privaten Link zu ermöglichen, ohne das öffentliche Internet zu durchlaufen. Wenn Sie einen privaten Endpunkt für Ihr Event Grid-Thema oder Ihre Event Grid-Domäne erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und der Event Grid-Ressource bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres virtuellen Netzwerks zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und dem Event Grid-Dienst wird eine sichere private Verbindung verwendet.

Azure Event Grid unterstützt zudem öffentliche IP-basierte Zugriffssteuerungen für die Veröffentlichung in Themen und Domänen. Mit IP-basierter Steuerung können Sie Herausgeber auf ein Thema oder eine Domäne beschränken, sodass Sie nur auf eine Reihe genehmigter Computer und Clouddienste festgelegt sind. Diese Funktion ergänzt die Authentifizierungsmechanismen, die von Event Grid unterstützt werden. 

- [Weitere Informationen zu privaten Event Grid-Endpunkten](network-security.md#private-endpoints)

- [Weitere Informationen zur IP-Firewall von Event Grid](network-security.md#ip-firewall)

- [Azure Event Grid-Netzwerksicherheit](network-security.md) 

- [Übersicht über Azure Private Link](../private-link/private-link-overview.md)

- [Azure-Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Event Grid-Ressourcen in Azure abzusichern. Wenn Sie virtuelle Azure-Computer verwenden, um auf Ihre Event Grid-Ressourcen zuzugreifen, aktivieren Sie die Datenflussprotokolle der Netzwerksicherheitsgruppen (NSGs), und senden Sie Protokolle an ein Speicherkonto für die Überwachung

des Datenverkehrs.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Grundlegendes zur Netzwerksicherheit über das Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Sie können die IP-Firewall für Ihre Event Grid-Ressource so konfigurieren, dass der Zugriff über das öffentliche Internet auf eine bestimmte Gruppe von IP-Adressen oder IP-Adressbereichen beschränkt wird.

Sie können private Endpunkte konfigurieren, um den Zugriff nur auf ausgewählte virtuelle Netzwerke zu beschränken.

Aktivieren Sie DDoS Protection Standard für diese virtuellen Netzwerke, um sie gegen DDoS-Angriffe (Distributed Denial-of-Service) zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern. Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Konfigurieren privater Endpunkte für Azure Event Grid-Themen oder -Domänen](configure-private-endpoints.md)

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

- [Weitere Informationen zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Wenn Sie virtuelle Azure-Computer verwenden, um auf Ihre Event Grid-Ressourcen zuzugreifen, aktivieren Sie die Datenflussprotokolle der Netzwerksicherheitsgruppen (NSGs), und senden Sie Protokolle an ein Speicherkonto für die Überwachung des Datenverkehrs. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Hinweis: Netzwerkrichtlinien sind standardmäßig deaktiviert, wenn private Endpunkte für Event Grid erstellt werden. Daher funktioniert der oben beschriebene Workflow möglicherweise nicht.

Aktivieren Sie die Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.  Wenn Nutzlastuntersuchung keine Anforderung ist, kann Azure Firewall Threat Intelligence verwendet werden. Die auf Azure Firewall Threat Intelligence basierende Filterung wird verwendet, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu blockieren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen in virtuellen Netzwerken, die Zugriff auf Ihre Azure Event Grid-Ressourcen benötigen, VNET-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (beispielsweise „AzureEventGrid“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Verwenden des Diensttags für Azure Event Grid](network-security.md#service-tags)

- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Event Grid-Namespaces zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.EventGrid“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Event Grid-Ressourcen zu erstellen. 

Sie können auch die integrierten Richtliniendefinitionen für Azure Event Grid verwenden. Dazu zählen u. a. die folgenden: „Azure Event Grid-Domänen müssen private Links verwenden“ und „Azure Event Grid-Themen müssen private Links verwenden“
- [Integrierte Richtlinien für Event Grid-Ressourcen](../governance/policy/samples/built-in-policies.md#event-grid)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihren Azure Event Grid-Ressourcen zugeordnet sind, um sie in einer Taxonomie logisch zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Azure Event Grid beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von Azure Event Grid generierten Sicherheitsdaten zu aggregieren. Verwenden Sie Log Analytics-Arbeitsbereiche in Azure Monitor, um Analysen abzufragen und auszuführen, und verwenden Sie Speicherkonten für die langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren.

- [Aktivieren von Diagnoseprotokollen für Azure Event Grid](diagnostic-logs.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Diagnoseeinstellungen ermöglichen es Event Grid-Benutzern, Protokolle zu Veröffentlichungs- und Übermittlungsfehlern entweder in einem Speicherkonto, einem Event Hub oder einem Log Analytics-Arbeitsbereich zu erfassen und anzuzeigen.

- [Aktivieren von Diagnoseprotokollen für Azure Event Grid-Themen oder -Domänen](enable-diagnostic-logs-topic.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum für Protokolle für Log Analytics-Arbeitsbereiche, die Ihren Azure Event Grid-Ressourcen zugeordnet sind, gemäß den Compliancevorschriften Ihrer Organisation fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und überprüfen Sie die Ergebnisse aus Azure Event Grid regelmäßig. Verwenden Sie Azure Monitor und einen Log Analytics-Arbeitsbereich, um Protokolle zu überprüfen und Abfragen für die Protokolldaten auszuführen.

Alternativ können Sie auch Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren. 

- [Aktivieren von Diagnoseprotokollen für Azure Event Grid-Themen oder -Domänen](enable-diagnostic-logs-topic.md)

- [Ausführen von Abfragen für Azure Event Grid in Log Analytics-Arbeitsbereichen](diagnostic-logs.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Aktivieren von Diagnoseeinstellungen für Ihr Event Grid, um auf Fehlerprotokolle im Zusammenhang mit der Veröffentlichung und Übermittlung zuzugreifen. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente. Sie können die Protokolle an einen Log Analytics-Arbeitsbereich senden. Verwenden Sie Azure Security Center mit Log Analytics für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. 

Außerdem können Sie Warnungen zu Azure Event Grid-Metriken und -Aktivitätsprotokollvorgängen erstellen. Sie können Warnungen zu Veröffentlichungs- und Übermittlungsmetriken für Azure Event Grid-Ressourcen (Themen und Domänen) erstellen. 

Darüber hinaus können Sie Ihren Log Analytics-Arbeitsbereich in Azure Sentinel integrieren, um eine Lösung für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR) bereitzustellen. Dadurch können Playbooks (automatisierte Lösungen) erstellt und zum Beheben von Sicherheitsproblemen verwendet werden.

- [Aktivieren von Diagnoseprotokollen für Azure Event Grid-Themen oder -Domänen](enable-diagnostic-logs-topic.md)

- [Festlegen von Warnungen für Azure Event Grid-Metriken und -Aktivitätsprotokolle](set-alerts.md)

- [Details des Event Grid-Diagnoseprotokollschemas](diagnostic-logs.md)

- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend; In Azure Event Grid werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend; In Azure Event Grid werden keine Protokolle zu DNS verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Event Grid bietet die Möglichkeit, den Umfang zu steuern, in dem unterschiedliche Benutzer Zugriff auf verschiedene Verwaltungsvorgänge erhalten, z.B. Auflisten und Erstellen von Ereignisabonnements und Generieren von Schlüsseln. Event Grid verwendet die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC). Event Grid unterstützt integrierte und benutzerdefinierte Rollen.

Mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden.

- [Autorisieren des Zugriffs auf Event Grid-Ressourcen](security-authorization.md)

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Die Zugriffsverwaltung für Event Grid-Ressourcen wird über Azure Active Directory (Azure AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten.

Sie können auch den Just-In-Time-Zugriff aktivieren, indem Sie Azure AD Privileged Identity Management und Azure Resource Manager verwenden.

Event Grid kann eine verwaltete Dienstidentität für Azure Event Grid-Themen oder -Domänen aktivieren und mit dieser Identität Ereignisse an unterstützte Ziele weiterleiten (z. B. Service Bus-Warteschlangen und -Themen, Event Hubs und Speicherkonten). Für die Veröffentlichung von Ereignissen in Azure Event Grid wird ein SAS-Token (Shared Access Signature) verwendet. Erstellen Sie standardmäßige Verfahren für den Zugriff auf Ereignisse sowie für die Weiterleitung und Veröffentlichung mit diesen Konten.

- [Authentifizieren der Ereignisübermittlung an Ereignishandler (Azure Event Grid)](security-authentication.md)

- [Authentifizieren von Veröffentlichungsclients (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Nicht zutreffend; der Event Grid-Dienst bietet keine Unterstützung für SSO.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Nicht zutreffend; die mehrstufige Authentifizierung wird nicht vom Event Grid-Dienst verwendet

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Nicht zutreffend; für Event Grid-Szenarien sind keine Arbeitsstationen mit privilegiertem Zugriff erforderlich. 

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte und -Überwachungsfunktionen, um verdächtige oder nicht sichere Aktivität in der Umgebung zu ermitteln. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Nicht zutreffend Azure AD wird nicht von Event Grid für die Authentifizierung von Clients verwendet, die Ereignisse veröffentlichen. Event Grid unterstützt die Authentifizierung über SAS-Schlüssel.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

Event Grid kann eine verwaltete Dienstidentität für Azure Event Grid-Themen oder -Domänen aktivieren und mit dieser Identität Ereignisse an unterstützte Ziele weiterleiten (z. B. Service Bus-Warteschlangen und -Themen, Event Hubs und Speicherkonten). Für die Veröffentlichung von Ereignissen in Azure Event Grid wird ein SAS-Token (Shared Access Signature) verwendet. 

- [Authentifizieren der Ereignisübermittlung an Ereignishandler (Azure Event Grid)](security-authentication.md)

- [Authentifizieren von Veröffentlichungsclients (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Azure AD-Identitäts und -Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. 
 
Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten und Überwachungs- und Risikoereignisse, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Azure AD Identity Protection-Funktionen können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.
 
 
 
- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht zutreffend; der Event Grid-Dienst unterstützt aktuell keine Kunden-Lockbox.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.
 
 
 
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. Sie können die Zugriffsebene auf diejenigen Ihrer Azure-Ressourcen beschränken, die von Ihren Anwendungen und Unternehmensumgebungen gefordert werden. Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure steuern.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Event Grid erfordert HTTPS für die Veröffentlichung und unterstützt HTTPS für die Übermittlung von Ereignissen an einen Webhook-Endpunkt. In Azure Global unterstützt Event Grid sowohl TLS 1.1 als auch TLS 1.2. Es wird jedoch dringend empfohlen, Version 1.2 zu verwenden. In nationalen Clouds wie Azure Government und Azure-Bereitstellungen über 21Vianet in China unterstützt Event Grid lediglich TLS 1.2. 

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen zur Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Event Grid noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Ressourcenzugriffs

**Leitfaden**: Azure Event Grid unterstützt die Verwendung von Azure Active Directory (AD) zum Autorisieren von Anforderungen an Event Grid-Ressourcen. Mit Azure AD können Sie Azure RBAC (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung von Azure) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer oder einen Anwendungsdienstprinzipal handeln kann.

- [Autorisieren des Zugriffs auf Event Grid-Ressourcen](security-authorization.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Event Grid und anderen kritischen bzw. verwandten Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.
 
 
 
- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

* Not allowed resource types (Unzulässige Ressourcentypen)
* Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

* Not allowed resource types (Unzulässige Ressourcentypen)
* Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie bedingten Azure AD-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.
 
 
 
- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihren Azure Event Grid-Dienst. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.EventGrid“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Event Grid-Dienste zu erstellen.

Mit Azure Resource Manager ist es möglich, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser sollte vor der Bereitstellung überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen. Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die für Ihre Organisation erforderliche Sicherheitskonfiguration der Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie für Ihr Event Grid oder verwandte Ressourcen benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.EventGrid“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Verwenden von Aliasen](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen. Nutzen Sie Azure Policy auch für Warnungen und Überprüfungen in Bezug auf Azure-Ressourcenkonfigurationen.

- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Event Grid verwendet SAS-Token (Shared Access Signature) für die Veröffentlichung von Ereignissen in Event Grid-Themen oder -Domänen. Dabei werden SAS-Token erstellt, die lediglich innerhalb eines bestimmten Zeitfensters Zugriff auf die erforderlichen Ressourcen bieten.

Verwenden Sie verwaltete Identitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen.

- [Authentifizieren von Veröffentlichungsclients (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Verwenden von verwalteten Identitäten für Azure-Ressourcen](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

- [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Event Grid kann eine verwaltete Dienstidentität für Azure Event Grid-Themen oder -Domänen aktivieren. Verwenden Sie diese Methode, um Ereignisse an unterstützte Ziele wie Service Bus-Warteschlangen und -Themen, Event Hubs und Speicherkonten weiterzuleiten.

- [Ereignisübermittlung mit einer verwalteten Identität](managed-service-identity.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Event Grid) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Es liegt in Ihrer Verantwortung, alle Inhalte vorab zu überprüfen, die auf computefremde Azure-Ressourcen hochgeladen werden. Microsoft kann nicht auf Kundendaten zugreifen und kann daher keine Antischadsoftwarescans von Kundeninhalten in Ihrem Namen durchführen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Event Grid verfügt über eine automatische georedundante Notfallwiederherstellung (GeoDR) für Metadaten – nicht nur für neue, sondern auch für alle vorhandenen Domänen, Themen und Ereignisabonnements. Wenn eine vollständige Azure-Region ausfällt, hat Event Grid bereits alle ereignisbezogenen Infrastrukturmetadaten in ein Regionspaar synchronisiert.

- [Serverseitige georedundante Notfallwiederherstellung in Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Event Grid verfügt über eine automatische georedundante Notfallwiederherstellung (GeoDR) für Metadaten – nicht nur für neue, sondern auch für alle vorhandenen Domänen, Themen und Ereignisabonnements. Wenn eine vollständige Azure-Region ausfällt, hat Event Grid bereits alle ereignisbezogenen Infrastrukturmetadaten in ein Regionspaar synchronisiert.

Derzeit unterstützt Event Grid keine kundenseitig verwalteten Schlüssel. 

- [Serverseitige georedundante Notfallwiederherstellung in Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Event Grid verfügt über eine automatische georedundante Notfallwiederherstellung (GeoDR) für Metadaten – nicht nur für neue, sondern auch für alle vorhandenen Domänen, Themen und Ereignisabonnements. Wenn eine vollständige Azure-Region ausfällt, hat Event Grid bereits alle ereignisbezogenen Infrastrukturmetadaten in ein Regionspaar synchronisiert.

Derzeit unterstützt Event Grid keine kundenseitig verwalteten Schlüssel. 

- [Serverseitige georedundante Notfallwiederherstellung in Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen. 
 

Derzeit unterstützt Event Grid keine kundenseitig verwalteten Schlüssel. 

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle in Ihrer Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. 

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie sicher Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

 
 
 
 Markieren Sie Abonnements außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Ermitteln Sie Schwachpunkte und Lücken, und passen Sie Ihren Reaktionsplan dann entsprechend an.
 
 
 
- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.
 
 
 
- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über die Funktion „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und Ihre Azure-Ressourcen noch besser zu schützen.

- [Konfigurieren der Workflowautomatisierung in Security Center](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).