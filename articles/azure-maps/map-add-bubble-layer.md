---
title: Hinzufügen einer Blasenebene zu Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Blasenebene zur JavaScript-Karte
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769684"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Hinzufügen einer Blasenebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie Punktdaten aus einer Datenquelle als Blasenebene auf einer Karte rendern können. Blasenebenen rendern Punkte als Kreise mit festem Pixelradius auf der Karte. 

> [!TIP]
> Blasenebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'Point']` oder `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest, um die Ebene dahingehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden, wenn auch MultiPoint-Funktionen berücksichtigt werden sollen.

## <a name="add-a-bubble-layer"></a>Hinzufügen einer Blasenebene

<iframe height='500' scrolling='no' title='DataSource „BubbleLayer“' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>DataSource „BubbleLayer“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird ein Array von [Punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)-Objekten definiert und zum [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Objekt hinzugefügt.

Eine [Blasenebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) rendert punktbasierte Daten, die von der [Datenquelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) umschlossen sind, als Kreise auf der Karte. Der letzte Codeblock erstellt eine Blasenebene und fügt sie der Karte hinzu. Die Eigenschaften einer Blasenebene finden Sie unter [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Das Array von Punktobjekten, die Datenquelle und die Blasenebene werden erstellt und in der Funktion [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zur Karte hinzugefügt, um sicherzustellen, dass nach dem vollständigen Laden der Karte der Kreis angezeigt wird.

## <a name="show-labels-with-a-bubble-layer"></a>Anzeigen von Bezeichnungen bei einer Blasenebene

<iframe height='500' scrolling='no' title='DataSource „MultiLayer“' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>DataSource „MultiLayer“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Der obige Code zeigt, wie Sie Daten auf der Karte visualisieren und beschriften können. Der erste Block des obigen Codes erstellt ein Kartenobjekt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)-Objekt. Dann erstellt er ein Datenquellenobjekt mithilfe der [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Klasse und fügt den Punkt der Datenquelle hinzu.

Eine [Blasenebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) rendert punktbasierte Daten, die von der [Datenquelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) umschlossen sind, als Kreise auf der Karte. Der dritte Codeblock erstellt eine Blasenebene und fügt sie der Karte hinzu. Die Eigenschaften einer Blasenebene finden Sie unter [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind. Der letzte Codeblock erstellt eine Symbolebene und fügt sie der Karte hinzu, die die Beschriftung für die Blase rendert. Die Eigenschaften einer Symbolebene finden Sie unter [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Die Datenquelle und die Ebenen werden erstellt und in der Funktion [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zur Karte hinzugefügt, um sicherzustellen, dass die Daten nach dem vollständigen Laden der Karte angezeigt werden.

## <a name="customize-a-bubble-layer"></a>Anpassen einer Blasenebene

Für die Blasenebene gibt es nur einige wenige Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Optionen für Blasenebenen' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Beachten Sie den Stift <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Bubble Layer Options (Optionen für Blasenebenen)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) unter <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](map-add-pin.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)