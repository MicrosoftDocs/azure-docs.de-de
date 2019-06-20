---
title: Vordefinierte Domänen für Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS umfasst einen Satz von vordefinierten Domänen zum schnellen Hinzufügen von allgemeinen Szenarien für Benutzerkonversationen.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198928"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Hinzufügen vordefinierter Domänen für gängige Verwendungsszenarien 

LUIS umfasst eine Reihe von vordefinierten Absichten aus den vordefinierten Domänen, damit Sie schnell allgemeine Absichten und Äußerungen hinzufügen können. Dies ist eine schnelle und einfache Möglichkeit, um Ihrer Client-App für Konversationen grundlegende Fähigkeiten hinzuzufügen, ohne die Modelle für diese Fähigkeiten entwerfen zu müssen. 

## <a name="add-a-prebuilt-domain"></a>Hinzufügen einer vordefinierten Domäne

1. Wählen Sie auf der Seite **Meine Apps** Ihre App aus. Daraufhin wird Ihre App im Abschnitt **Build** der App geöffnet. 

1. Wählen Sie auf der Seite **Absichten** auf der Symbolleiste links unten **Add prebuilt domains** (Vordefinierte Domänen hinzufügen) aus. 

1. Wählen Sie die Absicht **Kalender** und dann die Schaltfläche **Domäne hinzufügen** aus.

    ![Hinzufügen der vordefinierten Domäne „Kalender“](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Wählen Sie in der Navigation links **Absichten** aus, um die Kalender-Absichten anzuzeigen. Jede Absicht aus dieser Domäne hat das Präfix `Calendar.`. Zusammen mit den Äußerungen werden der App zwei Entitäten für diese Domäne hinzugefügt: `Calendar.Location` und `Calendar.Subject`. 

## <a name="train-and-publish"></a>Trainieren und Veröffentlichen

1. Trainieren Sie die App nach dem Hinzufügen der Domäne, indem Sie auf der Symbolleiste rechts oben **Trainieren** auswählen. 

1. Wählen Sie auf der oberen Symbolleiste **Veröffentlichen** aus. Veröffentlichen Sie für die **Produktion**. 

1. Wenn die grüne Erfolgsmeldung angezeigt wird, wählen Sie den Link **Refer to the list of endpoints** (Endpunktliste anzeigen) aus, um die Endpunkte anzuzeigen.

1. Wählen Sie einen Endpunkt aus. An diesen Endpunkt wird eine neue Browserregisterkarte geöffnet. Lassen Sie die Browserregisterkarte geöffnet, und fahren Sie mit dem Abschnitt **Testen** fort.

## <a name="test"></a>Test

Testen Sie die neue Absicht am Endpunkt, indem Sie einen Wert für den **q**-Parameter hinzufügen: `Schedule a meeting with John Smith in Seattle next week`.

LUIS gibt die richtige Absicht und das Thema der Besprechung zurück:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Vordefinierter Domänenverweis](./luis-reference-prebuilt-domains.md)
