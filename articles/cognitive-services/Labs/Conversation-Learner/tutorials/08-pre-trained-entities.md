---
title: Hinzufügen vortrainierter Entitäten zu einem Conversation Learner-Modell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie vortrainierte Entitäten in einem Conversation Learner-Modell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fb70983c2f9fd20368bb8c6803c9568b27141af7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389257"
---
# <a name="how-to-add-pre-trained-entities"></a>Hinzufügen vortrainierter Entitäten
In diesem Tutorial erfahren Sie, wie Sie Ihrem Conversation Learner-Modell vortrainierte Entitäten hinzufügen.

## <a name="video"></a>Video

[![Vortrainierte Entitäten: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

Vortrainierte Entitäten erkennen häufig verwendete Entitätstypen wie Nummern, Datumsangaben, Geldbeträge und Ähnliches.  Sie können direkt verwendet werden, müssen nicht trainiert werden, und ihr Verhalten kann im Gegensatz zum Verhalten benutzerdefinierter Entitäten nicht geändert werden.  Vortrainierte Entitäten sind standardmäßig mehrwertig und kumulieren jede erkannte Instanz der Entität.

## <a name="steps"></a>Schritte

Beginnen Sie auf der Homepage in der Webbenutzeroberfläche.

### <a name="create-the-model"></a>Erstellen des Modells

1. Wählen Sie **Neues Modell** aus.
2. Geben Sie **PretrainedEntities** als **Namen** ein.
3. Klicken Sie auf **Erstellen**.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf **Entitäten** und dann auf **Neue Entität**.
2. Wählen Sie **Pre-Trained/datetimeV2** als **Entitätstyp** aus.
3. Aktivieren Sie **Mehrwertig**, um zu ermöglichen, dass die Entität einen oder mehrere Werte kumuliert. Beachten Sie, dass vortrainierte Entitäten nicht negiert werden können.
4. Klicken Sie auf **Erstellen**.

![](../media/T08_entity_create.png)

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie im Feld **Bot's response...** (Antwort des Bots) die Zeichenfolge **The date is $builtin-datetimev2** (Das Datum lautet $builtin-datetimev2) ein.
3. Klicken Sie auf **Erstellen**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Erstellen der zweiten Aktion

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie **What's the date?** (Wie lautet das Datum?) als **Bot‘s Response...** (Antwort des Bots) ein. Vortrainierte Entitäten können keine **erforderlichen Entitäten** sein, da sie standardmäßig für alle Äußerungen erkannt werden.
3. Geben Sie **builtin-datetimev2** als **Disqualifying Entities** (Disqualifizierende Entitäten) ein.
4. Klicken Sie auf **Erstellen**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Trainieren des Modells

1. Wählen Sie im linken Bereich **Train Dialogs** (Trainingsdialogfelder) aus, und klicken Sie dann auf **New Train Dialog** (Neues Trainingsdialogfeld).
2. Geben Sie im linken Chatbereich **hello** (Hallo) als Äußerung des Benutzers ein.
3. Wählen Sie **Score Actions** (Aktionen bewerten) aus.
4. Wählen Sie **What's the date?** (Wie lautet das Datum) aus der Aktionsliste aus.
5. Geben Sie im linken Chatbereich **today** (heute) als Äußerung des Benutzers ein.
    - Die Äußerung **today** (heute) wird von vortrainierten Modellen in LUIS automatisch erkannt.
    - Wenn Sie auf Werte vortrainierter Entitäten zeigen, werden zusätzliche, von LUIS bereitgestellte Daten angezeigt.

![](../media/T08_training.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entitäts-Resolver](./09-entity-resolvers.md)
