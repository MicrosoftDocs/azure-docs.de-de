---
title: 'Erkunden von Daten mit dem Explorer: Azure Time Series Insights | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit dem Azure Time Series Insights-Explorer IoT-Daten anzeigen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020111"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights-Explorer (Gen1)

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel werden die Features und Optionen der [Explorer-Web-App](https://insights.timeseries.azure.com/) für Azure Time Series Insights (Gen1) beschrieben. Der Azure Time Series Insights-Explorer veranschaulicht die leistungsstarken Datenvisualisierungsfunktionen, die der Dienst bereitstellt und auf die innerhalb Ihrer eigenen Umgebung zugegriffen werden kann.

Azure Time Series Insights ist ein vollständig verwalteter Analyse-, Speicher- und Visualisierungsdienst, der die simultane Untersuchung und Analyse von Milliarden von IoT-Ereignissen ermöglicht. Sie erhalten globale Einblicke in Ihre Daten und können basierend darauf Ihre IoT-Lösung schneller überprüfen und kostenintensive Ausfallzeiten unternehmenskritischer Geräte vermeiden. Sie können versteckte Trends aufdecken, Abweichungen erkennen und Ursachenanalysen nahezu in Echtzeit durchführen.

> [!TIP]
> Eine interaktive Tour durch die Demoumgebung finden Sie in der [Schnellstartanleitung für Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Informieren Sie sich über das Abfragen von Daten mit dem Azure Time Series Insights-Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Sehen Sie sich das vorherige Video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">Erste Schritte mit Time Series Insights unter Verwendung eines Azure IoT Solution Accelerators</a> an.

## <a name="prerequisites"></a>Voraussetzungen

Vor der Verwendung des Azure Time Series Insights-Explorers müssen Sie folgende Schritte ausführen:

- Erstellen einer Azure Time Series Insights-Umgebung. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure Time Series Insights Gen1-Umgebung über das Azure-Portal](./time-series-insights-get-started.md).
- [Erteilen Sie Zugriff](./concepts-access-policies.md) auf Ihr Konto in der Umgebung.
- Fügen Sie eine [IoT Hub](./how-to-ingest-data-iot-hub.md)- oder [Event Hub](./how-to-ingest-data-event-hub.md)-Ereignisquelle hinzu.

## <a name="explore-and-query-data"></a>Untersuchen und Abfragen von Daten

Binnen weniger Minuten nach der Verbindung der Ereignisquelle mit der Azure Time Series Insights-Umgebung können Sie Ihre Zeitreihendaten untersuchen und abfragen.

1. Öffnen Sie zunächst den [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com/) in Ihrem Webbrowser. Wählen Sie auf der linken Seite des Fensters eine Umgebung aus. Alle Umgebungen, auf die Sie Zugriff haben, sind in alphabetischer Reihenfolge aufgeführt.

1. Nachdem Sie eine Umgebung ausgewählt haben, können Sie die Konfigurationen **Von** und **Bis** im oberen Bereich verwenden. Sie können die gewünschte Zeitspanne aber auch mit der Maus markieren und so auswählen. Klicken Sie rechts oben auf die Lupe, oder klicken Sie mit der rechten Maustaste auf die ausgewählte Zeitspanne, und wählen Sie **Suchen** aus.

1. Mithilfe der Schaltfläche **Auto On** (Automatisch ein) können Sie außerdem festlegen, dass die Verfügbarkeit automatisch einmal pro Minute aktualisiert werden soll. Die Schaltfläche **Auto On** (Automatisch ein) gilt nur für das Verfügbarkeitsdiagramm, nicht für den Inhalt der Hauptvisualisierung.

1. Über das Azure-Cloudsymbol gelangen Sie zu Ihrer Umgebung im Azure-Portal.

   [![Azure Time Series Insights-Umgebungsauswahl](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Dann wird ein Diagramm angezeigt, in dem die Anzahl aller Ereignisse in der ausgewählten Zeitspanne angezeigt wird. Hier stehen mehrere Steuerelemente zur Verfügung:

    - **Editor-Bereich für Begriffe**: Im Begriffsbereich führen Sie Abfragen in Ihrer Umgebung durch. Er befindet sich auf der linken Seite des Bildschirms:
      - **MEASURE**: In dieser Dropdownliste werden alle numerischen Spalten (**Doubles**) angezeigt.
      - **AUFTEILEN NACH**: In dieser Dropdownliste werden Kategoriespalten (**Zeichenfolgen**) angezeigt.
      - Mithilfe der Einstellungen neben **MEASURE** können Sie die Schrittinterpolation aktivieren, Mindest- und Höchstwerte anzeigen und die Y-Achse anpassen. Außerdem können Sie festlegen, ob die angezeigten Daten als Anzahl, Durchschnitt oder Summe der Daten angezeigt werden sollen.
      - Sie können bis zu fünf Begriffe hinzufügen, um sie auf der gleichen X-Achse anzuzeigen. Wählen Sie **Hinzufügen** aus, um einen neuen Begriff hinzuzufügen, oder verwenden Sie die Schaltfläche **Diesen Begriff klonen**, um eine Kopie eines vorhandenen Begriffs hinzuzufügen.

        [![Bereich „Begriffsauswahl, Filterung und Abfrage“](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Prädikat**: Mithilfe des Prädikats können Sie Ihre Ereignisse schnell unter Verwendung der in der folgenden Tabelle aufgeführten Gruppe von Operanden filtern. Wenn Sie eine Suche durch Auswählen oder Klicken ausführen, wird das Prädikat basierend auf dieser Suche automatisch aktualisiert. Folgende Operandentypen werden unterstützt:

         |Vorgang  |Unterstützte Typen  |Notizen  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=** , **!=** , **<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Alle Operanden müssen denselben Typ aufweisen oder eine **NULL**-Konstante sein.        |
         |**HAS**     | **String**        |  Im rechten Bereich sind nur konstante Zeichenfolgenliterale zulässig. Leere Zeichenfolgen und **NULL** sind unzulässig.       |

      - **Beispielabfragen**

         [![Gen1-Beispielabfragen](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Mit dem Schieberegler für die **Intervallgröße** können Sie die Intervalle in einer Zeitspanne vergrößern und verkleinern. Dies ermöglicht eine genauere Steuerung der Bewegung zwischen größeren Zeitabschnitten, die Trends gleichmäßig bis hin zu kleinsten Zeitabschnitten in Millisekunden anzeigen. So können Sie präzise, hochauflösende Schnitte Ihrer Daten anzeigen und analysieren. Der Standardstartpunkt des Schiebereglers ist auf die optimale Darstellung der Daten aus Ihrer Auswahl festgelegt und bietet ein ausgewogenes Verhältnis zwischen Auflösung, Abfragegeschwindigkeit und Granularität.

1. Mit dem Tool **Time Brush** können Sie komfortabel zwischen den einzelnen Zeitspannen navigieren.

1. Wählen Sie den Befehl **Speichern** aus, um Ihre aktuelle Abfrage zu speichern und für andere Benutzer der Umgebung freizugeben. Wenn Sie das Symbol **Öffnen** auswählen, können Sie alle Ihre gespeicherten Abfragen sowie freigegebene Abfragen anderer Benutzer in Umgebungen überprüfen, auf die Sie Zugriff besitzen.

   [![Abfragen](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualisieren von Daten

1. Mit dem Tool **Perspektivische Ansicht** können Sie bis zu vier eindeutige Abfragen parallel anzeigen. Die Schaltfläche **Perspektivische Ansicht** befindet sich in der rechten oberen Ecke des Diagramms.

   [![Auswählen von Abfragen zum Hinzufügen zum Bereich „Perspektive“](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Zeigen Sie ein Diagramm an, um Ihre Daten visuell zu untersuchen, und verwenden Sie die **Diagrammtools**:

    - **Wählen Sie eine bestimmte Zeitspanne oder eine einzelne Datenreihe aus**, oder **klicken Sie auf eine bestimmte Zeitspanne oder auf eine einzelne Datenreihe**.
    - Innerhalb einer Zeitspannenauswahl können Sie zoomen und Ereignisse untersuchen.
    - Innerhalb einer Datenreihe können Sie die Reihe nach einer anderen Spalte teilen, die Reihe als neuen Begriff hinzufügen, nur die ausgewählte Reihe anzeigen, die ausgewählte Reihe ausschließen, die Reihe anheften oder Ereignisse aus der ausgewählten Reihe untersuchen.
    - Im Filterbereich links neben dem Diagramm können Sie alle dargestellten Datenreihen überprüfen und nach Wert oder Name sortieren. Darüber hinaus können Sie alle Datenreihen oder alle angehefteten/getrennten Reihen anzeigen. Sie können eine einzelne Datenreihe auswählen und nach einer anderen Spalte teilen, die Reihe als neuen Begriff hinzufügen, nur die ausgewählte Reihe anzeigen, die ausgewählte Reihe ausschließen, die Reihe anheften oder Ereignisse aus der ausgewählten Reihe untersuchen.
    - Bei gleichzeitiger Darstellung mehrerer Begriffe können Sie Stapel bilden, die Stapelung aufheben, weitere Daten zu einer Datenreihe überprüfen und für alle Begriffe die gleiche Y-Achse verwenden. Verwenden Sie hierzu die Schaltflächen in der rechten oberen Ecke des Diagramms.

    [![Einstellungen für die Optionen in der oberen rechten Ecke des Diagrammtools](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Mithilfe des **Wärmebilds** können Sie schnell besondere oder ungewöhnliche Datenreihen in einer bestimmten Abfrage erkennen. Nur ein Suchbegriff kann als Wärmebild visualisiert werden.

    [Wärmebilddiagramm von Time Series Insights-Explorer](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Beim Untersuchen von Ereignissen durch Auswählen oder Klicken mit der rechten Maustaste wird der Bereich **EREIGNISSE** angezeigt. Hier können Sie alle Ihre Rohereignisse überprüfen und die Ereignisse als JSON- oder CSV-Dateien exportieren. In Azure Time Series Insights werden alle Rohdaten gespeichert.

    [![Ereignisse](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Wählen Sie nach dem Untersuchen von Ereignissen die Registerkarte **STATISTIK** aus, um die Funktionen für Muster und Spaltenstatistik anzuzeigen.

    - **Muster**: Dieses proaktive Feature zeigt die Muster an, die in einer ausgewählten Datenregion statistisch besonders signifikant sind. Sie müssen nicht Tausende von Ereignissen überprüfen, um zu ermitteln, welche Muster die meiste Zeit und Energie erfordern. Mit Azure Time Series Insights können Sie direkt zu diesen statistisch signifikanten Mustern navigieren und Ihre Analyse fortsetzen. Dieses Feature ist auch nützlich für nachträgliche Überprüfungen von Verlaufsdaten.
    - **Spaltenstatistik**: Die Spaltenstatistik liefert Diagramme und Tabellen, in denen Daten aus den einzelnen Spalten der ausgewählten Datenreihe über die ausgewählte Zeitspanne aufgeschlüsselt sind.

      [![Diagramm und Optionen für die Spalte STATISTIK](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nun haben Sie die wichtigsten Features, Konfigurationseinstellungen und Anzeigeoptionen kennengelernt, die in der Explorer-Web-App von Azure Time Series Insights verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [Probleme in Ihrer Time Series Insights-Umgebung diagnostizieren und beheben](time-series-insights-diagnose-and-solve-problems.md).

- Sehen Sie sich die [Schnellstartanleitung für Azure Time Series Insights](time-series-quickstart.md) an.