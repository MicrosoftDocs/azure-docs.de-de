---
title: Aktualisieren von Log Analytics-Dashboardvisualisierungen
description: Erfahren Sie, wie Sie Ihre Log Analytics-Dashboardvisualisierungen mit Abfragen aktualisieren können, die leistungsstarke Erkenntnisse ermöglichen.
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 96aea35a4796eae9d31062cedaf917a736f0fe82
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547128"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Aktualisieren von Log Analytics-Dashboardvisualisierungen

Im Februar 2020 haben wir eine verbesserte Visualisierungstechnologie eingeführt. Sie bietet optimierte und erweiterte Funktionen zum Visualisieren von Abfrageergebnissen und schnelle umfassende Erkenntnisse. 

Weitere Informationen zu dieser Aktualisierung finden Sie in diesem [Azure-Update](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Diese neue Visualisierungstechnologie ermöglicht Ihnen neue und verbesserte Funktionen rund um Ihre Abfrageergebnisse. 

## <a name="dashboards-in-azure"></a>Dashboards in Azure

Mit Azure-Dashboards können Sie den Status Ihrer gesamten Azure-Oberfläche visualisieren. Dashboards bieten eine zentrale Konsole für den Status Ihrer Azure-Umgebung und ermöglichen eine Vielzahl von Verknüpfungen für häufige Aktionen. 

Weitere Informationen finden Sie unter [Azure-Dashboards](../../azure-portal/azure-portal-dashboards.md).


## <a name="upgrading-log-analytics-dashboard-parts"></a>Aktualisieren von Log Analytics-Dashboardbereichen

Mit der neuen Visualisierungstechnologie werden einige häufige Probleme bei der alten Implementierung behoben und mehrere neue Funktionen für angeheftete Log Analytics-Bereiche eingeführt: 

- **Identische verfügbare Typen:** Alle in Log Analytics verfügbaren Visualisierungstypen sind als angeheftete Bereiche in Dashboards verfügbar.

- **Einheitliches Erscheinungsbild:** Das Erscheinungsbild der Visualisierung für angeheftete Bereiche ist jetzt nahezu identisch mit den Bereichen in Log Analytics. Die Unterschiede sind auf Optimierungen zurückzuführen, die geringe Unterschiede in den Dateninhalten der visuellen Elemente erforderlich machen. Weitere Informationen zu diesen Unterschieden finden Sie im Abschnitt „Überlegungen“ weiter unten.

- **QuickInfos und Bezeichnungen:** Die neuen angehefteten Log Analytics-Visualisierungen unterstützen QuickInfos. In Kreis- und Ringdiagrammen werden nun Bezeichnungen unterstützt.

- **Interaktive Legenden:** Durch Klicken auf die Visualisierungslegende können Dimensionen in angehefteten visuellen Elementen wie in Log Analytics hinzugefügt und entfernt werden.

## <a name="stage-1---opt-in-upgrade-message"></a>Phase 1: Nachricht zur optionalen Aktualisierung

Wenn ein angehefteter Log Analytics-Bereich aktualisiert werden kann, wird eine neue Benachrichtigung zur *optionalen* Aktualisierung in angehefteten Log Analytics-Bereichen in Dashboards angezeigt, sodass Benutzer die Visualisierung aktualisieren können. Wenn Sie die neuen Visualisierungen ausprobieren möchten, können Sie somit ausgewählte Visualisierungen im zugehörigen Dashboard aktualisieren.

 
![Seitenleiste](media/dashboard-upgrade/update-message-1.png)
 
![Screenshot einer Aktualisierung der Kachelvisualisierung](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Nach dem Veröffentlichen des Dashboards kann die Aktualisierung nicht mehr rückgängig gemacht werden. Änderungen werden jedoch verworfen, wenn Sie das Dashboard verlassen, ohne es neu zu veröffentlichen.  

Nach dem Klicken auf die Schaltfläche wird die Visualisierung auf die neue Technologie aktualisiert. Es können sich kleinere Unterschiede in der Visualisierung ergeben, um ihr Erscheinungsbild in Log Analytics anzugleichen.

Nach dem Aktualisieren der Visualisierungen müssen Sie das Dashboard neu veröffentlichen, damit die Änderungen wirksam werden.

![Screenshot mit aktualisierten Visualisierungen](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Phase 2: Migration aller Dashboards

Nach Ablauf eines anfänglichen Zeitraums für die optionale Aktualisierung aktualisiert das Log Analytics-Team alle Dashboards im System. Durch die Angleichung aller Azure-Dashboards kann das Team weitere Visualisierungen und allgemeine Optimierungen einführen.

## <a name="considerations"></a>Überlegungen

Log Analytics-Visualisierungen, die an ein Dashboard angeheftet sind, weisen ein spezifisches Verhalten zur optimalen Nutzung auf. Berücksichtigen Sie die folgenden Überlegungen bezüglich des Designs, wenn Sie eine Visualisierung an ein Dashboard anheften.

### <a name="query-time-scope---30-day-limit"></a>Zeitbereich für Abfragen: 30 Tage

Da Dashboards mehrere Visualisierungen aus verschiedenen Abfragen enthalten können, ist der Zeitbereich für eine einzelne angeheftete Abfrage auf 30 Tage beschränkt. Eine einzelne Abfrage kann nur für eine Zeitspanne von maximal 30 Tagen ausgeführt werden. Diese Einschränkung soll eine angemessene Ladezeit des Dashboards sicherstellen.

### <a name="query-data-values---25-values-and-other-grouping"></a>Datenwerte für Abfragen: 25 Werte und Gruppierung „Andere“

Dashboards können visuell kompakt und komplex sein. Um die Anzeige in einem Dashboard nicht zu überfrachten, haben wir die Visualisierungen optimiert und die Anzeige auf 25 unterschiedliche Datentypen beschränkt. Bei mehr als 25 Datentypen werden die Daten in Log Analytics optimiert. Die 25 Datentypen mit den meisten Daten werden separat angezeigt, die restlichen Werte werden unter dem Wert „Andere“ gruppiert. Ein Beispiel dafür ist im folgenden Diagramm dargestellt.  

![Screenshot eines Dashboards mit 25 verschiedenen Datentypen](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Dashboardaktualisierung beim Laden

Dashboards werden beim Laden aktualisiert. Alle Abfragen, die sich auf an Dashboards angeheftete Log Analytics-Visualisierungen beziehen, werden ausgeführt, und das Dashboard wird beim Laden entsprechend aktualisiert. Wenn die Dashboardseite geöffnet bleibt, werden die Daten im Dashboard alle 60 Minuten aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen und Freigeben von Dashboards in Log Analytics](../learn/tutorial-logs-dashboards.md)
