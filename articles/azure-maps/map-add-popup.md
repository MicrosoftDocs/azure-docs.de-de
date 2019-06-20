---
title: Hinzufügen eines Popupfensters mit Azure Maps | Microsoft-Dokumentation
description: Hinzufügen eines Popupfensters zu einer JavaScript-Karte
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769548"
---
# <a name="add-a-popup-to-the-map"></a>Hinzufügen eines Popupfensters zu der Karte

In diesem Artikel wird gezeigt, wie Sie ein Popupfenster einem Punkt auf einer Karte hinzufügen.

## <a name="understand-the-code"></a>Grundlegendes zum Code

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Hinzufügen eines Popupfensters mit Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sehen Sie sich bei <a href='https://codepen.io'>CodePen</a> die Informationen unter <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Hinzufügen eines Popupfensters mit Azure Maps) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) an.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md). Außerdem wird HTML-Inhalt erstellt, der im Popupfenster angezeigt wird.

Im zweiten Codeblock wird mit der [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-Klasse ein Datenquellenobjekt erstellt. Ein Punkt ist ein [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) der [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)-Klasse. Anschließend wird ein Punktobjekt mit einem Namen und Beschreibungseigenschaften erstellt und der Datenquelle hinzugefügt.

Eine [Symbolebene](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) als Symbole auf der Karte umschlossen sind.  Im dritten Codeblock wird eine Symbolebene erstellt. Die Datenquelle wird der Symbolebene hinzugefügt, die dann der Karte hinzugefügt wird.

Der vierte Codeblock erstellt ein [Popupobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) über `new atlas.Popup()`. Popupeigenschaften, z.B. „position“ und „pixelOffset“ sind Teil von [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). „PopupOptions“ können Sie im Popupkonstruktor oder über die Funktion [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) der Popup-Klasse definieren. Anschließend wird ein `mouseover`-Ereignislistener für die Symbolebene erstellt.

Mit dem letzten Codeblock wird eine Funktion erstellt, die mit dem `mouseover`-Ereignislistener ausgelöst wird. Damit werden der Inhalt und die Eigenschaften des Popupfensters festgelegt, und das Popupobjekt wird der Karte hinzugefügt.

## <a name="reusing-a-popup-with-multiple-points"></a>Wiederverwenden eines Popups für mehrere Punkte

Wenn Sie viele Punkte verwenden und immer nur ein Popup anzeigen möchten, sollten Sie am besten ein Popup erstellen und wiederverwenden, anstatt für jedes Punktfeature ein Popup zu erstellen. Auf diese Weise wird die Anzahl der von der Anwendung erstellten DOM-Elemente erheblich reduziert, wodurch eine bessere Leistung erzielt werden kann. In diesem Beispiel werden drei Punktfeatures erstellt. Wenn Sie auf eines davon klicken, wird ein Popup mit dem Inhalt für dieses Punktfeature angezeigt.

<br/>

<iframe height='500' scrolling='no' title='Wiederverwenden eines Popups für mehrere Stecknadeln' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Informationen hierzu finden Sie unter <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Wiederverwenden eines Popups mit mehreren Stecknadeln) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Die folgenden interessanten Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines HTML-Markers](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Form](./map-add-shape.md)