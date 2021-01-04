---
title: Abrufen des V3-Vorhersageendpunkts
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128160"
---
1. Kopieren Sie im LUIS-Portal im Abschnitt **Verwalten** (Menü rechts oben) auf der Seite **Azure-Ressourcen** (linkes Menü) auf der Registerkarte **Vorhersageressourcen** die **Beispielabfrage** am unteren Seitenrand. Die URL enthält Ihre App-ID, den Schlüssel und den Slotnamen. Die URL für V3 des Vorhersageendpunkts hat das folgende Format: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Beispielabfrage im Abschnitt mit den Vorhersageressourcen" lightbox="../media/prediction-resources-example-query.png":::
    
    Fügen Sie die URL in einen neuen Browsertab ein. Falls die URL nicht angezeigt wird, verfügen Sie nicht über eine Vorhersageressource und müssen eine erstellen. 

    

    

