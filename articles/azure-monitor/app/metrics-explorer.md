---
title: Untersuchen von Metriken in Azure Application Insights | Microsoft Docs
description: Interpretieren von Metrik-Explorer-Diagrammen und Anpassen von Metrik-Explorer-Blättern
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5c659ca2f40d47450227d16963499a6b27c9e313
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60700707"
---
# <a name="exploring-metrics-in-application-insights"></a>Untersuchen von Metriken in Application Insights
Metriken in [Application Insights][start] sind gemessene Werte und Anzahlen von Ereignissen, die als Telemetriedaten aus Ihrer Anwendung gesendet werden. Sie dienen zum Erkennen von Leistungsproblemen und Überwachen von Trends bei der Nutzung Ihrer Anwendung. Es gibt eine Vielzahl von Standardmetriken, doch Sie können auch eigene benutzerdefinierte Metriken und Ereignisse erstellen.

> [!NOTE]
> Dieser Artikel beschreibt die klassische Metriken-Explorer-Benutzeroberfläche, die derzeit als veraltet markiert ist und schließlich entfernt wird. Sie sollten die [in diesem Artikel](../platform/metrics-charts.md) beschriebene neue Oberfläche kennenlernen.

Metriken und Ereignisanzahlen werden in Diagrammen aggregierter Werte z. B. als Summen, Mittelwerte oder Zählwerte angezeigt.

Hier sehen Sie einige Beispiele für Diagramme:

![](./media/metrics-explorer/01-overview.png)

Metrikdiagramme finden Sie überall im Application Insights-Portal. In den meisten Fällen lassen sich diese anpassen, und Sie können dem Blatt weitere Diagramme hinzufügen. Klicken Sie auf dem Blatt „Übersicht“, um ausführlichere Diagramme anzuzeigen (mit Titeln wie z.B. „Server“), oder klicken Sie auf **Metrik-Explorer**, um ein neues Blatt zu öffnen, auf dem Sie benutzerdefinierte Diagramme erstellen können.

## <a name="time-range"></a>Zeitbereich
Sie können den Zeitbereich der Diagramme oder Raster auf jedem Blatt ändern.

![Öffnen Sie das Blatt "Übersicht" Ihrer Anwendung im Azure-Portal](./media/metrics-explorer/03-range.png)

Klicken Sie auf "Aktualisieren", wenn Sie einige Daten erwarten, die noch nicht angezeigt wurden. Diagramme aktualisieren sich regelmäßig selbst, wobei die Intervalle für größere Zeiträume länger sind. Es kann eine Weile dauern, bis Daten durch die Analysepipeline in ein Diagramm gelangen.

Zum Vergrößern eines Teils eines Diagramms ziehen Sie die Maus darüber:

![Ziehen Sie die Maus über einen Teil eines Diagramms.](./media/metrics-explorer/12-drag.png)

Klicken Sie auf die Schaltfläche zum Aufheben der Vergrößerung, um es wiederherzustellen.

## <a name="granularity-and-point-values"></a>Granularität und Punktwerte
Bewegen Sie die Maus über dem Diagramm, um die Werte der Metriken zu diesem Zeitpunkt anzuzeigen.

![Bewegen Sie den Mauszeiger über einem Diagramm](./media/metrics-explorer/02-focus.png)

Der Wert der Eigenschaft zu einem bestimmten Zeitpunkt wird über das vorherige Abtastintervall aggregiert.

Das Abtastintervall, auch als „Granularität“ bezeichnet, wird oben auf dem Blatt angezeigt.

![Die Kopfzeile eines Blatts.](./media/metrics-explorer/11-grain.png)

Sie können die Granularität auf dem Blatt „Zeitbereich“ anpassen:

![Die Kopfzeile eines Blatts.](./media/metrics-explorer/grain.png)

Die verfügbaren Granularitäten hängen vom ausgewählten Zeitbereich ab. Die expliziten Granularitäten sind Alternativen für die „automatische“ Granularität für den Zeitbereich.


## <a name="editing-charts-and-grids"></a>Bearbeiten von Diagrammen und Rastern
So fügen Sie dem Blatt ein neues Diagramm hinzu:

![Wählen Sie im Metrik-Explorer "Diagramm hinzufügen"](./media/metrics-explorer/04-add.png)

Wählen Sie in einem vorhandenen oder neuen Diagramm die Option **Bearbeiten** , um dessen Anzeige zu bearbeiten:

![Wählen Sie eine oder mehrere Metriken aus](./media/metrics-explorer/08-select.png)

Sie können in einem Diagramm mehr als eine Metrik anzeigen, wenngleich es Einschränkungen bezüglich der Kombinationen gibt, die zusammen angezeigt werden können. Sobald Sie eine Metrik auswählen, werden einige der anderen deaktiviert.

Wenn Sie [benutzerdefinierte Metriken][track] in Ihrer App (Aufrufe von „TrackMetric“ und „TrackEvent“) programmiert haben, sind diese hier aufgelistet.

## <a name="segment-your-data"></a>Segmentieren der Daten
Sie können eine Metrik nach Eigenschaften aufteilen, um beispielsweise Seitenansichten auf Clients mit unterschiedlichen Betriebssystemen zu vergleichen.

Wählen Sie ein Diagramm oder Raster aus, aktivieren Sie das Gruppieren, und wählen Sie eine Eigenschaft, nach der gruppiert werden soll:

![Wählen Sie "Gruppierung EIN" und dann in "Gruppieren nach" eine Eigenschaft aus](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Wenn Sie die Gruppierung verwenden, zeigen die Diagrammtypen „Fläche“ und „Balken“ eine gestapelte Ansicht. Dies ist nützlich, wenn die Aggregationsmethode „Sum“ lautet. Wird als Aggregationstyp aber „Durchschnitt“ verwendet, wählen Sie die Anzeigetypen „Zeile“ oder „Raster“.
>
>

Wenn Sie [benutzerdefinierte Metriken][track] in Ihrer App programmiert haben und diese Eigenschaftswerte enthalten, können Sie die Eigenschaft in der Liste auswählen.

Ist das Diagramm für segmentierte Daten zu klein? Passen Sie die Höhe an:

![Passen Sie den Schieberegler an](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregationstypen
In der Legende auf der Seite wird standardmäßig der über den Zeitraum des Diagramms aggregierte Wert angezeigt. Wenn Sie auf das Diagramm zeigen, wird der an diesem Punkt befindliche Wert angezeigt.

Jeder Datenpunkt im Diagramm ist ein Aggregat der Datenwerte, die im vorherigen Samplingintervall (Granularität) empfangen wurden. Die Granularität wird oben auf dem Blatt angezeigt und variiert abhängig von der gesamten Zeitskala des Diagramms.

Metriken können auf unterschiedliche Weisen zusammengefasst werden:

* **Anzahl** ist die Anzahl der im Abtastintervall empfangenen Ereignisse. Sie wird für Ereignisse wie beispielsweise Anforderungen verwendet. Abweichungen bei der die Höhe des Diagramms geben Abweichungen bei der Rate an, mit der die Ereignisse auftreten. Beachten Sie jedoch, dass sich der numerische Wert ändert, wenn Sie das Abtastintervall ändern.
* **Summe** addiert die Werte aller Datenpunkte, die über das Samplingintervall oder den Zeitraum des Diagramms empfangen werden.
* **Durchschnitt** teilt die Summe durch die Anzahl der Datenpunkte, die während des Intervalls empfangen werden.
* **Eindeutige** Werte werden verwendet, um Benutzer und Konten zu zählen. Die Abbildung zeigt die Anzahl der verschiedenen Benutzer, die während des Samplingintervalls oder über den Zeitraum des Diagramms gezählt werden.
* **%** – Prozentsatzversionen der Aggregationen werden nur bei segmentierten Diagrammen verwendet. Die Summe ergibt immer 100%, und das Diagramm zeigt den relativen Anteil verschiedener Komponenten an einer Summe.

    ![Prozentwertaggregation](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Ändern des Aggregationstyps

![Bearbeiten Sie das Diagramm, und wählen Sie dann „Aggregation“ aus.](./media/metrics-explorer/05-aggregation.png)

Die Standardmethode für die einzelnen Metriken wird bei der Erstellung eines neuen Diagramms bzw. bei Deaktivierung der gesamten Metrikauswahl angezeigt:

![Heben Sie die Auswahl aller Metriken auf, um die Standardwerte anzuzeigen](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Anheften der Y-Achse 
Standardmäßig zeigt ein Diagramm Y-Werte von Null bis zu den Maximalwerten des Datenbereichs an, um das Quantum der Werte optisch aufzubereiten. In manchen Fällen kann es jedoch hilfreich sein, anstelle des Quantums geringfügige Wertänderungen in einer optischen Darstellung zu untersuchen. Für solche Anpassungen können Sie das Bereichsbearbeitungsfeature für die Y-Achse verwenden, um den minimalen oder maximalen Wert der Y-Achse am gewünschten Ort anzuheften.
Klicken Sie auf das Kontrollkästchen „Erweiterte Einstellungen“ , um die Bereichseinstellungen für die Y-Achse anzuzeigen.

![Klicken Sie auf „Erweiterte Einstellungen“, wählen Sie „Benutzerdefinierter Bereich“ aus, und geben Sie den minimalen/maximalen Wert an.](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtern der Daten
So zeigen Sie nur die Metriken für eine ausgewählte Gruppe von Eigenschaftswerten an:

![Klicken Sie auf "Filter", erweitern Sie eine Eigenschaft, und überprüfen Sie einige Werte](./media/metrics-explorer/19-filter.png)

Wenn Sie für eine bestimmte Eigenschaft keine Werte auswählen, entspricht dies der Auswahl aller Werte ohne Angabe von Filtern.

Beachten Sie die Anzahlen von Ereignissen neben jedem Eigenschaftswert. Wenn Sie Werte einer Eigenschaft auswählen, werden neben anderen Eigenschaftswerten die Anzahlen angepasst.

Filter gelten für alle Diagramme eines Blatts. Falls unterschiedliche Filter auf unterschiedliche Diagramme angewendet werden sollen, müssen Sie unterschiedliche Blätter mit Metriken erstellen und speichern. Sie können bei Bedarf auch Diagramme von unterschiedlichen Blättern auf dem Dashboard anheften, damit sie nebeneinander angezeigt werden.

### <a name="remove-bot-and-web-test-traffic"></a>Entfernen von Robot- und Webtest-Datenverkehr
Verwenden Sie den Filter **Realer oder synthetischer Datenverkehr**, und aktivieren Sie **Real**.

Sie können auch nach **Quelle von synthetischem Datenverkehr**filtern.

### <a name="to-add-properties-to-the-filter-list"></a>So fügen Sie der Filterliste Eigenschaften hinzu
Möchten Sie Telemetrie nach einer Kategorie Ihrer Wahl filtern? Möglicherweise unterteilen Sie Ihre Benutzer in verschiedene Kategorien und möchten Ihre Daten nach diesen Kategorien segmentieren.

[Erstellen Sie eine eigene Eigenschaft](../../azure-monitor/app/api-custom-events-metrics.md#properties). Legen Sie sie in einem [Telemetrieinitialisierer](../../azure-monitor/app/api-custom-events-metrics.md#defaults) fest, damit sie in allen Telemetrieformen angezeigt wird, einschließlich der von unterschiedlichen SDK-Modulen gesendeten Standardtelemetrie.

## <a name="edit-the-chart-type"></a>Bearbeiten des Diagrammtyps
Beachten Sie, dass Sie zwischen Rastern und Diagrammen wechseln können:

![Wählen Sie ein Raster oder Diagramm und anschließend einen Diagrammtyp](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Speichern des Blatts "Metriken"
Wenn Sie einige Diagramme erstellt haben, speichern Sie sie als Favoriten. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Wählen Sie "Favoriten"](./media/metrics-explorer/21-favorite-save.png)

Um das Blatt erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Wählen Sie "Favoriten" auf dem Blatt "Übersicht" aus](./media/metrics-explorer/22-favorite-get.png)

Wenn Sie beim Speichern den Zeitbereich "Relativ" ausgewählt haben, wird das Fenster mit den neuesten Metriken aktualisiert. Wenn Sie den Zeitraum "Absolut" gewählt haben, werden jedes Mal dieselben Daten gezeigt.

## <a name="reset-the-blade"></a>Zurücksetzen des Blatts
Wenn Sie ein Blatt bearbeiten, aber dann lieber wieder mit den ursprünglich gespeicherten Daten weiterarbeiten möchten, klicken Sie auf "Zurücksetzen".

![In den Schaltflächen am oberen Rand des Metrik-Explorers](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Öffnen Sie für eine unmittelbarere Ansicht Ihrer Telemetriedaten [Live Stream](live-stream.md). Die meisten Metriken werden aufgrund der Aggregation erst nach einigen Minuten angezeigt. Im Gegensatz dazu sind Livemetriken für geringe Latenz optimiert. 

## <a name="set-alerts"></a>Festlegen von Benachrichtigungen
Fügen Sie eine Benachrichtigung hinzu, wenn Sie per E-Mail über ungewöhnliche Werte einer beliebigen Metrik informiert werden möchten. Sie können auswählen, ob die E-Mail an die Kontoadministratoren oder an bestimmte E-Mail-Adressen gesendet wird.

![Wählen Sie im Metrik-Explorer "Warnungsregeln", "Warnung hinzufügen"](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Weitere Informationen zu Warnungen][alerts]


## <a name="continuous-export"></a>Fortlaufender Export
Wenn Sie Daten zur externen Verarbeitung fortlaufend exportieren möchten, sollten Sie die Funktion [Fortlaufender Export](../../azure-monitor/app/export-telemetry.md)verwenden.

### <a name="power-bi"></a>Power BI
Wenn Sie umfangreichere Ansichten der Daten wünschen, können Sie sie [nach Power BI exportieren](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analytics
[Analytics](../../azure-monitor/app/analytics.md) bietet eine vielseitigere Möglichkeit zum Analysieren Ihrer Telemetriedaten mit einer leistungsfähigen Abfragesprache. Verwenden Sie sie, wenn Sie Ergebnisse von Metriken kombinieren oder berechnen oder eine eingehende Untersuchung der kürzlich erreichten Leistung Ihrer App durchführen möchten. 

In einem Metrikdiagramm können Sie auf das Symbol „Analyse“ klicken, um direkt die entsprechende Analytics-Abfrage aufzurufen.

## <a name="troubleshooting"></a>Problembehandlung
*In meinem Diagramm werden keine Daten angezeigt.*

* Filter gelten für alle Diagramme im Blatt. Stellen Sie sicher, dass Sie bei der Arbeit mit einem Diagramm keinen Filter festgelegt haben, mit dem alle Daten eines anderen Diagramms ausgeschlossen werden.

    Wenn Sie verschiedene Filter für verschiedene Diagramme festlegen möchten, sollten Sie diese in unterschiedlichen Blättern erstellen und als separate Favoriten speichern. Sie können sie auch im Dashboard anheften, damit sie nebeneinander angezeigt werden.
* Wenn Sie ein Diagramm nach einer Eigenschaft gruppieren, die nicht in der Metrik definiert ist, sind im Diagramm keine Daten enthalten. Versuchen Sie, die Einstellungen für „Gruppieren nach“ zu löschen, oder wählen Sie eine andere Gruppierungseigenschaft aus.
* Leistungsdaten (CPU, E/A-Rate usw.) sind für Java-Webdienste, Windows-Desktop-Apps, [IIS-Web-Apps und -Dienste bei Installation des Statusmonitors](../../azure-monitor/app/monitor-performance-live-website-now.md) und [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md) verfügbar. Für Azure-Websites sind sie dagegen nicht verfügbar.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen der Verwendung mit Application Insights](../../azure-monitor/app/usage-overview.md)
* [Verwenden der Diagnosesuche](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
