---
title: Anzeigen der Azure Maps-API-Nutzungsmetriken | Microsoft Azure Maps
description: Erfahren Sie, wie Sie Azure Maps-API-Nutzungsmetriken anzeigen, z. B. Anforderungen gesamt, Fehler gesamt und Verfügbarkeit. Erfahren Sie, wie Sie Daten filtern und Ergebnisse aufteilen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003513"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Anzeigen der Azure Maps-API-Nutzungsmetriken

In diesem Artikel erfahren Sie, wie Sie die API-Nutzungsmetriken für Ihr Azure Maps-Konto im [Azure-Portal](https://portal.azure.com) anzeigen. Die Metriken werden in einem benutzerfreundlichen Diagrammformat für einen anpassbaren Zeitraum angezeigt.

## <a name="view-metric-snapshot"></a>Anzeigen der Metrikenmomentaufnahme

Sie sehen einige gängige Metriken auf der **Übersicht**-Seite Ihres Maps-Kontos. Derzeit werden *Anforderungen gesamt*, *Fehler gesamt* und *Verfügbarkeit* für einen auswählbaren Zeitraum angezeigt.

![Übersicht zu Nutzungsmetriken von Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Fahren Sie mit dem nächsten Abschnitt fort, wenn Sie diese Diagramme für Ihre spezielle Analyse anpassen müssen.

## <a name="view-detailed-metrics"></a>Anzeigen detaillierter Metriken

1. Melden Sie sich bei Ihrem Azure-Abonnement im [Portal](https://portal.azure.com) an.

2. Klicken Sie auf der linken Seite auf die Menüoption **Alle Ressourcen**, und navigieren Sie zu Ihrem *Azure Maps-Konto*.

3. Sobald Ihr Maps-Konto geöffnet ist, klicken Sie auf der linken Seite auf das Menü **Metriken**.

4. Wählen Sie im Bereich **Metriken** eine der folgenden Optionen aus:

   1. **Verfügbarkeit**, womit der *Durchschnitt* der API-Verfügbarkeit über einen bestimmten Zeitraum angezeigt wird.
   2. **Nutzung**, die Ihnen die *Anzahl* der Nutzungen für Ihr Konto anzeigt.

      ![Bereich für Nutzungsmetriken von Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Als Nächstes können Sie den *Zeitbereich* wählen, indem Sie auf **Letzte 24 Stunden (automatisch)** klicken. Standardmäßig ist der Zeitraum auf 24 Stunden festgelegt. Nach dem Klicken werden alle auswählbaren Zeitbereiche angezeigt. Sie können auch die *Zeitgranularität* auswählen und in der gleichen Dropdownliste wählen, ob Sie die *lokale* oder *GMT*-Uhrzeit anzeigen möchten. Klicken Sie auf **Anwenden**.

    ![Zeitbereich für Metriken von Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Nachdem Sie die Metrik hinzugefügt haben, können Sie **Filter** für die Eigenschaften dieser Metrik hinzufügen. Wählen Sie dann den Wert der Eigenschaft aus, die im Diagramm angezeigt werden soll.

    ![Filter für Nutzungsmetriken von Azure Maps](media/how-to-view-api-usage/filter.png)

7. Sie können auch basierend auf Ihrer ausgewählten Metrikeigenschaft die **Aufteilung für Ihre Metrik anwenden**. Damit kann das Diagramm in mehrere Diagramme aufgeteilt werden – jeweils eines für jeden Wert dieser Eigenschaft. In der folgenden Abbildung entspricht die Farbe jedes Diagramms dem am unteren Rand des Diagramms angezeigten Eigenschaftswert.

    ![Aufteilung der Nutzungsmetriken von Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Um mehrere Metriken im gleichen Diagramm zu beobachten, klicken Sie einfach oben auf die Schaltfläche **Metrik hinzufügen**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Azure Maps-APIs, für die Sie die Nutzung verfolgen möchten:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK: Anleitung](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK: Anleitung](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Dokumentation zur REST API von Azure Maps](/rest/api/maps)