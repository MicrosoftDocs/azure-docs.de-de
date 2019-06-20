---
title: Alternative Eingaben im Unterhaltungslernmodul – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Informationen zum Verwenden alternativer Eingaben im Unterhaltungslernmodul
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 383c063e1ba7a29986e4b1c48024072799234414
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387943"
---
# <a name="how-to-use-alternative-inputs"></a>Alternative Eingaben

Dieses Tutorial zeigt, wie Sie das Feld „Alternative Eingaben“ für Benutzeräußerungen in der Trainingsschnittstelle verwenden.

## <a name="video"></a>Video

[![Tutorial zu alternativen Eingaben (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details
Alternative Eingaben sind alternative, semantisch gleichwertige Benutzeräußerungen, die der Benutzer möglicherweise an einer bestimmten Stelle in einem Trainingsdialog getätigt hat. Mit diesen alternativen Eingaben können Sie Variationen von Äußerungen kompakter bestimmen, ohne jede Variante in einem separaten Trainingsdialog zu verarbeiten.

## <a name="steps"></a>Schritte

### <a name="create-the-model"></a>Erstellen des Modells

1. Klicken Sie auf der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie im Feld „Name“ die Zeichenfolge „AlternativeInputs“ ein, und drücken Sie die EINGABETASTE.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf „Entitäten“ und dann auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Custom Trained“ (Benutzerdefiniert trainiert) aus.
3. Geben Sie als Entitätsname „City“ (Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

![](../media/T10_actions.png)

Jetzt erstellen wir drei Aktionen.

### <a name="create-the-first-action"></a>Erstellen der ersten Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „Which city?“ (Welche Stadt?) ein.
3. Geben Sie im Feld „Expected Entity in User reply...“ (Erwartete Entität in Benutzerantwort...) die Zeichenfolge „City“ (Stadt) ein.
4. Geben Sie im Feld „Disqualifying Entities“ (Disqualifizierende Entitäten) die Zeichenfolge „city“ (Stadt) ein.
5. Klicken Sie auf die Schaltfläche „Erstellen“.

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Erstellen der zweiten Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) den Satz „Das Wetter in $Stadt ist wahrscheinlich sonnig“ ein.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Erstellen der dritten Aktion

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „Try asking for the weather“ (Nach dem Wetter fragen) ein.
3. Geben Sie im Feld „Disqualifying Entities“ (Disqualifizierende Entitäten) die Zeichenfolge „city“ (Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

![](../media/T10_action_create_3.png)

Jetzt haben Sie drei Aktionen.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Trainieren des Modells

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „What's the weather?“ (Wie ist das Wetter?) ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Which city?“ (Welche Stadt?) aus.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Denver“ ein.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die Antwort „The weather in Denver is probably sunny.“ (Das Wetter in Denver ist wahrscheinlich sonnig.) aus.
8. Klicken Sie auf die Schaltfläche „Änderung übermitteln“.

![](../media/T10_training_1.png)

Wir trainieren das Modell weiter, indem wir einen weiteren Trainingsdialog erstellen.

### <a name="second-model-train-dialog"></a>Zweiter Trainingsdialog des Modells

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „What can you do?“ (Was können Sie tun?) ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Try asking for weather“ (Nach dem Wetter fragen) aus.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?) ein.
6. Klicken Sie auf „Seattle“ und anschließend in der Entitätsliste auf „City“ (Stadt).
7. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
8. Wählen Sie die Antwort „The weather in Seattle is probably sunny.“ (Das Wetter in Seattle ist wahrscheinlich sonnig.) aus.
9. Klicken Sie auf die Schaltfläche „Änderung übermitteln“.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Dritter Modelltrainingsdialog mit alternativen Eingaben

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „help“ (Hilfe) ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Das Modell ist nicht mit Sicherheit die beste Option, daher hat es standardmäßig das höchste Perzentil gewählt.
4. Klicken Sie auf die Schaltfläche „Training abbrechen“ und dann auf „Bestätigen“.

![](../media/T10_training_3.png)

Jetzt optimieren wir das System mit alternativen Eingaben. Sie können alternative Eingaben während des Trainings oder später hinzufügen.

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge), und wählen Sie dann „What can you do?“ (Was können Sie tun?) aus der Liste der Trainingsdialoge aus.
1. Klicken Sie im Chatbereich auf die Äußerung „What can you do?“ (Was können Sie tun?).
1. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „help“ (Hilfe) ein, und drücken Sie die EINGABETASTE.
1. Klicken Sie auf die Schaltfläche „Änderungen speichern“.

![](../media/T10_training_4.png)

Nun fügen wir eine weitere alternative Eingabe für Houston hinzu.

1. Klicken Sie im Chatbereich auf die Äußerung „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?).
1. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „forecast for Houston“ (Vorhersage für Houston) ein, und drücken Sie die EINGABETASTE.
   - Die Fehlermeldung hebt die Tatsache hervor, dass alternative Eingaben semantisch gleichwertig sein und die gleichen Entitäten wie die ursprüngliche Äußerung enthalten müssen, nicht nur die gleichen Werte von Entitäten. Das Vorhandensein der gleichen Entitäten ist erforderlich.
1. Klicken Sie auf „Houston“, und wählen Sie „city“ (Stadt) aus der Entitätenliste aus.
1. Geben Sie im Feld „Add alternative input...“ (Alternative Eingabe hinzufügen) die Zeichenfolge „forecast for Seattle“ (Vorhersage für Seattle) ein, und drücken Sie die EINGABETASTE.
1. Klicken Sie auf „Seattle“, und wählen Sie „city“ (Stadt) aus der Entitätenliste aus.
1. Klicken Sie auf die Schaltfläche „Änderungen speichern“.
1. Klicken Sie auf die Schaltfläche „Save Edit“ (Bearbeitung speichern).

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testen des Modells

1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf „New Log Dialog“ (Neuer Protokolldialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „help me“ (Hilfe) ein.
3. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „forecast for Denver“ (Vorhersage für Denver) ein.

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Protokollieren von Dialogen](./11-log-dialogs.md)
