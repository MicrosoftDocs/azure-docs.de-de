---
title: Behandeln von Kartenereignissen | Microsoft Azure Maps
description: Erfahren Sie, welche Ereignisse ausgelöst werden, wenn Benutzer mit Karten interagieren. Zeigen Sie eine Liste aller unterstützten Kartenereignisse an. Erfahren Sie, wie Sie mit dem Azure Maps Web SDK Ereignisse verarbeiten können.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890784"
---
# <a name="interact-with-the-map"></a>Interaktion mit der Karte

In diesem Artikel wird die Verwendung der [Klasse von Kartenereignissen](/javascript/api/azure-maps-control/atlas.map#events) gezeigt. Die Eigenschaft hebt Ereignisse auf der Karte und auf verschiedenen Ebenen der Karte hervor. Sie können auch Ereignisse hervorheben, wenn Sie mit einem HTML-Marker interagieren.

## <a name="interact-with-the-map"></a>Interaktion mit der Karte

Experimentieren Sie mit der unten gezeigten Karte, und sehen Sie die entsprechenden Mausereignisse hervorgehoben auf der rechten Seite. Sie können auf die Registerkarte **JS** klicken, um den JavaScript-Code anzuzeigen und zu bearbeiten. Sie können auch auf **Auf CodePen bearbeiten** klicken, um den Code auf CodePen zu ändern.

<br/>

<iframe height='600' scrolling='no' title='Interaktion mit der Karte – Mausereignisse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map – mouse events</a> (Interagieren mit der Karte – Mausereignisse) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interaktion mit Kartenebenen

Der folgende Code hebt das ausgelöste Ereignis hervor, während Sie mit der Symbolebene interagieren. Die Symbol-, Blasen-, Linien- und Polygonebenen unterstützen den gleichen Satz von Ereignissen. Die Wärmebild- und Kachelebenen unterstützen keines dieser Ereignisse.

<br/>

<iframe height='600' scrolling='no' title='Interaktion mit der Karte – Ebenenereignisse' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a> (Interaktion mit der Karte – Ebenenereignisse) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interaktion mit dem HTML-Marker

Der folgende Code fügt einem HTML-Marker JavaScript-Kartenereignisse hinzu. Er hebt außerdem den Namen der Ereignisse hervor, die bei der Interaktion mit dem HTML-Marker ausgelöst werden.

<br/>

<iframe height='500' scrolling='no' title='Interaktion mit der Karte – HTML Markerereignisse' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a> (Interaktion mit der Karte – HTML Markerereignisse) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

In der folgenden Tabelle sind alle unterstützten Ereignisse der Eigenschaft „map class events“ aufgelistet.

| Ereignis               | BESCHREIBUNG |
|---------------------|-------------|
| `boxzoomend`        | Wird ausgelöst, wenn eine Interaktion vom Typ „Feld zoomen“ beendet wird.|
| `boxzoomstart`      | Wird ausgelöst, wenn eine Interaktion vom Typ „Feld zoomen“ gestartet wird.|
| `click`             | Wird ausgelöst, wenn ein Zeigegerät an derselben Stelle auf der Karte gedrückt und losgelassen wird.|
| `close`             | Wird ausgelöst, wenn das Popup manuell oder programmgesteuert geschlossen wird.|
| `contextmenu`       | Wird ausgelöst, wenn mit der rechten Maustaste geklickt wird.|
| `data`              | Wird ausgelöst, wenn Kartendaten geladen oder geändert werden. |
| `dataadded`         | Wird ausgelöst, wenn Formen zu `DataSource` hinzugefügt werden.|
| `dataremoved`       | Wird ausgelöst, wenn Formen aus `DataSource` entfernt werden.|
| `datasourceupdated` | Wird ausgelöst, wenn das `DataSource`-Objekt aktualisiert wird.|
| `dblclick`          | Wird ausgelöst, wenn mit einem Zeigegerät zweimal an derselben Stelle auf der Karte geklickt wird.|
| `drag`              | Wird während einer Interaktion vom Typ „zum Schwenken ziehen“ für die Karte, das Popup oder den HTML-Marker wiederholt ausgelöst.|
| `dragend`           | Wird ausgelöst, wenn eine Interaktion vom Typ „zum Schwenken ziehen“ für die Karte, das Popup oder den HTML-Marker beendet wird.|
| `dragstart`         | Wird ausgelöst, wenn eine Interaktion vom Typ „zum Schwenken ziehen“ für die Karte, das Popup oder den HTML-Marker gestartet wird.|
| `error`             | Wird ausgelöst, wenn ein Fehler auftritt.|
| `idle`              | <p>Wird ausgelöst, nachdem der letzte Frame gerendert wurde, jedoch bevor die Karte in den Leerlauf wechselt:<ul><li>Es werden keine Kameraübergänge ausgeführt.</li><li>Alle zurzeit angeforderten Kacheln wurden geladen.</li><li>Alle Ausblend-/Übergangsanimationen wurden abgeschlossen.</li></ul></p>|
| `keydown`           | Wird ausgelöst, wenn eine Taste gedrückt wird.|
| `keypress`          | Wird ausgelöst, wenn eine Taste gedrückt wird, die ein typisierbares Zeichen (einen ANSI-Schlüssel) erzeugt.|
| `keyup`             | Wird ausgelöst, wenn eine Taste losgelassen wird.|
| `layeradded`        | Wird ausgelöst, wenn der Karte eine Ebene hinzugefügt wird.|
| `layerremoved`      | Wird ausgelöst, wenn aus der Karte eine Ebene entfernt wird.|
| `load`              | Wird sofort ausgelöst, nachdem alle erforderlichen Ressourcen heruntergeladen wurden und das erste visuell vollständige Rendering der Karte stattgefunden hat.|
| `mousedown`         | Wird ausgelöst, wenn ein Zeigegerät innerhalb der Karte oder auf einem Element gedrückt wird.|
| `mouseenter`        | Wird ausgelöst, wenn ein Zeigegerät anfänglich über die Karte oder ein Element bewegt wird. |
| `mouseleave`        | Wird ausgelöst, wenn ein Zeigegerät aus der Karte oder aus einem Element herausbewegt wird. |
| `mousemove`         | Wird ausgelöst, wenn ein Zeigegerät innerhalb der Karte oder in einem Element bewegt wird.|
| `mouseout`          | Wird ausgelöst, wenn ein Zeigegerät den Zeichenbereich der Karte oder eines Elements verlässt.|
| `mouseover`         | Wird ausgelöst, wenn ein Zeigegerät über die Karte oder ein Element bewegt wird.|
| `mouseup`           | Wird ausgelöst, wenn ein Zeigegerät innerhalb der Karte oder auf einem Element losgelassen wird.|
| `move`              | Wird während eines animierten Übergangs von einer Ansicht zu einer anderen wiederholt ausgelöst, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `moveend`           | Wird ausgelöst, kurz nachdem die Karte den Übergang von einer Ansicht zu einer anderen abgeschlossen hat, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `movestart`         | Wird ausgelöst, kurz bevor die Karte den Übergang von einer Ansicht zu einer anderen beginnt, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `open`              | Wird ausgelöst, wenn das Popup manuell oder programmgesteuert geöffnet wird.|
| `pitch`             | Wird ausgelöst, sobald sich die Neigung der Karte entweder infolge einer Benutzerinteraktion oder durch Methoden ändert.|
| `pitchend`          | Wird ausgelöst, unmittelbar nachdem das Ändern der Kartenneigung beendet wurde, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `pitchstart`        | Wird ausgelöst, sobald mit dem Ändern der Kartenneigung begonnen wird, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `ready`             | Wird ausgelöst, wenn die mindestens erforderlichen Kartenressourcen geladen werden, bevor mit der Karte programmgesteuert interagiert werden kann.|
| `render`            | <p>Wird ausgelöst, sobald die Karte aufgrund eines der folgenden Ereignisse auf dem Bildschirm gezeichnet wird:<ul><li>Eine Änderung der Position, des Zooms, der Neigung oder der Peilung der Karte.</li><li>Eine Änderung des Kartenstils.</li><li>Eine Änderung an einer `DataSource`-Quelle.</li><li>Das Laden einer Vektorkachel, einer GeoJSON-Datei, eines Glyphen oder eines Sprites.</li></ul></p>|
| `resize`            | Wird unmittelbar nach einer Größenänderung der Karte ausgelöst.|
| `rotate`            | Wird während einer Interaktion vom Typ „zum Drehen ziehen“ wiederholt ausgelöst.|
| `rotateend`         | Wird ausgelöst, wenn eine Interaktion vom Typ „zum Drehen ziehen“ beendet wird.|
| `rotatestart`       | Wird ausgelöst, wenn eine Interaktion vom Typ „zum Drehen ziehen“ gestartet wird.|
| `shapechanged`      | Wird ausgelöst, wenn eine Shape-Objekteigenschaft geändert wird.|
| `sourcedata`        | Wird ausgelöst, wenn eine Kartenquelle oder eine Kachel, die zu einer Quelle gehört, geladen oder geändert wird. |
| `sourceadded`       | Wird ausgelöst, wenn der Karte `DataSource` oder `VectorTileSource` hinzugefügt wird.|
| `sourceremoved`     | Wird ausgelöst, wenn aus der Karte `DataSource` oder `VectorTileSource` entfernt wird.|
| `styledata`         | Wird ausgelöst, wenn der Kartenstil geladen oder geändert wird.|
| `styleimagemissing` | Wird ausgelöst, wenn eine Ebene versucht, ein Bild aus dem noch nicht vorhandenen Bild-Sprite zu laden. |
| `tokenacquired`     | Wird ausgelöst, wenn ein Zugriffstoken für Azure Active Directory abgerufen wird.|
| `touchcancel`       | Wird ausgelöst, wenn ein `touchcancel`-Ereignis innerhalb der Karte auftritt.|
| `touchend`          | Wird ausgelöst, wenn ein `touchend`-Ereignis innerhalb der Karte auftritt.|
| `touchmove`         | Wird ausgelöst, wenn ein `touchmove`-Ereignis innerhalb der Karte auftritt.|
| `touchstart`        | Wird ausgelöst, wenn ein `touchstart`-Ereignis innerhalb der Karte auftritt.|
| `wheel`             | Wird ausgelöst, wenn ein Mausradereignis innerhalb der Karte auftritt.|
| `zoom`              | Wird während eines animierten Übergangs von einem Zoomfaktor zu einem anderen wiederholt ausgelöst, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `zoomend`           | Wird ausgelöst, kurz nachdem die Karte den Übergang von einem Zoomfaktor zu einem anderen abgeschlossen hat, entweder infolge einer Benutzerinteraktion oder durch Methoden.|
| `zoomstart`         | Wird ausgelöst, kurz bevor die Karte den Übergang von einem Zoomfaktor zu einem anderen beginnt, entweder infolge einer Benutzerinteraktion oder durch Methoden.|


## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Dienste“ von Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)