---
title: Anzeigen von Suchergebnissen mit Azure Maps | Microsoft-Dokumentation
description: So führen Sie eine Suchanfrage mit Azure Maps aus und zeigen dann die Ergebnisse auf einer Javascript-Karte an
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 85e15b756d2ea241296e11ebfcb5bf3029143de7
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357783"
---
# <a name="show-search-results-on-the-map"></a>Anzeigen von Suchergebnissen auf der Karte

In diesem Artikel erfahren Sie, wie nach einem bestimmten Ort gesucht wird und die Suchergebnisse auf der Karte angezeigt werden.

Es gibt zwei Möglichkeiten, um nach einem bestimmten Ort zu suchen. Eine Möglichkeit besteht darin, über ein Dienstmodul eine Suchanforderung zu stellen. Die andere Möglichkeit ist, eine Suchanforderung über die [Fetch-API](https://fetch.spec.whatwg.org/) an die [Azure Maps-API für die Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) zu stellen. Beide Möglichkeiten werden im Folgenden erläutert.

## <a name="make-a-search-request-via-service-module"></a>Durchführen einer Suchanforderung über das Dienstmodul

<iframe height='500' scrolling='no' title='Anzeigen von Suchergebnissen auf einer Karte (Dienstmodul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Siehe <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Anzeigen von Suchergebnissen auf einer Karte (Dienstmodul)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt eine `SubscriptionKeyCredentialPolicy`, um HTTP-Anforderungen an Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet dann die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)-Instanz. `searchURL` stellt eine URL zu [Suchvorgängen](https://docs.microsoft.com/rest/api/maps/search) von Azure Maps dar.

Der dritte Codeblock erstellt mit der [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Klasse ein Datenquellenobjekt und fügt diesem Suchergebnisse hinzu. Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind.  Anschließend wird eine Symbolebene erstellt, und die Datenquelle wird der Symbolebene hinzugefügt, die dann der Karte hinzugefügt wird.

Der vierte Codeblock verwendet die [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)-Methode im [Dienstmodul](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Sie ermöglicht über die [REST-API „Get Search“ für die Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) die Freitextsuche nach Points of Interest. Die REST-API „Get Search“ für die Fuzzysuche kann eine beliebige Kombination von Fuzzyeingaben verarbeiten. Eine GeoJSON-Funktionssammlung aus der Antwort wird dann mit der `geojson.getFeatures()`-Methode extrahiert und der Datenquelle hinzugefügt, und die Daten werden über die Symbolebene automatisch auf der Karte gerendert.

Im letzten Codeblock werden mithilfe der Maps-Eigenschaft [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) die Kameragrenzen für die Karte angepasst.

Die Suchanforderung, die Datenquelle, die Symbolebene und die Kameragrenzen werden im bereiten [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte erstellt und festgelegt, um sicherzustellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.


## <a name="make-a-search-request-via-fetch-api"></a>Durchführen einer Suchanforderung über die Fetch-API

<iframe height='500' scrolling='no' title='Anzeigen von Suchergebnissen auf einer Karte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Anzeigen von Suchergebnissen auf einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt eine URL, an die eine Suchanforderung gerichtet werden kann. Außerdem werden zwei Arrays erstellt, um Grenzen und Markierungen für Suchergebnisse zu speichern.

Der dritte Codeblock verwendet die [Fetch-API](https://fetch.spec.whatwg.org/), um eine Anforderung an die [Azure Maps-API für die Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) zu stellen, um nach den Points of Interest zu suchen. Die API für die Fuzzysuche kann eine beliebige Kombination von Fuzzyeingaben verarbeiten. Anschließend verarbeitet und analysiert sie die Suchantwort und fügt die Ergebnismarkierungen dem searchPins-Array hinzu.

Der vierte Codeblock erstellt mit der [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Klasse ein Datenquellenobjekt und fügt diesem Suchergebnisse hinzu. Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind. Anschließend wird eine Symbolebene erstellt, und die Datenquelle wird der Symbolebene hinzugefügt, die dann der Karte hinzugefügt wird.

Der letzte Codeblock erstellt anhand des Ergebnisarrays ein [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest)-Objekt und passt dann die Kameragrenzen für die Karte mithilfe der Maps-Eigenschaft [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) an. Anschließend werden die Ergebnismarkierungen gerendert.

Die Suchanforderung, die Datenquelle, die Symbolebene und die Kameragrenzen werden im [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte festgelegt, um sicherzustellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur **Fuzzysucheuche**:

> [!div class="nextstepaction"]
> [Azure Maps-API für die Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Abrufen von Informationen von einer Koordinate](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](./map-route.md)
