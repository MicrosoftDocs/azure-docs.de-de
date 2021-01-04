---
title: Überprüfen von Benutzeräußerungen – LUIS
titleSuffix: Azure Cognitive Services
description: Überprüfen Sie die beim aktiven Lernen erfassten Äußerungen, um die Absicht auszuwählen und Entitäten für reale Äußerungen zu markieren. Akzeptieren Sie die Änderungen, und führen Sie das Training und die Veröffentlichung durch.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007601"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Verbessern der LUIS-App durch Überprüfen der Endpunktäußerungen

Der Prozess der Überprüfung von Endpunktäußerungen auf korrekte Vorhersagen wird als [Aktives Lernen](luis-concept-review-endpoint-utterances.md) bezeichnet. Beim aktiven Lernen werden Endpunktabfragen erfasst und die Äußerungen des Benutzers ausgewählt, bei denen es sich nicht sicher ist. Sie überprüfen diese Äußerungen, um die Absicht auszuwählen und Entitäten für diese realen Äußerungen zu markieren. Akzeptieren Sie diese Änderungen in Ihren Beispieläußerungen, dann trainieren und veröffentlichen Sie sie. Dann werden Äußerungen von LUIS genauer identifiziert.

## <a name="log-user-queries-to-enable-active-learning"></a>Protokollieren von Benutzerabfragen zur Ermöglichung aktiven Lernens

Um das aktive Lernen zu aktivieren, müssen Sie Benutzerabfragen protokollieren. Dies wird durch Aufrufen der [Endpunktabfrage](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) mit dem `log=true`-Abfragezeichenfolgenparameter und -wert erreicht.

Verwenden Sie das LUIS-Portal, um die richtige Endpunktabfrage zu erstellen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Navigieren Sie zum Abschnitt **Verwalten**, und wählen Sie dann **Azure-Ressourcen** aus.
1. Wählen Sie für die zugewiesene Vorhersageressource **Abfrageparameter ändern** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Link „Abfrageparameter ändern“](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Schalten Sie **Protokolle speichern** um, und speichern Sie dann, indem Sie **Fertig** auswählen.

    > [!div class="mx-imgBorder"]
    > ![Verwenden Sie das LUIS-Portal, um Protokolle zu speichern, die für aktives Lernen benötigt werden.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Dadurch wird der Beispiel-URL der Abfragezeichenfolgenparameter `log=true` hinzugefügt. Kopieren und verwenden Sie die geänderte Beispielabfrage-URL, wenn Sie Vorhersageabfragen an den Runtimeendpunkt richten.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korrigieren von Absichtsvorhersagen zum Ausrichten von Äußerungen

Für jede Äußerung wird eine vorgeschlagene Absicht in der Spalte **Aligned intent** (Zugeordnete Absicht) angezeigt.

> [!div class="mx-imgBorder"]
> [![Überprüfen der Endpunktäußerungen, bei denen LUIS unsicher ist](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Wenn Sie dieser Absicht zustimmen, aktivieren Sie das Kontrollkästchen. Wenn Sie mit dem Vorschlag nicht einverstanden sind, wählen Sie die richtige Absicht in der Dropdownliste der zugeordneten Absichten aus, und aktivieren Sie dann rechts neben der zugeordneten Absicht das Kontrollkästchen. Nachdem Sie das Kontrollkästchen aktiviert haben, wird die Äußerung zu der Absicht verschoben und aus der Liste **Endpunktäußerungen überprüfen** entfernt.

> [!TIP]
> Es ist wichtig, zur Seite mit den Absichtsdetails zu wechseln, um die Entitätsvorhersagen aus allen Beispieläußerungen aus der Liste **Endpunktäußerungen überprüfen** zu überprüfen und zu korrigieren.

## <a name="delete-utterance"></a>Löschen einer Äußerung

Jede Äußerung kann aus der Überprüfungsliste gelöscht werden. Nach dem Löschen wird sie nicht mehr in der Liste angezeigt. Dies gilt auch dann, wenn der Benutzer die gleiche Äußerung am Endpunkt eingibt.

Wenn Sie nicht wissen, ob Sie die Äußerung löschen sollten, verschieben Sie sie zur Absicht „None“, oder erstellen Sie eine neue Absicht, z. B. `miscellaneous`, und verschieben Sie die Äußerung in diese Absicht.

## <a name="disable-active-learning"></a>Deaktivieren des aktiven Lernens

Um das aktive Lernen zu deaktivieren, protokollieren Sie keine Benutzerabfragen. Dies wird erreicht, indem die [Endpunktabfrage](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) mit dem QueryString-Parameter `log=false` und dem Wert oder nicht mit dem QueryString-Wert festgelegt wird, da der Standardwert „false“ ist.

## <a name="next-steps"></a>Nächste Schritte

Um zu testen, wie die Leistung nach dem Bezeichnen der vorgeschlagenen Äußerungen verbessert wird, können Sie auf die Testkonsole zugreifen, die Sie im oberen Bereich **Test** auswählen. Anleitungen zum Testen Ihrer App mit der Testkonsole finden Sie unter [Trainieren und Testen Ihrer App](luis-interactive-test.md).
