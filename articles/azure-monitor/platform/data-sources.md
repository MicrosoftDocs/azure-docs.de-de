---
title: Quellen für Daten in Azure Monitor| Microsoft-Dokumentation
description: Beschreibt die verfügbaren Daten zum Überwachen von Integrität und Leistung Ihrer Azure-Ressourcen und der darauf ausgeführten Anwendungen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: c6b976ca1beb9beb78f7c56d7ae512c1f499c2c5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186523"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Quellen für Überwachungsdaten für Azure Monitor
Azure Monitor basiert auf einer [allgemeinen Überwachungsdatenplattform](data-platform.md), die [Protokolle](data-platform-logs.md) und [Metriken](data-platform-metrics.md) umfasst. Das Sammeln von Daten auf dieser Plattform macht es möglich, Daten von mehreren Ressourcen zusammen mit einem gemeinsamen Satz von Tools in Azure Monitor zu analysieren. Überwachungsdaten werden ggf. auch zur Unterstützung bestimmter Szenarien an andere Speicherorte gesendet, und einige Ressourcen schreiben möglicherweise Daten an andere Speicherorte, bevor sie in Protokollen oder Metriken gesammelt werden können.

In diesem Artikel werden die unterschiedlichen Quellen für Überwachungsdaten beschrieben, die von Azure Monitor zusätzlich zu den von Azure-Ressourcen erstellten Überwachungsdaten erfasst werden. Es stehen Links zu detaillierten Informationen für die Konfiguration bereit, die zum Sammeln dieser Daten an unterschiedlichen Speicherorten erforderlich ist.

## <a name="application-tiers"></a>Anwendungsebenen

Quellen für Überwachungsdaten von Azure-Anwendungen können in Ebenen organisiert sein, wobei die höchste Ebene Ihre Anwendung selbst und die niedrigste Ebene die Komponenten der Azure-Plattform darstellen. Die Methode für den Zugriff auf Daten der jeweiligen Ebene ist unterschiedlich. Die Anwendungsebenen sind in der folgenden Tabelle zusammengefasst, und die Quellen für Überwachungsdaten der einzelnen Ebene sind in den folgenden Abschnitten aufgeführt. Eine Beschreibung der einzelnen Speicherorte und des Zugriffs auf die jeweiligen Daten finden Sie unter [Speicherorte für Überwachungsdaten in Azure](../monitor-reference.md).


![Überwachungsebenen](../media/overview/overview.png)


### <a name="azure"></a>Azure
Die folgende Tabelle enthält kurze Beschreibungen der Azure-spezifischen Anwendungsebenen. Folgen Sie dem Link, um zu weiteren Informationen in den folgenden Abschnitten zu gelangen.

| Tarif | BESCHREIBUNG | Sammlungsmethode |
|:---|:---|:---|
| [Azure-Mandant](#azure-tenant) | Daten zum Betrieb von Azure-Diensten auf Mandantenebene, z. B. Azure Active Directory. | Zeigen Sie AAD-Daten im Portal an, oder konfigurieren Sie die Sammlung in Azure Monitor mithilfe einer Diagnoseeinstellung für Mandanten. |
| [Azure-Abonnement](#azure-subscription) | Daten im Zusammenhang mit der Integrität und Verwaltung von ressourcenübergreifenden Diensten in Ihrem Azure-Abonnement, z.B. Resource Manager und Service Health. | Zeigen Sie die Daten im Portal an, oder konfigurieren Sie die Sammlung in Azure Monitor mithilfe eines Protokollprofils. |
| [Azure-Ressourcen](#azure-resources) |  Daten zu Betrieb und Leistung der einzelnen Azure-Ressourcen. | Metriken werden automatisch gesammelt. Die Anzeige erfolgt im Metrik-Explorer.<br>Konfigurieren Sie Diagnoseeinstellungen zum Sammeln von Protokollen in Azure Monitor.<br>Überwachungslösungen und Insights sind für eine detailliertere Überwachung für bestimmte Ressourcentypen verfügbar. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, andere Cloud oder lokal 
Die folgende Tabelle enthält kurze Beschreibungen der Anwendungsebenen, die in Azure, einer anderen Cloud oder lokal vorhanden sein können. Folgen Sie dem Link, um zu weiteren Informationen in den folgenden Abschnitten zu gelangen.

| Tarif | BESCHREIBUNG | Sammlungsmethode |
|:---|:---|:---|
| [Betriebssystem (Gast)](#operating-system-guest) | Daten zum Betriebssystem auf Computeressourcen. | Installieren Sie den Log Analytics-Agent, um Clientdatenquellen in Azure Monitor zu sammeln, und den Dependency-Agent, um Abhängigkeiten zu sammeln, die Azure Monitor für VMs unterstützen.<br>Für virtuelle Azure-Computer installieren Sie die Azure-Diagnoseerweiterung, um Protokolle und Metriken in Azure Monitor zu sammeln. |
| [Anwendungscode](#application-code) | Daten über die Leistung und Funktionalität der tatsächlichen Anwendung und des Codes, einschließlich Leistungsnachverfolgungen, Anwendungsprotokolle und Benutzertelemetrie. | Instrumentieren Sie den Code für das Sammeln von Daten in Application Insights. |
| [Benutzerdefinierte Quellen](#custom-sources) | Daten von externen Diensten oder anderen Komponenten oder Geräten. | Sammeln Sie Protokoll- oder Metrikdaten von einem beliebigen REST-Client in Azure Monitor. |

## <a name="azure-tenant"></a>Azure-Mandant
Die auf Ihren Azure-Mandanten bezogenen Telemetriedaten werden von mandantenweiten Diensten gesammelt, wie z.B. Azure Active Directory.

![Azure-Mandantensammlung](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory-Überwachungsprotokolle
[Azure Active Directory-Berichterstellung](../../active-directory/reports-monitoring/overview-reports.md) beinhaltet den Verlauf der Anmeldeaktivität und den Überwachungspfad von Änderungen, die innerhalb eines bestimmten Mandanten vorgenommen wurden. 

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Konfigurieren Sie Azure AD-Protokolle für das Sammeln in Azure Monitor, um diese mit anderen Überwachungsdaten zu analysieren. | [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle (Vorschauversion)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportieren Sie Azure AD-Protokolle zur Archivierung in Azure Storage. | [Tutorial: Archivieren von Azure AD-Protokollen in einem Azure-Speicherkonto (Vorschauversion)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Streamen Sie Azure AD-Protokolle mithilfe von Event Hubs an andere Speicherorte. | [Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub (Vorschauversion)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="azure-subscription"></a>Azure-Abonnement
Telemetriedaten im Zusammenhang mit Integrität und Betrieb Ihres Azure-Abonnements.

![Azure-Abonnement](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-Aktivitätsprotokoll 
Das [Azure-Aktivitätsprotokoll](platform-logs-overview.md) enthält Service Health-Datensätze sowie Datensätze zu jeglichen Konfigurationsänderungen, die an den Ressourcen in Ihrem Azure-Abonnement vorgenommen wurden. Das Aktivitätsprotokoll ist für alle Azure-Ressourcen verfügbar und stellt ihre _externe_ Ansicht dar.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|
| Aktivitätsprotokoll | Das Aktivitätsprotokoll wird in einem eigenen Datenspeicher gesammelt, den Sie über das Azure Monitor-Menü anzeigen oder zum Erstellen von Aktivitätsprotokollwarnungen verwenden können. | [Abfragen des Aktivitätsprotokolls im Azure-Portal](./activity-log.md#view-the-activity-log) |
| Azure Monitor-Protokolle | Konfigurieren Sie Azure Monitor-Protokolle zum Erfassen des Aktivitätsprotokolls, um es mit anderen Überwachungsdaten zu analysieren. | [Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](./activity-log.md) |
| Azure Storage | Exportieren Sie das Aktivitätsprotokoll zum Archivieren in Azure Storage. | [Archivieren des Aktivitätsprotokolls](./resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Streamen Sie das Aktivitätsprotokoll mithilfe von Event Hubs an andere Speicherorte. | [Streamen von Aktivitätsprotokollen an Event Hubs](./resource-logs.md#send-to-azure-event-hubs) |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) enthält Informationen zur Integrität der Azure-Dienste in Ihrem Abonnement, von denen Ihre Anwendung und Ressourcen abhängen.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Aktivitätsprotokoll<br>Azure Monitor-Protokolle | Service Health-Datensätze werden im Azure-Aktivitätsprotokoll gespeichert, sodass Sie sie im Azure-Portal anzeigen oder andere Aktivitäten ausführen können, die mit dem Aktivitätsprotokoll möglich sind. | [Anzeigen von Dienstintegritätsbenachrichtigungen im Azure-Portal](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Azure-Ressourcen
Metriken und Ressourcenprotokolle enthalten Informationen zum _internen_ Betrieb von Azure-Ressourcen. Diese sind für die meisten Azure-Dienste verfügbar, und Überwachungslösungen und Insights bieten zusätzliche Daten für bestimmte Dienste.

![Azure-Ressourcensammlung](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Plattformmetriken 
Die meisten Azure-Dienste senden [Plattformmetriken](data-platform-metrics.md), die ihre Leistung und ihren Betrieb widerspiegeln, direkt an die Metrikdatenbank. Die spezifischen [Metriken variieren je nach Ressourcentyp](metrics-supported.md). 

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Metriken | Plattformmetriken werden ohne Konfiguration in die Azure Monitor-Metrikdatenbank geschrieben. Greifen Sie über den Metrik-Explorer auf Plattformmetriken zu.  | [Erste Schritte mit dem Azure-Metrik-Explorer](metrics-getting-started.md)<br>[Unterstützte Metriken von Azure Monitor](metrics-supported.md) |
| Azure Monitor-Protokolle | Kopieren Sie Plattformmetriken in Protokolle zur Trend- und sonstigen Analyse mit Log Analytics. | [Azure-Diagnosen direkt an Log Analytics](./resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Streamen Sie Metriken mithilfe von Event Hubs an andere Speicherorte. |[Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Ressourcenprotokolle
[Ressourcenprotokolle](platform-logs-overview.md) bieten Einblicke in den _internen_ Betrieb einer Azure-Ressource.  Ressourcenprotokolle werden automatisch erstellt, doch müssen Sie eine Diagnoseeinstellung erstellen, um ein Ziel anzugeben, das für jede Ressource erfasst werden soll.

Die Konfigurationsanforderungen und der Inhalt der Ressourcenprotokolle sind je nach Ressourcentyp verschieden, und noch nicht alle Dienste erstellen diese. Ausführliche Informationen zu den einzelnen Diensten und Links zu detaillierten Konfigurationsverfahren finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Ressourcenprotokolle](./resource-logs-schema.md). Wenn der Dienst in diesem Artikel nicht aufgeführt ist, werden von ihm derzeit keine Ressourcenprotokolle erstellt.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Senden Sie Ressourcenprotokolle zur Analyse mit anderen gesammelten Protokolldaten an Azure Monitor-Protokolle. | [Erfassen von Azure-Ressourcenprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](./resource-logs.md#send-to-azure-storage) |
| Storage | Senden Sie Ressourcenprotokolle zur Archivierung an Azure Storage. | [Archivieren von Azure-Ressourcenprotokollen](./resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Streamen Sie Ressourcenprotokolle mithilfe von Event Hubs an andere Speicherorte. |[Streamen von Azure-Ressourcenprotokollen an Event Hubs](./resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Betriebssystem (Gast)
Computeressourcen in Azure, in anderen Clouds und lokal haben ein Gastbetriebssystem zu überwachen. Mit der Installation mindestens eines Agents können Sie Telemetriedaten aus dem Gastbetriebssystem in Azure Monitor sammeln, um sie mit denselben Überwachungstools wie die Azure-Dienste selbst zu analysieren.

![Azure-Computeressourcensammlung](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure-Diagnoseerweiterung
Durch Aktivieren der Azure-Diagnoseerweiterung für virtuelle Azure-Computer können Sie Protokolle und Metriken aus dem Gastbetriebssystem von Azure-Computeressourcen sammeln, einschließlich Web- und Workerrollen des Azure-Clouddiensts (klassisch), Virtual Machines, Skalierungsgruppen von Virtual Machines und Service Fabric.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Storage | Die Azure-Diagnoseerweiterung schreibt immer in ein Azure Storage-Konto. | [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](diagnostics-extension-windows-install.md)<br>[Verwenden der Linux-Diagnoseerweiterung zum Überwachen von Metriken und Protokollen](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor-Metriken | Wenn Sie die Diagnoseerweiterung zum Sammeln von Leistungsindikatoren konfigurieren, werden diese in die Azure Monitor-Metrikdatenbank geschrieben. | [Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für einen virtuellen Windows-Computer](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Konfigurieren Sie die Diagnoseerweiterung für das Streamen von Daten an andere Speicherorte mithilfe von Event Hubs.  | [Streamen von Azure-Diagnosedaten mit Event Hubs](diagnostics-extension-stream-event-hubs.md)<br>[Verwenden der Linux-Diagnoseerweiterung zum Überwachen von Metriken und Protokollen](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights-Protokolle | Sammeln Sie Protokolle und Leistungsindikatoren von den Computeressourcen, die Ihre Anwendung unterstützen, um sie mit anderen Anwendungsdaten zu analysieren. | [Senden von Cloud Services-, Virtual Machines- oder Service Fabric-Diagnosedaten an Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-Agent 
Installieren Sie den Log Analytics-Agent für eine umfassende Überwachung und Verwaltung Ihrer virtuellen Windows- oder Linux-Computer. Der virtuelle Computer kann in Azure, einer anderen Cloud oder lokal ausgeführt werden.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Der Log Analytics-Agent stellt entweder direkt oder über System Center Operations Manager eine Verbindung mit Azure Monitor her und ermöglicht Ihnen, Daten aus Datenquellen zu sammeln, die Sie konfigurieren, oder aus Überwachungslösungen, die zusätzliche Einblicke in Anwendungen ermöglichen, die auf dem virtuellen Computer ausgeführt werden. | [Agent-Datenquellen in Azure Monitor](agent-data-sources.md)<br>[Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](om-agents.md) |
| VM-Speicher | Azure Monitor für VMs verwendet den Log Analytics-Agent zur Speicherung von Informationen zum Integritätsstatus an einem benutzerdefinierten Speicherort. Weitere Informationen finden Sie im nächsten Abschnitt.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs 
[Azure Monitor für VMs](../insights/vminsights-overview.md) stellt eine angepasste Überwachungsoberfläche für virtuelle Computer mit Funktionen bereit, die über die grundlegende Azure Monitor-Funktionalität hinausgehen. Es ist ein Dependency-Agent auf virtuellen Windows- und Linux-Computern erforderlich, der in den Log Analytics-Agent integriert wird, um ermittelte Daten zu Prozessen, die auf dem virtuellen Computer ausgeführt werden, und externen Prozessabhängigkeiten zu sammeln.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Speichert Daten zu Prozessen und Abhängigkeiten auf dem Agent. | [Verwenden der Zuordnung in Azure Monitor für VMs (Vorschauversion) zum Verstehen von Anwendungskomponenten](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Anwendungscode
Die detaillierte Anwendungsüberwachung in Azure Monitor erfolgt mit [Application Insights](/azure/application-insights/). Dieser Dienst sammelt Daten von Anwendungen, die auf einer Vielzahl von Plattformen ausgeführt werden. Die Anwendung kann in Azure, einer anderen Cloud oder lokal ausgeführt werden.

![Anwendungsdatensammlung](media/data-sources/applications.png)


### <a name="application-data"></a>Anwendungsdaten
Wenn Sie Application Insights durch Installation eines Instrumentierungspakets für eine Anwendung aktivieren, werden auf Leistung und Betrieb der Anwendung bezogene Metriken und Protokolle gesammelt. Application Insights speichert die gesammelten Daten auf derselben Azure Monitor-Datenplattform, die auch von anderen Datenquellen verwendet wird. Der Dienst enthält umfassende Tools zum Analysieren der Daten, aber Sie können sie auch mithilfe von Tools wie dem Metrik-Explorer und Log Analytics mit Daten aus anderen Quellen analysieren.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Operative Daten zu Ihrer Anwendung, einschließlich Seitenaufrufe, Anwendungsanforderungen, Ausnahmen und Ablaufverfolgungen. | [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informationen zu Abhängigkeiten zwischen Anwendungskomponenten zur Unterstützung der Anwendungszuordnung und Telemetriekorrelation. | [Telemetriekorrelation in Application Insights](../app/correlation.md) <br> [Anwendungszuordnung](../app/app-map.md) |
|            | Ergebnisse von Verfügbarkeitstests, mit denen die Verfügbarkeit und Reaktionsfähigkeit der Anwendung von unterschiedlichen Standorten im öffentlichen Internet aus getestet werden. | [Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites](../app/monitor-web-app-availability.md) |
| Azure Monitor-Metriken | Application Insights erfasst Metriken, die Leistung und Betrieb der Anwendung beschreiben, zusätzlich zu benutzerdefinierten Metriken, die Sie in Ihrer Anwendung definieren, in der Azure Monitor-Metrikdatenbank. | [Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights-API für benutzerdefinierte Ereignisse und Metriken](../app/api-custom-events-metrics.md) |
| Azure Storage | Senden Sie Anwendungsdaten zur Archivierung an Azure Storage. | [Exportieren von Telemetriedaten aus Application Insights](../app/export-telemetry.md) |
|            | Details von Verfügbarkeitstests werden in Azure Storage gespeichert. Verwenden Sie Application Insights im Azure-Portal zum Herunterladen für die lokale Analyse. Ergebnisse von Verfügbarkeitstests werden in Azure Monitor-Protokollen gespeichert. | [Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites](../app/monitor-web-app-availability.md) |
|            | Profiler-Ablaufverfolgungsdaten werden in Azure Storage gespeichert. Verwenden Sie Application Insights im Azure-Portal zum Herunterladen für die lokale Analyse.  | [Profilerstellung für Produktionsanwendungen in Azure mit Application Insights Profiler](../app/profiler-overview.md) 
|            | Daten zu Debugmomentaufnahmen, die für eine Teilmenge von Ausnahmen erfasst werden, werden in Azure Storage gespeichert. Verwenden Sie Application Insights im Azure-Portal zum Herunterladen für die lokale Analyse.  | [Funktionsweise von Momentaufnahmen](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Überwachungslösungen und Insights
[Überwachungslösungen](../insights/solutions.md) und [Insights](../monitor-reference.md) sammeln Daten, um zusätzliche Erkenntnisse zum Betrieb eines bestimmten Diensts oder einer bestimmten Anwendung zu liefern. Dazu können Ressourcen auf verschiedenen Anwendungsebenen und sogar mehreren Ebenen verwendet werden.

### <a name="monitoring-solutions"></a>Überwachungslösungen

| Destination | BESCHREIBUNG | Verweis
|:---|:---|:---|
| Azure Monitor-Protokolle | Überwachungslösungen sammeln Daten in Azure Monitor-Protokollen, wo sie mit der Abfragesprache oder [Ansichten](view-designer.md), die in der Regel in der Lösung enthalten sind, analysiert werden können. | [Ausführliche Informationen zu Datensammlungen für Überwachungslösungen in Azure](../monitor-reference.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor für Container
[Azure Monitor für Container](../insights/container-insights-overview.md) stellt eine angepasste Überwachungsoberfläche für [Azure Kubernetes Service (AKS)](../../aks/index.yml) bereit. Hiermit werden zusätzliche Daten über diese Ressourcen gesammelt, die in der folgenden Tabelle beschrieben sind.

| Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|
| Azure Monitor-Protokolle | Speichert Überwachungsdaten für AKS, einschließlich Bestand, Protokolle und Ereignisse. Metrikdaten werden ebenfalls in Protokollen gespeichert, um deren Analysefunktionen im Portal zu nutzen. | [Verstehen der Leistung von AKS-Clustern mit Azure Monitor für Container](../insights/container-insights-analyze.md) |
| Azure Monitor-Metriken | Metrikdaten werden in der Metrikdatenbank für die Visualisierung und Warnungen gespeichert. | [Anzeigen von Containermetriken im Metrik-Explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Bietet direkten Zugriff auf Ihre Azure Kubernetes Service-Containerprotokolle (stdout/stderror), -Ereignisse und -Podmetriken im Portal. | [Anzeigen von Kubernetes-Protokollen, -Ereignissen und -Podmetriken in Echtzeit ](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs
[Azure Monitor für VMs](../insights/vminsights-overview.md) bietet eine angepasste Oberfläche für die Überwachung virtueller Computer. Eine Beschreibung der von Azure Monitor für VMs gesammelten Daten enthält der Abschnitt [Betriebssystem (Gast)](#operating-system-guest) weiter oben.

## <a name="custom-sources"></a>Benutzerdefinierte Quellen
Über die Standardebenen einer Anwendung hinaus müssen Sie möglicherweise weitere Ressourcen überwachen, deren Telemetrie nicht mit den anderen Datenquellen erfasst werden kann. Für diese Ressourcen schreiben Sie die Daten mithilfe einer Azure Monitor-API entweder in Metriken oder Protokolle.

![Benutzerdefinierte Sammlung](media/data-sources/custom.png)

| Destination | Methode | BESCHREIBUNG | Verweis |
|:---|:---|:---|:---|
| Azure Monitor-Protokolle | Data Collector API (Datensammler-API) | Sammeln Sie Protokolldaten von einem beliebigen REST-Client, und speichern Sie sie im Log Analytics-Arbeitsbereich. | [Senden von Protokolldaten an Azure Monitor mit der HTTP-Datensammler-API (Public Preview)](data-collector-api.md) |
| Azure Monitor-Metriken | API für benutzerdefinierte Metriken | Sammeln Sie Metrikdaten von einem beliebigen REST-Client, und speichern Sie sie in der Azure Monitor-Metrikdatenbank. | [Senden benutzerdefinierter Metriken für eine Azure-Ressource an den Azure Monitor-Metrikspeicher mithilfe einer REST-API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Sonstige Dienste
Sonstige Dienste in Azure schreiben Daten auf die Azure Monitor-Datenplattform. Dadurch können Sie Daten, die von diesen Diensten gesammelt werden, mit den von Azure Monitor gesammelten Daten analysieren und dieselben Analyse- und Virtualisierungstools nutzen.

| Dienst | Destination | BESCHREIBUNG | Verweis |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Azure Monitor-Protokolle | Azure Security Center speichert die gesammelten Sicherheitsdaten in einem Log Analytics-Arbeitsbereich und ermöglicht so die Analyse mit anderen von Azure Monitor gesammelten Protokolldaten.  | [Datensammlung in Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Azure Monitor-Protokolle | Azure Sentinel speichert die aus verschiedenen Datenquellen gesammelten Daten in einem Log Analytics-Arbeitsbereich und ermöglicht so die Analyse mit anderen von Azure Monitor gesammelten Protokolldaten.  | [Herstellen einer Verbindung mit Datenquellen](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Typen der von Azure Monitor gesammelten Überwachungsdaten](data-platform.md) und wie diese Daten angezeigt und analysiert werden.
- Listen Sie die [verschiedenen Speicherorte auf, an denen Azure-Ressourcen Daten speichern](../monitor-reference.md), und erfahren Sie, wie Sie darauf zugreifen können.