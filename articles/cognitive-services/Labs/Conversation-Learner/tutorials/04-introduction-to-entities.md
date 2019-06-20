---
title: Verwenden von Entitäten in einem Conversation Learner-Modell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Entitäten in einem Conversation Learner-Modell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 3d9e2498a23ad49eb014cb0f81c819f3f63eef5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387797"
---
# <a name="introduction-to-entities"></a>Einführung in Entitäten

In diesem Tutorial werden Entitäten, disqualifizierende Entitäten, erforderliche Entitäten sowie deren Nutzung in Conversation Learner eingeführt.

## <a name="video"></a>Video

[![Einführung in Entitäten: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Requirements (Anforderungen)

Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

Entitäten erfassen die Informationen, die der Bot zum Durchführen seiner Aufgabe benötigt. Diese werden entweder aus Benutzeräußerungen extrahiert oder durch benutzerdefinierten Code zugewiesen. Entitäten selbst können auch die Verfügbarkeit von Aktion einschränken, indem diese explizit als „Erforderlich“ oder „Disqualifizierend“ klassifiziert werden.

- Erforderliche Entitäten müssen im Speicher des Modells vorhanden sein, damit die Aktion verfügbar ist.
- Die disqualifizierenden Entitäten müssen *nicht* im Speicher des Modells vorhanden sein, damit die Aktion verfügbar ist.

In diesem Tutorial geht es vor allem um benutzerdefinierte Entitäten: Vortrainierte, mehrwertige, negierbare und programmgesteuerte Entitäten werden in anderen Tutorials eingeführt.

## <a name="steps"></a>Schritte

### <a name="create-the-model"></a>Erstellen des Modells

1. Klicken Sie auf der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie im Feld „Name“ den Text „IntroToEntities“ ein, und drücken Sie die EINGABETASTE.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf „Entitäten“ und dann auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Custom Trained“ (Benutzerdefiniert trainiert) aus.
3. Geben Sie als Entitätsname „City“ (Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Der Entitätstyp „Benutzerdefiniert trainiert“ bedeutet, dass diese Entität im Gegensatz zu anderen Typen von Entitäten trainiert werden kann.

### <a name="create-the-actions"></a>Erstellen der Aktionen

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „I don't know what city you want.“ (Ich weiß nicht, welche Stadt Sie möchten.) ein.
3. Geben Sie im Feld „Disqualifying Entities“ (Disqualifizierende Entitäten) die Zeichenfolge „City“ (Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Durch Hinzufügen der Entität „city“ zu „Disqualifying Entities“ (Disqualifizierende Entitäten) würde die Berücksichtigung dieser Aktion durch den Bot disqualifiziert, wenn die Entität „city“ im Speicher des Bots definiert ist.

Erstellen Sie nun eine zweite Aktion.

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) den Satz „Das Wetter in $Stadt ist wahrscheinlich sonnig“ ein.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

> [!NOTE]
> Die Entität „city“ wurde durch einen Verweis in der Antwort automatisch der Liste mit erforderlichen Entitäten hinzugefügt.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Trainieren des Modells

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hello“ ein.
    - Dies simuliert die Konversation auf der Seite des Benutzers.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Ich weiß nicht, welche Stadt Sie möchten“ aus.
5. Antworten Sie als Benutzer mit „Seattle“.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die Antwort „Das Wetter in $city ist wahrscheinlich sonnig“ aus.
8. Klicken Sie auf die Schaltfläche „Speichern“.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erwartete Entität](./05-expected-entity.md)
