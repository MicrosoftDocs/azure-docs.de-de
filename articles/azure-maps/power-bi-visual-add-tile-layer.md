---
title: Hinzufügen einer Kachelebene zum Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie die Kachelebene im Microsoft Azure Maps-Visual für Power BI verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896207"
---
# <a name="add-a-tile-layer"></a>Hinzufügen einer Kachelebene

Mit dem Kachelebenenfeature können Sie, wie beim Referenzebenenfeature, zusätzliche Daten über die Karte legen, um mehr Kontext bereitzustellen. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Dies ist eine großartige Möglichkeit, große oder komplexe Datasets, z. B. Bilder von Drohnen oder Millionen von Datenzeilen, über Karten zu legen.

> [!div class="mx-imgBorder"]
> ![Karte mit einer Blasenebene über einer Kachelebene mit aktuellen Infrarotwetterdaten aus Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Kachelebenen werden in Kacheln von einem Server geladen. Diese Bilder können entweder vorab oder dynamisch gerendert werden. Vorab gerenderte Bilder werden wie jedes andere Bild auf einem Server gespeichert, wobei eine Namenskonvention verwendet wird, die von der Kachelebene verstanden wird. Dynamisch gerenderte Bilder verwenden einen Dienst, um die Bilder nahezu in Echtzeit zu laden. Kachelebenen sind eine gute Möglichkeit, große Datasets auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden.

Der Begrenzungsrahmen und der Zoombereich, innerhalb derer ein Kacheldienst verfügbar ist, können als einschränkende Einstellungen beim Anfordern von Kacheln übergeben werden. Dies führt sowohl für das Visual als auch den Kacheldienst zu einer Leistungsverbesserung. Im Folgenden sehen Sie eine Übersicht über alle Einstellungen, die im Bereich **Format** im Abschnitt **Kachelebene** verfügbar sind.

| Einstellung        | BESCHREIBUNG   |
|----------------|---------------|
| url            | Eine formatierte URL, die auf einen Kacheldienst verweist  |
| Tile size (Kachelgröße)      | Ein ganzzahliger Wert, der sowohl die Breite als auch die Höhe der Kacheln angibt   |
| North bound (Nördliche Grenze)    | Der nördliche Breitengrad des Begrenzungsrahmens, in dem Kacheln verfügbar sind |
| South bound (Südliche Grenze)    | Der südliche Breitengrad des Begrenzungsrahmens, in dem Kacheln verfügbar sind |
| East bound (Östliche Grenze)     | Der östliche Längengrad des Begrenzungsrahmens, in dem Kacheln verfügbar sind  |
| West bound (Westliche Grenze)     | Der westliche Längengrad des Begrenzungsrahmens, in dem Kacheln verfügbar sind   |
| Transparenz   | Transparenz der Kachelebene   |
| Is TMS (Ist TMS)         | Tile Map Services, eine Spezifikation, die die Y-Koordinatenachse der Kachelebene umkehrt |
| Min zoom (Minimalzoom)       | Minimaler Zoomfaktor, bei dem Kacheln verfügbar sind |
| Max zoom (Maximalzoom)       | Maximaler Zoomfaktor, bei dem Kacheln verfügbar sind  |
| Layer position (Ebenenposition) | Gibt die relative Position der Ebene in Bezug auf andere Kartenebenen an |

## <a name="tile-url-formatting"></a>Formatierung der Kachel-URL

Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die vom Azure Maps-Visual unterstützt werden:

-   **X, Y, Zoomnotation:** X ist die Spalten- und Y die Zeilenposition der Kachel im Kachelraster, und die Zoomnotation ist ein Wert, der auf dem Zoomfaktor basiert.
-   **Quadkey-Notation:** Bei dieser Notation werden X-, Y- und Zoominformationen in einem einzelnen Zeichenfolgenwert kombiniert. Dieser Zeichenfolgenwert wird zu einem eindeutigen Bezeichner für eine einzelne Kachel.
-   **Begrenzungsrahmen:** Geben Sie ein Bild im Koordinatenformat des Begrenzungsrahmens an: `{west},{south},{east},{north}`. Dieses Format wird häufig von [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms) verwendet.

Die Kachel-URL ist eine HTTPS-URL zu einer Kachel-URL-Vorlage, in der die folgenden Parameter verwendet werden:

-   `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
-   `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
-   `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
-   `{quadkey}`: `quadkey`-Bezeichner der Kachel basierend auf der Namenskonvention des Bing Maps-Kachelsystems
-   `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.

Das folgende Beispiel zeigt eine formatierte Kachel-URL für den [Wetterradar-Kacheldienst](/rest/api/maps/renderv2/getmaptilepreview) in Azure Maps. Beachten Sie, dass `[subscription-key]` ein Platzhalter für Ihren Azure Maps-Abonnementschlüssel ist.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Weitere Informationen zum Azure Maps-Kachelsystem finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Anzeigen von Echtzeit-Datenverkehr](power-bi-visual-show-real-time-traffic.md)