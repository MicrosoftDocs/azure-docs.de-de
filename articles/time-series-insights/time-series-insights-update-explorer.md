---
title: Visualisieren von Daten im Azure Time Series Insights Preview-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden die in der Time Series Insights Preview-Explorer-Web-App verfügbaren Funktionen und Optionen beschrieben.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399863"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualisieren von Daten in der Explorer Preview

In diesem Dokument werden die UI/UX-Features und die Oberfläche der [Demo-Web-App](https://insights.timeseries.azure.com/preview/demo) für Azure Time Series Insights Preview beschrieben. Insbesondere werden darin das Layout des gehosteten Beispiels, die Anpassungsoptionen für die Benutzeroberfläche und die Navigation durch die bereitgestellte Demo erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Zum Einstieg in den Azure Time Series Insights Preview-Explorer müssen Sie Folgendes erledigt haben:

* Einrichten einer Time Series Insights-Umgebung. Wenn Sie mehr zur Bereitstellung einer Instanz erfahren möchten, probieren Sie das Tutorial [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) aus.
* [Bereitstellung von Datenzugriff](./time-series-insights-data-access.md) auf die Time Series Insights-Umgebung, die Sie für das Konto erstellt haben. Außer Ihnen, können Sie auch anderen Zugriff gewähren.
* Hinzufügen einer Ereignisquelle zur Time Series Insights-Umgebung zum Senden von Daten per Push an die Umgebung:
  * Informationen zum [Herstellen einer Verbindung mit einem Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Informationen zum [Herstellen einer Verbindung mit einem IoT-Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>Informationen zum Preview-Explorer

Der Azure Time Series Insights Preview-Explorer besteht aus folgenden Elementen:

[![Explorer-Ansicht](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Umgebungsbereich</a>: Zeigt Ihre Azure Time Series Insights-Umgebungen an.
- <a href="#navigation-menu">Navigationsmenü</a>: Ermöglicht das Wechseln zwischen den Seiten **Analysieren** und **Modell**.
- <a href="#hierarchy-tree">Hierarchiestruktur</a>: Ermöglicht das Auswählen bestimmter Modell- und Datenelemente für die Diagrammdarstellung.
- <a href="#preview-well">Zeitreihenquelle</a>: Zeigt Ihre aktuell ausgewählten Datenelemente im Tabellenformat mit Farbcodierung an.
- <a href="#preview-chart">Diagrammbereich</a>: Zeigt Ihr aktuelles Arbeitsdiagramm an.
- <a href="#time-editor-panel">Zeitachse</a>: Ermöglicht das Ändern des Arbeitszeitraums.
- <a href="#navigation-panel">App-Leiste</a>: Enthält Ihre Benutzerverwaltungsoptionen (etwa den aktuellen Mandanten). Ermöglicht das Ändern von Design- und Spracheinstellungen.

## <a name="environment-drop-down-list"></a>Dropdownliste für die Umgebung

In der Dropdownliste für die Umgebung werden alle Time Series Insights-Umgebungen angezeigt, auf die Sie Zugriff haben. Die Liste enthält Umgebungen mit nutzungsbasierter Bezahlung (unter anderem Time Series Insights Preview). Darüber hinaus sind in der Liste auch allgemein verfügbare S1-/S2-Umgebungen enthalten.

1. Wählen Sie den Dropdownpfeil neben der angezeigten Umgebung aus.

   [![Die Systemsteuerung](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Wählen Sie anschließend die gewünschte Umgebung aus.

## <a name="navigation-menu"></a>Navigationsmenü

  [![Das Navigationsmenü](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Im Navigationsmenü stehen zwei Ansichten zur Auswahl:

* **Analysieren**: Ermöglicht umfangreiche Analysen für Ihre modellierten oder nicht modellierten Zeitreihendaten sowie deren Darstellung als Diagramme.
* **Modell**: Ermöglicht das Pushen neuer Time Series Insights Preview-Typen, -Hierarchien und -Instanzen an Ihr Time Series Insights-Modell.

## <a name="hierarchy-tree"></a>Hierarchiestruktur

Die Hierarchiestruktur zeigt ausgewählte Datenelemente an. Hierzu zählen unter anderem Modelle, bestimmte Geräte sowie Sensoren Ihrer Geräte.

### <a name="model-search-panel"></a>Modellsuchbereich

Im Modellsuchbereich können Sie ganz einfach in Ihrer Zeitreihenmodellhierarchie suchen und navigieren, um die spezifischen Zeitreiheninstanzen zu finden, die Sie in Ihrem Diagramm anzeigen möchten. Die ausgewählten Instanzen werden sowohl dem aktuellen Diagramm als auch der Datenquelle hinzugefügt.

  [![Der Modellsuchbereich](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modellerstellung

Azure Time Series Insights Preview unterstützt vollständige CRUD-Vorgänge („Create“, „Read“, „Update“, „Delete“; „Erstellen“, „Lesen“, „Aktualisieren“, „Löschen“) für Ihr Zeitreihenmodell.

* **Zeitreihenmodelltyp**: Mithilfe von Time Series Insights-Typen können Sie Variablen oder Formeln für Berechnungen definieren. Sie werden mit einer bestimmten Time Series Insights-Instanz verknüpft. Ein Typ kann eine oder mehrere Variablen enthalten.
* **Zeitreihenmodellhierarchie**: Hierarchien sind systematische Organisationen Ihrer Daten. Hierarchien stellen die Beziehungen zwischen verschiedenen Entitäten in Ihren Time Series Insights-Daten dar.
* **Zeitreihenmodellinstanz**: Instanzen sind die Zeitreihen selbst. In den meisten Fällen handelt es sich dabei um die Geräte-ID (**DeviceID**) oder die Objekt-ID (**AssetID**) – also um den eindeutigen Bezeichner des Objekts in der Umgebung.

Weitere Informationen zu Zeitreihenmodellen finden Sie unter [Zeitreihenmodelle](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Vorschauquelle

In der Quelle werden Instanzfelder und andere Metadaten angezeigt, die ausgewählten Time Series Insights-Instanzen zugeordnet sind. Mithilfe der Kontrollkästchen auf der rechten Seite können Sie bestimmte Instanzen im aktuellen Diagramm ausblenden oder anzeigen. Sie können auch bestimmte Datenelemente aus Ihrer aktuellen Datenquelle entfernen, indem Sie auf der linken Seite des Elements auf das rote Steuerelement **Löschen** (Papierkorb) klicken.

  [![Die Vorschauquelle](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Wenn Sie das Layout der Diagrammseite **Analysieren** neu konfigurieren möchten, wählen Sie die Auslassungspunkte in der rechten oberen Ecke aus:

  [![Layoutoptionen für Telemetrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Sollte die folgende Meldung angezeigt werden, liegen im ausgewählten Zeitraum keine Daten für die Instanz vor. Verwenden Sie zur Behebung des Problems einen größeren Zeitraum, oder vergewissern Sie sich, dass von der Instanz Daten gepusht werden.
>
> ![Keine Datenbenachrichtigung](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Vorschaudiagramm

Mit dem Diagramm können Sie Time Series Insights-Instanzen als Linien anzeigen. Durch Auswählen der Websteuerelemente können Sie den Umgebungsbereich, das Datenmodell und die Einstellungen für den Zeitraum reduzieren, um das Diagramm zu vergrößern.

  [![Vorschaudiagramm „Übersicht“](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Ausgewählter Datumsbereich**: Steuert, welche Datenelemente zur Visualisierung verfügbar sind.

- **Schieberegler für den inneren Datumsbereich**: Ziehen Sie die beiden Endpunktsteuerelemente über den gewünschten Zeitraum.

- **Zeitraum-Reduzierungssteuerelement**: Reduziert und erweitert den Zeitraumbereichs-Editor.

- **Y-Achsenformat-Steuerelement**: Durchläuft die verfügbaren Optionen für die Y-Achse:

    * `Default`: Jede Zeile hat eine eigene Y-Achse.
    * `Stacked`: Ermöglicht das Stapeln mehrerer Zeilen auf der gleichen Y-Achse, wobei sich die Y-Achsendaten je nach ausgewählter Zeile ändern.
    * `Shared`: Alle Y-Achsendaten werden zusammen angezeigt.

- **Aktuelles Datenelement**: Das aktuell ausgewählte Datenelement und seine zugehörigen Details.

Sie können Detailinformationen zu einem bestimmten Datenslice anzeigen, indem Sie auf einen Datenpunkt im aktuellen Diagramm klicken und dann den ausgewählten Bereich zum Endpunkt Ihrer Wahl ziehen. Klicken Sie mit der rechten Maustaste auf den grauen ausgewählten Bereich, und wählen Sie **Zoom** aus, wie in der folgenden Abbildung dargestellt:

  [![Vorschaudiagramm „Zoom“](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Nach dem **Zoomen** wird Ihr ausgewähltes Dataset angezeigt. Wählen Sie das Steuerelement für das Format der Y-Achse aus, um zwischen den drei Y-Achsendarstellungen Ihrer Time Series Insights-Daten zu wechseln.

  [![Vorschaudiagramm für die Y-Achse](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Hier sehen Sie ein Beispiel für gemeinsam genutzte Y-Achsen:

  [![Vorschau gemeinsam genutzter Y-Achsen](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Editor-Bereich für Zeiten

Wenn Sie mit Time Series Insights Preview arbeiten, wählen Sie zunächst einen Zeitraum aus. Der ausgewählte Zeitraum steuert das Dataset, das für die Manipulation durch die Time Series Insights Preview-Widgets verfügbar ist. In Time Series Insights Preview stehen folgende Websteuerelemente zum Auswählen Ihres Arbeitszeitraums zur Verfügung:

  [![Zeitauswahlbereich](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Schieberegler für den inneren Datumsbereich**: Ziehen Sie die beiden Endpunktsteuerelemente über den gewünschten Zeitraum. Dieser innere Datumsbereich wird durch den Schieberegler für den äußeren Datumsbereich begrenzt.

1. **Schaltflächen zum Erhöhen/Verringern des Datumsbereichs**: Sie können Ihren Zeitraum erhöhen oder verringern, indem Sie eine der Schaltflächen für das gewünschte Intervall auswählen.

1. **Zeitraum-Reduzierungssteuerelement**: Mit diesem Websteuerelement können Sie alle Steuerelemente ausblenden (mit Ausnahme des Schiebereglers für den inneren Datumsbereich).

1. **Schieberegler für den äußeren Datumsbereich**: Verwenden Sie die Endpunktsteuerelemente, um den äußeren Datumsbereich auszuwählen. Dieser steht dann für das Steuerelement für den inneren Datumsbereich zur Verfügung.

1. **Dropdownliste zum schnellen Ändern des Datumsbereichs**: Hiermit können Sie schnell zwischen voreingestellten Zeitbereichen wechseln. Beispiele wären etwa die letzten **30 Minuten**, die letzten **12 Stunden** oder ein **benutzerdefinierter Bereich**. Durch das Ändern dieses Werts werden auch die verfügbaren Intervallbereiche geändert, die im Intervallgrößen-Schiebereglertool besprochen werden.

1. **Intervallgröße-Schiebereglertool**: Hiermit können Sie Intervalle innerhalb eines Zeitraums vergrößern und verkleinern. Diese Aktion bietet eine genauere Steuerung der Bewegung zwischen größeren Zeitabschnitten. Sie zeigt ausgeglichene Trends für Slices bis hin zum Millisekundenbereich an. Dies ermöglicht präzise, hochauflösende Schnitte Ihrer Daten. Der Standardstartpunkt des Schiebereglers ist als optimierte Ansicht der Daten aus Ihrer Auswahl festgelegt und gleicht somit Auflösung, Abfragegeschwindigkeit und Granularität aus.

1. **Websteuerelement für Ende und Anfang eines Datumsbereichs**: Mit diesem Websteuerelement können Sie komfortabel die gewünschten Datumsbereiche und Zeiträume auswählen. Sie können das Steuerelement auch verwenden, um zwischen verschiedenen Zeitzonen zu wechseln. Wählen Sie **Speichern** aus, nachdem Sie die gewünschten Änderungen für Ihren aktuellen Arbeitsbereich vorgenommen haben.

   [![Auswahlbereich „In“ und „Aus“](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigationsbereich

Der Navigationsbereich von Time Series Insights Preview wird oben in Ihrer Time Series Insights-App angezeigt. Er bietet die folgenden Funktionen.

### <a name="current-session-share-link-control"></a>Steuerelement für Freigabelink der aktuellen Sitzung

  [![Symbol „Freigeben“](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Wählen Sie das neue Symbol **Freigeben** aus, um einen URL-Link für Ihr Team freizugeben.

  [![Freigeben Ihrer Instanz-URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Mandantenabschnitt

  [![Mandantenauswahl](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Zeigt Ihre aktuellen Time Series Insights-Anmeldekontoinformationen an.
* Ermöglicht das Wechseln zwischen den verfügbaren Time Series Insights-Designs.
* Ermöglicht das Anzeigen der [Demo-Web-App](https://insights.timeseries.azure.com/preview/demo) für die Vorschauversion.

### <a name="theme-selection"></a>Designauswahl

Wenn Sie ein neues Design auswählen möchten, wählen Sie rechts oben Ihr Profilsymbol aus. Wählen Sie dann **Design ändern** aus.

  [![Designauswahl](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Die Sprachauswahl ist auch verfügbar, wenn Sie Ihr Profilsymbol auswählen.

Azure Time Series Insights Preview unterstützt zwei Designs:

* **Helles Design**: Das Standarddesign, das im gesamten vorliegenden Dokument gezeigt wird.
* **Dunkles Design**: Stellt den Explorer wie hier gezeigt dar:

  [![„Dunkles Design“ ausgewählt](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Steuerelemente für S1/S2-Umgebungen

### <a name="preview-terms-panel"></a>Begriffebereich der Preview

Dieser Abschnitt gilt nur für vorhandene S1/S2-Umgebungen, die versuchen, den Explorer mit der aktualisierten Benutzeroberfläche zu verwenden. Das allgemein verfügbare Produkt und die Vorschauversion können gemeinsam verwendet werden. Wir haben dem aktualisierten Explorer einige Funktionen aus der vorhandenen Benutzeroberfläche hinzugefügt, aber Sie können die vollständige Benutzeroberflächenerfahrung für S1/S2-Umgebungen im vorhandenen Time Series Insights-Explorer erhalten. 

Anstelle der Hierarchie wird der Time Series Insights-Begriffebereich angezeigt, in dem Sie Abfragen in Ihrer Umgebung definieren können. Dadurch können Sie Ihre Daten auf der Grundlage eines Prädikats filtern.

  [![Abfragebereich „Where“](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Der Time Series Insights Preview-Begriffe-Editor-Bereich akzeptiert die folgenden Parameter:

**Where**: Mit der Where-Klausel können Sie Ihre Ereignisse schnell mithilfe der in der folgenden Tabelle aufgeführten Gruppe von Operanden filtern. Wenn Sie eine Suche durch Auswählen eines Operanden ausführen, wird das Prädikat automatisch auf Basis dieser Suche aktualisiert. Folgende Operandentypen werden unterstützt:

| Vorgang | Unterstützte Typen   | Notizen |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Alle Operanden müssen denselben Typ aufweisen oder eine NULL-Konstante sein. |
| `HAS` | Zeichenfolge | Im rechten Bereich sind nur konstante Zeichenfolgenliterale zulässig. Leere Zeichenfolgen und NULL sind nicht zulässig. |

Informationen zu den unterstützten Abfragevorgängen und Datentypen finden Sie unter [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Beispiele für Where-Klauseln

  [![Beispiele für die „Where-Klausel“](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Measure**: Eine Dropdownliste mit allen numerischen Spalten (**Doubles**), die Sie als Elemente für Ihr aktuelles Diagramm verwenden können.

**Aufteilen nach**: In dieser Dropdownliste werden alle in Ihrem Modell verfügbaren kategorischen Spalten (Zeichenfolgen) angezeigt, nach denen Sie Ihre Daten gruppieren können. Sie können bis zu fünf Begriffe hinzufügen, um sie auf der gleichen X-Achse anzuzeigen. Geben Sie die gewünschten Parameter ein, und wählen Sie dann **Hinzufügen** aus, um einen neuen Begriff hinzuzufügen.

  [![Abgerufene und gefilterte Ansicht eins](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Sie können Elemente im Diagrammbereich ein- und ausblenden, indem Sie das Symbol „Sichtbar“ auswählen, wie in der folgenden Abbildung dargestellt. Wenn Sie Abfragen vollständig entfernen möchten, wählen Sie das rote **X** aus.

  [![Abgerufene und gefilterte Ansicht zwei](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Datenspeicherung und Dateneingang](./time-series-insights-update-storage-ingress.md) in Azure Time Series Insights Preview.
- Lesen Sie das Time Series Insights Preview-Dokument zur [Datenmodellierung](./time-series-insights-update-tsm.md).
- Informieren Sie sich über die [Vorgehensweise zur Diagnose und Problembehandlung](./time-series-insights-update-how-to-troubleshoot.md) in Ihrer Time Series Insights-Instanz.
