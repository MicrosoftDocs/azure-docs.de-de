---
title: Azure-Sicherheitsbaseline für Azure Data Factory
description: Azure-Sicherheitsbaseline für Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e48b1357b8576baca0e8defca94f4c5248c6f74e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461291"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azure-Sicherheitsbaseline für Azure Data Factory

Die Azure-Sicherheitsbaseline für Azure Data Factory enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Wenn Sie eine Azure-SSIS Integration Runtime (IR) erstellen, können Sie diese mit einem virtuellen Netzwerk verknüpfen. Dadurch kann Azure Data Factory bestimmte Netzwerkressourcen wie Netzwerksicherheitsgruppen (NSGs) und Lastenausgleiche erstellen. Sie können auch eine eigene statische öffentliche IP-Adresse bereitstellen oder Azure Data Factory eine für Sie erstellen lassen. Bei NSGs, die automatisch von Azure Data Factory erstellt werden, ist Port 3389 standardmäßig für sämtlichen Datenverkehr geöffnet. Schließen Sie diesen, um sicherzustellen, dass nur Ihre Administratoren Zugriff haben.

Die selbstgehostete IRs können auf einem lokalen Computer oder einer Azure-VM in einem virtuellen Netzwerk bereitgestellt werden. Stellen Sie sicher, dass für die Subnetzbereitstellung des virtuellen Netzwerks eine NSG konfiguriert ist, die nur den Administratorzugriff zulässt. Die Azure-SSIS IR lässt standardmäßig keinen ausgehenden Datenverkehr über Port 3389 zu. Hierfür ist eine Windows Firewall-Regel für jeden IR-Knoten konfiguriert. Sie können Ihre Ressourcen für das virtuelle Netzwerk schützen, indem Sie dem Subnetz eine NSG zuordnen und strikte Regeln festlegen.

Wenn Private Link verfügbar ist, können Sie private Endpunkte verwenden, um mit Ihrer Azure Data Factory-Pipeline verknüpfte Ressourcen wie Azure SQL Server zu schützen. Mit Private Link wird der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst über das Microsoft-Backbonenetzwerk übertragen und dadurch vom öffentlichen Internet isoliert.

* [Erstellen einer Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [Erstellen und Konfigurieren einer selbstgehosteten IR](./create-self-hosted-integration-runtime.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Grundlegendes zu Azure Private Link](../private-link/private-link-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und setzen Sie die Netzwerkschutzempfehlungen für das virtuelle Netzwerk bzw. die Netzwerksicherheitsgruppe um, das oder die Ihrer Integration Runtime-Bereitstellung zugeordnet ist.

Sie können zudem Flussprotokolle für Netzwerksicherheitsgruppen aktivieren, damit die NSG Ihre Integration Runtime-Bereitstellung schützt und Protokolle zum Datenverkehrsauditing an ein Azure Storage-Konto sendet.

Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie DDoS Protection Standard in den virtuellen Netzwerken, die Ihrer Integration Runtime-Bereitstellung zugeordnet sind, um diese vor verteilten Denial-of-Service-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

* [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Sie können Flussprotokolle für Netzwerksicherheitsgruppen aktivieren, damit die NSG Ihre Integration Runtime-Bereitstellung schützt und Protokolle zum Datenverkehrsauditing an ein Azure Storage-Konto sendet.

Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wenn Sie den ausgehenden Datenverkehr von Azure-SSIS IR überprüfen möchten, können Sie den von Azure-SSIS IR initiierten Datenverkehr über Azure ExpressRoute-Tunnelerzwingung an eine lokale Firewallappliance oder an ein virtuelles Netzwerkgerät vom Azure Marketplace weiterleiten, das IDS/IPS-Funktionen unterstützt. Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden.

* [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie VNET-Diensttags, um Netzwerkzugriffssteuerungen für die Netzwerksicherheitsgruppe oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „DataFactoryManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie eingehenden Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

* [Grundlegendes zu Diensttags und deren Verwendung](../virtual-network/service-tags-overview.md)

* [Grundlegendes zu Azure Data Factory-spezifischen Diensttags](./join-azure-ssis-integration-runtime-virtual-network.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkeinstellungen und Netzwerkressourcen, die Ihren Azure Data Factory-Instanzen zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.DataFactory“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Data Factory-Instanzen zu erstellen. Sie können auch vordefinierte Richtliniendefinitionen verwenden, die sich auf das Netzwerk oder Ihre Azure Data Factory-Instanzen beziehen, z. B.:
- DDoS Protection Standard sollte aktiviert sein.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network) 

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Ressourcen im Zusammenhang mit der Netzwerksicherheit und dem Datenverkehrsfluss für Ihre Azure Data Factory-Instanzen, um Metadaten und eine logische Organisation bereitzustellen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen im Zusammenhang mit Ihren Azure Data Factory-Instanzen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle für Zeitstempel in den Protokollen, die für Azure-Ressourcen wie Azure Data Factory verwendet wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor, um die von Azure Data Factory generierten Sicherheitsdaten zu aggregieren. In Azure Monitor können Sie den Log Analytics-Arbeitsbereich abfragen, der für den Empfang Ihrer Azure Data Factory-Aktivitätsprotokolle konfiguriert ist. Verwenden Sie Azure-Speicherkonten, um Protokolle langfristig zu speichern bzw. zu archivieren, oder Event Hubs, um Daten in andere Systeme zu exportieren.

Alternativ können Sie Azure Sentinel oder ein SIEM-System eines Drittanbieters aktivieren und einrichten. Sie können Azure Data Factory zudem mit Git integrieren, um verschiedene Vorteile bei der Quellcodeverwaltung zu nutzen, z. B. das Nachverfolgen und Auditing von Änderungen und das Rückgängigmachen von Änderungen, die zu Fehlern führen.

* [Erstellen von Diagnoseeinstellungen im Azure-Portal](../azure-monitor/platform/diagnostic-settings.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Quellcodeverwaltung in Azure Data Factory](./source-control.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure Storage-Konto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Verwenden Sie Diagnoseeinstellungen, um Diagnoseprotokolle für Nicht-Compute-Ressourcen in Azure Data Factory zu konfigurieren, z. B. Metriken und Pipelineausführungsdaten. Azure Data Factory speichert Pipelineausführungsdaten 45 Tage lang. Wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten, speichern Sie die Diagnoseprotokolle in einem Speicherkonto zum Auditing oder zur manuellen Überprüfung, und geben Sie die Aufbewahrungsdauer in Tagen an. Sie können die Protokolle auch an Azure Event Hubs streamen oder zur Analyse an einen Log Analytics-Arbeitsbereich senden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Grundlegendes zu Azure Data Factory-Diagnoseprotokollen](./monitor-using-azure-monitor.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie mit Azure Monitor Daten von der VM sammeln. Wenn Sie die Log Analytics-VM-Erweiterung installieren, kann Azure Monitor Daten von Ihren virtuellen Azure-Computern sammeln. Azure Security Center kann dann die Protokolle für sicherheitsrelevante Ereignisse für VMs überwachen. Aufgrund der Datenmenge, die das Sicherheitsereignisprotokoll generiert, wird dieses standardmäßig nicht gespeichert.

Wenn Ihre Organisation die Daten des Sicherheitsereignisprotokolls aufbewahren möchte, kann es in einer Datensammlungsebene gespeichert werden. Dann kann es in Log Analytics abgefragt werden.

* [Sammeln von Daten von einem virtuellen Azure-Computer in Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Aktivieren der Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure Data Factory. Wenn Sie Protokolle in einem Log Analytics-Arbeitsbereich speichern, legen Sie den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancebestimmungen Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

* [Aktivieren der Diagnoseprotokollierung in Azure Data Factory](./monitor-using-azure-monitor.md)

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für Azure Data Factory, und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich. Analysieren und überwachen Sie Protokolle mit Log Analytics auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Stellen Sie sicher, dass Sie die Diagnoseeinstellungen auch für alle Datenspeicher aktivieren, die in Zusammenhang mit Ihren Azure Data Factory-Bereitstellungen stehen. Eine Anleitung zum Aktivieren der Diagnoseeinstellungen finden Sie in der Sicherheitsbaseline des jeweiligen Diensts.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, sollten Sie auch die Diagnoseeinstellungen für die VM aktivieren.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Log Analytics-Schema](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Sammeln von Daten von einem virtuellen Azure-Computer mit Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Sie können Warnungen zu unterstützten Metriken in Data Factory einstellen, indem Sie in Azure Monitor zum Abschnitt „Warnungen und Metriken“ navigieren.

Konfigurieren Sie die Diagnoseeinstellungen für Azure Data Factory, und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie in Ihrem Log Analytics-Arbeitsbereich Warnungen, die ausgelöst werden sollen, wenn vordefinierte Bedingungen eintreten. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

Stellen Sie außerdem sicher, dass Sie die Diagnoseeinstellungen für Dienste aktivieren, die mit Ihren Datenspeichern in Zusammenhang stehen. Anleitungen hierzu finden Sie in der Sicherheitsbaseline des jeweiligen Diensts.

* [Warnungen in Azure Data Factory](./monitor-visually.md#alerts)

* [Alle unterstützten Metriken](../azure-monitor/platform/metrics-supported.md)

* [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie Microsoft Antimalware für Azure Cloud Services und Azure Virtual Machines verwenden und Ihre VMs so konfigurieren, dass Ereignisse in einem Azure Storage-Konto protokolliert werden. Konfigurieren Sie einen Log Analytics-Arbeitsbereich darauf, Ereignisse aus den Speicherkonten zu erfassen und ggf. Warnungen auszulösen. Befolgen Sie die Empfehlungen in Azure Security Center: Compute und Apps

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Aktivieren der Überwachung auf Gastebene für Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend, da Azure Data Factory keine Protokolle zu DNS verarbeitet oder erzeugt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie auch die Überwachungsprotokolle für die Befehlszeile aktivieren. Azure Security Center generiert Protokolle für sicherheitsrelevante Ereignisse für Azure-VMs. Microsoft Monitoring Agent wird von Security Center auf allen unterstützten Azure-VMs sowie auf allen neu erstellten VMs bereitgestellt, wenn die automatische Bereitstellung aktiviert ist. Sie können den Agent alternativ manuell installieren. Der Agent liefert das Prozesserstellungsereignis 4688 und das Feld „CommandLine“ im Ereignis 4688. Auf der VM erstellte neue Prozesse werden von EventLog aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht.

* [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Stellen Sie in Azure Data Factory sicher, dass Sie den Benutzerzugriff in regelmäßigen Abständen nachverfolgen und abstimmen. Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ oder ein Administrator des Azure-Abonnements sein.

Auf Mandantenebene umfasst Azure Active Directory (Azure AD) zudem integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD-PowerShell-Modul, um Ad-hoc-Abfragen zu stellen und so Konten zu ermitteln, die Mitglieder bei Administratorgruppen sind und Administratorzugriff auf die Steuerungsebene Ihrer Azure Data Factory-Instanzen haben.

Obwohl Azure AD zum Verwalten des Benutzerzugriffs empfohlen wird, sollten Sie bedenken, dass auf der Azure-VM, auf der Ihre Integration Runtime ausgeführt wird, auch lokale Konten vorhanden sein können. Lokale Konten und Domänenkonten sollten mit so wenig Aufwand wie möglich überprüft und verwaltet werden. Zudem wird empfohlen, Privileged Identity Management auf das Just-in-Time-Feature zu überprüfen, um die Verfügbarkeit von Administratorberechtigungen zu verringern.

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Rollen und Berechtigungen für Azure Data Factory)

* [Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Informationen zu lokalen Konten](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Data Factory verwendet Azure Active Directory (Azure AD), um Zugriff auf das Azure-Portal und die Azure Data Factory-Konsole zu gewähren. Azure AD verfügt nicht über Standardkennwörter. Es liegt allerdings in Ihrer Verantwortung, Standardkennwörter für benutzerdefinierte Anwendungen oder Drittanbieteranwendungen zu ändern oder nicht zuzulassen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Etablieren Sie Standardverfahren für die Verwendung dedizierter Administratorkonten für den Zugriff auf die Azure-Steuerungsebene (das Azure-Portal) und die Azure Data Factory-Konsole. Verwenden Sie die Identitäts- und Zugriffsverwaltung von Azure Security Center, um die Anzahl der Administratorkonten in Azure AD zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können auch die Administratorkonten für diese mit Azure AD Privileged Identity Management konfiguriert werden. Azure AD Privileged Identity Management bietet die Just-in-Time-Rechteerweiterung, die mehrstufige Authentifizierung und Delegierungsoptionen, durch die Berechtigungen nur für bestimmte Zeitrahmen verfügbar sind und durch eine zweite Person genehmigt werden müssen.

* [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

* [Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Rollen und Berechtigungen für Azure Data Factory)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie eine Azure-App-Registrierung (Dienstprinzipal), um ein Token abzurufen, das von der Anwendung oder Funktion für den Zugriff auf und die Interaktion mit Recovery Services-Tresoren verwendet werden kann.

* [Aufrufen von Azure-REST-APIs mit Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrieren Ihrer Clientanwendung mit Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Recovery Services](/rest/api/recoveryservices/)

* [Informationen zur REST-API für Azure Data Factory](/rest/api/datafactory/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie Ihre VM zusätzlich in Azure Sentinel integrieren. Microsoft Azure Sentinel ist eine skalierbare, cloudnative Lösung für Security Information & Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Azure Sentinel bietet intelligente Sicherheits- und Bedrohungsanalysen für das ganze Unternehmen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit.

* [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

* [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Eine Data Factory kann einer verwalteten Identität für Azure-Ressourcen zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese verwaltete Identität für die Azure SQL-Datenbank-Authentifizierung verwenden. Die angegebene Factory kann mithilfe dieser Identität auf Daten zugreifen und Daten aus der oder in die Datenbank kopieren.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie verwaltete Identitäten für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Der Code, der auf einer VM ausgeführt wird, kann eine verwaltete Identität zum Anfordern von Zugriffstokens für Dienste verwenden, die die Azure AD-Authentifizierung unterstützen.

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md)

* [Kopieren und Transformieren von Daten in Azure SQL-Datenbank mithilfe von Azure Data Factory](./connector-azure-sql-database.md)

* [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL-Datenbank](../azure-sql/database/authentication-aad-configure.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (AAD) enthält Protokolle zum Ermitteln veralteter Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, müssen Sie die lokalen Sicherheitsgruppen und Benutzer überprüfen, um sicherzustellen, dass keine unerwarteten Konten vorhanden sind, die das System gefährden könnten.

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

* [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten (Azure Active Directory) und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist. Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie diese VM in Azure Sentinel integrieren. Microsoft Azure Sentinel ist eine skalierbare, cloudnative Lösung für Security Information & Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Azure Sentinel bietet intelligente Sicherheits- und Bedrohungsanalysen für das ganze Unternehmen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](../event-hubs/authorize-access-azure-active-directory.md)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Azure Data Factory-Ressourcen wie SQL-Datenbank und Azure Virtual Machines. Falls abweichendes Anmeldeverhalten bei der Kontoanmeldung auf Steuerungsebene (dem Azure-Portal) auftritt, verwenden Sie Azure AD Identity Protection und Risikoerkennungsfeatures, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, wird per Azure-Kunden-Lockbox eine Benutzeroberfläche bereitgestellt, auf der Kunden Anforderungen des Zugriffs auf Kundendaten prüfen und dann genehmigen oder ablehnen können. Azure Lockbox ist zwar nicht für Azure Data Factory verfügbar, unterstützt jedoch Azure SQL-Datenbank und Azure Virtual Machines.

* [Grundlegendes zu Kunden-Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Verwenden Sie die Azure SQL-Datenbank-Funktion zur Datenermittlung und -klassifizierung. Die Datenermittlung und -klassifizierung verfügt über erweiterte Funktionen für Azure SQL-Datenbank zum Ermitteln, Klassifizieren, Bezeichnen und Schützen der vertraulichen Daten in Ihren Datenbanken.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Verwenden der Datenermittlung und -klassifizierung für Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Integration Runtimes müssen durch ein virtuelles Netzwerk (VNET) bzw. Subnetz getrennt und entsprechend mit Tags versehen werden.

 Sie können auch private Endpunkte verwenden, um eine Netzwerkisolation zu erzielen. Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst effektiv in Ihr VNet ein.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Grundlegendes zu Private Link](../private-link/private-endpoint-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Kennzeichnen Sie die Datenquellen wie Azure SQL-Datenbank, die vertrauliche Informationen für Ihre Azure Data Factory-Bereitstellung speichern oder verarbeiten, und die zugehörigen Ressourcen mit Tags als vertraulich.

Wenn Private Link verfügbar ist, können Sie private Endpunkte verwenden, um mit Ihrer Azure Data Factory-Pipeline verknüpfte Ressourcen zu schützen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können das Risiko der Datenexfiltration auch verringern, indem Sie einen strengen Satz an Ausgangsregeln für eine Netzwerksicherheitsgruppe (NSG) konfigurieren und diese NSG Ihrem Subnetz zuordnen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Grundlegendes zu Azure Private Link](../private-link/private-link-overview.md)

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Wenn der Clouddatenspeicher HTTPS oder TLS unterstützt, erfolgen alle Datenübertragungen zwischen Datenverschiebungsdiensten in Data Factory und einem Clouddatenspeicher über einen sicheren Kanal (HTTPS oder TLS). Die verwendete TLS-Version ist 1.2.

Für alle Verbindungen mit Azure SQL-Datenbank und Azure Synapse Analytics ist eine Verschlüsselung (SSL/TLS) erforderlich, solange Daten in die und aus der Datenbank übertragen werden. Wenn Sie eine Pipeline mit JSON erstellen, fügen Sie die Verschlüsselungseigenschaft hinzu, und legen Sie die Eigenschaft in der Verbindungszeichenfolge auf „true“ fest. Für Azure Storage können Sie „HTTPS“ in der Verbindungszeichenfolge verwenden.

* [Grundlegendes zur Verschlüsselung während der Übertragung in Azure Data Factory](./data-movement-security-considerations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Wenn Sie Azure Data Factory verwenden, um Ihre Azure SQL-Datenbank-Instanzen zu kopieren und zu transformieren, können Sie die Features zur Datenermittlung und -klassifizierung von Azure SQL-Datenbank nutzen. Die Datenermittlung und -klassifizierung verfügt über erweiterte Funktionen für Azure SQL-Datenbank zum Ermitteln, Klassifizieren, Bezeichnen und Schützen der vertraulichen Daten in Ihren Datenbanken.

Die Features zur Datenermittlung und -klassifizierung sind noch nicht für andere Azure-Dienste verfügbar.

* [Verwenden der Datenermittlung und -klassifizierung für Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf die Steuerungsebene (Azure-Portal) von Azure Data Factory zu steuern.

Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ oder ein Administrator des Azure-Abonnements sein.

Weitere Informationen zu Azure RBAC für Ihre Data Factory-Datenquellen wie Azure SQL-Datenbank finden Sie in der Sicherheitsbaseline für den jeweiligen Dienst.

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Rollen und Berechtigungen für Azure Data Factory)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Es wird empfohlen, den Datenverschlüsselungsmechanismus für alle Datenspeicher zu aktivieren, die in Zusammenhang mit Ihrer Azure Data Factory-Bereitstellungen stehen. Weitere Informationen zur Verschlüsselung ruhender Daten finden Sie in der Sicherheitsbaseline für den jeweiligen Dienst.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, werden virtuelle Datenträger auf Windows-VMs im Ruhezustand mithilfe serverseitiger Verschlüsselung oder Azure Disk Encryption (ADE) verschlüsselt. Azure Disk Encryption nutzt das BitLocker-Feature von Windows zum Verschlüsseln verwalteter Datenträger mit vom Kunden verwalteten Schlüsseln innerhalb der Gast-VM. Die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln bewirkt eine ADE-Verbesserung, indem Sie beliebige Betriebssystemtypen und Images für Ihre VMs verwenden können, indem Daten im Speicherdienst verschlüsselt werden.

Sie können Anmeldeinformationen oder Geheimniswerte in Azure Key Vault speichern und während der Pipelineausführung verwenden, um sie an Ihre Aktivitäten zu übergeben. Sie können Anmeldeinformationen für Datenspeicher und Computervorgänge auch in einer Azure Key Vault-Instanz speichern. Azure Data Factory ruft die Anmeldeinformationen ab, wenn eine Aktivität ausgeführt wird, die den Datenspeicher/Computevorgänge verwendet.

* [Grundlegendes zur Verschlüsselung ruhender Daten in Azure Data Factory](./data-movement-security-considerations.md)

* [Serverseitige Verschlüsselung von verwalteten Azure-Datenträgern](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption für Windows-VMs](../virtual-machines/windows/disk-encryption-overview.md)

* [Verwenden von Azure Key Vault-Geheimnissen in Pipelineaktivitäten](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Speichern von Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an Azure Data Factory und zugehörigen Ressourcen ausgegeben werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

* [Azure Storage Analytics-Protokollierung](../storage/common/storage-analytics-logging.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Wenn Sie Azure SQL-Datenbank als Datenspeicher verwenden, können Sie Advanced Data Security für Azure SQL-Datenbank aktivieren und die Azure Security Center-Empfehlungen zur Durchführung von Sicherheitsrisikobewertungen für Ihre Azure SQL Server-Instanzen befolgen.

Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, sollten Sie die Azure Security Center-Empfehlungen zur Durchführung von Sicherheitsrisikobewertungen für Ihre VMs befolgen. Verwenden Sie von Azure Security Center empfohlene Lösungen oder Drittanbieterlösungen, um Sicherheitsrisikobewertungen für Ihre VMs durchzuführen.

* [Ermitteln von Sicherheitsrisiken für die Datenbank mithilfe der SQL-Sicherheitsrisikobewertung](../azure-sql/database/sql-vulnerability-assessment.md)

* [Aktivieren von Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

* [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie die Azure-Updateverwaltung nutzen, um Updates und Patches für Ihre VMs zu verwalten. In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu patchen. Mit Tools wie System Center Updates Publisher (Updates Publisher) können Sie benutzerdefinierte Updates in Windows Server Update Services (WSUS) veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Updateverwaltung in Azure](../automation/update-management/overview.md)

* [Verwalten von Updates und Patches für Ihre Azure-VMs](../automation/update-management/manage-updates-for-vm.md)

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie eine Drittanbieterlösung für die Patchverwaltung verwenden. Sie können die Azure-Updateverwaltung verwenden, um Updates und Patches für Ihre VMs zu verwalten. In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu patchen. Mit Tools wie System Center Updates Publisher (Updates Publisher) können Sie benutzerdefinierte Updates in Windows Server Update Services (WSUS) veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

* [Updateverwaltung in Azure](../automation/update-management/overview.md)

* [Verwalten von Updates und Patches für Ihre Azure-VMs](../automation/update-management/manage-updates-for-vm.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie die Scanergebnisse in regelmäßigen Intervallen exportieren und vergleichen, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn Sie die vom Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwenden, können Sie in das Portal der ausgewählten Lösung wechseln, um den Verlauf der Scandaten anzuzeigen.

* [Grundlegendes zum integrierten Scanner für Sicherheitsrisiken für VMs](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie den nativen Scanner für Sicherheitsrisiken verwenden. Die in Azure Security Center enthaltene Überprüfung auf Sicherheitsrisiken wird unterstützt von Qualys. Die Überprüfung auf Sicherheitsrisiken von Qualys ist das führende Tool für die Echtzeiterkennung von Sicherheitsrisiken auf ihren virtuellen Azure-Computern.

Wenn von Security Center Sicherheitsrisiken identifiziert werden, werden die Ergebnisse und die zugehörigen Informationen als Empfehlungen angezeigt. Die zugehörigen Informationen umfassen Schritte zur Bereinigung, zugehörige CVEs, CVSS-Bewertungen und mehr. Sie können die identifizierten Sicherheitsrisiken für ein oder mehrere Abonnements oder für einen bestimmten virtuellen Computer anzeigen.

* [Integrierter Scanner für Sicherheitsrisiken für VMs](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.) in ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

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

**Leitfaden**: Definieren Sie genehmigte Azure-Ressourcen und genehmigte Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie die Azure-VM-Inventur nutzen, um die Datensammlung für sämtliche Software auf den VMs zu automatisieren. Mit Azure Automation haben Sie in Bezug auf die Bereitstellung, Abläufe und Außerbetriebnahme von Workloads und Ressourcen die volle Kontrolle.

Hinweis: Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Kunden müssen die Diagnose auf Gastebene aktivieren und die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzufügen, um auf das Installationsdatum und andere Informationen zugreifen zu können.

* [Einführung in Azure Automation](../automation/automation-intro.md)

* [Aktivieren der Azure-VM-Inventur](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, bietet Azure Automation eine vollständige Kontrolle über die Bereitstellung, den Betrieb und die Außerbetriebnahme von Workloads und Ressourcen. Mithilfe der Änderungsnachverfolgung können Sie sämtliche auf den VMs installierte Software ermitteln. Sie können einen eigenen Prozess implementieren oder Azure Automation State Configuration verwenden, um nicht autorisierte Software zu entfernen.

* [Einführung in Azure Automation](../automation/automation-intro.md)

* [Nachverfolgen von Änderungen in Ihrer Umgebung mit der Änderungsnachverfolgung](../automation/change-tracking/overview.md)

* [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie die adaptive Anwendungssteuerung in Azure Security Center verwenden, um sicherzustellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software auf VMs blockiert wird.

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Die adaptive Anwendungssteuerung ist eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Computern mit oder ohne Azure ausgeführt werden können (Windows und Linux). Implementieren Sie eine Drittanbieterlösung, wenn diese die Anforderung Ihrer Organisation nicht erfüllt.

Beachten Sie, dass das nur gilt, wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird.

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie abhängig vom Skripttyp betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern verwenden, um die Möglichkeit der Benutzer zur Skriptausführung innerhalb von Azure-Computeressourcen einzuschränken. Sie können auch die adaptiven Anwendungssteuerungen in Azure Security Center nutzen, um sicherzustellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

* [Steuern der PowerShell-Skriptausführung in Windows-Umgebungen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Anwendungen mit hohem Risiko, die in Ihrer Azure-Umgebung bereitgestellt werden, können z. B. mithilfe von virtuellen Netzwerken, Subnetzen, Abonnements, Verwaltungsgruppen isoliert und durch eine Azure Firewall-Instanz, eine Web Application Firewall (WAF) oder eine Netzwerksicherheitsgruppe (NSG) ausreichend geschützt werden.

* [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-machines/network-overview.md)

* [Was ist Azure Firewall?](../firewall/overview.md)

* [Was ist Azure Web Application Firewall?](../web-application-firewall/overview.md)

* [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)

* [Was ist Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

* [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

* [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure Data Factory mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.DataFactory“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Data Factory-Instanzen zu erstellen.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie die Azure Security Center-Empfehlung „Beheben von Sicherheitsrisiken in Sicherheitskonfigurationen auf virtuellen Computern“ anwenden, um Sicherheitskonfigurationen für alle Computeressourcen zu verwalten.

* [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md)

* [Umsetzen von Azure Security Center-Empfehlungen](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

* [Erstellen von Azure Resource Manager-Vorlagen](../virtual-machines/windows/ps-template.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, gibt es verschiedene Optionen für die Verwaltung einer sicheren Konfiguration für bereitzustellende VMs:
- Azure Resource Manager-Vorlagen: Diese JSON-basierten Dateien werden zum Bereitstellen einer VM über das Azure-Portal verwendet, und eine benutzerdefinierte Vorlage muss verwaltet werden. Microsoft führt die Verwaltung der Basisvorlagen durch.
- Benutzerdefinierte virtuelle Festplatte (VHD): In einigen Fällen kann es erforderlich sein, benutzerdefinierte VHD-Dateien zu verwenden, z. B. bei komplexen Umgebungen, die nicht anders verwaltet werden können. – Azure Automation State Configuration: Nachdem das Basisbetriebssystem bereitgestellt wurde, kann es verwendet werden, um die Einstellungen präziser zu steuern und über das Automatisierungsframework zu erzwingen.

In den meisten Fällen sind die Microsoft-Basisvorlagen in Kombination mit Azure Automation Desired State Configuration beim Erfüllen und Verwalten der Sicherheitsanforderungen nützlich.

* [Herunterladen einer Vorlage für einen virtuellen Computer](../virtual-machines/windows/download-template.md)

* [Erstellen von Azure Resource Manager-Vorlagen](../virtual-machines/windows/ps-template.md)

* [Hochladen einer benutzerdefinierten VM-VHD in Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Wenn benutzerdefinierte Images verwendet werden, sollten Sie durch die Nutzung der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) sicherstellen, dass nur autorisierte Benutzer auf die Images zugreifen können. Speichern Sie Containerimages in Azure Container Registry, und verwenden Sie Azure RBAC, um sicherzustellen, dass nur autorisierte Benutzer auf die Images zugreifen können.

Mit der Data Factory-Rolle „Mitwirkender“ können Data Factorys und untergeordnete Ressourcen erstellt und verwaltet werden.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Grundlegendes zu Azure RBAC für Container Registry](../container-registry/container-registry-roles.md)

* [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Rollen und Berechtigungen für Azure Data Factory)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.DataFactory“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Diese Empfehlung gilt, wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird. Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](../automation/automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.DataFactory“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Auswirkungen [Audit], [Deny] und [DeployIfNotExists], um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Diese Empfehlung gilt, wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird. Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](../automation/automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

Sie können Anmeldeinformationen oder Geheimniswerte auch in Azure Key Vault speichern und während der Pipelineausführung verwenden, um sie an Ihre Aktivitäten zu übergeben. Stellen Sie sicher, dass das vorläufige Löschen aktiviert ist.

* [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Erstellen einer Key Vault-Instanz](../key-vault/secrets/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

* [Verwenden von Azure Key Vault-Geheimnissen in Pipelineaktivitäten](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Vorläufiges Löschen in Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Wenn Sie eine Data Factory erstellen, kann beim Erstellen der Factory auch eine verwaltete Identität erstellt werden. Die verwaltete Identität ist eine verwaltete Anwendung, die in Azure Active Directory registriert ist und diese spezifische Data Factory darstellt.

* [Verwaltete Identität für Azure Data Factory](./data-factory-service-identity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie Microsoft Antimalware für Azure-VMs unter Windows verwenden, um Ihre Ressourcen kontinuierlich zu überwachen und zu schützen.

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird jedoch nicht für Ihre Inhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage usw.

Mithilfe der Bedrohungserkennung für Datendienste in Azure Security Center können Sie Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde.

* [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Grundlegendes zur Bedrohungserkennung für Datendienste in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Mit einer bereitgestellten Microsoft Antimalware-Instanz werden standardmäßig die neuesten Signaturupdates, Plattformupdates und Engine-Updates automatisch installiert. Befolgen Sie die Empfehlungen in Azure Security Center: „Compute &amp; Apps“, um sicherzustellen, dass alle Endpunkte mit den neuesten Signaturen auf dem aktuellen Stand sind. Das Windows-Betriebssystem kann durch zusätzliche Sicherheitsmaßnahmen noch besser geschützt werden, indem das Risiko durch Viren oder auf Schadsoftware basierende Angriffe mit dem Microsoft Defender Advanced Threat Protection-Dienst begrenzt wird, der mit Azure Security Center integriert ist.

* [Bereitstellen von Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie Azure Backup aktivieren und die VM sowie die gewünschte Häufigkeit und den Aufbewahrungszeitraum für automatische Sicherungen konfigurieren.

Empfehlungen zum Ausführen regulärer, automatisierter Sicherungen für Ihre Datenspeicher finden Sie in der Sicherheitsbaseline des jeweiligen Diensts.

* [Übersicht über die Sicherung von Azure-VMs](../backup/backup-azure-vms-introduction.md)

* [Sichern einer Azure-VM über die VM-Einstellungen](../backup/backup-azure-vms-first-look-arm.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, können Sie Azure Backup aktivieren, um Azure-Ziel-VMs sowie die gewünschte Häufigkeit und den Aufbewahrungszeitraum festzulegen. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

Empfehlungen zum Ausführen regulärer, automatisierter Sicherungen für Ihre Datenspeicher finden Sie in der Sicherheitsbaseline des jeweiligen Diensts.

* [Übersicht über die Sicherung von Azure-VMs](../backup/backup-azure-vms-introduction.md)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird, sollten Sie dafür sorgen, dass die Daten in Azure Backup regelmäßig wiederhergestellt werden können. Testen Sie die Wiederherstellung von Inhalten ggf. in einem isolierten VLAN. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln regelmäßig.

Informationen zum Überprüfen von Sicherungen für Ihre Datenspeicher finden Sie in der Sicherheitsbaseline des jeweiligen Diensts.

* [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../backup/backup-azure-restore-files-from-vm.md)

* [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Wenn Ihre Integration Runtime auf einer Azure-VM ausgeführt wird und Sie diese mit Azure Backup sichern, wird Ihre VM im Ruhezustand mit der Speicherdienstverschlüsselung (SSE) verschlüsselt. Azure Backup kann auch virtuelle Azure-Computer sichern, die mit Azure Disk Encryption verschlüsselt wurden. Azure Disk Encryption kann in BitLocker-Verschlüsselungsschlüssel (BEK) integriert werden, die in einem Schlüsseltresor als Geheimnisse geschützt werden. Azure Disk Encryption kann auch in Azure Key Vault-Schlüssel für die Schlüsselverschlüsselung (Key Encryption Keys, KEK) integriert werden. Aktivieren Sie das vorläufige Löschen in Key Vault, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

* [Vorläufiges Löschen für VMs](../backup/backup-azure-security-feature-cloud.md)

* [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](../key-vault/general/soft-delete-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Sentinel zu streamen.

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

**Leitfaden**: 

* [Befolgen Sie die Einsatzregeln von Microsoft, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).