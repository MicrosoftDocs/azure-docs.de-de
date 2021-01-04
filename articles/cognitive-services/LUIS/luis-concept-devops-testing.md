---
title: Testen von LUIS-Apps in DevOps
description: Erfahren Sie, wie Sie Ihre LUIS-App (Language Understanding) in einer DevOps-Umgebung testen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: f002bfdd5aeb784b5b10b549389e663216fa0361
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561221"
---
# <a name="testing-for-luis-devops"></a>Testen für LUIS DevOps

Softwareentwickler, die eine Language Understanding-App (LUIS) entwickeln, können DevOps-Verfahren für [Quellcodeverwaltung](luis-concept-devops-sourcecontrol.md), [automatisierte Builds](luis-concept-devops-automation.md), [Tests](luis-concept-devops-testing.md) und [Releaseverwaltung](luis-concept-devops-automation.md#release-management) anwenden, indem sie diese Richtlinien befolgen.

Bei den Methoden der agilen Softwareentwicklung spielt Testen eine zentrale Rolle beim Erstellen von Qualitätssoftware. Jede bedeutende Änderung an einer LUIS-App sollte mit Tests einhergehen, die für das Testen der neuen Funktionalität ausgelegt wurden, die der Entwickler in die App integriert. Diese Tests werden zusammen mit dem `.lu`-Quellcode Ihrer LUIS-App in Ihr Quellcoderepository eingecheckt. Die Implementierung der Änderung ist abgeschlossen, wenn die App die Tests zufriedenstellend absolviert hat.

Tests sind ein kritischer Bestandteil von [CI/CD-Workflows](luis-concept-devops-automation.md). Wenn in einem Pull Request (PR) Änderungen an einer LUIS-App vorgeschlagen werden oder Änderungen in Ihrem Mainbranch zusammengeführt wurden, sollten CI-Workflow die Tests ausführen, um zu überprüfen, ob durch die Änderungen Regressionen ausgelöst wurden.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Ausführen von Komponententests und Testen in Batches

Es gibt zwei verschiedene Arten von Tests für eine LUIS-App, die Sie in Continuous Integration Workflows ausführen müssen:

- **Komponententests**: Relativ einfache Tests, mit denen die Hauptfunktionen ihrer Luis-APP überprüft werden. Ein Komponententest ist bestanden, wenn für eine bestimmte Testäußerung die erwartete Absicht und die erwarteten Entitäten zurückgegeben werden. Damit der Test erfolgreich bestanden ist, müssen alle Komponententests bestanden werden.  
Diese Art Test ähnelt den [interaktiven Tests](./luis-concept-test.md), die Sie im [LUIS-Portal](https://www.luis.ai/) ausführen können.

- **Batchtests**: Das Testen in Batches stellt umfassende Tests Ihres aktuellen trainierten Modells dar, um seine Leistung zu messen. Im Gegensatz zu Komponententests liegt Batchtests nicht die Unterscheidung „bestanden|nicht bestanden“ zugrunde. Die Erwartung beim Testen in Batches richtet sich nicht darauf, dass jeder Test die erwartete Absicht und die erwarteten Entitäten zurückgibt. Stattdessen hilft Ihnen das Testen in Batches, die Genauigkeit jeder Absicht und jeder Entität in Ihrer App darzustellen und im zeitlichen Verlauf zu vergleichen, wenn Sie Verbesserungen vornehmen.  
Diese Tests sind von der gleichen Art wie das [Testen in Batches](./luis-concept-batch-test.md), das Sie interaktiv im LUIS-Portal ausführen können.

Sie können Komponententests vom Beginn Ihres Projekts an einsetzen. Testen in Batches ist erst wirklich von Nutzen, wenn Sie das Schema Ihrer LUIS-App entwickelt haben und an der Verbesserung ihrer Genauigkeit arbeiten.

Sowohl bei Komponententests als auch beim Testen in Batches sollten Sie darauf achten, dass Ihre Testäußerungen von Ihren Trainingsäußerungen getrennt bleiben. Wenn Sie mit den gleichen Daten testen, mit denen auch das Training erfolgt, erhalten Sie den falschen Eindruck, dass Ihre App eine gute Leistung aufweist, wenn lediglich eine Überanpassung an die Testdaten vorliegt. Tests dürfen vom Modell nicht erkannt werden, um zu überprüfen, wie gut es generalisiert.

### <a name="writing-tests"></a>Schreiben von Tests

Wenn Sie einen Satz von Tests schreiben, müssen Sie Folgendes für jeden von ihnen definieren:

* Testen der Äußerung
* Erwartete Absicht
* Erwartete Entitäten

Verwenden Sie die [Batchdateisyntax](./luis-concept-batch-test.md#batch-syntax-template-for-intents-with-entities) von LUIS, um eine Gruppe von Tests in einer Datei im JSON-Format zu definieren. Beispiel:

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

Einige Testtools, wie z. B. [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) unterstützen außerdem Testdateien im LUDown-Format.

#### <a name="designing-unit-tests"></a>Entwerfen von Komponententests

Komponententests sollten für das Testen der Kernfunktionen Ihrer LUIS-App ausgelegt werden. In jeder Iteration oder jedem Sprint Ihrer App-Entwicklung sollten Sie eine ausreichende Anzahl Tests schreiben, um zu überprüfen, ob die Schlüsselfunktionen, die Sie in dieser Iteration implementieren, ordnungsgemäß funktionieren.

Jeder Komponententest gibt Ihnen für eine bestimmte Testäußerung diese Möglichkeiten:

* Testen, ob die richtige Absicht zurückgegeben wird
* Testen, ob die Schlüsselentitäten – jede, die für Ihre Lösung kritisch sind – zurückgegeben werden
* Testen, ob das [Vorhersageergebnis](./luis-concept-prediction-score.md) für Absicht und Entitäten einen von Ihnen definierten Schwellenwert überschreitet. Beispielsweise können Sie entscheiden, dass ein Test nur als bestanden gilt, wenn das Vorhersageergebnis für die Absicht und Ihre Schlüsselentitäten 0,75 überschreitet.

In Komponententest empfiehlt es sich, zu testen, ob Ihre Schlüsselentitäten in der Vorhersageantwort zurückgegeben werden, falsch positive Ergebnisse aber zu ignorieren. *Falsch positiv* sind Entitäten, die in der Vorhersageantwort auftreten, die aber nicht in den erwarteten Ergebnissen für den Test definiert sind. Das Ignorieren von falsch positiven Ergebnissen macht das Schreiben von Komponententests weniger beschwerlich, erlaubt es Ihnen aber trotzdem, das Augenmerk bei den Tests darauf zu legen, dass in einer Vorhersageantwort die für Ihre Lösung entscheidenden Daten zurückgegeben werden.

> [!TIP]
> Das [NLU.DevOps](https://github.com/microsoft/NLU.DevOps)-Tool unterstützt alle Ihre Testanforderungen für LUIS. Der Befehl `compare` bewirkt bei der Verwendung im [Komponententestmodus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode), dass alle Tests bestanden und falsch positive Ergebnisse für Entitäten ignoriert werden, die nicht in den erwarteten Ergebnissen bezeichnet sind.

#### <a name="designing-batch-tests"></a>Entwerfen des Testens in Batches

Das Testen in Batches sollte eine große Anzahl von Testfällen umfassen, die dafür ausgelegt sind, alle Absichten und alle Entitäten in Ihrer LUIS-App abzudecken. Informationen zum Definieren eines Satzes für das Testen in Batches finden Sie unter [Batch testing in the LUIS portal](./luis-concept-batch-test.md) (Testen in Batches im LUIS-Portal).

### <a name="running-tests"></a>Ausführen von Tests

Das LUIS-Portal bietet Funktionen zur Unterstützung von interaktiven Tests:

* [**Interaktive Tests**](./luis-concept-test.md) ermöglichen es Ihnen, eine Beispieläußerung zu senden und die von LUIS erkannten Absichten und Entitäten in der Antwort zu erhalten. Der Erfolg des Tests wird durch visuelle Untersuchung überprüft.

* Beim [**Testen in Batches**](./luis-concept-batch-test.md) wird eine Batchtestdatei als Eingabe verwendet, um Ihre aktive trainierte Version zu überprüfen, um ihre Vorhersagegenauigkeit zu messen. Ein Batchtest zeigt die Ergebnisse in einem Diagramm an, sodass Sie einen besseren Einblick in die Genauigkeit der einzelnen Absichten und Entitäten in Ihrer aktuellen Version erhalten.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Ausführen von Tests in einem automatisierten Buildworkflow

Die Funktionen zum interaktiven Testen im LUIS-Portal sind nützlich, für DevOps sind automatisierte Tests, die in einem CI/CD-Workflow ausgeführt werden, jedoch an bestimmte Anforderungen gebunden:

* Testtools müssen in einem Workflowschritt auf einem Buildserver ausgeführt werden. Das bedeutet, die Tools müssen von der Befehlszeile aus ausführbar sein.
* Die Testtools müssen eine Gruppe von Tests für einen Endpunkt ausführen und die tatsächlichen Ergebnisse automatisch anhand erwarteten Ergebnisse überprüfen können.
* Wenn bei Tests Fehler auftreten, müssen die Testtools einen Statuscode zurückgeben, um den Workflow anzuhalten, und den Build fehlschlagen zu lassen.

LUIS bietet weder ein Befehlszeilentool noch eine allgemeine API, die diese Funktionalität bereitstellt. Wir empfehlen, das [NLU.DevOps](https://github.com/microsoft/NLU.DevOps)-Tool zum Ausführen der Tests und Überprüfen der Ergebnisse zu verwenden, sowohl an der Befehlszeile als auch beim automatischen Testen im Rahmen eines CI/CD-Workflows.

Für die Testfunktionen, die im LUIS-Portal verfügbar sine, ist kein veröffentlichter Endpunkt erforderlich, und sie bilden einen Teil der Erstellungsfunktionen von LUIS. Wenn Sie Tests in einem automatisierten Buildworkflow implementieren, müssen Sie die zu testende Version der LUIS-App an einem Endpunkt veröffentlichen, damit Testtools wie NLU.DevOps im Rahmen der Tests Vorhersageanforderungen senden können.

> [!TIP]
> * Wenn Sie Ihre eigene Testlösung implementieren und Code schreiben, um Testäußerungen an einen Endpunkt zu senden, bedenken Sie, dass die zulässige Transaktionsrate beim Verwenden des LUIS-Erstellungsschlüssels auf 5 TPS beschränkt ist. Begrenzen Sie entweder die Senderate, oder verwenden Sie stattdessen einen Vorhersageschlüssel.
> * Vergessen Sie beim Senden von Testabfragen an einen Endpunkt nicht, `log=false` in der Abfragezeichenfolge Ihrer Vorhersageanforderung zu verwenden. Dadurch wird sichergestellt, dass Ihre Testäußerungen nicht von LUIS protokolliert werden und sich schließlich in der Liste der zu überprüfenden Endpunktäußerungen wiederfinden, die von der LUIS-Funktion für [aktives Lernen](./luis-concept-review-endpoint-utterances.md) unterhalten wird. In der Folge würden sie unbeabsichtigt den Trainingsäußerungen Ihrer App hinzugefügt.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Ausführen von Komponententests an der Befehlszeile und in CI/CD-Workflows

Sie können das [NLU.DevOps](https://github.com/microsoft/NLU.DevOps)-Paket verwenden, um Tests an der Befehlszeile auszuführen:

* Verwenden Sie den NLU.DevOps-[Testbefehl](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md), um Tests aus einer Testdatei an einen Endpunkt zu senden und die tatsächlichen Vorhersageergebnisse in einer Datei zu erfassen.
* Verwenden Sie den NLU.DevOps[-Vergleichsbefehl](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md), um die tatsächlichen Ergebnisse mit den erwarteten Ergebnissen zu vergleichen, die in der Eingabetestdatei definiert sind. Mit dem Befehl `compare` wird die NUnit-Testausgabe generiert, und bei der Verwendung im [Komponententestmodus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) wird mit Verwendung des `--unit-test`-Flags sichergestellt, dass alle Tests bestanden werden.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Ausführen von Batchtests an der Befehlszeile und in CI/CD-Workflows

Sie können das NLU.DevOps-Paket außerdem verwenden, um Batchtests an der Befehlszeile auszuführen.

* Verwenden Sie den NLU.DevOps-[Testbefehl](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md), um Tests aus einer Testdatei an einen Endpunkt zu senden und die tatsächlichen Vorhersageergebnisse in einer Datei zu erfassen, genau wie bei Komponententests.
* Verwenden Sie den [Vergleichsbefehl](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) von NLU.DevOps im [Leistungstestmodus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode), um die Leistung Ihrer App zu messen. Sie können außerdem die Leistung Ihrer App mit einem Baselineleistungsbenchmark vergleichen, beispielsweise die Ergebnisse des letzten Commits mit einem Mainbranch oder dem aktuellen Release. Im Leistungstestmodus generiert der `compare`-Befehl eine NUnit-Testausgabe und [Batchtestergebnisse](./luis-glossary.md#batch-test) im JSON-Format.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>Nicht deterministisches Training in LUIS und die Auswirkungen auf Tests

Beim Trainieren eines Modells, beispielsweise einer Absicht, benötigt LUIS sowohl positive Daten – die bezeichneten Trainingsäußerungen, die Sie zum Trainieren der App für das Modell zur Verfügung gestellt haben – als auch negative Daten – Daten, die *keine* gültigen Beispiele für die Verwendung des Modells darstellen. Während des Trainings erstellt LUIS die negativen Daten eines Modells aus all den positiven Daten, die Sie für die anderen Modelle angegeben haben, in einigen Fällen kann dabei aber ein Ungleichgewicht der Daten entstehen. Um dieses Ungleichgewicht zu vermeiden, erstellt LUIS in nicht deterministischer Weise eine Teilmenge der negativen Daten in einer Stichprobe, um einen ausgeglicheneren Trainingssatz, bessere Leistung des Modells und kürzere Trainingszeiten zu erreichen.

Das Ergebnis dieses nicht deterministischen Trainings ist, dass Sie eine [zwischen verschiedenen Trainingssitzungen leicht abweichende Vorhersageantwort](./luis-concept-prediction-score.md) erhalten können, normalerweise für Absichten und/oder Äußerungen, deren [Vorhersageergebnis](./luis-concept-prediction-score.md) nicht hoch ist.

Wenn Sie das nicht deterministische Training für die von Ihnen erstellten LUIS-Apps für Testzwecke deaktivieren möchten, verwenden Sie die [Versionseinstellungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings), mit auf `true` festgelegter Einstellung `UseAllTrainingData`.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum [Implementieren von CI/CD-Workflows](luis-concept-devops-automation.md)
* Erfahren Sie, wie Sie [DevOps für LUIS mit GitHub implementieren](luis-how-to-devops-with-github.md)