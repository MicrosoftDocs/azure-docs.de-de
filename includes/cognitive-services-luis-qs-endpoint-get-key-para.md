---
title: Includedatei
description: Includedatei
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: ef8dae8219eaf1a85a5c112705517b992e25a50f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124067"
---
Der Zugriff auf den Vorhersageendpunkt erfolgt mit einem Endpunktschlüssel. Verwenden Sie für diese Schnellstartanleitung den kostenlosen Startschlüssel, der Ihrem LUIS-Konto zugeordnet ist. 
 
1. Melden Sie sich mit Ihrem LUIS-Konto an. 

    [![Screenshot der Language Understanding (LUIS)-App-Liste](media/cognitive-services-luis/app-list.png "Screenshot der Language Understanding (LUIS)-App-Liste")](media/cognitive-services-luis/app-list.png)

2. Wählen Sie im Menü oben rechts Ihren Namen und dann **Einstellungen** aus.

    ![Zugriff auf das Menü mit den LUIS-Benutzereinstellungen](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Kopieren Sie den Wert des **Erstellungsschlüssels**. Sie verwenden diesen später in der Schnellstartanleitung. 

    [![Screenshot der Language Understanding (LUIS)-Benutzereinstellungen](media/cognitive-services-luis/get-user-authoring-key.png "Screenshot der Language Understanding (LUIS)-Benutzereinstellungen")](media/cognitive-services-luis/get-user-authoring-key.png)

    Der Erstellungsschlüssel ermöglicht eine kostenlose unbegrenzte Anzahl von Anforderungen an die Erstellungs-API und bis zu 1000 Abfragen an die Vorhersageendpunkt-API pro Monat für alle LUIS-Apps. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
