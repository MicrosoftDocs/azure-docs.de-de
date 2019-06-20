---
title: Kartenstilfunktionen in Azure Maps | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu stilbezogenen Funktionen von Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60795903"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Auswählen eines Kartenstils in Azure Maps

In Azure Maps stehen vier verschiedene Kartenstile zur Auswahl. Weitere Informationen zu Kartenstilen finden Sie unter [Unterstützte Kartenstile in Azure Maps](./supported-map-styles.md). In diesem Artikel wird gezeigt, wie Sie mit den stilbezogenen Funktionen einen Stil für eine geladene Karte festlegen, einen neuen Stil festlegen und das Stilauswahlsteuerelement verwenden.

## <a name="set-style-on-map-load"></a>Festlegen des Stils für die geladene Karte

<iframe height='500' scrolling='no' title='Festlegen des Stils für eine geladene Karte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

Mit dem Codeblock oben wird der Abonnementschlüssel festgelegt und ein Kartenobjekt mit dem Stil „grayscale_dark“ erstellt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

## <a name="update-the-style"></a>Aktualisieren des Stils

<iframe height='500' scrolling='no' title='Aktualisieren des Stils' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

Mit dem Codeblock oben wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Der Stil wird nicht vorab festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird die [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode der Karte verwendet, um den Kartenstil auf „Satellit“ festzulegen.

## <a name="add-the-style-picker"></a>Hinzufügen der Stilauswahl

<iframe height='500' scrolling='no' title='Hinzufügen der Stilauswahl' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> A PEN BY Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf der <a href='https://codepen.io'>CodePen-Website</a>.
</iframe>

Mit dem ersten Codeblock im Code oben wird der Abonnementschlüssel festgelegt und ein Kartenobjekt erstellt. Als Stil wird vorab „grayscale_dark“ festgelegt. Anweisungen zum Erstellen einer Karte finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mithilfe des Atlas-[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)-Konstruktors eine Stilauswahl erstellt.

Mit einer Stilauswahl können Sie den Stil für die Karte auswählen. Im dritten Codeblock wird der Karte mithilfe der [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)-Methode der Karte die Stilauswahl hinzugefügt. Die Stilauswahl befindet sich im **Ereignislistener** der Karte, um sicherzustellen, dass sie nach dem vollständigen Laden der Karte geladen wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Hinzufügen von Steuerelementen zu Ihren Karten:

> [!div class="nextstepaction"]
> [Hinzufügen von Kartensteuerelementen](./map-add-controls.md)

Hinzufügen einer Kartenstecknadel:

> [!div class="nextstepaction"]
> [Hinzufügen von Stecknadeln zur Karte](./map-add-pin.md)
