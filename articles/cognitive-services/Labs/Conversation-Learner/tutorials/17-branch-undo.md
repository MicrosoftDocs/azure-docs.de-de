---
title: Verwenden von Branching- und Rückgängig-Aktionen in einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Branching- und Rückgängig-Aktionen in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 6ffa0881df07e453c8beb175b8580deebbfc1ec9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389890"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Verwenden von Vorgängen zum Branchen und Rückgängigmachen
In diesem Tutorial befassen wir uns mit Rückgängig- und Branching-Vorgängen.


## <a name="details"></a>Details
### <a name="undo"></a>Rückgängig
Ermöglicht es dem Entwickler, die letzte Benutzereingabe oder Aktionsauswahl rückgängig zu machen. Hinter den Kulissen erstellt „Rückgängig machen“ tatsächlich einen neuen Dialog und spielt ihn bis zum vorhergehenden Schritt ab.  Dies bedeutet, dass der Rückruf zur Entitätserkennung und die API-Aufrufe im Dialog erneut aufgerufen werden.

### <a name="branch"></a>Verzweigung
Erstellt einen neuen Trainingsdialog, der genau so beginnt wie der vorhandene Trainingsdialog – dadurch entfällt die Mühe, Dialogwendungen manuell erneut einzugeben. Hinter den Kulissen erstellt „Branch“ einen neuen Dialog und spielt den vorhandenen Trainingsdialog noch einmal bis zum ausgewählten Schritt ab.  Dies bedeutet, dass der Rückruf zur Entitätserkennung und die API-Aufrufe im Dialog erneut aufgerufen werden.


## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der Pizzabestellungsbot ausgeführt werden:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Öffnen oder Importieren der Demo

Wenn Sie das Tutorial zur Pizzabestellung bereits durchgearbeitet haben, öffnen Sie das entsprechende Modell einfach über die Liste auf der Webbenutzeroberfläche. Andernfalls müssen Sie auf „Import Tutorials“ (Tutorials importieren) klicken und das Modell mit dem Namen „Demo-PizzaOrder“ auswählen.

## <a name="undo"></a>Rückgängig

Hier ist ein Beispiel dafür, wie das `Undo`-Feature in Aktion angezeigt wird:

### <a name="training-dialogs"></a>Trainingsdialoge
1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche `New Train Dialog`.
2. Geben Sie „Order a pizza“ (Pizza bestellen) ein.
3. Klicken Sie auf die Schaltfläche `Score Actions`.
4. Wählen Sie „What would you like on your pizza?“ (Welchen Belag wünschen Sie auf Ihrer Pizza) durch Klicken aus.
5. Geben Sie „anything“ (irgendetwas) ein.
6. Klicken Sie auf die Schaltfläche `Undo`.
    - Der letzte Eintrag wird entfernt, sodass die letzte Botantwort von „What would you like on your pizza?“ (Welchen Belag wünschen Sie auf Ihrer Pizza) beibehalten wird.

## <a name="branch"></a>Verzweigung

Für diese Demo öffnen wir einen vorhandenen Trainingsdialog und erstellen daraus durch Verzweigen einen neuen Trainingsdialog.

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge).
2. Beachten Sie das Raster. Darin sollte nur ein Training angezeigt werden, das mit „new order“ (Neue Bestellung) beginnt.
3. Klicken Sie im Raster auf „new order“ (neue Bestellung), um den vorhandenen Trainingsdialog zu öffnen.
4. Klicken Sie auf das letzte „no“ (Nein) im Dialog.
5. Klicken Sie auf das Verzweigungssymbol. Es ist in dieser Abbildung rot umrandet:
    - ![](../media/tutorial15_branch.PNG)
    - Der gesamte Trainingsdialog vor dem „no“ (Nein) wird in einen neuen Trainingsdialog kopiert.
    - Dies erspart Ihnen die erneute Eingabe der vorhergehenden Wendungen, um eine neue Konversationsverzweigung ab diesem Punkt zu untersuchen.
6. Geben Sie „yes“ (Ja) ein, und drücken Sie die EINGABETASTE.
7. Klicken Sie auf die Schaltfläche `Score Actions`.
    - An diesem Punkt wählt der Bot automatisch eine Antwort. Die Antwort gefällt uns jedoch nicht, also ändern wir sie.
8. Klicken Sie auf die letzte Botantwort.
    - Dadurch können wir eine andere Antwort auswählen.
9. Wählen Sie „UseLastToppings“ aus.
10. Klicken Sie auf die Schaltfläche `Score Actions`.
    - Der Bot wählt erneut automatisch eine Antwort aus. Sie sollte „You have sausage, cheese and mushrooms on your pizza.“ (Sie haben Wurst, Käse und Pilze auf Ihrer Pizza). 
    - Dieses Mal gefällt uns die Antwort, also behalten wir sie bei.
11. Klicken Sie auf die Schaltfläche `Score Actions`.
    - Der Bot wählt erneut automatisch eine Antwort aus, sie sollte „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) lauten.
12. Geben Sie „no“ (Nein) ein.
13. Klicken Sie auf die Schaltfläche `Save Branch`.
14. Beachten Sie, dass das Raster nun zwei Trainings enthält, die mit „new order“ (Neue Bestellung) beginnen.
    - Eines davon ist das, von dem Sie die Verzweigung erstellt haben.
    - Und das andere ist die verzweigte Version, die Sie gerade gespeichert haben.
    - Klicken Sie auf beide, um diese Erwartungen jeweils zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Versionsverwaltung und Tagging](./18-version-tag.md)
