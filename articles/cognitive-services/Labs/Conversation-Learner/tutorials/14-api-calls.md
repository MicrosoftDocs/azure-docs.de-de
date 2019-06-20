---
title: Verwenden von API-Aufrufe in einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie API-Aufrufe in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389939"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Hinzufügen von API-Aufrufen zu einem Unterhaltungslernmodell

Dieses Tutorial zeigt, wie Sie Ihrem Modell API-Aufrufe hinzufügen. API-Aufrufe sind Funktionen, die Sie in Ihrem Bot definieren und erstellen und die von Conversation Learner aufgerufen werden können.

## <a name="video"></a>Video

[![Tutorial zu API-Aufrufen (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der tutorialAPICalls.ts-Bot ausgeführt werden.

    npm run tutorial-api-calls

## <a name="details"></a>Details

- Mithilfe von API-Aufrufen können Entitäten gelesen und bearbeitet werden.
- API-Aufrufe haben Zugriff auf das Speicher-Manager-Objekt.
- API-Aufrufe können Argumente annehmen.

### <a name="open-the-demo"></a>Öffnen der Demo

Klicken Sie auf der Webbenutzeroberfläche auf „Tutorials importieren“, und wählen Sie das Modell „Tutorial-14-APICalls“ aus.

### <a name="entities"></a>Entitäten

Wir haben im Modell eine Entität mit dem Namen `number` definiert.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-Aufrufe
Der Code für die API-Aufrufe ist in dieser Datei definiert: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Der `RandomGreeting`-Rückruf gibt eine zufällige Grußformel zurück, die im `greeting`-Array definiert ist.
- Der `Multiply`-Rückruf multipliziert zwei Zahlen, die von der Aktion übergeben werden, die ihn aufruft. Dann gibt er ein Ergebnis zurück, das auf der Benutzeroberfläche dargestellt werden kann.
    - Beachten Sie, dass der Speichermanager das erste Argument ist. 
    - Beachten Sie, dass API-Rückrufe mehrere Eingaben aufnehmen können, in diesem Fall `num1string` und `num2string`.
- Der `ClearEntities`-Rückruf löscht die Zahlenentität, sodass der Benutzer eine andere Zahl eingeben kann. 
    - Veranschaulicht, wie Entitäten mithilfe von API-Aufrufen bearbeitet werden können.

### <a name="actions"></a>Aktionen
Wir haben vier Aktionen erstellt. Drei davon sind API-Aktionen vom Typ „Non-Wait“, die vierte ist eine „Text“-Aktion, die dem Benutzer ähnliche Fragen wie in anderen Tutorials stellt. Führen Sie die folgenden Schritte aus, um zu sehen, wie die einzelnen Aktionen erstellt wurden:
1. Klicken Sie im linken Bereich „Aktionen“ und dann auf eine der vier Aktionen, die im Raster aufgeführt sind.
2. Beachten Sie die Werte jedes Felds im Formular, das angezeigt wird.
3. Beachten Sie die Schaltfläche `Refresh` neben dem API-Feld.
    - Wenn wir den Bot beenden und Änderungen an den APIs vornehmen, während die Seite der Benutzeroberfläche geöffnet ist, können Sie auf die Schaltfläche `Refresh` klicken, um die letzten Änderungen zu übernehmen.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, Multiply und RandomGreeting
Alle drei der folgenden Aktionen sind vom Typ API. Sie basieren jeweils auf API-Rückruffunktionen, die einige Aufgaben ausführen und möglicherweise einen Wert zurückgeben, der dem Benutzer angezeigt werden soll.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>„Welche Zahl möchten Sie mit 12 multiplizieren?“
Dies ist die Aktion „Text“ und stellt dem Benutzer einfach eine Frage. Diese Aktion interagiert zwar nicht tatsächlich mit einem der API-Rückrufe, fordert den Benutzer jedoch auf, mit einer Zahl zu antworten, die in den Speicher einer Entität aufgenommen wird. Diese kann anschließend von der Aktion „Multiplizieren“ verwendet werden, die eine der API-Rückrufe verwendet.


### <a name="train-dialog"></a>Trainingsdialog

Im Folgenden sehen wir uns einen Trainingsdialog an.

1. Klicken Sie im linken Bereich auf `Train Dialogs` und dann auf die Schaltfläche `New Train Dialog`.
2. Geben Sie „Hallo“ ein.
3. Klicken Sie auf die Schaltfläche `Score Actions`.
4. Wählen Sie `RandomGreeting` aus. 
    - Dadurch wird der Random-Greeting-API-Aufruf ausgeführt.
    - Es wird NICHT auf eine Benutzerantwort gewartet.
5. Wählen Sie `What number to do you want to multiply by 12?`.
6. Geben Sie eine Zahl ein – eine beliebige Zahl und nur eine Zahl.
    - Beachten Sie, dass Ihre Zahl automatisch als die `number`-Entität bezeichnet wurde.
7. Klicken Sie auf die Schaltfläche `Score Actions`.
8. Wählen Sie die `Multiply`-Aktion aus.
    - Beachten Sie das Ergebnis der Multiplikation mit 12.
    - Beachten Sie, dass der Speicher weiterhin den Wert enthält, den Sie für `number` eingegeben haben.
9. Wählen Sie die `ClearEntities`-Aktion aus.
    - Beachten Sie, dass der Entitätswert für `number` aus dem Speicher gelöscht wurde.
10. Klicken Sie auf die Schaltfläche `Save`.

Sie haben nun erfahren, wie API-Rückrufe registriert werden, eine gängige Muster gesehen und das Definieren von Argumenten sowie das Zuordnen von Werten und Entitäten in ihnen kennengelernt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Karten – Teil 1](./15-cards.md)
