---
title: Verwenden der Eigenschaft „Erwartete Entität“ von Aktionen in Conversation Learner – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie die Eigenschaft „Erwartete Entität“ in einem Conversation Learner-Modell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 48a8abd401ff7191da4709c726042b566d140b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387823"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Verwenden der Eigenschaft „Erwartete Entität“ von Aktionen

In diesem Tutorial wird die Eigenschaft „Erwartete Entität“ von Aktionen veranschaulicht.

## <a name="video"></a>Video

[![Tutorial zu „Erwartete Entität“ (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details
Verwenden Sie Eigenschaft „Erwartete Entität“ einer Aktion, um die Antwort des Benutzers für diese Aktion in einer Entität zu speichern.

Beim Hinzufügen von Entitäten zur Eigenschaft „Erwartete Entität“ einer Aktion wird das System:

1. zuerst versuchen, Entitäten mithilfe des auf maschinellem Lernen basierenden Entitätsextraktionsmodells abzugleichen
2. die gesamte Benutzeräußerung basierend auf Heuristik „$entity“ zuweisen, wenn keine Entitäten gefunden werden
3. `EntityDetectionCallback` aufrufen und mit der Aktionsauswahl fortfahren.

## <a name="steps"></a>Schritte

### <a name="create-the-model"></a>Erstellen des Modells

1. Klicken Sie auf der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie im Feld „Name“ den Text „ExpectedEntities“ ein, und drücken Sie die EINGABETASTE.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf „Entitäten“ und dann auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Custom Trained“ (Benutzerdefiniert trainiert) aus.
3. Geben Sie als Entitätsname „name“ (Name) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Der Entitätstyp „Benutzerdefiniert trainiert“ bedeutet, dass diese Entität im Gegensatz zu anderen Typen von Entitäten trainiert werden kann.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Erstellen der ersten Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) den Satz „What's your name?“ (Wie lautet Ihr Name?) ein.
3. Geben Sie im Feld „Erwartete Entitäten“ die Zeichenfolge „name“ ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Erkannte und aus der Antwort des Benutzers extrahierte Entitäten werden in der Entität „Name“ gespeichert, wenn diese Aktion ausgewählt wird. Wenn keine Entitäten erkannt werden, wird die gesamte Antwort in dieser Entität gespeichert.

### <a name="create-the-second-action"></a>Erstellen der zweiten Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „Hi $name!“ ein.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Die Entität „Name“ wurde durch einen Verweis in der Antwort automatisch den erforderlichen Entitäten hinzugefügt.

Jetzt sind zwei Aktionen vorhanden.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Trainieren des Modells

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hi“ ein.
    - Dies simuliert die Konversation auf der Seite des Benutzers.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Wie lautet Ihr Name?“ aus.
    - Die Antwort „Hi $name!“ kann nicht ausgewählt werden, da diese Antwort erfordert, dass die Entität „Name“ jetzt im Speicher des Modells definiert ist.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Frank“ ein.
    - „Frank“ wird als Entität basierend auf der Heuristik hervorgehoben, die wir zuvor zum Speichern der Antwort als Entität eingerichtet haben.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Die Entität „Name“ ist jetzt als „Frank“ im Speicher des Modells definiert, daher kann die Aktion „Hallo $name“ als Aktion ausgewählt werden.
7. Wählen Sie die Antwort „Hi $name!“ aus.
8. Klicken Sie auf die Schaltfläche „Speichern“.

Durch das Hinzufügen von alternativen Eingaben wird das Modell weiter trainiert.

1. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „I'm Jose.“ (Ich bin Jose) ein.
    - Das Modell erkennt den Namen nicht als Entität, daher wählt es den gesamten Textblock als Wert der Entität aus.
2. Klicken Sie auf den Ausdruck „I'm Jose“ (Ich bin Jose) und dann das Papierkorbsymbol.
3. Klicken Sie auf „Jose“ und dann in der Entitätsliste auf „Name“.
4. Klicken Sie auf „Score Actions“ (Bewertungsaktionen).
5. Wählen Sie die Antwort „Hi Frank!“ aus.
6. Klicken Sie auf die Schaltfläche „Speichern“.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Negierbare Entitäten](./06-negatable-entities.md)
