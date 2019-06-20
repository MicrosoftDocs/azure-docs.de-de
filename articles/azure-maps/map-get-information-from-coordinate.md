---
title: Anzeigen von Informationen über eine Koordinate mit Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Informationen zu einer Adresse auf der Karte anzeigen, wenn ein Benutzer eine Koordinate auswählt.
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3b635ab96f1438377cb0d282c17304c2531185be
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357802"
---
# <a name="get-information-from-a-coordinate"></a>Abrufen von Informationen von einer Koordinate

In diesem Artikel wird gezeigt, wie Sie eine inverse Adresssuche durchführen, die die Adresse einer angeklickten Popup-Position anzeigt.

Es gibt zwei Möglichkeiten, eine inverse Adresssuche durchzuführen. Eine Möglichkeit besteht darin, die [Azure Maps-API für eine inverse Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) über ein Dienstmodul abzufragen. Die andere Möglichkeit besteht in der Nutzung der [Fetch-API](https://fetch.spec.whatwg.org/) für eine Anforderung an die [Azure Maps-API für die umgekehrte Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), um eine Adresse zu finden. Beide Möglichkeiten werden im Folgenden untersucht.

## <a name="make-a-reverse-search-request-via-service-module"></a>Durchführen einer inversen Suchanforderung über das Dienstmodul

<iframe height='500' scrolling='no' title='Abrufen von Informationen von einer Koordinate (Dienstmodul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock erstellt eine `SubscriptionKeyCredentialPolicy`, um HTTP-Anforderungen an Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet dann die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)-Instanz. `searchURL` stellt eine URL zu [Suchvorgängen](https://docs.microsoft.com/rest/api/maps/search) von Azure Maps dar.

Der dritte Codeblock aktualisiert den Stil des Mauscursors in einen Zeiger und erstellt ein [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)-Objekt. Eine Anleitung finden Sie unter [Hinzufügen eines Popupfensters zur Karte](./map-add-popup.md).

Der vierte Codeblock fügt einen [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) für Mausklicks hinzu. Bei Auslösung wird eine Suchabfrage mit den Koordinaten des Punkts erstellt, auf den geklickt wurde. Dabei wird die [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)-Methode des Dienstmoduls verwendet, um die [API für die umgekehrte Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) nach der Adresse der Koordinaten abzufragen. Anschließend wird mit der Methode `geojson.getFeatures()` eine GeoJSON-Merkmalsauswahl aus der Antwort extrahiert.

Der fünfte Codeblock richtet den HTML-Popupinhalt ein, um die Adresse aus der Antwort für die Koordinatenposition, auf die geklickt wurde, anzuzeigen.

Die Änderung des Cursors, ein Popup-Objekt und das Klickereignis werden jeweils im [Ladeereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte erstellt, um sicherzustellen, dass die Karte vollständig geladen wurde, bevor die Koordinateninformationen abgerufen werden können.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Durchführen einer inversen Suchanforderung über die Fetch-API

Klicken Sie auf die Karte, um für die ausgewählte Position mithilfe der Fetch-API eine inverse Geocodierungsanforderung auszuführen.

<iframe height='500' scrolling='no' title='Abrufen von Informationen von einer Koordinate' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Abrufen von Informationen von einer Koordinate) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Im obigen Code erstellt der erste Codeblock ein Kartenobjekt und legt als Authentifizierungsmechanismus den Abonnementschlüssel fest. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Der zweite Codeblock aktualisiert den Stil des Mauscursors in einen Zeiger und ein Objekt vom Typ [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Eine Anleitung finden Sie unter [Hinzufügen eines Popupfensters zur Karte](./map-add-popup.md).

Der dritte Codeblock fügt einen Ereignislistener für Mausklicks hinzu. Nach einem Mausklick wird mithilfe der [Fetch-API](https://fetch.spec.whatwg.org/) die [Azure Maps-API für eine umgekehrte Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) nach der Adresse für die Koordinaten, auf die geklickt wurde, abgefragt. Für eine erfolgreiche Antwort wird die Adresse für die angeklickte Position gesammelt sowie der Popupinhalt und die Position über die Funktion [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) der Popupklasse definiert.

Die Änderung des Cursors, ein Popup-Objekt und das Klickereignis werden jeweils im [Ladeereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) der Karte erstellt, um sicherzustellen, dass die Karte vollständig geladen wurde, bevor die Koordinateninformationen abgerufen werden können.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](./map-route.md)

> [!div class="nextstepaction"]
> [Anzeigen des Verkehrs](./map-show-traffic.md)
