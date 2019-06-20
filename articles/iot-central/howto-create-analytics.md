---
title: Analysieren Ihrer Gerätedaten in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Analysieren Sie Ihre Gerätedaten in Ihrer Azure IoT Central-Anwendung.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ffe8b350c1b5cea23aeb65092c7912c6d6c1ed89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052960"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Gewusst wie: Analysieren von Gerätedaten durch Analysen

*Dieser Artikel gilt für Betreiber, Ersteller und Administratoren.*

Azure IoT Central stellt umfassende Analysefunktionen bereit, um große Mengen an Daten von Ihren Geräten zu verarbeiten. Um zu beginnen, wechseln Sie im links angezeigten Navigationsmenü zu **Analyse**.

## <a name="querying-your-data"></a>Abfragen Ihrer Daten

Sie müssen zunächst eine **Gerätegruppe** auswählen, einen **Filter** hinzufügen (optional) und einen **Zeitraum** auswählen, um zu starten. Wählen Sie anschließend **Ergebnisse anzeigen** aus, um mit der Visualisierung Ihrer Daten zu beginnen.

* **Gerätegruppen:** Eine [Gerätegruppe](howto-use-device-sets.md) ist eine benutzerdefinierte Gruppe Ihrer Geräte. Beispiele: „Alle Kühlschränke in Oakland“ oder „Alle Windturbinen der Version 2.0“.

* **Filter:** Sie können Ihrer Suche optional Filter hinzufügen, um Ihre Daten zu verfeinern. Sie können bis zu 10 Filter gleichzeitig hinzufügen. Beispielsweise können Sie für „Alle Kühlschränke in Oakland“ diejenigen herausfiltern, deren Temperatur über 15 Grad angestiegen ist.
* **Zeitraum:** Standardmäßig werden die Daten der letzten 10 Minuten abgerufen. Sie können diesen Wert in einen der vordefinierten Zeitbereiche ändern oder einen benutzerdefinierten Zeitraum auswählen.

  ![Analyseabfrage](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisieren Ihrer Daten

Nachdem Sie Ihre Daten abgefragt haben, können Sie damit beginnen, sie zu visualisieren. Sie können Messungen anzeigen/ausblenden, die Art der Datenaggregation ändern und die Daten nach unterschiedlichen Geräteeigenschaften weiter unterteilen.  

* **Aufteilen nach:** Durch das Aufteilen von Daten nach Geräteeigenschaften können Sie weitere Detailinformationen zu Ihren Daten anzeigen. Beispielsweise können Sie Ihre Ergebnisse nach Geräte-ID oder Standort unterteilen.

* **Messungen:** Sie können in einem Arbeitsschritt bis zu 10 verschiedene Telemetrieelemente ein-/ausblenden, die von Ihren Geräten gemeldet werden. Messungen können beispielsweise Informationen wie Temperatur und Feuchtigkeit umfassen.

* **Aggregation:** Daten werden standardmäßig nach dem Durchschnitt aggregiert, aber Sie können die Datenaggregation Ihren Anforderungen entsprechend ändern.

   ![Visualisierung der Analysedaten – „Teilen nach“](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interaktion mit Ihren Daten

Es gibt verschiedene Möglichkeiten, Ihre Abfrageergebnisse so zu ändern, dass sie Ihren Visualisierungsanforderungen entsprechen. Sie können zwischen einer Diagramm- und einer Tabellenansicht wechseln, die Anzeige vergrößern oder verkleinern, Ihr Dataset aktualisieren und die Anzeige der Zeilen ändern.

* **Raster anzeigen:** Ihre Ergebnisse stehen in einem Tabellenformat zur Verfügung, damit Sie den genauen Wert für jeden Datenpunkt anzeigen können. Diese Ansicht erfüllt außerdem die Standards der Barrierefreiheit.
* **Diagramm anzeigen**: Ihre Ergebnisse werden in einem Linienformat angezeigt, damit Sie Aufwärts- oder Abwärtstrends und Anomalien einfacher erkennen können.

  ![Rasteransicht für Ihre Analyse](media/howto-create-analytics/analytics-showgrid.png)

Mit der Vergrößerung können Sie die Ansicht auf Ihre Daten beschränken. Wenn Sie einen bestimmten Zeitraum für Ihr Resultset genauer untersuchen möchten, können Sie den gewünschten Bereich mit dem Cursor markieren und über die verfügbaren Steuerelemente die folgenden Aktionen ausführen:

* **Vergrößern:** Nachdem Sie einen Zeitraum ausgewählt haben, wird die Zoomfunktion aktiviert, und Sie können die Datenansicht vergrößern.
* **Verkleinern:** Mit diesem Steuerelement können Sie die Vergrößerung Ihrer letzten Ansicht um eine Stufe verringern. Wenn Sie Ihre Datenansicht beispielsweise dreimal vergrößert haben, gelangen Sie mit dieser Funktion jeweils einen Schritt zurück.
* **Zoom zurücksetzen**: Nachdem Sie verschiedene Vergrößerungen/Verkleinerungen durchgeführt haben, können Sie die Anzeige mit diesem Steuerelement auf das ursprüngliche Resultset zurücksetzen.

  ![Zoomvorgänge in Ihren Daten](media/howto-create-analytics/analytics-zoom.png)

Sie können den Linienstil an Ihre Anforderungen anpassen. Sie haben vier Möglichkeiten:

* **Linie:** Eine gerade Linie zwischen den einzelnen Datenpunkten.
* **Geglättet**: Eine geschwungene Linie zwischen den einzelnen Datenpunkten.
* **Schritt:** Linie in Form einer Stufe zwischen den einzelnen Datenpunkten im Diagramm.
* **Punkt:** Alle Punkte werden im Diagramm ohne Verbindungslinien gezeichnet.

  ![Verfügbare Linientypen bei der Analyse](media/howto-create-analytics/analytics-linetypes.png)

Schließlich können Sie Ihre Daten auf der Y-Achse anordnen, indem Sie einen von drei Modi auswählen:

* **Gestapelt:** Für jede Messung werden Diagramme als Stapel dargestellt, wobei jedes Diagramm mit einer eigenen Y-Achse versehen ist. Gestapelte Diagramme sind nützlich, wenn Sie mehrere Messungen ausgewählt haben und unterschiedliche Ansichten von diesen Messungen anzeigen möchten.
* **Nicht gestapelt:** Für jedes Measure wird ein Diagramm anhand einer Y-Achse dargestellt, wobei sich die Werte für die Y-Achse jedoch basierend auf dem markierten Measure ändern. Nicht gestapelte Diagramme sind nützlich, wenn Sie mehrere Measures überlagern und für denselben Zeitraum Muster in diesen Measures ermitteln möchten.
* **Gemeinsame Y-Achse**: Alle Diagramme besitzen dieselbe Y-Achse, und die Werte für die Achse ändern sich nicht. Diagramme mit gemeinsamen Y-Achsen sind hilfreich, wenn Sie ein einziges Measure untersuchen möchten und gleichzeitig die Daten nach bestimmten Kriterien aufteilen.

  ![Anordnung der Daten auf der Y-Achse mit unterschiedlichen Visualisierungsmodi](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie benutzerdefinierte Analysen für Ihre Azure IoT Central-Anwendung erstellt werden, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Eine Node.js-Anwendung vorbereiten und verbinden](howto-connect-nodejs.md)