---
title: Sitzungsrückrufe in einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Sitzungsrückrufe in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: eeed0718a1feb170dbbaa783ec0a840c7829c02e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390014"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Verwenden von Sitzungsrückrufen in einem Unterhaltungslernmodell

Dieses Tutorial bietet eine Einführung in Sitzungen, deren Verarbeitung und die onSessionStart- und onSessionEnd-Rückrufe von Conversation Learner.

## <a name="video"></a>Video

[![Tutorial zu Sitzungsrückrufen (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der Bot „tutorialSessionCallbacks“ ausgeführt werden.

    npm run tutorial-session-callbacks

## <a name="details"></a>Details
Dieses Tutorial behandelt das Konzept einer Sitzung, wie Sitzungen standardmäßig ablaufen, und wie Sie dieses Verhalten überschreiben können.

In Conversation Learner stellt eine Sitzung einen ununterbrochenen interaktiven Austausch mit dem Bot dar. Sitzungen können mehrere Durchläufe beinhalten, jedoch nach mehr als 30 Minuten aufgrund von Inaktivität programmgesteuert beendet werden.  Auf der Hilfeseite für „Grenzen“ finden Sie Informationen zum Ändern dieses Standardwerts während der Dauer des Sitzungstimeout.

Dieser lange Zeitraum der Inaktivität führt dazu, dass der Bot eine neue Sitzung erstellt und das rekurrente neuronale Netz auf seinen ursprünglichen Zustand zurücksetzt. Standardmäßig werden alle Entitätswerte gelöscht. Dieses Standardverhalten, dass Entitätswerte gelöscht werden, kann wie unten dargestellt geändert werden.

### <a name="load-the-demo-model"></a>Laden des Demomodells

Klicken Sie auf der Webbenutzeroberfläche auf „Tutorials importieren“, und wählen Sie das Modell „Tutorial-13-SessionCallbacks“ aus.

### <a name="code-for-the-callbacks"></a>Rückrufcode

Beispielcode für die zwei Rückrufe dieses Modells Sie in: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Diese Methode bestimmt die Entität „BotName“.
- OnSessionEndCallback: Mit dieser Methode können Sie angeben, was Sie erhalten möchten. Alle Entitäten außer dem Namen und der Telefonnummer des Benutzers werden gelöscht.

Jeder Rückruf ist optional.

### <a name="actions"></a>Aktionen

Im Modell sind vier Aktionen definiert. Die vorhandenen Aktionen werden in der Rasteransicht für „Aktionen“ angezeigt.

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Erstellen einer Aktion zum Beenden der Sitzung (für Rückrufaufruf)

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Wählen Sie „ENDSESSION“ als Entitätstyp.
3. Geben Sie in das Feld „Daten...“ den Text „Fertig“ ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="edit-an-existing-action"></a>Bearbeiten einer vorhandenen Aktion

1. Wählen Sie in der Rasteransicht die Aktion „So, $UserName, you are in $UserLocation“ ($UserName, Sie befinden sich also in $UserLocation).
2. Deaktivieren Sie das Kontrollkästchen „Auf Antwort warten“.
3. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="chaining-actions"></a>Verkettung von Aktionen

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hi“ ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Hi, I'm Botty. What's your name?“ “ (Hallo, ich bin Botty. Wie heißen Sie?) aus.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Lars“ ein.
6. Wählen Sie die Antwort „Hi Lars. Wie lautet Ihre Telefonnummer?“ aus.
7. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „555-555-5555“ ein.
8. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
9. Wählen Sie die Antwort „Can you tell Botty your location, Lars?“ (Teilen Sie Botty Ihren Standort mit, Lars.) aus.
10. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Seattle“ ein.
11. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
12. Wählen Sie die Antwort „So, Lars, you are in Seattle“ (Lars, Sie befinden sich also in Seattle) aus.
13. Wählen Sie die Antwort „Done“ (Fertig) aus.
14. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="testing-the-model"></a>Testen des Modells

1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf die Schaltfläche „New Log Dialog“ (Neuer Protokolldialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hi“ ein.
3. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Lars“ ein.
4. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „555-555-5555“ ein.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Seattle“ ein.
    - An diesem Punkt sollten alle Entitätswerte mit Ausnahme des Standorts gespeichert worden sein.
6. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hello“ ein.
7. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Detroit“ ein.
8. Klicken Sie auf die Schaltfläche „Session Timeout“ (Sitzungstimeout).
    - Durch Klicken auf diese Schaltfläche wird die Antwort des Bots auf lange Zeiträume ohne Aktivität ausgeführt.
9. Klicken Sie auf die Schaltfläche „OK“.
10. Klicken Sie auf die Schaltfläche „Done Testing“ (Tests abgeschlossen).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [API-Aufrufe](./14-api-calls.md)
