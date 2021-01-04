---
title: Azure-Sicherheitsbaseline für Virtual Network NAT
description: Die Sicherheitsbaseline für Virtual Network NAT enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4ab71138f110df19ec84fa8707b480ad1bc6e72c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255079"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Azure-Sicherheitsbaseline für Virtual Network NAT

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) auf Microsoft Virtual Network NAT an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure.
Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden, für Virtual Network NAT geltenden Empfehlungen definiert werden. Nicht auf Virtual Network NAT anwendbare **Steuerungen** wurden ausgeschlossen.

 
Die gesamte Zuordnung zwischen Virtual Network NAT und dem Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Virtual Network NAT-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Virtual Network NAT unterstützt das Steuern von ausgehendem Datenverkehr mit Netzwerksicherheitsgruppen (NSG) nicht.  Eingehender Datenverkehr ist nur als Antwort auf einen ausgehenden ursprünglichen Flow zulässig.

Sie können jedoch Datenflussprotokolle für Netzwerksicherheitsgruppen (NSG) mit NAT-Gatewayressourcen verwenden, um diesen ursprünglichen ausgehenden Datenverkehr zu überwachen.

Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Azure-Netzwerkressourcen abzusichern. Aktivieren Sie Netzwerksicherheitsgruppen-Datenflussprotokolle, und senden Sie Protokolle zur Überwachung an ein Azure Storage-Konto. Sie können die Datenflussprotokolle auch an einen Log Analytics-Arbeitsbereich senden und dann mithilfe von Traffic Analytics Einblicke in die Datenverkehrsmuster in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren, Hotspots und Sicherheitsbedrohungen zu erkennen, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln. 

- [Übersicht über Virtual Network NAT](https://docs.microsoft.com/azure/virtual-network/nat-overview)

- [NAT-Gatewayressource](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource)

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal) 

- [Aktivieren und Verwenden von Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie die Network Watcher-Paketerfassung, um anomale Aktivitäten zu untersuchen. 

- [Erstellen einer Network Watcher-Instanz](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Subnetze, die mit NAT-Gatewayressourcen konfiguriert sind, mithilfe integrierter oder benutzerdefinierter Azure Policy-Definitionen und -Zuweisungen.

- [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy-Beispiele für Netzwerke](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und zum Erkennen von Änderungen an den NAT-Gatewayressourcen und VNET-Ressourcen. Erstellen Sie in Azure Monitor Warnungen, mit denen Sie benachrichtigt werden, wenn sich wichtige Ressourcen ändern.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Erstellen von Warnungen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle im Zusammenhang mit Virtual Network NAT über Azure Monitor, um Sicherheitsdaten zu aggregieren, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Alternativ können Sie diese Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre NAT-Gatewayressourcen mit Ausnahme von Lesevorgängen (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) 

Virtual Network NAT erzeugt derzeit keine zusätzlichen Diagnoseprotokolle, die von den Kunden konfiguriert werden können.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center mit einem Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. Sie können stattdessen auch Daten in Azure Sentinel aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Verwalten von Warnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) 

- [Warnungen bei Log Analytics-Protokolldaten](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (etwa Computeressourcen, Speicher, Netzwerke, Ports, Protokolle und Ähnliches) in Ihren Abonnements abzufragen bzw. zu ermitteln. 

Sorgen Sie für entsprechende Berechtigungen (Leseberechtigungen) in Ihrem Mandanten, und zählen Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Azure Resource Graph-Abfragen](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Wenden Sie Tags für die Verwaltung von Azure-Ressourcen an.

- [Erstellen zusätzlicher Azure-Abonnements](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Erstellen von Verwaltungsgruppen](https://docs.microsoft.com/azure/governance/management-groups/create) 

- [Erstellen und Verwenden von Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Zurzeit nicht verfügbar

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um den Typ der Ressourcen, die in ihren Abonnements erstellt werden können, einzuschränken.
Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 
- [Erstellen von Abfragen mit Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um einzuschränken, welche Dienste in Ihrer Umgebung bereitgestellt werden können.

- [Konfigurieren und Verwalten von Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Nicht zutreffend: Virtual Network NAT verfügt nicht über Sicherheitskonfigurationen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten. Dazu zählen z. B. benutzerdefinierte Azure Policy-Richtlinien, Azure Resource Manager-Vorlagen und Desired State Configuration-Skripts. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierte Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern. 

- [Speichern von Code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Nicht zutreffend: Virtual Network NAT verfügt nicht über Sicherheitskonfigurationen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Richten Sie ein Verfahren für die Reaktion auf Vorfälle für das Angebot ein, um sicherzustellen, dass geeignete Prozesse zur Reaktion auf Vorfälle vorhanden sind, damit bis zur Lösung des Vorfalls eine geeignete Priorisierung besteht.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Richten Sie ein Verfahren zur Bewertung und Priorisierung von Vorfällen für das Angebot ein, um für eine ordnungsgemäße Risiko- oder Bedrohungsbewertung zu sorgen, damit bis zur Lösung des Vorfalls eine geeignete Priorisierung besteht.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Richten Sie ein Verfahren für die Reaktion auf Sicherheitsvorfälle für das Angebot ein, und testen Sie es, um sicherzustellen, dass bis zur Lösung des Vorfalls eine geeignete Risikominderung mit passender Priorisierung stattfinden kann.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Richten Sie ein Verfahren für die Reaktion auf Sicherheitsvorfälle für das Angebot ein, und testen Sie es, um sicherzustellen, dass bis zur Lösung des Vorfalls eine geeignete Risikominderung mit passender Priorisierung stattfinden kann.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Richten Sie ein Verfahren für die Reaktion auf Sicherheitsvorfälle für das Angebot ein, und testen Sie es, um sicherzustellen, dass bis zur Lösung des Vorfalls eine geeignete Risikominderung mit passender Priorisierung stattfinden kann.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Richten Sie ein Verfahren für die Reaktion auf Sicherheitsvorfälle für das Angebot ein, und testen Sie es, um sicherzustellen, dass bis zur Lösung des Vorfalls eine geeignete Risikominderung mit passender Priorisierung stattfinden kann.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen. 

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](/azure/security/benchmarks/overview) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).
