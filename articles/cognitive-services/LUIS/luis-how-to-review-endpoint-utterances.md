---
title: Überprüfen von Benutzeräußerungen
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beim aktiven Lernen werden Endpunktabfragen erfasst und die Äußerungen des Benutzers ausgewählt, bei denen es sich nicht sicher ist. Sie überprüfen diese Äußerungen, um die Absicht auszuwählen und Entitäten für diese realen Äußerungen zu markieren. Akzeptieren Sie diese Änderungen in Ihren Beispieläußerungen, dann trainieren und veröffentlichen Sie sie. Dann werden Äußerungen von LUIS genauer identifiziert.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195127"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Überprüfen von Endpunktäußerungen im LUIS-Portal für aktives Lernen

Beim [aktiven Lernen](luis-concept-review-endpoint-utterances.md) werden Endpunktabfragen erfasst und die Äußerungen des Benutzers ausgewählt, bei denen es sich nicht sicher ist. Sie überprüfen diese Äußerungen, um die Absicht auszuwählen und Entitäten für diese realen Äußerungen zu markieren. Akzeptieren Sie diese Änderungen in Ihren Beispieläußerungen, dann trainieren und veröffentlichen Sie sie. Dann werden Äußerungen von LUIS genauer identifiziert.


## <a name="enable-active-learning"></a>Aktivieren des aktiven Lernens

Um das aktive Lernen zu aktivieren, protokollieren Sie Benutzerabfragen. Dies wird durch Festlegen der [Endpunktabfrage](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) mit dem `log=true`-Abfragezeichenfolgenparameter und -wert erreicht.

## <a name="disable-active-learning"></a>Deaktivieren des aktiven Lernens

Um das aktive Lernen zu deaktivieren, protokollieren Sie keine Benutzerabfragen. Dies wird durch Festlegen der [Endpunktabfrage](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) mit dem `log=false`-Abfragezeichenfolgenparameter und -wert erreicht.

## <a name="filter-utterances"></a>Filtern von Äußerungen

1. Öffnen Sie Ihre App (z.B. TravelAgent), indem Sie ihren Namen auf der Seite **Meine Apps** und dann auf der oberen Leiste **Erstellen** auswählen.

1. Wählen Sie unter **Improve app performance** (App-Leistung verbessern) die Option **Review endpoint utterances** (Endpunktäußerungen überprüfen) aus.

1. Wählen Sie auf der Seite **Review endpoint utterances** (Endpunktäußerungen überprüfen) das Textfeld **Filter list by intent or entity** (Liste nach Absicht oder Entität filtern) aus. Diese Dropdownliste enthält alle Absichten unter **INTENTS** und alle Entitäten unter **ENTITIES**.

    ![Äußerungsfilter](./media/label-suggested-utterances/filter.png)

1. Wählen Sie in der Dropdownliste eine Kategorie (Absichten oder Entitäten) aus, und überprüfen Sie die Äußerungen.

    ![Absichtsäußerungen](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Bezeichnen von Entitäten
LUIS ersetzt Entitätstoken (Wörter) durch Entitätsnamen, die blau hervorgehoben werden. Wenn eine Äußerung Entitäten ohne Beschriftung aufweist, bezeichnen Sie sie in der Äußerung. 

1. Wählen Sie die Wörter in der Äußerung aus. 

1. Wählen Sie eine Entität in der Liste aus.

    ![Bezeichnen der Entität](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Zuordnen einer einzelnen Äußerung

Für jede Äußerung wird eine vorgeschlagene Absicht in der Spalte **Aligned intent** (Zugeordnete Absicht) angezeigt. 

1. Wenn Sie diesem Vorschlag zustimmen, aktivieren Sie das Kontrollkästchen.

    ![Beibehalten der zugeordneten Absicht](./media/label-suggested-utterances/align-intent-check.png)

1. Wenn Sie mit dem Vorschlag nicht einverstanden sind, wählen Sie die richtige Absicht in der Dropdownliste der zugeordneten Absichten aus, und aktivieren Sie dann rechts neben der zugeordneten Absicht das Kontrollkästchen. 

    ![Zuordnen von Absichten](./media/label-suggested-utterances/align-intent.png)

1. Nachdem Sie das Kontrollkästchen aktiviert haben, wird die Äußerung aus der Liste entfernt. 

## <a name="align-several-utterances"></a>Zuordnen mehrerer Äußerungen

Um mehrere Äußerungen zuzuordnen, aktivieren Sie das Kontrollkästchen links neben den Äußerungen, und wählen Sie dann die Schaltfläche **Ausgewählte hinzufügen** aus. 

![Zuordnen mehrerer Elementen](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Überprüfen der zugeordneten Absicht

Sie können überprüfen, ob die Äußerung der richtigen Absicht zugeordnet wurde, indem Sie zu der Seite **Intents** (Absichten) navigieren, den Namen der Absicht auswählen und dann die Äußerungen überprüfen. Die Äußerung aus **Review endpoint utterances** (Endpunktäußerungen überprüfen) befindet sich in der Liste.

## <a name="delete-utterance"></a>Löschen einer Äußerung

Jede Äußerung kann aus der Überprüfungsliste gelöscht werden. Nach dem Löschen wird sie nicht mehr in der Liste angezeigt. Dies gilt auch dann, wenn der Benutzer die gleiche Äußerung am Endpunkt eingibt. 

Wenn Sie nicht wissen, ob Sie die Äußerung löschen sollten, verschieben Sie sie zur Absicht „None“, oder erstellen Sie eine neue Absicht, z.B. „Sonstige“, und verschieben Sie die Äußerung in diese Absicht. 

## <a name="delete-several-utterances"></a>Löschen mehrerer Äußerungen

Um mehrere Äußerungen zu löschen, wählen Sie jedes Element und dann den Papierkorb rechts neben der Schaltfläche **Ausgewählte hinzufügen** aus.

![Löschen von mehreren Elementen](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Nächste Schritte

Um zu testen, wie die Leistung nach dem Bezeichnen der vorgeschlagenen Äußerungen verbessert wird, können Sie auf die Testkonsole zugreifen, die Sie im oberen Bereich **Test** auswählen. Anleitungen zum Testen Ihrer App mit der Testkonsole finden Sie unter [Trainieren und Testen Ihrer App](luis-interactive-test.md).
