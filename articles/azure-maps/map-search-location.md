---
title: Anzeigen von Suchergebnissen auf einer Karte | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mit dem Microsoft Azure Maps Web SDK eine Suchanfrage ausführen und die Ergebnisse auf der Karte anzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895272"
---
# <a name="show-search-results-on-the-map"></a>Anzeigen von Suchergebnissen auf der Karte

In diesem Artikel erfahren Sie, wie nach einem bestimmten Ort gesucht wird und die Suchergebnisse auf der Karte angezeigt werden.

Es gibt zwei Möglichkeiten, um nach einem bestimmten Ort zu suchen. Eine Möglichkeit besteht darin, über ein Dienstmodul eine Suchanforderung zu stellen. Die andere Möglichkeit ist, eine Suchanforderung über die [Fetch-API](https://fetch.spec.whatwg.org/) an die [Azure Maps-API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) zu stellen. Beide Möglichkeiten werden im Folgenden erläutert.

## <a name="make-a-search-request-via-service-module"></a>Durchführen einer Suchanforderung über das Dienstmodul

<iframe height='500' scrolling='no' title='Anzeigen von Suchergebnissen auf einer Karte (Dienstmodul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Siehe <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Anzeigen von Suchergebnissen auf einer Karte (Dienstmodul)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Block ein Kartenobjekt und legt als Authentifizierungsmechanismus die Verwendung des Zugriffstokens fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein `TokenCredential`-Element, um HTTP-Anforderungen an Azure Maps mit dem Zugriffstoken zu authentifizieren. Anschließend wird das `TokenCredential`-Element an `atlas.service.MapsURL.newPipeline()` übergeben, und es wird eine [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline)-Instanz erstellt. `searchURL` stellt eine URL zu [Suchvorgängen](/rest/api/maps/search) von Azure Maps dar.

Der dritte Codeblock erstellt mit der [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)-Klasse ein Datenquellenobjekt und fügt diesem Suchergebnisse hinzu. Eine [Symbolebene](/javascript/api/azure-maps-control/atlas.layer.symbollayer) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) als Symbole auf der Karte umschlossen sind.  Anschließend wird eine Symbolebene erstellt. Die Datenquelle wird der Symbolebene hinzugefügt, die dann der Karte hinzugefügt wird.

Der vierte Codeblock verwendet die [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)-Methode im [Dienstmodul](how-to-use-services-module.md). Sie ermöglicht über die [REST-API „Get Search“ für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) die Freitextsuche nach Points of Interest. Abrufanforderungen an die API „Search Fuzzy“ (Fuzzysuche) können jede Kombination von Fuzzyeingaben verarbeiten. Eine GeoJSON-Funktionssammlung aus der Antwort wird dann mit der `geojson.getFeatures()`-Methode extrahiert und der Datenquelle hinzugefügt, und die Daten werden über die Symbolebene automatisch auf der Karte gerendert.

Im letzten Codeblock werden mithilfe der Maps-Eigenschaft [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) die Kameragrenzen für die Karte angepasst.

Die Suchanforderung, die Datenquelle, die Symbolebene und die Kameragrenzen befinden sich innerhalb des [Ereignislisteners](/javascript/api/azure-maps-control/atlas.map#events) der Karte. Wir möchten sicherstellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.


## <a name="make-a-search-request-via-fetch-api"></a>Durchführen einer Suchanforderung über die Fetch-API

<iframe height='500' scrolling='no' title='Anzeigen von Suchergebnissen auf einer Karte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Anzeigen von Suchergebnissen auf einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Es legt den Authentifizierungsmechanismus für die Verwendung des Zugriffstokens fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt eine URL, an die eine Suchanforderung gerichtet werden kann. Außerdem werden zwei Arrays erstellt, um Grenzen und Markierungen für Suchergebnisse zu speichern.

Der dritte Codeblock verwendet die [Fetch-API](https://fetch.spec.whatwg.org/). Mit der [Fetch-API](https://fetch.spec.whatwg.org/) wird eine Anforderung an die [Azure Maps-API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) gestellt, um nach den Points of Interest zu suchen. Die API für die Fuzzysuche kann eine beliebige Kombination von Fuzzyeingaben verarbeiten. Anschließend verarbeitet und analysiert sie die Suchantwort und fügt die Ergebnismarkierungen dem searchPins-Array hinzu.

Im vierten Codeblock wird mit der [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)-Klasse ein Datenquellenobjekt erstellt. Im Code fügen wir Suchergebnisse zum Quellobjekt hinzu. Eine [Symbolebene](/javascript/api/azure-maps-control/atlas.layer.symbollayer) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) als Symbole auf der Karte umschlossen sind. Anschließend wird eine Symbolebene erstellt. Die Datenquelle wird der Symbolebene hinzugefügt, die dann der Karte hinzugefügt wird.

Der letzte Codeblock erstellt ein [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox)-Objekt. Es verwendet das Array der Ergebnisse und passt dann die Kameragrenzen für die Karte mithilfe der [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-Eigenschaft der Karte an. Anschließend werden die Ergebnismarkierungen gerendert.

Die Suchanforderung, die Datenquelle, die Symbolebene und die Kameragrenzen werden im [Ereignislistener](/javascript/api/azure-maps-control/atlas.map#events) der Karte festgelegt, um sicherzustellen, dass die Ergebnisse nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für die Verwendung des Suchdiensts](how-to-use-best-practices-for-search.md)

Weitere Informationen zur **Fuzzysucheuche** :

> [!div class="nextstepaction"]
> [Azure Maps-API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Abrufen von Informationen von einer Koordinate](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](map-route.md)