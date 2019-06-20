---
title: Erkunden von Daten mithilfe des Azure Time Series Insights-Explorers | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure Time Series Insights-Explorers im Webbrowser schnell eine globale Ansicht Ihrer Big Data anzeigen und die IoT-Umgebung überprüfen können.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: a7905213d67fd1cb97a8e50c938427b7ad775924
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165789"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights-Explorer

In diesem Artikel werden die allgemein verfügbaren Features und Optionen der [Explorer-Web-App](https://insights.timeseries.azure.com/) für Azure Time Series Insights beschrieben. Der Time Series Insights-Explorer veranschaulicht die leistungsstarken Datenvisualisierungsfunktionen, die der Dienst bereitstellt und auf die innerhalb Ihrer eigenen Umgebung zugegriffen werden kann.

Azure Time Series Insights ist ein vollständig verwalteter Analyse-, Speicher- und Visualisierungsdienst, der die simultane Untersuchung und Analyse von Milliarden von IoT-Ereignissen ermöglicht. Sie erhalten globale Einblicke in Ihre Daten und können basierend darauf Ihre IoT-Lösung schneller überprüfen und kostenintensive Ausfallzeiten unternehmenskritischer Geräte vermeiden. Sie können versteckte Trends aufdecken, Abweichungen erkennen und Ursachenanalysen nahezu in Echtzeit durchführen. Der Time Series Insights-Explorer ist derzeit als öffentliche Vorschau verfügbar.

> [!TIP]
> Eine interaktive Tour durch die Demoumgebung finden Sie in der [Schnellstartanleitung für Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Informieren Sie sich über das Abfragen von Daten mit dem Time Series Insights-Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Sehen Sie sich das obige Video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">Getting started with Time Series Insights by using an Azure IoT Solution Accelerator</a> (Erste Schritte mit Time Series Insights unter Verwendung eines Azure IoT Solution Accelerators) an.

## <a name="prerequisites"></a>Voraussetzungen

Vor der Verwendung des Time Series Insights-Explorers müssen Sie folgende Schritte ausführen:

- Erstellen Sie eine Time Series Insights-Umgebung. Weitere Informationen finden Sie unter [Erste Schritte mit Time Series Insights](./time-series-insights-get-started.md).
- [Erteilen Sie Zugriff](time-series-insights-data-access.md) auf Ihr Konto in der Umgebung.
- Fügen Sie eine [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)- oder [Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md)-Ereignisquelle hinzu.

## <a name="explore-and-query-data"></a>Untersuchen und Abfragen von Daten

Binnen weniger Minuten nach der Verbindung der Ereignisquelle mit der Time Series Insights-Umgebung können Sie Ihre Zeitreihendaten untersuchen und abfragen.

1. Öffnen Sie zunächst den [Time Series Insights-Explorer](https://insights.timeseries.azure.com/) in Ihrem Webbrowser. Wählen Sie auf der linken Seite des Fensters eine Umgebung aus. Alle Umgebungen, auf die Sie Zugriff haben, sind in alphabetischer Reihenfolge aufgeführt.

1. Nachdem Sie eine Umgebung ausgewählt haben, können Sie die Konfigurationen **Von** und **Bis** im oberen Bereich verwenden. Sie können aber auch klicken und den Zeiger über die gewünschte Zeitspanne ziehen. Klicken Sie rechts oben auf die Lupe, oder klicken Sie mit der rechten Maustaste auf die ausgewählte Zeitspanne, und wählen Sie **Suchen** aus.

1. Mithilfe der Schaltfläche **Auto On** (Automatisch ein) können Sie außerdem festlegen, dass die Verfügbarkeit automatisch einmal pro Minute aktualisiert werden soll. Die Schaltfläche **Auto On** (Automatisch ein) gilt nur für das Verfügbarkeitsdiagramm, nicht für den Inhalt der Hauptvisualisierung.

1. Über das Azure-Cloudsymbol gelangen Sie zu Ihrer Umgebung im Azure-Portal.

   [![Time Series Insights-Umgebung](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Als Nächstes sehen Sie ein Diagramm, in dem die Anzahl aller Ereignisse in der ausgewählten Zeitspanne angezeigt wird. Hier stehen mehrere Steuerelemente zur Verfügung:

    - **Editor-Bereich für Begriffe**: Im Begriffsbereich führen Sie Abfragen in Ihrer Umgebung durch. Er befindet sich auf der linken Seite des Bildschirms:
      - **Measure**: In dieser Dropdownliste werden alle numerischen Spalten (**Doubles**) angezeigt.
      - **Aufteilen nach**: In dieser Dropdownliste werden Kategoriespalten (**Zeichenfolgen**) angezeigt.
      - Mithilfe der Einstellungen neben **Measure** können Sie die Schrittinterpolation aktivieren, Mindest- und Höchstwerte anzeigen und die Y-Achse anpassen. Außerdem können Sie festlegen, ob die angezeigten Daten als Anzahl, Durchschnitt oder Summe der Daten angezeigt werden sollen.
      - Sie können bis zu fünf Begriffe hinzufügen, um sie auf der gleichen X-Achse anzuzeigen. Verwenden Sie die Schaltfläche **Copy-down** (Kopieren), um einen zusätzlichen Begriff hinzuzufügen, oder wählen Sie **Hinzufügen** aus, um einen neuen Begriff hinzuzufügen.

        [![Editor-Bereich für Begriffe](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Prädikat**: Mithilfe des Prädikats können Sie Ihre Ereignisse schnell unter Verwendung der in der folgenden Tabelle aufgeführten Gruppe von Operanden filtern. Wenn Sie eine Suche durch Auswählen oder Klicken ausführen, wird das Prädikat basierend auf dieser Suche automatisch aktualisiert. Folgende Operandentypen werden unterstützt:

         |Vorgang  |Unterstützte Typen  |Notizen  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Alle Operanden müssen denselben Typ aufweisen oder eine NULL-Konstante sein.        |
         |HAS     | string        |  Im rechten Bereich sind nur konstante Zeichenfolgenliterale zulässig. Leere Zeichenfolgen und NULL sind nicht zulässig.       |

      - **Beispiele für Abfragen**

         [![Beispielabfragen](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Mit dem Schieberegler für die **Intervallgröße** können Sie die Intervalle in einer Zeitspanne vergrößern und verkleinern. Dies ermöglicht eine genauere Steuerung der Bewegung zwischen größeren Zeitabschnitten, die Trends gleichmäßig bis hin zu kleinsten Zeitabschnitten in Millisekunden anzeigen, um präzise, hochauflösende Schnitte Ihrer Daten zu erhalten. Der Standardstartpunkt des Schiebereglers ist auf die optimale Darstellung der Daten aus Ihrer Auswahl festgelegt und bietet ein ausgewogenes Verhältnis zwischen Auflösung, Abfragegeschwindigkeit und Granularität.

1. Mit dem Tool **Time Brush** können Sie komfortabel zwischen den einzelnen Zeitspannen navigieren.

1. Mit dem Befehl **Speichern** können Sie Ihre aktuelle Abfrage speichern und für andere Benutzer der Umgebung freigeben. Über **Öffnen** können Sie alle Ihre gespeicherten Abfragen sowie freigegebene Abfragen anderer Benutzer in Umgebungen anzeigen, auf die Sie Zugriff haben.

   [![Abfragen](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualisieren von Daten

1. Mit dem Tool **Perspektivische Ansicht** können Sie bis zu vier eindeutige Abfragen parallel anzeigen. Die Schaltfläche **Perspektivische Ansicht** befindet sich in der rechten oberen Ecke des Diagramms.

   [![Perspektivische Ansicht](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Zeigen Sie ein Diagramm an, um Ihre Daten visuell zu untersuchen, und verwenden Sie die **Diagrammtools**:

    - **Wählen Sie eine bestimmte Zeitspanne oder eine einzelne Datenreihe aus**, oder **klicken Sie auf eine bestimmte Zeitspanne oder auf eine einzelne Datenreihe**.
    - Innerhalb einer Zeitspannenauswahl können Sie zoomen und Ereignisse untersuchen.
    - Innerhalb einer Datenreihe können Sie die Reihe nach einer anderen Spalte teilen, die Reihe als neuen Begriff hinzufügen, nur die ausgewählte Reihe anzeigen, die ausgewählte Reihe ausschließen, die Reihe anheften oder Ereignisse aus der ausgewählten Reihe untersuchen.
    - Im Filterbereich links neben dem Diagramm können Sie alle dargestellten Datenreihen sehen und nach Wert oder Name sortieren. Darüber hinaus können Sie alle Datenreihen oder alle angehefteten/getrennten Reihen anzeigen. Sie können eine einzelne Datenreihe auswählen und nach einer anderen Spalte teilen, die Reihe als neuen Begriff hinzufügen, nur die ausgewählte Reihe anzeigen, die ausgewählte Reihe ausschließen, die Reihe anheften oder Ereignisse aus der ausgewählten Reihe untersuchen.
    - Bei gleichzeitiger Darstellung mehrerer Begriffe können Sie Stapel bilden, die Stapelung aufheben, weitere Daten zu einer Datenreihe anzeigen und für alle Begriffe die gleiche Y-Achse verwenden. Verwenden Sie hierzu die Schaltflächen in der rechten oberen Ecke des Diagramms.

    [![Diagrammtool](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Mithilfe des **Wärmebilds** können Sie schnell besondere oder ungewöhnliche Datenreihen in einer bestimmten Abfrage erkennen. Nur ein Suchbegriff kann als Wärmebild visualisiert werden.

    [![Wärmebild](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Beim Untersuchen von Ereignissen durch Auswählen oder Klicken mit der rechten Maustaste wird der **Ereignisbereich** angezeigt. Hier können Sie alle Ihre Rohereignisse anzeigen und die Ereignisse als JSON- oder CSV-Dateien exportieren. In Time Series Insights werden alle Rohdaten gespeichert.

    [![Ereignisse](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Wählen Sie nach dem Untersuchen von Ereignissen die Registerkarte **STATISTIK** aus, um die Funktionen für Muster und Spaltenstatistik anzuzeigen.

    - **Muster**: Dieses proaktive Feature zeigt die Muster an, die in einer ausgewählten Datenregion statistisch besonders signifikant sind. Sie müssen nicht Tausende von Ereignissen überprüfen, um zu ermitteln, welche Muster die meiste Zeit und Energie erfordern. Mit Time Series Insights können Sie direkt zu diesen statistisch signifikanten Mustern navigieren und Ihre Analyse fortsetzen. Dieses Feature ist auch nützlich für nachträgliche Überprüfungen von Verlaufsdaten.
    - **Spaltenstatistik**: Die Spaltenstatistik liefert Diagramme und Tabellen, in denen Daten aus den einzelnen Spalten der ausgewählten Datenreihe über die ausgewählte Zeitspanne aufgeschlüsselt sind.

      [![STATISTIK](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Damit kennen Sie nun die verschiedenen Features und Optionen, die in der Explorer-Web-App von Time Series Insights zur Verfügung stehen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [Probleme in Ihrer Time Series Insights-Umgebung diagnostizieren und beheben](time-series-insights-diagnose-and-solve-problems.md).
- Sehen Sie sich die [Schnellstartanleitung für Azure Time Series Insights](time-series-quickstart.md) an.
