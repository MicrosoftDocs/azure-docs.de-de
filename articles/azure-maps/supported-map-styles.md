---
title: Unterstützte integrierte Azure Maps-Kartenstile
description: Hier erfahren Sie mehr über die von Azure Maps unterstützten integrierten Kartenstile, etwa „road“, „blank_accessible“, „satellite“, „satellite_road_labels“, „road_shaded_relief“ und „night“.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896938"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>In Azure Maps unterstützte integrierte Kartenstile

Azure Maps unterstützt mehrere integrierte Kartenstile, die im Folgenden beschrieben werden.

## <a name="road"></a>Straße

Eine **Straßenkarte** ist eine Standardkarte, auf der Straßen angezeigt werden. Außerdem werden natürliche und künstliche Features sowie die Bezeichnungen für diese Features angezeigt.

![Kartenstil „Straße“](./media/supported-map-styles/road.png)

**Anwendbare APIs:**

* [Kartenbild](/rest/api/maps/render/getmapimage)
* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="blank-and-blank_accessible"></a>„blank“ und „blank_accessible“

Die Kartenstile **blank** und **blank_accessible** stellen einen leeren Zeichenbereich für die Visualisierung von Daten bereit. Der Stil **blank_accessible** wird weiterhin Updates für die Sprachausgabe mit Details zum Standort der Karte bereitstellen, obwohl die Basiskarte nicht angezeigt wird.

> [!Note]
> Im Web-SDK können Sie die Hintergrundfarbe der Karte ändern, indem Sie den CSS-Stil `background-color` des DIV-Kartenelements festlegen.

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement

## <a name="satellite"></a>Satellit

Der Kartenstil **Satellit** ist eine Kombination aus Satellitenbildern und Luftaufnahmen.

![Kartenstil „Satellitenkachel“](./media/supported-map-styles/satellite.png)

**Anwendbare APIs:**

* [Satellitenkachel](/rest/api/maps/render/getmapimagerytilepreview)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="satellite_road_labels"></a>Satellite_Road_Labels

Dieser Kartenstil ist eine Hybriddarstellung, bei der Straßen und Bezeichnungen Satellitenbilder und Luftaufnahmen überlagern.

![Kartenstil „satellite_road_labels“](./media/supported-map-styles/satellite-road-labels.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="grayscale_dark"></a>grayscale_dark

**grayscale_dark** ist eine dunkle Version des Kartenstils „Straße“.

![Kartenstil „gray_scale“](./media/supported-map-styles/grayscale-dark.png)

**Anwendbare APIs:**

* [Kartenbild](/rest/api/maps/render/getmapimage)
* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="grayscale_light"></a>grayscale_light

**grayscale_light** ist eine helle Version des Stils „Straßenkarte“.

![Kartenstil „grayscale_light“](./media/supported-map-styles/grayscale-light.png)

**Anwendbare APIs:**
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="night"></a>Nacht

**Nacht** ist eine dunkle Version des Kartenstils „Straße“ mit farbigen Straßen und Symbolen.

![Kartenstil „Nacht“](./media/supported-map-styles/night.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="road_shaded_relief"></a>road_shaded_relief

**road shaded relief** (Relief mit schattierten Straßen) ist eine Azure Maps-Hauptformatvorlage mit Konturen der Erde.

![Kartenstil „Schattiertes Relief“](./media/supported-map-styles/shaded-relief.png)

**Anwendbare APIs:**

* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** ist ein dunkler Kartenstil mit einem höheren Kontrast als bei anderen Stilen.

![Dunkler Kartenstil mit starkem Kontrast](./media/supported-map-styles/high-contrast-dark.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Power BI-Visualisierung

## <a name="next-steps"></a>Nächste Schritte

Informationen, wie ein Kartenstil in Azure Maps festgelegt wird:

[Auswählen eines Kartenstils](./choose-map-style.md)