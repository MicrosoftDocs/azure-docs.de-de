---
title: Vordefinierte Absichten
titleSuffix: Azure Cognitive Services
description: LUIS umfasst einen Satz von vordefinierten Absichten zum schnellen Hinzufügen von allgemeinen Szenarien für Benutzerkonversationen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 8e11b6d5a9c9df10e789c8e0be3a90d94ecf3d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505675"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Hinzufügen von vordefinierten Absichten für gängige Absichten 

LUIS umfasst eine Reihe von vordefinierten Absichten aus den vordefinierten Domänen, damit Sie schnell allgemeine Absichten und Äußerungen hinzufügen können. Dies ist eine schnelle und einfache Möglichkeit, um Ihrer Client-App für Konversationen grundlegende Fähigkeiten hinzuzufügen, ohne die Modelle für diese Fähigkeiten entwerfen zu müssen. 

## <a name="add-a-prebuilt-intent"></a>Hinzufügen einer vordefinierten Absicht

1. Wählen Sie auf der Seite **Meine Apps** Ihre App aus. Daraufhin wird Ihre App im Abschnitt **Build** der App geöffnet. 

1. Wählen Sie auf der Seite **Absichten** auf der Symbolleiste über der Absichtenliste **Add prebuilt intent** (Vordefinierte Absicht hinzufügen) aus. 

1. Wählen Sie die Absicht **Utilities.Cancel** im Popupdialogfeld aus. 

    ![Hinzufügen der vordefinierten Absicht](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Wählen Sie die Schaltfläche **Fertig** aus.

## <a name="train-and-test"></a>Trainieren und Testen

1. Trainieren Sie die App nach dem Hinzufügen der Absicht, indem Sie auf der Symbolleiste rechts oben **Trainieren** auswählen. 

1. Testen Sie die neue Absicht, indem Sie auf der rechten Symbolleiste **Testen** auswählen. 

1. Geben Sie im Textfeld Äußerungen für Stornierungen ein:

    |Testen der Äußerung|Vorhersageergebnis|
    |--|:--|
    |Ich möchte meinen Flug stornieren.|0,67|
    |Storniere den Kauf.|0,52|
    |Storniere die Besprechung.|0,56|

    ![Testen der vordefinierten Absicht](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Vordefinierte Entitäten](./luis-prebuilt-entities.md)
