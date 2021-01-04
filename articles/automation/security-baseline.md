---
title: Azure-Sicherheitsbaseline für Azure Automation
description: Azure-Sicherheitsbaseline für Automation
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e07e9035dc55f77a2e76b4f4ee306deab829980a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182749"
---
# <a name="azure-security-baseline-for-automation"></a>Azure-Sicherheitsbaseline für Automation

Die Azure-Sicherheitsbaseline für Automation enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Das Azure Automation-Konto unterstützt noch nicht Azure Private Link, um den Zugriff auf den Dienst über private Endpunkte einzuschränken. Runbooks, die für Ressourcen in Azure authentifiziert und ausgeführt werden, werden in einer Azure-Sandbox ausgeführt und nutzen freigegebene Back-End-Ressourcen, für deren Isolierung voneinander Microsoft verantwortlich ist. Ihr Netzwerk ist uneingeschränkt und kann auf öffentliche Ressourcen zugreifen. Azure Automation bietet über die Unterstützung von Hybrid Runbook Worker-Instanzen hinaus derzeit keine Integration des virtuellen Netzwerks für private Netzwerke. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Zur weiteren Isolation ihrer Runbooks können Sie auf virtuellen Azure-Computern ausgeführte Hybrid Runbook Worker-Instanzen verwenden. Beim Erstellen eines virtuellen Azure-Computers müssen Sie ein virtuelles Netzwerk (VNET) erstellen oder ein vorhandenes VNET verwenden und Ihre VMs mit einem Subnetz konfigurieren. Stellen Sie sicher, dass auf alle bereitgestellten Subnetze eine Netzwerksicherheitsgruppe mit Netzwerkzugriffssteuerungen angewendet wurde, die für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. Informationen zu dienstspezifischen Anforderungen finden Sie in der Sicherheitsempfehlung für den jeweiligen Dienst.

Alternativ kann für eine bestimmte Anforderung auch die Azure-Firewall verwendet werden.

* [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-machines/network-overview.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Ausführungsumgebung für Runbooks](./automation-runbook-execution.md#runbook-execution-environment)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Azure Automation bietet über die Unterstützung von Hybrid Runbook Worker-Instanzen hinaus derzeit keine Integration des virtuellen Netzwerks für private Netzwerke. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen mit Unterstützung virtueller Azure-Computer verwenden, stellen Sie sicher, dass das Subnetz, in dem diese Worker enthalten sind, mit einer Netzwerksicherheitsgruppe (NSG) aktiviert ist, und konfigurieren Sie Datenflussprotokolle, um Protokolle an ein Speicherkonto für Datenverkehrsüberwachung weiterzuleiten. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich weiterleiten und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Obwohl NSG-Regeln und benutzerdefinierte Routen nicht für den privaten Endpunkt gelten, werden NSG-Datenflussprotokolle und Überwachungsinformationen für ausgehende Verbindungen weiterhin unterstützt und können verwendet werden.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Azure Automation bietet über die Unterstützung von Hybrid Runbook Worker-Instanzen hinaus derzeit keine Integration des virtuellen Netzwerks für private Netzwerke. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von virtuellen Azure-Computern unterstützt werden, aktivieren Sie den DDoS-Standardschutz (Distributed Denial of Service) zum Schutz vor DDoS-Angriffen in Ihren virtuellen Netzwerken, die Ihre Hybrid Runbook Worker-Instanzen hosten. Mit der integrierten Threat Intelligence in Azure Security Center können Sie die Kommunikation mit bekannten schädlichen IP-Adressen verweigern. Konfigurieren Sie die Azure Firewall auf allen Segmenten des virtuellen Netzwerks mit aktivierter Threat Intelligence, und konfigurieren Sie **Warnen und ablehnen** für schädlichen Netzwerkdatenverkehr.

Sie können mit dem Just-in-Time-Netzwerkzugriff in Azure Security Center die Verfügbarkeit von Windows-VMs auf genehmigte IP-Adressen und einen begrenzten Zeitraum einschränken. Verwenden Sie außerdem die Empfehlungen der adaptiven Netzwerkhärtung in Azure Security Center für NSG-Konfigurationen, um Ports und Quell-IPs basierend auf tatsächlichem Datenverkehr und Threat Intelligence einzuschränken.

* [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../security-center/azure-defender.md)

* [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

* [Grundlegendes zur Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../security-center/security-center-just-in-time.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Azure Automation bietet über die Unterstützung von Hybrid Runbook Worker-Instanzen hinaus derzeit keine Integration des virtuellen Netzwerks für private Netzwerke. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von Azure-VMs unterstützt werden, können Sie NSG-Datenflussprotokolle in einem Speicherkonto aufzeichnen, um Datenfluss-Datensätze für Ihre Azure-VMs zu generieren, die als Runbook Worker-Instanzen fungieren. Beim Untersuchen anomaler Aktivitäten können Sie Network Watcher-Paketerfassung aktivieren, damit Netzwerkdatenverkehr auf ungewöhnliche und unerwartete Aktivitäten überprüft werden kann.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Azure Automation bietet über die Unterstützung von Hybrid Runbook Worker-Instanzen hinaus derzeit keine Integration des virtuellen Netzwerks für private Netzwerke. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie auf virtuellen Azure-Computern gehostete Hybrid Runbook Worker-Instanzen verwenden, können Sie von Network Watcher und Open-Source-IDS-Tools bereitgestellte Paketerfassungen kombinieren, um auf einer großen Bandbreite von Bedrohungen dieser Workercomputer basierende Netzwerkangriffe zu erkennen. Sie können auch Azure Firewall auf allen Segmenten des virtuellen Netzwerks mit aktivierter Threat Intelligence nach Bedarf und mit „Warnen und ablehnen“ für schädlichen Netzwerkdatenverkehr konfiguriert bereitstellen.

* [Durchführen der Erkennung von Netzwerkangriffen mit Network Watcher und Open Source-Tools](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie VNET-Diensttags, um Netzwerkzugriffssteuerungen für in Azure konfigurierte Netzwerksicherheitsgruppen oder Azure Firewall-Instanzen zu definieren, die Zugriff auf Ihre Automation-Ressourcen benötigen. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (z. B. GuestAndHybridManagement) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

* [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die von Azure Automation verwendet werden.

Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelemente und Richtlinien in einer einzigen Blaupausendefinition verpacken. Sie können die Blaupause auf neue Abonnements anwenden sowie die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für NSGs und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

* [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und Erkennen von Änderungen an Ihren Netzwerkressourcen. Erstellen Sie in Azure Monitor Warnungen, die bei Änderungen an wichtigen Ressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen. Sie haben jedoch die Möglichkeit, die Zeitsynchronisierungseinstellungen für alle Hybrid Runbook Worker-Instanzen zu verwalten, die auf virtuellen Windows-Computern ausgeführt werden.

* [Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen](../virtual-machines/windows/time-sync.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Leiten Sie Protokolldaten an Azure Monitor-Protokolle weiter, um die von Azure Automation-Ressourcen generierten Sicherheitsdaten zu aggregieren. Verwenden Sie in Azure Monitor Protokollabfragen, um Analysen zu untersuchen und auszuführen, und Azure Storage-Konten für die langfristige Speicherung/Archivierung. Azure Automation kann Runbookauftragsstatus, Auftragsstreams, Automatisierungszustands-Konfigurationsdaten, Updateverwaltung und Änderungsnachverfolgungs- oder Bestandsprotokolle an Ihren Log Analytics-Arbeitsbereich senden. Diese Informationen sind über Azure-Portal, Azure PowerShell und Azure Monitor-Protokolle-API sichtbar, sodass Sie einfache Untersuchungen durchführen können.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle](./automation-manage-send-joblogs-log-analytics.md)

* [Integration von DSC in Azure Monitor-Protokolle](./automation-dsc-diagnostics.md)

* [Unterstützte Regionen für einen verknüpften Log Analytics-Arbeitsbereich](./how-to/region-mappings.md)

* [Abfragen von Protokollen der Updateverwaltung](./update-management/query-logs.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Azure Monitor für den Zugriff auf Ihre Überwachungs- und Aktivitätsprotokolle, was Ereignisquelle, Datum, Benutzer, Zeitstempel, Quell- und Zieladressen sowie andere nützliche Elemente einschließt.

* [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Wenn Sie Azure Automation mit den mehrinstanzenfähigen Runbook Worker-Instanzen verwenden, ist diese Steuerung nicht anwendbar, und die Plattform behandelt die zugrunde liegenden virtuellen Computer.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, stellt Azure Security Center die Sicherheitsereignisprotokoll-Überwachung für virtuelle Windows-Computer bereit. Wenn Ihre Organisation die Daten des Sicherheitsereignisprotokolls aufbewahren möchte, kann es in einer Datensammlungsebene gespeichert werden. Dann kann es in Log Analytics abgefragt werden. Es gibt verschiedene Ebenen: „Minimal“, „Allgemein“ und „Alle“, die im folgenden Link ausführlich erläutert werden.

* [Konfigurieren der Datensammlungsebene in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

* [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Details zur Datenaufbewahrung für Automation-Konten](./automation-managing-data.md#data-retention)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anormales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Verwenden Sie Protokollabfragen von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen.

Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Grundlegendes zu Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center mit Azure Monitor für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden.

Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Warnungen zu Azure Monitor-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Wenn Sie Azure Automation mit mehrinstanzenfähigen Runbook Worker-Instanzen verwenden, ist diese Steuerung nicht anwendbar, und die Plattform behandelt die zugrunde liegenden virtuellen Computer.

Wenn Sie jedoch das Hybrid Runbook Worker-Feature verwenden, können Sie Microsoft Antimalware für Azure Cloud Services und virtuelle Computer verwenden. Konfigurieren Sie Ihre virtuellen Computer, um Ereignisse in einem Azure Storage-Konto zu protokollieren. Konfigurieren Sie einen Log Analytics-Arbeitsbereich darauf, Ereignisse aus den Speicherkonten zu erfassen und ggf. Warnungen auszulösen. Befolgen Sie die Empfehlungen in Azure Security Center: „Compute &amp; Apps“.

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Aktivieren der Überwachung auf Gastebene für Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung aus Azure Marketplace für die DNS-Protokollierungslösung entsprechend den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Wenn Sie Azure Automation mit den mehrinstanzenfähigen Runbook Worker-Instanzen verwenden, ist diese Steuerung nicht anwendbar, und die Plattform behandelt die zugrunde liegenden virtuellen Computer.

Wenn Sie jedoch die Hybrid Runbook Worker-Funktion verwenden, stellt Azure Security Center die Sicherheitsereignisprotokoll-Überwachung für virtuelle Azure-Computer bereit. Bei aktivierter automatischer Bereitstellung wird der Log Analytics-Agent von Security Center auf allen unterstützten und neu erstellten virtuellen Azure-Computern bereitgestellt. Alternativ können Sie den Agent manuell installieren. Der Agent liefert das Prozesserstellungsereignis 4688 und das Feld „CommandLine“ im Ereignis 4688. Auf der VM erstellte neue Prozesse werden vom Ereignisprotokoll aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht.

* [Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwenden Sie die integrierten Administratorrollen in Azure Active Directory, die explizit zugewiesen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind. Stellen Sie bei der Verwendung von ausführenden Automation-Konten für Ihre Runbooks sicher, dass diese Dienstprinzipale auch im Bestand nachverfolgt werden, da sie häufig über erweiterte Berechtigungen verfügen. Löschen Sie nicht verwendete ausführende Konten, um die Angriffsfläche zu minimieren.

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Löschen eines ausführenden Kontos oder klassischen ausführenden Kontos](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Verwalten eines ausführenden Azure Automation-Kontos](./manage-runas-account.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Das Azure Automation-Konto verfügt nicht über ein Konzept mit Standardkennwörtern. Kunden sind für Anwendungen von Drittanbietern und Marketplace-Dienste zuständig, die möglicherweise Standardkennwörter verwenden, die auf dem Dienst oder seinen Hybrid Runbook Worker-Instanzen ausgeführt werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen. Stellen Sie bei der Verwendung von ausführenden Automation-Konten für Ihre Runbooks sicher, dass diese Dienstprinzipale auch im Bestand nachverfolgt werden, da sie häufig über erweiterte Berechtigungen verfügen. Ordnen Sie diesen Identitäten nur die Mindestberechtigungen zu, damit Ihre Runbooks ihren automatisierten Prozess erfolgreich durchführen. Löschen Sie nicht verwendete ausführende Konten, um die Angriffsfläche zu minimieren.

Sie können außerdem einen Just-in-Time- oder Just-Enough-Zugriff aktivieren, indem Sie mit Azure AD Privileged Identity Management verwaltete privilegierte Rollen für Microsoft-Dienste und Azure Resource Manager verwenden.

* [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Löschen eines ausführenden Kontos oder klassischen ausführenden Kontos](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Verwalten eines ausführenden Azure Automation-Kontos](./manage-runas-account.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie nach Möglichkeit SSO mit Azure Active Directory, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

* [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

* [Authentifizieren bei Azure mithilfe von Azure AD](./automation-use-azure-ad.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie mehrstufige Azure AD-Authentifizierung (MFA), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie PAWs mit Multi-Factor Authentication, die für die Anmeldung bei Azure Automation-Kontoressourcen in Produktionsumgebungen und deren Konfiguration konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure AD-Risikoerkennungen, um Warnungen und Berichte zum Verhalten von Risikobenutzern anzuzeigen. Kunden können die Risikoerkennungswarnungen von Azure Security Center optional an Azure Monitor weiterleiten und mithilfe von Aktionsgruppen benutzerdefinierte Warnungen/Benachrichtigungen konfigurieren.

* [Was ist Azure Active Directory Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Konfigurieren von Aktionsgruppen für benutzerdefinierte Warnungen und Benachrichtigungen](../azure-monitor/platform/action-groups.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Benannte Standorte mit bedingtem Zugriff sollten verwendet werden, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure AD als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert. Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, können Sie anstelle ausführender Konten verwaltete Identitäten nutzen, um sicherere nahtlose Berechtigungen zu ermöglichen.

* [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory-domain-services/tutorial-create-instance.md)

* [Verwenden der Runbookauthentifizierung mit verwalteten Identitäten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. Stellen Sie bei der Verwendung von ausführenden Automation-Konten für Ihre Runbooks sicher, dass diese Dienstprinzipale auch im Bestand nachverfolgt werden, da sie häufig über erweiterte Berechtigungen verfügen. Löschen Sie nicht verwendete ausführende Konten, um die Angriffsfläche zu minimieren.

* [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

* [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

* [Löschen eines ausführenden Kontos oder klassischen ausführenden Kontos](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Verwalten eines ausführenden Azure Automation-Kontos](./manage-runas-account.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten für Ihre Netzwerkressource konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Bei Azure Automation-Konten kann der Microsoft-Support während einer offenen Supportanfrage auf Metadaten der Plattformressource zugreifen, ohne ein anderes Tool zu verwenden.

Wenn Sie jedoch Hybrid Runbook Worker-Instanzen verwenden, die von virtuellen Azure-Computern unterstützt werden, und ein Drittanbieter auf Kundendaten zugreifen muss (z. B. während einer Supportanfrage), verwenden Sie Kunden-Lockbox (Vorschau) für virtuelle Azure-Computer, um Anforderungen des Zugriffs auf Kundendaten zu überprüfen und zu genehmigen oder abzulehnen.

* [Grundlegendes zu Kunden-Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure Automation-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Isolieren Sie Umgebungen mit separaten Automation-Kontoressourcen. Ressourcen wie Hybrid Runbook Worker-Instanzen sollten durch ein virtuelles Netzwerk oder Subnetz getrennt, entsprechend gekennzeichnet und in einer Netzwerksicherheitsgruppe (NSG) oder Azure Firewall gesichert werden. Implementieren Sie für VMs, auf denen vertrauliche Informationen gespeichert oder verarbeitet werden, Richtlinien und Verfahren, mit denen Sie sie bei Nichtverwendung deaktivieren können.

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

**Leitfaden**: Nutzen Sie bei Verwendung des Hybrid Runbook Worker-Features eine Drittanbieterlösung von Azure Marketplace an Netzwerkperimetern, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Bei der zugrundeliegenden Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure-Ressourcen in virtuellen Azure-Netzwerken verbinden, TLS 1.2 oder höher aushandeln können. Für alle externen HTPS-Endpunkte unterstützt Azure Automation vollständig und erzwingt TLS 1.2 (Transport Layer) und alle Clientanrufe oder spätere Versionen (über Webhooks, DSC-Knoten, Hybrid Runbook Worker).

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

* [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Erzwingen von TLS 1.2 für Azure Automation](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Verwenden Sie ein aktives Ermittlungstools eines Drittanbieters, um alle vertraulichen Informationen zu bestimmen, die von den Technologiesystemen der Organisation gespeichert, verarbeitet oder übertragen werden, einschließlich der Systeme, die sich vor Ort oder bei einem Remotedienstanbieter befinden, und um den Bestand an vertraulichen Informationen des Unternehmens zu aktualisieren.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Steuern Sie mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) den Zugriff auf Azure Automation-Ressourcen mithilfe der integrierten Rollendefinitionen, und weisen Sie Benutzern, die auf Ihre Automatisierungsressourcen zugreifen, den Zugriff nach dem Modell des Zugriffs mit gerade ausreichenden Rechten zu. Nutzen Sie bei der Verwendung von Hybrid Runbook Worker-Instanzen verwaltete Identitäten für diese virtuellen Computer, um die Verwendung von Dienstprinzipalen zu vermeiden. Wenn Sie sowohl mehrinstanzenfähige Runbook Worker-Instanzen als auch Hybrid Runbook Worker-Instanzen verwenden, stellen Sie sicher, dass Sie für die Identität der Runbook Worker-Instanzen ordnungsgemäße bereichsbezogene Azure RBAC-Berechtigungen anwenden.

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Runbook-Berechtigungen für einen Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Verwalten von Berechtigungen und Sicherheit für Rollen](./automation-role-based-access-control.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von virtuellen Azure-Computern unterstützt werden, müssen Sie eine hostbasierte Drittanbieterlösung zur Verhinderung von Datenverlusten verwenden, um die Zugriffssteuerung für Ihre gehosteten Hybrid Runbook Worker-VMs zu erzwingen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verwenden Sie von Kunden verwaltete Schlüssel mit Azure Automation. Azure Automation unterstützt die Verwendung von Kunden verwalteter Schlüssel, um alle verwendeten „sicheren Objekte“ zu verschlüsseln, wie z. B.: Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Nutzen Sie verschlüsselte Variablen mit ihren Runbooks für alle Ihre Suchanforderungen persistenter Variablen, um unbeabsichtigtes Verfügbarmachen zu verhindern.

Bei der Verwendung von Hybrid Runbook Worker-Instanzen werden die virtuellen Datenträger auf den virtuellen Computern mithilfe der serverseitigen Verschlüsselung oder der Azure Disk Encryption (ADE) im Ruhezustand verschlüsselt. Azure Disk Encryption nutzt das BitLocker-Feature von Windows zum Verschlüsseln verwalteter Datenträger mit vom Kunden verwalteten Schlüsseln innerhalb der Gast-VM. Die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln bewirkt eine ADE-Verbesserung, indem Sie beliebige Betriebssystemtypen und Images für Ihre VMs verwenden können, indem Daten im Speicherdienst verschlüsselt werden.

* [Serverseitige Verschlüsselung von verwalteten Azure-Datenträgern](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption für Windows-VMs](../virtual-machines/windows/disk-encryption-overview.md)

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Verwaltete Variablen in Azure Automation](./shared-resources/variables.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an wichtigen Azure-Ressourcen wie Netzwerkkomponenten, Azure Automation-Konten und Runbooks ausgegeben werden.

* [Diagnoseprotokollierung für eine Netzwerksicherheitsgruppe](../private-link/private-link-overview.md#logging-and-monitoring)

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Befolgen Sie die Azure Security Center-Empfehlungen zum Durchführen von Sicherheitsrisikobewertungen für Ihre Azure-Ressourcen.

* [Sicherheitsempfehlungen in Azure Security Center](../security-center/security-center-recommendations.md)

* [Referenz für Security Center-Empfehlung](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von Azure-VMs unterstützt werden, verwalten Sie mit der Azure-Updateverwaltung Updates und Patches für Ihre virtuellen Computer. In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu patchen. Mit Tools wie System Center Updates Publisher (Updates Publisher) können Sie benutzerdefinierte Updates in Windows Server Update Services (WSUS) veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

* [Updateverwaltung in Azure](./update-management/overview.md)

* [Verwalten von Updates und Patches für Ihre VMs](./update-management/manage-updates-for-vm.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von Azure-VMs unterstützt werden, können Sie mit der Azure-Updateverwaltung Updates und Patches für Ihre virtuellen Computer verwalten. In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu patchen. Mit Tools wie System Center Updates Publisher (Updates Publisher) können Sie benutzerdefinierte Updates in Windows Server Update Services (WSUS) veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

* [Updateverwaltung in Azure](./update-management/overview.md)

* [Verwalten von Updates und Patches für Ihre Azure-VMs](./update-management/manage-updates-for-vm.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Exportieren Sie die Scanergebnisse in regelmäßigen Abständen und vergleichen Sie die Ergebnisse, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn der Kunde die vom Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwendet, kann er in das Portal der ausgewählten Lösung wechseln, um den Verlauf der Scandaten anzuzeigen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Priorisieren Sie die Behebung von ermittelten Sicherheitsrisiken mithilfe der von Azure Security Center bereitgestellten Standardrisikobewertungen (Secure Score).

* [Grundlegendes zum Secure Score des Azure Security Center](../security-center/secure-score-security-controls.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Azure Automation-Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

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

**Leitfaden**: Verwenden Sie Tagging, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Azure Automation-Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden. Löschen Sie nicht verwendete ausführende Konten, um die Angriffsfläche zu minimieren.

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

* [Löschen eines ausführenden Kontos oder klassischen ausführenden Kontos](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Verwalten eines ausführenden Azure Automation-Kontos](./manage-runas-account.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Sie müssen einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß Ihren organisatorischen Anforderungen erstellen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen/zu ermitteln. Bei Umgebungen mit hohen Sicherheitsanforderungen wie Storage-Konten kann diese Methode nützlich sein.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Integrierte Azure Policy-Beispiele für Azure Automation](./policy-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Das Azure Automation-Angebot macht die zugrunde liegenden virtuellen Computer mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden. Es ist jedoch möglich, PowerShell- oder Python-Module zu installieren, zu entfernen und zu verwalten, auf die Runbooks über das Portal oder Cmdlets zugreifen können. Nicht genehmigte oder alte Module sollten für die Runbooks entfernt oder aktualisiert werden.

Wenn Sie Hybrid Runbook Worker-Instanzen verwenden, die von Azur-VMs unterstützt werden, bietet Azure Automation eine vollständige Kontrolle über die Bereitstellung, den Betrieb und die Außerbetriebnahme von Workloads und Ressourcen. Verwenden Sie den Bestand virtueller Azure-Computer, um die Erfassung von Informationen zur gesamten Software auf virtuellen Computern zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Kunden müssen die Diagnose auf Gastebene aktivieren und die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzufügen, um auf das Installationsdatum und andere Informationen zugreifen zu können.

* [Einführung in Azure Automation](./automation-intro.md)

* [Ermitteln der auf Ihren VMs installierten Software](./automation-tutorial-installed-software.md)

* [Verwalten von Modulen in Azure Automation](./shared-resources/modules.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Der Kunde kann die Erstellung oder Verwendung von Ressourcen mit Azure Policy den Unternehmensrichtlinien entsprechend verhindern. Sie können einen eigenen Prozess zum Entfernen nicht autorisierter Ressourcen implementieren. Es ist jedoch im Rahmen des Azure Automation-Angebots möglich, PowerShell- oder Python-Module zu installieren, zu entfernen und zu verwalten, auf die Runbooks über das Portal oder Cmdlets zugreifen können. Nicht genehmigte oder alte Module sollten für die Runbooks entfernt oder aktualisiert werden.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Verwalten von Modulen in Azure Automation](./shared-resources/modules.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, können Sie mit den adaptiven Anwendungssteuerungen in Azure Security Center sicherstellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, können Sie die adaptiven Anwendungssteuerungen in Azure Security Center mit ihren Hybrid Worker-VMs verwenden.

Die adaptive Anwendungssteuerung ist eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Computern mit oder ohne Azure ausgeführt werden können (Windows und Linux). Implementieren Sie eine Drittanbieterlösung, wenn diese die Anforderung Ihrer Organisation nicht erfüllt.

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie die Azure-Richtlinien für bedingten Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für ungesicherte oder nicht genehmigte Speicherorte oder Geräte für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, können Sie abhängig vom Skripttyp betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern verwenden, um die Möglichkeit der Benutzer zur Skriptausführung innerhalb von Azure-Computeressourcen einzuschränken. Sie können auch die adaptiven Anwendungssteuerungen in Azure Security Center nutzen, um sicherzustellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

* [Steuern der PowerShell-Skriptausführung in Windows-Umgebungen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Anwendungen mit hohem Risiko, die in Ihrer Azure-Umgebung bereitgestellt werden, können mithilfe von separaten Netzwerk- und Ressourcencontainern unter Verwendung von Konstrukten wie virtuellen Netzwerken, Subnetzen, Abonnements, Verwaltungsgruppen isoliert und durch eine Azure Firewall-Instanz, eine Web Application Firewall (WAF) oder eine Netzwerksicherheitsgruppe (NSG) ausreichend geschützt werden.

* [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-machines/network-overview.md)

* [Übersicht über Azure Firewall](../firewall/overview.md)

* [Azure Web Application Firewall – Übersicht](../web-application-firewall/overview.md)

* [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)

* [Übersicht über Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

* [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Konfiguration Ihrer Azure Automation- und zugehörigen Ressourcen zu erstellen. Sie können auch integrierte Azure Policy-Definitionen verwenden.

Außerdem hat Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen.

Sie können auch Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

* [Integrierte Azure Policy-Beispiele für Azure Automation](./policy-reference.md)

* [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar. Dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, verwalten Sie Sicherheitskonfigurationen auf Ihren VMs mit der Azure Security Center-Empfehlung „Remediate Vulnerabilities in Security Configurations on your Virtual Machines“ (Beheben von Sicherheitsrisiken in Sicherheitskonfigurationen auf virtuellen Computern).

* [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md)

* [Umsetzen von Azure Security Center-Empfehlungen](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Resource Manager-Vorlagen und Azure Policy, um Azure-Ressourcen, die Azure Automation zugeordnet sind, sicher zu konfigurieren. Azure Resource Manager-Vorlagen sind JSON-basierte Dateien, die zum Bereitstellen von Azure-Ressourcen verwendet werden, und alle benutzerdefinierten Vorlagen müssen sicher in einem Coderepository gespeichert und verwaltet werden. Halten Sie die Runbooks mit der Funktion zur Integration der Quellcodeverwaltung in Ihrem Automation-Konto auf demselben aktuellen Stand wie die Skripts in Ihrem Quellcodeverwaltungrepository. Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure-Ressourcen zu erzwingen.

* [Verwenden der Integration der Quellcodeverwaltung](./source-control-integration.md)

* [Erstellen von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

* [Bereitstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Integrierte Azure Policy-Beispiele für Azure Automation](./policy-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, können Sie mehrere Optionen für die Beibehaltung einer sicheren Konfiguration für virtuelle Azure-Computer für die Bereitstellung nutzen:

- Azure Resource Manager-Vorlagen: Diese JSON-basierten Dateien werden zum Bereitstellen einer VM über das Azure-Portal verwendet, und eine benutzerdefinierte Vorlage muss verwaltet werden. Microsoft führt die Verwaltung der Basisvorlagen durch.
- Benutzerdefinierte virtuelle Festplatte (VHD): In einigen Fällen kann es erforderlich sein, benutzerdefinierte VHD-Dateien zu verwenden, z. B. bei komplexen Umgebungen, die nicht anders verwaltet werden können.
- Azure Automation State Configuration: Nachdem das Basisbetriebssystem bereitgestellt wurde, kann es verwendet werden, um die Einstellungen präziser zu steuern und über das Automatisierungsframework zu erzwingen.

In den meisten Fällen sind die Microsoft-Basisvorlagen in Kombination mit Azure Automation State Configuration beim Erfüllen und Verwalten der Sicherheitsanforderungen nützlich.

* [Herunterladen einer Vorlage für einen virtuellen Computer](../virtual-machines/windows/download-template.md)

* [Informationen zum Erstellen von ARM-Vorlagen](../virtual-machines/windows/ps-template.md)

* [Hochladen einer benutzerdefinierten VM-VHD in Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten, z. B. benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen und Desired State Configuration-Skripts. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory definierten Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern. Halten Sie die Runbooks mit der Funktion zur Integration der Quellcodeverwaltung in Ihrem Automation-Konto auf demselben aktuellen Stand wie die Skripts in Ihrem Quellcodeverwaltungrepository.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Verwenden der Integration der Quellcodeverwaltung](./source-control-integration.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, stellen Sie sicher, dass Sie den Zugriff auf das benutzerdefinierte Betriebssystemimage in Ihrem Speicherkonto ordnungsgemäß einschränken, sodass nur autorisierte Benutzer auf das Image zugreifen können.

* [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure-Ressourcen zu erstellen. Sie können auch auf integrierte Richtliniendefinitionen zurückgreifen, die sich auf Ihre spezifischen Ressourcen beziehen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Aliase](../governance/policy/concepts/definition-structure.md#aliases)

* [Integrierte Azure Policy-Beispiele für Azure Automation](./policy-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, verwenden Sie Azure Automation State Configuration auf den Runbook Worker-Instanzen, einen Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](./automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy zu Warnung und Überwachung in Verbindung mit Azure-Ressourcenkonfigurationen. Policy kann verwendet werden, um bestimmte Ressourcen zu erkennen, die nicht mit einem privaten Endpunkt konfiguriert sind.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, nutzen Sie Azure Security Center, um grundlegende Scans für Ihre virtuellen Azure-Computer durchzuführen. Zu den zusätzlichen Methoden für die automatisierte Konfiguration zählt Azure Automation State Configuration.

* [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Erste Schritte mit Azure Automation State Configuration](./automation-dsc-getting-started.md)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Integrierte Azure Policy-Beispiele für Azure Automation](./policy-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Das Azure Automation-Angebot macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, verwenden Sie Azure Automation State Configuration für die Runbook Worker-Instanzen, einen Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

* [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](./automation-dsc-onboarding.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Anleitung:** Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Verwenden der Runbookauthentifizierung mit verwalteten Identitäten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Das Azure Automation-Angebot macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, verwenden Sie Microsoft Antimalware für virtuelle Azure-Windows-Computer, um Ihre Runbook Worker-Ressourcen fortlaufend zu überwachen und zu schützen.

* [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Nicht zutreffend; Azure Automation as-a-Service speichert keine Dateien. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Automation) aktiviert, wird jedoch nicht für Ihre Inhalte ausgeführt.

* [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Azure Automation macht die zugrunde liegenden virtuellen Computer oder das Betriebssystem mehrinstanzenfähiger Runbook Worker-Instanzen derzeit nicht verfügbar, und dies wird von der Plattform behandelt. Diese Steuerung ist nicht anwendbar, wenn Sie den Out-of-Box-Dienst ohne Hybrid Runbook Worker-Instanzen verwenden.

Wenn Sie die Hybrid Runbook Worker-Funktion verwenden, verwenden Sie Microsoft Antimalware für Azure, um die neuesten Signatur-, Plattform- und Engine-Updates standardmäßig automatisch auf Ihrer Runbook Worker-Instanz zu installieren. Befolgen Sie die Empfehlungen in Azure Security Center: „Compute &amp; Apps“, um sicherzustellen, dass alle Endpunkte mit den neuesten Signaturen auf dem aktuellen Stand sind. Das Windows-Betriebssystem kann durch zusätzliche Sicherheitsmaßnahmen noch besser geschützt werden, indem das Risiko durch Viren oder auf Schadsoftware basierende Angriffe mit dem Microsoft Defender Advanced Threat Protection-Dienst begrenzt wird, der mit Azure Security Center integriert ist.

* [Bereitstellen von Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Azure Automation-Konten und zugehörige Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von Azure Automation-Konten und zugehöriger Ressourcen verwendet werden können. Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen.

Halten Sie die Runbooks mit der Funktion zur Integration der Quellcodeverwaltung in Ihrem Automation-Konto auf demselben aktuellen Stand wie die Skripts in Ihrem Quellcodeverwaltungrepository.

* [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager-Vorlagenreferenz für Azure Automation-Ressourcen](/azure/templates/microsoft.automation/allversions)

* [Erstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](./quickstart-create-automation-account-template.md)

* [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

* [Einführung in Azure Automation](./automation-intro.md)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Verwenden der Integration der Quellcodeverwaltung](./source-control-integration.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Azure Automation-Konten und zugehörige Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit, Vorlagen zu exportieren, die als Sicherungen für die Wiederherstellung von Azure Automation-Konten und zugehöriger Ressourcen verwendet werden können. Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault. Sie können Ihre Runbooks mithilfe von Azure-Portal oder PowerShell in Skriptdateien exportieren.

* [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager-Vorlagenreferenz für Azure Automation-Ressourcen](/azure/templates/microsoft.automation/allversions)

* [Erstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](./quickstart-create-automation-account-template.md)

* [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

* [Einführung in Azure Automation](./automation-intro.md)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure-Datensicherung für Automation-Konten](./automation-managing-data.md#data-backup)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Sicherstellen der Fähigkeit, die Bereitstellung von Azure Resource Manager-Vorlagen bei Bedarf regelmäßig in einem isolierten Abonnement auszuführen. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Verwenden Sie Azure DevOps zum Speichern und Verwalten Ihres Codes wie z. B. Azure Resource Manager-Vorlagen. Um die Ressourcen, die Sie in Azure DevOps verwalten, zu schützen, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory definierten Gruppen (bei Integration in Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration in TFS) Berechtigungen erteilen oder verweigern.

Halten Sie die Runbooks mit der Funktion zur Integration der Quellcodeverwaltung in Ihrem Automation-Konto auf demselben aktuellen Stand wie die Skripts in Ihrem Quellcodeverwaltungrepository.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Verwenden der Integration der Quellcodeverwaltung](./source-control-integration.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht verfügbar

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

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen.

* [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass Ihre Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

* [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).