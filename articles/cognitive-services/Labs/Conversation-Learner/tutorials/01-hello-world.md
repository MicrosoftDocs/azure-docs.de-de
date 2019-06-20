---
title: Erstellen eines Unterhaltungslernmodells des Typs „Hallo Welt“ – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Informationen zum Erstellen einer Demo des Typs „Hallo Welt“ in einem Unterhaltungslernmodell
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388061"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Erstellen eines Hallo Welt-Modells mit dem Unterhaltungslernmodul

Dieses Tutorial ist eine Einführung in Conversation Learner. Hier lernen Sie, wie Sie Aktionen erstellen, den Bot interaktiv trainieren und Korrekturen an protokollierten Dialogen von Endbenutzern vornehmen.

## <a name="video"></a>Video

[![Tutorial zu „Hallo Welt“ (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Requirements (Anforderungen)
Überprüfen Sie als Erstes, ob alle Setupschritte abgeschlossen wurden, einschließlich der Erstellung einer `.env`-Datei mit Ihrem LUIS-Erstellungsschlüssel.  Weitere Informationen finden Sie unter [Schnellstart](../quickstart.md).

Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="steps"></a>Schritte

Beginnen Sie auf der Homepage in der Webbenutzeroberfläche.

### <a name="create-the-model"></a>Erstellen des Modells
1. Klicken Sie auf die Schaltfläche „Neues Modell“.
2. Geben Sie im Feld „Name“ „Hallo Welt“ ein.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

Jetzt sollte die Ansicht des von Ihnen erstellten Modells angezeigt werden.

### <a name="create-an-action"></a>Erstellen einer Aktion
1. Klicken Sie im linken Bereich auf „Aktionen“ und anschließend auf die Schaltfläche „Neue Aktion“.
    - Eine Aktion kann eine Textnachricht sein, die das Unterhaltungslernmodul an den Benutzer zurückgibt, ein API-Aufruf oder eine Karte.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „Hello“ (Hallo) ein.
    - Dies ist die Antwort, die der Bot zurückgibt.
3. Klicken Sie auf die Schaltfläche „Erstellen“.

Sie haben die erste Aktion erstellt, die der Bot durchführen kann, d.h. eine Textantwort zurückgeben.

### <a name="train-dialogs"></a>Trainingsdialoge
Damit trainieren Sie das Modell für das Reagieren auf Benutzeräußerungen.

#### <a name="first-training-dialog"></a>Erster Trainingsdialog

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie „Hi“ ein, und drücken Sie die EINGABETASTE.
    - Dies ist Beispiel dafür, was der Benutzer am Anfang einer Konversation sagen könnte.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie „Hello“ (Hallo) aus.
    - Sie haben soeben einen vollständigen Durchlauf in diesem Beispieldialog abgeschlossen. 
5. Geben Sie die Benutzerantwort „Goodbye“ (Auf Wiedersehen) ein.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Klicken Sie auf die Schaltfläche „+Aktion“.
8. Geben Sie „Goodbye!“ (Auf Wiedersehen) im Feld „Bot's response...“ (Antwort des Bots...) ein, und klicken Sie dann auf die Schaltfläche „Erstellen“.
    - Beachten Sie, dass der Bot mit der Aktion geantwortet hat, die Sie gerade erstellt haben.
9. Klicken Sie auf die Schaltfläche „Speichern“. 
    - Dadurch wird dieser Trainingsdialog gespeichert und beendet.

Sie haben jetzt einen Trainingsdialog im Modell, zusammen mit einer einzelnen Entität und zwei Aktionen.

#### <a name="second-training-dialog"></a>Zweiter Trainingsdialog
Führen Sie ein weiteres Training aus, um die Antwort des Bots zu testen.

1. Klicken Sie auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie „Hi“ ein.
    - Diese Eingabe ähnelt dem ersten Dialog. Deswegen wird erwartet, dass der Bot eine hohe Punktzahl zurückgibt.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Die Position und die Punktzahl sind möglicherweise noch nicht präzise genug. Gegebenenfalls ist zusätzliches Training erforderlich.
4. Wählen Sie „Hello“ (Hallo) aus.
5. Geben Sie die Benutzerantwort „bye“ (Tschüss) ein.
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie „Goodbye!“ (Auf Wiedersehen!) aus.
8. Klicken Sie auf die Schaltfläche „Speichern“.

### <a name="log-dialogs"></a>Protokolldialoge
Diese verwenden Sie zum Testen, Anzeigen und Korrigieren von Konversationen, die Sie oder echte Benutzer mit Ihrem Bot hatten.

#### <a name="test-the-model-as-an-end-user"></a>Testen des Modells als Endbenutzer
1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf die Schaltfläche „New Log Dialog“ (Neuer Protokolldialog).
2. Geben Sie „Hallo“ ein.
3. Warten Sie kurz, der Bot sollte automatisch mit „Hallo“ antworten.
4. Geben Sie „Tschüss“ ein.
5. Warten Sie kurz, der Bot sollte wieder automatisch mit „Hallo“ antworten.
6. Klicken Sie auf die Schaltfläche „Done Testing“ (Tests abgeschlossen).

#### <a name="view-and-correct-a-user-conversation"></a>Anzeigen und Korrigieren eine Benutzerkonversation
Mithilfe von Protokolldialogen können Sie die Liste der Konversationen anzeigen, die Benutzer mit Ihrem Bot geführt haben. Sie können sie auch bearbeiten, um die Antworten des Bots zu korrigieren und die Interaktionen als Trainingsdialoge zu speichern. Gehen Sie dafür folgendermaßen vor:
1. Klicken Sie im Raster auf das Protokoll der Konversation.
2. Klicken Sie auf die letzte Botaktion, z.B. „Hallo!“
3. Wählen Sie „Goodbye!“ (Auf Wiedersehen!) aus, um den Bot zu korrigieren.
4. Klicken Sie auf die Schaltfläche „Save As Train Dialog“ (Als Trainingsdialog speichern).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einführung in das Training](./02-intro-to-training.md)
