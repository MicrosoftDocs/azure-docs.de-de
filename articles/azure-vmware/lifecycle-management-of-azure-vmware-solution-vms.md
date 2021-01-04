---
title: Lebenszyklusverwaltung von Azure VMware Solution-VMs
description: Erfahren Sie, wie Sie alle Aspekte des Lebenszyklus Ihrer Azure VMware Solution-VMs mit nativen Tools in Microsoft Azure verwalten.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: ff1032900fa936895f3adfcb0d8a872f24948aca
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326810"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Lebenszyklusverwaltung von Azure VMware Solution-VMs

Sie können die nativen Tools in Microsoft Azure verwenden, um Ihre virtuellen Computer (VMs) in der Azure-Umgebung zu überwachen und zu verwalten. Sie können damit jedoch auch Ihre virtuellen Computer in Azure VMware Solution und Ihre lokalen VMs überwachen und verwalten. In dieser Übersicht betrachten wir die integrierte Überwachungsarchitektur in Azure und die Möglichkeiten, die Ihnen mit den enthaltenen nativen Tools zum Verwalten Ihrer Azure VMware Solution-VMs über den gesamten Lebenszyklus zur Verfügung stehen.

## <a name="benefits"></a>Vorteile

- Native Azure-Dienste können zum Verwalten Ihrer VMs in einer Hybridumgebung (Azure, Azure VMware Solution und lokal) verwendet werden.
- Integrierte Überwachung und Sichtbarkeit von VMs in Azure, Azure VMware Solution und an Ihrem lokalen Standort.
- Mit der Azure-Updateverwaltung in Azure Automation können Sie Betriebssystemupdates für Ihre Windows- und Linux-Computer verwalten. 
- Azure Security Center ermöglicht einen erweiterten Schutz vor Bedrohungen, einschließlich:
    - Überwachung der Dateiintegrität
    - Dateilose Sicherheitswarnungen
    - Bewertung von Patches für Betriebssysteme
    - Bewertung von Sicherheitsfehlkonfigurationen
    - Bewertung von Endpoint Protection 
- Stellen Sie den Log Analytics-Agent mithilfe der Unterstützung der VM-Erweiterung für Server mit Azure Arc-Unterstützung für neue und vorhandene virtuelle Computer einfach bereit. 
- Ihr Log Analytics-Arbeitsbereich in Azure Monitor ermöglicht das Sammeln von Protokollen und Leistungsindikatoren über den Log Analytics-Agent oder Erweiterungen. Sammeln Sie Daten und Protokolle an einem einzigen Punkt, und präsentieren Sie diese Daten in verschiedenen nativen Azure-Diensten. 
- Zu den Vorteilen Azure Monitor gehören: 
    - Nahtlose Überwachung 
    - Bessere Sichtbarkeit der Infrastruktur 
    - Sofortige Benachrichtigungen 
    - Automatische Behebung 
    - Kosteneffizienz 

## <a name="integrated-azure-monitoring-architecture"></a>Integrierte Azure-Überwachungsarchitektur

Das folgende Diagramm zeigt die integrierte Überwachungsarchitektur für Azure VMware Solution-VMs.

![Integrierte Azure-Überwachungsarchitektur](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Vorbereitung

Wenn Sie neu in Azure sind oder mit einem der oben genannten Dienste nicht vertraut sind, lesen Sie die folgenden Artikel:

- [Übersicht über die Automation-Kontoauthentifizierung](../automation/automation-security-overview.md)
- [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../azure-monitor/platform/design-logs-deployment.md) und [Azure Monitor](../azure-monitor/overview.md)
- [Planung](../security-center/security-center-planning-and-operations-guide.md) und [Unterstützte Plattformen](../security-center/security-center-os-coverage.md) für Azure Security Center
- [Aktivieren von Azure Monitor für VMs: Übersicht](../azure-monitor/insights/vminsights-enable-overview.md)
- [Was sind Server mit Azure Arc-Aktivierung?](../azure-arc/servers/overview.md) und [Was ist Kubernetes mit Azure Arc-Aktivierung?](../azure-arc/kubernetes/overview.md)
- [Übersicht über die Updateverwaltung](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrieren und Bereitstellen von nativen Azure-Diensten

### <a name="enable-azure-update-management"></a>Aktivieren der Azure-Updateverwaltung

Die Azure-Updateverwaltung in Azure Automation verwaltet Betriebssystemupdates für Ihre Windows- und Linux-Computer in einer Hybridumgebung. Sie überwacht die Konformität von Patches und leitet Warnungen zu Abweichungen bei Patches zur Korrektur an Azure Monitor weiter. Die Azure-Updateverwaltung muss eine Verbindung mit Ihrem Log Analytics-Arbeitsbereich herstellen, um gespeicherte Daten zum Bewerten des Status von Updates auf Ihren VMs verwenden zu können.

1.  Bevor Sie Log Analytics zur Azure-Updateverwaltung hinzufügen können, müssen Sie zuerst ein [Azure Automation-Konto erstellen](../automation/automation-create-standalone-account.md). Wenn Sie Ihr Konto lieber mithilfe einer Vorlage erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](../automation/quickstart-create-automation-account-template.md).

2. Der **Log Analytics-Arbeitsbereich** ermöglicht das Sammeln von Protokollen und Leistungsindikatoren über den Log Analytics-Agent oder Erweiterungen. Eine Anleitung zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md). Wenn Sie es vorziehen, können Sie einen Arbeitsbereich auch über die [CLI](../azure-monitor/learn/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md) oder eine [Azure Resource Manager-Vorlage](../azure-monitor/samples/resource-manager-workspace.md) erstellen.

3. Informationen zum Aktivieren der Azure-Updateverwaltung für Ihre VMs finden Sie unter [Aktivieren der Updateverwaltung über ein Automation-Konto](../automation/update-management/enable-from-automation-account.md). Dabei verknüpfen Sie Ihren Log Analytics-Arbeitsbereich mit Ihrem Automation-Konto. 
 
4. Nachdem Sie der Azure-Updateverwaltung VMs hinzugefügt haben, können Sie [Updates auf VMs bereitstellen und die Ergebnisse überprüfen](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Aktivieren von Azure Security Center

Azure Security Center bietet lokal und in der Cloud erweiterten Bedrohungsschutz für alle Ihre Hybridworkloads. Die Lösung bewertet das Sicherheitsrisiko von Azure VMware Solution-VMs und gibt bei Bedarf Warnungen aus. Diese Sicherheitswarnungen können zur Behebung an Azure Monitor weitergeleitet werden.

Azure Security Center muss nicht bereitgestellt werden. Weitere Informationen finden Sie in der Liste der [unterstützten Features für virtuelle Computer](../security-center/security-center-services.md).

1. Informationen zum Hinzufügen von Azure VMware Solution-VMs und Nicht-Azure-VMs zum Security Center finden Sie unter [Schnellstart: Einrichten von Azure Security Center](../security-center/security-center-get-started.md). 

2. Nach dem Hinzufügen von Azure VMware Solution-VMs oder VMs aus einer Nicht-Azure-Umgebung, aktivieren Sie Azure Defender im Security Center. Das Security Center wird die VMs hinsichtlich potenzieller Sicherheitsprobleme bewerten. Außerdem werden auf der Registerkarte „Übersicht“ Empfehlungen bereitgestellt. Weitere Informationen finden Sie unter [Sicherheitsempfehlungen in Azure Security Center anzeigen](../security-center/security-center-recommendations.md).

3. Sie können in Azure Security Center Sicherheitsrichtlinien definieren. Informationen zum Konfigurieren Ihrer Sicherheitsrichtlinien finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Durchführen des Onboardings für virtuelle Computer auf Servern mit Azure Arc-Unterstützung

Azure Arc erweitert die Azure-Verwaltung auf jede Infrastruktur, einschließlich Azure VMware Solution, der lokalen Infrastruktur oder anderer Cloudplattformen.

- Weitere Informationen zum Aktivieren von Servern mit Azure Arc-Unterstützung für mehrere Windows- oder Linux-VMs finden Sie unter [Verbinden von Hybridcomputern mit Azure im großen Stil](../azure-arc/servers/onboard-service-principal.md).

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Durchführen des Onboardings für Kubernetes-Hybridcluster mit Kubernetes mit Arc-Aktivierung

Sie können einen Kubernetes-Cluster, der in Ihrer Azure VMware Solution-Umgebung gehostet wird, mithilfe von Kubernetes mit Arc-Aktivierung anfügen. 

- Weitere Informationen finden Sie unter [Erstellen eines Azure Arc-fähigen Onboardingdienstprinzipals](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Bereitstellen des Log Analytics-Agents

Azure VMware Solution-VMs können über den Log Analytics-Agent (auch als Microsoft Monitoring Agent (MMA) oder OMS Linux-Agent bezeichnet) überwacht werden. Sie haben bereits einen Log Analytics-Arbeitsbereich erstellt, während Sie die Azure Automation-Updateverwaltung aktiviert haben.

- Stellen Sie den Log Analytics-Agent mithilfe von [Servern mit Azure Arc-Unterstützung und Unterstützung der VM-Erweiterung](../azure-arc/servers/manage-vm-extensions.md) bereit.

### <a name="enable-azure-monitor"></a>Aktivieren von Azure Monitor

Azure Monitor ist eine umfassende Lösung für das Sammeln und Analysieren von Telemetriedaten aus Ihren Cloud- und lokalen Umgebungen und das Reagieren auf diese. Es ist keine Bereitstellung erforderlich. Mit Azure Monitor können Sie die Leistung des Gastbetriebssystems überwachen und Anwendungsabhängigkeiten für Azure VMware Solution oder lokale virtuelle Computer erkennen und zuordnen.

- Mit Azure Monitor können Sie Daten aus verschiedenen Quellen erfassen, um sie zu überwachen und zu analysieren. Weitere Informationen finden Sie unter [Quellen von Überwachungsdaten für Azure Monitor](../azure-monitor/platform/data-sources.md).

- Sammeln Sie verschiedene Arten von Daten für Analysen, Visualisierungen und Warnungen. Weitere Informationen finden Sie unter [Azure Monitor-Datenplattform](../azure-monitor/platform/data-platform.md).

- Informationen zum Konfigurieren von Azure Monitor mit Ihrem Log Analytics-Arbeitsbereich finden Sie unter [Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs](../azure-monitor/insights/vminsights-configure-workspace.md).

- Sie können Warnungsregeln erstellen, um Probleme in Ihrer Umgebung wie hohe Ressourcennutzung, fehlende Patches, wenig Speicherplatz auf dem Datenträger und Takt Ihrer VMs zu ermitteln. Sie können auch eine automatisierte Antwort auf erkannte Ereignisse festlegen, indem Sie eine Warnung an die ITSM-Tools (IT-Service-Management) senden. Benachrichtigungen zu erkannten Warnungen können auch per E-Mail gesendet werden. Informationen zum Erstellen solcher Regeln finden Sie unter:
    - [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-metric.md)
    - [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)
    - [Aktionsregeln](../azure-monitor/platform/alerts-action-rules.md) zum Festlegen automatisierter Aktionen und Benachrichtigungen
    - [Verbinden von Azure mit ITSM-Tools mithilfe des ITSM-Connectors](../azure-monitor/platform/itsmc-overview.md)