---
title: Kontinuierliche Überwachung mit Azure Monitor | Microsoft-Dokumentation
description: Beschreibt bestimmte Schritte für die Verwendung von Azure Monitor, um eine kontinuierliche Überwachung in Ihren Workflows zu ermöglichen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 1892afde42ccbcf1c28b7d6a757875772ee3ac5d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905603"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Kontinuierliche Überwachung mit Azure Monitor

Die kontinuierliche Überwachung bezieht sich auf den Prozess und die Technologie, die erforderlich sind, um die Überwachung in jeder Phase des Lebenszyklus Ihrer DevOps und der IT-Abläufe zu integrieren. Sie hilft, die Integrität, Leistung und Zuverlässigkeit Ihrer Anwendung und Infrastruktur auf dem Weg von der Entwicklung zur Produktion kontinuierlich sicherzustellen. Die kontinuierliche Überwachung basiert auf den Konzepten der Continuous Integration und Continuous Deployment (CI/CD), mit denen Sie Software schneller und zuverlässiger entwickeln und bereitstellen können, um Ihren Benutzern einen kontinuierlichen Mehrwert zu bieten.

[Azure Monitor](overview.md) ist die vereinheitlichte Überwachungslösung in Azure, die eine systemübergreifende Transparenz von Anwendungen und Infrastrukturen in der Cloud und lokal ermöglicht. Es arbeitet nahtlos mit [Visual Studio und Visual Studio Code](https://visualstudio.microsoft.com/) während der Entwicklung und des Tests zusammen und wird mit [Azure DevOps](/azure/devops/user-guide/index) für die Release- und Arbeitselementverwaltung während der Bereitstellung und des Betriebs integriert. Es lässt sich sogar in die ITSM- und SIEM-Tools Ihrer Wahl integrieren, um Probleme und Vorfälle innerhalb Ihrer bestehenden IT-Prozesse zu verfolgen.

In diesem Artikel werden bestimmte Schritte für die Verwendung von Azure Monitor beschrieben, um eine kontinuierliche Überwachung in Ihren Workflows zu ermöglichen. Er enthält Links zu anderen Dokumentationen, die Details zur Implementierung verschiedener Features bereitstellen.


## <a name="enable-monitoring-for-all-your-applications"></a>Aktivieren der Überwachung für alle Ihre Anwendungen
Um die Transparenz in Ihrer gesamten Umgebung sicherzustellen, müssen Sie die Überwachung für Ihre gesamten Webanwendungen und -dienste aktivieren. Auf diese Weise können Sie ganz einfach End-to-End-Transaktionen und -Verbindungen über alle Komponenten hinweg visualisieren.

- [Azure DevOps Projects](../devops-project/overview.md) bietet eine vereinfachte Umgebung mit Ihrem vorhandenen Code und dem Git-Repository, oder wählen Sie eine der Beispielanwendungen aus, um eine Continuous Integration- und Continuous Delivery-Pipeline (CI/CD) für Azure zu erstellen.
- Die [kontinuierliche Überwachung in Ihrer DevOps-Releasepipeline](./app/continuous-monitoring.md) ermöglicht es Ihnen, Ihre Bereitstellung auf der Grundlage von Überwachungsdaten zu steuern oder zurückzusetzen.
- Der [Statusmonitor](./app/monitor-performance-live-website-now.md) ermöglicht es Ihnen, eine .NET-Live-App unter Windows mit Azure Application Insights zu instrumentieren, ohne Ihren Code ändern oder neu bereitstellen zu müssen.
- Wenn Sie Zugriff auf den Code für Ihre Anwendung haben, aktivieren Sie die vollständige Überwachung mit [Application Insights](./app/app-insights-overview.md), indem Sie das Azure Monitor Application Insights SDK für [.NET](./app/asp-net.md), [.NET Core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./learn/nodejs-quick-start.md) oder [andere Programmiersprachen](./app/platforms.md) installieren. Auf diese Weise können Sie benutzerdefinierte Ereignisse, Metriken oder Seitenansichten festlegen, die für Ihre Anwendung und Ihr Unternehmen relevant sind.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Aktivieren der Überwachung für Ihre gesamte Infrastruktur
Anwendungen sind nur so zuverlässig wie ihre zugrunde liegende Infrastruktur. Die Aktivierung der Überwachung in der gesamten Infrastruktur hilft Ihnen, eine vollständige Transparenz zu erreichen und erleichtert es, bei Ausfällen eine mögliche Grundursache zu finden. Azure Monitor hilft Ihnen, die Integrität und Leistung Ihrer gesamten Hybridinfrastruktur zu überwachen, einschließlich Ressourcen wie virtuelle Computer, Container, Speicher und Netzwerk.

- Sie erhalten automatisch [Plattformmetriken, Aktivitäts- und Diagnoseprotokolle](platform/data-sources.md) von den meisten Ihrer Azure-Ressourcen ohne Konfiguration.
- Aktivieren Sie eine intensivere Überwachung für virtuelle Computer mit [Azure Monitor für VMs](insights/vminsights-overview.md).
-  Aktivieren Sie eine intensivere Überwachung für AKS-Cluster mit [Azure Monitor für Container](insights/container-insights-overview.md).
- Fügen Sie [Überwachungslösungen](./monitor-reference.md) für verschiedene Anwendungen und Dienste in Ihrer Umgebung hinzu.


[Infrastruktur als Code](/azure/devops/learn/what-is-infrastructure-as-code) ist die Verwaltung der Infrastruktur in einem beschreibenden Modell, wobei dieselbe Versionsverwaltung verwendet wird, die DevOps-Teams für Quellcode verwenden. Es erhöht die Zuverlässigkeit und Skalierbarkeit Ihrer Umgebung und ermöglicht es Ihnen, ähnliche Prozesse wie bei der Verwaltung Ihrer Anwendungen zu nutzen.

-  Verwenden Sie [Resource Manager-Vorlagen](./samples/resource-manager-workspace.md), um die Überwachung und Konfiguration von Warnungen über eine große Anzahl von Ressourcen zu ermöglichen.
- Verwenden Sie [Azure Policy](../governance/policy/overview.md), um unterschiedliche Regeln für Ihre Ressourcen zu erzwingen. Auf diese Weise wird sichergestellt, dass diese Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel konform sind. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinieren von Ressourcen in Azure-Ressourcengruppen
Eine typische Anwendung in Azure umfasst heute mehrere Ressourcen wie VMs und App Services oder Microservices, die in Cloud Services, AKS-Clustern oder Service Fabric gehostet werden. Diese Anwendungen nutzen häufig Abhängigkeiten wie Event Hubs, Storage, SQL und Service Bus.

- Kombinieren Sie Ressourcen in Azure-Ressourcengruppen, um eine vollständige Sichtbarkeit über Ihre gesamten Ressourcen zu erhalten, aus denen sich Ihre verschiedenen Anwendungen zusammensetzen. [Azure Monitor für Ressourcengruppen](./insights/resource-group-insights.md) bietet eine einfache Möglichkeit, die Integrität und Leistung Ihrer gesamten systemübergreifenden Anwendung zu verfolgen und ermöglicht es, für Untersuchungen oder Debugging einen Drilldown in die entsprechenden Komponenten auszuführen.

## <a name="ensure-quality-through-continuous-deployment"></a>Sicherstellen der Qualität durch Continuous Deployment
Mit Continuous Integration/Continuous Deployment können Sie Codeänderungen automatisch in Ihre Anwendung auf Basis der Ergebnisse automatisierter Tests integrieren und bereitstellen. Es optimiert den Bereitstellungsprozess und sichert die Qualität aller Änderungen, bevor sie in die Produktionsumgebung übernommen werden.


- Verwenden Sie [Azure Pipelines](/azure/devops/pipelines), um Continuous Deployment zu implementieren und Ihren gesamten Prozess (vom Codecommit bis zur Produktion) auf Basis Ihrer CI/CD-Tests zu automatisieren.
- Verwenden Sie [Quality Gates](/azure/devops/pipelines/release/approvals/gates), um die Überwachung in die Prozesse vor oder nach der Bereitstellung zu integrieren. Dadurch wird sichergestellt, dass Sie die wichtigsten Integritäts-/Leistungsmetriken (KPIs) erfüllen, wenn Ihre Anwendungen von der Entwicklung in die Produktionsumgebung wechseln und Unterschiede in der Infrastrukturumgebung oder -größe sich nicht negativ auf Ihre KPIs auswirken.
- [Unterhalten Sie separate Überwachungsinstanzen](./app/separate-resources.md) zwischen Ihren verschiedenen Bereitstellungsumgebungen wie Entwicklung, Test, Canary und Produktion. Auf diese Weise wird sichergestellt, dass die gesammelten Daten für alle zugehörigen Anwendungen und Infrastrukturen relevant sind. Wenn Sie Daten umgebungsübergreifend korrelieren möchten, können Sie [Diagramme für mehrere Ressourcen im Metrik-Explorer](./platform/metrics-charts.md) verwenden oder [ressourcenübergreifende Abfragen in Azure Monitor](log-query/cross-workspace-query.md) erstellen.


## <a name="create-actionable-alerts-with-actions"></a>Erstellen von handlungsrelevanten Warnungen mit Aktionen
Ein kritischer Aspekt der Überwachung ist die proaktive Benachrichtigung von Administratoren über aktuelle und vorhergesagte Probleme. 

- Erstellen Sie [Warnungen in Azure Monitor](./platform/alerts-overview.md) basierend auf Protokollen und Metriken, um vorhersehbare Fehlerstatus zu identifizieren. Sie sollten das Ziel haben, dass alle Warnungen handlungsrelevant sind. Das bedeutet, dass sie tatsächliche kritische Bedingungen darstellen und versuchen, falsch positive Ergebnisse zu reduzieren. Verwenden Sie [dynamische Schwellenwerte](platform/alerts-dynamic-thresholds.md), um automatisch Grundwerte für Metrikdaten zu berechnen, anstatt Ihre eigenen statischen Schwellenwerte zu definieren. 
- Definieren Sie Aktionen für Warnungen, um die effektivsten Mittel zur Benachrichtigung Ihrer Administratoren zu nutzen. Verfügbare [Aktionen für Benachrichtigungen](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) sind SMS, E-Mails, Pushbenachrichtigungen oder Sprachanrufe.
- Verwenden Sie erweiterte Aktionen, um [eine Verbindung zu Ihrem ITSM-Tool](platform/itsmc-overview.md) oder anderen Warnungsverwaltungssystemen über [Webhooks](platform/activity-log-alerts-webhook.md) herzustellen.
- Beheben Sie Situationen, die in Warnungen identifiziert wurden, auch mit [Azure Automation Runbooks](../automation/automation-webhooks.md) oder [Logic Apps](/connectors/custom-connectors/create-webhook-trigger), die mithilfe von Webhooks aus einer Warnung heraus gestartet werden können. 
- Verwenden Sie die [automatische Skalierung](./learn/tutorial-autoscale-performance-schedule.md), um Ihre Computeressourcen basierend auf gesammelten Metriken dynamisch zu erhöhen und zu verringern.

## <a name="prepare-dashboards-and-workbooks"></a>Vorbereiten von Dashboards und Arbeitsmappen
Die Sicherstellung, dass Entwicklung und Betrieb Zugriff auf dieselbe Telemetrie und dieselben Tools haben, ermöglicht es ihnen, Muster in der gesamten Umgebung anzuzeigen und die mittlere Zeit bis zur Erkennung (MTTD) und die mittlere Zeit bis zur Wiederherstellung (MTTR) zu minimieren.

- Bereiten Sie [benutzerdefinierte Dashboards](./learn/tutorial-app-dashboards.md) auf der Grundlage gemeinsamer Metriken und Protokolle für die verschiedenen Rollen in Ihrem Unternehmen vor. Dashboards können Daten aus allen Azure-Ressourcen kombinieren.
- Bereiten Sie [Arbeitsmappen](./platform/workbooks-overview.md) vor, um den Wissensaustausch zwischen Entwicklung und Betrieb sicherzustellen. Diese können als dynamische Berichte mit Metrikdiagrammen und Protokollabfragen oder auch als Leitfäden zur Problembehandlung von Entwicklern erstellt werden, die dem Kundensupport oder dem operativen Betrieb bei der Lösung grundlegender Probleme helfen.

## <a name="continuously-optimize"></a>Kontinuierliche Optimierung
 Die Überwachung ist einer der grundlegenden Aspekte der beliebten Erstellen-Messen-Lernen-Philosophie, die empfiehlt, Ihre KPIs und Benutzerverhaltensmetriken kontinuierlich zu verfolgen und dann durch Planungsiterationen zu optimieren. Azure Monitor hilft Ihnen, Metriken und Protokolle zu sammeln, die für Ihr Unternehmen relevant sind, und bei Bedarf neue Datenpunkte bei der nächsten Bereitstellung hinzuzufügen.

- Verwenden Sie Tools in Application Insights, um [das Verhalten und die Bindung von Endbenutzern zu verfolgen](./learn/tutorial-users.md).
- Verwenden Sie die [Auswirkungsanalyse](./app/usage-impact.md), um Ihnen zu helfen, die wichtigen Bereiche zu priorisieren, um entscheidende KPIs zu erreichen.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die verschiedenen Komponenten von [Azure Monitor](overview.md).
- [Hinzufügen der kontinuierlichen Überwachung](./app/continuous-monitoring.md) zur Releasepipeline