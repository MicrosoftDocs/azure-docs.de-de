---
title: Karten in einem Unterhaltungslernanwendungsmodell, Teil 1 – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Verwenden von Karten in einem Unterhaltungslernmodell
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389518"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Verwenden von Karten (Teil 1 von 2)

Dieses Tutorial zeigt, wie Sie einfache Karte in Ihrem Bot hinzufügen und verwenden.

> [!NOTE]
> Conversation Learner erwartet, dass Ihre Kartendefinitionsdateien in einem Verzeichnis namens „cards“ gespeichert sind und dass sich dieses in dem Verzeichnis befindet, in dem der Bot gestartet wird.

## <a name="video"></a>Video

[![Karten: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details

Karten sind Benutzeroberflächenelemente, mit denen Benutzer eine Option in der Unterhaltung auswählen können. 

### <a name="open-the-demo"></a>Öffnen der Demo

Klicken Sie auf der Webbenutzeroberfläche auf „Tutorials importieren“, und wählen Sie das Modell „Tutorial-15-Cards“ aus.

### <a name="the-card"></a>Die Karte

Die Kartendefinition befindet sich an folgendem Speicherort: C:\<Installationspfad\>\src\cards\prompt.json.

Das System erwartet, dass sich Ihre Kartendefinitionen in diesem Kartenverzeichnis befinden.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Beachten Sie den Textkörpertyp „TextBlock“ und den Platzhalter „{{question}}“ im Textfeld.
> Es gibt zwei Schaltflächen zum Übermitteln, für die jeweils ein anderer Text gesendet wird.

### <a name="actions"></a>Aktionen

Wir haben drei Aktionen erstellt. Wie Sie unten sehen, ist die erste Aktion eine Karte.

![](../media/tutorial13_actions.PNG)

Der Aktionstyp „Karte“ wurde folgendermaßen erstellt:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Die Karte enthält drei verschiedene Parameter: Frageeingabe, Schaltfläche 1 und Schaltfläche 2. Diese Elemente sind Vorlagenverweise auf der Karte, auf der Sie die Frage und die entsprechenden Antworten eingeben. Sie können auch auf Entitäten oder auf eine Kombination aus Text und Entitäten verweisen und diese verwenden.

Das Augensymbol zeigt, wie die Karte aussieht.

### <a name="practicing-creating-card-actions"></a>Übung: Erstellen von Kartenaktionen

1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
2. Wählen Sie als Aktionstyp „KARTE“ aus.
3. Wählen Sie in der Vorlagenliste „Eingabeaufforderung“ aus.
4. Geben Sie im Feld „question“ (Frage) die Zeichenfolge „Go left or right“ (Nach links oder rechts) ein.
5. Geben Sie im Feld „button1“ (Schaltfläche 1) die Zeichenfolge „left“ (links) ein.
6. Geben Sie im Feld „button2“ (Schaltfläche 2) die Zeichenfolge „right“ (rechts) ein.
7. Klicken Sie auf die Schaltfläche „Abbrechen“.

### <a name="train-dialog-using-an-adaptive-card"></a>Trainieren des Dialogs unter Verwendung einer adaptiven Karte

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „hi“ ein.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die folgende Antwort aus: „prompt: question: Go left or right?“ (Eingabeaufforderung: Frage: Nach links oder rechts?).
    - Mithilfe des Augensymbols können Sie eine Vorschau der Karte anzeigen.
5. Klicken Sie im Chatbereich in der gerenderten Eingabeaufforderung auf die Schaltfläche „Left“ (Links).
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die Aktion „Left“ (Links) aus.
8. Klicken Sie auf die Schaltfläche „Speichern“.
9. Wählen Sie die folgende Antwort aus: „prompt: question: Go left or right?“ (Eingabeaufforderung: Frage: Nach links oder rechts?).
10. Klicken Sie im Chatbereich in der gerenderten Eingabeaufforderung auf die Schaltfläche „Right“ (Rechts).
11. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
12. Wählen Sie die Aktion „Right“ (Rechts) aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hybrid-Bots](./16-hybrid-bots.md)
