---
title: Hinzufügen eines HTML-Markers zu Azure Maps | Microsoft-Dokumentation
description: So fügen Sie einen HTML-Marker zur Javascript-Karte hinzu
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769667"
---
# <a name="add-html-markers-to-the-map"></a>Hinzufügen von HTML-Markern zur Karte

In diesem Artikel erfahren Sie, wie Sie einen benutzerdefinierten HTML-Code, wie eine Bilddatei, zur Karte als HTML-Marker hinzufügen.

> [!NOTE]
> HTML-Marker sind nicht mit Datenquellen verbunden. Stattdessen werden Positionsinformationen direkt dem Marker hinzugefügt und der Marker wird der Karteneigenschaft `markers` hinzugefügt, die ein [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest) ist.

> [!IMPORTANT]
> Im Gegensatz zu den meisten Ebenen im Azure Maps-Websteuerelement, die WebGL zum Rendern verwenden, werden von HTML-Markern traditionelle DOM-Elemente zum Rendern verwendet. Je mehr HTML-Marker eine Seite hinzugefügt haben, desto mehr DOM-Elemente gibt es. Die Leistung kann nach dem Hinzufügen von ein paar hundert HTML-Markern nachlassen. Für größere Datensätze empfiehlt sich entweder ein Clustering der Daten oder die Verwendung einer Symbol- oder Blasenebene.

## <a name="add-an-html-marker"></a>Hinzufügen eines HTML-Markers

Die HtmlMarker-Klasse verfügt über ein Standardformat. Sie können die Marker anpassen, indem Sie die Farb- und Textoptionen des Markers festlegen. Das Standardformat der HtmlMarker-Klasse ist eine SVG-Vorlage mit einem Farb- und Textplatzhalter. Für eine schnelle Anpassung legen Sie die Farb- und Texteigenschaften in den HtmlMarker-Optionen fest. 

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen eines HTML-Markers zu einer Karte' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add an HTML Marker to a map</a> (Hinzufügen eines HTML-Markers zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock fügt der Karte mit der [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers)-Eigenschaft der [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Klasse ein [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)-Objekt hinzu. Das HtmlMarker-Objekt wird der Karte innerhalb der Funktion [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit es angezeigt wird, nachdem die Karte vollständig geladen wurde.

## <a name="create-svg-templated-html-marker"></a>Erstellen von auf SVG-Vorlagen basierenden HTML-Markern

Der Standardwert `htmlContent` eines HTML-Markers ist eine SVG-Vorlage mit den Platzierungsordnern `{color}` und `{text}`. Sie können benutzerdefinierte SVG-Zeichenketten erstellen und diese Platzhalter zu Ihren SVG hinzufügen, sodass die Einstellungen der Optionen `color` und `text` des Markers diese Platzhalter in Ihren SVG aktualisieren.

<br/>

<iframe height='500' scrolling='no' title='HTML-Marker mit benutzerdefinierter SVG-Vorlage' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML Marker with Custom SVG Template</a> (HTML-Marker mit benutzerdefinierter SVG-Vorlage) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Hinzufügen eines HTML-Markers mit Cascading Stylesheets (CSS)

Einer der Vorteile von HTML-Markern sind die zahlreichen hervorragenden Anpassungen, die mithilfe von CSS erreicht werden können. In diesem Beispiel besteht der Inhalt von „HtmlMarker“ aus HTML und CSS, die einen animierten Pin erzeugen, der an seinen Platz fällt und Impulse erzeugt.

<br/>

<iframe height='500' scrolling='no' title='DataSource „HTML“' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource „HTML“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Ziehbare HTML-Marker

Dieses Beispiel zeigt, wie Sie einen HTML-Marker ziehbar machen. HTML-Marker unterstützen `drag`, `dragstart`- und `dragend`-Ereignisse.

<br/>

<iframe height='500' scrolling='no' title='Ziehbarer HTML-Marker' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML Marker</a> (Ziehbarer HTML-Marker) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Hinzufügen von Mausereignissen zu HTML-Markern

Diese Beispiele zeigen, wie einem HTML-Marker Maus- und Ziehereignisse hinzugefügt werden können.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen von Mausereignissen zu HTML-Markern' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adding Mouse Events to HTML Markers</a> (Hinzufügen von Mausereignissen zu HTML-Markern) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](./map-add-bubble-layer.md)