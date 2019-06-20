---
title: Hinzufügen einer Symbolebene zu Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Symbolebene zur JavaScript-Karte
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2580f1177bf9e6e3a92934f88a5d8ab51894e8d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771667"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Hinzufügen einer Symbolebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie Punktdaten aus einer Datenquelle als Symbolebene auf einer Karte rendern können. Symbolebenen werden mit WebGL gerendert und unterstützen eine deutlich größere Anzahl von Datenpunkten als HTML-Marker. Sie unterstützen jedoch keine herkömmlichen CSS- und HTML-Elemente für die Formatierung.  

> [!TIP]
> Symbolebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'Point']` oder `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest, um die Ebene dahin gehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden, wenn auch MultiPoint-Funktionen berücksichtigt werden sollen.

## <a name="add-a-symbol-layer"></a>Hinzufügen einer Symbolebene

<iframe height='500' scrolling='no' title='Ändern der Stecknadel eines Standorts' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Ändern der Stecknadel eines Standorts</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des obigen Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Ein [Feature] mit einer [Punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)geometrie wird von der [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)-Klasse umschlossen, um die Aktualisierung zu vereinfachen, und wird anschließend erstellt und zur Datenquelle hinzugefügt.

Der dritte Codeblock erstellt einen [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) und aktualisiert die Koordinaten des Punktes bei Mausklick mit der [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)-Methode der Shape-Klasse.

Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind.  Die Datenquelle, der Klickereignislistener und die Symbolebene werden erstellt und der Karte innerhalb der [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)-Funktion `ready` hinzugefügt, damit der Punkt angezeigt wird, nachdem die Karte vollständig geladen wurde und auf sie zugegriffen werden kann.

> [!TIP]
> Standardmäßig optimieren Symbolebenen aus Leistungsgründen das Rendering von Symbolen, indem sie überlappende Symbole ausblenden. Beim Zoomen werden die ausgeblendeten Symbole angezeigt. Um dieses Feature zu deaktivieren und alle Symbole jederzeit anzuzeigen, legen Sie die Eigenschaft `allowOverlap` der `iconOptions`-Optionen auf `true` fest.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Hinzufügen eines benutzerdefinierten Zeichens zu einer Symbolebene

Symbolebenen werden mit WebGL gerendert. Daher müssen alle Ressourcen wie etwa Zeichenbilder in den WebGL-Kontext geladen werden. Dieses Beispiel zeigt, wie ein benutzerdefiniertes Symbol zu den Kartenressourcen hinzugefügt und anschließend zum Rendern von Punktdaten mit einem benutzerdefinierten Symbol auf der Karte verwendet wird. Die `textField`-Eigenschaft der Symbolebene erfordert, dass ein Ausdruck angegeben wird. In diesem Fall soll die Temperatureigenschaft gerendert werden. Da es sich aber um eine Zahl handelt, muss sie in eine Zeichenfolge konvertiert werden. Außerdem soll „°F“ angefügt werden. Dies ist mit folgendem Ausdruck möglich: `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Benutzerdefiniertes Symbolbildzeichen' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Benutzerdefiniertes Symbolbildzeichen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Anpassen einer Symbolebene 

Die Symbolebene bietet zahlreiche Optionen für die Formatierung. Im Folgenden finden Sie ein Tool, mit dem Sie diese verschiedenen Formatoptionen testen können.

<br/>

<iframe height='700' scrolling='no' title='Symbolebenenoptionen' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Symbolebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen eines Popups](map-add-popup.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Form](map-add-shape.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen von HTML-Markern](map-add-bubble-layer.md)
