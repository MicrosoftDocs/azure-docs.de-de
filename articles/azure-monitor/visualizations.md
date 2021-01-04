---
title: Visualisieren von Daten aus Azure Monitor | Microsoft-Dokumentation
description: Enthält eine Zusammenfassung der verfügbaren Methoden zum Visualisieren von in Azure Monitor gespeicherten Metrik- und Protokolldaten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: f9f95a5e85ec4eebf82cca707476750595331d73
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522667"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisieren von Daten aus Azure Monitor
Dieser Artikel enthält eine Zusammenfassung der verfügbaren Methoden zum Visualisieren von in Azure Monitor gespeicherten Metrik- und Protokolldaten.

Visualisierungen wie Diagramme und Grafiken können Ihnen die Analyse Ihrer Überwachungsdaten zur Anzeige von Detailinformationen zu Problemen und zum Identifizieren von Mustern erleichtern. Je nach dem Tool, das Sie verwenden, haben Sie vielleicht auch die Möglichkeit, Visualisierungen für andere Benutzer innerhalb und außerhalb Ihrer Organisation freizugeben.

## <a name="workbooks"></a>Arbeitsmappen
[Arbeitsmappen](./platform/workbooks-overview.md) sind interaktive Dokumente, die umfassende Einblicke in Ihre Daten, Untersuchungen und die Zusammenarbeit innerhalb des Teams bereitstellen. Spezifische Beispiele, wo Arbeitsmappen hilfreich sind, sind Anleitungen zur Problembehandlung und Nachbesprechungen zu Incidents.

![Das Diagramm zeigt Screenshots mehrerer Seiten einer Arbeitsmappe, einschließlich der Analyse der Seitenaufrufe, der Nutzung und der auf der Seite verbrachten Zeit.](media/visualizations/workbook.png)

### <a name="advantages"></a>Vorteile
- Sowohl Metriken als auch Protokolle werden unterstützt.
- Unterstützt Parameter, die interaktive Berichte ermöglichen, wobei durch die Auswahl eines Elements in einer Tabelle zugeordnete Diagramme und Visualisierungen dynamisch aktualisiert werden.
- Dokumentähnlicher Ablauf.
- Option für persönliche oder freigegebene Arbeitsmappen.
- Einfache und zusammenarbeitsfreundliche Benutzeroberfläche für die Erstellung.
- Die Vorlagen unterstützen den öffentlichen Vorlagenkatalog auf GitHub-Basis.

### <a name="limitations"></a>Einschränkungen
- Keine automatische Aktualisierung.
- Kein dichtes Layout wie bei Dashboards, sodass Arbeitsmappen weniger als zentrale Konsole geeignet sind. Eher für tiefere Einblicke vorgesehen.


## <a name="azure-dashboards"></a>Azure-Dashboards
[Azure-Dashboards](../azure-portal/azure-portal-dashboards.md) sind die primäre Dashboardtechnologie für Azure. Sie sind besonders nützlich zur Bereitstellung einer zentralen Konsole für Ihre Azure-Infrastruktur und Dienste, sodass Sie wichtige Probleme schnell identifizieren können.

![Screenshot eines Beispiels für ein Azure-Dashboard mit anpassbaren Informationen](media/visualizations/dashboard.png)

Im Folgenden finden Sie eine Videoanleitung zum Erstellen von Dashboards.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Vorteile
- Enge Integration in Azure. Visualisierungen aus mehreren Azure-Seiten einschließlich Metriken-Explorer, Log Analytics und Application Insights können an Dashboards geheftet werden.
- Sowohl Metriken als auch Protokolle werden unterstützt.
- Die Daten aus mehreren Quellen einschließlich der Ausgabe von [Metriken-Explorer](platform/metrics-charts.md) und [Protokollabfragen](log-query/log-query-overview.md) sowie [Karten](app/app-map.md) und Verfügbarkeit in Application Insights werden kombiniert.
- Option für persönliche oder freigegebene Dashboards. Integriert in [Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung in Azure)](../role-based-access-control/overview.md).
- Automatische Aktualisierung. Metriken werden abhängig vom Zeitbereich mindestens alle fünf Minuten aktualisiert. Protokolle werden stündlich aktualisiert, können mit einer Option für die manuelle Aktualisierung bei Bedarf aktualisiert werden (durch Klicken auf das Symbol „Aktualisieren“ für eine bestimmte Visualisierung) oder werden durch eine Aktualisierung des gesamten Dashboards aktualisiert.
- Dashboards mit parametrisierten Metriken mit Zeitstempel und benutzerdefinierten Parametern.
- Flexible Layoutoptionen.
- Vollbildmodus.


### <a name="limitations"></a>Einschränkungen
- Eingeschränkte Kontrolle über Protokollvisualisierungen ohne Unterstützung für Datentabellen. Die Gesamtanzahl der Datenreihen ist auf 10 begrenzt, wobei weitere Datenreihen unter einem _anderen_ Bucket gruppiert sind.
- Benutzerdefinierte Parameter für Protokolldiagramme werden nicht unterstützt.
- Protokolldiagramme sind auf die letzten 30 Tage beschränkt.
- Protokolldiagramme können nur in freigegebenen Dashboards angeheftet werden.
- Keine Interaktivität mit Dashboarddaten.
- Eingeschränkte kontextbezogene Anzeige von Detailinformationen.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) ist besonders nützlich zum Erstellen von geschäftskritischen Dashboards und Berichten sowie Berichten, die langfristige KPI-Trends analysieren. Sie können [die Ergebnisse einer Protokollabfrage in ein Power BI-Dataset importieren](platform/powerbi.md), damit Sie dessen Features wie das Kombinieren von Daten aus verschiedenen Quellen und das Freigeben von Berichten im Web und auf mobilen Geräten nutzen können.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vorteile
- Umfassende Visualisierungen.
- Umfassende Interaktivität einschließlich Zoomen und Kreuzfilterung.
- Mühelose Freigabe in Ihrer gesamten Organisation.
- Integration in andere Daten aus mehreren Datenquellen.
- Bessere Leistung mit Ergebnissen, die in einem Cube zwischengespeichert werden.


### <a name="limitations"></a>Einschränkungen
- Unterstützt Protokolle, jedoch keine Metriken.
- Keine Azure-Integration. Dashboards und Modelle können nicht über Azure Resource Manager verwaltet werden.
- Abfrageergebnisse müssen zum Konfigurieren in ein Power BI-Modell importiert werden. Einschränkung der Ergebnisgröße und Aktualisierung.
- Einschränkung der Datenaktualisierung auf acht Mal pro Tag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) ist eine offene Plattform, die für operationale Dashboards ideal ist. Sie ist besonders nützlich zum Erkennen, Isolieren und Selektieren von Betriebsincidents. Sie können das [Grafana Azure Monitor-Datenquellen-Plug-in](platform/grafana-plugin.md) Ihrem Azure-Abonnement hinzufügen, damit es Ihre Azure-Metrikdaten visualisiert.

![Screenshot von Grafana-Visualisierungen](media/visualizations/grafana.png)

### <a name="advantages"></a>Vorteile
- Umfassende Visualisierungen.
- Umfangreiches Datenquellenökosystem.
- Dateninteraktivität inklusive Zoomen.
- Unterstützt Parameter.

### <a name="limitations"></a>Einschränkungen
- Keine Azure-Integration. Dashboards und Modelle können nicht über Azure Resource Manager verwaltet werden.
- Kosten zur Unterstützung zusätzlicher Grafana-Infrastruktur oder zusätzliche Kosten für Grafana-Cloud.


## <a name="build-your-own-custom-application"></a>Erstellen Sie Ihre eigene benutzerdefinierte Anwendung
Sie können auf Protokoll- und Metrikdaten in Azure Monitor mit einem beliebigen REST-Client über die zugehörige API zugreifen, sodass Sie Ihre eigenen benutzerdefinierten Websites und Anwendungen erstellen können.

### <a name="advantages"></a>Vorteile
- Vollständige Flexibilität bei Benutzeroberfläche, Visualisierung, Interaktivität und Funktionen.
- Kombinieren von Metriken und Protokolldaten mit anderen Datenquellen.

### <a name="disadvantages"></a>Nachteile
- Großer Entwicklungsaufwand erforderlich.


## <a name="azure-monitor-views"></a>Azure Monitor-Ansichten

> [!IMPORTANT]
> Ansichten werden zurzeit ausgesondert. Anleitungen zum Umwandeln von Ansichten in Arbeitsmappen finden Sie im [Handbuch für den Übergang vom Azure Monitor-Ansicht-Designer zu Arbeitsmappen](platform/view-designer-conversion-overview.md).

Mit [Ansichten in Azure Monitor](platform/view-designer.md) erstellen Sie benutzerdefinierte Visualisierungen mit Protokolldaten. Sie werden von [Überwachungslösungen](insights/solutions.md) zur Präsentation der Daten verwendet, die sie sammeln.


![Screenshot einer Kachel „Containerüberwachungslösung“ und der detaillierten Azure Monitor-Ansicht, die bei deren Auswahl geöffnet wird](media/visualizations/view.png)

### <a name="advantages"></a>Vorteile
- Umfangreiche Visualisierungen für die Protokolldaten.
- Exportieren und Importieren von Ansichten, um sie in andere Ressourcengruppen und Abonnements zu übertragen.
- Integriert in das Azure Monitor-Verwaltungsmodell mit Arbeitsbereichen und Überwachungslösungen.
- [Filtern](platform/view-designer-filters.md) nach benutzerdefinierten Parametern.
- Interaktiv, unterstützt Drill-In (Sicht, die Detailinformationen einer anderen Ansicht zeigt) mit mehreren Ebenen.

### <a name="limitations"></a>Einschränkungen
- Unterstützt Protokolle, jedoch keine Metriken.
- Keine persönlichen Ansichten. Für alle Benutzer mit Zugriff auf den Arbeitsbereich verfügbar.
- Keine automatische Aktualisierung.
- Begrenzte Layoutoptionen.
- Keine Unterstützung von Abfragen über mehrere Arbeitsbereiche oder Application Insights-Anwendungen hinweg.
- Die Antwortgröße für Abfragen ist auf 8MB und die Abfrageausführungszeit auf 110 Sekunden beschränkt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum [Sammeln von Daten in Azure Monitor](platform/data-platform.md).
- Weitere Informationen zum [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).
- Weitere Informationen zum [Erstellen benutzerdefinierter Ansichten mithilfe des Ansicht-Designers in Log Analytics](platform/view-designer.md).
- Weitere Informationen zu [Arbeitsmappen](./platform/workbooks-overview.md).
- Weitere Informationen zum [Importieren von Log Analytics-Daten in Power BI](./platform/powerbi.md).
- Weitere Informationen zum [Überwachen Ihrer Azure-Dienste in Grafana](./platform/grafana-plugin.md).

