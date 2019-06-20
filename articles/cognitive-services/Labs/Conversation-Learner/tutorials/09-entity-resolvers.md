---
title: Entitäts-Resolver in einem Conversation Learner-Modell – Azure Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Entitäts-Resolver in Conversation Learner verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 51f74f504f0ad70c8c7f73be8ee6a05add685824
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475740"
---
# <a name="entity-resolvers"></a>Entitäts-Resolver

Dieses Tutorial zeigt, wie Sie Entitäts-Resolver in Conversation Learner verwenden.

## <a name="video"></a>Video

[![Tutorial zu Entitäts-Resolvern (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

- Der Resolvertyp ist eine optionale Eigenschaft von benutzerdefinierten Entitäten.
- Entity-Resolver nutzen die Leistungsfähigkeit von vortrainierten Entitätserkennungen in LUIS, um weitere Details und bessere Übersichtlichkeit für Ihre benutzerdefinierte Entität bereitzustellen.

## <a name="steps"></a>Schritte

Beginnen Sie auf der Homepage in der Webbenutzeroberfläche.

### <a name="create-the-model"></a>Erstellen des Modells

1. Wählen Sie **Neues Modell** aus.
2. Geben Sie **Entity Resolvers** (Entitäts-Resolver) als **Namen** ein.
3. Klicken Sie auf **Erstellen**.

### <a name="create-a-pair-of-entities"></a>Erstellen eines Paars von Entitäten

1. Klicken Sie im linken Bereich auf **Entitäten** und dann auf **Neue Entität**.
2. Geben Sie **departure** (Abreise) als **Entitätsnamen** ein.
3. Wählen Sie **datetimeV2** als **Resolver Type** (Resolvertyp) aus.
4. Klicken Sie auf **Erstellen**. Schließen Sie das Popupfenster mit Informationen, indem Sie auf **OK** klicken.
5. Wiederholen Sie die Schritte 1 bis 4, um eine zweite Entität namens **return** (Rückkehr) mit dem Resolvertyp **datetimeV2** zu erstellen.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Erstellen eines Paars von Aktionen

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie im Feld **Bot's Response...** (Antwort des Bots) den Text **You are leaving on $departure and returning on $return** (Sie reisen am $departure ab und kehren am $return zurück) ein.
    - WICHITG: Bei der Eingabe von „$[entityName]“ müssen Sie die EINGABETASTE drücken oder in der Dropdownliste auf die Entität klicken. Andernfalls erkennt Conversation Learner die Eingabe als Text anstelle einer Entität.
    - Beachten Sie, dass das Feld **Required Entities** (Erforderliche Entitäten) diese Entitäten ebenfalls abruft, und sie können nicht entfernt werden. Dies verhindert, dass diese Aktion verfügbar wird, bevor beide erforderlichen Entitäten vorhanden sind.
3. Klicken Sie auf **Erstellen**.
4. Wählen Sie **New Action** (Neue Aktion) aus, um eine zweite Aktion zu erstellen.
5. Geben Sie im Feld **Bot's Response...** (Antwort des Bots) den Text **When are you planning to travel?** (Wann möchten Sie reisen?) ein.
6. Geben Sie **departure** (Abreise) und **return** (Rückkehr) als **Disqualifying Entities** (Disqualifizierende Entitäten) ein. Dadurch wird der Bot angewiesen, diese Aktion NICHT durchzuführen, wenn eine dieser Entitäten einen Wert enthält.
7. Klicken Sie auf **Erstellen**.

![](../media/T09_actions.png)

### <a name="training"></a>Training

1. Warten Sie bei der Angabe **Training: [Status]** in der oberen linken Ecke auf den Status **Completed** (Abgeschlossen).
    - Wenn dies zu lange dauert, können Sie auf den Link „Aktualisieren“ klicken.
    - Der Trainingsstatus „Abgeschlossen“, ist erforderlich, damit die Entitäts-Resolver funktionieren, wenn wir das Modell trainieren.

2. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
3. Geben Sie die erste Benutzeräußerung „Flug buchen“ ein. 
4. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
5. Wählen Sie die Antwort „Wann möchten Sie reisen?“ aus.
6. Antworten Sie als Benutzer mit „Morgen abreisen und Sonntag nächste Woche zurückkommen“.
    - Beachten Sie, dass Conversation Learner in diesem Benutzerdurchlauf zwei Entitäten mit vortrainiertem Datum erkannt hat.
7. Wählen Sie im Bereich „Entitätserkennung“ den Text „morgen“ aus, und markieren Sie ihn als „Abreise“.
8. Bezeichnen Sie auch den Text „Sonntag nächste Woche“ als „Rückreise“.
9. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Beachten Sie, dass der Bereich „Speicher“ Ihre Ab- und Rückreisedaten enthält.
    - Zeigen Sie mit der Maus auf jedes Datum. Sie sehen, dass die Entitäten Datumsobjekte sind, die im Gegensatz zu „Sonntag“ oder „morgen“ das tatsächliche Kalenderdatum genau erfassen.
10. Wählen Sie die Botantwort „Sie reisen am ... ab“ aus.
11. Klicken Sie auf die Schaltfläche „Speichern“.

![](../media/T09_training.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Enum-Entitäten](./tutorial-enum-set-entity.md)
