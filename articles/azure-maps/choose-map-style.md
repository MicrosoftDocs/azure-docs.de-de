---
title: Ändern des Stils des Azure Maps-Web-Kartensteuerelements
description: Lernen Sie, den Stil und die Optionen einer Karte zu ändern. Erfahren Sie, wie Sie einer Karte in Azure Maps ein Stilauswahl-Steuerelement hinzufügen können, sodass Benutzer zwischen verschiedenen Stilen wechseln können.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: f2d7c9e39cb3eb14a2c2c6a7b13e37f3a526184b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92889798"
---
# <a name="change-the-style-of-the-map"></a>Ändern des Kartenstils

Das Kartensteuerelement unterstützt verschiedene [Stiloptionen](/javascript/api/azure-maps-control/atlas.styleoptions) und [Basiskartenstile](supported-map-styles.md) für Karten. Alle Stile können festgelegt werden, wenn das Kartensteuerelement initialisiert wird. Alternativ können Sie auch Stile festlegen, indem Sie die `setStyle`-Funktion des Kartensteuerelements verwenden. In diesem Artikel wird gezeigt, wie diese Stiloptionen verwendet werden, um die Kartendarstellung anzupassen. Außerdem erfahren Sie, wie Sie das Stilauswahl-Steuerelement in Ihrer Karte implementieren. Das Stilauswahl-Steuerelement ermöglicht dem Benutzer das Umschalten zwischen verschiedenen Basisstilen.

## <a name="set-map-style-options"></a>Festlegen von Kartenstiloptionen

Stiloptionen können während der Initialisierung des Websteuerelements festgelegt werden. Alternativ können Sie auch Stiloptionen aktualisieren, indem Sie die `setStyle`-Funktion des Kartensteuerelements aufrufen. Alle verfügbaren Stiloptionen können Sie unter [Stiloptionen](/javascript/api/azure-maps-control/atlas.styleoptions) anzeigen.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

Das folgende Tool zeigt, wie die verschiedenen Stiloptionen die Kartendarstellung ändern. Um die 3D-Gebäude anzuzeigen, zoomen Sie stark auf eine größere Stadt herein.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Kartenstiloptionen" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Map style options</a> (Kartenstiloptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Festlegen eines Kartenbasisstils

Sie können das Kartensteuerelement auch mit einem der [Basiskartenstile](supported-map-styles.md) initialisieren, die im Web SDK verfügbar sind. Sie können die `setStyle`-Funktion verwenden, um den Basisstil mit einem anderen Kartenstil zu aktualisieren.

### <a name="set-a-base-map-style-on-initialization"></a>Festlegen eines Kartenbasisstils bei der Initialisierung

Basisstile des Kartensteuerelements können während der Initialisierung festgelegt werden. Im folgenden Code wird die Option `style` des Kartensteuerelements auf den [Kartenbasisstil `grayscale_dark`](supported-map-styles.md#grayscale_dark) festgelegt.  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Festlegen des Stils für eine geladene Karte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualisieren des grundlegenden Kartenstils

Der Kartenbasisstil kann mithilfe der `setStyle`-Funktion und durch Festlegen der `style`-Option aktualisiert werden, um entweder zu einem anderen Kartenbasisstil zu wechseln oder um zusätzliche Stiloptionen hinzuzufügen.

```javascript
map.setStyle({ style: 'satellite' });
```

Im folgenden Code wird nach dem Laden einer Karteninstanz der Kartenstil mithilfe der Funktion [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) von `grayscale_dark` auf `satellite` aktualisiert.

<br/>

<iframe height='500' scrolling='no' title='Aktualisieren des Stils' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Hinzufügen des Stilauswahl-Steuerelements

Das Stilauswahl-Steuerelement bietet eine komfortable Schaltfläche mit Flyoutbereich, die vom Endbenutzer verwendet werden kann, um zwischen Basisstilen zu wechseln.

Die Stilauswahl weist zwei verschiedene Layoutoptionen auf: `icon` und `list`. Außerdem gestattet Ihnen die Stilauswahl, zwei unterschiedliche `style`-Optionen für das Stilauswahl-Steuerelement auszuwählen: `light` und `dark`. In diesem Beispiel verwendet die Stilauswahl das `icon`-Layout und zeigt eine Auswahlliste der Kartenbasisstile in Form von Symbolen an. Das Stilauswahl-Steuerelement umfasst den folgenden Basissatz an Stilen: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]`. Weitere Informationen zu den Optionen des Stilauswahl-Steuerelements finden Sie unter [Stilsteuerelement-Optionen](/javascript/api/azure-maps-control/atlas.stylecontroloptions).

Das folgende Bild zeigt das im `icon`-Layout angezeigte Stilauswahl-Steuerelement.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Symbollayout in der Stilauswahl":::

Das folgende Bild zeigt das im `list`-Layout angezeigte Stilauswahl-Steuerelement.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Symbollayout in der Stilauswahl":::

> [!IMPORTANT]
> Standardmäßig listet das Stilauswahl-Steuerelement alle im S0-Tarif von Azure Maps verfügbaren Stile auf. Soll die Liste weniger Stile enthalten, können Sie an die Option `mapStyle` der Stilauswahl ein Array mit den Stilen übergeben, die in der Liste angezeigt werden sollen. Wenn Sie S1 verwenden und alle verfügbaren Stile anzeigen möchten, legen Sie die Option `mapStyles` der Stilauswahl auf `"all"` fest.

Im folgenden Code wird gezeigt, wie Sie die standardmäßige Liste der Basisstile `mapStyles` außer Kraft setzen können. In diesem Beispiel legen wir die `mapStyles`-Option so fest, dass die Basisstile aufgelistet werden, die vom Stilauswahl-Steuerelement angezeigt werden sollen.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen der Stilauswahl' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Fügen Sie Ihren Karten Steuerelemente hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen von Kartensteuerelementen](map-add-controls.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Stecknadeln zur Karte](map-add-pin.md)