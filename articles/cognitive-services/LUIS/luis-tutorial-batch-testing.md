---
title: Batchtest
titleSuffix: Azure Cognitive Services
description: Dieses Tutorial veranschaulicht die Verwendung von Batchtests zum Ermitteln von Problemen bei der Vorhersage für Äußerungen in Ihrer App und zu ihrer Behebung.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: af04ca19961abcfc7ee218824a4a1a804f7ad79c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146155"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: Testen von Datasets in Batches

Dieses Tutorial veranschaulicht die Verwendung von Batchtests zum Ermitteln von Problemen bei der Vorhersage für Äußerungen in Ihrer App und zu ihrer Behebung.  

Anhand von Batchtests können Sie den Zustand des aktiven, trainierten Modells mit einer bekannten Gruppe von bezeichneten Äußerungen und Entitäten überprüfen. Fügen Sie in die JSON-formatierte Batchdatei die Äußerungen hinzu, und legen Sie die Entitätsbezeichnungen fest, die innerhalb der Äußerung vorhergesagt werden sollen. 

Anforderungen für Batchtests:

* Maximal 1000 Äußerungen pro Test 
* Keine Duplikate 
* Zulässige Entitätstypen: nur per maschinellem Lernen trainierte Entitäten von einfachen und zusammengesetzten Entitäten. Batchtests eignen sich nur für mit Machine Learning trainierten Absichten und Entitäten.

Wenn Sie eine andere App als die in diesem Tutorial verwenden, verwenden Sie *nicht* die Beispieläußerungen, die einer Absicht bereits hinzugefügt wurden. 

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importieren der Beispiel-App
> * Erstellen einer Batchtestdatei 
> * Ausführen eines Batchtests
> * Überprüfen der Testergebnisse
> * Beheben von Fehlern 
> * Erneutes Testen des Batchs

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importieren der Beispiel-App

Fahren Sie mit der im letzten Tutorial erstellten App mit dem Namen **HumanResources** fort. 

Führen Sie die folgenden Schritte aus:

1.  Laden Sie die [App-JSON-Datei](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json) herunter, und speichern Sie sie.

2. Importieren Sie den JSON-Code in eine neue App.

3. Klonen Sie die Version von der Registerkarte **Versionen** aus dem Abschnitt **Verwalten**, und geben Sie ihr den Namen `batchtest`. Durch Klonen können Sie ohne Auswirkungen auf die ursprüngliche Version mit verschiedenen Features von LUIS experimentieren. Da der Versionsname als Teil der URL-Route verwendet wird, darf er keine Zeichen enthalten, die in einer URL ungültig sind. 

4. Trainieren Sie die App.

## <a name="batch-file"></a>Batchdatei

1. Erstellen Sie `HumanResources-jobs-batch.json` in einem Text-Editor, oder [laden Sie](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) es herunter. 

2. Fügen Sie in die JSON-formatierte Batchdatei Äußerungen mit der **Absicht** hinzu, für die Sie im Test eine Vorhersage erhalten möchten. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Ausführen des Batchs

1. Wählen Sie auf der oberen Navigationsleiste **Test** aus. 

2. Wählen Sie im rechten Bereich **Batch testing panel** (Batchtestbereich) aus. 

    [![Screenshot der LUIS-App mit hervorgehobener Option „Batchtestbereich“](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Wählen Sie **Dataset importieren** aus.

    [![Screenshot der LUIS-App mit hervorgehobener Option „Dataset importieren“](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Wählen Sie den Speicherort für die Datei `HumanResources-jobs-batch.json` aus.

5. Versehen Sie das Dataset mit dem Namen `intents only`, und klicken Sie auf **Fertig**.

    ![Datei auswählen](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Wählen Sie die Schaltfläche **Ausführen**. 

7. Wählen Sie **See results** (Ergebnisse anzeigen) aus.

8. Überprüfen Sie die Ergebnisse im Diagramm und in der Legende.

    [![Screenshot der LUIS-App mit hervorgehobener Option „Batchtestergebnisse“](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Überprüfen der Batchergebnisse

Das Batchdiagramm zeigt die Ergebnisse in vier Quadranten an. Rechts neben dem Diagramm befindet sich ein Filter. Standardmäßig ist der Filter auf die erste Absicht in der Liste festgelegt. Der Filter enthält alle Absichten und nur einfache und zusammengesetzte Entitäten. Wenn Sie einen [Diagrammabschnitt](luis-concept-batch-test.md#batch-test-results) oder einen Punkt innerhalb des Diagramms auswählen, werden die zugehörigen Äußerungen unterhalb des Diagramms angezeigt. 

Wenn Sie auf das Diagramm zeigen, können Sie mit dem Mausrad die Ansicht im Diagramm vergrößern oder verkleinern. Dies ist hilfreich, wenn es viele Punkte im Diagramm gibt, die nah beieinander gruppiert sind. 

Das Diagramm ist in vier Quadranten unterteilt, von denen zwei in Rot angezeigt werden. **Auf diese Abschnitte sollten Sie besonders achten**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation-Testergebnisse

Die im Filter angezeigten **GetJobInformation**-Testergebnisse zeigen, dass zwei der vier Vorhersagen erfolgreich waren. Klicken Sie auf die Bezeichnung **Falsch positives Ergebnis** oberhalb des oberen rechten Quadranten, damit die Äußerungen unterhalb des Diagramms angezeigt werden. 

![Äußerungen aus dem LUIS-Batchtest](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Warum wird für zwei Äußerungen anstelle der richtigen Absicht **GetJobInformation** **ApplyForJob** vorhergesagt? Beide Absichten gleichen sich sehr stark in Bezug auf Wortwahl und Wortanordnung. Darüber hinaus stehen für **ApplyForJob** fast dreimal so viele Beispiele zur Verfügung wie für **GetJobInformation**. Diese Ungleichheit bei den Beispieläußerungen hat den Ausschlag für die Absicht **ApplyForJob** gegeben. 

Beachten Sie, dass beide Absichten die gleiche Anzahl von Fehlern aufweisen. Eine falsche Vorhersage bei einer Absicht wirkt sich auch auf die andere Absicht aus. Beide Absichten weisen Fehler auf, da die Äußerungen für beide Absichten falsch vorhergesagt wurden. 

![Fehler bei LUIS-Batchtestfiltern](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Bei den Äußerungen, die dem obersten Punkt im Abschnitt **Falsch positives Ergebnis** entsprechen, handelt es sich um `Can I apply for any database jobs with this resume?` und `Can I apply for any database jobs with this resume?`. Bei der ersten Äußerung wurde das Wort `resume` nur in **ApplyForJob** verwendet. Bei der zweiten Äußerung wurde das Wort `apply` nur in der Absicht **ApplyForJob** verwendet.

## <a name="fix-the-app"></a>Problembehebung in der App

Ziel dieses Abschnitts ist es, alle Äußerungen für **GetJobInformation** durch Korrigieren der App korrekt vorherzusagen. 

Eine scheinbar schnelle Lösung wäre es, die Äußerungen aus dieser Batchdatei der richtigen Absicht hinzuzufügen. Davon ist jedoch abzuraten. Die LUIS-App sollte diese Äußerungen richtig vorhersagen, ohne dass diese als Beispiele hinzugefügt werden. 

Möglicherweise gedenken Sie auch, Äußerungen aus **ApplyForJob** zu entfernen, bis die Anzahl der Äußerungen der von **GetJobInformation** entspricht. Hierdurch werden zwar eventuell die Testergebnisse korrigiert, die LUIS-App würde diese Absicht beim nächsten Mal allerdings trotzdem nicht korrekt vorhersagen. 

Die erste Lösung besteht darin, weitere Äußerungen zu **GetJobInformation** hinzuzufügen. Die zweite Lösung wäre, die Gewichtung von Wörtern wie `resume` und `apply` der Gewichtung der Absicht **ApplyForJob** nach unten hin anzugleichen. 

### <a name="add-more-utterances"></a>Hinzufügen weiterer Äußerungen

1. Schließen Sie den Batchtestbereich, indem Sie auf der oberen Navigationsleiste auf die Schaltfläche **Test** klicken. 

2. Wählen Sie aus der Liste mit Absichten die Option **GetJobInformation** aus. 

3. Fügen Sie weitere Äußerungen hinzu, die im Hinblick auf Länge, Wortwahl und Wortanordnung variieren, und schließen Sie dabei die Benennungen `resume`, `c.v.` und `apply` ein:

    |Beispieläußerungen für die Absicht **GetJobInformation**|
    |--|
    |Does the new job in the warehouse for a stocker require that I apply with a resume?|
    |Where are the roofing jobs today?|
    |I heard there was a medical coding job that requires a resume.|
    |I would like a job helping college kids write their c.v.s. |
    |Here is my resume, looking for a new post at the community college using computers.|
    |What positions are available in child and home care?|
    |Is there an intern desk at the newspaper?|
    |My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?|
    |Where are the earth drilling jobs right now?|
    |I've worked 8 years as an EMS driver. Any new jobs?|
    |New food handling jobs require application?|
    |How many new yard work jobs are available?|
    |Is there a new HR post for labor relations and negotiations?|
    |I have a masters in library and archive management. Any new positions?|
    |Are there any babysitting jobs for 13 year olds in the city today?|

    Versehen Sie die Entität **Job** nicht mit Bezeichnungen in den Äußerungen. In diesem Abschnitt des Tutorials wird ausschließlich die Absichtsvorhersage behandelt.

4. Trainieren Sie die App, indem Sie im oberen rechten Navigationsbereich auf **Trainieren** klicken.

## <a name="verify-the-new-model"></a>Überprüfen des neuen Modells

Um sicherzustellen, dass die Äußerungen im Batchtest richtig vorhergesagt werden, führen Sie den Batchtest erneut aus.

1. Wählen Sie auf der oberen Navigationsleiste **Test** aus. Wenn die Batchergebnisse noch geöffnet sind, wählen Sie **Zurück zur Liste** aus.  

2. Klicken Sie auf die Schaltfläche mit Auslassungspunkten ( **...** ) rechts neben dem Batchnamen und dann auf ***Dataset ausführen***. Warten Sie, bis der Batchtest abgeschlossen ist. Beachten Sie, dass die Schaltfläche **Ergebnisse anzeigen** nun in Grün angezeigt wird. Dies bedeutet, dass der gesamte Batch erfolgreich ausgeführt wurde.

3. Wählen Sie **See results** (Ergebnisse anzeigen) aus. Links neben dem Namen aller Absichten sollten grüne Symbole angezeigt werden. 

    ![Screenshot der LUIS-App mit hervorgehobener Schaltfläche „Batchergebnisse“](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Erstellen einer Batchdatei mit Entitäten 

Um Entitäten in einem Batchtest zu überprüfen, müssen die Entitäten in der JSON-Batchdatei mit Bezeichnungen versehen werden. Nur die per maschinellem Lernen trainierten Entitäten werden verwendet, d. h. einfache und zusammengesetzte Entitäten. Fügen Sie keine Entitäten hinzu, die nicht per Machine Learning trainiert wurden, da sie immer entweder über reguläre Ausdrücke oder über explizite Textübereinstimmungen gefunden werden.

Die Entitätsvielfalt für die Gesamtwortzahl ([token](luis-glossary.md#token)) kann sich auf die Qualität der Vorhersagen auswirken. Stellen Sie sicher, dass die für die Absicht bereitgestellten Trainingsdaten mit bezeichneten Äußerungen Entitäten mit unterschiedlichen Längen enthalten. 

Wenn Sie zum ersten Mal Batchdateien schreiben und testen, sollten Sie für den Einstieg Äußerungen und Entitäten verwenden, von denen Sie wissen, dass sie funktionieren, und Äußerungen, die Ihrer Ansicht nach eventuell falsch vorhergesagt werden. Auf diese Weise können Sie die Problemherde schnell eingrenzen. Nachdem Sie die Absichten **GetJobInformation** und **ApplyForJob** mit verschiedenen Auftragsnamen getestet haben, die nicht vorhergesagt wurden, weist diese Batchtestdatei gezielt darauf hin, ob ein Vorhersageproblem mit bestimmten Werten für die Entität **Job** vorliegt. 

Der Wert einer **Job**-Entität, die in den Testäußerungen angegeben wurde, besteht in der Regel aus einem oder zwei Wörtern, wobei einige Beispiele mehr Wörter enthalten. Wenn _Ihre eigene_ Personalabteilungs-App üblicherweise Auftragsnamen mit einer Vielzahl von Wörtern enthält, wären die mit der **Job**-Entität in dieser App bezeichneten Beispieläußerungen nicht gut geeignet.

1. Erstellen Sie `HumanResources-entities-batch.json` in einem Text-Editor, wie etwa [VSCode](https://code.visualstudio.com/), oder [laden Sie](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) es herunter.


2. Fügen Sie in der JSON-formatierten Batchdatei ein Array von Objekten hinzu, das Äußerungen mit der **Absicht** enthält, für die im Test Vorhersagen erzeugt und für die Standorte von Entitäten in der Äußerung vorhergesagt werden sollen. Da eine Entität auf Token basiert, achten Sie darauf, dass jede Entität mit einem Zeichen beginnt und endet. Die Äußerung darf nicht mit einem Leerraum beginnen oder enden. Denn dieser verursacht einen Fehler beim Import der Batchdatei.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Ausführen der Batchdatei mit Entitäten

1. Wählen Sie auf der oberen Navigationsleiste **Test** aus. 

2. Wählen Sie im rechten Bereich **Batch testing panel** (Batchtestbereich) aus. 

3. Wählen Sie **Dataset importieren** aus.

4. Wählen Sie den Speicherort im Dateisystem für die Datei `HumanResources-entities-batch.json` aus.

5. Versehen Sie das Dataset mit dem Namen `entities`, und klicken Sie auf **Fertig**.

6. Wählen Sie die Schaltfläche **Ausführen**. Warten Sie, bis der Test abgeschlossen ist.

7. Wählen Sie **See results** (Ergebnisse anzeigen) aus.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>Überprüfen der Entitätsbatchergebnisse

Das Diagramm wird geöffnet und zeigt alle Absichten mit den richtigen Vorhersagen an. Scrollen Sie im Filter auf der rechten Seite nach unten, um die fehlerhaften Entitätsvorhersagen zu finden. 

1. Wählen Sie die **Job**-Entität im Filter aus.

    ![Fehlerhafte Entitätsvorhersagen im Filter](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Das Diagramm wechselt zur Anzeige der Entitätsvorhersagen. 

2. Wählen Sie im unteren linken Quadranten des Diagramms **Falsch negatives Ergebnis** aus. Drücken Sie dann die Tastenkombination STRG+E, um zur Tokenansicht zu wechseln. 

    [![Tokenansicht der Entitätsvorhersagen](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Bei den Äußerungen unterhalb des Diagramms wird ein konsistenter Fehler angezeigt, wenn der Auftragsname `SQL` enthält. In den Beispieläußerungen und der Liste von Auftragsausdrücken wird „sql“ nur einmal und nur als Bestandteil des längeren Auftragsnamens `sql/oracle database administrator` verwendet.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Korrigieren der App anhand der Entitätsbatchergebnisse

Für die Korrektur der App muss die LUIS-App die Varianten der SQL-Aufträge korrekt ermitteln. Es gibt mehrere Optionen für diese Korrektur. 

* Explizites Hinzufügen von weiteren Beispieläußerungen, in denen „sql“ verwendet wird und die diese Wörter als Job-Entität bezeichnen 
* Explizites Hinzufügen von weiteren SQL-Aufträgen zur Liste von Ausdrücken

Diese Aufgaben liegen in Ihrem Zuständigkeitsbereich.

Durch Hinzufügen eines [Musters](luis-concept-patterns.md), bevor die Entität richtig vorhergesagt wird, wird das Problem nicht behoben. Denn es werden Musterübereinstimmungen gefunden, wenn alle Entitäten im Muster erkannt wurden. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Das Tutorial verwendete einen Batchtest zum Aufspüren von Problemen beim aktuellen Modell. Das Modell wurde repariert und erneut mit der Batchdatei getestet, um die Richtigkeit der Änderung zu überprüfen.

> [!div class="nextstepaction"]
> [Mehr über Muster erfahren](luis-tutorial-pattern.md)

