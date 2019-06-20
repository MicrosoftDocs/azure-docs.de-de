---
title: Migrieren Ihres Projekts zur 3.0 API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Custom Vision-Projekte von der vorherigen Version der API zur 3.0 API migrieren können.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816457"
---
# <a name="migrate-to-the-30-api"></a>Migrieren zur 3.0 API

Custom Vision hat jetzt die allgemeine Verfügbarkeit erreicht und ein API-Update erhalten.
Dieses Update umfasst ein paar neue Features sowie einige entscheidende Änderungen:

* Die Vorhersage-API ist jetzt basierend auf dem Projekttyp in zwei Teile unterteilt.
* Die VAIDK-Exportoption (Vision AI Developer Kit) erfordert die Erstellung eines Projekts auf eine bestimmte Weise.
* Standarditerationen wurden zugunsten einer benannten Iteration für die Veröffentlichung/Aufhebung der Veröffentlichung entfernt.

In diesem Leitfaden erfahren Sie, wie Sie Ihre Projekte aktualisieren können, um mit der neuen API-Version zu arbeiten. Eine vollständige Liste der Änderungen finden Sie in den [Versionshinweisen](release-notes.md).

## <a name="use-the-updated-prediction-api"></a>Verwenden der aktualisierten Vorhersage-API

Die APIs der Version 2.x verwendeten denselben Vorhersageaufruf sowohl für Bildklassifizierer als auch für Objekterkennungsprojekte. Beide Projekttypen waren für die Aufrufe **PredictImage** und **PredictImageUrl** zulässig. Ab 3.0 wurde diese API aufgeteilt, sodass Sie den Projekttyp an den Aufruf anpassen müssen:

* Verwenden Sie **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** und **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** , um Vorhersagen für Bildklassifizierungsprojekte zu erhalten.
* Verwenden Sie **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** und **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** , um Vorhersagen für Objekterkennungsprojekte zu erhalten.

## <a name="use-the-new-iteration-publishing-workflow"></a>Verwenden des neuen Workflows zur Iterationsveröffentlichung

Die APIs der Version 2.x verwendeten die Standarditeration oder eine bestimmte Iterations-ID, um die für die Vorhersage zu verwendende Iteration auszuwählen. Ab 3.0 wurde ein Veröffentlichungsablauf eingeführt, bei dem Sie zunächst eine Iteration unter einem bestimmten Namen aus der Trainings-API veröffentlichen. Anschließend übergeben Sie den Namen an die Vorhersagemethoden, um festzulegen, welche Iteration verwendet werden soll.

> [!IMPORTANT]
> Die 3.0 APIs verwenden nicht das Standarditerationsfeature. Bis die älteren APIs veraltet sind, können Sie die APIs der Version 2.x weiterhin verwenden, um eine Iteration als Standard festzulegen. Diese APIs werden für einen bestimmten Zeitraum aufrechterhalten, und Sie können die Methode **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** aufrufen, um eine Iteration als Standard zu kennzeichnen.

### <a name="publish-an-iteration"></a>Veröffentlichen einer Iteration

Sobald eine Iteration trainiert wurde, können Sie sie mit der Methode **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** für die Vorhersage zur Verfügung stellen. Zum Veröffentlichen einer Iteration benötigen Sie die Ressourcen-ID der Vorhersage, die auf der Einstellungsseite der Custom Vision-Website verfügbar ist.

![Die Einstellungsseite der Custom Vision-Website mit der beschriebenen ID der Vorhersageressource.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Sie können diese Informationen auch über das [Azur-Portal](https://portal.azure.com) abrufen, indem Sie zur Custom Vision-Vorhersageressource wechseln und **Eigenschaften** auswählen.

Nachdem Ihre Iteration veröffentlicht wurde, kann sie von Apps zur Vorhersage verwendet werden, indem sie den Namen in ihrem Aufruf der Vorhersage-API angeben. Verwenden Sie die API **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** , um eine Iteration für Vorhersageaufrufe nicht verfügbar zu machen.

## <a name="additional-export-options"></a>Zusätzliche Exportoptionen

Mit den 3.0 APIs werden zwei zusätzliche Exportziele vorgestellt: ARM-Architektur und Vision AI Developer Kit.

* Damit Sie ARM verwenden können, müssen Sie nur eine kompakte Domäne und dann DockerFile und ARM als Exportoptionen auswählen.
* Für das Vision AI Developer Kit muss das Projekt mit der Domäne __Allgemein (kompakt)__ erstellt werden und VAIDK im Argument für die Zielexportplattformen angegeben werden.

## <a name="next-steps"></a>Nächste Schritte

* [Referenzdokumentation zur Trainings-API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Referenzdokumentation zur Vorhersage-API (REST)](https://go.microsoft.com/fwlink/?linkid=865445)