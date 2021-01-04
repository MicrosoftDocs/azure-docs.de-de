---
title: Verwenden von Features zum Verbessern der LUIS-Wortliste
titleSuffix: Azure Cognitive Services
description: Verwenden Sie Language Understanding (LUIS), um App-Features hinzuzufügen, die die Erkennung oder Vorhersage von Absichten und Entitäten verbessern können, die Kategorien und Muster
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999925"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Verstärken des Signals von Wortlisten mithilfe von Features

Sie können Ihrer LUIS-App Features hinzufügen, um ihre Genauigkeit zu verbessern. Features unterstützen LUIS mit Hinweisen, dass bestimmte Wörter und Ausdrücke Teil eines App-Domänenvokabulars sind.

Machen Sie sich mit den [Konzepten](luis-concept-feature.md) vertraut, um zu erfahren, wann und warum ein Feature verwendet wird.

## <a name="add-phrase-list-as-a-feature"></a>Hinzufügen einer Begriffsliste als Feature

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Wählen Sie **Erstellen** und anschließend im linken Bereich Ihrer App die Option **Features** aus.

1. Wählen Sie auf der Seite **Features** die Option **+ Erstellen** aus.

1. Geben Sie im Dialogfeld **Create new phrase list feature** (Neues Ausdruckslistenfeature erstellen) einen Namen für das Feature ein (beispielsweise `Cities`). Geben Sie im Feld **Wert** Beispiele für die Städte ein (beispielsweise `Seattle`). Sie können jeweils einen Wert oder eine Gruppe durch Kommas getrennter Werte eingeben und dann die **EINGABETASTE** drücken.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Hinzufügen eines Features (Ausdrucksliste) in Form von Städten](./media/luis-add-features/add-phrase-list-cities.png)

    Sobald Sie genügend Werte für LUIS eingegeben haben, werden Vorschläge angezeigt. Sie können **+ Alle hinzufügen** verwenden, um alle vorgeschlagenen Werte hinzufügen, oder einzelne Begriffe auswählen.

1. Lassen Sie die Option **These values are interchangeable** (Diese Werte sind austauschbar.) aktiviert, wenn die Ausdrücke synonym verwendet werden können.

1. Die Liste der Ausdrücke kann auf die gesamte App mit der Einstellung **Global** oder nur auf ein bestimmtes Modell (Absicht oder Entität) angewendet werden. Wenn Sie die Ausdrucksliste als _Feature_ auf der Grundlage einer Absicht oder Entität erstellen, ist die Umschaltfläche nicht auf global festgelegt. In diesem Fall bedeutet die Umschaltfläche, dass es sich um ein lokales Feature handelt, das nur für dieses Modell (und somit _nicht global_ für die Anwendung) gilt.

1. Wählen Sie **Fertig** aus. Das neue Feature wird der Seite **ML Features** (ML-Features) hinzugefügt.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Eine Begriffsliste kann über die kontextbezogene Symbolleiste auf der Seite **ML Features** (ML-Features) gelöscht oder deaktiviert werden.

## <a name="global-phrase-list-applies-to-entire-app"></a>Globale Ausdrucksliste für die gesamte App

Eine Ausdrucksliste sollte auf die Absicht oder Entität angewendet werden, für deren Unterstützung sie vorgesehen ist. Manchmal empfiehlt es sich jedoch, eine Ausdruckliste als **globales** Feature auf die gesamte App anzuwenden.

Wählen Sie auf der Seite „ML-Features“ die Ausdrucksliste und anschließend oben auf der kontextbezogenen Symbolleiste die Option **Global machen** aus.

## <a name="model-as-a-feature"></a>Modell als Feature

Eine Entität kann als [Feature für eine Absicht oder Entität](luis-concept-feature.md) fungieren.

Wenn Sie einer Absicht eine Entität als Feature hinzufügen möchten, wählen Sie auf der Seite „Absichten“ die Absicht und anschließend über der kontextbezogenen Symbolleiste die Option **+ Feature hinzufügen** aus. Die Liste enthält alle Ausdruckslisten und Entitäten, die als Feature angewendet werden können.

Wenn Sie einer anderen Entität eine Entität als Feature hinzufügen möchten, können Sie dazu entweder die [Entitätspalette](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) auf der Detailseite der Absicht verwenden oder das [Feature auf der Detailseite der Entität hinzufügen](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity).

## <a name="next-steps"></a>Nächste Schritte

[Trainieren und testen Sie die App](luis-interactive-test.md) nach dem Hinzufügen, Bearbeiten, Löschen oder Deaktivieren eines Features erneut, um festzustellen, ob sich die Leistung verbessert hat.
