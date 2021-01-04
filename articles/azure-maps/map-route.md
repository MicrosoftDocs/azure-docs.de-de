---
title: Anzeigen von Wegbeschreibungen auf einer Karte | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Microsoft Azure Maps Web SDK eine Wegbeschreibung zwischen zwei Orten auf einer Karte anzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890716"
---
# <a name="show-directions-from-a-to-b"></a>Anzeigen einer Wegbeschreibung von A nach B

In diesem Artikel erfahren Sie, wie Sie eine Route anfordern und die Route auf der Karte anzeigen.

Dafür gibt es zwei Möglichkeiten. Die erste Möglichkeit besteht darin, die [Azure Maps-Routen-API](/rest/api/maps/route/getroutedirections) über ein Dienstmodul abzufragen. Die zweite Möglichkeit besteht in der Verwendung der [Fetch-API](https://fetch.spec.whatwg.org/) für eine Suchanforderung an die [Routen-API von Azure Maps](/rest/api/maps/route/getroutedirections). Beide Möglichkeiten werden im Folgenden erläutert.

## <a name="query-the-route-via-service-module"></a>Abfragen der Route über das Dienstmodul

<iframe height='500' scrolling='no' title='Anzeigen einer Wegbeschreibung von A nach B auf einer Karte (Dienstmodul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show directions from A to B on a map (Service Module)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Block ein Kartenobjekt und legt als Authentifizierungsmechanismus die Verwendung des Zugriffstokens fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein `TokenCredential`-Element, um HTTP-Anforderungen an Azure Maps mit dem Zugriffstoken zu authentifizieren. Anschließend wird das `TokenCredential`-Element an `atlas.service.MapsURL.newPipeline()` übergeben, und es wird eine [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline)-Instanz erstellt. `routeURL` stellt eine URL zu [Routenvorgängen](/rest/api/maps/route) von Azure Maps dar.

Mit dem dritten Codeblock wird ein [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)-Objekt erstellt und der Karte hinzugefügt.

Mit dem vierten Codeblock werden [points](/javascript/api/azure-maps-control/atlas.data.point)-Objekte als Start- und Endpunkte erstellt und dem dataSource-Objekt hinzugefügt.

Eine Linie ist ein [Feature](/javascript/api/azure-maps-control/atlas.data.feature)-Objekt für LineString. Ein [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)-Objekt rendert Linienobjekte, die von [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) umschlossen sind, als Linien in der Karte. Mit dem letzten Codeblock wird eine Linienebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Linienebene finden Sie unter [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Eine [Symbolebene](/javascript/api/azure-maps-control/atlas.layer.symbollayer) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) umschlossen sind. Die Texte oder Symbole werden als Symbole auf der Karte gerendert. Mit dem fünften Codeblock wird eine Symbolebene erstellt und der Karte hinzugefügt.

Mit dem sechsten Codeblock wird der Azure Maps-Routingdienst abgefragt, der Teil des [Dienstmoduls](how-to-use-services-module.md) ist. Die [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods)-Methode von RouteURL wird genutzt, um eine Route zwischen den Start- und Endpunkten zu erhalten. Anschließend wird mit der `geojson.getFeatures()`-Methode eine GeoJSON-Merkmalsauswahl extrahiert und der Datenquelle hinzugefügt. Anschließend wird die Antwort als Route in der Karte gerendert. Weitere Informationen zum Hinzufügen einer Linie zur Karte finden Sie unter [Hinzufügen einer Linie zur Karte](map-add-line-layer.md).

Mit dem letzten Codeblock werden die Grenzen der Karte mithilfe der [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-Eigenschaft der Karte festgelegt.

Die Routenabfrage, die Datenquelle, das Symbol, die Linienebenen und die Kameragrenzen werden innerhalb des [Ereignislisteners](/javascript/api/azure-maps-control/atlas.map#events) erstellt. Diese Codestruktur stellt sicher, dass die Ergebnisse erst nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="query-the-route-via-fetch-api"></a>Abfragen der Route über die Fetch-API

<iframe height='500' scrolling='no' title='Anzeigen einer Wegbeschreibung von A nach B auf einer Karte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Anzeigen einer Wegbeschreibung von A nach B auf einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus das Zugriffstoken fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Mit dem zweiten Codeblock wird ein [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)-Objekt erstellt und der Karte hinzugefügt.

Mit dem dritten Codeblock werden die Start- und Zielpunkte für die Route erstellt. Anschließend werden sie der Datenquelle hinzugefügt. Eine Anleitung zum Verwenden von [addPins](/javascript/api/azure-maps-control/atlas.map) finden Sie unter [Hinzufügen von Stecknadeln zur Karte](map-add-pin.md).

Ein [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)-Objekt rendert Linienobjekte, die von [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) umschlossen sind, als Linien in der Karte. Mit dem letzten Codeblock wird eine Linienebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Linienebene finden Sie unter [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Eine [Symbolebene](/javascript/api/azure-maps-control/atlas.layer.symbollayer) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) als Symbole auf der Karte umschlossen sind. Mit dem fünften Codeblock wird eine Symbolebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Symbolebene finden Sie unter [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Mit dem nächsten Codeblock werden die Punkte `SouthWest` und `NorthEast` für die Start- und Endpunkte erstellt und die Grenzen der Karte festgelegt, indem die [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-Eigenschaft der Karte verwendet wird.

Der letzte Codeblock verwendet die [Fetch-API](https://fetch.spec.whatwg.org/) für eine Suchanforderung an die [Routen-API von Azure Maps](/rest/api/maps/route/getroutedirections). Die Antwort wird dann analysiert. Bei einer erfolgreichen Antwort wird anhand der Breiten- und Längengradinformationen ein Array mit Linien erstellt, indem diese Punkte verbunden werden. Die Liniendaten werden dann der Datenquelle hinzugefügt, um die Route in der Karte zu rendern. Eine Anleitung finden Sie unter [Hinzufügen einer Linie zur Karte](map-add-line-layer.md).

Die Routenabfrage, die Datenquelle, das Symbol, die Linienebenen und die Kameragrenzen werden innerhalb des [Ereignislisteners](/javascript/api/azure-maps-control/atlas.map#events) erstellt. Auch hier möchten wir sicherstellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für die Verwendung des Routingdienst](how-to-use-best-practices-for-search.md)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Anzeigen von Datenverkehr auf einer Karte](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagieren mit der Karte – Mausereignisse](./map-events.md)