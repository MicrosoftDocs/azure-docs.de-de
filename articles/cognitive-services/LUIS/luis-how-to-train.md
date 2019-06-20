---
title: Trainieren der App
titleSuffix: Language Understanding - Azure Cognitive Services
description: Sie trainieren die Version Ihrer LUIS-App, indem Sie ihr Fähigkeiten vermitteln, mit denen sie natürliche Sprache besser verstehen kann. Trainieren Sie Ihre LUIS-App, nachdem Sie Ihr Modell aktualisiert haben, indem Sie Entitäten, Absichten und Äußerungen hinzufügen, bearbeiten, bezeichnen oder löschen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/07/2019
ms.author: diberry
ms.openlocfilehash: ba0db22437961a33b0b415ec7cb60ad3df12821c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195849"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Trainieren Ihrer aktiven Version der LUIS-App 

Sie trainieren Ihre LUIS-App, indem Sie ihr Fähigkeiten vermitteln, mit denen sie natürliche Sprache besser verstehen kann. Trainieren Sie Ihre LUIS-App, nachdem Sie Ihr Modell aktualisiert haben, indem Sie Entitäten, Absichten und Äußerungen hinzufügen, bearbeiten, bezeichnen oder löschen. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Das Trainieren und [Testen](luis-concept-test.md) eine App ist ein iterativer Vorgang. Nachdem Sie Ihre LUIS-App trainiert haben, sollten Sie sie mit Beispieläußerungen testen, um festzustellen, ob die Absichten und Entitäten ordnungsgemäß erkannt werden. Ist das nicht der Fall, aktualisieren Sie die LUIS-App, und trainieren und testen Sie sie erneut. 

Das Training wird auf die aktive Version im LUIS-Portal angewandt. 

## <a name="how-to-train-interactively"></a>Interaktives Trainieren

Sie müssen Ihre LUIS-App mindestens zunächst mindestens einmal im [LUIS-Portal](https://www.luis.ai) trainieren, damit Sie den iterativen Prozess starten können. Vergewissern Sie sich vor dem Training, dass jeder Absicht mindestens eine Äußerung zugeordnet ist.

1. Greifen Sie auf Ihre App zu, indem Sie ihren Namen auf der Seite **Meine Apps** auswählen. 

2. Klicken Sie in Ihrer App im oberen Bereich auf **Trainieren**. 

3. Wenn das Training abgeschlossen ist, wird im oberen Bereich des Browsers eine Benachrichtigungsleiste in grün angezeigt.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Wenn Ihre App mindestens eine Absicht aufweist, die keine Beispieläußerungen enthält, können Sie Ihre App nicht trainieren. Fügen Sie für sämtliche Absichten Äußerungen hinzu. Weitere Informationen finden Sie unter [Add example utterances (Hinzufügen von Beispieläußerungen)](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Datum und Uhrzeit für das Training

Das Datum und die Uhrzeit für das Training ist GMT + 2. 

## <a name="train-with-all-data"></a>Trainieren mit allen Daten

Das Training verwendet einen kleinen Prozentsatz negativer Stichproben. Wenn Sie alle Daten anstelle der kleinen Menge entnommener negativer Stichproben verwenden möchten, verwenden Sie die [Versionseinstellungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) mit auf TRUE festgelegter Option `UseAllTrainingData`, um dieses Feature zu deaktivieren. 

## <a name="unnecessary-training"></a>Unnötiges Trainieren

Sie müssen nicht nach jeder einzelnen Änderung trainieren. Trainings sollten durchgeführt werden, nachdem mehrere Änderungen auf das Modell angewandt wurden und Sie im nächsten Schritt testen oder veröffentlichen möchten. Wenn Sie weder testen noch veröffentlichen möchten, ist kein Training erforderlich. 

## <a name="training-with-the-rest-apis"></a>Trainieren mit der REST-API

Das Trainieren umfasst im LUIS-Portal einen einzigen Schritt – das Klicken auf die Schaltfläche **Trainieren**. Mit der REST-APIs sind zwei Schritte erforderlich. Die erste besteht darin, mit einer HTTP POST-Anforderung das [Trainieren anzufordern](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45). Fragen Sie dann den [Trainingsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) mit HTTP Get ab. 

Um zu ermitteln, wann das Training abgeschlossen ist, müssen Sie den Status abrufen, bis alle Modelle erfolgreich trainiert wurden. 

## <a name="next-steps"></a>Nächste Schritte

* [Bezeichnen von vorgeschlagenen Äußerungen mit LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Verwenden von Features zum Verbessern der Leistung Ihrer LUIS-App](luis-how-to-add-features.md) 
