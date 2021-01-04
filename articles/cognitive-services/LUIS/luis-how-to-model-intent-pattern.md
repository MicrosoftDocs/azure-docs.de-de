---
title: 'Steigern der Genauigkeit mit Mustern: LUIS'
titleSuffix: Azure Cognitive Services
description: Fügen Sie Mustervorlagen hinzu, um die Vorhersagegenauigkeit in LUIS-Apps (Language Understanding Intelligent Service) zu verbessern.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5e1cc5cdda921e63315c2904d560b7ad3939776
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018853"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hinzufügen von Mustern zum Verbessern der Vorhersagegenauigkeit
Nachdem eine LUIS-App Endpunktäußerungen empfängt, verbessern Sie mithilfe eines [Musters](luis-concept-patterns.md) die Vorhersagegenauigkeit für Äußerungen, die ein Muster in der Wortreihenfolge und Wortwahl zeigen. Muster verwenden eine bestimmte [Syntax](luis-concept-patterns.md#pattern-syntax), um den Speicherort von [Entitäten](luis-concept-entity-types.md), [Entitätsrollen](./luis-concept-entity-types.md) und optionalem Text anzugeben.

> [!CAUTION]
> Muster enthalten nur übergeordnete, durch maschinelles Lernen erworbene Entitäten, keine untergeordneten Entitäten.

## <a name="add-template-utterance-using-correct-syntax"></a>Hinzufügen der Vorlagenäußerung mit korrekter Syntax

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Wählen Sie im linken Bereich unter **Improve app performance** (App-Leistung verbessern) die Option **Muster** aus.

1. Wählen Sie die richtige Absicht für das Muster aus.

1. Geben Sie im Textfeld der Vorlage die Vorlagenäußerung ein, und drücken Sie die EINGABETASTE. Wenn Sie den Namen der Entität eingeben möchten, verwenden Sie die richtige Musterentitätssyntax. Beginnen Sie die Entitätssyntax mit `{`. Die Liste der Entitäten wird angezeigt. Wählen Sie die korrekte Entität aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Entität für das Muster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Wenn die Entität eine [Rolle](./luis-concept-entity-types.md) enthält, geben Sie die Rolle mit einem einzelnen Doppelpunkt `:` nach dem Entitätsnamen an, z. B. `{Location:Origin}`. Die Liste der Rollen für die Entitäten wird in einer Liste angezeigt. Wählen Sie die Rolle aus, und drücken Sie die EINGABETASTE.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Entität mit der Rolle](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nach dem Auswählen der richtigen Entität beenden Sie die Eingabe des Musters und drücken die EINGABETASTE. Wenn Sie die Mustereingabe beendet haben, [trainieren](luis-how-to-train.md) Sie Ihre App.

    > [!div class="mx-imgBorder"]
    > ![Screenshot eines eingegebenen Musters mit beiden Entitätstypen](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>Erstellen einer pattern.any-Entität

[Pattern.Any](luis-concept-entity-types.md)-Entitäten sind nur in [Mustern](luis-how-to-model-intent-pattern.md) gültig, nicht in Beispieläußerungen von Absichten. Mit diesem Entitätstyp kann LUIS das Ende von Entitäten unterschiedlicher Länge und Wortwahl finden. Da diese Entität in einem Muster verwendet wird, weiß LUIS, wo sich das Ende der Entität in der Äußerungsvorlage befindet.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Wählen Sie im Abschnitt **Erstellen** im linken Bereich die Option **Entitäten** und dann **+ Erstellen** aus.

1. Geben Sie im Dialogfeld **Entitätstyps auswählen** den Entitätsnamen in das Feld **Name** ein, und wählen Sie **Pattern.Any** als **Typ** und anschließend **Erstellen** aus.

    Nachdem Sie mit dieser Entität [eine Musteräußerung erstellt](luis-how-to-model-intent-pattern.md) haben, wird die Entität mit einem kombinierten Algorithmus (durch maschinelles Lernen erworben und Textvergleich) extrahiert.

## <a name="adding-example-utterances-as-pattern"></a>Hinzufügen von Beispieläußerungen als Muster

Ein Muster für eine Entität lässt sich _am einfachsten_ auf der Seite mit den Absichtsdetails erstellen. Bei dieser Vorgehensweise wird sichergestellt, dass Ihre Syntax zur Beispieläußerung passt.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Wählen Sie auf der Listenseite **Absichten** den Absichtsnamen der Beispieläußerung aus, auf deren Grundlage Sie eine Vorlagenäußerung erstellen möchten.
1. Wählen Sie auf der Seite mit den Absichtsdetails die Zeile für die Beispieläußerung aus, die Sie als Vorlagenäußerung verwenden möchten, und wählen Sie anschließend auf der Kontextsymbolleiste die Option **+ Als Muster hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Auswählen der Beispieläußerung als Vorlagenmuster auf der Seite mit den Absichtsdetails](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    Die Äußerung muss eine Entität enthalten, um ein Muster auf Grundlage dieser Äußerung erstellen zu können.

1. Wählen Sie im Popupfeld auf der Seite **Muster bestätigen** die Option **Fertig** aus. Die untergeordneten Entitäten der Entitäten oder Features müssen nicht definiert werden. Sie müssen lediglich die durch maschinelles Lernen erworbene Entität auflisten.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Bestätigen der Beispieläußerung als Vorlagenmuster auf der Seite mit den Absichtsdetails](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Wenn Sie die Vorlage bearbeiten müssen, um z. B. Text mithilfe der eckigen Klammern (`[]`) als optional auszuwählen, müssen Sie diese Bearbeitung auf der Seite **Muster** vornehmen.

1. Wählen Sie auf der Navigationsleiste die Option **Trainieren** aus, um die App mit dem neuen Muster zu trainieren.

## <a name="train-your-app-after-changing-model-with-patterns"></a>Trainieren Ihrer App nach dem Ändern des Modells mit Mustern
Nachdem Sie ein Muster hinzufügen, bearbeiten, entfernen oder neu zuweisen, [trainieren](luis-how-to-train.md) und [veröffentlichen](luis-how-to-publish-app.md) Sie Ihre App, damit die Änderungen auf Endpunktabfragen angewandt werden.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Verwenden der kontextbezogenen Symbolleiste

Die kontextbezogene Symbolleiste über der Liste mit Mustern ermöglicht Ihnen:

* Suchen nach Mustern
* Bearbeiten eines Musters
* Neuzuweisen von einzelnen Muster zu einer anderen Absicht
* Zuweisen von mehreren Mustern zu unterschiedlichen Absichten
* Löschen eines einzelnen Musters
* Löschen von mehreren Mustern
* Filtern der Musterliste nach Entität
* Filtern der Musterliste nach Absicht
* Entfernen eines Entitäts- oder Absichtsfilters
* Hinzufügen von Mustern aus vorhandenen Äußerungen auf der Seite einer Absicht oder Entität

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie in einem Tutorial, wie Sie mit „pattern.any“ und Rollen [ein Muster erstellen](luis-tutorial-pattern.md).
* Erfahren Sie, wie Sie Ihre App [trainieren](luis-how-to-train.md).
