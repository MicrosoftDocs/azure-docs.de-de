---
title: Azure-Sicherheitsbaseline für Azure Machine Learning
description: Die Azure Machine Learning-Sicherheitsbaseline enthält schrittweise Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f3f66209b2d0a1bae18364e38790c237952e729f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458258"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure-Sicherheitsbaseline für Azure Machine Learning

Die Azure-Sicherheitsbaseline für Microsoft Azure Machine Learning enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können. Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können. Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Azure Machine Learning ist hinsichtlich Computeressourcen auf andere Azure-Dienste angewiesen. Computeressourcen (Computeziele) dienen zum Trainieren und Bereitstellen von Modellen. Sie können diese Computeziele in einem virtuellen Netzwerk erstellen. Beispielsweise können Sie die Computeinstanz von Azure Virtual Machine Learning verwenden, um ein Modell zu trainieren, und das Modell anschließend in Azure Kubernetes Service (AKS) bereitstellen. Sie können Ihre Machine Learning-Lebenszyklen durch Isolieren von Trainings- und Rückschlussaufträgen in Azure Machine Learning innerhalb eines virtuellen Azure-Netzwerks (VNET) sichern können.

Azure Firewall kann verwendet werden, um den Zugriff auf Ihren Azure Machine Learning-Arbeitsbereich und das öffentliche Internet zu steuern.

- [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)

- [Verwenden des Arbeitsbereichs hinter Azure Firewall für Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Azure Machine Learning ist hinsichtlich Computeressourcen auf andere Azure-Dienste angewiesen. Weisen Sie den Netzwerken, die als Ihre Machine Learning-Bereitstellung erstellt werden, Netzwerksicherheitsgruppen zu. 

Aktivieren Sie Netzwerksicherheitsgruppen-Datenflussprotokolle, und senden Sie Protokolle zur Überwachung an ein Azure Storage-Konto. Sie können die Datenflussprotokolle auch an einen Log Analytics-Arbeitsbereich senden und dann mithilfe von Traffic Analytics Einblicke in die Datenverkehrsmuster in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren, Hotspots und Sicherheitsbedrohungen zu erkennen, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Sie können HTTPS aktivieren, um die Kommunikation mit den von Azure Machine Learning bereitgestellten Webdiensten zu sichern. Webdienste werden in Azure Kubernetes Services (AKS) oder Azure Container Instances (ACI) bereitgestellt und sichern die von den Clients übermittelten Daten. Sie können auch private IP-Adressen mit AKS verwenden, um die Bewertung einzuschränken, sodass nur Clients, die sich hinter einem virtuellen Netzwerk befinden, auf den Webdienst zugreifen können.

- [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)

- [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie in den virtuellen Netzwerken, die Ihrer Machine Learning-Instanz zugeordnet sind, DDoS Protection Standard, um sich vor DDoS-Angriffen (Distributed Denial-of-Service) zu schützen. Verwenden Sie die in Azure Security Center integrierte Bedrohungserkennung, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu erkennen.

Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation mit aktivierter, auf Threat Intelligence basierender Filterung bereit, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird.

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

- [Verwenden des Arbeitsbereichs hinter Azure Firewall für Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Weitere Informationen zur Bedrohungserkennung in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Für alle VMs mit der richtigen Erweiterung, die in Azure Machine Learning Services installiert sind, können Sie Network Watcher-Paketerfassung aktivieren, um anomale Aktivitäten zu untersuchen. 

- [Erstellen einer Network Watcher-Instanz](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu blockieren.

Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.  Wenn Nutzlastuntersuchung keine Anforderung ist, kann Azure Firewall Threat Intelligence verwendet werden. Die auf Azure Firewall Threat Intelligence basierende Filterung wird verwendet, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die Zugriff auf Ihr Azure Machine Learning-Konto benötigen, Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (beispielsweise „AzureMachineLearning“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Azure Machine Learning Service dokumentiert eine Liste von Diensttags für die Computeziele innerhalb eines virtuellen Netzwerks, die dazu beitragen, die Komplexität zu verringern. Sie können diese als Richtlinien in Ihrer Netzwerkverwaltung verwenden.

- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

- [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Machine Learning-Namespaces zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.MachineLearning“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Machine Learning-Namespaces zu erstellen. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihrer Azure Machine Learning-Bereitstellung zugeordnet sind, um sie gemäß einer Taxonomie logisch zu organisieren.

Für eine Ressource in Ihrem virtuellen Azure Machine Learning-Netzwerk, die das Feld „Description“ (Beschreibung) unterstützt, verwenden Sie diese, um die Regeln zu dokumentieren, die Datenverkehr zu/aus einem Netzwerk erlauben.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Azure Machine Learning beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle für Zeitstempel in den Protokollen, die für Azure-Ressourcen wie Azure Machine Learning verwendet wird.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von Azure Machine Learning generierten Sicherheitsdaten zu aggregieren. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-Security Incident and Event Management-Lösung (SIEM) aktivieren und integrieren.

- [Konfigurieren von Diagnoseprotokollen für Azure Machine Learning](monitor-azure-machine-learning.md#configuration)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren der Diagnoseeinstellungen für Azure-Ressourcen für den Zugriff auf Überwachungs-, Sicherheits- und Diagnoseprotokolle. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente.

Sie können Vorgangsprotokolle von Machine Learning Service auch zu Sicherheits- und Compliancezwecken korrelieren.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Aktivieren der Protokollierung in Azure Machine Learning](./how-to-track-experiments.md)

- [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, ist Microsoft für die Erfassung und Überwachung verantwortlich. 

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Für Computeressourcen, die sich im Besitzt Ihrer Organisation befinden, verwenden Sie Azure Security Center, um das Betriebssystem zu überwachen. 

- [Sammeln von internen Azure Virtual Machine-Hostprotokollen mit Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Grundlegendes zur Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor die Protokollaufbewahrungsdauer für Log Analytics-Arbeitsbereiche, die Ihrer Azure Machine Learning-Instanz zugeordnet sind, gemäß den Compliancevorschriften Ihres Unternehmens fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und überprüfen Sie die Ergebnisse aus Azure Machine Learning regelmäßig. Verwenden Sie Azure Monitor und einen Log Analytics-Arbeitsbereich, um Protokolle zu überprüfen und Abfragen für die Protokolldaten auszuführen.

Alternativ können Sie auch Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren. 

- [Ausführen von Abfragen für Azure Machine Learning in Log Analytics-Arbeitsbereichen](monitor-azure-machine-learning.md#analyzing-log-data)

- [Aktivieren der Protokollierung in Azure Machine Learning](./how-to-track-experiments.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Konfigurieren Sie in Azure Monitor Protokolle im Zusammenhang mit Azure Machine Learning innerhalb des Aktivitätsprotokolls und der Machine Learning-Diagnoseeinstellungen, um Protokolle für Abfragen an einen Log Analytics-Arbeitsbereich oder für die langfristige Archivierung an ein Speicherkonto zu senden. Verwenden Sie einen Log Analytics-Arbeitsbereich, um Warnungen zu anomalen Aktivitäten zu erstellen, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren.

- [Weitere Informationen zu Azure Machine Learning-Warnungen](monitor-azure-machine-learning.md#alerts)

- [Erstellen von Warnungen für Protokolldaten eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/tutorial-response.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für die Bereitstellung von Antischadsoftware von Azure Machine Learning Service verantwortlich. 

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Aktivieren Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Antischadsoftware-Ereigniserfassung für Microsoft Antimalware für Azure Cloud Services und Virtual Machines.

- [Konfigurieren von Microsoft Antimalware für einen virtuellen Computer](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Konfigurieren der Microsoft Antimalware-Erweiterung für Clouddienste](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Grundlegendes zu Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend, da Azure Machine Learning keine DNS-bezogenen Protokolle verarbeitet oder generiert.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Wenn sich Computeressourcen im Besitz Ihrer Organisation befinden, verwenden Sie Azure Security Center, um Überwachung des Sicherheitsereignisprotokolls für virtuelle Azure-Computer zu aktivieren. Bei aktivierter automatischer Bereitstellung wird der Log Analytics-Agent von Azure Security Center auf allen unterstützten und neu erstellten virtuellen Azure-Computern bereitgestellt. Alternativ können Sie den Agent manuell installieren. Der Agent liefert das Prozesserstellungsereignis 4688 und das Feld „CommandLine“ im Ereignis 4688. Auf der VM erstellte neue Prozesse werden vom Ereignisprotokoll aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Sie können die Registerkarte „Identitäts- und Zugriffsverwaltung“ für eine Ressource im Azure-Portal verwenden, um rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) zu konfigurieren und den Bestand der Azure Machine Learning-Ressourcen zu verwalten. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden.

Azure Machine Learning bietet für gängige Verwaltungsszenarien in Azure Machine Learning integrierte Rollen. Eine Person mit einem Profil in Azure Active Directory (Azure AD) kann diese Rollen Benutzern, Gruppen, Dienstprinzipalen oder verwalteten Identitäten zuweisen, um den Zugriff auf Ressourcen und Vorgänge für Azure Machine Learning-Ressourcen zu gewähren oder zu verweigern.

Sie können auch das Azure AD PowerShell-Modul verwenden, um Ad-hoc-Abfragen zum Ermitteln von Konten durchzuführen, die Mitglieder von Verwaltungsgruppen sind.

- [Informationen zur rollenbasierten Azure-Zugriffssteuerung in Azure Machine Learning](how-to-assign-roles.md)

- [Abrufen einer Verzeichnisrolle in Azure Active Directory mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Die Zugriffsverwaltung für Machine Learning-Ressourcen wird über Azure Active Directory (Azure AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Azure Machine Learning stellt drei Standardrollen zur Verfügung, wenn ein neuer Arbeitsbereich erstellt wird. Erstellen Sie Standardbetriebsverfahren rund um die Verwendung von Besitzerkonten.

Sie können auch Just-In-Time-Zugriff auf Verwaltungskonten aktivieren, indem Sie Azure AD Privileged Identity Management und Azure Resource Manager verwenden. 

- [Weitere Informationen zu Machine Learning-Standardrollen](how-to-assign-roles.md#default-roles)

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Machine Learning ist in Azure Active Directory integriert. Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identität und den Zugriff in Azure Security Center an.  

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie mehrstufige Authentifizierung für Azure Active Directory, und befolgen Sie die Empfehlungen für Identität und Zugriff in Azure Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine sichere, von Azure verwaltete Arbeitsstation (auch als Arbeitsstation mit privilegiertem Zugriff, Privileged Access Workstation (PAW) bezeichnet) für Verwaltungsaufgaben, für die erhöhte Rechte benötigt werden.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)

- [Planen einer Bereitstellung von Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte und -Überwachungsfunktionen, um verdächtige oder nicht sichere Aktivität in der Umgebung zu ermitteln. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Azure AD-Standorte, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.
 
 
 
- [Schnellstart: Konfigurieren benannter Orte in Azure Active Directory](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.
 
Der Rollenzugriff kann für mehrere Ebenen in Azure gelten. Für Machine Learning können Rollen auf Arbeitsbereichsebene verwaltet werden. Es kann z. B. sein, dass ein Benutzer mit Vollzugriff für einen Arbeitsbereich für die Ressourcengruppe, die diesen Arbeitsbereich enthält, keinen Vollzugriff besitzt. Dies ermöglicht präzisere Zugriffssteuerungen, um Rollen innerhalb derselben Ressourcengruppe voneinander zu trennen. 

- [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md) 
 
- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Azure AD-Identitäts und -Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. 
 
Verwenden Sie Azure Active Directory Privileged Identity Management (Azure AD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

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

**Leitfaden**: Nicht zutreffend. Azure Machine Learning Service unterstützt keine Kunden-Lockbox.

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

**Leitfaden**: Verwenden Sie eine Drittanbieterlösung aus Azure Marketplace an Netzwerkumkreisen, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt. 

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung. 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Über Azure Machine Learning bereitgestellte Webdienste unterstützen nur TLS-Version 1.2, bei der Datenverschlüsselung für Daten im Übergang erzwungen wird.

- [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen zur Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Machine Learning noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Zugriffs auf Ressourcen

**Leitfaden**: Azure Machine Learning unterstützt die Verwendung von Azure Active Directory (Azure AD) zum Autorisieren von Anforderungen an Machine Learning-Ressourcen. Mit Azure AD können Sie Azure RBAC (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung von Azure) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer oder einen Anwendungsdienstprinzipal handeln kann.

- [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md)
- [Verwenden von Azure RBAC für die Kubernetes-Autorisierung](../aks/manage-azure-rbac.md)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Machine Learning und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Machine Learning speichert Momentaufnahmen, Ausgaben und Protokolle in dem Azure Blob Storage-Konto, das mit dem Azure Machine Learning-Arbeitsbereich und Ihrem Abonnement verknüpft ist. Alle in Azure Blob Storage gespeicherten Daten werden im Ruhezustand mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können auch Daten, die in Azure Blob Storage gespeichert sind, mit Ihren eigenen Schlüsseln in Machine Learning Service verschlüsseln. 

- [Azure Machine Learning: Verschlüsselung ruhender Daten](concept-enterprise-security.md#encryption-at-rest)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurieren der von Kunden verwalteten Verschlüsselungsschlüssel](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Machine Learning und anderen kritischen bzw. verwandten Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für die Verwaltung von Sicherheitsrisiken von Azure Machine Learning Service verantwortlich. 

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Befolgen Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, die Empfehlungen aus Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre virtuellen Azure-Computer, Containerimages und SQL Server-Instanzen.

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für die Patchverwaltung von Azure Machine Learning Service verantwortlich. 

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für alle Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Azure Automation-Updateverwaltung, um sicherzustellen, dass die aktuellsten Sicherheitsupdates auf Ihren Windows- und Linux-VMs installiert sind. Stellen Sie bei virtuellen Windows-Computern sicher, dass Windows Update aktiviert wurde und auf „Automatisch Aktualisieren“ festgelegt ist.

- [Konfigurieren der Updateverwaltung für virtuelle Computer in Azure](../automation/update-management/overview.md)

- [Grundlegendes zu von Security Center überwachten Azure-Sicherheitsrichtlinien](../security-center/policy-reference.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, eine Patchverwaltungslösung eines Drittanbieters. Kunden, die bereits Configuration Manager in ihrer Umgebung verwenden, können auch System Center Updates Publisher verwenden, sodass sie benutzerdefinierte Updates in Windows Server Update Service veröffentlichen können. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Befolgen Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Empfehlungen aus Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre virtuellen Azure-Computer, Containerimages und SQL Server-Instanzen. Exportieren Sie die Scanergebnisse in regelmäßigen Abständen und vergleichen Sie die Ergebnisse mit vorherigen Scans, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn Sie die von Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwenden, können Sie in das Portal der ausgewählten Lösung wechseln, um die historischen Scandaten anzuzeigen.

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports, Protokolle usw.) in Ihren Abonnements abzufragen und zu ermitteln.  Sorgen Sie für entsprechende (Lese-) Berechtigungen auf Ihrem Mandanten, und zählen Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über Azure Resource Graph Explorer ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, um Metadaten hinzuzufügen und die Ressourcen einer Taxonomie entsprechend logisch zu organisieren.

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

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

* Not allowed resource types (Unzulässige Ressourcentypen)
* Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Azure Automation-Änderungsnachverfolgung und -Bestand, um die Erfassung von Inventarinformationen von Ihren virtuellen Windows- und Linux-VMs zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Um das Installationsdatum und andere Informationen abzurufen, aktivieren Sie die Diagnose auf Gastebene, und leiten Sie die Windows-Ereignisprotokolle an einen Log Analytics-Arbeitsbereich um.

- [Aktivieren von Azure Automation-Bestandserfassung für eine VM](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, verwenden Sie die Dateiintegritätsüberwachung (File Integrity Monitoring, FIM) von Azure Security Center, um die gesamte auf VMs installierte Software zu identifizieren. Eine weitere Option, die anstelle von oder in Verbindung mit FIM verwendet werden kann, ist Azure Automation-Änderungsnachverfolgung und -Bestand zum Erfassen von Bestand von Ihren Linux- und Windows-VMs. 

Sie können einen eigenen Prozess zum Entfernen nicht autorisierter Software implementieren. Sie können auch eine Drittanbieterlösung verwenden, um nicht genehmigte Software zu identifizieren.

Entfernen Sie Azure-Ressourcen, wenn diese nicht mehr benötigt werden.

- [Verwenden der Dateiintegritätsüberwachung](../security-center/security-center-file-integrity-monitoring.md)

- [Grundlegendes zu Azure Automation-Änderungsnachverfolgung und -Bestand](../automation/change-tracking/overview.md)

- [Aktivieren des Bestands an virtuellen Azure-Computern](../automation/automation-tutorial-installed-software.md)

- [Löschen von Azure-Ressourcengruppen und -Ressourcen](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, können Sie mit den adaptiven Anwendungssteuerungen in Azure Security Center sicherstellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

- [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

* Not allowed resource types (Unzulässige Ressourcentypen)
* Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für beliebige Computeressourcen, die sich im Besitz Ihrer Organisation befinden, die adaptiven Anwendungssteuerungen in Azure Security Center, um festzulegen, auf welche Dateitypen eine Regel angewendet oder nicht angewendet werden kann.

Implementieren Sie eine Drittanbieterlösung, wenn adaptive Anwendungssteuerungen die Anforderung nicht erfüllen.

- [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie bedingten Azure AD-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.
 
- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, können Sie abhängig vom Skripttyp betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern verwenden, um die Möglichkeit von Benutzern zur Skriptausführung innerhalb von Azure-Computeressourcen einzuschränken.  Sie können auch die adaptiven Anwendungssteuerungen in Azure Security Center verwenden, um sicherzustellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

- [Steuern der PowerShell-Skriptausführung in Windows-Umgebungen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Azure Machine Learning Service. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.MachineLearning“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Machine Learning Services zu erstellen.

Mit Azure Resource Manager ist es möglich, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen.

Sie können auch die Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

Azure Machine Learning unterstützt Git-Repositorys zur Nachverfolgung der Arbeit – Sie können Repositorys direkt für Ihr freigegebenes Arbeitsbereichsdateisystem klonen, Git auf Ihrer lokalen Arbeitsstation verwenden und als Teil Ihrer Machine Learning-Umgebung sicherstellen, dass sichere Konfigurationen für Coderessourcen gelten.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für den Betrieb systemsicherer Konfigurationen von Azure Machine Learning Service verantwortlich.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitzt Ihrer Organisation befinden, Azure Security Center-Empfehlungen, um die Sicherheitskonfigurationen für alle Computeressourcen zu verwalten.  Darüber hinaus können Sie auch benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems festzulegen.

- [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

- [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md)

- [Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/upload-vhd.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen. Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die für Ihre Organisation erforderliche Sicherheitskonfiguration der Azure-Ressourcen zu verwalten. 
 
 
 
- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)
 
- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
 
- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für den Betrieb systemsicherer Konfigurationen von Azure Machine Learning Service verantwortlich.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Befolgen Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Empfehlungen aus Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Azure-Computeressourcen.  Darüber hinaus können Sie auch Azure Resource Manager-Vorlagen, benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems zu verwalten.   Die Vorlagen für virtuelle Computer von Microsoft in Kombination mit Azure Automation State Configuration können als Hilfe beim Erfüllen und Verwalten der Sicherheitsanforderungen dienen. 

Beachten Sie, dass von Microsoft in Azure Marketplace veröffentlichte VM-Images von Microsoft verwaltet und gepflegt werden. 

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Erstellen eines virtuellen Azure-Computers mithilfe einer ARM-Vorlage](../virtual-machines/windows/ps-template.md)

- [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md)

- [Erstellen eines virtuellen Windows-Computers im Azure-Portal](../virtual-machines/windows/quick-create-portal.md)

- [Herunterladen einer Vorlage für einen virtuellen Computer](../virtual-machines/windows/download-template.md)

- [Beispielskript zum Hochladen einer generalisierten virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie für Machine Learning-Ressourcen oder für verwandte Ressourcen benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

Azure Machine Learning unterstützt Git-Repositorys zur Nachverfolgung der Arbeit – Sie können Repositorys direkt für Ihr freigegebenes Arbeitsbereichsdateisystem klonen, Git auf Ihrer lokalen Arbeitsstation verwenden und als Teil Ihrer Machine Learning-Umgebung sicherstellen, dass sichere Konfigurationen für Coderessourcen gelten.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um sicherzustellen, dass nur autorisierte Benutzer auf Ihre benutzerdefinierten Images zugreifen können. Mit einer Azure Shared Image Gallery können Sie Ihre Images für unterschiedliche Benutzer, Dienstprinzipale oder AD-Gruppen in Ihrer Organisation freigeben. Speichern Sie Containerimages in Azure Container Registry, und stellen Sie mithilfe von Azure RBAC sicher, dass nur autorisierte Benutzer über Zugriff verfügen.

- [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Grundlegendes zu Azure RBAC für Container Registry](../container-registry/container-registry-roles.md)

- [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Übersicht über den Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md)

- [Verwenden von Azure RBAC für die Kubernetes-Autorisierung](../aks/manage-azure-rbac.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliasse im Namespace „Microsoft.MachineLearning“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Verwenden von Aliasen](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für die Bereitstellung einer sicheren Konfiguration von Azure Machine Learning Service verantwortlich.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, verwenden Sie Azure Automation State Configuration für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist. 

- [Aktivieren von Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen. Nutzen Sie Azure Policy auch für Warnungen und Überprüfungen in Bezug auf Azure-Ressourcenkonfigurationen.
 
 
 
- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Wenn sich die Computeressource im Besitz von Microsoft befindet, dann ist Microsoft für die Überwachung einer automatisierten sicheren Konfiguration von Azure Machine Learning Service verantwortlich.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Compute &amp; Apps von Azure Security Center, und befolgen Sie die Empfehlungen für VMs und Server sowie Container.

- [Grundlegendes zu Azure Security Center-Containerempfehlungen](../security-center/container-security.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie verwaltete Identitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen.

Azure Machine Learning unterstützt die Verschlüsselung von Datenspeichern mit vom Kunden verwalteten Schlüsseln. Sie müssen die Schlüsselrotation und den Widerruf von Sicherheits- und Complianceanforderungen pro Organisation verwalten. 

Verwenden Sie Azure Key Vault anstelle von Klartext in Ihren Trainingsskripts, um Geheimnisse sicher an Remoteausführungen zu übergeben.

- [Kundenseitig verwaltete Schlüssel in Azure Machine Learning](concept-enterprise-security.md#azure-blob-storage)

- [Verwenden von Geheimnissen als Anmeldeinformationen für die Authentifizierung in Azure Machine Learning-Trainingsausführungen](how-to-use-secrets-in-runs.md)

- [Verwenden von verwalteten Identitäten für Azure-Ressourcen](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

- [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Azure Machine Learning unterstützt sowohl integrierte Rollen als auch die Möglichkeit, benutzerdefinierte Rollen zu erstellen. Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

 
- [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md)

- [Konfigurieren verwalteter Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Machine Learning) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Verwenden Sie für Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Microsoft Antimalware für Azure, um Ihre Ressourcen fortlaufend zu überwachen und zu schützen. Verwenden Sie für Linux eine Antischadsoftware von Drittanbietern. Mithilfe der Bedrohungserkennung für Datendienste in Azure Security Center können Sie auch Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde.

- [Konfigurieren von Microsoft Antimalware für Azure](../security/fundamentals/antimalware.md)

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Machine Learning) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Es liegt in Ihrer Verantwortung, alle Inhalte vorab zu überprüfen, die auf computefremde Azure-Ressourcen hochgeladen werden. Microsoft kann nicht auf Kundendaten zugreifen und kann daher keine Antischadsoftwarescans von Kundeninhalten in Ihrem Namen durchführen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host aktiviert und wird für die zugrunde liegenden Azure-Dienste (z. B. Azure Machine Learning) verwaltet, aber nicht für Kundeninhalte ausgeführt.

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeressourcen und sogar Ihre eigenen Computeressourcen. Befolgen Sie für alle Computeressourcen, die sich im Besitz Ihrer Organisation befinden, Empfehlungen in Compute &amp; Apps von Azure Security Center, um sicherzustellen, dass alle Endpunkte mit den neuesten Signaturen auf dem neuesten Stand sind. Verwenden Sie für Linux eine Antischadsoftware von Drittanbietern.

- [Bereitstellen von Microsoft Antimalware für Azure](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Datenwiederherstellungsverwaltung in Machine Learning Service erfolgt über Datenverwaltung in verbundenen Datenspeichern. Stellen Sie sicher, dass Sie die Datenwiederherstellungsrichtlinien für verbundene Speicher befolgen, um Daten gemäß den Richtlinien der Kundenorganisation zu sichern.

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Datensicherung in Machine Learning Service erfolgt über Datenverwaltung in verbundenen Datenspeichern. Aktivieren Sie Azure Backup für VMs, und konfigurieren Sie die gewünschte Häufigkeit und Beibehaltungsdauer. Sichern Sie kundenseitig verwaltete Schlüsseln in Azure Key Vault.

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../backup/backup-azure-restore-files-from-vm.md)
- [Wiederherstellen von Key Vault-Schlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Datensicherungsüberprüfung in Machine Learning Service erfolgt über Datenverwaltung in verbundenen Datenspeichern. Führen Sie die Wiederherstellung von Inhalten in Azure Backup regelmäßig aus. Stellen Sie sicher, dass gesicherte kundenseitig verwaltete Schlüssel wiederhergestellt werden können.

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase. Verwenden Sie rollenbasierte Zugriffssteuerung in Azure, um Sicherungen und kundenseitig verwaltete Schlüssel zu schützen. 

Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen. Wenn Azure Storage zum Speichern von Sicherungen verwendet wird, aktivieren Sie vorläufiges Löschen, um Ihre Daten zu speichern und wiederherzustellen, wenn Blobs oder Blobmomentaufnahmen gelöscht werden.
 
 
- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Vorläufiges Löschen für Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

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