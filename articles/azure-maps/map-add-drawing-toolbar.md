---
title: Hinzufügen einer Symbolleiste mit Zeichentools zu einer Karte | Microsoft Azure Maps
description: Hinzufügen einer Zeichnungssymbolleiste zu einer Karte mithilfe des Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896394"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Hinzufügen einer Symbolleiste mit Zeichentools zu einer Karte

In diesem Artikel erfahren Sie, wie Sie das Modul mit Zeichentools verwenden und die Zeichnungssymbolleiste auf der Karte anzeigen. Das Steuerelement [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) fügt die Zeichnungssymbolleiste auf der Karte hinzu. Erfahren Sie, wie Karten mit nur einem und mit allen Zeichnungstools erstellt werden und wie das Rendering der Zeichnungsformen im Zeichnungs-Manager angepasst wird.

## <a name="add-drawing-toolbar"></a>Hinzufügen einer Symbolleiste zum Zeichnen

Mit dem folgenden Code wird eine Instanz des Zeichnungs-Managers erstellt und die Symbolleiste auf der Karte angezeigt.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Nachstehend finden Sie das vollständige, ausführbare Codebeispiel für die oben erläuterte Funktionalität:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Hinzufügen einer Symbolleiste zum Zeichnen" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Add drawing toolbar</a> (Hinzufügen einer Zeichnungssymbolleiste) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Begrenzen der angezeigten Symbolleistenoptionen

Mit dem folgenden Code wird eine Instanz des Zeichnungs-Managers erstellt und die Symbolleiste mit nur einem Polygonzeichnungstool auf der Karte angezeigt. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Nachstehend finden Sie das vollständige, ausführbare Codebeispiel für die oben erläuterte Funktionalität:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Hinzufügen eines Polygonzeichnungstools" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Add a polygon drawing tool</a> (Hinzufügen eines Polygonzeichnungstools) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Ändern des Zeichnungsrenderingstils

Der Stil der gezeichneten Formen kann angepasst werden, indem mithilfe der `drawingManager.getLayers()`-Funktion die zugrundeliegenden Ebenen des Zeichnungs-Managers abgerufen und dann Optionen in den einzelnen Ebenen festgelegt werden. Die Ziehpunkte, die für Koordinaten beim Bearbeiten einer Form angezeigt werden, stellen HTML-Marker dar. Der Stil der Ziehpunkte kann angepasst werden, indem HTML-Markeroptionen an die Optionen `dragHandleStyle` und `secondaryDragHandleStyle` des Zeichnungs-Managers übergeben werden.  

Der folgende Code ruft die Renderingebenen aus dem Zeichnungs-Manager ab und ändert die zugehörigen Optionen, um den Renderingstil für das Zeichnen zu ändern. In diesem Fall werden die Punkte mit einem blauen Markersymbol gerendert. Die Linien werden rot und vier Pixel breit sein. Polygone haben eine grüne Füllfarbe und eine orangefarbene Kontur. Anschließend wird der Stil der Ziehpunkte in quadratische Symbole geändert. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Nachstehend finden Sie das vollständige, ausführbare Codebeispiel für die oben erläuterte Funktionalität:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ändern des Zeichnungsrenderingstils" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>Change drawing rendering style</a> (Ändern des Zeichnungsrenderingstils) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie die weiteren Funktionen des Zeichentools-Moduls verwendet werden:

> [!div class="nextstepaction"]
> [Abrufen von Formdaten](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagieren auf Zeichnungsereignisse](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstypen und Tastenkombinationen](drawing-tools-interactions-keyboard-shortcuts.md)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Zeichnungssymbolleiste](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Zeichnungs-Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)