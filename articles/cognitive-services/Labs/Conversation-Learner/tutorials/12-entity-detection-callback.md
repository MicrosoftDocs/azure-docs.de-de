---
title: Verwenden des Rückrufs zur Entitätserkennung in Verbindung mit einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie den Rückruf zur Entitätserkennung in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ed254ea3db1c8f0e680cba71185e9b4505d8f200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387655"
---
# <a name="how-to-use-entity-detection-callback"></a>Verwenden des Rückrufs zur Entitätserkennung

Dieses Tutorial veranschaulicht den Rückruf zur Entitätserkennung und demonstriert ein allgemeines Muster zum Auflösen von Entitäten.

## <a name="video"></a>Video

[![Rückruf zur Entitätserkennung: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der `tutorialEntityDetectionCallback`-Bot ausgeführt werden.

    npm run tutorial-entity-detection

## <a name="details"></a>Details
Rückrufe zur Entitätserkennung ermöglichen die Änderung des Verhaltens der Entitätserkennung sowie die Bearbeitung von Entitäten mittels Code. Diese Funktion wird hier anhand eines typischen Entwurfsmusters für Rückrufe zur Entitätserkennung veranschaulicht. Ein Beispiel wäre etwa die Auflösung von „Big Apple“ zu „New York“.

## <a name="steps"></a>Schritte

### <a name="create-the-model"></a>Erstellen des Modells

1. Klicken Sie auf der Webbenutzeroberfläche auf „Neues Modell“.
2. Geben Sie im Feld „Name“ die Zeichenfolge „EntityDetectionCallback“ ein, und drücken Sie die EINGABETASTE.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

Als Nächstes erstellen wir drei Entitäten, da wir für dieses Beispiel drei Entitäten benötigen.

### <a name="create-the-custom-trained-entity"></a>Erstellen der benutzerdefiniert trainierten Entität

1. Klicken Sie im linken Bereich auf „Entitäten“ und anschließend auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Custom Trained“ (Benutzerdefiniert trainiert) aus.
3. Geben Sie als Entitätsname „City“ (Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="create-the-first-programmatic-entity"></a>Erstellen der ersten programmgesteuerten Entität

1. Klicken Sie auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Programmgesteuert“ aus.
3. Geben Sie als Entitätsname „CityUnknown“ (Unbekannte Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

### <a name="create-the-first-programmatic-entity"></a>Erstellen der ersten programmgesteuerten Entität

1. Klicken Sie auf die Schaltfläche „Neue Entität“.
2. Wählen Sie unter „Entitätstyp“ die Option „Programmgesteuert“ aus.
3. Geben Sie als Entitätsname „CityResolved“ (Aufgelöste Stadt) ein.
4. Klicken Sie auf die Schaltfläche „Erstellen“.

Erstellen Sie als Nächstes drei Aktionen.

### <a name="action-creation"></a>Aktionserstellung

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „Which city do you want?“ (Welche Stadt möchten Sie?) ein.
3. Geben Sie im Feld „Expected Entity in User reply...“ (Erwartete Entität in Benutzerantwort...) die Zeichenfolge „City“ (Stadt) ein.
4. Geben Sie im Feld „Disqualifying Entities“ (Disqualifizierende Entitäten) die Zeichenfolge „City“ (Stadt) ein.
5. Klicken Sie auf die Schaltfläche „Erstellen“.
6. Klicken Sie auf die Schaltfläche „Neue Aktion“.
7. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „Which city do you want?“ (Welche Stadt möchten Sie?) ein.
8. Geben Sie im Feld „Expected Entity in User reply...“ (Erwartete Entität in Benutzerantwort...) die Zeichenfolge „I don't know this city.“ (Ich kenne diese Stadt nicht.) ein.
9. Klicken Sie auf die Schaltfläche „Erstellen“.
10. Klicken Sie auf die Schaltfläche „Neue Aktion“.
11. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Antwort „$CityResolved is very nice.“ ($CityResolved ist sehr schön.) ein.
12. Klicken Sie auf die Schaltfläche „Erstellen“.

Rückrufcode:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Trainieren des Modells

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hi“ ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „Which city do you want?“ (Welche Stadt möchten Sie?) aus.
5. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „big apple“ ein.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Durch Klicken auf die Schaltfläche wird der Rückruf zur Entitätserkennung ausgelöst.
    - Der Rückrufcode legt den Wert der Entität „CityResolved“ korrekt auf „new york“ fest.
7. Wählen Sie die Antwort „new york is very nice“ (New York ist sehr schön) aus.

Dieses Muster wird in vielen Botszenarien verwendet. Benutzeräußerungen und extrahierte Entitäten werden für Ihre Geschäftslogik bereitgestellt, und diese Logik transformiert die Äußerung in das kanonische Format, das daraufhin für nachfolgende Durchläufe des Dialogs in programmgesteuerten Entitäten gespeichert wird.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Sitzungsrückrufe](./13-session-callbacks.md)
