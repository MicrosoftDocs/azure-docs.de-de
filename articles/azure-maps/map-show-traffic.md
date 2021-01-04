---
title: Anzeigen von Verkehrsinfos auf einer Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie Karten Verkehrsinfos hinzufügen. Erfahren Sie mehr über Verkehrsflussdaten und wie Sie mit dem Azure Maps Web SDK Vorfallsdaten und Flussdaten zu Karten hinzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: ec7459a356221fb7b599cbbc02f1cb825920b5b3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890665"
---
# <a name="show-traffic-on-the-map"></a>Anzeigen von Datenverkehr auf einer Karte

In Azure Maps sind zwei Arten von Verkehrsdaten verfügbar:

- Vorfallsdaten: Punkt- und linienbasierte Daten, z. B. für Baustellen, Straßensperrungen und Unfälle.
- Flussdaten: Stellt Metriken zum Verkehrsfluss auf Straßen bereit. Daten zum Verkehrsfluss werden häufig verwendet, um Straßen farbig zu markieren. Die Farben basieren auf dem Verkehrsaufkommen, das aufgrund einer Geschwindigkeitsbeschränkung oder anderen Metrik zu einer Verlangsamung des Verkehrsflusses führt. Die Daten zum Verkehrsfluss in Azure Maps verfügen über drei unterschiedliche Metriken für Messungen:
    - `relative`: Geschwindigkeit relativ zur Geschwindigkeit bei ungehindertem Verkehrsfluss auf der Straße.
    - `absolute`: Absolute Geschwindigkeit aller Fahrzeuge auf der Straße.
    - `relative-delay`: Zeigt Bereiche mit einer langsameren Geschwindigkeit als der durchschnittlichen erwarteten Verzögerung an.

Der folgende Code verdeutlicht, wie Sie Verkehrsdaten auf der Karte anzeigen.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Anzeigen von Datenverkehr auf einer Karte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Anzeigen von Datenverkehr auf einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Optionen für Verkehrsdatenüberlagerungen

Mit dem folgenden Tool können Sie zwischen den unterschiedlichen Einstellungen für Verkehrsdatenüberlagerungen wechseln, um auszuprobieren, wie sich das Rendering ändert. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Optionen für Verkehrsdatenüberlagerungen" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie im Pen <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Traffic overlay options</a> (Optionen für Verkehrsdatenüberlagerungen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Hinzufügen von Steuerelementen für Straßenverkehr

Es gibt zwei Steuerelementen für Straßenverkehr, die der Karte hinzugefügt werden können. Das erste Steuerelement (`TrafficControl`) fügt eine Umschalttaste hinzu, über die der Verkehr ein- und ausgeschaltet werden kann. Mit Optionen für dieses Steuerelement können Sie angeben, wann Verkehrseinstellungen verwendet werden sollen, wenn Verkehr angezeigt wird. Standardmäßig zeigt dieses Steuerelement den relativen Verkehrsfluss und die Vorfalldaten an. Sie können dies jedoch ändern, um den absoluten Verkehrsfluss und ggf. keine Vorfälle anzuzeigen. Das zweite Steuerelement (`TrafficLegendControl`) fügt der Karte eine Verkehrsflusslegende hinzu, mit der Benutzer verstehen können, was die farbigen Straßenmarkierungen bedeuten. Dieses Steuerelement wird nur bei Anzeige von Verkehrsflussdaten auf der Karte eingeblendet und ist zu allen anderen Zeiten ausgeblendet.

Der folgende Code zeigt, wie die Verkehrssteuerelemente der Karte hinzugefügt werden können.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Verkehrssteuerelemente" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Siehe den Pen <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>Traffic controls</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Verbessern der Benutzerfreundlichkeit:

> [!div class="nextstepaction"]
> [Karteninteraktion mit Mausereignissen](map-events.md)

> [!div class="nextstepaction"]
> [Erstellen einer zugänglichen Karte](map-accessibility.md)

> [!div class="nextstepaction"]
> [Seite für Beispielcode](https://aka.ms/AzureMapsSamples)