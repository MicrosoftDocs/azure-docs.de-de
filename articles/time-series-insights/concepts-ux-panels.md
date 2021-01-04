---
title: 'Visualisieren von Daten im Time Series Insights-Explorer: Azure Time Series Insights Gen2 | Microsoft-Dokumentation'
description: Hier erfahren Sie mehr über die im Azure Time Series Insights-Explorer verfügbaren Features und Optionen.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: edfbbdd1b30cba74cf4f63d6b5e84c2e616e1a0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020979"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights-Explorer

In diesem Artikel werden die in der Azure Time Series Insights Gen2-[Demoumgebung](https://insights.timeseries.azure.com/preview/demo) verfügbaren verschiedenen Features und Optionen beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte mit dem Azure Time Series Insights-Explorer ist Folgendes erforderlich:

* Bereitstellung einer Azure Time Series Insights Gen2-Umgebung. Weitere Informationen zur Bereitstellung einer Instanz erhalten Sie im Tutorial [Azure Time Series Insights Gen2](./tutorials-set-up-tsi-environment.md).
* [Bereitstellung von Datenzugriff](./concepts-access-policies.md) auf die Azure Time Series Insights Gen2-Umgebung, die Sie für das Konto erstellt haben. Außer Ihnen, können Sie auch anderen Zugriff gewähren.
* Hinzufügen einer Ereignisquelle zur Azure Time Series Insights Gen2-Umgebung zum Übertragen von Daten an die Umgebung mithilfe von Push:
  * Informationen zum [Herstellen einer Verbindung mit einem Event Hub](./how-to-ingest-data-event-hub.md)
  * Informationen zum [Herstellen einer Verbindung mit einem IoT-Hub](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Erkunden des Azure Time Series Insights-Explorers

Der Azure Time Series Insights-Explorer besteht aus den folgenden sieben Elementen:

[![Übersicht zum Azure Time Series Insights-Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Umgebungsbereich](#1-environment-panel): Hier werden Ihre Azure Time Series Insights Gen2-Umgebungen angezeigt.
1. [Navigationsleiste](#2-navigation-bar): Ermöglicht ihnen das Wechseln zwischen den Seiten **Analysieren** und **Modell**.
1. [Hierarchiestruktur und Suchbereich](#3-hierarchy-tree-and-search-panel): Ermöglicht Ihnen die Auswahl und Suche bestimmter Datenelemente für die Diagrammdarstellung.
1. [Zeitreihenquelle](#4-time-series-well): Zeigt alle Ihre aktuell ausgewählten Datenelemente an.
1. [Diagrammbereich](#5-chart-panel): Zeigt Ihr aktuelles Arbeitsdiagramm an.
1. [Zeitachse](#6-time-editor-panel): Ermöglicht Ihnen die Änderung Ihres Arbeitszeitraums.
1. [App-Leiste](#7-app-bar): Enthält Ihre Benutzerverwaltungsoptionen (z. B. den aktuellen Mandanten) und gestattet Ihnen die Änderung von Design- und Spracheinstellungen.

## <a name="1-environment-panel"></a>1. Umgebungsbereich

Im Umgebungsbereich werden alle Azure Time Series Insights Gen2-Umgebungen angezeigt, auf die Sie Zugriff haben. Die Liste umfasst Gen2- und Gen1-Umgebungen. Wählen Sie einfach die Umgebung aus, die Sie verwenden möchten, um sofort dorthin zu navigieren.

1. Wählen Sie den Dropdownpfeil neben der angezeigten Umgebung aus.

   [![Umgebungsbereich](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Wählen Sie anschließend die gewünschte Umgebung aus.

## <a name="2-navigation-bar"></a>2. Navigationsleiste

  [![ Die Navigationsleiste](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

In der Navigationsleiste können Sie zwischen zwei Ansichten auswählen:

* **Analysieren**: Ermöglicht umfangreiche Analysen für Ihre modellierten oder nicht modellierten Zeitreihendaten sowie deren Darstellung als Diagramme.
* **Modell**: Diese Option ermöglicht das Übertragen neuer Azure Time Series Insights Gen2-Typen, -Hierarchien und -Instanzen an Ihr Zeitreihenmodell mithilfe von Push.

### <a name="model-authoring"></a>Modellerstellung

Azure Time Series Insights Gen2 unterstützt vollständige CRUD-Vorgänge („Create“, „Read“, „Update“, „Delete“; „Erstellen“, „Lesen“, „Aktualisieren“, „Löschen“) für Ihr Zeitreihenmodell.

[![Der Modellsuchbereich](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Zeitreihenmodelltyp**: Mithilfe von Zeitreihenmodelltypen können Sie Variablen oder Formeln für Berechnungen definieren. Sie werden mit einer bestimmten Zeitreihenmodellinstanz verknüpft. Ein Typ kann eine oder mehrere Variablen enthalten.
* **Zeitreihenmodellhierarchie**: Hierarchien sind systematische Organisationen Ihrer Daten. Hierarchien stellen die Beziehungen zwischen verschiedenen Instanzen in Ihrem Zeitreihenmodell dar.
* **Zeitreihenmodellinstanz**: Instanzen sind die Zeitreihen selbst. In den meisten Fällen handelt es sich dabei um die Geräte-ID (**DeviceID**) oder die Objekt-ID (**AssetID**) – also um den eindeutigen Bezeichner des Objekts in der Umgebung.

Weitere Informationen zum Zeitreihenmodell finden Sie unter [Zeitreihenmodelle](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Hierarchiestruktur und Suchbereich

Mit der HIerarchiestruktur und dem Suchbereich können Sie ganz einfach in Ihrer [Zeitreihenmodell](./concepts-model-overview.md)-Hierarchie suchen und navigieren, um die spezifischen Zeitreiheninstanzen zu finden, die Sie in Ihrem Diagramm anzeigen möchten. Wenn Sie Ihre Instanzen auswählen, werden sie nicht nur dem aktuellen Diagramm, sondern auch den Daten hinzugefügt.

[![Hierarchiestruktur und Suchbereich](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Der Suchergebnissebereich erlaubt Ihnen außerdem, Ihre Ergebnisse in einer Hierarchie- oder Listenansicht anzuzeigen, die das Auffinden der anzuzeigenden Instanzen erleichtern.

## <a name="4-time-series-well"></a>4. Zeitreihenquelle

In der Quelle werden Instanzfelder und andere Metadaten angezeigt, die ausgewählten Zeitreihenmodellinstanzen zugeordnet sind. Mithilfe der Kontrollkästchen auf der rechten Seite können Sie bestimmte Instanzen im aktuellen Diagramm ausblenden oder anzeigen.

  [![Gen2-Quelle](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Sie können bestimmte Datenelemente aus Ihrer aktuellen Datenquelle entfernen, indem Sie auf der linken Seite des Elements auf das rote Steuerelement **Löschen** (Papierkorb) klicken. Über die Quelle können Sie außerdem steuern, wie jedes Element in dem Diagramm angezeigt wird. Sie können auswählen, Mindest-/Höchstwert für Schatten und Datenpunkte hinzufügen, das Element zeitlich zu verschieben und die Instanz auf abgestufte Weise zu visualisieren.

Darüber hinaus können Sie mithilfe des Steuerelements für Erkundungen problemlos Zeitverschiebungen und Punktdiagramme erstellen.  

  [![Quellenlayoutoptionen](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Sollte die folgende Meldung angezeigt werden, liegen im ausgewählten Zeitraum keine Daten für die Instanz vor. Verwenden Sie zur Behebung des Problems einen größeren Zeitraum, oder vergewissern Sie sich, dass von der Instanz Daten gepusht werden.
>
> ![Keine Datenbenachrichtigung](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Diagrammbereich

Mit dem Diagramm können Sie Zeitreiheninstanzen als Linien anzeigen. Sie können den Umgebungsbereich, das Datenmodell und den Zeitraumsteuerelement-Bereich reduzieren, indem Sie auf die Websteuerelemente klicken, um das Diagramm zu vergrößern.

  [![Gen2-Diagrammübersicht](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagrammtyp**: Steuert, welche Datenelemente zur Visualisierung verfügbar sind.

1. **Intervallgröße**: Mit dem Schieberegler für die Intervallgröße können Sie die Intervalle in einer Zeitspanne vergrößern und verkleinern. Dies ermöglicht eine genauere Steuerung der Bewegung zwischen größeren Zeitabschnitten, die Trends gleichmäßig bis hin zu kleinsten Zeitabschnitten in Millisekunden anzeigen, sodass Sie präzise, hochauflösende Schnitte Ihrer Daten überprüfen können. Der Standardstartpunkt des Schiebereglers ist als optimierte Ansicht der Daten aus Ihrer Auswahl festgelegt und gleicht somit die Auflösung, Abfragegeschwindigkeit und Granularität aus.

1. **Zoomen und Schwenken**: Wählen Sie dieses Steuerelement aus, um das Diagramm zu zoomen und zu schwenken.

1. **Y-Achsen-Steuerelement**: Durchläuft die verfügbaren Optionen für die Y-Achse:

    * `Stacked`: Jede Zeile hat eine eigene Y-Achse.
    * `Overlap`: Ermöglicht das Stapeln mehrerer Zeilen auf derselben Y-Achse, wobei sich die Y-Achsendaten je nach ausgewählter Zeile ändern.
    * `Shared`: Anzeige aller Y-Achsendaten gemeinsam.

1. **Marker-Element**: Das aktuell ausgewählte Datenelement und seine zugehörigen Details.

Sie können ein Drilldown in einen bestimmten Datenschnitt ausführen, indem Sie auf einen Datenpunkt im aktuellen Diagramm **klicken**, die Maustaste gedrückt halten und dann den ausgewählten Bereich zum Endpunkt Ihrer Wahl ziehen. **Klicken Sie mit der rechten Maustaste** auf den blauen, ausgewählten Bereich, und wählen Sie dann (wie unten dargestellt) **Zoom** aus. Sie können die Telemetrieereignisse auch in der ausgewählten Zeitspanne anzeigen und herunterladen.

  [![Gen2-Diagrammvergrößerung](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Nach dem **Zoomen** wird Ihr ausgewähltes Dataset angezeigt. Wählen Sie das Steuerelement für das Format aus, um zwischen den drei Y-Achsendarstellungen Ihrer Daten zu wechseln.

  [![Y-Achse des Gen2-Diagramms](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Hier sehen Sie ein Beispiel für ein **überlappendes Diagramm**:

  [![Option „Überlappendes Diagramm“](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Die Schaltfläche **Weitere Aktionen** wird erweitert, um die Optionen **Als CSV-Datei herunterladen**, **Mit Power BI verbinden**, **Diagrammdaten als Tabelle anzeigen** und **Rohereignisse untersuchen** anzuzeigen.

  [![Option „Weitere Aktionen“](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Weitere Informationen zur Option **Mit Power BI verbinden** finden Sie unter [Verbinden von Azure Time Series Insights Gen2 mit Power BI](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Editor-Bereich für Zeiten

Wenn Sie mit Azure Time Series Insights Gen2 arbeiten, wählen Sie zunächst einen Zeitraum aus. Der ausgewählte Zeitraum steuert das Dataset, das für die Manipulation durch die Azure Time Series Insights Gen2-Updatewidgets verfügbar ist.

  [![Zeitauswahlbereich](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Ein Teil der Zeitachse wird in Bernstein oder Orange hervorgehoben, um die Datenspanne anzugeben, die im warmen Speicher verfügbar ist.

In Azure Time Series Insights Gen2 stehen die folgenden Websteuerelemente zum Auswählen Ihres Arbeitszeitraums zur Verfügung.

  [![Steuerelement für Erkundungsquelle](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Schieberegler für den inneren Datumsbereich**: Ziehen Sie die beiden Endpunktsteuerelemente über den gewünschten Zeitraum. Dieser innere Datumsbereich wird durch den Schieberegler für den äußeren Datumsbereich begrenzt.

1. **Schaltflächen zum Erhöhen/Verringern des Datumsbereichs**: Sie können Ihren Zeitraum erhöhen oder verringern, indem Sie eine der Schaltflächen für das gewünschte Intervall auswählen.

1. **Zeitraum-Reduzierungssteuerelement**: Mit diesem Websteuerelement können Sie alle Steuerelemente ausblenden (mit Ausnahme des Schiebereglers für den inneren Datumsbereich).

1. **Schieberegler für den äußeren Datumsbereich**: Verwenden Sie die Endpunktsteuerelemente, um den äußeren Datumsbereich auszuwählen. Dieser steht dann für das Steuerelement für den inneren Datumsbereich zur Verfügung.

1. **Schieberegler für den Zeitbereich**: Hiermit können Sie schnell zwischen voreingestellten Zeitbereichen wechseln. Beispiele wären etwa die letzten **30 Minuten**, die letzten **12 Stunden** oder ein **benutzerdefinierter Bereich**. Durch das Ändern dieses Werts werden auch die verfügbaren Intervallbereiche geändert, die im Intervallgrößen-Schiebereglertool besprochen werden.

   [![Auswahlbereich „In“ und „Aus“](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. App-Leiste

Der Azure Time Series Insights Gen2-Navigationsbereich wird oben in Ihrer App angezeigt. Er bietet folgende Funktionen:

### <a name="current-session-share-link-control"></a>Steuerelement für Freigabelink der aktuellen Sitzung

  [![Symbol „Freigeben“](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Wählen Sie das neue Symbol **Freigeben** aus, um einen URL-Link für Ihr Team freizugeben.

  [![Freigeben Ihrer Instanz-URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Mandantenabschnitt

  [![Mandantenauswahl](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Ihre aktuellen Azure Time Series Insights Gen2-Anmeldekontoinformationen werden angezeigt.
* Diese Option ermöglicht das Wechseln zwischen den verfügbaren Designs.
* Der Abschnitt wird zum Anzeigen der [Gen2-Demoumgebung](https://insights.timeseries.azure.com/preview/demo) verwendet.

### <a name="theme-selection"></a>Designauswahl

Wenn Sie ein neues Design auswählen möchten, wählen Sie rechts oben Ihr Profilsymbol aus. Wählen Sie dann **Design ändern** aus.

  [![Designauswahl](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Die Sprachauswahl ist auch verfügbar, wenn Sie Ihr Profilsymbol auswählen.

Der Azure Time Series Insights-Explorer unterstützt zwei Designs:

* **Helles Design**: Das Standarddesign, das im gesamten vorliegenden Dokument gezeigt wird.
* **Dunkles Design**: Bei diesem Design wird der Explorer wie folgt angezeigt:

  [![„Dunkles Design“ ausgewählt](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1-Umgebungssteuerelemente

### <a name="gen2-terms-panel"></a>Gen2-Begriffsbereich

Dieser Abschnitt gilt nur für vorhandene Gen1-Umgebungen, die versuchen, den Explorer mit der aktualisierten Benutzeroberfläche zu verwenden. Sie sollten die Gen1- und das Gen1-Produkte auch in Kombination verwenden. Wir haben dem aktualisierten Explorer einige Funktionen aus der vorhandenen Benutzeroberfläche hinzugefügt, aber Sie können die vollständigen Benutzeroberflächenfunktionen für eine Gen1-Umgebung im neuen Azure Time Series Insights-Explorer nutzen.

Anstelle der Hierarchie wird der Azure Time Series Insights Gen2-Begriffsbereich angezeigt. Mit dem Begriffsbereich können Sie Abfragen in Ihrer Umgebung definieren. Verwenden Sie ihn auch, um Daten basierend auf einem Prädikat zu filtern.

  [![Abfragebereich „Where“](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Der Bereich des Azure Time Series Insights Gen2-Begriffs-Editors akzeptiert die folgenden Parameter:

**Where**: Mit der Where-Klausel können Sie Ihre Ereignisse schnell mithilfe der in der folgenden Tabelle aufgeführten Gruppe von Operanden filtern. Wenn Sie eine Suche durch Auswählen eines Operanden ausführen, wird das Prädikat automatisch auf Basis dieser Suche aktualisiert. Folgende Operandentypen werden unterstützt:

| Vorgang    | Unterstützte Typen    | Notizen |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Alle Operanden müssen denselben Typ aufweisen oder eine NULL-Konstante sein. |
| `HAS` | String | Im rechten Bereich sind nur konstante Zeichenfolgenliterale zulässig. Leere Zeichenfolgen und NULL sind nicht zulässig. |

Informationen zu den unterstützten Abfragevorgängen und Datentypen finden Sie unter [Time Series Expression (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax).

### <a name="examples-of-where-clauses"></a>Beispiele für Where-Klauseln

  [![Beispiele für die „Where-Klausel“](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Measure**: Eine Dropdownliste mit allen numerischen Spalten (**Doubles**), die Sie als Elemente für Ihr aktuelles Diagramm verwenden können.

**Aufteilen nach**: In dieser Dropdownliste werden alle in Ihrem Modell verfügbaren kategorischen Spalten (Zeichenfolgen) angezeigt, nach denen Sie Ihre Daten gruppieren können. Sie können bis zu fünf Begriffe hinzufügen, um sie auf der gleichen X-Achse anzuzeigen. Geben Sie die gewünschten Parameter ein, und wählen Sie dann **Hinzufügen** aus, um einen neuen Begriff hinzuzufügen.

  [![Abgerufene und gefilterte Ansicht eins](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Sie können Elemente im Diagrammbereich ein- und ausblenden, indem Sie das Symbol „Sichtbar“ auswählen, wie in der folgenden Abbildung dargestellt. Wenn Sie Abfragen vollständig entfernen möchten, wählen Sie das rote **X** aus.

  [![Abbrechen einer abgerufenen und gefilterten Option](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Daten in Ihrer Umgebung erfassen](./concepts-ingestion-overview.md) können.

* Lesen Sie den Artikel zum [Speicher](concepts-storage.md).

* Informieren Sie sich über die [Datenmodellierung](./concepts-model-overview.md) in Azure Time Series Insights Gen2.

* Außerdem sollten Sie sich über das [Diagnostizieren und Beheben von Problemen](./how-to-diagnose-troubleshoot.md) in Ihrer Umgebung informieren.