---
title: Vorhersageergebnisse
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ein Vorhersageergebnis gibt den Grad der Zuverlässigkeit an, den der LUIS-API-Dienst den Ergebnissen von Vorhersagen basierend auf einer Benutzeräußerung zuordnet.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813987"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Vorhersageergebnisse geben die Vorhersagegenauigkeit für Absichten und Entitäten an

Ein Vorhersageergebnis gibt den Grad der Zuverlässigkeit an, den LUIS den Ergebnissen von Vorhersagen basierend auf einer Benutzeräußerung zuordnet.

Ein Vorhersageergebnis liegt zwischen 0 (null) und 1 (eins). Ein Beispiel für eine hohe Zuverlässigkeitsbewertung von LUIS ist der Wert 0,99. Ein Beispiel für eine Bewertung mit niedriger Zuverlässigkeit ist 0,01. 

|Ergebniswert|Confidence|
|--|--|
|1|definitive Übereinstimmung|
|0,99|hohe Zuverlässigkeit|
|0.01|niedrige Zuverlässigkeit|
|0|definitiv keine Übereinstimmung|

Wenn eine Äußerung eine niedrige Zuverlässigkeitsbewertung liefert, hebt LUIS es auf der [LUIS](luis-reference-regions.md)-Website auf der Seite **Absicht** hervor und kennzeichnet die identifizierte **bezeichnete Absicht** rot.

![Abweichungen bei der Bewertung](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Absicht mit der höchsten Bewertung

Zu jeder Vorhersage einer Äußerung wird die am höchsten bewertete Absicht zurückgegeben. Diese Vorhersage ist ein numerischer Vergleich der Vorhersageergebnisse. Zwischen den 2 höchsten Bewertungen kann nur ein sehr kleiner Unterschied bestehen. LUIS zeigt diesen geringen Unterschied nicht an, sondern gibt lediglich das höchste Ergebnis zurück.  

## <a name="return-prediction-score-for-all-intents"></a>Zurückgeben der Vorhersageergebnisse für alle Absichten

Ein Test- oder Endpunktergebnis kann alle Absichten enthalten. Diese Konfiguration wird am [Endpunkt](https://aka.ms/v1-endpoint-api-docs) mit dem Name-Wert-Paar `verbose=true` in der Abfragezeichenfolge festgelegt.

## <a name="review-intents-with-similar-scores"></a>Überprüfen von Absichten mit ähnlichen Bewertungen

Durch das Überprüfen der Ergebnisse aller Absichten können Sie sicherstellen, dass nicht nur die richtige Absicht identifiziert wird, sondern auch die Bewertung der nächstniedrigeren Absicht bei allen Äußerungen deutlich niedriger ist.

Wenn mehrere Absichten ähnliche Vorhersagebewertungen haben, kann LUIS basierend auf dem Kontext einer Äußerung möglicherweise zwischen diesen Absichten wechseln. Um diese Situation zu beheben, fügen Sie jeder Absicht weiterhin Äußerungen mit einer größeren Vielfalt von kontextuellen Unterschieden hinzu, oder Sie können die Clientanwendung (z.B. einen Chatbot) programmgesteuerte Entscheidungen darüber treffen lassen, wie mit den 2 besten Absichten umgegangen werden soll.

2 Absichten, deren Bewertungen zu dicht beieinander liegen, können aufgrund von nicht deterministischem Training umgekehrt werden. Die höchste Bewertung könnte zur zweithöchsten werden und die zweithöchste zur höchsten. Um diese Situation zu verhindern, fügen Sie jeder der beiden oberen Absichten Beispieläußerungen für diese Äußerung mit Wortauswahl und Kontext, der die 2 Absichten unterscheidet, hinzu. Die beiden Absichten sollten etwa über die gleiche Anzahl von Beispieläußerungen verfügen. Eine Faustregel für die Trennung zum Verhindern der trainingsbedingten Umkehrung ist ein Unterschied von 15% in den Bewertungen.

Sie können das nicht deterministische Training deaktivieren, indem Sie [alle Daten für das Training verwenden](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Unterschiede bei Vorhersagen aus verschiedenen Trainingssitzungen

Wenn Sie das gleiche Modell in einer anderen App trainieren und die Ergebnisse nicht identisch sind, liegt dieser Unterschied daran, dass ein nicht deterministisches Training stattfindet (ein Zufallselement). Darüber hinaus bedeutet eine Überlappung einer Äußerung über mehrere Absichten, dass die am höchsten bewertete Absicht für dieselbe Äußerung wechseln kann.

Wenn Ihr Chatbot eine bestimmte LUIS-Bewertung erfordert, um die Zuverlässigkeit einer Absicht anzuzeigen, sollten Sie den Unterschied zwischen den Ergebnissen der beiden am höchsten bewerteten Absichten verwenden. Diese Situation bietet Flexibilität für Varianten im Training.

## <a name="e-exponent-notation"></a>E-Notation (Exponent)

Vorhersagebewertungen können exponentiell angegeben werden, um außerhalb des Intervalls von 0 bis 1 *angezeigt* zu werden, z.B. `9.910309E-07`. Dieses Ergebnis ist ein Hinweis auf eine sehr **kleine** Zahl.

|Bewertung in E-Notation |Tatsächliche Bewertung|
|--|--|
|9,910309E-07|0,0000009910309|

## <a name="punctuation"></a>Interpunktion

Interpunktion ist ein separates Token in LUIS. Eine Äußerung mit einem Punkt am Ende und eine Äußerung, in der dies nicht der Fall ist, sind zwei separate Äußerungen und erhalten möglicherweise zwei unterschiedliche Vorhersagen. Stellen Sie sicher, dass das Modell die Interpunktion entweder in den [Beispieläußerungen](luis-concept-utterance.md) (mit und ohne Interpunktion) oder in den [Mustern](luis-concept-patterns.md) behandelt, wo es mit der speziellen Syntax einfacher ist, die Interpunktion zu ignorieren: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Hinzufügen von Entitäten zu LUIS-Apps finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md).
