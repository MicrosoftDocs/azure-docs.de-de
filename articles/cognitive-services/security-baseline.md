---
title: Azure-Sicherheitsbaseline für Cognitive Services
description: Azure-Sicherheitsbaseline für Cognitive Services
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 97bcf0bedb3ed175f8fdb5d4dc361c069ae0327d
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805208"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Azure-Sicherheitsbaseline für Cognitive Services

Die Azure-Sicherheitsbaseline für Cognitive Services enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie in der [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Azure Cognitive Services bietet ein mehrschichtiges Sicherheitsmodell. Dank dieses Modells können Sie Ihre Cognitive Services-Konten für eine bestimmte Teilmenge von Netzwerken schützen. Wenn Netzwerkregeln konfiguriert wurden, können nur Anwendungen, die Daten über die angegebene Gruppe von Netzwerken anfordern, auf das Konto zugreifen. Sie können den Zugriff auf Ihre Ressourcen mithilfe von Anforderungsfilterung einschränken und nur Anforderungen zulassen, die aus angegebenen IP-Adressen, IP-Adressbereichen oder einer Liste von Subnetzen in virtuellen Azure-Netzwerken stammen.

Die Unterstützung von virtuellen Netzwerken und Dienstendpunkten für Cognitive Services ist auf eine bestimmte Gruppe von Regionen beschränkt.

* [Konfigurieren von virtuellen Netzwerken für Azure Cognitive Services](./cognitive-services-virtual-networks.md?tabs=portal)

* [Übersicht über virtuelle Azure-Netzwerke](../virtual-network/virtual-networks-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Wenn virtuelle Computer (VMs) im gleichen virtuellen Netzwerk wie Ihr Azure Cognitive Services-Container bereitgestellt werden, können Sie Netzwerksicherheitsgruppen (NSG) verwenden, um das Risiko von Datenexfiltration zu verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Wenn Sie Cognitive Services in einem Container verwenden, können Sie Ihre Containerbereitstellung mit einer Front-End-Firewall-Lösung für Webanwendungen verbessern, die schädlichen Datenverkehr filtert und die End-to-End-TLS-Verschlüsselung unterstützt, sodass der Containerendpunkt privat und sicher bleibt.

Beachten Sie, dass Cognitive Services-Container erforderlich sind, um Messungsinformationen zu Abrechnungszwecken zu übermitteln. Die einzige Ausnahme sind Offlinecontainer, da für sie eine abweichende Abrechnungsmethode gilt. Wenn Sie die verschiedenen Netzwerkkanäle, auf die sich die Cognitive Services-Container stützen, nicht in einer Zulassungsliste aufführen, funktionieren die Container nicht. Der Host sollte Port 443 und die folgenden Domänen in seiner Zulassungsliste aufführen:
- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Beachten Sie außerdem, dass Sie die eingehende Paketuntersuchung für Ihre Firewall in den sicheren Kanälen, die von den Cognitive Services-Containern zu Microsoft-Servern erstellt werden, deaktivieren müssen. Andernfalls funktionieren die Container nicht ordnungsgemäß.

* [Grundlegendes zur Containersicherheit in Azure Cognitive Services](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Wenn virtuelle Computer im gleichen virtuellen Netzwerk wie Ihr Azure Cognitive Services-Container bereitgestellt werden, definieren und implementieren Sie mit Azure Policy Standard-Sicherheitskonfigurationen für verknüpfte Netzwerkressourcen. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.CognitiveServices“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Cache for Redis-Instanzen zu erstellen. Sie können auch integrierte Richtliniendefinitionen verwenden, z. B.:
- DDoS Protection Standard sollte aktiviert sein.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Wenn Sie Cognitive Services in einem Container verwenden, können Sie Ihre Containerbereitstellung mit einer Front-End-Firewall-Lösung für Webanwendungen verbessern, die schädlichen Datenverkehr filtert und die End-to-End-TLS-Verschlüsselung unterstützt, sodass der Containerendpunkt privat und sicher bleibt.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

* [Grundlegendes zur Containersicherheit in Azure Cognitive Services](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Wenn virtuelle Computer (VMs) im gleichen virtuellen Netzwerk wie Ihr Azure Cognitive Services-Container bereitgestellt werden, können Sie Netzwerksicherheitsgruppen (NSG) verwenden, um das Risiko von Datenexfiltration zu verringern. Aktivieren Sie NSG-Flussprotokolle, und senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

* [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wenn Sie Cognitive Services in einem Container verwenden, können Sie Ihre Containerbereitstellung mit einer Front-End-Firewall-Lösung für Webanwendungen verbessern, die schädlichen Datenverkehr filtert und die End-to-End-TLS-Verschlüsselung unterstützt, sodass der Containerendpunkt privat und sicher bleibt. Sie können ein Angebot aus dem Azure Marketplace auswählen, das IDS-/IPS-Funktionen mit Funktionalität zum Deaktivieren der Nutzlastuntersuchung unterstützt.

Beachten Sie, dass Cognitive Services-Container erforderlich sind, um Messungsinformationen zu Abrechnungszwecken zu übermitteln. Die einzige Ausnahme bilden Offlinecontainer, da sie einer anderen Abrechnungsmethode unterliegen. Wenn Sie die verschiedenen Netzwerkkanäle, auf die sich die Cognitive Services-Container stützen, nicht in einer Zulassungsliste aufführen, funktionieren die Container nicht. Der Host sollte Port 443 und die folgenden Domänen in seiner Zulassungsliste aufführen:
- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Beachten Sie außerdem, dass Sie die eingehende Paketuntersuchung für Ihre Firewall in den sicheren Kanälen, die von den Cognitive Services-Containern zu Microsoft-Servern erstellt werden, deaktivieren müssen. Andernfalls funktionieren die Container nicht ordnungsgemäß.

* [Grundlegendes zur Containersicherheit in Azure Cognitive Services](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Wenn Sie Cognitive Services in einem Container verwenden, können Sie Ihre Containerbereitstellung mit einer Front-End-Firewall-Lösung für Webanwendungen verbessern, die schädlichen Datenverkehr filtert und die End-to-End-TLS-Verschlüsselung unterstützt, sodass der Containerendpunkt privat und sicher bleibt.

Beachten Sie, dass Cognitive Services-Container erforderlich sind, um Messungsinformationen zu Abrechnungszwecken zu übermitteln. Die einzige Ausnahme sind Offlinecontainer, da für sie eine abweichende Abrechnungsmethode gilt. Wenn Sie die verschiedenen Netzwerkkanäle, auf die sich die Cognitive Services-Container stützen, nicht in einer Zulassungsliste aufführen, funktionieren die Container nicht. Der Host sollte Port 443 und die folgenden Domänen in seiner Zulassungsliste aufführen:
- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Beachten Sie außerdem, dass Sie die eingehende Paketuntersuchung für Ihre Firewall in den sicheren Kanälen, die von den Cognitive Services-Containern zu Microsoft-Servern erstellt werden, deaktivieren müssen. Andernfalls funktionieren die Container nicht ordnungsgemäß.

* [Grundlegendes zur Containersicherheit in Azure Cognitive Services](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie VNet-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen (NSG) oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Sie können auch Anwendungssicherheitsgruppen (ASG) verwenden, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Mit ASGs können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren.

* [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

* [Anwendungssicherheitsgruppen](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihrem Azure Cognitive Services-Container zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.CognitiveServices“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Cache for Redis-Instanzen zu erstellen.

Sie können auch Azure Blueprints verwenden, um umfangreiche Azure-Bereitstellungen zu vereinfachen, indem wichtige Umgebungsartefakte, z. B. Azure Resource Manager-Vorlagen, rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Richtlinien, gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen einer Azure-Blaupause](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihrem Azure Cognitive Services-Container zugeordnet sind, um sie in einer Taxonomie logisch zu organisieren.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Nutzen Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen im Zusammenhang mit Ihrem Azure Cognitive Services-Container. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

* [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die Zeitquelle, die für Azure-Ressourcen wie Azure Cognitive Services verwendet wird, für Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hub-Instanz oder ein Azure-Speicherkonto. Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihren Azure Cognitive Services-Container auf Steuerungsebene erfolgen. Mit Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) auf Steuerungsebene ermitteln, die für Ihre Azure Cache for Redis-Instanzen stattfinden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie für die Überwachungsprotokollierung auf der Steuerungsebene die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle zwecks Archivierung an einen Log Analytics-Arbeitsbereich, eine Azure Event Hubs-Instanz oder ein Azure-Speicherkonto. Mit den Daten aus Azure-Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die auf Steuerungsebene für Ihre Azure-Ressourcen durchgeführt werden.

Außerdem sendet Azure Cognitive Services Diagnoseereignisse, die gesammelt und dann für die Analyse, Warnung und Berichterstellung verwendet werden können. Diagnoseereignisse für einen Cognitive Services-Container lassen sich im Azure-Portal konfigurieren. Sie können ein oder mehrere Diagnoseereignisse an ein Speicherkonto, eine Event Hubs-Instanz oder einen Log Analytics-Arbeitsbereich senden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Verwenden von Diagnoseeinstellungen für Azure Cognitive Services](./diagnostic-logging.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung oder Archivierung.

* [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, und senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich. Diese Protokolle stellen umfassende, häufig verwendete Daten zu den Vorgängen einer Ressource bereit, die für die Ermittlung von Problemen und das Debuggen verwendet werden. Führen Sie Abfragen in Log Analytics durch, um Suchbegriffe zu ermitteln, Trends auszumachen, Muster zu analysieren und viele weitere Erkenntnisse auf Grundlage der Aktivitätsprotokolldaten zu gewinnen, die möglicherweise für Azure Cognitive Services gesammelt wurden.

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

* [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Sie können Warnungen zu unterstützten Metriken in Azure Cognitive Services einstellen, indem Sie in Azure Monitor zum Abschnitt „Warnungen und Metriken“ navigieren.

Konfigurieren Sie die Diagnoseeinstellungen für Ihren Cognitive Services-Container, und senden Sie Protokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie in Ihrem Log Analytics-Arbeitsbereich Warnungen, die ausgelöst werden sollen, wenn vordefinierte Bedingungen eintreten. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Cognitive Services verarbeitet keine Protokolle zu Antischadsoftware und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. Azure Cognitive Services verarbeitet keine Protokolle zu DNS und erzeugt auch keine.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (AAD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

* [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Der Zugriff auf Steuerungsebene auf Azure Cognitive Services wird mittels Azure Active Directory (AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

Der Zugriff auf Datenebene auf Azure Cognitive Services wird mittels Zugriffsschlüsseln gesteuert. Diese Schlüssel werden von den Clients verwendet, die eine Verbindung mit Ihrem Cache herstellen, und können jederzeit neu generiert werden.

Es wird nicht empfohlen, Standardkennwörter in Ihre Anwendung zu integrieren. Stattdessen können Sie Ihre Kennwörter in Azure Key Vault speichern und sie über Azure Active Directory abrufen.

* [Neugenerieren von Zugriffsschlüsseln für Azure Cognitive Services](../azure-cache-for-redis/cache-configure.md#settings)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Zusätzlich können Sie, um den Überblick über dedizierte Verwaltungskonten zu behalten, Empfehlungen des Azure Security Center oder integrierte Azure-Richtlinien befolgen, wie z. B:
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

* [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Azure Cognitive Services verwendet Zugriffsschlüssel zur Benutzerauthentifizierung und unterstützt kein einmaliges Anmelden (Single Sign-On, SSO) auf Datenebene. Der Zugriff auf die Steuerungsebene für Azure Cognitive Services ist über die REST-API verfügbar und unterstützt SSO. Legen Sie den Autorisierungsheader für Ihre Anforderungen auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abrufen.

* [Grundlegendes zur REST-API von Azure Cognitive Services](/rest/api/cognitiveservices/)

* [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (AAD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

* [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (AAD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

* [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Konfigurieren Sie in der Azure Active Directory-Funktion (AAD) „Bedingter Zugriff“ benannte Standorte, um den Zugriff nur bestimmten logischen Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zu gestatten.

* [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert. Wenn Ihr Anwendungsfall die AD-Authentifizierung unterstützt, verwenden Sie Azure AD, um Anforderungen an Ihre Cognitive Services-API zu authentifizieren.

Derzeit unterstützen nur Maschinelles Sehen-API, Gesichtserkennungs-API, Textanalyse-API, Plastischer Reader, Formularerkennung, Anomalieerkennung und alle Bing-Dienste mit Ausnahme der benutzerdefinierten Bing-Suche die Authentifizierung mit Azure AD.

* [Authentifizieren von Anforderungen für Cognitive Services](./authentication.md#authenticate-with-azure-active-directory)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Darüber hinaus können Kunden Zugriffsüberprüfungen für Azure-Identitäten verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

Kunden können einen Bestand an Benutzerkonten für API Management verwalten und den Zugriff nach Bedarf abstimmen. In API Management sind Entwickler die Benutzer der APIs, die Sie mit API Management verfügbar machen. Neu erstellte Entwicklerkonten sind standardmäßig „Aktiv“ und werden der Gruppe „Entwickler“ zugewiesen. Entwicklerkonten mit dem Status „Aktiv“ können sämtliche APIs aufrufen, die sie abonniert haben.

* [Verwalten von Benutzerkonten in Azure API Management](../api-management/api-management-howto-create-or-invite-developers.md)

* [Abrufen einer Liste mit API Management-Benutzern](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf Azure Active Directory- Anmeldeaktivitäten (AAD) sowie Quellen für Überwachungs- und Risikoereignisprotokolle, wodurch die Integration in Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen in Log Analytics konfigurieren.

* [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie für abweichendes Verhalten bei der Kontoanmeldung auf Steuerungsebene Azure Active Directory (AAD) Identity Protection und Risikoerkennungsfunktionen, um automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

* [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

* [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht verfügbar. Kunden-Lockbox wird für Azure Cognitive Services noch nicht unterstützt.

* [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein VNET/Subnetz getrennt, ordnungsgemäß gekennzeichnet und durch eine NSG oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden. Implementieren Sie für VMs, auf denen vertrauliche Informationen gespeichert oder verarbeitet werden, Richtlinien und Verfahren, mit denen Sie sie bei Nichtverwendung deaktivieren können.

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

**Leitfaden**: Noch nicht verfügbar. Funktionen für Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Cognitive Services noch nicht verfügbar.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Cognitive Services und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Zurzeit nicht verfügbar

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Bei allen Cognitive Services-Endpunkten, die über HTTP verfügbar gemacht werden, wird TLS 1.2 erzwungen. Aufgrund des erzwungenen Sicherheitsprotokolls gelten für Consumer, die einen Cognitive Services-Endpunkt aufrufen möchten, folgende Richtlinien:
- Das Clientbetriebssystem (Operating System, OS) muss TLS 1.2 unterstützen.
- Die für den HTTP-Aufruf verwendete Sprache (und Plattform) muss TLS 1.2 als Teil der Anforderung angeben. (Je nach Sprache und Plattform erfolgt die Angabe von TLS entweder implizit oder explizit.)

* [Grundlegendes zu Transport Layer Security für Azure Cognitive Services](./cognitive-services-security.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Cognitive Services noch nicht verfügbar. Versehen Sie Instanzen mit vertraulichen Informationen mit entsprechenden Tags, und implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Integrieren Sie die rollenbasierte Zugriffssteuerung (Azure RBAC) in Azure, um den Zugriff auf die Steuerungsebene von Azure Cognitive Services (d. h. das Azure-Portal) zu steuern.

* [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure Cognitive Services und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

* [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Verschlüsselung im Ruhezustand für Cognitive Services hängt vom spezifischen Dienst ab, der verwendet wird. In den meisten Fällen werden Daten mittels FIPS 140-2-konformer 256-Bit-AES-Verschlüsselung ver- und entschlüsselt. Verschlüsselung und Entschlüsselung sind transparent, was bedeutet, dass die Verschlüsselung und der Zugriff für Sie verwaltet werden. Ihre Daten werden standardmäßig geschützt, und Sie müssen weder Code noch Anwendungen ändern, um die Verschlüsselung nutzen zu können.

Sie können außerdem Azure Key Vault zum Speichern Ihrer vom Kunden verwalteten Schlüssel verwenden. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren.

* [Liste der Dienste, die Informationen im Ruhezustand verschlüsseln](./encryption/cognitive-services-encryption-keys-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Azure Cognitive Services und anderen kritischen bzw. verbundenen Ressourcen vorgenommen werden.

* [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Cognitive Services unterstützen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure Cognitive Services unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

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

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Azure Cache for Redis-Instanzen und damit verbundenen Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Verwenden Sie darüber hinaus Azure Policy, um Einschränkungen für die Ressourcentypen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

* [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

* [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

* [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen und Azure als Ganzes vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:
- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

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

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

* [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

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

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihre Azure Cognitive Services-Container. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.CognitiveServices“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Cache for Redis-Instanzen zu erstellen.

* [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen oder Azure Resource Manager-Vorlagen für Ihre Azure Cognitive Services-Container und damit verbundene Ressourcen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Cache“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.CognitiveServices“, um benutzerdefinierte Azure Policy-Definitionen zum Benachrichtigen, Überprüfen und Erzwingen von Systemkonfigurationen zu verwenden. Verwenden Sie die Azure Policy-Einstellungen [audit] (überwachen), [deny] (verweigern) und [deploy if not existent] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure Cache for Redis-Instanzen und damit verbundene Ressourcen automatisch zu erzwingen.

* [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cognitive Services-API verwendet werden, nutzen Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Schlüsselverwaltung für Azure Cognitive Services zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

* [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Erstellen einer Key Vault-Instanz](../key-vault/secrets/quick-create-portal.md)

* [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)

* [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Wenn Azure-VMs oder Webanwendungen, die in Azure App Service ausgeführt werden, für den Zugriff auf Ihre Azure Cognitive Services-API verwendet werden, nutzen Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Schlüsselverwaltung für Azure Cognitive Services zu vereinfachen und zu schützen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist.

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

* [Konfigurieren von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

* [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Azure Cognitive Services) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft-Antischadsoftware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Cache for Redis) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in Azure-Ressourcen ohne Computeaufgaben hochgeladen werden sollen, z. B. App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Azure Cognitive Services) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets automatisch repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Azure Storage kopiert Ihre Daten so, dass sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern und Netzwerk- oder Stromausfällen bis zu schweren Naturkatastrophen usw. Sie können Ihre Daten wahlweise im selben Rechenzentrum, Rechenzentren in derselben Region und sogar übergreifend über geografisch getrennte Regionen replizieren.

Sie können außerdem ein Lebenszyklusverwaltungsfeature verwenden, um Daten auf der Archivebene zu sichern. Darüber hinaus können Sie das vorläufige Löschen für Ihre Sicherungen aktivieren, die im Speicherkonto gespeichert werden.

* [Grundlegendes zu Azure Storage-Redundanz und Vereinbarungen zum Servicelevel](../storage/common/storage-redundancy.md)

* [Verwalten des Azure Blob Storage-Lebenszyklus](../storage/blobs/storage-lifecycle-management-concepts.md)

* [Vorläufiges Löschen für Azure Storage-Blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Verwenden Sie Azure Resource Manager, um Cognitive Services und zugehörige Ressourcen bereitzustellen. Azure Resource Manager bietet die Möglichkeit zum Exportieren von Vorlagen, mit denen Sie Ihre Lösung während des Entwicklungslebenszyklus erneut bereitstellen und zugleich sichergehen können, dass Ihre Ressourcen in einem konsistenten Zustand bereitgestellt werden. Verwenden Sie Azure Automation, um die API zum Export von Azure Resource Manager-Vorlagen regelmäßig aufzurufen. Sichern von vorinstallierten Schlüsseln in Azure Key Vault.

* [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Erstellen einer Cognitive Services-Ressource mithilfe einer Azure Resource Manager-Vorlage](./create-account-resource-manager-template.md?tabs=portal)

* [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Ressourcengruppen: Vorlage exportieren](/rest/api/resources/resourcegroups/exporttemplate)

* [Einführung in Azure Automation](../automation/automation-intro.md)

* [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Sicherstellen der Fähigkeit, die Bereitstellung von Azure Resource Manager-Vorlagen bei Bedarf regelmäßig in einem isolierten Abonnement auszuführen. Testen der Wiederherstellung von gesicherten vordefinierten Schlüsseln.

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Verwenden von Azure DevOps zum Speichern und Verwalten Ihrer Azure Resource Manager-Vorlagen. Um die Ressourcen, die Sie in Azure DevOps verwalten, zu schützen, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierten Gruppen (bei Integration in Azure DevOps) oder in Active Directory definierten Gruppen (bei Integration in TFS) Berechtigungen erteilen oder verweigern.  Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure, um kundenseitig verwaltete Schlüssel zu schützen. Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen. 

* [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

* [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

* [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [„Computer Security Incident Handling Guide“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

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

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

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

**Leitfaden:** * Befolgen Sie die [Einsatzregeln für Penetrationstests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1), damit diese alle Anforderungen der Microsoft-Richtlinien erfüllen.

* Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).