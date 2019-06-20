---
title: Hinzufügen von Beispieläußerungen
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beispieläußerungen sind Textbeispiele für Benutzerfragen oder -befehle. Fügen Sie einer Absicht Beispieläußerungen hinzu, um Language Understanding-Apps zu trainieren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: badf351f8336e501b3ee1c035fcb389a570750c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072854"
---
# <a name="add-an-entity-to-example-utterances"></a>Hinzufügen einer Entität zu Beispieläußerungen 

Beispieläußerungen sind Textbeispiele für Benutzerfragen oder -befehle. Fügen Sie [Beispieläußerungen](luis-concept-utterance.md) zu einer [Absicht](luis-concept-intent.md) hinzu, um Language Understanding-Apps zu trainieren.

Im Allgemeinen fügen Sie einer Absicht zunächst eine Beispieläußerung hinzu und erstellen dann Entitäten und bezeichnen Äußerungen auf der Seite der Absicht. Wenn Sie zuerst Entitäten erstellen möchten, finden Sie weitere Informationen unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Markieren von Entitäten in Beispieläußerungen

Wenn Sie Text in der Beispieläußerung für eine Entität markieren, wird ein direktes Popupmenü angezeigt. Erstellen in diesem Menü eine Entität, oder wählen Sie sie aus. 

Bestimmte Entitätstypen, z.B. vordefinierte Entitäten und RegEx-Entitäten, können in der Beispieläußerung nicht markiert werden, da sie automatisch markiert werden. 

## <a name="add-a-simple-entity"></a>Hinzufügen einer einfachen Entität

Mit dem folgenden Verfahren erstellen und markieren Sie eine benutzerdefinierte Entität innerhalb der folgenden Äußerung auf der Seite der Absicht:

```text
Are there any SQL server jobs?
```

1. Wählen Sie `SQL server` in der Äußerung aus, um den Begriff als einfache Entität zu bezeichnen. Im Dropdownfeld der Entität, das angezeigt wird, können Sie eine vorhandene Entität auszuwählen oder eine neue Entität hinzufügen. Geben Sie den Namen der neuen Entität (`Job`), die Sie hinzufügen möchten, in das Textfeld ein, und wählen Sie anschließend **Neue Entität erstellen** aus.

    ![Screenshot: Eingeben des Entitätsnamens](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Wenn Sie Wörter auswählen, um sie als Entitäten zu markieren, gilt Folgendes:
    > * Wählen Sie bei einem einzelnen Wort nur dieses Wort aus. 
    > * Wählen Sie bei mindestens zwei Wörtern den Anfang und das Ende der Wortgruppe aus.

1. Überprüfen Sie im Popupfeld **What type of entity do you want to create?** (Welchen Entitätstyp möchten Sie erstellen?) den Entitätsnamen, und wählen Sie den Entitätstyp **Einfach** und anschließend **Fertig** aus.

    Normalerweise wird eine [Ausdrucksliste](luis-concept-feature.md) verwendet, um das Signal einer einfachen Entität zu verstärken.

## <a name="add-a-list-entity"></a>Hinzufügen einer Listenentität

Listenentitäten stellen eine Gruppe verwandter Wörter mit genauer Textübereinstimmung in Ihrem System dar. 

Für die Abteilungsliste eines Unternehmens können Sie normalisierte Werte verwenden: `Accounting` und `Human Resources`. Jeder normalisierte Name verfügt über Synonyme. Für eine Abteilung können diese Synonyme Abteilungskürzel, Nummern oder Jargon umfassen. Sie müssen nicht alle Werte kennen, wenn Sie die Entität erstellen. Sie können weitere Werte hinzufügen, nachdem Sie wirkliche Benutzeräußerungen mit Synonymen überprüft haben.

1. Wählen Sie in einer Beispieläußerung auf der Seite **Absichten** das Wort oder den Ausdruck aus, die in der neuen Liste enthalten sein sollen. Wenn das Dropdownmenü für die Entität angezeigt wird, geben Sie den Namen für die neue Listenentität in das obere Textfeld ein und wählen dann **Neue Entität erstellen** aus.   

1. Benennen Sie die Entität im Popupfeld **What type of entity do you want to create?** (Welchen Entitätstyp möchten Sie erstellen?), und wählen Sie **Liste** als Typ aus. Fügen Sie Synonyme für dieses Listenelement hinzu, und wählen Sie dann **Fertig** aus. 

    ![Screenshot: Eingeben von Synonymen für Listenentität](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Sie können weitere Listenelemente oder Synonyme hinzufügen, indem Sie Bezeichnungen für andere Äußerungen vergeben oder indem Sie die Entität im linken Bereich unter **Entitäten** bearbeiten. Durch die [Bearbeitung](luis-how-to-add-entities.md#add-list-entities) von Entitäten können Sie zusätzliche Elemente mit entsprechenden Synonymen eingeben oder eine Liste importieren. 

## <a name="add-composite-entity"></a>Zusammengesetzte Entität hinzufügen

Zusammengesetzte Entitäten werden aus vorhandenen **Entitäten** in einer übergeordneten Entität erstellt. 

Bei der Äußerung `Does John Smith work in Seattle?` kann eine zusammengesetzte Äußerung Entitätsinformationen zum Mitarbeiternamen `John Smith` und dem Ort `Seattle` in einer zusammengesetzten Entität zurückgeben. Die untergeordneten Entitäten müssen bereits in der App vorhanden sein und in der Beispieläußerung markiert sein, bevor die zusammengesetzte Entität erstellt wird.

1. Um untergeordnete Entitäten in einer zusammengesetzten Entität zu umschließen, wählen Sie die **erste** bezeichnete Entität (ganz links) in der Äußerung für die zusammengesetzte Entität aus. In einer Dropdownliste werden die Optionen für diese Auswahl angezeigt.

1. Wählen Sie **Wrap in composite entity** (In zusammengesetzter Entität umschließen) aus der Dropdownliste aus. 

1. Wählen Sie das letzte Wort der zusammengesetzten Entität aus (ganz rechts). Die zusammengesetzte Entität ist mit einer grünen Linie unterstrichen. Dies ist der visuelle Indikator für eine zusammengesetzte Entität. Er sollte unter allen Wörtern in der zusammengesetzten Entität von der untergeordneten Entität ganz links bis zur untergeordneten Entität ganz rechts vorhanden sein.

1. Geben Sie den Namen der zusammengesetzten Entität in der Dropdownliste ein.

    Wenn Sie die Entitäten ordnungsgemäß umschließen, wird eine grüne Linie unter dem gesamten Ausdruck angezeigt.

1. Überprüfen Sie die Details der zusammengesetzten Entität im Popupfeld **What type of entity do you want to create?** (Welchen Entitätstyp möchten Sie erstellen?), und wählen Sie dann **Fertig** aus.

    ![Screenshot des Popupelements mit den Details der Entität](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Die zusammengesetzte Entität wird mit blauer Hervorhebung einzelner Entitäten und einer grünen Unterstreichung der gesamten zusammengesetzten Entität angezeigt. 

    ![Screenshot der Seite „Intents details“ (Details zu Absichten) mit zusammengesetzter Entität](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Hinzufügen der Rolle der Entität zur Äußerung

Eine Rolle ist ein benannter Untertyp einer Entität, der durch den Kontext der Äußerung bestimmt wird. Sie können eine Entität innerhalb einer Äußerung als Entität markieren oder eine Rolle innerhalb dieser Entität auswählen. Jede Entität kann Rollen aufweisen, einschließlich benutzerdefinierter Entitäten, die durch maschinelles Lernen erworben werden (einfache Entitäten und zusammengesetzte Entitäten), die nicht durch maschinelles Lernen erworben werden (vorkonfigurierte Entitäten, Entitäten mit regulären Ausdrücken, Listenentitäten). 

Informationen zum [Markieren einer Äußerung mit Entitätsrollen](tutorial-entity-roles.md) erhalten Sie ein einem praxisorientierten Tutorial. 

## <a name="entity-status-predictions"></a>Vorhersagen zum Entitätsstatus

Wenn Sie im LUIS-Portal eine neue Äußerung eingeben, enthält diese Äußerung möglicherweise Vorhersagefehler für die Entität. Der Vorhersagefehler ist der Unterschied zwischen der Bezeichnung einer Entität und der Vorhersage zur Entität durch LUIS. 

Dieser Unterschied wird im LUIS-Portal in der Äußerung rot unterstrichen. Die rote Unterstreichung kann in den Klammern der Entität oder außerhalb der Klammern angezeigt werden. 

![Screenshot der Vorhersageabweichung für den Entitätsstatus](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Wählen Sie die rot unterstrichenen Wörter in der Äußerung aus. 

Im Entitätsfeld wird der **Entitätsstatus** mit einem roten Ausrufezeichen angezeigt, wenn eine Abweichung zur Vorhersage vorliegt. Wählen Sie zum Anzeigen des Entitätsstatus mit Informationen zu den Unterschieden zwischen den bezeichneten und vorhergesagten Entitäten die Option **Entitätsstatus** und dann das Element auf der rechten Seite aus.

![Screenshot der Entitätsstatusauswahl](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

Die rote Linie kann in folgenden Situationen angezeigt werden:

   * Wenn eine Äußerung eingegeben wird, bevor die Entität bezeichnet wurde
   * Wenn die Entitätsbezeichnung angewandt wird
   * Wenn die Entitätsbezeichnung entfernt wird
   * Wenn für den Text mehrere Entitätsbezeichnungen vorhergesagt werden 

Die folgenden Lösungen beheben Abweichungen bei Entitätsvorhersagen:

|Entität|Visueller Indikator|Vorhersage|Lösung|
|--|--|--|--|
|Die Äußerung wurde eingegeben, aber die Entität wurde noch nicht bezeichnet.|rot unterstrichen|Die Vorhersage ist falsch.|Bezeichnen Sie die Entität mit dem vorhergesagten Wert.|
|Nicht bezeichneter Text|rot unterstrichen|Falsche Vorhersage|Die aktuellen Äußerungen, die diese falsche Entität verwenden, müssen für alle Absichten überprüft werden. Die aktuellen Äußerungen haben LUIS fälschlicherweise trainiert, diesen Text als die vorhergesagte Entität anzusehen.
|Richtig bezeichneter Text|blaue Entitätsmarkierung, rot unterstrichen|Falsche Vorhersage|Stellen Sie weitere Äußerungen mit der richtig bezeichneten Entität an verschiedenen Stellen und in unterschiedlichen Verwendungen bereit. Die aktuellen Äußerungen sind entweder nicht ausreichend, um LUIS für diese Entität zu trainieren, oder ähnliche Entitäten kommen im selben Kontext vor. Ähnliche Entitäten sollten in einer einzigen Entität zusammengefasst werden, damit LUIS nicht verwirrt wird. Eine weitere Lösung besteht darin, eine Ausdrucksliste hinzuzufügen, um die Wichtigkeit der Wörter zu steigern. |
|Falsch bezeichneter Text|blaue Entitätsmarkierung, rot unterstrichen|Richtige Vorhersage| Stellen Sie weitere Äußerungen mit der richtig bezeichneten Entität an verschiedenen Stellen und in unterschiedlichen Verwendungen bereit. 

## <a name="other-actions"></a>Andere Aktionen

Sie können Aktionen für Beispieläußerungen als ausgewählte Gruppe oder als einzelnes Element ausführen. Bei Gruppen von ausgewählten Beispieläußerungen ändert sich das Kontextmenü oberhalb der Liste. Für einzelne Elemente können das Kontextmenü oberhalb der Liste sowie die einzelnen kontextbezogenen Auslassungspunkte am Ende jeder Zeile einer Äußerung verwendet werden. 

### <a name="remove-entity-labels-from-utterances"></a>Entfernen von Entitätsbeschriftungen aus Äußerungen

Sie können durch maschinelles Lernen erworbene Entitätsbeschriftungen aus einer Äußerung auf der Seite der Absicht entfernen. Wenn die Entität nicht durch maschinelles Lernen erworben wurde, kann sie nicht aus der Äußerung entfernt werden. Wenn Sie eine nicht durch maschinelles Lernen erworbene Entität aus der Äußerung entfernen möchten, müssen Sie die Entität aus der gesamten App löschen. 

Um eine durch maschinelles Lernen erworbene Entität aus einer Äußerung zu entfernen, wählen Sie die Entität in der Äußerung aus. Wählen Sie anschließend im angezeigten Dropdownfeld der Entität die Option **Beschriftung entfernen** aus.

### <a name="add-prebuilt-entity-label"></a>Hinzufügen von vordefinierten Entitätsbeschriftungen

Wenn Sie Ihrer LUIS-App die vordefinierten Entitäten hinzufügen, müssen Sie die Äußerungen nicht mit diesen Entitäten markieren. Weitere Informationen zu vordefinierten Entitäten und wie Sie sie hinzufügen finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-regular-expression-entity-label"></a>Hinzufügen einer Entitätsbeschriftung vom Typ „Regulärer Ausdruck“

Wenn Sie Ihrer LUIS-App die Entitäten vom Typ „Regulärer Ausdruck“ hinzufügen, müssen Sie die Äußerungen nicht mit diesen Entitäten markieren. Weitere Informationen zu Entitäten vom Typ „Regulärer Ausdruck“ und wie Sie sie hinzufügen finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Erstellen eines Musters aus einer Äußerung

Weitere Informationen finden Sie unter [Hinzufügen eines Musters aus einer vorhandenen Äußerung auf der Seite der Absicht oder der Entität](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Hinzufügen einer pattern.any-Entität

Wenn Sie die „Pattern.any“-Entitäten zu Ihrer LUIS-App hinzufügen, können Sie die Äußerungen nicht mit diesen Entitäten beschriften. Sie sind nur in Mustern gültig. Weitere Informationen zu „Pattern.any“-Entitäten und wie Sie sie hinzufügen finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Trainieren Ihrer App nach dem Ändern des Modells mit Äußerungen

Nachdem Sie Äußerungen hinzugefügt, bearbeitet oder entfernt haben, [trainieren](luis-how-to-train.md) und [veröffentlichen](luis-how-to-publish-app.md) Sie Ihre App, damit die Änderungen Auswirkungen auf Endpunktabfragen zeigen. 

## <a name="next-steps"></a>Nächste Schritte

Nach dem Beschriften von Äußerungen in Ihren Absichten können Sie jetzt eine [zusammengesetzte Entität](luis-how-to-add-entities.md) erstellen.
