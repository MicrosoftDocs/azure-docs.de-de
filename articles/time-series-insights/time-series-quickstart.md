---
title: 'Schnellstart: Azure Time Series Insights-Explorer | Microsoft-Dokumentation'
description: Dieser Schnellstart veranschaulicht die ersten Schritte mit dem Azure Time Series Insights-Explorer im Webbrowser zum Visualisieren großer IoT-Datenmengen. Übersicht über die wichtigsten Features in einer Demoumgebung.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 17aee2555a0d0364ca5eb8e260ae10432a19bce6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244060"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Schnellstart: Erkunden von Azure Time Series Insights

Dieser Schnellstart für den Azure Time Series Insights-Explorer unterstützt Sie bei den ersten Schritten mit Azure Time Series in einer kostenlosen Demoumgebung. Durch ihn erfahren Sie, wie Sie große IoT-Datenmengen im Webbrowser visualisieren, und lernen die wichtigsten Features kennen, die nun allgemein verfügbar sind.

Azure Time Series Insights ist ein vollständig verwalteter Analyse-, Speicher- und Visualisierungsdienst, der die simultane Untersuchung und Analyse von Milliarden von IoT-Ereignissen vereinfacht. Sie erhalten eine globale Ansicht Ihrer Daten und können basierend darauf Ihre IoT-Lösung schneller überprüfen und kostenintensive Ausfallzeiten unternehmenskritischer Geräte vermeiden. Mit Azure Time Series Insights können Sie versteckte Trends aufdecken, Anomalien erkennen und Ursachenanalysen nahezu in Echtzeit durchführen.

Zur Erhöhung der Flexibilität kann Azure Time Series Insights über seine leistungsfähigen [REST-APIs](./time-series-insights-update-tsq.md) und das [Client-SDK](./tutorial-create-tsi-sample-spa.md) einer bereits vorhandenen Anwendung hinzugefügt werden. Die APIs ermöglichen es Ihnen, Zeitreihendaten in einer Clientanwendung Ihrer Wahl zu speichern, abzufragen und zu nutzen. Sie können auch das Client-SDK verwenden, um Ihrer vorhandenen Anwendung Benutzeroberflächenkomponenten hinzuzufügen.

In dieser Schnellstartanleitung zum Time Series Insights-Explorer werden die Features beschrieben, die nun allgemein verfügbar sind.

## <a name="prepare-the-demo-environment"></a>Vorbereiten der Demoumgebung

1. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), sofern noch nicht geschehen.

1. Navigieren Sie in Ihrem Browser zur [Demo der allgemeinen Verfügbarkeit](https://insights.timeseries.azure.com/demo).

1. Wenn Sie dazu aufgefordert werden, melden Sie sich mit Ihren Azure-Kontoanmeldeinformationen beim Time Series Insights-Explorer an.

1. Die Seite zur Schnelleinführung in Time Series Insights wird angezeigt. Wählen Sie **Weiter** aus, um die Schnelleinführung zu starten.

   [![Auswählen von „Weiter“](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Erkunden der Demoumgebung

1. Der **Zeitauswahlbereich** wird angezeigt. Wählen Sie in diesem Bereich den Zeitrahmen aus, der dargestellt werden soll.

   [![Zeitauswahlbereich](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Wählen Sie einen Zeitrahmen aus, und ziehen Sie ihn in den Bereich. Wählen Sie anschließend **Suchen** aus.

   [![Auswählen eines Zeitrahmens](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   In Time Series Insights wird eine Diagrammvisualisierung für den angegebenen Zeitrahmen angezeigt. Sie können verschiedene Aktionen im Liniendiagramm ausführen. Nutzen Sie beispielsweise Optionen zum Filtern, Anheften, Sortieren und Stapeln.

   Um zum **Zeitauswahlbereich** zurückzukehren, wählen Sie wie dargestellt den Pfeil nach unten aus:

   [![Diagramm](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Wählen Sie im **Begriffsbereich** die Option **Hinzufügen** aus, um einen neuen Suchbegriff hinzuzufügen.

   [![Hinzufügen eines Elements](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Im Diagramm können Sie eine Region auswählen, mit der rechten Maustaste auf die Region klicken und **Explore Events** (Ereignisse durchsuchen) auswählen.

   [![Ereignisse untersuchen](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Ein Raster der Rohdaten aus der durchsuchten Region wird angezeigt.

   [![Rasteransicht](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Auswählen und Filtern von Daten

1. Bearbeiten Sie Ihre Begriffe, um die Werte im Diagramm zu ändern. Fügen Sie einen weiteren Begriff zum übergreifenden Korrelieren verschiedener Werttypen hinzu.

   [![Hinzufügen eines Begriffs](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Geben Sie in das Feld **Datenreihe filtern...** einen Filterbegriff zum improvisierten Filtern von Datenreihen ein. Geben Sie für den Schnellstart **Station5** ein, um die Temperatur und den Druck für die Station übergreifend zu korrelieren.

   [![Datenreihen filtern](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Nach Abschluss des Schnellstarts können Sie das Beispiel-DataSet verwenden, um verschiedene Visualisierungen zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Nun können Sie Ihre eigene Time Series Insights-Umgebung erstellen:
> [!div class="nextstepaction"]
> [Planen Ihrer Time Series Insights-Umgebung](time-series-insights-environment-planning.md)
