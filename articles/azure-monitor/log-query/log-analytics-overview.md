---
title: Übersicht über Log Analytics in Azure Monitor
description: Dieser Artikel beschreibt Log Analytics, ein Tool im Azure-Portal, mit dem Protokollabfragen zum Analysieren von Daten in Azure Monitor-Protokollen bearbeitet und ausgeführt werden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: ba27739ff9e9d992ffe6202629a1db19604b1409
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186115"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Übersicht über Log Analytics in Azure Monitor
Log Analytics ist ein Tool im Azure-Portal, mit dem Protokollabfragen für Daten in Azure Monitor-Protokollen bearbeitet und ausgeführt werden. Sie können eine einfache Abfrage schreiben, die eine Reihe von Datensätzen zurückgibt, und dann Funktionen von Log Analytics zum Sortieren, Filtern und Analysieren verwenden. Sie können auch eine erweiterte Abfrage schreiben, um eine statistische Analyse durchzuführen und die Ergebnisse in einem Diagramm zu visualisieren, und auf diese Weise einen bestimmten Trend erkennen. Unabhängig davon, ob Sie die Ergebnisse Ihrer Abfragen interaktiv verwenden oder mit anderen Azure Monitor-Funktionen wie Protokollabfragewarnungen oder Arbeitsmappen nutzen, ist Log Analytics das Tool, das Sie zum Schreiben und Testen der Abfragen verwenden. 


> [!TIP]
> Dieser Artikel enthält eine Beschreibung von Log Analytics und den einzelnen Funktionen. Wenn Sie direkt mit einem Tutorial fortfahren möchten, wechseln Sie zum [Log Analytics-Tutorial](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Starten von Log Analytics
Starten Sie Log Analytics im Azure-Portal im **Azure Monitor**-Menü über **Protokolle**. Diese Option wird auch im Menü für die meisten Azure-Ressourcen angezeigt. Unabhängig davon, von welcher Stelle aus Sie Log Analytics starten, ist es immer das gleiche Tool. Das Menü, das Sie zum Starten von Log Analytics verwenden, bestimmt jedoch die jeweils verfügbaren Daten. Wenn Sie das Tool über das **Azure Monitor**-Menü oder das Menü **Log Analytics-Arbeitsbereiche** starten, haben Sie Zugriff auf alle Datensätze in einem Arbeitsbereich. Wenn Sie **Protokolle** über einen anderen Ressourcentyp auswählen, sind die Daten auf die Protokolldaten für diese Ressource beschränkt. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](scope.md).

[![Starten von Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Wenn Sie Log Analytics starten, wird als Erstes ein Dialogfeld mit [Beispielabfragen](example-queries.md) angezeigt. Diese sind nach Lösung kategorisiert, und Sie können nach Abfragen suchen, die Ihren besonderen Anforderungen entsprechen. Vielleicht finden Sie eine Abfrage, die genau Ihren Anforderungen entspricht, oder Sie laden eine Abfrage in den Editor und ändern sie nach Bedarf. Das Durchsuchen von Beispielabfragen bietet eine gute Möglichkeit zu lernen, wie Sie eigene Abfragen schreiben. Wenn Sie jedoch mit einem leeren Skript beginnen und die Abfrage selbst schreiben möchten, können Sie die Beispielabfragen schließen. Klicken Sie einfach am oberen Bildschirmrand auf **Abfragen**, wenn Sie diese wieder anzeigen möchten.

## <a name="log-analytics-interface"></a>Log Analytics-Benutzeroberfläche
Die folgende Abbildung zeigt die verschiedenen Komponenten von Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. Obere Aktionsleiste
Steuerelemente zum Arbeiten mit der Abfrage im Abfragefenster.

| Option | BESCHREIBUNG |
|:---|:---|
| `Scope` | Gibt den für die Abfrage verwendeten Bereich von Daten an. Dabei kann es sich um alle Daten in einem Log Analytics-Arbeitsbereich oder um Daten für eine bestimmte Ressource in mehreren Arbeitsbereichen handeln. Informationen finden Sie unter [Abfragebereich](scope.md). |
| Schaltfläche „Ausführen“ | Klicken Sie auf diese Schaltfläche, um die ausgewählte Abfrage im Abfragefenster auszuführen. Sie können auch UMSCHALT+EINGABETASTE drücken, um eine Abfrage auszuführen. |
| Zeitauswahl | Wählen Sie den Zeitraum für die Daten aus, die für die Abfrage zur Verfügung stehen. Dieser wird überschrieben, wenn Sie einen Zeitfilter in die Abfrage einschließen. Weitere Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](scope.md). |
| Schaltfläche „Speichern“ | Speichern Sie die Abfrage im Abfrage-Explorer für den Arbeitsbereich. |
 Schaltfläche „Kopieren“ | Kopieren Sie einen Link für die Abfrage, den Abfragetext oder die Abfrageergebnisse in die Zwischenablage. |
| Schaltfläche „Neue Warnungsregel“ | Erstellen Sie eine neue Registerkarte mit einer leeren Abfrage. |
| Schaltfläche "Exportieren" | Exportieren Sie die Ergebnisse der Abfrage in eine CSV-Datei oder die Abfrage zur Verwendung mit Power BI in das Power Query Formula Language-Format. |
| Schaltfläche „An Dashboard anheften“ | Fügen Sie die Ergebnisse der Abfrage einem Azure-Dashboard hinzu. |
| Schaltfläche „Abfrage formatieren“ | Ordnen Sie den ausgewählten Text zur besseren Lesbarkeit an. |
| Schaltfläche „Beispielabfragen“ | Öffnen Sie das Dialogfeld mit Beispielabfragen, das beim ersten Öffnen von Log Analytics angezeigt wird. |
| Schaltfläche „Abfrage-Explorer“ | Öffnen Sie **Abfrage-Explorer**, der Zugriff auf gespeicherte Abfragen im Arbeitsbereich bietet. |


### <a name="2-sidebar"></a>2. Seitenleiste
Listen der Tabellen im Arbeitsbereich, Beispielabfragen und Filteroptionen für die aktuelle Abfrage.

| Registerkarte | BESCHREIBUNG |
|:---|:---|
| Tabellen | Listet die Tabellen auf, die Teil des ausgewählten Bereichs sind. Wählen Sie **Gruppieren nach** aus, um die Gruppierung der Tabellen zu ändern. Zeigen Sie auf einen Tabellennamen, um ein Dialogfeld mit einer Beschreibung der Tabelle und Optionen zum Anzeigen der zugehörigen Dokumentation sowie eine Vorschau der Daten anzuzeigen. Erweitern Sie eine Tabelle, um die zugehörigen Spalten anzuzeigen. Doppelklicken Sie auf einen Tabellen- oder Spaltennamen, um ihn der Abfrage hinzuzufügen. |
| Abfragen | Liste der Beispielabfragen, die Sie im Abfragefenster öffnen können. Dies ist die gleiche Liste, die angezeigt wird, wenn Sie Log Analytics öffnen. Wählen Sie **Gruppieren nach** aus, um die Gruppierung der Abfragen zu ändern. Doppelklicken Sie auf eine Abfrage, um sie dem Abfragefenster hinzuzufügen, oder zeigen Sie darauf, um weitere Optionen anzuzeigen. |
| Filtern | Erstellt Filteroptionen basierend auf den Ergebnissen einer Abfrage. Nachdem Sie eine Abfrage ausgeführt haben, werden Spalten mit unterschiedlichen Werten aus den Ergebnissen angezeigt. Wählen Sie einen oder mehrere Werte aus, und klicken Sie dann auf **Anwenden und ausführen**, um der Abfrage einen **Where**-Befehl hinzuzufügen und sie erneut auszuführen. |

### <a name="3-query-window"></a>3. Abfragefenster
Das Abfragefenster dient zum Bearbeiten der Abfrage. Dies umfasst IntelliSense für KQL-Befehle und Farbcodierung, um die Lesbarkeit zu verbessern. Klicken Sie oben im Fenster auf das **+** , um eine weitere Registerkarte zu öffnen.

Ein einzelnes Fenster kann mehrere Abfragen enthalten. Da eine Abfrage keine leeren Zeilen enthalten darf, können Sie mehrere Abfragen in einem Fenster mithilfe einer oder mehrerer leerer Zeilen voneinander trennen. Die aktuelle Abfrage ist die, in der sich der Cursor an einer beliebigen Stelle befindet.

Zum Ausführen der aktuellen Abfrage klicken Sie auf die Schaltfläche **Ausführen**, oder drücken Sie UMSCHALT+EINGABETASTE.

### <a name="4-results-window"></a>4. Ergebnisfenster
Die Abfrageergebnisse werden im Ergebnisfenster angezeigt. Standardmäßig werden die Ergebnisse in Tabellenform dargestellt. Zur Anzeige als Diagramm wählen Sie entweder **Diagramm** im Ergebnisfenster aus, oder fügen Sie der Abfrage einen **Render**-Befehl hinzu.

#### <a name="results-view"></a>Ergebnisse (Ansicht)
Zeigt Abfrageergebnisse in einer Tabelle nach Spalten und Zeilen geordnet an. Klicken Sie links neben einer Zeile, um deren Werte zu erweitern. Klicken Sie auf das Dropdownmenü **Spalten**, um die Liste der Spalten zu ändern. Sortieren Sie die Ergebnisse, indem Sie auf einen Spaltennamen klicken. Filtern Sie die Ergebnisse, indem Sie auf den Trichter neben einem Spaltennamen klicken. Löschen Sie die Filter, und setzen Sie die Sortierung zurück, indem Sie die Abfrage erneut ausführen.

Wählen Sie **Spalten gruppieren** aus, um die Gruppierungsleiste über den Abfrageergebnissen anzuzeigen. Gruppieren Sie die Ergebnisse nach einer beliebigen Spalte, indem Sie sie auf die Leiste ziehen. Erstellen Sie verschachtelte Gruppen in den Ergebnissen, indem Sie zusätzliche Spalten hinzufügen. 

#### <a name="chart-view"></a>Diagrammansicht
Zeigt die Ergebnisse als einen von mehreren verfügbaren Diagrammtypen an. Sie können den Diagrammtyp in einem **Render**-Befehl in der Abfrage angeben oder ihn aus dem Dropdownmenü **Visualisierungstyp** auswählen.

| Option | BESCHREIBUNG |
|:---|:---|
| **Visualisierungstyp** | Der Typ des anzuzeigenden Diagramms. |
| **X-Achse** | Die Spalte in den Ergebnissen, die als X-Achse verwendet werden soll. 
| **Y-Achse** | Die Spalte in den Ergebnissen, die als Y-Achse verwendet werden soll. Dies ist normalerweise eine numerische Spalte. |
| **Aufteilen nach** | Die Spalte in den Ergebnissen, die die Reihen im Diagramm definiert. Eine Reihe wird für jeden Wert in der Spalte erstellt. |
| **Aggregation** | Der Aggregationstyp, der für die numerischen Werte auf der Y-Achse durchgeführt werden soll. |

## <a name="relationship-to-azure-data-explorer"></a>Beziehung zu Azure Data Explorer
Wenn Sie bereits mit der Azure Data Explorer-Webbenutzeroberfläche vertraut sind, dann sollte Ihnen Log Analytics bekannt vorkommen. Der Grund dafür ist, dass Log Analytics auf Azure Data Explorer basiert und die gleiche Kusto-Abfragesprache (KQL) verwendet. Log Analytics fügt Azure Monitor-spezifische Funktionen hinzu, z. B. das Filtern nach Zeitbereich und die Möglichkeit, eine Warnungsregel aus einer Abfrage zu erstellen. Beide Tools enthalten einen Explorer, mit dem Sie die Struktur von verfügbaren Tabellen durchsuchen können, doch verwendet die Azure Data Explorer-Webbenutzeroberfläche hauptsächlich Tabellen in Azure Data Explorer-Datenbanken, während Log Analytics mit Tabellen in einem Log Analytics-Arbeitsbereich arbeitet. 

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen erhalten Sie im [Tutorial zum Verwenden von Log Analytics im Azure-Portal](./log-analytics-tutorial.md).
- Weitere Informationen erhalten Sie im [Tutorial zum Schreiben von Abfragen](get-started-queries.md).