---
title: Hinzufügen einer OGC-Kartenebene (Open Geospatial Consortium) | Microsoft Azure Maps
description: Erfahren Sie, wie Sie eine OGC-Kartenebene auf der Karte überlagern und die verschiedenen Optionen in der OgcMapLayer-Klasse nutzen können.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891664"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Hinzufügen einer Kartenebene aus Open Geospatial Consortium (OGC)

Die Klasse `atlas.layer.OgcMapLayer` kann WMS-Aufnahmen (Web Map Services) und WMTS-Aufnahmen (Web Map Tile Services) auf der Karte überlagern. WMS ist ein von OGC entwickeltes Standardprotokoll für die Bereitstellung georeferenzierter Kartenbilder im Internet. Die Georeferenzierung von Bildern ist der Prozess der Zuordnung eines Bilds zu einem geografischen Ort. WMTS ist auch ein von OGC entwickeltes Standardprotokoll. Es ist für die Bereitstellung vorgerenderter und georeferenzierter Kartenkacheln konzipiert.

In den folgenden Abschnitten werden die Web Map Service-Funktionen beschrieben, die von der Klasse `OgcMapLayer` unterstützt werden.

**Web Map Service (WMS)**

- Unterstützte Versionen: `1.0.0`, `1.1.0`, `1.1.1` und `1.3.0`
- Der Dienst muss das `EPSG:3857`-Projektionssystem unterstützen oder in der Lage sein, Neuprojektionen durchzuführen.
- GetFeatureInfo erfordert, dass der Dienst `EPSG:4326` unterstützt oder Neuprojektionen durchführt. 
- Unterstützte Vorgänge:

    | Vorgang | BESCHREIBUNG |
    | :-- | :-- |
    | GetCapabilities | Ruft Metadaten zum Dienst mit den unterstützten Funktionen ab |
    | GetMap | Ruft ein Kartenbild für eine angegebene Region ab |
    | GetFeatureInfo | Ruft `feature_info` ab, worin sich die zugrunde liegende Daten zum Feature befinden |

**Web Map Tile Service (WMTS)**

- Unterstützte Versionen: `1.0.0`
- Kacheln müssen quadratisch sein, z. B. `TileWidth == TileHeight`.
- Unterstützte CRS: `EPSG:3857` oder `GoogleMapsCompatible` 
- Der Bezeichner TileMatrix muss ein ganzzahliger Wert sein, der einer Zoomstufe auf der Karte entspricht. Auf einer Azure-Karte ist die Zoomstufe ein Wert von `"0"` bis `"22"`. Daher wird `"0"` unterstützt, `"00"` jedoch nicht.
- Unterstützte Vorgänge:

    | Vorgang | BESCHREIBUNG |
    | :-- | :-- |
    | GetCapabilities | Ruft die unterstützten Vorgänge und Features ab |
    | GetTile | Ruft Aufnahmen für eine bestimmte Kachel ab |

## <a name="overlay-an-ogc-map-layer"></a>Überlagern einer OGC-Kartenebene

`url` kann die Basis-URL für den Dienst oder eine vollständige URL mit der Abfrage zum Abrufen der Fähigkeiten des Diensts sein. Je nach den angegebenen Details kann der WFS-Client verschiedene URL-Standardformate ausprobieren, um zu bestimmen, wie der erstmalige Zugriff auf den Dienst erfolgen soll.

Der folgende Code zeigt, wie eine OGC-Kartenebene auf der Karte überlagert werden kann.

<br/>

<iframe height='700' scrolling='no' title='OGC Map layer example' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC Map layer example</a> (Beispiel einer OGC-Kartenebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Optionen für eine OGC-Kartenebene

Im folgenden Beispiel werden die verschiedenen Optionen für die OGC-Kartenebene veranschaulicht. Sie können rechts oben auf die Schaltfläche CodePen klicken, um den CodePen zu bearbeiten.

<br/>

<iframe height='700' scrolling='no' title='OGC map layer options' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC map layer options</a> (Optionen für die OGC-Kartenebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web Map Service explorer

Das folgende Tool überlagert Aufnahmen aus Web Mapping Services (WMS) und Web Map Tile Services (WMTS) als Ebenen. Sie können auswählen, welche Ebenen des Diensts auf der Karte gerendert werden. Sie können auch die zugehörigen Legenden für diese Ebenen anzeigen.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web Map Service explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web Map Service explorer</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Sie können auch die Karteneinstellungen so angeben, dass ein Proxydienst verwendet wird. Mit dem Proxydienst können Sie Ressourcen laden, die in Domänen gehostet werden, in denen CORS nicht aktiviert sind.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einem WFS-Dienst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Nutzen von Kernvorgängen](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)