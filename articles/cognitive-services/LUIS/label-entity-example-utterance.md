---
title: Bezeichnen einer Entität in einer Beispieläußerung
description: Hier erfahren Sie, wie Sie eine Machine-Learning-Entität mit untergeordneten Entitäten in einer Beispieläußerung auf einer Absichtsdetailseite des LUIS-Portals bezeichnen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019734"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Bezeichnen einer Machine-Learning-Entität in einer Beispieläußerung

Die Bezeichnung einer Entität in einer Beispieläußerung bietet LUIS ein Beispiel der Entität und wo die Entität in der Äußerung auftauchen kann.

Sie können Bezeichnungen für (untergeordnete) Machine-Learning-Entitäten festlegen.

Da es nicht möglich ist, Bezeichnungen für reguläre Ausdrücke, Listen oder vordefinierte Entitäten festzulegen, müssen Sie erst eine (untergeordnete) Entität erstellen und diese Elemente dann, wenn möglich, als Features zu dieser hinzufügen.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Festlegen von Bezeichnungen für Beispieläußerungen auf der Seite mit den Absichtsdetails

Wählen Sie die Absicht der Äußerung aus, um Bezeichnungen für Beispielentitäten in der Äußerung festzulegen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Wählen Sie die Absicht mit den Beispieläußerungen aus, für die Sie zur Extraktion mit einer Entität Bezeichnungen festlegen möchten.
1. Wählen Sie erst den Text, für den Sie eine Bezeichnung festlegen möchten, und anschließend die Entität aus.

## <a name="two-techniques-to-label-entities"></a>Zwei Techniken zum Bezeichnen von Entitäten

Die Seite mit den Absichtsdetails unterstützt zwei Techniken zum Festlegen von Bezeichnungen.
* Wählen Sie erst die entsprechende (untergeordnete) Entität aus der [Entitätenpalette](#label-with-the-entity-palette-visible) aus, und treffen Sie anschließend eine Auswahl innerhalb des Beispieltexts für die Äußerung. Es wird empfohlen, diese Technik zu verwenden, weil Sie dabei visuell überprüfen können, ob Sie gemäß Ihrem Schema mit der richtigen (untergeordneten) Entität arbeiten.
* Treffen Sie zunächst eine Auswahl innerhalb des Beispieltexts für die Äußerung. Dann wird ein Popupmenü mit [möglichen Bezeichnungen](#how-to-label-entity-from-in-place-menu) geöffnet.

## <a name="label-with-the-entity-palette-visible"></a>Festlegen von Bezeichnungen mit geöffneter Entitätenpalette

Nachdem Sie einen [Plan für Ihr Schema mit Entitäten](luis-how-plan-your-app.md) erstellt haben, sollten Sie die **Entitätenpalette** nicht schließen, während Sie Bezeichnungen festlegen. Die **Entitätenpalette** soll als Erinnerung dafür dienen, welche Entitäten Sie laut Ihrem Plan noch extrahieren müssen.

Wenn Sie die **Entitätenpalette** öffnen möchten, klicken Sie auf das Symbol **@** in der kontextbezogenen Symbolleiste, die sich oberhalb der Liste mit den Beispieläußerungen befindet.

> [!div class="mx-imgBorder"]
> ![Screenshot: Entitätenpalette auf der Seite mit den Absichtsdetails](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Bezeichnen von Entitäten über die Entitätenpalette

Die Entitätspalette stellt eine Alternative zur oben beschrieben Form der Bezeichnung dar. Sie können damit über Text streichen, um diesen sofort mit einer Entität zu bezeichnen.

1. Öffnen Sie die Entitätenpalette, indem Sie rechts oben in der Tabelle mit den Äußerungen auf das Symbol **@** klicken.

2. Wählen Sie aus der Palette eine Entität aus, für die Sie eine Bezeichnung festlegen möchten. Diese Aktion wird visuell durch einen neuen Cursor dargestellt. Der Cursor folgt dem Mauszeiger, wenn Sie ihn im LUIS-Portal bewegen.

3. _Zeichnen_ Sie die Entität in der Beispieläußerung mithilfe des Cursors.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der mit dem Cursor gezeichneten Entität](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Hinzufügen einer Entität aus der Entitätenpalette als Feature

Im unteren Bereich der Entitätenpalette können Sie Features zu der jeweils ausgewählten Entität hinzufügen. Sie können entweder aus den vorhanden Entitäten und Ausdruckslisten auswählen oder eine neue Ausdrucksliste erstellen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Entitätenpalette mit einer Entität als Feature](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Beschriften von Entitätsrollen

Entitätsrollen werden mithilfe der **Entitätenpalette** beschriftet.

1. Wählen Sie auf der Detailseite der Absicht die **Entitätspalette** aus der Kontext-Symbolleiste aus.
1. Wenn die Entitätspalette offen ist, wählen Sie aus der Liste die gewünschte Entität aus.
1. Wählen Sie aus der Liste der Entitäten eine vorhandene Rolle aus.
1. Beschriften Sie in der Beispieläußerung den Text mit der Entitätsrolle.

## <a name="how-to-label-entity-from-in-place-menu"></a>Bezeichnen von Entitäten über das Direktmenü

Es geht schneller, Bezeichnungen über das Direktmenü festzulegen, indem Sie den Text innerhalb der Äußerung auswählen. Sie können auch über den bezeichneten Text eine Machine-Learning-Entität oder eine Listenentität erstellen.

Gehen Sie von der Beispieläußerung `hi, please I want a cheese pizza in 20 minutes` aus.

Wählen Sie erst den am weitesten links stehenden Text und dann den Text ganz rechts in der Entität aus. Wählen Sie anschließend die Entität aus, für die Sie eine Bezeichnung festlegen möchten.

> [!div class="mx-imgBorder"]
> ![Bezeichnen der vollständigen Machine-Learning-Entität](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Überprüfen des bezeichneten Texts

Überprüfen Sie nach der Bezeichnung die Beispieläußerung, und vergewissern Sie sich, dass der ausgewählte Textabschnitt mit der ausgewählten Entität unterstrichen ist. Die durchgezogene Linie gibt an, dass der Text bezeichnen wurde.

> [!div class="mx-imgBorder"]
> ![Bezeichnete, vollständige Machine-Learning-Entität](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Bestätigen der vorhergesagten Entität

Wenn ein gepunkteter Rahmen um den Textabschnitt gezogen wird, bedeutet dies, dass der Text vorhergesagt, aber _noch nicht als Bezeichnung festgelegt_ wurde Wählen Sie die Zeile mit der Äußerung aus, und klicken Sie dann in der kontextbezogenen Symbolleiste auf **Confirm entities** (Entitäten bestätigen), um aus der Vorhersage eine Bezeichnung zu machen.

## <a name="relabeling-over-existing-entities"></a>Neubezeichnen vorhandener Entitäten

Wenn Sie Text, der bereits bezeichnet wurde, eine neue Bezeichnung hinzufügen möchten, können Sie mithilfe von LUIS vorhandene Bezeichnungen teilen oder zusammenführen.

## <a name="labeling-for-punctuation"></a>Bezeichnungen für Interpunktion

Für Interpunktion sind keine Bezeichnungen erforderlich. Über die [Anwendungseinstellungen](luis-reference-application-settings.md) können Sie steuern, welche Auswirkungen die Interpunktion auf Äußerungsvorhersagen hat.

## <a name="unlabel-entities"></a>Entitäten ohne Bezeichnung

> [!NOTE]
> Nur die Bezeichnungen von Machine-Learning-Entitäten können entfernt werden. Für Entitäten für reguläre Ausdrücke, Listenentitäten oder vordefinierte Entitäten können weder Bezeichnungen festgelegt noch entfernt werden.

Wenn Sie die Bezeichnung einer Entität entfernen möchten, wählen Sie diese aus, und klicken Sie im Direktmenü auf **Bezeichnung entfernen**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Bezeichnung einer Entität entfernen](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatisches Bezeichnen von über- und untergeordneten Entitäten

Wenn Sie eine Bezeichnung für eine übergeordnete Entität festlegen, wird auch für jede untergeordnete Entität, die basierend auf der aktuell trainierten Version vorhergesagt werden kann, eine Bezeichnung hinzugefügt.

Wenn Sie eine Bezeichnung für eine untergeordnete Entität festlegen, wird auch automatisch für die übergeordnete Entität eine Bezeichnung hinzugefügt.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatisches Bezeichnen von Entitäten, die nicht auf Machine Learning basieren

Entitäten, die nicht aus Machine Learning basieren, sind z. B. vordefinierte Entitäten, Entitäten für reguläre Ausdrücke, Listenentitäten und pattern.any-Entitäten. Diese werden automatisch von LUIS bezeichnet, sodass sie nicht manuell von den Benutzern bezeichnet werden müssen.

## <a name="intent-prediction-errors"></a>Fehler bei Absichtsvorhersagen

Es wird ein Absichtsvorhersagefehler zurückgegeben, wenn die Beispieläußerung mit der aktuell trainierten App nicht für die Absicht vorhergesagt werden konnte.

Informieren Sie sich, wie Sie diese Fehler auf der Seite mit den Absichtsdetails [abrufen](luis-how-to-add-intents.md#intent-prediction-errors) können.

## <a name="entity-prediction-errors"></a>Fehler bei der Entitätsvorhersage

Vorhersagefehler bei Entitäten deuten darauf hin, dass die vorhergesagte Entität nicht der bezeichneten Entität entspricht. Dies wird mit durch einen Warnindikator neben der Äußerung verdeutlicht.

> [!div class="mx-imgBorder"]
> ![Entitätenpalette für Machine-Learning-Entitäten](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie das [Dashboard](luis-how-to-use-dashboard.md), und [überprüfen Sie die Endpunktäußerungen](luis-how-to-review-endpoint-utterances.md), um die Vorhersagequalität Ihrer App zu verbessern.
