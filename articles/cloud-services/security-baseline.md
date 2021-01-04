---
title: Azure-Sicherheitsbaseline für Azure Cloud Services
description: Die Azure Cloud Services-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 100871ab2188aa638c4c9f2b058021706c7741bb
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531903"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure-Sicherheitsbaseline für Azure Cloud Services

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 1.0](../security/benchmarks/overview-v1.md) auf Microsoft Azure Cloud Services an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure.
Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure Cloud Services geltenden Empfehlungen definiert werden. Nicht auf Azure Cloud Services anwendbare **Kontrollen** wurden ausgeschlossen.

 
Die vollständige Zuordnung von Azure Cloud Services zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Cloud Services-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Erstellen Sie ein klassisches Azure Virtual Network mit getrennten öffentlichen und privaten Subnetzen, um die Isolierung auf der Grundlage vertrauenswürdiger Ports und IP-Bereiche durchzusetzen. Bei diesem virtuellen Netzwerk und den Subnetzen muss es sich um die auf dem klassischen virtuellen Netzwerk (klassische Bereitstellung) basierenden Ressourcen handeln und nicht um die aktuellen Azure Resource Manager-Ressourcen.  

Sie müssen den Datenverkehr unter Verwendung einer Netzwerksicherheitsgruppe zulassen bzw. verweigern, die Regeln zur Zugriffssteuerung basierend auf der Richtung des Datenverkehrs, des Protokolls, der Quelladresse und des Quellports sowie der Zieladresse und des Zielports enthält. Die Regeln für eine Netzwerksicherheitsgruppe können jederzeit geändert werden, und die Änderungen werden auf alle zugeordneten Instanzen angewendet.

Microsoft Azure Cloud Services (klassisch) kann nicht in virtuelle Azure Resource Manager-Netzwerke platziert werden. Virtuelle, auf Resource Manager basierende Netzwerke und virtuelle Netzwerke, die auf klassischen Bereitstellungen basieren, können aber über Peering verbunden werden. 

- [Netzwerksicherheitsgruppe – Übersicht](../virtual-network/network-security-groups-overview.md)

- [Peering virtueller Netzwerke](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Dokumentieren Sie Ihre Azure Cloud Services-Konfiguration, und überwachen Sie sie auf Änderungen. Verwenden Sie die Konfigurationsdatei des Diensts, um die Anzahl der Rolleninstanzen, die für jede Rolle im Dienst bereitgestellt werden, die Werte aller Konfigurationseinstellungen und die Fingerabdrücke für alle einer Rolle zugeordneten Zertifikate festzulegen. 

Wenn der Dienst Teil eines virtuellen Netzwerks ist, müssen Konfigurationsinformationen für das Netzwerk in der Dienstkonfigurationsdatei sowie in der Konfigurationsdatei für virtuelle Netzwerke bereitgestellt werden. Die Standarderweiterung für die Dienstkonfigurationsdatei ist .cscfg. Beachten Sie, dass Azure Policy nicht für klassische Bereitstellungen zur Konfigurationsdurchsetzung unterstützt wird.

Legen Sie die Konfigurationswerte eines Clouddiensts in der Dienstkonfigurationsdatei (.cscfg) und die Definition in einer Dienstdefinitionsdatei (.csdef) fest. Verwenden Sie die Dienstdefinitionsdatei zum Definieren des Dienstmodells für eine Anwendung. Definieren Sie die Rollen, die für einen Clouddienst verfügbar sind, und geben Sie auch die Dienstendpunkte an. Protokollieren Sie die Konfiguration für Azure Cloud Services mit der Dienstkonfigurationsdatei. Jede Neukonfiguration kann über die Datei „ServiceConfig.cscfg“ durchgeführt werden. 

Überwachen Sie die Dienstdefinition des optionalen NetworkTrafficRules-Elements, mit dem eingeschränkt werden kann, welche Rollen mit angegebenen internen Endpunkten kommunizieren können. Konfigurieren Sie den Knoten „NetworkTrafficRules“, ein optionales Element in der Dienstdefinitionsdatei, um festzulegen, wie die Rollen miteinander kommunizieren sollen. Legen Sie Begrenzungen dafür fest, welche Rollen auf die internen Endpunkte der bestimmten Rolle zugreifen können.  Beachten Sie, dass die Dienstdefinition nicht geändert werden kann. 

Aktivieren Sie Netzwerksicherheitsgruppen-Datenflussprotokolle, und senden Sie Protokolle zur Überwachung an ein Azure Storage-Konto. Senden Sie die Datenflussprotokolle auch an einen Log Analytics-Arbeitsbereich, und verwenden Sie Traffic Analytics, um Einblicke in die Datenverkehrsmuster in Ihrem Azure-Mandanten zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren, Hotspots und Sicherheitsbedrohungen zu erkennen, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../azure-resource-manager/management/deployment-models.md)

- [Azure Cloud Services-Konfigurationsdatei](schema-cscfg-file.md)

- [Liste der Dienste, die von Azure Policy unterstützt werden](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Microsoft verwendet das Protokoll TLS v1.2 (Transport Layer Security) zum Schutz von Daten bei der Übertragung zwischen Azure Cloud Services und Kunden. Die Microsoft-Rechenzentren verhandeln eine TLS-Verbindung mit Clientsystemen, die eine Verbindung mit Azure-Diensten herstellen. TLS bietet strenge Authentifizierung, Datenschutz von Nachrichten und Integrität (ermöglicht die Erkennung von Manipulation, Abfangen und Fälschung von Nachrichten), Interoperabilität, Algorithmusflexibilität sowie einfache Bereitstellung und Verwendung.

- [Grundlagen der Verschlüsselung](../security/fundamentals/encryption-overview.md)

- [Konfigurieren von TLS/SSL-Zertifikaten](cloud-services-configure-ssl-certificate-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Azure Cloud implementiert mehrstufige Netzwerksicherheit zum Schutz von Plattformdiensten gegen verteilte Denial-of-Services-Angriffe (DDoS). Azure DDoS Protection ist Teil des ständigen Überwachungsprozesses von Azure Cloud, der durch Penetrationstests kontinuierliche verbessert wird. Dieser DDoS-Schutz ist darauf ausgelegt, nicht nur Angriffen von außerhalb, sondern auch von anderen Azure-Mandanten standzuhalten. 

Es gibt neben dem Schutz auf Plattformebene innerhalb von Azure Cloud Services verschiedene Möglichkeiten, die Kommunikation zu blockieren oder zu verweigern. Dies sind: 

-  Erstellen von Starttasks, um einige bestimmte IP-Adressen selektiv zu blockieren
-  Beschränken des Zugriffs einer Azure-Webrolle auf einen Satz angegebener IP-Adressen durch Ändern der IIS-Datei „web.config“

Verhindern, dass Datenverkehr bei der Standard-URL dem Namen von Azure Cloud Services (z. B. *.cloudapp.net) eingeht. Legen Sie für den Hostheader in der Azure Cloud Services-Definitionsdatei (* .csdef) unter der Websitebindungskonfiguration einen benutzerdefinierten DNS-Namen fest.

Konfigurieren Sie eine Verweigern/Übernehmen-Regel für Zuweisungen für klassische Abonnementadministratoren. Standardmäßig ist der Kommunikationsfluss von beliebigen Rollen zum internen Endpunkt einer Rolle ohne Einschränkungen möglich, nachdem ein interner Endpunkt definiert wurde. Um die Kommunikation einzuschränken, müssen Sie in der Dienstdefinitionsdatei dem ServiceDefinition-Element ein NetworkTrafficRules-Element hinzufügen.

- [Wie kann ich den bei der Standard-URL des Clouddiensts eingehenden Datenverkehr blockieren/deaktivieren?](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDoS Protection](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Blockieren bestimmter IP-Adressen](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Verwenden Sie Azure Network Watcher, einen Dienst zur Überwachung, Diagnose und Analyse der Netzwerkleistung, der die Überwachung von Azure-Netzwerken ermöglicht. Die VM-Erweiterung des Network Watcher-Agents ist eine Voraussetzung für die bedarfsgesteuerte Erfassung von Netzwerkdatenverkehr und andere erweiterte Funktionen in Azure Virtual Machines. Installieren Sie die VM-Erweiterung des Network Watcher-Agents, und aktivieren Sie die Datenflussprotokolle für Netzwerksicherheitsgruppen.

Konfigurieren Sie die Datenflussprotokollierung für eine Netzwerksicherheitsgruppe. Lesen Sie die detaillierten Informationen zur Bereitstellung der VM-Erweiterung von Network Watcher für einen vorhandenen, über das klassische Bereitstellungsmodell bereitgestellten virtuellen Computer.

- [Konfigurieren der Datenflussprotokollierung für eine Netzwerksicherheitsgruppe](../virtual-machines/extensions/network-watcher-linux.md)

- [Weitere Informationen zum Konfigurieren von Datenflussprotokollen finden Sie unter ](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Azure Cloud Services verfügt über keine integrierten IDS- oder IPS-Funktionen. Kunden können basierend auf ihren organisatorischen Anforderungen eine zusätzliche netzwerkbasierte IDS- oder IPS-Lösung aus dem Azure Marketplace auswählen und bereitstellen. Werden Lösungen von Drittanbietern verwendet, stellen Sie sicher, dass Sie Ihre ausgewählten IDS- oder IPS-Lösung mit Azure Cloud Services gründlich testen, um den ordnungsgemäßen Betrieb und die ordnungsgemäße Funktion sicherzustellen.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Dienstzertifikate, die an Azure Cloud Services angefügt sind, ermöglichen die sichere Kommunikation zu und von den Diensten. Diese Zertifikate werden in der Definition des Diensts definiert und automatisch auf dem virtuellen Computer bereitgestellt, auf dem eine Instanz der Webrolle ausgeführt wird. Sie können beispielsweise für eine Webrolle ein Dienstzertifikat zum Authentifizieren eines verfügbar gemachten HTTPS-Endpunkts verwenden. 

Um das Zertifikat zu aktualisieren, muss lediglich ein neues Zertifikat hochgeladen und der Fingerabdruckwert in der Dienstkonfigurationsdatei geändert werden.

Verwenden Sie das TLS 1.2-Protokoll, die am häufigsten verwendete Methode zur Absicherung von Daten, um Vertraulichkeit und Integritätsschutz zu gewährleisten. 

Generell können Sie zum Schutz von Webanwendungen und zur Absicherung gegen Angriffe wie OWASP Top 10 ein Azure Web Application Firewall-aktiviertes Azure Application Gateway zum Schutz von Webanwendungen einsetzen. 

- [Dienstzertifikate](cloud-services-certs-create.md)

- [Konfigurieren von TLS für eine Anwendung in Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Bereitstellen von Application Gateway](../application-gateway/quick-create-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Härten Sie Ihre Azure Cloud Services-Konfiguration, und überwachen Sie sie auf Änderungen. Die Dienstkonfigurationsdatei gibt die Anzahl der Rolleninstanzen, die für jede Rolle im Dienst bereitgestellt werden, die Werte aller Konfigurationseinstellungen und die Fingerabdrücke für alle einer Rolle zugeordneten Zertifikate an. 

Wenn Ihr Dienst Teil eines virtuellen Netzwerks ist, müssen die Konfigurationsinformationen für das Netzwerk in der Dienstkonfigurationsdatei sowie in der Konfigurationsdatei für virtuelle Netzwerke bereitgestellt werden. Die Standarderweiterung für die Dienstkonfigurationsdatei ist .cscfg.

Beachten Sie, dass Azure Policy nicht mit Azure Cloud Services zur Konfigurationsdurchsetzung unterstützt wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Azure-Netzwerksicherheitsgruppen können verwendet werden, um Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem Azure Virtual Network zu filtern. Eine Netzwerksicherheitsgruppe enthält Sicherheitsregeln, die eingehenden Netzwerkdatenverkehr an verschiedene Typen von Azure-Ressourcen oder ausgehenden Netzwerkdatenverkehr von diesen zulassen oder verweigern. Für jede Regel können Sie die Quelle, das Ziel, den Port und das Protokoll angeben.

Verwenden Sie das Feld „Beschreibung“ für einzelne Netzwerksicherheitsgruppen-Regeln in Azure Cloud Services, um die Regeln zu dokumentieren, die Datenverkehr zu oder aus einem Netzwerk zulassen.

- [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen-Regeln](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie die integrierte Features zur Endpunktüberwachung und zum automatischen Endpunktfailover von Azure Traffic Manager. Dies unterstützt Sie bei der Bereitstellung von Anwendungen mit hoher Verfügbarkeit, die auch Ausfälle von Endpunkten Azure-Regionen überstehen. Zum Konfigurieren der Endpunktüberwachung müssen Sie bestimmte Einstellungen in Ihrem Traffic Manager-Profil angeben.

Sammeln Sie Einblicke in Ereignisse auf Abonnementebene aus dem Aktivitätsprotokoll, einem Plattformprotokoll in Azure. Dies sind beispielsweise Informationen wie das Ändern einer Ressource oder das Starten eines virtuellen Computers. Zeigen Sie das Aktivitätsprotokoll im Azure-Portal an, oder rufen Sie Einträge mit PowerShell und der CLI ab. 

Erstellen Sie eine Diagnoseeinstellung, mit der das Aktivitätsprotokoll an Azure Monitor, an Azure Event Hubs zur Weiterleitung außerhalb von Azure oder an Azure Storage zur Archivierung gesendet wird. Konfigurieren Sie Azure Monitor für Benachrichtigungen, wenn wichtige Ressourcen in Ihren Azure Cloud Services geändert werden. 

- [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-log.md)

- [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Traffic Manager-Endpunktüberwachung](../traffic-manager/traffic-manager-monitoring.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure-Ressourcen für Azure Cloud Services. Kunden müssen möglicherweise eine Netzwerkregel erstellen, um den Zugriff auf einen in ihrer Umgebung verwendeten Zeitserver über Port 123 mit UDP-Protokoll zu ermöglichen.

- [NTP-Serverzugriff](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Mit Azure Event Hubs können Sie die Streamingdaten Ihres Clouddiensts programmgesteuert nutzen. Integrieren und senden Sie all diese Daten an Azure Sentinel, um Ihre Protokolle zu überwachen und zu überprüfen, oder verwenden Sie ein Drittanbieter-SIEM. Konfigurieren Sie für die zentrale Verwaltung des Sicherheitsprotokolls den fortlaufenden Export der ausgewählten Azure Security Center-Daten in Azure Event Hubs und richten Sie den entsprechenden Connector für Ihre SIEM-Lösung ein. Hier finden Sie einige Optionen für Azure Sentinel, einschließlich Tools von Drittanbietern:

- Azure Sentinel – Verwenden Sie den nativen Datenconnector für Azure Security Center-Warnungen
- Splunk – Verwenden Sie das Azure Monitor-Add-On für Splunk
- IBM QRadar: Verwenden Sie eine manuell konfigurierte Protokollquelle
- ArcSight: Verwenden Sie SmartConnector

Weitere Informationen zu verfügbaren Connectors mit Azure Sentinel finden Sie in der Azure Sentinel-Dokumentation. 

- [Herstellen einer Verbindung mit Datenquellen](../sentinel/connect-data-sources.md)

- [Integration in eine SIEM-Lösung](../security-center/continuous-export.md)

- [Speichern von Diagnosedaten](diagnostics-extension-to-storage.md)

- [Konfigurieren der SIEM-Integration über Azure Event Hubs](../security-center/continuous-export.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Konfigurieren Sie Visual Studio, um Azure-Diagnose zur Problembehandlung bei Azure Cloud Services einzurichten, das System- und Protokolldaten auf virtuellen Computern erfasst, einschließlich der VM-Instanzen, auf denen Ihre Azure Cloud Services ausgeführt werden. Die Diagnosedaten werden in ein Speicherkonto Ihrer Wahl übertragen. Aktivieren Sie die Diagnose in Azure Cloud Services-Projekten vor deren Bereitstellung.

 
Zeigen Sie den Änderungsverlauf für bestimmte Ereignisse im Aktivitätsprotokoll in Azure Monitor an. Überprüfen Sie, welche Änderungen während eines Ereigniszeitraums aufgetreten sind. Wählen Sie ein Ereignis aus dem Aktivitätsprotokoll für eine weitergehende Untersuchung über die Registerkarte „Änderungsverlauf (Vorschau)“ aus. Senden Sie die Diagnosedaten an Application Insights, wenn Sie eine Azure Cloud Services-Instanz von Visual Studio aus veröffentlichen. Erstellen Sie die Azure-Ressource von Application Insights zu diesem Zeitpunkt, oder senden Sie die Daten an eine vorhandene Azure-Ressource. 

Azure Cloud Services kann von Application Insights auf Verfügbarkeit, Leistung, Fehler und Auslastung überwacht werden. Application Insights können benutzerdefinierte Diagramme hinzugefügt werden. So haben Sie immer die relevanten Daten im Blick. Rolleninstanzdaten können mithilfe des Application Insights SDKs in Ihrem Azure Cloud Services-Projekt erfasst werden. 

- [Aktivieren der Diagnose in Visual Studio vor der Bereitstellung](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Anzeigen des Änderungsverlaufs](../azure-monitor/platform/activity-log.md#view-change-history)

- [Application Insights für Azure Cloud Services (klassisch)](../azure-monitor/app/cloudservices.md)

- [Einrichten der Diagnose für Azure Cloud Services (klassisch) und virtuelle Azure-Computer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Sie können die erweiterte Überwachung mit Azure Cloud Services verwenden, mit der Sie in Intervallen von fünf Minuten, einer Stunde und zwölf Stunden zusätzliche Metriken abfragen und erfassen können. Die aggregierten Daten werden unter einem Speicherkonto in Tabellen gespeichert und nach zehn Tagen gelöscht. Das verwendete Speicherkonto wird jedoch nach der Rolle konfiguriert, und Sie können verschiedene Speicherkonten für verschiedene Rollen verwenden. Die Konfiguration erfolgt mithilfe einer Verbindungszeichenfolge in der CSDEF- und CSCFG-Datei.

Beachten Sie, dass bei der erweiterten Überwachung die Erweiterung Azure-Diagnose (und optional das Application Insights-SDK) für die zu überwachende Rolle verwendet wird. Die Diagnoseerweiterung verwendet (pro Rolle) eine Konfigurationsdatei mit dem Namen diagnostics.wadcfgx zur Konfiguration der überwachten Diagnosemetriken. Die Azure-Diagnoseerweiterung sammelt und speichert Daten in einem Azure Storage-Konto. Diese Einstellungen werden in den WADCFGX-, CSDEF- und CSCFG-Dateien konfiguriert.

- [Einführung in die Überwachung von Clouddiensten](cloud-services-how-to-monitor.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Für Azure Cloud Services sind grundlegende oder erweiterte Überwachungsmodi verfügbar. Azure Cloud Services sammelt automatisch grundlegende Überwachungsdaten (CPU-Prozentsatz, Netzwerk-E/A sowie Lese-/Schreibzugriff auf dem Datenträger) von einem virtuellen Hostcomputer. Die erfassten Überwachungsdaten werden auf den Übersichts- und Metrikseiten eines Clouddiensts im Azure-Portal angezeigt. 

Aktivieren Sie die Diagnose in Azure Cloud Services, um Diagnosedaten wie Anwendungsprotokolle, Leistungsindikatoren und mehr zu sammeln, während Sie die Erweiterung Azure-Diagnose verwenden. Aktivieren oder aktualisieren Sie die Diagnosekonfiguration für einen bereits ausgeführten Clouddienst mit dem Cmdlet „Set-AzureServiceDiagnosticsExtension“, oder stellen Sie einen Clouddienst mit Diagnoseerweiterung automatisch bereit. Installieren Sie optional das Application Insights SDK. Senden Sie Leistungsindikatoren an Azure Monitor.

Die Azure-Diagnoseerweiterung sammelt und speichert Daten in einem Azure Storage-Konto. Übertragen Sie Diagnosedaten an den Microsoft Azure-Speicheremulator oder an Azure Storage, da diese nicht dauerhaft gespeichert werden. Die Daten im Speicher können mit einem von mehreren verfügbaren Tools angezeigt werden, z. B. mit Server-Explorer in Visual Studio, Microsoft Azure Storage-Explorer, Azure Management Studio. Konfigurieren Sie die Diagnosemetriken so, dass sie (pro Rolle) mit einer Konfigurationsdatei mit dem Namen „diagnostics.wadcfgx“ in der Diagnoseerweiterung überwacht werden. 

- [Einführung in die Überwachung von Clouddiensten](cloud-services-how-to-monitor.md)

- [Aktivieren der Diagnose in einer Workerrolle – Integrieren in ein SIEM](../security-center/continuous-export.md)

- [Aktivieren der Diagnose mithilfe von PowerShell in Azure Cloud Services](cloud-services-diagnostics-powershell.md)

- [Speichern und Anzeigen von Diagnosedaten in Azure Storage](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Sie können die Protokolldaten von Azure Cloud Services durch die Integration mit Azure Sentinel oder mit einem Drittanbieter-SIEM überwachen, indem Sie Warnungen bei anomalen Aktivitäten aktivieren.

- [Integration in eine SIEM-Lösung](../security-center/continuous-export.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Microsoft Antimalware für Azure schützt Azure Cloud Services und virtuelle Computer. Darüber hinaus können Sie zusätzlich Sicherheitslösungen von Drittanbietern bereitstellen, z.B. Web Application Firewalls, Netzwerkfirewalls, Antischadsoftware, Angriffserkennungs- und Eindringschutzsysteme (IDS oder IPS) und weitere.

- [Welche Features und Funktionen bieten grundlegende Azure-IPS/IDS und -DDoS?](./cloud-services-configuration-and-management-faq.md?preserve-view=true#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Microsoft empfiehlt das Verwalten des Zugriffs auf Azure-Ressourcen mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC). Azure Cloud Services unterstützt das Azure RBAC-Modell jedoch nicht, da es sich nicht um einen auf Azure Resource Manager basierenden Dienst handelt und Sie ein klassisches Abonnement verwenden müssen.

Kontoadministrator, Dienstadministrator und Co-Administrator sind standardmäßig die drei klassischen Administratorrollen für ein Abonnement in Azure. 

Administratoren für klassische Abonnements verfügen über Vollzugriff auf das Azure-Abonnement. Sie können Ressourcen über das Azure-Portal, Azure Resource Manager-APIs und APIs des klassischen Azure-Bereitstellungsmodells verwalten. Das Konto, das zum Registrieren für Azure verwendet wird, wird automatisch sowohl als Kontoadministrator als auch als Dienstadministrator eingerichtet. Zusätzliche Co-Administratoren können später hinzugefügt werden. 

Der Dienstadministrator und die Co-Administratoren haben gleichwertigen Zugriff wie Benutzer, denen für den Abonnementbereich die Rolle „Besitzer“ (eine Azure-Rolle) zugewiesen wurde. Auf der Registerkarte „Klassische Administratoren“ im Azure-Portal können Sie Co-Administratoren verwalten oder den Dienstadministrator anzeigen. 

Listen Sie die Rollenzuweisungen für klassische Dienstadministratoren und -Co-Administratoren mit PowerShell mit dem folgenden Befehl auf:

Get-AzRoleAssignment -IncludeClassicAdministrators

Überprüfen Sie die Unterschiede zwischen den klassischen Administratorrollen für Abonnements. 

- [Unterschiede zwischen drei klassischen Abonnementadministratorrollen](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Es wird empfohlen, Standardbetriebsverfahren für die Verwendung dedizierter Verwaltungskonten zu erstellen, die auf den verfügbaren Rollen und den für den Betrieb und die Verwaltung der Azure Cloud Services-Ressourcen erforderlichen Berechtigungen basieren.

- [Unterschiede zwischen den klassischen Abonnementadministratorrollen](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Vermeiden Sie die Verwaltung separater Identitäten für Anwendungen, die unter Azure Cloud Services ausgeführt werden. Implementieren Sie Einmaliges Anmelden, um zu vermeiden, dass Benutzer mehrere Identitäten und Anmeldeinformationen verwalten müssen.

- [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Es empfiehlt sich, für Verwaltungsaufgaben, für die erhöhte Rechte benötigt werden, eine sichere, von Azure verwaltete Arbeitsstation (auch als Arbeitsstation mit privilegiertem Zugriff, Privileged Access Workstation bezeichnet) zu verwenden.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)

- [Planen einer Bereitstellung von Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie die REST-APIs von Azure Cloud Services, um eine Inventur Ihrer Azure Cloud Services-Ressourcen für vertrauliche Informationen durchzuführen. Rufen Sie die bereitgestellten Clouddienstressourcen ab, um die Konfiguration und PKG-Ressourcen zu erhalten.

 Als Beispiel sind unten einige APIs aufgeführt:

- Get Deployment – Durch den Get Deployment-Vorgang werden Konfigurationsinformationen, Status und Systemeigenschaften für eine Bereitstellung zurückgegeben.
- Get Package – Der Get Package-Vorgang ruft ein Clouddienstpaket für eine Bereitstellung ab und speichert die Paketdateien im Microsoft Azure-Blobspeicher.
- Get Cloud Service Properties – Der Get Cloud Service Properties-Vorgang ruft Eigenschaften für den angegebenen Clouddienst ab.

Lesen Sie die Dokumentation zu den REST-APIs für Azure Cloud Services, und erstellen Sie basierend auf den Anforderungen Ihrer Organisation ein Verfahren für den Datenschutz vertraulicher Informationen.

- [Get Deployment](/rest/api/compute/cloudservices/rest-get-deployment)

- [Abrufen von Cloud-Diensteigenschaften](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Get Package](/rest/api/compute/cloudservices/rest-get-package)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe für Azure Cloud Services.

Sie können auch das „permissionLevel“-Objekt im Zertifikatselement von Azure Cloud Services bearbeiten, um die Zugriffsberechtigungen für die Rollenprozesse festzulegen. Geben Sie die Berechtigung „elevated“ an, wenn nur Prozesse mit erhöhten Rechten auf den privaten Schlüssel zugreifen sollen. Mit der Berechtigung „limitedOrElevated“ können alle Rollenprozesse auf den privaten Schlüssel zugreifen. Mögliche Werte sind „limitedOrElevated“ oder „elevated“. Der Standardwert ist „limitedOrElevated“.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [WebRole-Schema](schema-csdef-webrole.md#Certificate)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Es empfiehlt sich, eine Drittanbieterlösung aus Azure Marketplace an Netzwerkumkreisen zu verwenden, um die nicht autorisierte Übertragung von vertraulichen Informationen zu überwachen, derartige Übertragungen zu blockieren und gleichzeitig die Informationssicherheitsexperten zu benachrichtigen.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Konfigurieren Sie TLS v2 für Azure Cloud Services. Verwenden Sie das Azure-Portal, um das Zertifikat zu ihrer gestaffelten Azure Cloud Services-Bereitstellung hinzuzufügen, und fügen Sie die Zertifikatinformationen zu den CSDEF- und CSCFG-Dateien der Dienste hinzu. Packen Sie Ihre Anwendung neu, und aktualisieren Sie Ihre gestaffelte Bereitstellung, um das neue Paket zu verwenden. 

Verwenden Sie Dienstzertifikate in Azure, die an Azure Cloud Services angefügt werden, um die sichere Kommunikation zu und von den Diensten zu ermöglichen. Stellen Sie ein Zertifikat bereit, das einen verfügbar gemachten HTTPS-Endpunkt authentifizieren kann. Definieren Sie Dienstzertifikate in der Dienstdefinition des Clouddiensts, und stellen Sie sie automatisch auf dem virtuellen Computer bereit, auf dem eine Instanz Ihrer Rolle ausgeführt wird.

Authentifizierung mit der Management-API mit Verwaltungszertifikaten: Verwaltungszertifikate ermöglichen Ihnen die Authentifizierung beim klassischen Bereitstellungsmodell. Diese Zertifikate werden in vielen Programmen und Tools (z. B. Visual Studio oder das Azure SDK) zum Automatisieren der Konfiguration und Bereitstellung verschiedener Azure-Dienste verwendet. 

Als zusätzliche Referenz bietet die Azure-Dienstverwaltungs-API programmgesteuerten Zugriff auf die im Azure-Portal verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure SDK für Python können Sie Azure Cloud Services- und Azure Storage-Konten verwalten. Das Azure SDK für Python umfasst die Dienstverwaltungs-API (eine REST-API). Alle API-Vorgänge werden über TLS ausgeführt und mithilfe von X.509 v3-Zertifikaten gegenseitig authentifiziert. Auf den Verwaltungsdienst kann von einem Dienst, der in Azure ausgeführt wird, zugegriffen werden. Der Zugriff kann auch direkt über das Internet erfolgen, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

- [Konfigurieren von TLS für eine Anwendung in Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Verwenden der Dienstverwaltung aus Python](cloud-services-python-how-to-use-service-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Es empfiehlt sich, ein aktives Ermittlungstool eines Drittanbieters zu verwenden, um alle vertraulichen Informationen zu bestimmen, die von den Technologiesystemen der Organisation gespeichert, verarbeitet oder übertragen werden, einschließlich der lokalen oder der bei einem Remotedienstanbieter befindlichen Systeme, und dann den Bestand an vertraulichen Informationen des Unternehmens zu aktualisieren.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Gilt nicht für Azure Cloud Services (klassisch). Die Verhinderung von Datenverlusten wird nicht erzwungen.

Es empfiehlt sich, ein Drittanbietertool, z. B. eine automatisierte hostbasierte Lösung für die Verhinderung von Datenverlust, zu implementieren, um Zugriffssteuerungen für Daten zu erzwingen, auch wenn Daten aus einem System heraus kopiert werden.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Cloud Services unterstützt keine Verschlüsselung ruhender Daten. Das liegt daran, dass Azure Cloud Services als zustandsloser Dienst konzipiert ist. Azure Cloud Services unterstützt externen Speicher, z. B. Azure Storage, der standardmäßig im Ruhezustand verschlüsselt ist.  

Die auf temporären Datenträgern gespeicherten Anwendungsdaten werden nicht verschlüsselt. Der Kunde ist dafür verantwortlich, diese Daten zu verwalten und bei Bedarf zu verschlüsseln.  

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Sie können klassische Metrikwarnungen in Azure Monitor verwenden, um benachrichtigt zu werden, wenn eine Ihrer auf kritische Ressourcen angewendeten Metriken einen Schwellenwert überschreitet. Klassische Metrikwarnungen sind eine ältere Funktionalität, die es ermöglicht, nur bei nichtdimensionalen Metriken zu warnen. Es gibt eine neuere Funktionalität namens Metrikwarnungen, die die Funktionalität gegenüber klassischen Metrikwarnungen verbessert hat. 

Application Insights kann Azure Cloud Services-Apps außerdem auf Verfügbarkeit, Leistung, Fehler und Auslastung überwachen. Dabei werden kombinierte Daten aus Application Insights-SDKs mit Daten von Azure-Diagnose aus Ihrer Azure Cloud Services-Instanz verwendet.

- [Erstellen, Anzeigen und Verwalten von klassischen Metrikwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-classic-portal.md)

- [Übersicht zu Metrikwarnungen](../azure-monitor/platform/alerts-metric-overview.md) 

- [Application Insights für Azure Cloud Services (klassisch)](../azure-monitor/app/cloudservices.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Beachten Sie, dass sich diese Informationen auf das Azure-Gastbetriebssystem für Azure Cloud Services-Mitarbeiter sowie Webrollen mit Platform-as-a-Service (PaaS) beziehen. Sie gelten jedoch nicht für Azure Virtual Machines mit Infrastructure-as-a-Service (IaaS).

Das Gastbetriebssystem Ihres Kunden wird von Azure standardmäßig in regelmäßigen Abständen auf das neueste unterstützte Image innerhalb der Betriebssystemfamilie aktualisiert, die der Kunde in seiner Dienstkonfiguration (CSCFG-Datei) angegeben hat, z. B. Windows Server 2016.

Wenn ein Kunde eine bestimmte Betriebssystemversion für seine Azure Cloud Services-Bereitstellung auswählt, werden die automatischen Betriebssystemupdates deaktiviert und das Patching geht in seine Verantwortung über. Der Kunde muss sicherstellen, dass seien Rolleninstanzen Updates erhalten, ansonsten ist seien Anwendungen unter Umständen Sicherheitsrisiken ausgesetzt.

- [Azure-Gastbetriebssystem](cloud-services-guestos-msrc-releases.md)

- [Unterstützungs- und Deaktivierungsrichtlinie für Azure-Gastbetriebssysteme](cloud-services-guestos-retirement-policy.md)

- [Konfigurieren von Clouddiensten (klassisch)](cloud-services-how-to-configure-portal.md)

- [Verwalten der Gastbetriebssystem-Version](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Verwenden Sie eine Lösung für die Patchverwaltung von Drittanbietern. Kunden, die bereits Configuration Manager in ihrer Umgebung verwenden, können auch System Center Updates Publisher verwenden, sodass sie benutzerdefinierte Updates in Windows Server Update Service veröffentlichen können. 

Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern patchen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Der Kunde sollte sein Risiko durch DDoS-Angriffe durchgehend einschätzen können. 

Wir empfehlen, die folgenden Szenarien zu betrachten:

- Welche neuen, öffentlich verfügbaren Azure-Ressourcen benötigen Schutz?
- Gibt es Single Points of Failure im Dienst?
- Wie können Dienste isoliert werden, um die Auswirkungen eines Angriffs einzuschränken, aber die Dienste legitimen Kunden weiterhin verfügbar zu machen?
- Gibt es virtuelle Netzwerke, in denen DDoS Protection Standard aktiviert werden sollte, aber noch nicht aktiviert ist?
- Sind meine Dienste mehrere Regionen übergreifend aktiv / aktiv mit Failover?

Unterstützende Dokumentation:

- [Risikobewertung für Ihre Azure-Ressourcen](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Gilt nicht für Azure Cloud Services. Diese Empfehlung gilt für IaaS-Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Sie sollten den Bestand regelmäßig abstimmen und sicherstellen, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Der Kunde sollte genehmigte Azure-Ressourcen und genehmigte Software für Computeressourcen definieren.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:
- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt
- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung
- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps
- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind
- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:

- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt

- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung

- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps

- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind

- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:

- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt

- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung

- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps

- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind

- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:

- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt

- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung

- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps

- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind

- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:
- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt
- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung
- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps
- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind
- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Verwenden Sie das Feature „Adaptive Anwendungssteuerung“, das im Azure Security Center verfügbar ist. Dabei handelt es sich um eine intelligente, automatisierte End-to-End-Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren Windows- und Linux-Computern sowie Computern mit oder ohne Azure ausgeführt werden können. Sie trägt außerdem dazu bei, Ihre Computer gegen Malware zu schützen. 

Dieses Feature steht sowohl für Windows- (alle Versionen, klassisch oder Azure Resource Manager) und Linux-Computer und -Server mit oder ohne Azure zur Verfügung.

Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren, und erstellt basierend auf diesen Daten eine Zulassungsliste. Dadurch können Sie Richtlinien für Anwendungszulassungsliste deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:

- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)

- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt

- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung

- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps

- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind

- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

Weitere Informationen finden Sie unter den angegebenen Links.

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Implementieren Sie für vertrauliche oder mit hohem Risiko behaftete Anwendungen mit Azure Cloud Services separate Abonnements oder Verwaltungsgruppen, um für Isolation zu sorgen.

Verwenden Sie eine Netzwerksicherheitsgruppe, erstellen Sie eine Eingangssicherheitsregel, wählen Sie einen Dienst wie HTTP und auch einen benutzerdefinierten Port aus, geben Sie ihm eine Priorität und einen Namen. Die Priorität wirkt sich auf die Reihenfolge aus, in der die Regeln angewendet werden: je niedriger der numerische Wert, desto früher wird die Regel angewendet. Sie müssen Ihre Netzwerksicherheitsgruppe einem Subnetz oder einer bestimmten Netzwerkschnittstelle zuordnen, um den Netzwerkdatenverkehr entsprechend Ihren Geschäftsanforderungen zu isolieren oder zu segmentieren.

Weitere Informationen finden Sie unter den angegebenen Links.

- [Tutorial – Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure Cloud Services-Ressourcen. 

Wählen Sie im Azure-Portal „Security Center“ > „Compute &amp; Apps“ < „Azure Cloud Services“ aus, um die für Ihre Dienstressourcen geltenden Empfehlungen anzuzeigen.

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Gilt nicht für Azure Cloud Services. Basiert auf dem klassischen Bereitstellungsmodell. Es wird empfohlen, eine Drittanbieterlösung für die Verwaltung sicherer Azure-Ressourcenkonfigurationen zu verwenden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Die Konfigurationsdatei von Azure Cloud Services speichert die Betriebsattribute für eine Ressource. Sie können eine Kopie der Konfigurationsdateien in einem sicheren Speicherkonto speichern.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Gilt nicht für Azure Cloud Services. Basiert auf dem klassischen Bereitstellungsmodell und kann nicht mit den auf der Azure Resource Manager-Bereitstellung basierenden Konfigurationstools verwaltet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Gilt nicht für Azure Cloud Services. Diese Empfehlung gilt für auf Infrastructure-as-a-Service (IaaS) basierende Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen.  

- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Wählen Sie im Azure Security Center das Feature „Compute &amp; Apps“ aus, und folgen Sie den Empfehlungen für virtuelle Computer, Server und Container.

- [Grundlegendes zu Azure Security Center-Containerempfehlungen](../security-center/container-security.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Azure Cloud Services basiert auf einem klassischen Bereitstellungsmodell und lässt sich nicht in Azure Key Vault integrieren.

Sie können geheime Schlüssel wie Anmeldeinformationen, die in Azure Cloud Services verwendet werden, sichern, sodass Sie nicht jedes Mal ein Kennwort eingeben müssen. Geben Sie zunächst ein Klartextkennwort an, und konvertieren Sie es dann mit dem PowerShell-Befehl „ConvertTo-SecureString“ in eine sichere Zeichenfolge. Als Nächstes konvertieren Sie diese sichere Zeichenfolge mit „ConvertFrom-SecureString“ in eine verschlüsselte Standardzeichenfolge.  Jetzt können Sie diese verschlüsselte Standardzeichenfolge mit „Set-Content“ in einer Datei speichern.

Zusätzlich wird empfohlen, die privaten Schlüssel für Zertifikate, die in Azure Cloud Services verwendet werden, in einem gesicherten Speicher zu speichern.

- [Konfigurieren von Remotedesktop über PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Sichern Sie geheime Schlüssel wie Anmeldeinformationen, die in Azure Cloud Services verwendet werden, sodass Sie nicht jedes Mal ein Kennwort eingeben müssen. 
 

Geben Sie zunächst ein Klartextkennwort an, und ändern Sie es dann mit dem PowerShell-Befehl „ConvertTo-SecureString“ in eine sichere Zeichenfolge. Als Nächstes konvertieren Sie diese sichere Zeichenfolge mit „ConvertFrom-SecureString“ in eine verschlüsselte Standardzeichenfolge. Speichern Sie diese verschlüsselte Standardzeichenfolge jetzt mit dem Befehl „Set-Content“ in einer Datei.

Speichern Sie die privaten Schlüssel für Zertifikate, die in Azure Cloud Services verwendet werden, an einem gesicherten Speicherort.

- [Konfigurieren von Remotedesktop über PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: Microsoft Antimalware für Azure ist für Azure Cloud Services und Azure Virtual Machines verfügbar. Microsoft Antimalware für Azure ist eine kostenlose Echtzeit-Schutzfunktion zum Erkennen und Entfernen von Viren, Spyware und anderer Schadsoftware. Das Tool generiert Warnungen, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen. 

Verwenden Sie zum Abrufen der Antimalware-Konfiguration das auf PowerShell basierende Antimalware-Cmdlet „Get-AzureServiceAntimalwareConfig“.

Aktivieren Sie die Antimalware-Erweiterung mit einem PowerShell-Skript im Starttask in Azure Cloud Services.

Wählen Sie im Azure Security Center das Feature „Adaptive Anwendungssteuerung“ aus, eine intelligente, automatisierte End-to-End-Lösung. Mit diesem Feature können Sie Ihren Computer gegen Schadsoftware härten, die Ausführung schädlicher Anwendungen blockieren oder beim Versuch, eine schädliche Anwendung auszuführen, eine Warnung ausgeben (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden).

- [Wie kann ich in Azure Cloud Services automatisch eine Antischadsoftware-Erweiterung hinzufügen?](./cloud-services-configuration-and-management-faq.md?preserve-view=true#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Antimalware-Bereitstellungsszenarien](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Adaptive Anwendungssteuerungen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand. 

Markieren Sie Abonnements aussagekräftig (z. B. „Produktion“, „Nicht-Produktion“), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Nicht verfügbar

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. 

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

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).