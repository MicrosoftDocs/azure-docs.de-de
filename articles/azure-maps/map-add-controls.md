---
title: Hinzufügen von Kartensteuerelementen in Azure Maps | Microsoft-Dokumentation
description: Hinzufügen von Steuerelementen für Zoom, Neigung und Drehen sowie einer Stilauswahl in Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108718"
---
# <a name="add-map-controls-to-azure-maps"></a>Hinzufügen von Kartensteuerelementen zu Azure Maps

In diesem Artikel wird gezeigt, wie Sie einer Karte Kartensteuerelemente hinzufügen. Außerdem erfahren Sie, wie Sie eine Karte mit allen Steuerelementen und einer [Stilauswahl](https://docs.microsoft.com/azure/azure-maps/choose-map-style) erstellen.

## <a name="add-zoom-control"></a>Hinzufügen eines Zoomsteuerelements

<iframe height='500' scrolling='no' title='Hinzufügen eines Zoomsteuerelements' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control</a> (Hinzufügen eines Zoomsteuerelements) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Codeblock wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Der Stil wird nicht vorab festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Das Zoomsteuerelement fügt die Möglichkeit hinzu, die Karte zu vergrößern und zu verkleinern. Der zweite Codeblock erstellt ein Zoomsteuerelement-Objekt mit dem Atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu. Das Zoomsteuerelement befindet sich im **Ereignislistener** der Karte, um sicherzustellen, dass es nach dem vollständigen Laden der Karte geladen wird.

## <a name="add-pitch-control"></a>Hinzufügen eines Neigungssteuerelements

<iframe height='500' scrolling='no' title='Hinzufügen eines Neigungssteuerelements' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control</a> (Hinzufügen eines Neigungssteuerelements) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Codeblock wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Der Stil wird nicht vorab festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Das Neigungssteuerelement fügt die Möglichkeit hinzu, die Neigung der Karte zu ändern. Der zweite Codeblock erstellt ein Neigungssteuerelement-Objekt mit dem Atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu. Das Neigungssteuerelement befindet sich im **Ereignislistener** der Karte, um sicherzustellen, dass es nach dem vollständigen Laden der Karte geladen wird.

## <a name="add-compass-control"></a>Hinzufügen eines Kompasssteuerelements

<iframe height='500' scrolling='no' title='Hinzufügen eines Drehsteuerelements' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control</a> (Hinzufügen eines Drehsteuerelements) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Codeblock wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Der Stil wird nicht vorab festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein Kompasssteuerelement-Objekt mit dem Atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol). Außerdem fügt er der Karte das Kompasssteuerelement mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu. Das Kompasssteuerelement befindet sich im **Ereignislistener** der Karte, um sicherzustellen, dass es nach dem vollständigen Laden der Karte geladen wird.

## <a name="a-map-with-all-controls"></a>Eine Karte mit allen Steuerelementen

<iframe height='500' scrolling='no' title='Eine Karte mit allen Steuerelementen' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls</a> (Eine Karte mit allen Steuerelementen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Codeblock wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Der Stil wird nicht vorab festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein Kompasssteuerelement-Objekt mit dem Atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu.

Der dritte Codeblock erstellt ein Zoomsteuerelement-Objekt mit dem Atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu.

Der vierte Codeblock erstellt ein Neigungssteuerelement-Objekt mit dem Atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu.

Der letzte Codeblock erstellt ein Stilauswahlobjekt mit dem Atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) und fügt es der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode von Maps hinzu. Alle Steuerelementobjekte befinden sich im **Ereignislistener** der Karte, um sicherzustellen, dass sie nach dem vollständigen Laden der Karte geladen werden.

Die Reihenfolge der Steuerelementobjekte im Skript bestimmt die Reihenfolge, in der sie auf der Karte angezeigt werden. Wenn Sie die Reihenfolge der Steuerelemente auf der Karte ändern möchten, ändern Sie ihre Reihenfolge im Skript.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Die folgenden Artikel enthalten den vollständigen Code:

> [!div class="nextstepaction"]
> [Hinzufügen von Stecknadeln zur Karte](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines Popups](./map-add-popup.md)
