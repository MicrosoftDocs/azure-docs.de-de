---
title: Steigern der Genauigkeit mit Mustern
titleSuffix: Language Understanding - Azure Cognitive Services
description: Fügen Sie Mustervorlagen hinzu, um die Vorhersagegenauigkeit in LUIS-Apps (Language Understanding Intelligent Service) zu verbessern.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60196658"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hinzufügen von Mustern zum Verbessern der Vorhersagegenauigkeit
Nachdem eine LUIS-App Endpunktäußerungen empfängt, verbessern Sie mithilfe eines [Musters](luis-concept-patterns.md) die Vorhersagegenauigkeit für Äußerungen, die ein Muster in der Wortreihenfolge und Wortwahl zeigen. Muster verwenden eine bestimmte [Syntax](luis-concept-patterns.md#pattern-syntax), um den Speicherort von [Entitäten](luis-concept-entity-types.md), [Entitätsrollen](luis-concept-roles.md) und optionalem Text anzugeben.

## <a name="add-template-utterance-to-create-pattern"></a>Hinzufügen von Vorlagenäußerungen zum Erstellen von Mustern
1. Öffnen Sie Ihre App, indem Sie den Namen auf der Seite **Meine Apps** auswählen, und wählen Sie dann **Muster** im linken Bereich unter **Improve app performance** (App-Leistung verbessern) aus.

    ![Screenshot der Musterliste](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wählen Sie die richtige Absicht für das Muster aus. 

    ![Auswählen der Absicht](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Geben Sie im Textfeld der Vorlage die Vorlagenäußerung ein, und drücken Sie die EINGABETASTE. Wenn Sie den Namen der Entität eingeben möchten, verwenden Sie die richtige Musterentitätssyntax. Beginnen Sie die Entitätssyntax mit `{`. Die Liste der Entitäten wird angezeigt. Wählen Sie die richtige Entität aus, und drücken Sie die EINGABETASTE. 

    ![Screenshot der Entität für das Muster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Wenn die Entität eine [Rolle](luis-concept-roles.md) enthält, geben Sie die Rolle mit einem einzelnen Doppelpunkt `:` nach dem Entitätsnamen an, z. B. `{Location:Origin}`. Die Liste der Rollen für die Entitäten wird in einer Liste angezeigt. Wählen Sie die Rolle aus, und drücken Sie die EINGABETASTE. 

    ![Screenshot der Entität mit der Rolle](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nach dem Auswählen der richtigen Entität beenden Sie die Eingabe des Musters und drücken die EINGABETASTE. Wenn Sie die Mustereingabe beendet haben, [trainieren](luis-how-to-train.md) Sie Ihre App.

    ![Screenshot eines eingegebenen Musters mit beiden Entitätstypen](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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
