---
title: Erstellen einer Karte mit Azure Maps | Microsoft-Dokumentation
description: Erstellen einer JavaScript-Karte
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108598"
---
# <a name="create-a-map"></a>Erstellen einer Karte

In diesem Artikel erfahren Sie, wie Sie eine Karte erstellen und animieren.  

## <a name="understand-the-code"></a>Grundlegendes zum Code

Es gibt zwei Wege, um eine Karte zu erstellen. Sie können die Kamera der Karte einstellen, indem Sie den Mittelpunkt und den Zoomfaktor angeben, oder die Kameragrenzen der Karte festlegen, indem Sie den südwestlichen und nordöstlichen Begrenzungspunkt angeben.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Festlegen der Kamera

<iframe height='500' scrolling='no' title='Erstellen einer Karte über „CameraOptions“' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen zum <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Erstellen einer Karte über `CameraOptions` </a>von Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code wird mit `new atlas.Map()` ein [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Objekt erstellt, und der Mittelpunkt und Zoomfaktor werden festgelegt. Karteneigenschaften wie Mittelpunkt und Zoomfaktor sind Teil der [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Festlegen der Kameragrenzen

<iframe height='500' scrolling='no' title='Erstellen einer Karte über „CameraBoundsOptions“' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen zum <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Erstellen einer Karte über `CameraBoundsOptions` </a>von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im oben gezeigten Code wird über `new atlas.Map()` ein [Map-Objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) konstruiert. Karteneigenschaften wie `CameraBoundsOptions` können mit der Funktion [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) der Map-Klasse definiert werden. Eigenschaften von Begrenzungen und Abständen werden mit `setCamera` festgelegt.

### <a name="animate-map-view"></a>Animieren der Kartenansicht

<iframe height='500' scrolling='no' title='Animieren der Kartenansicht' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animieren der Kartenansicht</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Der erste Block im obigen Code erstellt ein [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Objekt mit `new atlas.Map()`. Karteneigenschaften wie Mittelpunkt und Zoomfaktor sind Teil der [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` können Sie im Map-Konstruktor oder über die Funktion [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) der Map-Klasse definieren. Der [Kartenstil](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) entspricht `road`.

Der zweite Codeblock erstellt eine animierte Kartenfunktion, die Änderungen in der Kartenansicht animiert, indem sie [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) mit der Funktion [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) definiert. Die Funktion wird durch die Schaltfläche „Karte animieren“ ausgelöst, um bei jedem Klick einen zufälligen Zoomfaktor zu erzeugen.

## <a name="try-out-the-code"></a>Testen Sie den Code

Sehen Sie sich den obigen Beispielcode an. Sie können den JavaScript-Code links auf der Registerkarte **JS** bearbeiten und sich die Änderungen an der Kartenansicht rechts auf der Registerkarte **Ergebnis** ansehen. Außerdem können Sie auf die Schaltfläche **Auf CodePen bearbeiten** klicken, und den Code auf CodePen bearbeiten.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Sehen Sie sich Codebeispiele an, die zeigen, wie Sie Ihrer App Funktionen hinzufügen:

> [!div class="nextstepaction"]
> [Auswählen eines Kartenstils](choose-map-style.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Kartensteuerelementen](map-add-controls.md)
