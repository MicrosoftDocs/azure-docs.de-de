---
title: Hinzufügen eines HTML-Markers zu einer Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie HTML-Marker zu Karten hinzufügen. Sehen Sie, wie Sie das Azure Maps Web SDK zum Anpassen von Markern und Hinzufügen von Popups und Mausereignissen zu einem Marker verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 1c4367e2a649f4e239e2dab374afc4fb867e517b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891192"
---
# <a name="add-html-markers-to-the-map"></a>Hinzufügen von HTML-Markern zur Karte

In diesem Artikel erfahren Sie, wie Sie einen benutzerdefinierten HTML-Code, wie eine Bilddatei, zur Karte als HTML-Marker hinzufügen.

> [!NOTE]
> HTML-Marker sind nicht mit Datenquellen verbunden. Stattdessen werden Positionsinformationen direkt dem Marker hinzugefügt und der Marker wird der Karteneigenschaft `markers` hinzugefügt, die ein [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager) ist.

> [!IMPORTANT]
> Im Gegensatz zu den meisten Ebenen im Azure Maps-Websteuerelement, die WebGL zum Rendern verwenden, werden von HTML-Markern traditionelle DOM-Elemente zum Rendern verwendet. Je mehr HTML-Marker zu einer Seite hinzugefügt werden, desto mehr DOM-Elemente gibt es. Die Leistung kann nach dem Hinzufügen von ein paar hundert HTML-Markern nachlassen. Für größere Datensätze empfiehlt sich entweder ein Clustering der Daten oder die Verwendung einer Symbol- oder Blasenebene.

## <a name="add-an-html-marker"></a>Hinzufügen eines HTML-Markers

Die [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)-Klasse verfügt über ein Standardformat. Sie können die Marker anpassen, indem Sie die Farb- und Textoptionen des Markers festlegen. Das Standardformat der HtmlMarker-Klasse ist eine SVG-Vorlage mit einem `{color}`- und `{text}`-Platzhalter. Für eine schnelle Anpassung legen Sie die Farb- und Texteigenschaften in den HtmlMarker-Optionen fest. 

Der folgende Code erstellt einen HTML-Marker und setzt die Farbeigenschaft auf „DodgerBlue“ und die Texteigenschaft auf „10“. Ein Popup wird an den Marker angefügt, und das `click`-Ereignis wird verwendet, um die Sichtbarkeit des Popups umzuschalten.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen eines HTML-Markers zu einer Karte' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add an HTML Marker to a map</a> (Hinzufügen eines HTML-Markers zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Erstellen von auf SVG-Vorlagen basierenden HTML-Markern

Der Standardwert `htmlContent` eines HTML-Markers ist eine SVG-Vorlage mit den Platzierungsordnern `{color}` und `{text}`. Sie können benutzerdefinierte SVG-Zeichenketten erstellen und diese Platzhalter zu Ihren SVG hinzufügen, sodass die Einstellungen der Optionen `color` und `text` des Markers diese Platzhalter in Ihren SVG aktualisieren.

<br/>

<iframe height='500' scrolling='no' title='HTML-Marker mit benutzerdefinierter SVG-Vorlage' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML Marker with Custom SVG Template</a> (HTML-Marker mit benutzerdefinierter SVG-Vorlage) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Das Azure Maps Web SDK bietet mehrere SVG-Bildvorlagen, die mit HTML-Markern verwendet werden können. Weitere Informationen finden Sie im Dokument [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md).

## <a name="add-a-css-styled-html-marker"></a>Hinzufügen eines HTML-Markers mit Cascading Stylesheets (CSS)

Einer der Vorteile von HTML-Markern sind die zahlreichen hervorragenden Anpassungen, die mithilfe von CSS erreicht werden können. In diesem Beispiel besteht der Inhalt von „HtmlMarker“ aus HTML und CSS, die einen animierten Pin erzeugen, der an seinen Platz fällt und Impulse erzeugt.

<br/>

<iframe height='500' scrolling='no' title='DataSource „HTML“' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource „HTML“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Ziehbare HTML-Marker

Dieses Beispiel zeigt, wie Sie einen HTML-Marker ziehbar machen. HTML-Marker unterstützen `drag`-, `dragstart`- und `dragend`-Ereignisse.

<br/>

<iframe height='500' scrolling='no' title='Ziehbarer HTML-Marker' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML Marker</a> (Ziehbarer HTML-Marker) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Hinzufügen von Mausereignissen zu HTML-Markern

Diese Beispiele zeigen, wie einem HTML-Marker Maus- und Ziehereignisse hinzugefügt werden können.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen von Mausereignissen zu HTML-Markern' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adding Mouse Events to HTML Markers</a> (Hinzufügen von Mausereignissen zu HTML-Markern) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](./map-add-bubble-layer.md)