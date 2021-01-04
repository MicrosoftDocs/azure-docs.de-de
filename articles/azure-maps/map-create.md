---
title: Erstellen einer Karte mit Azure Maps | Microsoft Azure Maps
description: Finden Sie heraus, wie Sie mithilfe des Azure Maps Web SDK Karten zu Webseiten hinzufügen. Erfahren Sie mehr über Optionen für Animation, Stil, Kamera, Dienste und Benutzerinteraktionen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890920"
---
# <a name="create-a-map"></a>Erstellen einer Karte

In diesem Artikel erfahren Sie, wie Sie eine Karte erstellen und animieren.  

## <a name="loading-a-map"></a>Laden einer Karte

Um eine Karte zu laden, erstellen Sie eine neue Instanz der [Map-Klasse](/javascript/api/azure-maps-control/atlas.map). Beim Initialisieren der Karte übergeben Sie eine DIV-Element-ID zum Rendern der Karte und eine Reihe von Optionen, die beim Laden der Karte verwendet werden. Wenn im `atlas`-Namespace keine standardmäßigen Authentifizierungsinformationen angegeben sind, müssen diese Informationen beim Laden der Karte in den Kartenoptionen angegeben werden. Die Karte lädt aus Leistungsgründen verschiedene Ressourcen asynchron. Fügen Sie daher nach dem Erstellen der Karteninstanz ein `ready`- oder ein `load`-Ereignis zur Karte hinzu, und fügen Sie dann zusätzlichen Code, der mit der Karte interagiert, zum Ereignishandler hinzu. Das `ready`-Ereignis wird ausgelöst, sobald genügend Ressourcen geladen wurden, um eine programmgesteuerte Interaktion mit der Karte zu ermöglichen. Das `load`-Ereignis wird ausgelöst, nachdem die anfängliche Kartenansicht vollständig geladen wurde. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundlegende Kartenladefunktion" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> (Grundlegende Kartenladefunktion) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Sie können mehrere Karten auf dieselbe Seite laden. Mehrere Karten auf derselben Seite können dieselben oder unterschiedliche Authentifizierungs- und Spracheinstellungen verwenden.

## <a name="show-a-single-copy-of-the-world"></a>Anzeigen nur einer Weltkarte

Wenn der Maßstab einer Karte auf einem Breitbildschirm verkleinert wird, werden mehrere Kopien der Weltkarte horizontal angezeigt. Diese Option ist für einige Szenarien hervorragend geeignet, aber für andere Anwendungen ist es empfehlenswert, dass nur eine einzelne Kopie der Welt angezeigt wird. Dieses Verhalten wird implementiert, indem die Option `renderWorldCopies` der Karte auf `false` festgelegt wird.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>


## <a name="map-options"></a>Kartenoptionen

Beim Erstellen einer Karte können mehrere verschiedene Typen von Optionen übergeben werden, um die Funktionsweise der Karte anzupassen. Diese sind nachfolgend aufgeführt.

- Mit [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) und [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) wird der Bereich angegeben, der auf der Karte angezeigt werden soll.
- Mit [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) wird angegeben, wie die Karte mit Diensten interagieren soll, die die Karte unterstützen.
- Mit [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) wird angegeben, wie die Karte formatiert und gerendert werden soll.
- Mit [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) wird angegeben, wie die Karte erreicht werden soll, wenn der Benutzer mit der Karte interagiert. 

Diese Optionen können auch nach dem Laden der Karte mit den Funktionen `setCamera`, `setServiceOptions`, `setStyle`und `setUserInteraction` aktualisiert werden. 

## <a name="controlling-the-map-camera"></a>Steuern der Kartenkamera

Es gibt zwei Möglichkeiten, um den angezeigten Kartenbereich mithilfe der Kamera einer Karte festzulegen. Sie können die Kameraoptionen beim Laden der Karte festlegen. Sie können die Option `setCamera` auch jederzeit nach dem Laden der Karte aufrufen, um die Kartenansicht programmgesteuert zu aktualisieren.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Festlegen der Kamera

Mit der Kartenkamera wird gesteuert, was im Viewport der Kartencanvas angezeigt wird. Kameraoptionen können bei der Initialisierung in den Kartenoptionen übermittelt oder in der Funktion `setCamera` der Karte übergeben werden.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

Im folgenden Code wird ein [Map-Objekt](/javascript/api/azure-maps-control/atlas.map) erstellt, und die Optionen für Zentrieren und Zoomen werden festgelegt. Karteneigenschaften wie „Zentrieren“ und „Zoomfaktor“ gehören zu [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Erstellen einer Karte über „CameraOptions“' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen zum <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Erstellen einer Karte über `CameraOptions` </a> von Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Festlegen der Kameragrenzen

Ein Begrenzungsrahmen kann zum Aktualisieren der Kartenkamera verwendet werden. Wenn der Begrenzungsrahmen aus Punktdaten berechnet wurde, ist es oft hilfreich, in den Kameraoptionen auch einen Pixelauffüllungswert anzugeben, um die Symbolgröße zu berücksichtigen. Dadurch wird sichergestellt, dass keine Punkte am Rand des Kartenviewports ausgelassen werden.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

Im folgenden Code wird über `new atlas.Map()` ein [Map-Objekt](/javascript/api/azure-maps-control/atlas.map) konstruiert. Karteneigenschaften wie `CameraBoundsOptions` können mit der Funktion [setCamera](/javascript/api/azure-maps-control/atlas.map) der Map-Klasse definiert werden. Eigenschaften von Begrenzungen und Abständen werden mit `setCamera` festgelegt.

<br/>

<iframe height='500' scrolling='no' title='Erstellen einer Karte über „CameraBoundsOptions“' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen zum <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Erstellen einer Karte über`CameraBoundsOptions` </a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animieren der Kartenansicht

Beim Festlegen der Kameraoptionen der Karte können auch [Animationsoptionen](/javascript/api/azure-maps-control/atlas.animationoptions) festgelegt werden. Mit diesen Optionen werden der Animationstyp und die Dauer zum Verschieben der Kamera angegeben.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

Im folgenden Code erstellt der erste Codeblock eine Karte und legt die Kartenstile für Zentrierung und Zoom fest. Im zweiten Codeblock wird ein Klickereignishandler für die Schaltfläche zum Animieren erstellt. Beim Klicken auf diese Schaltfläche wird die `setCamera`-Funktion mit zufällig generierten Werten für [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) und [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) aufgerufen.

<br/>

<iframe height='500' scrolling='no' title='Animieren der Kartenansicht' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animieren der Kartenansicht</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="request-transforms"></a>Anforderungstransformationen

Manchmal ist es nützlich, HTTP-Anforderungen ändern zu können, die vom Kartensteuerelement erstellt wurden. Beispiel:

- Das Hinzufügen zusätzlicher Header zu Kachelanforderungen. Dies wird oft für kennwortgeschützte Dienste durchgeführt.
- Ändern von URLs zum Ausführen von Anforderungen über einen Proxydienst.

Die [Dienstoptionen](/javascript/api/azure-maps-control/atlas.serviceoptions) der Karte umfassen eine `transformRequest`-Funktion, die zum Ändern aller von der Karte gestellten Anforderungen verwendet werden kann, bevor diese durchgeführt werden. Die `transformRequest`-Option ist eine Funktion, die zwei Parameter akzeptiert: eine Zeichenfolgen-URL und eine Ressourcentyp-Zeichenfolge, die angibt, wofür die Anforderung verwendet wird. Diese Funktion muss ein [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters)-Ergebnis zurückgeben.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

Im folgenden Beispiel wird veranschaulicht, wie dies dazu verwendet werden kann, alle Anforderungen an die Größe `https://example.com` anzupassen, indem ein Benutzername und Kennwort als Header zur Anforderung hinzugefügt werden.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>Testen Sie den Code

Schauen Sie sich die Codebeispiele an. Sie können den JavaScript-Code auf der Registerkarte **JS** bearbeiten und sich die Änderungen an der Kartenansicht auf der Registerkarte **Ergebnis** ansehen. Sie können auch oben rechts auf **Auf CodePen bearbeiten** klicken und den Code in CodePen ändern.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Sehen Sie sich Codebeispiele an, die zeigen, wie Sie Ihrer App Funktionen hinzufügen:

> [!div class="nextstepaction"]
> [Ändern des Kartenstils](choose-map-style.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Steuerelementen zur Karte](map-add-controls.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)