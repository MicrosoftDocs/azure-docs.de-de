---
title: Azure-Sicherheitsbaseline für VM-Skalierungsgruppen
description: Die Sicherheitsbaseline für VM-Skalierungsgruppen enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Azure-Sicherheitsvergleichstest angegeben sind.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5c00d08fcaeed8eba5b9732c9196b9abddc32b36
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500715"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Azure-Sicherheitsbaseline für VM-Skalierungsgruppen

Die Azure-Sicherheitsbaseline für VM-Skalierungsgruppen enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Beim Erstellen eines virtuellen Azure-Computers (Azure-VM) müssen Sie ein virtuelles Netzwerk erstellen oder ein vorhandenes verwenden und die VM mit einem Subnetz konfigurieren. Stellen Sie sicher, dass auf alle bereitgestellten Subnetze eine Netzwerksicherheitsgruppe mit Netzwerkzugriffssteuerungen angewendet wurde, die für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind.

Wenn Sie einen bestimmten Anwendungsfall für eine zentralisierte Firewall haben, erfüllt auch Azure Firewall diese Anforderungen.

* [Netzwerk für Azure-VM-Skalierungsgruppen](./virtual-machine-scale-sets-networking.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Verwenden Sie Azure Security Center zum Identifizieren und Befolgen der Empfehlungen für den Netzwerkschutz, um Ihre Azure-VM-Ressourcen in Azure zu schützen. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle an ein Speicherkonto, um den Datenverkehr an die VM auf ungewöhnliche Aktivitäten zu überwachen.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Wenn Sie Ihre VM-Skalierungsgruppe (Virtual Machine Scale Set, VMSS) zum Hosten von Webanwendungen verwenden, beschränken Sie den Netzwerkdatenverkehr, die Ports und die Protokolle für die Kommunikation mithilfe einer Netzwerksicherheitsgruppe (NSG) im VMSS-Subnetz. Befolgen Sie beim Konfigurieren Ihrer NSGs einen Netzwerkansatz für geringste Berechtigungen, sodass nur der erforderliche Datenverkehr für Ihre Anwendung zugelassen wird.

Sie können auch Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereitstellen, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Aktivieren Sie die Diagnoseeinstellung für WAF, und erfassen Sie Protokolle in ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich.

* [Netzwerk für Azure-VM-Skalierungsgruppen](./virtual-machine-scale-sets-networking.md)

* [Erstellen eines Anwendungsgateways mit einer Web Application Firewall über das Azure-Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Aktivieren Sie den DDoS-Standardschutz (Distributed Denial of Service) in den virtuellen Netzwerken, um sie vor DDoS-Angriffen zu schützen. Mit der integrierten Threat Intelligence in Azure Security Center können Sie die Kommunikation mit bekannten schädlichen IP-Adressen überwachen. Konfigurieren Sie Azure Firewall in allen Segmenten des virtuellen Netzwerks mit aktivierter Threat Intelligence, und konfigurieren Sie „Warnen und ablehnen“ für schädlichen Netzwerkdatenverkehr.

Sie können mit dem Just-in-Time-Netzwerkzugriff in Azure Security Center die Verfügbarkeit von Windows-VMs auf genehmigte IP-Adressen und einen begrenzten Zeitraum einschränken. Verwenden Sie außerdem die adaptive Netzwerkhärtung in Azure Security Center, um NSG-Konfigurationen zu empfehlen, die Ports und Quell-IP-Adressen basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken.

* [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

* [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

* [Grundlegendes zur Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../security-center/security-center-just-in-time.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Sie können die NSG-Flowprotokolle in einem Speicherkonto aufzeichnen, um Flowdatensätze für Ihre virtuellen Azure-Computer zu generieren. Beim Untersuchen anomaler Aktivitäten können Sie Network Watcher-Paketerfassung aktivieren, damit Netzwerkdatenverkehr auf ungewöhnliche und unerwartete Aktivitäten überprüft werden kann.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Indem Sie die von Network Watcher bereitgestellten Paketerfassungen mit einem Open-Source-IDS-Tool kombinieren, können Sie die Erkennung von Netzwerkangriffen für einen großen Bereich von Bedrohungen durchführen. Sie können nach Bedarf auch Azure Firewall auf den Segmenten des virtuellen Netzwerks mit aktivierter Threat Intelligence und mit „Warnen und ablehnen“ für schädlichen Netzwerkdatenverkehr konfiguriert bereitstellen.

* [Durchführen der Erkennung von Netzwerkangriffen mit Network Watcher und Open-Source-Tools](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Wenn Sie VM-Skalierungsgruppen (Virtual Machine Scale Sets, VMSS) zum Hosten von Webanwendungen verwenden, können Sie Azure Application Gateway für Webanwendungen mit aktiviertem HTTPS/SSL für vertrauenswürdige Zertifikate bereitstellen. Mit Azure Application Gateway leiten Sie den Webdatenverkehr Ihrer Anwendungen an bestimmte Ressourcen weiter, indem Sie Ports Listener zuweisen, Regeln erstellen und Ressourcen zu einem Back-End-Pool wie VMSS usw. hinzufügen.

* [Bereitstellen von Application Gateway](../application-gateway/quick-create-portal.md)

* [Konfigurieren von Application Gateway für die Verwendung von HTTPS](../application-gateway/create-ssl-portal.md)

* [Erstellen einer Skalierungsgruppe, die auf ein Application Gateway verweist](./virtual-machine-scale-sets-networking.md#create-a-scale-set-that-references-an-application-gateway)

* [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](../application-gateway/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren, die für Ihre virtuellen Azure-Computer konfiguriert sind. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

* [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-VM-Skalierungsgruppen mit Azure Policy. Sie können auch mit Azure Blueprints umfangreiche Azure-Bereitstellungen vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Rollenzuweisungen und Azure Policy-Zuweisungen gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause auf Abonnements anwenden und Ressourcenverwaltung durch die Blaupausen-Versionsverwaltung ermöglichen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Informationen zu Vorlagen für VM-Skalierungsgruppen](./virtual-machine-scale-sets-mvss-start.md)

* [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Sie können Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss verwenden, die für Ihre virtuellen Windows-Computer konfiguriert sind. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen oder die Dauer für Regeln festzulegen, die Datenverkehr in ein oder aus einem Netzwerk zulassen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Änderungen an Konfigurationen von Netzwerkressourcen in Verbindung mit Ihrer Azure VM-Skalierungsgruppe. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkeinstellungen oder -ressourcen ausgelöst werden.

Verwenden Sie Azure Policy, um die Konfiguration für Netzwerkressourcen im Zusammenhang mit VM-Skalierungsgruppen zu überprüfen (und zu korrigieren).

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben jedoch die Möglichkeit, die Zeitsynchronisierungseinstellungen für Ihre VMs zu verwalten.

* [Konfigurieren der Zeitsynchronisierung für Azure Windows-Computeressourcen](../virtual-machines/windows/time-sync.md)

* [Konfigurieren der Zeitsynchronisierung für Azure Linux-Computeressourcen](../virtual-machines/linux/time-sync.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivitätsprotokolle können zum Überwachen von Vorgängen und Aktionen verwendet werden, die für Ressourcen von VM-Skalierungsgruppen ausgeführt werden. Das Aktivitätsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Ressourcen, jedoch keine Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Sie können die von Azure-Aktivitätsprotokollen oder VM-Ressourcen erzeugten Protokolldaten aktivieren und zur zentralen Sicherheitsprotokollverwaltung in Azure Sentinel oder eine SIEM-Drittanbieterlösung integrieren.

Mit Azure Security Center können Sie die Protokolle für sicherheitsrelevante Ereignisse für Azure Virtual Machines überwachen. Aufgrund der Datenmenge, die das Sicherheitsereignisprotokoll generiert, wird dieses standardmäßig nicht gespeichert.

Wenn Ihre Organisation die Daten im Sicherheitsereignisprotokoll des virtuellen Computers beibehalten möchte, können sie in einem Log Analytics-Arbeitsbereich auf der gewünschten in Azure Security Center konfigurierten Datensammlungsebene gespeichert werden.

* [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Informationen zum Überwachen von virtuellen Computern in Azure](../azure-monitor/insights/monitor-vm-azure.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle können zum Überwachen von Vorgängen und Aktionen verwendet werden, die für Ressourcen von VM-Skalierungsgruppen ausgeführt werden. Das Aktivitätsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Ressourcen, jedoch keine Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Aktivieren Sie die Sammlung von Diagnosedaten für das Gastbetriebssystem, indem Sie die Diagnoseerweiterung auf Ihren virtuellen Computern (VMs) bereitstellen. Mithilfe der Diagnoseerweiterung können Sie Diagnosedaten wie Anwendungsprotokolle oder Leistungsindikatoren von einem virtuellen Azure-Computer sammeln.

Für erweiterte Einblicke in die von der Azure VM-Skalierungsgruppe unterstützten Anwendungen und Dienste können Sie Azure Monitor für VMs und Application Insights aktivieren. Mit Application Insights können Sie Ihre Anwendung überwachen und Telemetriedaten erfassen, etwa HTTP-Anforderungen, Ausnahmen usw., damit Sie Probleme zwischen den VMs und ihrer Anwendung korrelieren können.

* [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Informationen zum Überwachen von virtuellen Computern in Azure](../azure-monitor/insights/monitor-vm-azure.md)

* [Übersicht über Application Insights](../azure-monitor/app/app-insights-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Mit Azure Security Center können Sie die Protokolle für sicherheitsrelevante Ereignisse für Azure Virtual Machines überwachen. Aufgrund der Datenmenge, die das Sicherheitsereignisprotokoll generiert, wird dieses standardmäßig nicht gespeichert.

Wenn Ihre Organisation die Daten im Sicherheitsereignisprotokoll des virtuellen Computers beibehalten möchte, können sie in einem Log Analytics-Arbeitsbereich auf der gewünschten in Azure Security Center konfigurierten Datensammlungsebene gespeichert werden.

* [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Informationen zum Überwachen von virtuellen Computern in Azure](../azure-monitor/insights/monitor-vm-azure.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Stellen Sie sicher, dass für die Speicherkonten oder Log Analytics-Arbeitsbereiche zum Speichern der Protokolle virtueller Computer der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.

* [Informationen zum Überwachen von virtuellen Computern in Azure](../azure-monitor/insights/monitor-vm-azure.md)

* [Konfigurieren des Aufbewahrungszeitraums des Log Analytics-Arbeitsbereichs](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen.

Alternativ dazu können Sie zum Überwachen und Überprüfen Ihrer Protokolle auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center mit einem konfigurierten Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen für Ihre virtuellen Azure-Computer gefunden werden.

Alternativ dazu können Sie zum Einrichten von Warnungen für anomale Aktivitäten auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Sie können Microsoft Antimalware für Azure Cloud Services und Azure Virtual Machines verwenden und Ihre Windows-VMs so konfigurieren, dass Ereignisse in einem Azure Storage-Konto protokolliert werden. Konfigurieren Sie einen Log Analytics-Arbeitsbereich darauf, Ereignisse aus den Speicherkonten zu erfassen und ggf. Warnungen auszulösen. Befolgen Sie die Empfehlungen in Azure Security Center: Compute und Apps Für Linux-VMs benötigen Sie ein Drittanbietertool für die Erkennung von Sicherheitsrisiken durch Schadsoftware.

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Aktivieren der Überwachung auf Gastebene für Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

* [Anweisungen für das Onboarding von Linux-Servern in Azure Security Center](../security-center/quickstart-onboard-machines.md)

* [Der folgende Link enthält die empfohlenen Sicherheitsrichtlinien von Microsoft, die als Kriterienliste für die ausgewählte Sicherheitsrisikosoftware dienen können.](../virtual-machines/security-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung aus Azure Marketplace für die DNS-Protokollierungslösung entsprechend den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Azure Security Center bietet Protokollüberwachung auf sicherheitsrelevante Ereignisse für Azure-VMs. Microsoft Monitoring Agent wird von Security Center auf allen unterstützten Azure-VMs sowie auf allen neu erstellten VMs bereitgestellt, wenn die automatische Bereitstellung aktiviert ist. Sie können den Agent alternativ manuell installieren. Der Agent liefert das Prozesserstellungsereignis 4688 und das Feld „CommandLine“ im Ereignis 4688. Auf der VM erstellte neue Prozesse werden von EventLog aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht.

Bei Linux-VMs können Sie die Konsolenprotokollierung pro Knoten manuell konfigurieren und die Daten mithilfe von Syslog speichern. Verwenden Sie außerdem den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Syslog-Daten von virtuellen Azure-Computern auszuführen.

* [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Syslog-Datenquellen in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Auch wenn Azure Active Directory die empfohlene Methode zum Verwalten des Benutzerzugriffs ist, verfügt Azure Virtual Machines möglicherweise über lokale Konten. Lokale Konten und Domänenkonten sollten mit so wenig Aufwand wie möglich überprüft und verwaltet werden. Nutzen Sie außerdem Azure-Privileged Identity Management für Administratorkonten, die für den Zugriff auf die Ressourcen der virtuellen Computer verwendet werden.

* [Informationen zu lokalen Konten finden Sie unter](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: In der Azure-VM-Skalierungsgruppe und in Azure Active Directory gibt es konzeptionell keine Standardkennwörter. Der Kunde ist verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsverfahren für die Verwendung dedizierter Administratorkonten, die Zugriff auf Ihre virtuellen Computer haben. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen. Administratorkonten für den Zugriff auf Ressourcen virtueller Azure-Computer können auch von Azure Privileged Identity Management (PIM) verwaltet werden. Azure Privileged Identity Management bietet verschiedene Optionen wie Just-in-Time-Rechteerweiterung, das Erfordern mehrstufiger Authentifizierung für das Übernehmen einer Rolle und Delegierungsoptionen, durch die Berechtigungen nur für bestimmte Zeitrahmen verfügbar sind und genehmigt werden müssen.

* [Grundlegendes zu Identität und Zugriff im Azure Security Center](../security-center/security-center-identity-access.md)

* [Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit SSO mit Azure Active Directory, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

* [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure AD Privileged Identity Management (PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder unsicheren Aktivitäten in der Umgebung. Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen. Kunden können die Risikoerkennungswarnungen von Azure Security Center optional in Azure Monitor verarbeiten und mithilfe von Aktionsgruppen benutzerdefinierte Warnungen/Benachrichtigungen konfigurieren.

* [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Was ist Azure Active Directory Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Konfigurieren von Aktionsgruppen für benutzerdefinierte Warnungen und Benachrichtigungen](../azure-monitor/platform/action-groups.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie die Richtlinien und benannten Standorte der Azure Active Directory-Funktion „Bedingter Zugriff“, um den Zugriff nur bestimmten logischen Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zu gestatten.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Der auf einem virtuellen Computer ausgeführte Code kann dessen verwaltete Identität zum Anfordern von Zugriffstoken für Dienste verwenden, die die Azure AD-Authentifizierung unterstützen.

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory-domain-services/tutorial-create-instance.md)

* [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](../active-directory/managed-identities-azure-resources/overview.md)

**Azure Security Center-Überwachung:** Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Identitätszugriffsüberprüfungen für Azure Active Directory, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen. Wenn Sie virtuelle Azure-Computer verwenden, müssen Sie die lokalen Sicherheitsgruppen und Benutzer überprüfen, um sicherzustellen, dass keine unerwarteten Konten vorhanden sind, die das System gefährden könnten.

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Konfigurieren Sie Diagnoseeinstellungen für Azure Active Directory, um die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich zu senden. Verwenden Sie außerdem Azure Monitor, um Protokolle zu überprüfen und Abfragen für Daten von Azure-VMs auszuführen.

* [Grundlegendes zum Log Analytics-Arbeitsbereich](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Informationen zum Überwachen von virtuellen Computern in Azure](../azure-monitor/insights/monitor-vm-azure.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Azure Active Directory-Features zum Risiko- und Identitätsschutz können Sie automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Speicherkontoressourcen konfigurieren. Sie sollten automatisierte Antworten über Azure Sentinel aktivieren, um die Sicherheitsmaßnahmen Ihrer Organisation zu implementieren.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Wenn Microsoft auf Kundendaten zugreifen muss (z. B. bei einer Supportanfrage), verwenden Sie Kunden-Lockbox für Azure-VMs, um Anforderungen zum Zugriff auf Kundendaten zu überprüfen und zu genehmigen oder abzulehnen.

* [Grundlegendes zu Kunden-Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von virtuellen Azure-Computern, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein virtuelles Netzwerk oder Subnetz getrennt, entsprechend gekennzeichnet und in einer Netzwerksicherheitsgruppe (NSG) oder durch Azure Firewall geschützt werden. Implementieren Sie für VMs, auf denen vertrauliche Informationen gespeichert oder verarbeitet werden, Richtlinien und Verfahren, mit denen Sie sie bei Nichtverwendung deaktivieren können.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von „Warnen“ oder „Warnen und ablehnen“ mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung an den Netzwerkgrenzen, die auf nicht autorisierte Übertragungen von vertraulichen Informationen überwacht und derartige Übertragungen blockiert und gleichzeitig Informationssicherheitsexperten benachrichtigt.

Bei der zugrunde liegenden Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Daten während der Übertragung zu, von und zwischen VMs mit Windows werden auf verschiedene Weise verschlüsselt, je nach der Art der Verbindung, z. B. Verbindung mit einer VM in einer RDP- oder SSH-Sitzung.

Microsoft verwendet das TLS-Protokoll (Transport Layer Security) zum Schutz von Daten bei der Übertragung zwischen den Clouddiensten und Kunden.

* [Verschlüsselung während der Übertragung auf virtuellen Computern](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Verwenden Sie ein aktives Ermittlungstools eines Drittanbieters, um alle vertraulichen Informationen zu bestimmen, die von den Technologiesystemen der Organisation gespeichert, verarbeitet oder übertragen werden, einschließlich der Systeme, die sich vor Ort oder bei einem Remotedienstanbieter befinden, und um den Bestand an vertraulichen Informationen des Unternehmens zu aktualisieren.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, RBAC) können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur die Zugriffsberechtigungen auf Ihre VM gewähren, die sie zur Ausführung ihrer Aufgaben benötigen. Anstatt allen Benutzern den uneingeschränkten Zugriff auf die VM zu gewähren, können Sie auch nur bestimmte Aktionen zulassen. Sie können die Zugriffssteuerung für die VM im Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mit Azure PowerShell konfigurieren.

* [Azure RBAC](../role-based-access-control/overview.md)

* [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Implementieren Sie ein Drittanbietertool, z. B. eine automatisierte Lösung für die Verhinderung von Datenverlusten, um Zugriffssteuerungen für Daten zu erzwingen und das Risiko von Datensicherheitsverletzungen zu verringern.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Virtuelle Datenträger auf VMs werden mithilfe der serverseitigen Verschlüsselung oder von Azure Disk Encryption (ADE) im Ruhezustand verschlüsselt. Azure Disk Encryption nutzt das DM-Crypt-Feature von Linux, um verwaltete Datenträger auf dem virtuellen Gastcomputer mit kundenseitig verwalteten Schlüsseln zu verschlüsseln. Die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln bewirkt eine ADE-Verbesserung, indem Sie beliebige Betriebssystemtypen und Images für Ihre VMs verwenden können, indem Daten im Speicherdienst verschlüsselt werden.

* [Azure Disk Encryption für VM-Skalierungsgruppen](./disk-encryption-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an VM-Skalierungsgruppen und zugehörigen Ressourcen ausgegeben werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

* [Azure Storage Analytics-Protokollierung](../storage/common/storage-analytics-logging.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Azure Security Center-Empfehlungen zum Durchführen von Sicherheitsrisikobewertungen für Ihre Azure-VMs. Verwenden Sie von Azure Security Center empfohlene Lösungen oder Drittanbieterlösungen, um Sicherheitsrisikobewertungen für Ihre VMs durchzuführen.

* [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Aktivieren Sie automatische Betriebssystemupgrades für unterstützte Betriebssystemversionen oder für benutzerdefinierte Images, die in einer Shared Image Gallery gespeichert sind.

* [Automatische Betriebssystemupgrades für VM-Skalierungsgruppen in Azure](./virtual-machine-scale-sets-automatic-upgrade.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Azure-VM-Skalierungsgruppen (Virtual Machine Scale Sets, VMSS) kann automatische Betriebssystemimageupgrades verwenden. Sie können die Azure-Erweiterung für die Konfiguration des gewünschten Zustands (Desired State Configuration, DSC) für zugrunde liegende VMs in der VMSS verwenden. DSC dient zum Konfigurieren der VMs, sobald sie online geschaltet wurden, damit sie in der gewünschten Software ausgeführt werden.

* [Verwenden von VM-Skalierungsgruppen mit der Azure DSC-Erweiterung](./virtual-machine-scale-sets-dsc.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Exportieren Sie die Scanergebnisse in regelmäßigen Abständen und vergleichen Sie die Ergebnisse, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn der Kunde die vom Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwendet, kann er in das Portal der ausgewählten Lösung wechseln, um den Verlauf der Scandaten anzuzeigen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die Standardrisikobewertungen (Secure Score), die von Azure Security Center bereitgestellt werden.

* [Grundlegendes zum Secure Score des Azure Security Center](../security-center/secure-score-security-controls.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (einschließlich VMs) in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

* [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie einer Taxonomie entsprechend logisch zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tags, Verwaltungsgruppen und separate Abonnements, um VM-Skalierungsgruppen und die zugehörigen Ressourcen zu organisieren und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen entsprechend den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie eine Azure-Richtlinie, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Bei Umgebungen mit hohen Sicherheitsanforderungen wie Storage-Konten kann diese Methode nützlich sein.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Mit Azure Automation haben Sie in Bezug auf die Bereitstellung, Abläufe und Außerbetriebnahme von Workloads und Ressourcen die volle Kontrolle. Verwenden Sie den Bestand virtueller Azure-Computer, um die Erfassung von Informationen zur gesamten Software auf virtuellen Computern zu automatisieren. Hinweis: Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Kunden müssen die Diagnose auf Gastebene aktivieren und die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzufügen, um auf das Installationsdatum und andere Informationen zugreifen zu können.

Derzeit ist die adaptive Anwendungssteuerung für VM-Skalierungsgruppen nicht verfügbar.

* [Einführung in Azure Automation](../automation/automation-intro.md)

* [Aktivieren der Azure-VM-Inventur](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Mit Azure Automation haben Sie in Bezug auf die Bereitstellung, Abläufe und Außerbetriebnahme von Workloads und Ressourcen die volle Kontrolle. Mithilfe der Änderungsnachverfolgung können Sie sämtliche auf den VMs installierte Software ermitteln. Sie können einen eigenen Prozess implementieren oder Azure Automation State Configuration verwenden, um nicht autorisierte Software zu entfernen.

* [Einführung in Azure Automation](../automation/automation-intro.md)

* [Nachverfolgen von Änderungen in Ihrer Umgebung mit der Änderungsnachverfolgung](../automation/change-tracking/overview.md)

* [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Derzeit ist die adaptive Anwendungssteuerung für VM-Skalierungsgruppen nicht verfügbar. Verwenden Sie Software von Drittanbietern, um die Verwendung auf genehmigte Anwendungen zu beschränken.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie eine Azure-Richtlinie, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Derzeit ist die adaptive Anwendungssteuerung für VM-Skalierungsgruppen nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn diese die Anforderung Ihrer Organisation nicht erfüllt.

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Abhängig vom Skripttyp können Sie betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern verwenden, um die Möglichkeit der Benutzer zur Skriptausführung innerhalb von Azure-Computeressourcen einzuschränken.

* [Steuern der PowerShell-Skriptausführung in Windows-Umgebungen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Anwendungen mit hohem Risiko, die in Ihrer Azure-Umgebung bereitgestellt werden, können z. B. mithilfe von virtuellen Netzwerken, Subnetzen, Abonnements, Verwaltungsgruppen isoliert und durch eine Azure Firewall-Instanz, eine Web Application Firewall (WAF) oder eine Netzwerksicherheitsgruppe (NSG) ausreichend geschützt werden.

* [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-machines/network-overview.md)

* [Übersicht über Azure Firewall](../firewall/overview.md)

* [Übersicht über Web Application Firewall](../web-application-firewall/overview.md)

* [Übersicht über die Netzwerksicherheit](../virtual-network/network-security-groups-overview.md)

* [Übersicht über Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

* [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy oder Azure Security Center, um Sicherheitskonfigurationen für alle Azure-Ressourcen zu verwalten. Außerdem bietet Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser Code sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihr Unternehmen erfüllen/übertreffen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Herunterladen einer Vorlage für einen virtuellen Computer](../virtual-machines/windows/download-template.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Wenden Sie die Azure Security Center-Empfehlung [Remediate Vulnerabilities in Security Configurations on your Virtual Machines (Sicherheitsrisiken in Sicherheitskonfigurationen auf virtuellen Computern korrigieren)] an, um Sicherheitskonfigurationen für alle Computeressourcen zu verwalten.

* [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md)

* [Umsetzen von Azure Security Center-Empfehlungen](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Resource Manager-Vorlagen und Azure-Richtlinien, um Azure-Ressourcen, die VM-Skalierungsgruppen zugeordnet sind, sicher zu konfigurieren. Azure Resource Manager-Vorlagen sind JSON-basierte Dateien, die zum Bereitstellen von virtuellen Computern mit Azure-Ressourcen verwendet werden. Dazu muss eine benutzerdefinierte Vorlage verwaltet werden. Microsoft führt die Verwaltung der Basisvorlagen durch. Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.

* [Erstellen von Azure Resource Manager-Vorlagen](../virtual-machines/windows/ps-template.md)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Es stehen mehrere Optionen zur Verwaltung einer sicheren Konfiguration für virtuelle Azure-Computer für die Bereitstellung zur Verfügung:

1. Azure Resource Manager-Vorlagen: Diese JSON-basierten Dateien werden zum Bereitstellen einer VM über das Azure-Portal verwendet, und eine benutzerdefinierte Vorlage muss verwaltet werden. Microsoft führt die Verwaltung der Basisvorlagen durch.

2. Benutzerdefinierte virtuelle Festplatte (VHD): In einigen Fällen kann es erforderlich sein, benutzerdefinierte VHD-Dateien zu verwenden, z. B. bei komplexen Umgebungen, die nicht anders verwaltet werden können.

3. Azure Automation State Configuration: Nachdem das Basisbetriebssystem bereitgestellt wurde, kann es verwendet werden, um die Einstellungen präziser zu steuern und über das Automatisierungsframework zu erzwingen.

In den meisten Fällen sind die Microsoft-Basisvorlagen in Kombination mit Azure Automation Desired State Configuration beim Erfüllen und Verwalten der Sicherheitsanforderungen nützlich.

* [Herunterladen einer Vorlage für einen virtuellen Computer](../virtual-machines/windows/download-template.md)

* [Informationen zum Erstellen von ARM-Vorlagen](../virtual-machines/windows/ps-template.md)

* [Hochladen einer benutzerdefinierten VM-VHD in Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten, z. B. benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen, Desired State Configuration-Skripts usw.  Für den Zugriff auf die in Azure DevOps verwalteten Ressourcen wie Code, Builds und Arbeitsnachverfolgungen müssen Sie über Berechtigungen für diese spezifischen Ressourcen verfügen. Die meisten Berechtigungen werden über integrierte Sicherheitsgruppen erteilt, wie unter „Berechtigungen und Zugriff“ beschrieben. Sie können bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierten Gruppen (bei Integration mit Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration mit TFS) Berechtigungen erteilen oder verweigern.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Wenn Sie benutzerdefinierte Images (z. B. eine virtuelle Festplatte) verwenden, stellen Sie über die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) sicher, dass nur autorisierte Benutzer auf die Images zugreifen können.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Policy, um Systemkonfigurationen für Ihre virtuellen Computer zu melden, zu überwachen und zu erzwingen. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](../automation/automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure Security Center, um Baselineüberprüfungen für Ihre virtuellen Computer durchzuführen. Zu den weiteren Methoden für die automatisierte Konfiguration zählt Azure Automation State Configuration.

* [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Erste Schritte mit Azure Automation State Configuration](../automation/automation-dsc-getting-started.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](../automation/automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

* [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Verwenden Sie Microsoft Antimalware für Azure-Windows-VMs, um Ihre Ressourcen kontinuierlich zu überwachen und zu schützen. Sie benötigen ein Drittanbietertool für den Schutz vor Schadsoftware auf dem virtuellen Azure-Linux-Computer.

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Dies gilt nicht für virtuelle Azure-Computer, da es sich dabei um eine Computeressource handelt.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Nicht verfügbar

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Bei einer Bereitstellung für Windows-VMs werden standardmäßig von Microsoft Antimalware für Azure automatisch die neuesten Signaturupdates, Plattformupdates und Engine-Updates installiert. Befolgen Sie die Empfehlungen in Azure Security Center: „Compute &amp; Apps“, um sicherzustellen, dass alle Endpunkte mit den neuesten Signaturen auf dem aktuellen Stand sind. Das Windows-Betriebssystem kann durch zusätzliche Sicherheitsmaßnahmen noch besser geschützt werden, indem das Risiko durch Viren oder auf Schadsoftware basierende Angriffe mit dem Microsoft Defender Advanced Threat Protection-Dienst begrenzt wird, der mit Azure Security Center integriert ist.

Sie benötigen ein Drittanbietertool für den Schutz vor Schadsoftware auf dem virtuellen Azure-Linux-Computer.

* [Bereitstellen von Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../virtual-machines/security-recommendations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Erstellen Sie mit PowerShell oder REST-APIs eine Momentaufnahme der Azure-VM-Skalierungsgruppeninstanz oder des mit der Instanz verbundenen verwalteten Datenträgers. Sie können auch Azure Automation verwenden, um die Sicherungsskripts regelmäßig auszuführen.

* [Erstellen einer Momentaufnahme einer VM-Skalierungsgruppeninstanz und eines verwalteten Datenträgers](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Einführung in Azure Automation](../automation/automation-intro.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Erstellen Sie mithilfe von PowerShell oder REST-APIs Momentaufnahmen Ihrer virtuellen Azure-Computer oder der mit diesen Instanzen verknüpften verwalteten Datenträger. Sichern Sie kundenseitig verwaltete Schlüssel in Azure Key Vault.

Aktivieren Sie Azure Backup für die virtuellen Azure-Zielcomputer (VMs) sowie die gewünschte Häufigkeit und Beibehaltungsdauer. Dies umfasst eine vollständige Sicherung des Systemzustands. Wenn Sie Azure Disk Encryption verwenden, werden bei der Azure-VM-Sicherung automatisch auch kundenseitig verwaltete Schlüssel gesichert.

* [Sichern auf Azure-VMs mit Verschlüsselung](../backup/backup-azure-vms-encryption.md)

* [Übersicht über das Sichern von Azure-VMs](../backup/backup-azure-vms-introduction.md)

* [Erstellen einer Momentaufnahme einer VM-Skalierungsgruppeninstanz und eines verwalteten Datenträgers](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie sicher, dass die Daten des verwalteten Datenträgers jederzeit in Azure Backup wiederhergestellt werden können. Testen Sie die Wiederherstellung von Inhalten ggf. in einem isolierten virtuellen Netzwerk oder Abonnement. Die Wiederherstellung der gesicherten kundenseitig verwalteten Schlüssel erfolgt durch den Kunden.

Wenn Sie Azure Disk Encryption verwenden, können Sie Ihre VM-Skalierungsgruppen mit den Verschlüsselungsschlüsseln für den Datenträger wiederherstellen. Bei Verwendung der Datenträgerverschlüsselung können Sie die Azure-VM mit den Verschlüsselungsschlüsseln für den Datenträger wiederherstellen.

* [Sichern auf Azure-VMs mit Verschlüsselung](../backup/backup-azure-vms-encryption.md)

* [Wiederherstellen eines Datenträgers und Erstellen einer wiederhergestellten VM in Azure](../backup/tutorial-restore-disk.md)

* [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Aktivieren der Datenträgerverschlüsselung für Azure-VM-Skalierungsgruppen](./disk-encryption-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Aktivieren Sie den Löschschutz für verwaltete Datenträger mithilfe von Sperren. Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.

* [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../azure-resource-manager/management/lock-resources.md)

* [Übersicht über den Schutz vor vorläufigem und endgültigem Löschen in Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung eines eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

* [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

* [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

* [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

* [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

* [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und die Red Teaming- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen.

* [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).