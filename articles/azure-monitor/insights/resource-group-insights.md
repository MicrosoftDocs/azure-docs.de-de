---
title: Einblicke in Azure Monitor-Ressourcengruppen | Microsoft-Dokumentation
description: Erfahren Sie mit Azure Monitor mehr über die Integrität und Leistung Ihrer verteilten Anwendungen und Dienste auf Ressourcengruppenebene.
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: d5c07e0d4aca8bda42ea9f78a1475ea7bb5861f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119419"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Überwachen von Ressourcengruppen mit Azure Monitor (Vorschauversion)

Moderne Anwendungen sind oft komplex und weit verteilt, wobei viele einzelne Teile zusammenarbeiten, um einen Dienst bereitzustellen. Angesichts dieser Komplexität bietet Azure Monitor Überwachungseinblicke für Ressourcengruppen. Dies erleichtert die Selektierung und Diagnose von Problemen Ihrer individuellen Ressourcen und bietet gleichzeitig einen Kontext für die Integrität und Leistung der Ressourcengruppe (und Ihrer Anwendung) als Ganzes.

## <a name="access-insights-for-resource-groups"></a>Zugreifen auf Einblicke in Ressourcengruppen

1. Wählen Sie auf der linken Navigationsleiste **Ressourcengruppen** aus.
2. Wählen Sie eine Ihrer Ressourcengruppen, die Sie untersuchen möchten. (Wenn Sie eine große Anzahl von Ressourcengruppen haben, kann das Filtern nach Abonnement hilfreich sein.)
3. Um Einblicke in eine Ressourcengruppe zu erhalten, klicken Sie im linken Menü einer beliebigen Ressourcengruppe auf **Einblicke**.

![Screenshot der Übersichtsseite mit Einblicken in eine Ressourcengruppe](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Ressourcen mit aktiven Warnungen und Integritätsproblemen

Die Übersichtsseite zeigt, wie viele Warnungen ausgelöst wurden und noch aktiv sind, sowie den aktuellen Azure Resource Health-Status jeder Ressource. Zusammen können diese Informationen Ihnen helfen, schnell alle Ressourcen zu erkennen, die Probleme haben. Warnungen helfen Ihnen, Probleme in Ihrem Code und die Konfiguration Ihrer Infrastruktur zu erkennen. Azure Resource Health deckt Probleme mit der Azure-Plattform selbst auf, die nicht spezifisch für Ihre individuellen Anwendungen sind.

![Screenshot des Bereichs „Azure Resource Health“](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Aktivieren Sie zum Anzeigen von Azure Resource Health das Kontrollkästchen **Azure Resource Health anzeigen** über der Tabelle. Diese Spalte ist standardmäßig ausgeblendet, damit die Seite schnell geladen wird.

![Screenshot mit hinzugefügtem Resource Health-Diagramm](./media/resource-group-insights/0003-overview.png)

Standardmäßig sind die Ressourcen nach App-Ebene und Ressourcentyp gruppiert. **App-Ebene** ist eine einfache Kategorisierung von Ressourcentypen, die nur im Kontext der Seite mit der Übersicht über Einblicke zu Ressourcengruppen vorhanden ist. Es gibt Ressourcentypen in Bezug auf Anwendungscode, Compute-Infrastruktur, Netzwerk, Speicher und Datenbanken. Verwaltungstools erhalten eigene App-Ebenen, und jede andere Ressource wird als zur App-Ebene **Andere** gehörig kategorisiert. Diese Gruppierung kann Ihnen helfen, auf einen Blick zu erkennen, welche Subsysteme Ihrer Anwendung fehlerfrei und welche fehlerbehaftet sind.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostizieren von Problemen in Ihrer Ressourcengruppe

Die Seite mit den Einblicken in die Ressourcengruppe bietet mehrere Tools zur Diagnose von Problemen.

   |         |          |
   | ---------------- |:-----|
   | [**Warnungen**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Dient zum Anzeigen, Erstellen und Verwalten von Warnungen. |
   | [**Metriken**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Dient zum Visualisieren und Untersuchen Ihrer auf Metriken basierenden Daten.    |
   | [**Aktivitätsprotokolle**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Enthält Ereignisse auf Abonnementebene, die in Azure aufgetreten sind.  |
   | [**Anwendungszuordnung**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Navigieren Sie durch die Topologie Ihrer verteilten Anwendung, um Leistungsengpässe oder Fehlerquellen auszumachen. |

## <a name="failures-and-performance"></a>Fehler und Leistung

Was ist, wenn Sie feststellen, dass Ihre Anwendung langsam ausgeführt wird oder Benutzer Fehler gemeldet haben? Es ist zeitaufwendig, alle Ihre Ressourcen zu durchsuchen, um Probleme zu isolieren.

Die Registerkarten **Leistung** und **Fehler** vereinfachen diesen Prozess durch das Zusammenführen von Diagnoseansichten für Leistung und Fehler für zahlreiche gängige Ressourcentypen.

Bei den meisten Ressourcentypen wird ein Katalog mit Azure Monitor-Arbeitsmappenvorlagen geöffnet. Jede Arbeitsmappe, die Sie erstellen, kann angepasst, gespeichert, für Ihr Team freigegeben und künftig wiederverwendet werden, um ähnliche Probleme zu untersuchen.

### <a name="investigate-failures"></a>Untersuchen von Fehlern

Um die Registerkarte „Fehler“ zu testen, wählen Sie im linken Menü **Fehler** unter **Untersuchen** aus.

Die linke Menüleiste ändert sich nach der Auswahl und zeigt neue Optionen.

![Screenshot des Übersichtsbereichs „Fehler“](./media/resource-group-insights/00004-failures.png)

Wenn Sie App Service auswählen, wird Ihnen ein Katalog mit Azure Monitor-Arbeitsmappenvorlagen angezeigt.

![Screenshot des Katalogs mit Anwendungsarbeitsmappen](./media/resource-group-insights/0005-failure-insights-workbook.png)

Bei Auswählen der Vorlage „Failure Insights“ wird die Arbeitsmappe geöffnet.

![Screenshot eines Fehlerberichts](./media/resource-group-insights/0006-failure-visual.png)

Sie können eine beliebige Zeile auswählen. Die Auswahl wird dann in einer grafischen Detailansicht angezeigt.

![Screenshot von Fehlerdetails](./media/resource-group-insights/0007-failure-details.png)

Arbeitsmappen abstrahieren die schwierige Aufgabe, benutzerdefinierte Berichte und Visualisierungen in einem leicht verständlichen Format zu erstellen. Auch wenn einige Benutzer nur die vordefinierten Parameter anpassen möchten, sind Arbeitsmappen vollständig anpassbar.

Um einen Eindruck davon zu gewinnen, wie diese Arbeitsmappe intern funktioniert, klicken Sie auf der oberen Leiste auf **Bearbeiten**.

![Screenshot der zusätzlichen Bearbeitungsoption](./media/resource-group-insights/0008-failure-edit.png)

Neben den verschiedenen Elementen der Arbeitsmappe werden mehrere Felder des Typs **Bearbeiten** angezeigt. Wählen Sie das Feld **Bearbeiten** unterhalb der Tabelle der Vorgänge aus.

![Screenshot der Felder „Bearbeiten“](./media/resource-group-insights/0009-failure-edit-graph.png)

Dadurch wird die zugrunde liegende Protokollabfrage angezeigt, die die Visualisierung der Tabelle steuert.

 ![Screenshot des Fensters der Protokollabfrage](./media/resource-group-insights/0010-failure-edit-query.png)

Sie können die Abfrage direkt ändern. Oder Sie können sie als Referenz nutzen und darauf zurückgreifen, wenn Sie Ihre eigene benutzerdefinierte, parametrisierte Arbeitsmappe erstellen.

### <a name="investigate-performance"></a>Untersuchen der Leistung

Für die Leistung gibt es einen eigenen Katalog mit Arbeitsmappen. Für App Service bietet die vordefinierte Arbeitsmappe „Anwendungsleistung“ die folgende Ansicht:

 ![Screenshot der Leistungsansicht](./media/resource-group-insights/0011-performance.png)

In diesem Fall wird Ihnen bei Auswahl von „Bearbeiten“ angezeigt, dass diese Gruppe von Visualisierungen auf Azure Monitor-Metriken basiert.

 ![Screenshot der Leistungsansicht mit Azure-Metriken](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="enabling-access-to-alerts"></a>Aktivieren des Zugriffs auf Warnungen

Damit in Azure Monitor für Ressourcengruppen Warnungen angezeigt werden, muss ein Benutzer, der für dieses Abonnement über die Rolle „Besitzer“ oder „Mitwirkender“ verfügt, Azure Monitor für Ressourcengruppen für eine beliebige Ressourcengruppe des Abonnements öffnen. Dadurch können alle Benutzer, die über Lesezugriff verfügen, in Azure Monitor für Ressourcengruppen Warnungen für alle Ressourcengruppen des Abonnements anzeigen. Falls Sie über eine Rolle vom Typ „Besitzer“ oder „Mitwirkender“ verfügen, aktualisieren Sie diese Seite in einigen Minuten.

Azure Monitor für Ressourcengruppen nutzt das Warnungsverwaltungssystem von Azure Monitor, um den Warnungsstatus abzurufen. Die Warnungsverwaltung ist standardmäßig nicht für alle Ressourcengruppen und Abonnements konfiguriert und kann nur von einem Benutzer mit einer Rolle vom Typ „Besitzer“ oder „Mitwirkender“ aktiviert werden. Die Aktivierung kann auf zwei Arten durchgeführt werden:
* Öffnen von Azure Monitor für Ressourcengruppen für eine beliebige Ressourcengruppe des Abonnements
* Navigieren zum Abonnement, Klicken auf **Ressourcenanbieter** und Klicken auf die Option zum **Registrieren für „Alerts.Management**“

## <a name="next-steps"></a>Nächste Schritte

- [Azure Monitor-Arbeitsmappen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor-Warnungen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
