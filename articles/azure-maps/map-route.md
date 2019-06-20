---
title: Anzeigen von Wegbeschreibungen mit Azure Maps | Microsoft-Dokumentation
description: Anzeigen von Wegbeschreibungen zwischen zwei Positionen in einer JavaScript-Karte
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 31403a11e8989cee6a1c166879a0b2f594892704
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357631"
---
# <a name="show-directions-from-a-to-b"></a>Anzeigen einer Wegbeschreibung von A nach B

In diesem Artikel erfahren Sie, wie Sie eine Route anfordern und die Route auf der Karte anzeigen.

Dafür gibt es zwei Möglichkeiten. Die erste Möglichkeit besteht darin, die [Azure Maps-Routen-API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) über ein Dienstmodul abzufragen. Die zweite Möglichkeit besteht in der Verwendung der [Fetch-API](https://fetch.spec.whatwg.org/) für eine Suchanforderung an die [Routen-API von Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Beide Möglichkeiten werden im Folgenden erläutert.

## <a name="query-the-route-via-service-module"></a>Abfragen der Route über das Dienstmodul

<iframe height='500' scrolling='no' title='Anzeigen einer Wegbeschreibung von A nach B auf einer Karte (Dienstmodul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show directions from A to B on a map (Service Module)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt eine `SubscriptionKeyCredentialPolicy`, um HTTP-Anforderungen an Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)-Instanz. `routeURL` stellt eine URL zu [Routenvorgängen](https://docs.microsoft.com/rest/api/maps/route) von Azure Maps dar.

Mit dem dritten Codeblock wird ein [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Objekt erstellt und der Karte hinzugefügt.

Mit dem vierten Codeblock werden [points](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)-Objekte als Start- und Endpunkte erstellt und dem dataSource-Objekt hinzugefügt.

Eine Linie ist ein [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)-Objekt von LineString. Ein [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)-Objekt rendert Linienobjekte, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) umschlossen sind, als Linien in der Karte. Mit dem letzten Codeblock wird eine Linienebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Linienebene finden Sie unter [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind. Mit dem fünften Codeblock wird eine Symbolebene erstellt und der Karte hinzugefügt.

Mit dem sechsten Codeblock wird der Azure Maps-Routingdienst abgefragt, der Teil des [Dienstmoduls](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js) ist. Die [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods)-Methode von RouteURL wird genutzt, um eine Route zwischen den Start- und Endpunkten zu erhalten. Anschließend wird mit der `geojson.getFeatures()`-Methode eine GeoJSON-Merkmalsauswahl extrahiert und der Datenquelle hinzugefügt. Anschließend wird die Antwort als Route in der Karte gerendert. Weitere Informationen zum Hinzufügen einer Linie zur Karte finden Sie unter [Hinzufügen einer Linie zur Karte](./map-add-shape.md#addALine).

Mit dem letzten Codeblock werden die Grenzen der Karte mithilfe der [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-Eigenschaft der Karte festgelegt.

Die Routenabfrage, die Datenquelle, die Symbolebene, die Linienebenen und die Kameragrenzen werden im [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte erstellt und festgelegt, um sicherzustellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="query-the-route-via-fetch-api"></a>Abfragen der Route über die Fetch-API

<iframe height='500' scrolling='no' title='Anzeigen einer Wegbeschreibung von A nach B auf einer Karte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Anzeigen einer Wegbeschreibung von A nach B auf einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Mit dem zweiten Codeblock wird ein [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Objekt erstellt und der Karte hinzugefügt.

Mit dem dritten Codeblock werden die Start- und Zielpunkte für die Route erstellt und der Datenquelle hinzugefügt. Eine Anleitung zum Verwenden von [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) finden Sie unter [Hinzufügen von Stecknadeln zur Karte](map-add-pin.md).

Ein [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)-Objekt rendert Linienobjekte, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) umschlossen sind, als Linien in der Karte. Mit dem letzten Codeblock wird eine Linienebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Linienebene finden Sie unter [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind. Mit dem fünften Codeblock wird eine Symbolebene erstellt und der Karte hinzugefügt. Die Eigenschaften einer Symbolebene finden Sie unter [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Mit dem nächsten Codeblock werden die Punkte `SouthWest` und `NorthEast` für die Start- und Endpunkte erstellt und die Grenzen der Karte festgelegt, indem die [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-Eigenschaft der Karte verwendet wird.

Der letzte Codeblock verwendet die [Fetch-API](https://fetch.spec.whatwg.org/) für eine Suchanforderung an die [Routen-API von Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Die Antwort wird dann analysiert. Bei einer erfolgreichen Antwort wird anhand der Breiten- und Längengradinformationen ein Array mit Linien erstellt, indem diese Punkte verbunden werden. Die Liniendaten werden dann der Datenquelle hinzugefügt, um die Route in der Karte zu rendern. Eine Anleitung finden Sie unter [Hinzufügen einer Linie zur Karte](./map-add-shape.md#addALine).

Die Routenabfrage, die Datenquelle, die Symbolebene, die Linienebenen und die Kameragrenzen werden im [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte erstellt und festgelegt, um sicherzustellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Anzeigen von Datenverkehr auf einer Karte](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagieren mit der Karte – Mausereignisse](./map-events.md)
