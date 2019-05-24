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
ms.openlocfilehash: 67c95ffcdbdbcfbb9a86e15c91d984953d7bbffc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124060"
---
Um zu verstehen, was ein LUIS-Vorhersageendpunkt zurückgibt, zeigen Sie ein Vorhersageergebnis in einem Webbrowser an. Zum Abfragen einer öffentlichen App benötigen Sie Ihren eigenen Schlüssel und die App-ID. Die ID der öffentlichen IoT-App (`df67dcdb-c37d-46af-88e1-8b97951ca1c2`) wird als Teil der URL in Schritt 1 bereitgestellt.

Das Format der URL für eine **GET**-Endpunktanforderung ist wie folgt:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Der Endpunkt der öffentlichen IoT-App hat das folgende Format: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Kopieren Sie die URL, und ersetzen Sie den Wert `<YOUR_KEY>` durch Ihren Schlüssel.

2. Fügen Sie die URL in ein Browserfenster ein, und drücken Sie die EINGABETASTE. Das im Browser angezeigte JSON-Ergebnis gibt an, dass LUIS die Absicht `HomeAutomation.TurnOn` als die am höchsten bewertete Absicht und die Entität `HomeAutomation.Room` mit dem Wert `bedroom` erkennt.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Ändern Sie den Wert des Parameters `q=` in der URL in `turn off the living room light`, und drücken Sie die EINGABETASTE. Das Ergebnis gibt jetzt an, dass LUIS die Absicht `HomeAutomation.TurnOff` als die am höchsten bewertete Absicht und die Entität `HomeAutomation.Room` mit dem Wert `living room` erkannt hat. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
