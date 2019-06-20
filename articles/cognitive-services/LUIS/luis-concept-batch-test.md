---
title: Testen in Batches
titleSuffix: Language Understanding - Azure Cognitive Services
description: Verwenden Sie Batchtests, um Ihre Anwendung fortlaufend zu optimieren und ihr Sprachverständnis zu verbessern.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: acb561970b6a8576d1219fc15758e21a3032c9e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813297"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Testen von Batches mit 1.000 Äußerungen im LUIS-Portal

Mit Batchtests wird Ihr [aktives](luis-concept-version.md#active-version) trainiertes Modell überprüft, um seine Vorhersagegenauigkeit zu messen. Mithilfe eines Batchtests können Sie die Genauigkeit der einzelnen Absichten und Entitäten in Ihrem aktuellen trainierten Modell in einem Diagramm anzeigen. Überprüfen Sie die Ergebnisse des Batchtests, um geeignete Maßnahmen zum Verbessern der Genauigkeit einzuleiten, etwa indem Sie einer Absicht weitere Beispieläußerungen hinzufügen, wenn Ihre App häufig Fehler beim Erkennen der korrekten Absicht macht.

## <a name="group-data-for-batch-test"></a>Gruppieren von Daten für Batchtests

Es ist wichtig, dass die für Batchtests verwendeten Äußerungen für LUIS neu sind. Wenn Sie über ein Dataset mit Äußerungen verfügen, teilen Sie die Äußerungen in diese drei Mengen auf: Äußerungen, die einer Absicht hinzugefügt wurden, Äußerungen, die vom veröffentlichten Endpunkt empfangen wurden und Äußerungen, die für Batchtests von LUIS nach dem Training verwendet werden. 

## <a name="a-dataset-of-utterances"></a>Ein Dataset mit Äußerungen

Senden Sie eine Batchdatei mit Äußerungen, die als *Dataset* bezeichnet wird, zum Batchtesten. Das Dataset ist eine Datei im JSON-Format, die maximal 1.000 bezeichnete **nicht doppelt vorhandene** Äußerungen enthält. Sie können bis zu 10 Datasets in einer App testen. Wenn Sie mehr testen müssen, löschen Sie ein Dataset, und fügen Sie dann ein neues hinzu.

|**Regeln**|
|--|
|\* Keine doppelten Äußerungen|
|1000 Äußerungen oder weniger|

\* Als doppelte Äußerungen gelten exakt übereinstimmende Zeichenfolgen, nicht Übereinstimmungen, die zuerst in Token übersetzt wurden. 

## <a name="entities-allowed-in-batch-tests"></a>In Batchtests zulässige Entitäten

Alle benutzerdefinierten Entitäten im Modell werden auch dann im Batchtest-Entitätenfilter angezeigt, wenn keine entsprechenden Entitäten in den Batchdateidaten vorhanden sind.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batchdateiformat

Die Batchdatei besteht aus Äußerungen. Jeder Äußerung muss eine erwartete Absichtsvorhersage in Verbindung mit allen [maschinell gelernten Entitäten](luis-concept-entity-types.md#types-of-entities), deren Erkennung Sie erwarten, zugeordnet sein. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Batchsyntaxvorlage für Absichten mit Entitäten

Verwenden Sie die folgende Vorlage, um die Batchdatei zu starten:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Die Batchdatei erkennt anhand der Eigenschaften **startPos** und **endPos** Anfang und Ende einer Entität. Die Werte sind nullbasiert und dürfen nicht mit einem Leerzeichen beginnen oder enden. Dies ist ein Unterschied zu den Abfrageprotokollen, die die Eigenschaften „startIndex“ und „endIndex“ verwenden. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Batchsyntaxvorlage für Absichten ohne Entitäten

Verwenden Sie die folgende Vorlage, um Ihre Batchdatei ohne Entitäten zu starten:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Wenn Sie keine Entitäten testen möchten, schließen Sie die Eigenschaft `entities` ein, und legen Sie den Wert auf ein leeres Array (`[]`) fest.


## <a name="common-errors-importing-a-batch"></a>Häufige Fehler beim Importieren eines Batches

Häufige Fehler sind z.B. folgende: 

> * Mehr als 1.000 Äußerungen
> * Ein JSON-Äußerungsobjekt ohne Entitätseigenschaft. Die Eigenschaft kann ein leeres Array sein.
> * Wörter, die in mehreren Entitäten bezeichnet werden
> * Entitätsbezeichnung, die mit einem Leerzeichen beginnt oder endet

## <a name="batch-test-state"></a>Batchteststatus

LUIS verfolgt für jedes Dataset den Status des letzten Tests nach. Dieser umfasst die Größe (Anzahl der Äußerungen im Batch), das Datum der letzten Ausführung und das letzte Ergebnis (Anzahl der erfolgreich vorhergesagten Äußerungen).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Batchtestergebnisse

Das Batchtestergebnis ist ein Punktdiagramm, auch als Fehlermatrix bekannt. Dieses Diagramm stellt einen Vierfachvergleich der Äußerungen in der Batchdatei mit den vorhergesagten Absichten und Entitäten des aktuellen Modells dar. 

Datenpunkte in den Abschnitten **False Positive** (Falsch positives Ergebnis) und **False Negative** (Falsch negatives Ergebnis) zeigen Fehler an, die untersucht werden sollten. Wenn sich alle Datenpunkte in den Abschnitten **True Positive** (Echt positives Ergebnis) und **True Negative** (Echt negatives Ergebnis) befinden, ist die Genauigkeit Ihrer App für dieses Dataset perfekt.

![Vier Abschnitte des Diagramms](./media/luis-concept-batch-test/chart-sections.png)

Dieses Diagramm unterstützt Sie beim Auffinden von Äußerungen, die LUIS auf der Grundlage seines aktuellen Trainings falsch vorhersagt. Die Ergebnisse werden pro Bereich des Diagramms angezeigt. Wählen Sie einzelne Punkte im Diagramm aus, um die Äußerungsinformationen zu überprüfen, oder wählen Sie den Bereichsnamen aus, um Ergebnisse für Äußerungen im betreffenden Bereich zu überprüfen.

![Batchtests](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fehler in den Ergebnissen

Fehler im Batchtest zeigen Absichten an, die nicht so wie in der Batchdatei vermerkt vorhergesagt werden. Fehler werden in den beiden roten Abschnitten des Diagramms angezeigt. 

Der Abschnitt für falsch positive Ergebnisse zeigt an, dass eine Übereinstimmung einer Äußerung mit einer Absicht oder Entität bestand, die sich nicht hätte ergeben sollen. Der Abschnitt für falsch negative Ergebnisse zeigt an, dass keine Übereinstimmung einer Äußerung mit einer Absicht oder Entität bestand, die sich hätte ergeben sollen. 

## <a name="fixing-batch-errors"></a>Beheben von Batchfehlern

Wenn im Batchtest Fehler auftreten, können Sie entweder weitere Äußerungen zu einer Absicht hinzufügen und/oder weitere Äußerungen mit der Entität bezeichnen, um LUIS bei der Unterscheidung zwischen Absichten zu unterstützen. Wenn Sie Äußerungen hinzugefügt und sie bezeichnet haben und trotzdem noch Vorhersagefehler beim Batchtest erhalten, erwägen Sie, ein [Begriffslisten](luis-concept-feature.md)-Feature mit domänenspezifischem Fachwortschatz hinzuzufügen, um LUIS beim schnelleren Lernen zu unterstützen. 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [in Batches testen](luis-how-to-batch-test.md).
