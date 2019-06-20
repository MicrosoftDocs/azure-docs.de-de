---
title: Verwenden von Aktionen mit und ohne Wartezeit in einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Aktionen mit und ohne Wartezeit in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 57a58b008792130c82a86ff4a5801f4bb39aabad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387904"
---
# <a name="wait-and-non-wait-actions"></a>Aktionen mit und ohne Wartezeit

In diesem Tutorial wird der Unterschied zwischen Aktionen mit und ohne Wartezeit im Unterhaltungslernmodul aufgezeigt.

## <a name="video"></a>Video

[![Aktionen mit und ohne Wartezeit: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

- Aktion mit Wartezeit: Nachdem das System eine Aktion mit Wartezeit ausgeführt hat, führt es keine weiteren Aktionen aus und wartet auf Benutzereingaben.
- Aktion ohne Wartezeit: Nachdem das System eine Aktion ohne Wartezeit ausgeführt hat, wählt es sofort eine andere Aktion aus, ohne auf eine Benutzereingabe zu warten.

## <a name="steps"></a>Schritte

### <a name="create-a-new-model"></a>Erstellen Sie ein neues Modell.

1. Klicken Sie in der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie im Feld „Name“ die Zeichenfolge „Wait Non-Wait“ ein, und drücken Sie die EINGABETASTE, oder klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="create-the-first-two-wait-actions"></a>Erstellen der ersten beiden Aktionen mit Wartezeit

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „What pizza would you like?“ (Welche Pizza möchten Sie?) ein.
    - Dies ist eine Aktion mit Wartezeit. Lassen Sie daher das Kontrollkästchen „Auf Antwort warten“ aktiviert.
3. Klicken Sie auf die Schaltfläche „Erstellen“.
4. Erstellen Sie mithilfe der gleichen Schritte eine weitere Aktion, verwenden Sie diesmal aber „Pizza on the way!“ (Pizza ist unterwegs!) als Antwort des Bots.

### <a name="train-using-those-wait-actions"></a>Trainieren unter Verwendung dieser Aktionen mit Wartezeit

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Hi“ ein. 
    - Dies simuliert die Konversation auf der Seite des Benutzers.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „What pizza would you like?“ (Welche Pizza möchten Sie?) aus.
5. Antworten Sie als Benutzer mit „Margherita“.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die Antwort „Pizza on the way!“ (Pizza ist unterwegs!) aus.
8. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="create-a-non-wait-action-while-training"></a>Erstellen einer Aktion ohne Wartezeit während des Trainings
Sie könnten die Aktion ohne Wartezeit zwar auf die gleiche Weise erstellen wie die anderen Aktionen, die Erstellung kann aber auch innerhalb einer Trainingssitzung erfolgen.
1. Klicken Sie auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie als Benutzer „Hello“ (Hallo) ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Klicken Sie auf die Schaltfläche „+Aktion“. 
    - Dadurch gelangen Sie zum inzwischen vertrauten Dialogfeld für die Aktionserstellung.
5. Geben Sie als Antwort des Bots „Welcome to Pizza Bot!“ (Willkommen beim Pizzabot!) ein.
6. Deaktivieren Sie das Kontrollkästchen „Auf Antwort warten“.
7. Klicken Sie auf die Schaltfläche „Erstellen“.
    - Beachten Sie, dass der Bot sofort mit „Welcome to Pizza Bot!“ (Willkommen beim Pizzabot!) antwortet und Sie erneut zur Eingabe einer Botantwort aufgefordert werden. Das liegt daran, dass es sich bei der Botantwort um die Aktion ohne Wartezeit handelt, die wir soeben erstellt haben.
9. Wählen Sie die Antwort „What pizza would you like?“ (Welche Pizza möchten Sie?) aus.
10. Antworten Sie als Benutzer mit „Margherita“.
11. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
12. Wählen Sie die Antwort „Pizza on the way!“ (Pizza ist unterwegs!) aus.
13. Klicken Sie auf die Schaltfläche „Speichern“.

> [!NOTE]
> Beachten Sie die Reihenfolge der Botantworten im Hinblick auf die Aktionen mit und ohne Wartezeit.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einführung in Entitäten](./04-introduction-to-entities.md)
