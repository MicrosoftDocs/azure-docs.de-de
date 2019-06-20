---
title: Azure Monitor-Übersicht | Microsoft-Dokumentation
description: Übersicht über die Microsoft-Dienste und -Funktionen, die zu einer vollständigen Überwachungsstrategie für Ihre Azure-Dienste und -Anwendungen beitragen.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: c72ac62b55b2b08be5aaad563933bcb2b703cba9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245076"
---
# <a name="azure-monitor-overview"></a>Azure Monitor – Übersicht

Azure Monitor maximiert die Verfügbarkeit und Leistung Ihrer Anwendungen durch die Bereitstellung einer umfassenden Lösung für das Sammeln, Analysieren und Reagieren auf Telemetriedaten aus Ihren cloudbasierten und lokalen Umgebungen. Diese Lösung hilft Ihnen, die Leistung Ihrer Anwendungen zu verstehen, und erkennt proaktiv Probleme, die sich auf sie auswirken, und Ressourcen, von denen sie abhängen.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Übersicht
Das folgende Diagramm zeigt eine allgemeine Übersicht von Azure Monitor. In der Mitte des Diagramms finden Sie die Datenspeicher für Metriken und Protokolle, die beiden grundlegenden Datenarten, mit denen Azure Monitor arbeitet. Auf der linken Seite befinden sich die [Quellen für Überwachungsdaten](platform/data-sources.md), die diese [Datenspeicher](platform/data-platform.md) ausfüllen. Auf der rechten Seite finden Sie die verschiedenen Funktionen, die Azure Monitor mit diesen gesammelten Daten ausführt, wie etwa Analyse, Ausgeben von Warnungen und Streamen auf externe Systeme.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Azure Monitor – Übersicht](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Überwachungsdaten-Plattform
Alle von Azure Monitor gesammelten Daten gehören einem von zwei Grundtypen an, [Metriken und Protokolle](platform/data-platform.md). [Metriken](platform/data-platform-metrics.md) sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Sie sind einfach strukturiert und in der Lage, Szenarien nahezu in Echtzeit zu unterstützen. [Protokolle](platform/data-platform-logs.md) enthalten verschiedene Arten von Daten, die in Datensätzen mit unterschiedlichen Eigenschaften für jeden Typ organisiert sind. Telemetriedaten wie etwa Ereignisse und Ablaufverfolgungen werden als Protokolle zusätzlich zu Leistungsdaten gespeichert, die alle zur Analyse kombiniert werden können.

Für viele Azure-Ressourcen können die von Azure Monitor gesammelten Daten direkt auf ihrer Übersichtsseite im Azure-Portal angezeigt werden. Werfen Sie beispielsweise einen Blick auf eine beliebige VM, dann sehen Sie eine Reihe von Diagrammen, die Leistungsmetriken darstellen. Klicken Sie auf eines dieser Diagramme, um die Daten im Azure-Portal im [Metrik-Explorer](platform/metrics-charts.md) anzuzeigen. Hier können Sie die Werte mehrerer Metriken im zeitlichen Verlauf als Diagramm darstellen.  Sie können die Diagramme interaktiv nutzen oder an ein Dashboard anheften, um sie mit anderen Visualisierungstools anzuzeigen.

![metrics](media/overview/metrics.png)

Die in Azure Monitor gesammelten Protokolldaten können mit [Abfragen](log-query/log-query-overview.md) analysiert werden, die die gesammelten Daten schnell abrufen, konsolidieren und analysieren.  Sie können Abfragen mit [Log Analytics](log-query/portals.md) im Azure-Portal erstellen und testen und die Daten dann entweder mit diesen Tools direkt analysieren oder Abfragen zur Verwendung mit [Visualisierungen](visualizations.md) oder [Warnungsregeln](platform/alerts-overview.md) speichern.

Azure Monitor verwendet eine Version der von Azure Data Explorer verwendeten [Abfragesprache Kusto](/azure/kusto/query/), die für einfache Protokollabfragen geeignet ist, aber auch erweiterte Funktionen enthält, z.B. Aggregationen, Verknüpfungen und intelligente Analysen. Sie können die Abfragesprache schnell erlernen, indem Sie [mehrere Lektionen](log-query/get-started-queries.md) nutzen.  Es ist eine spezielle Anleitung für Benutzer vorhanden, die bereits mit [SQL](log-query/sql-cheatsheet.md) und [Splunk](log-query/splunk-cheatsheet.md) vertraut sind.

![Protokolle](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Welche Daten sammelt Azure Monitor?
Azure Monitor kann Daten aus vielen verschiedenen Quellen sammeln. Sie können sich das Überwachen von Daten für Ihre Anwendungen in Ebenen vorstellen, die von Ihrer Anwendung über Betriebssystem und Dienste, auf denen sie aufbaut, bis zur Plattform selbst hinunterreichen. Azure Monitor sammelt Daten aus jeder der folgenden Schichten:

- **Überwachungsdaten zu Anwendungen**: Daten zur Leistung und Funktionalität des von Ihnen geschriebenen Codes, unabhängig von seiner Plattform.
- **Überwachungsdaten zum Gast-BS:** Daten zum Betriebssystem, unter dem die Anwendung ausgeführt wird. Es kann in Azure, einer anderen Cloud oder lokal ausgeführt werden. 
- **Überwachungsdaten zur Azure-Ressource:** Daten zum Betrieb einer Azure-Ressource.
- **Überwachungsdaten zum Azure-Abonnement**: Daten zum Betrieb und zur Verwaltung eines Azure-Abonnements sowie Daten zur Integrität und zum Betrieb von Azure selbst. 
- **Überwachungsdaten zu Azure-Mandanten**: Daten zum Betrieb von Azure-Diensten auf Mandantenebene, z. B. Azure Active Directory.

Sobald Sie ein Azure-Abonnement erstellen und damit beginnen, ihm Ressourcen hinzuzufügen, wie etwa VMs und Web-Apps, beginnt Azure Monitor mit dem Sammeln von Daten.  [Aktivitätsprotokolle](platform/activity-logs-overview.md) zeichnen auf, wenn Ressourcen erstellt oder geändert werden. [Metriken](platform/data-platform.md) teilen Ihnen mit, welche Leistung die Ressource aufweist und welche Ressourcen sie nutzt. 

Erweitern Sie die erfassten Daten in den eigentlichen Betrieb hinein, indem Sie [Diagnose aktivieren](platform/diagnostic-logs-overview.md) und zu Computeressourcen [einen Agent hinzufügen](platform/agent-windows.md). Dadurch werden Telemetriedaten für den internen Betrieb der Ressource gesammelt, und Sie erhalten die Möglichkeit, verschiedene [Datenquellen](platform/agent-data-sources.md) zu konfigurieren, um Protokolle und Metriken von Windows- und Linux-Gastbetriebssystemen zu erfassen. 

[Fügen Sie Ihrer Anwendung ein Instrumentierungspaket hinzu](app/azure-web-apps.md), um Application Insights das Sammeln von detaillierten Informationen über Ihre Anwendung zu ermöglichen, einschließlich der Anzahl der Seitenaufrufe, Anwendungsanforderungen und Ausnahmen. Überprüfen Sie darüber hinaus die Verfügbarkeit Ihrer Anwendung, indem Sie einen [Verfügbarkeitstest](app/monitor-web-app-availability.md) konfigurieren, um Benutzerdatenverkehr zu simulieren.

### <a name="custom-sources"></a>Benutzerdefinierte Quellen
Azure Monitor kann mithilfe der [Datensammler-API](platform/data-collector-api.md) Protokolldaten von jedem REST-Client erfassen. Dies erlaubt Ihnen das Erstellen von benutzerdefinierten Überwachungsszenarien und das Ausweiten der Überwachung auf Ressourcen, die Telemetriedaten nicht durch andere Quellen verfügbar machen.



## <a name="insights"></a>Einblicke
Das Überwachen von Daten ist nur nützlich, wenn dadurch Ihre Einsicht in den Betrieb Ihrer Computerumgebung ausgeweitet wird. Azure Monitor enthält eine Reihe von Features und Tools, die wertvolle Einblicke in Ihre Anwendungen und weitere Ressourcen bieten, von denen sie abhängen. [Überwachungslösungen](insights/solutions.md) und -features wie [Application Insights](app/app-insights-overview.md) und [Azure Monitor für Container](insights/container-insights-overview.md) ermöglichen umfassende Einblicke in verschiedene Aspekte Ihrer Anwendung und bestimmter Azure-Dienste. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) überwacht Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendungen, gleich ob sie in der Cloud oder lokal gehostet sind. Es nutzt die leistungsstarke Datenanalyseplattform in Azure Monitor, um Ihnen tiefgreifende Einblicke in den Betrieb Ihrer Anwendung zu geben und Fehler zu analysieren, ohne auf die Meldung durch einen Benutzer zu warten. Application Insights beinhaltet Verbindungspunkte zu einer Vielzahl von Entwicklungstools und lässt sich in Visual Studio integrieren, um Ihre DevOps-Prozesse zu unterstützen.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor für Container
[Azure Monitor für Container](insights/container-insights-overview.md) ist ein Feature zum Überwachen der Leistung von Containerworkloads, die in Managed Kubernetes-Clustern bereitgestellt und in Azure Kubernetes Service (AKS) gehostet sind. Es ermöglicht den Einblick in die Leistung, indem anhand der Metrik-API die in Kubernetes verfügbaren Speicher- und Prozessormetriken von Controllern, Knoten und Containern erfasst werden. Auch Containerprotokolle werden erfasst.  Nach der Aktivierung der Überwachung auf Kubernetes-Clustern werden diese Metriken und Protokolle für Sie automatisch mittels einer Containerversion des Log Analytics-Agents für Linux erfasst gespeichert.

![Containerintegrität](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs
[Azure Monitor für VMs](insights/vminsights-overview.md) überwacht Ihre virtuellen Azure-Computer im gewünschten Umfang, indem eine Analyse der Leistung und Integrität Ihrer Windows- und Linux-VMs durchgeführt wird. Dies schließt die verschiedenen Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen ein. Die Lösung umfasst Unterstützung für die Überwachung von Leistung und Anwendungsabhängigkeiten für VMs, die lokal oder bei einem anderen Cloudanbieter gehostet sind.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Überwachungslösungen
[Überwachungslösungen](insights/solutions.md) in Azure Monitor sind Programmpakete, die Einblicke für eine bestimmte Anwendung oder einen bestimmten Dienst liefern. Dazu gehören die Logik zum Sammeln von Überwachungsdaten für die Anwendung oder den Dienst, [Abfragen](log-query/log-query-overview.md) zur Analyse dieser Daten und [Ansichten](../log-analytics/log-analytics-view-designer.md) zur Visualisierung. Überwachungslösungen sind bei Partnern und [von Microsoft verfügbar](insights/solutions-inventory.md) und bieten Überwachung für verschiedene Azure-Dienste und andere Anwendungen.

![Überwachungslösungen](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reagieren auf kritische Situationen
Über die Möglichkeit zum interaktiven Analysieren von Überwachungsdaten hinaus muss eine effektive Überwachungslösung auch in der Lage sein, proaktiv auf kritische Bedingungen zu reagieren, die in den gesammelten Daten erkannt werden. Das kann durch Senden eines Texts oder einer E-Mail an einen für die Untersuchung eines Problems zuständigen Administrator erfolgen. Alternativ können Sie einen automatisierten Prozess starten, der versucht, einen Fehlerzustand zu korrigieren.


### <a name="alerts"></a>Alerts
[Warnungen in Azure Monitor](platform/alerts-overview.md) informieren Sie proaktiv über kritische Zustände und versuchen potenziell, Korrekturmaßnahmen einzuleiten. Warnungsregeln, die auf Metriken basieren, ermöglichen das Veröffentlichen von Warnungen nahezu in Echtzeit auf der Grundlage von numerischen Werten, während mit auf Protokollen basierenden Regeln komplexe Logik über Daten aus mehreren Quellen hinweg möglich ist.

Warnungsregeln in Azure Monitor verwenden [Aktionsgruppen](platform/action-groups.md), die eindeutige Sätze von Empfängern und Aktionen enthalten, die gemeinsam von mehreren Regeln übergreifend verwendet werden können. Je nach Ihren Anforderungen können Aktionsgruppen Aktionen wie das Verwenden von Webhooks ausführen, um externe Aktionen mithilfe von Warnungen zu starten oder die Integration in Ihre ITSM-Tools zu ermöglichen.

![Alerts](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
Bei der automatischen Skalierung können Sie jeweils die richtige Menge an Ressourcen ausführen, um die Lasten für Ihre Anwendung zu bewältigen. Es ermöglicht Ihnen das Erstellen von Regeln, die von Azure Monitor gesammelte Metriken verwenden, um zu bestimmen, wann Ressourcen automatisch hinzugefügt werden sollen, um eine höhere Last zu verarbeiten, und außerdem Geld zu sparen, indem Ressourcen entfernt werden, die nur im Leerlauf ausgeführt werden. Sie geben eine Minimal- und eine Maximalanzahl von Instanzen an und legen die Logik zum Herauf- oder Herabsetzen der Ressourcen fest.

![Autoscale](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualisieren von Überwachungsdaten
[Visualisierungen](visualizations.md) wie Diagramme und Tabellen sind effektive Tools zur Zusammenfassung von Überwachungsdaten und zu ihrer Präsentation für verschiedene Zielgruppen. Azure Monitor verfügt über eigene Features zum Visualisieren von Überwachungsdaten und nutzt andere Azure-Dienste, um sie für verschiedene Zielgruppen zu veröffentlichen.

### <a name="dashboards"></a>Dashboards
Mit [Azure Dashboards](../azure-portal/azure-portal-dashboards.md) können Sie verschiedene Arten von Daten, einschließlich Metriken und Protokolle, in einem einzelnen Bereich im [Azure-Portal](https://portal.azure.com) kombinieren. Sie können das Dashboard optional gemeinsam mit anderen Azure-Benutzern nutzen. Über die Ausgabe beliebiger Protokollabfragen oder Metrikdiagramme hinaus können Elemente im gesamten Azure Monitor einem Azure-Dashboard hinzugefügt werden. Beispielsweise können Sie ein Dashboard erstellen, das Kacheln kombiniert, die ein Diagramm der Metriken, eine Tabelle mit Aktivitätsprotokollen, ein Nutzungsdiagramm von Application Insights und die Ausgabe einer Protokollabfrage zeigen.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Ansichten
[Ansichten](../log-analytics/log-analytics-view-designer.md) stellen Protokolldaten in Azure Monitor visuell dar.  Jede Ansicht enthält eine einzelne Kachel, auf der ein Drilldown zu einer Kombination aus Visualisierungen möglich ist, z.B. Balken- und Liniendiagramme sowie Listen, in denen wichtige Daten zusammengefasst sind.  Überwachungslösungen enthalten Ansichten, in denen Daten für eine bestimmte Anwendung zusammengefasst sind, und Sie können Ihre eigenen Ansichten erstellen, um Daten aus beliebigen Protokollabfragen darzustellen. Wie andere Elemente in Azure Monitor können Ansichten zu Azure-Dashboards hinzugefügt werden.

![Sicht](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) ist ein Business Analytics-Dienst, der interaktive Visualisierungen für eine Vielzahl von Datenquellen bereitstellt und eine effektive Möglichkeit darstellt, Daten für andere Personen innerhalb und außerhalb Ihrer Organisation verfügbar zu machen. Sie können Power BI für den [automatischen Import von Protokolldaten aus Azure Monitor](../log-analytics/log-analytics-powerbi.md) konfigurieren, um diese zusätzlichen Visualisierungen zu nutzen.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrieren und Exportieren von Daten
Oft besteht die Anforderung, Azure Monitor in andere Systeme zu integrieren und benutzerdefinierte Lösungen zu erstellen, die Ihre Überwachungsdaten nutzen. Andere Azure-Dienste arbeiten mit Azure Monitor, um diese Integration zu ermöglichen.

### <a name="event-hub"></a>Event Hub
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) ist eine Streamingplattform und ein Datenerfassungsdienst, der Daten mithilfe beliebiger Echtzeit-Analyseanbieter oder Batchverarbeitungs-/Speicheradapter transformieren und speichern kann. Verwenden Sie Event Hubs für das [Streamen von Azure Monitor-Daten](platform/stream-monitoring-data-event-hubs.md) zu SIEM- und Überwachungstools von Partnern.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) ist ein Dienst, der Ihnen das Automatisieren von Aufgaben und Geschäftsprozessen mithilfe von Workflows ermöglicht, die sich in verschiedene Systeme und Dienste integrieren. Es sind Aktivitäten verfügbar, die Metriken und Protokolle in Azure Monitor lesen und schreiben. Dies ermöglicht Ihnen die Erstellung von Workflows, die sich in eine Vielzahl anderer Systeme integrieren lassen.


### <a name="api"></a>API
Über den Zugriff auf generierte Warnungen hinaus stehen mehrere APIs zur Verfügung, um Metriken und Protokolle in Azure Monitor zu schreiben und daraus zu lesen. Warnungen können darüber hinaus konfiguriert und abgerufen werden. Dadurch stehen Ihnen praktisch unbegrenzte Möglichkeiten zum Erstellen von benutzerdefinierten Lösungen zur Verfügung, die sich in Azure Monitor integrieren lassen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen:

* [Metriken und Protokolle](platform/data-platform.md) für die von Azure Monitor gesammelten Daten
* [Datenquellen](platform/data-sources.md) dazu, wie die verschiedenen Komponenten Ihrer Anwendung Telemetriedaten senden
* [Protokollabfragen](log-query/log-query-overview.md) zur Analyse der gesammelten Daten