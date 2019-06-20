---
title: 'Demo: Unterhaltungslernmodell, Kennwortzurücksetzung – Microsoft Cognitive Services | Microsoft-Dokumentation'
titleSuffix: Azure
description: Erfahren Sie, wie Sie eine Demo in einem Unterhaltungslernmodell erstellen.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 15aa3a8346087908cf77f1f68db916cc2c184448
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389877"
---
# <a name="demo-password-reset"></a>Demo: Zurücksetzen des Kennworts
Dieses Tutorial veranschaulicht einen einfachen technischen Support-Bot, der von Conversation Learner unterstützt wird und bei Kennwortzurücksetzungen helfen kann. Das Modell des Bots kann schwierige Dialogabläufe und Sequenzen aus mehreren Aktionen (einschließlich domänenexterner Klasse) erlernen. Die Aufgabe kann ohne Code oder Entitäten abgeschlossen werden.

## <a name="video"></a>Video

[![Demo: Kennwort – Vorschau](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Anforderungen
Für dieses Tutorial muss der Kennwortzurücksetzung-Bot ausgeführt werden.

    npm run demo-password

### <a name="open-the-demo"></a>Öffnen der Demo

Klicken Sie in der Modellliste der Webbenutzeroberfläche auf „Tutorial Demo Password Reset“ (Demotutorial: Kennwortzurücksetzung). 

### <a name="actions"></a>Aktionen

Das Modell enthält eine Reihe von Aktionen, die Benutzern bei der Lösung gängiger Kennwortprobleme helfen sollen.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Trainingsdialoge

Das Modell enthält auch mehrere Trainingsdialoge, darunter einige, die Training für domänenexterne Klassen demonstrieren. Darunter fallen beispielsweise Wegbeschreibungen, die von Benutzern angefordert werden. Der Beispielbot wurde zu Demonstrationszwecken mit einigen dieser Klassen trainiert und reagiert einfach mit der Antwort, dass er bei einem bestimmten Anliegen nicht helfen kann. Die Liste der vorhandenen Trainingsdialoge befindet sich im linken Bereich unter „Train Dialogs“ (Trainingsdialoge).

![](../media/tutorial_pw_reset_entities.PNG)

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „I lost my password“ (Ich habe mein Kennwort vergessen) ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Is that for your local account or Microsoft account?“ (Für Ihr lokales Konto oder Ihr Microsoft-Konto?) aus.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „local account please“ (lokales Konto bitte) ein.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die Antwort „Which version of Windows do you have?“ (Welche Windows-Version verwenden Sie?) aus.
8. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Windows XP“ ein.
9. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
10. Klicken Sie auf die Schaltfläche „+ Aktion“.
11. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „SOLUTION: How to reset password on Windows XP“ (LÖSUNG: Kennwortzurücksetzung unter Windows XP) ein.
12. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Trainingsdialoge für domänenexterne Szenarien

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf den vorhandenen Trainingsdialog „toy stores“ (Spielwarengeschäfte).
2. Klicken Sie im Chatbereich auf die Äußerung „toy stores“ (Spielwarengeschäfte).
3. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „web search“ (Websuche) ein, und drücken Sie die EINGABETASTE.
4. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „flight booking“ (Flugbuchung) ein, und drücken Sie die EINGABETASTE.
5. Klicken Sie auf die Schaltfläche „Änderungen speichern“.
6. Klicken Sie auf die Schaltfläche „Save Edit“ (Bearbeitung) speichern.
7. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf die Schaltfläche „New Log Dialog“ (Neuer Protokolldialog).
8. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „i can't find my password“ (Ich kann mein Kennwort nicht finden) ein.
9. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Microsoft-Konto“ ein.
10. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „thanks“ (Danke) ein.
11. Klicken Sie auf die Schaltfläche „Done Testing“ (Tests abgeschlossen).
12. Klicken Sie in der Rasteransicht auf den Protokolldialog „i can't find my password“ (Ich kann mein Kennwort nicht finden).
13. Klicken Sie im Chatbereich auf die falsch gerenderte Antwort „Solution: How to reset a Microsoft Account Password“ (Lösung: Zurücksetzen eines Microsoft-Kontokennworts).
14. Klicken Sie auf die Schaltfläche „+ Aktion“.
15. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „You are welcome“ (Gern geschehen) ein.
16. Klicken Sie auf die Schaltfläche „Erstellen“.
17. Klicken Sie auf die Schaltfläche „Save As Train Dialog“ (Als Trainingsdialog speichern).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Demo – Pizzabestellung](./demo-pizza-order.md)
