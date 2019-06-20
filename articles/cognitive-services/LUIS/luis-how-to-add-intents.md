---
title: Hinzufügen von Absichten
titleSuffix: Language Understanding - Azure Cognitive Services
description: Fügen Sie Ihrer LUIS-App Absichten hinzu, um Gruppen von Fragen oder Befehlen zu identifizieren, die den gleichen Zweck verfolgen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0c42ab44ba317888b982ba7c72f78be4ca73d93c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148167"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Hinzufügen von Absichten, um die Absicht von Benutzeräußerungen zu bestimmen

Fügen Sie Ihrer LUIS-App [Absichten](luis-concept-intent.md) hinzu, um Gruppen von Fragen oder Befehlen zu identifizieren, die den gleichen Zweck verfolgen. 

Navigieren Sie in der oberen Navigationsleiste zum Abschnitt **Build** und dann im linken Bereich zu **Absichten**, um die Absichten zu verwalten. 

## <a name="add-intent"></a>Hinzufügen einer Absicht

1. Wählen Sie auf der Seite **Absichten** die Option **Create new intent** (Neue Absicht erstellen).

1. Geben Sie im Dialogfeld **Create new intent** (Neue Absicht erstellen) den Namen `GetEmployeeInformation` für die Absicht ein, und klicken Sie auf **Fertig**.

    ![Hinzufügen einer Absicht](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Hinzufügen einer Beispieläußerung

Beispieläußerungen sind Textbeispiele für Benutzerfragen oder -befehle. Fügen Sie einer Absicht Beispieläußerungen hinzu, um Language Understanding-Apps zu trainieren.

1. Geben Sie auf der Seite **GetEmployeeInformation** mit den Details der Absicht im Textfeld unter dem Absichtsnamen eine relevante Äußerung ein, die Sie von Ihren Benutzern erwarten, z.B. `Does John Smith work in Seattle?`. Drücken Sie anschließend die EINGABETASTE.
 
    ![Screenshot der Seite „Intents details“ (Details zu den Absichten) mit hervorgehobener Äußerung](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konvertiert alle Äußerungen in Kleinbuchstaben und fügt vor und nach Token, z.B. Bindestrichen, Leerzeichen ein.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Fehler bei Absichtsvorhersagen 

Eine Beispieläußerung in eine Absicht, bei der möglicherweise ein Fehler bei der Absichtsvorhersage zwischen der Absicht, zu der die Beispieläußerung gehört, und der während des Trainings ermittelten Vorhersageabsicht aufgetreten ist. 

Um Äußerungsvorhersagefehler zu finden und zu beheben, verwenden Sie die **Auswertungsoptionen** „Falsch“ und „Unklar“ der Option **Filter** zusammen mit der Option **Ansicht** von **Detailansicht**. 

![Verwenden Sie für das Ermitteln und Beheben von Äußerungsvorhersagefehlern die Option „Filter“.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Wenn die Filter und die Ansicht angewandt wurden und Beispieläußerungen mit Fehlern vorhanden sind, enthält die Liste der Beispieläußerungen die Äußerungen und die Probleme.

![![Wenn die Filter und die Ansicht angewandt wurden und Beispieläußerungen mit Fehlern vorhanden sind, enthält die Liste der Beispieläußerungen die Äußerungen und die Probleme.](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Jede Zeile zeigt das aktuelle Vorhersageergebnis des Trainings für die Beispieläußerung, das nächstschlechtere Ergebnis und den Unterschied zwischen diesen beiden Bewertungen. 

### <a name="fixing-intents"></a>Beheben von Absichten

Wenn Sie erfahren möchten, wie Sie Absichtsvorhersagefehler beheben, verwenden Sie das [Zusammenfassungs-Dashboard](luis-how-to-use-dashboard.md). Das Zusammenfassungs-Dashboard enthält eine Analyse des letzten Trainings der aktiven Version und die besten Vorschläge für das Verbessern Ihres Modells.  

## <a name="add-a-custom-entity"></a>Hinzufügen einer benutzerdefinierten Entität

Nachdem eine Äußerung einer Absicht hinzugefügt wurde, können Sie Text der Äußerung auswählen, um eine benutzerdefinierte Entität zu erstellen. Eine benutzerdefinierte Entität ist eine Möglichkeit, um Text zusammen mit der richtigen Absicht für die Extrahierung zu kennzeichnen. 

Weitere Informationen finden Sie unter [Hinzufügen einer Entität zu Beispieläußerungen](luis-how-to-add-example-utterances.md).

## <a name="entity-prediction-discrepancy-errors"></a>Fehler durch Abweichung bei der Entitätsvorhersage 

Die Entität ist rot unterstrichen, um eine [Abweichung bei der Entitätsvorhersage](luis-how-to-add-example-utterances.md#entity-status-predictions) anzuzeigen. Da dies das erste Auftreten einer Entität ist, verfügt LUIS nicht über genügend Beispiele, um zuverlässig feststellen zu können, ob dieser Text mit der richtigen Entität gekennzeichnet ist. Diese Abweichung wird entfernt, nachdem die App trainiert wurde. 

![Screenshot: Seite mit Absichtsdetails, benutzerdefinierter Entitätsname blau hervorgehoben](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Der Text ist blau hervorgehoben, um eine Entität anzuzeigen.  

## <a name="add-a-prebuilt-entity"></a>Hinzufügen einer vordefinierten Entität

Weitere Informationen finden Sie unter [Vordefinierte Entitäten](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Verwenden der kontextbezogenen Symbolleiste

Wenn in der Liste mindestens eine Äußerung ausgewählt ist, können Sie über die Symbolleiste oberhalb der Liste mit den Äußerungen die folgenden Aktionen durchführen, indem Sie das Feld links von einer Äußerung aktivieren:

* Absicht neu zuweisen: Verschieben von Äußerungen in eine andere Absicht
* Löschen von Äußerungen
* Entitätsfilter: Ausschließliches Anzeigen von Äußerungen mit gefilterten Entitäten
* Alle anzeigen/Nur Fehler: Anzeigen von Äußerungen mit Vorhersagefehlern oder aller Äußerungen
* Ansicht für Entitäten/Token: Anzeigen der Ansicht für Entitäten mit Entitätsnamen oder des unformatierten Texts der Äußerung
* Lupe: Suchen nach Äußerungen mit spezifischem Text

## <a name="working-with-an-individual-utterance"></a>Arbeiten mit einer einzelnen Äußerung

Für eine einzelne Äußerung können über das Menü mit den Auslassungszeichen rechts neben der Äußerung die folgenden Aktionen durchgeführt werden:

* Bearbeiten: Ändern des Texts der Äußerung
* Löschen: Entfernen der Äußerung aus der Absicht. Falls Sie die Äußerung aufbewahren möchten, können Sie sie in die Absicht **Keine** verschieben. 
* Muster hinzufügen: Mit einem Muster können Sie in einer häufigen Äußerung den Text kennzeichnen, der ersetzt bzw. ignoriert werden kann. So verringern Sie die Notwendigkeit von weiteren Äußerungen in der Absicht. 

In der Spalte **Labeled intent** (Bezeichnete Absicht) können Sie die Absicht der Äußerung ändern.

## <a name="train-your-app-after-changing-model-with-intents"></a>Trainieren der App nach dem Ändern des Modells mit Absichten

Nachdem Sie Absichten hinzugefügt, bearbeitet oder entfernt haben, [trainieren](luis-how-to-train.md) und [veröffentlichen](luis-how-to-publish-app.md) Sie Ihre App, damit die Änderungen auf Endpunktabfragen angewendet werden. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum Hinzufügen von [Beispieläußerungen](luis-how-to-add-example-utterances.md) mit Entitäten. 
