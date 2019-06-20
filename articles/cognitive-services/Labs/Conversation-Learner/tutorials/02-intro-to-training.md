---
title: Einführung in das Training eines Conversation Learner-Modells – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie ein Modell über Conversation Learner trainiert wird, einschließlich Verzweigung und Bearbeitung eines vorherigen Trainings.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 0bf5b71a4b0f51a586febbdaeaf8caba03c5b25a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387920"
---
# <a name="introduction-to-training"></a>Einführung in das Training

Dieses Tutorial zeigt die Grundlagen des Trainings eines Modells, das Verzweigen eines neuen Trainings basierend auf einem vorherigen Training und das Bearbeiten einer Botantwort, um sie zu ändern.

## <a name="video"></a>Video

[![Tutorial zur Einführung in das Training (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

- Aktionen: Eine Botantwort auf eine Benutzereingabe.
- Trainieren: Damit bringen wir einem Bot bei, auf Benutzereingaben zu reagieren.
- Verzweigen: Die Änderung einer Benutzereingabe in einem gespeicherten Trainingsdialogfeld für die Erstellung eines neuen Trainingsdialog, dessen Beginn mit dem ursprünglichen Dialog identisch ist, die Konversation dann jedoch eine andere Richtung nimmt.

## <a name="steps"></a>Schritte

### <a name="create-a-new-model"></a>Erstellen Sie ein neues Modell.

1. Klicken Sie in der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie als Name den Text „Inspire Bot“ (Bot inspirieren) ein. Klicken Sie auf „Erstellen“.

### <a name="create-an-action"></a>Erstellen einer Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „Hi! Would you like to be inspired today?“ (Möchten Sie sich heute inspirieren lassen) ein.
    - Behalten Sie für alle anderen Felder und Kontrollkästchen die Standardeinstellung bei.
3. Klicken Sie auf „Erstellen“.

### <a name="first-training-and-creating-another-action-while-training"></a>Erstes Training und Erstellen einer anderen Aktion beim Training

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hello“ ein. 
    - Dies simuliert die Konversation auf der Seite des Benutzers.
3. Klicken Sie auf „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Hi! Would you like to be inspired today?“ (Möchten Sie sich heute inspirieren lassen) aus.
5. Antworten Sie als Benutzer mit „yes“ (Ja).
6. Klicken Sie auf „Score Actions“ (Bewertungsaktionen).
7. Klicken Sie auf die Schaltfläche „+Aktion“. 
    - Dadurch gelangen Sie zum inzwischen vertrauten Dialogfeld für die Aktionserstellung.
8. Geben Sie als Antwort des Bots den Text „You're awesome!“ (Sie sind toll!) ein.
9. Klicken Sie auf „Erstellen“.
10. Beachten Sie, dass der Bot sofort antwortet.
11. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Abzweigen eines zweiten Trainings vom ersten Training
1. Klicken Sie auf die Rasterzeile, die das erste Training zusammenfasst. 
    - Dadurch können Sie das vorhandene Training anzeigen und bearbeiten.
2. Klicken Sie auf die Benutzerantwort „yes“ (Ja). 
    - Dadurch werden Bearbeitungssteuerelemente verfügbar gemacht.
3. Klicken Sie auf das Verzweigungssymbol. 
    - Dadurch gelangen Sie zu einer Eingabeaufforderung für eine andere Benutzereingabe für eine neue Konversation.
4. Geben Sie „no“ (Nein) ein, und drücken Sie die EINGABETASTE, oder klicken Sie auf die Schaltfläche „Erstellen“. 
    - An diesem Punkt haben Sie eine neue Instanz eines Trainingsdialogs, der ursprüngliche Dialog bleibt unverändert.
5. Klicken Sie auf „Score Actions“ (Bewertungsaktionen).
6. Klicken Sie auf die falsche Antwort des Bots, die gerade erschienen ist.
7. Klicken Sie auf die Schaltfläche „+Aktion“, 
    - damit wir eine neue Aktion erstellen können, mit der der Bot antwortet.
8. Geben Sie als Antwort des Bots den Text „No problem! Have a great day!" (Kein Problem! Schönen Tag!) ein.
9. Klicken Sie auf „Erstellen“.
10. Beachten Sie, dass der Bot sofort antwortet.
11. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="test-the-trainings"></a>Testen der Trainings
1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf „New Log Dialog“ (Neuer Protokolldialog).
2. Geben Sie die Nachricht „Hi“ ein. 
3. Beachten Sie, dass der Bot automatisch so antwortet, wie wir ihn trainiert haben.
4. Geben Sie die Benutzerantwort „yes“ (Ja) ein.
5. Beachten Sie die Botantwort, sie zeigt, ob das erste Training funktioniert.
6. Klicken Sie auf die Schaltfläche „Session Timeout“ (Sitzungstimeout). Dadurch wird Conversation Learner informiert, dass wir erneut beginnen und die soeben erfolgten Konversationsdurchläufe ignorieren möchten.
7. Geben Sie die Nachricht „Hi“ ein. 
8. Beachten Sie, dass der Bot automatisch so antwortet, wie wir ihn trainiert haben.
9. Geben Sie die Benutzerantwort „no“ (Nein) ein.
10. Beachten Sie die Botantwort, sie zeigt, ob das zweite Training funktioniert.
11. Klicken Sie auf die Schaltfläche „Done Testing“ (Tests abgeschlossen).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktionen mit und ohne Wartezeit](./03-wait-vs-nonwait-actions.md)
