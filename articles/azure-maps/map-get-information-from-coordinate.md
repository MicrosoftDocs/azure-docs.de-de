---
title: Anzeigen von Informationen zu einer Koordinate auf einer Karte | Microsoft Azure Maps
description: Hier erfahren Sie, wie Sie Informationen zu einer Adresse auf der Karte anzeigen, wenn ein Benutzer eine Koordinate auswählt.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890750"
---
# <a name="get-information-from-a-coordinate"></a>Abrufen von Informationen von einer Koordinate

In diesem Artikel wird gezeigt, wie Sie eine inverse Adresssuche durchführen, die die Adresse einer angeklickten Popup-Position anzeigt.

Es gibt zwei Möglichkeiten, eine inverse Adresssuche durchzuführen. Eine Möglichkeit besteht darin, die [Azure Maps-API für eine inverse Adresssuche](/rest/api/maps/search/getsearchaddressreverse) über ein Dienstmodul abzufragen. Die andere Möglichkeit besteht in der Verwendung der [Fetch-API](https://fetch.spec.whatwg.org/) für eine Anforderung an die [Azure Maps-API für die umgekehrte Adresssuche](/rest/api/maps/search/getsearchaddressreverse), um eine Adresse zu finden. Beide Möglichkeiten werden im Folgenden untersucht.

## <a name="make-a-reverse-search-request-via-service-module"></a>Durchführen einer inversen Suchanforderung über das Dienstmodul

<iframe height='500' scrolling='no' title='Abrufen von Informationen von einer Koordinate (Dienstmodul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Block ein Kartenobjekt und legt als Authentifizierungsmechanismus die Verwendung des Zugriffstokens fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt ein `TokenCredential`-Element, um HTTP-Anforderungen an Azure Maps mit dem Zugriffstoken zu authentifizieren. Anschließend wird das `TokenCredential`-Element an `atlas.service.MapsURL.newPipeline()` übergeben, und es wird eine [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline)-Instanz erstellt. `searchURL` stellt eine URL zu [Suchvorgängen](/rest/api/maps/search) von Azure Maps dar.

Der dritte Codeblock aktualisiert den Stil des Mauscursors in einen Zeiger und erstellt ein [popup](/javascript/api/azure-maps-control/atlas.popup#open)-Objekt. Eine Anleitung finden Sie unter [Hinzufügen eines Popupfensters zur Karte](./map-add-popup.md).

Der vierte Codeblock fügt einen [Ereignislistener](/javascript/api/azure-maps-control/atlas.map#events) für Mausklicks hinzu. Bei Auslösung wird eine Suchabfrage mit den Koordinaten des Punkts erstellt, auf den geklickt wurde. Dabei wird die [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)-Methode verwendet, um die [API für die umgekehrte Adresssuche](/rest/api/maps/search/getsearchaddressreverse) nach der Adresse der Koordinaten abzufragen. Anschließend wird mit der Methode `geojson.getFeatures()` eine GeoJSON-Merkmalsauswahl aus der Antwort extrahiert.

Der fünfte Codeblock richtet den HTML-Popupinhalt ein, um die Adresse aus der Antwort für die Koordinatenposition, auf die geklickt wurde, anzuzeigen.

Die Änderung des Cursors, das Popup-Objekt und das Klickereignis werden jeweils im [Ladeereignislistener](/javascript/api/azure-maps-control/atlas.map#events) der Karte erstellt. Diese Codestruktur stellt sicher, dass die Karte vollständig geladen wurde, bevor Koordinateninformationen abgerufen werden.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Durchführen einer inversen Suchanforderung über die Fetch-API

Klicken Sie auf die Karte, um für die ausgewählte Position mithilfe der Fetch-API eine inverse Geocodierungsanforderung auszuführen.

<iframe height='500' scrolling='no' title='Abrufen von Informationen von einer Koordinate' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Abrufen von Informationen von einer Koordinate) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus das Zugriffstoken fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock aktualisiert den Stil des Mauscursors zu einem Zeiger. Er instanziiert ein [popup](/javascript/api/azure-maps-control/atlas.popup#open)-Objekt. Eine Anleitung finden Sie unter [Hinzufügen eines Popupfensters zur Karte](./map-add-popup.md).

Der dritte Codeblock fügt einen Ereignislistener für Mausklicks hinzu. Nach einem Mausklick wird mithilfe der [Fetch-API](https://fetch.spec.whatwg.org/) die [Azure Maps-API für eine umgekehrte Adresssuche](/rest/api/maps/search/getsearchaddressreverse) nach der Adresse für die Koordinaten, auf die geklickt wurde, abgefragt. Für eine erfolgreiche Antwort sammelt sie die Adresse für den angeklickten Standort. Sie definiert den popup-Inhalt und die Position mithilfe der Funktion [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) der popup-Klasse.

Die Änderung des Cursors, das Popup-Objekt und das Klickereignis werden jeweils im [Ladeereignislistener](/javascript/api/azure-maps-control/atlas.map#events) der Karte erstellt. Diese Codestruktur stellt sicher, dass die Karte vollständig geladen wurde, bevor Koordinateninformationen abgerufen werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für die Verwendung des Suchdiensts](how-to-use-best-practices-for-search.md)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](./map-route.md)

> [!div class="nextstepaction"]
> [Anzeigen des Verkehrs](./map-show-traffic.md)