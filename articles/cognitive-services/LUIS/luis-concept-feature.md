---
title: Features
titleSuffix: Language Understanding - Azure Cognitive Services
description: Fügen Sie einem Sprachmodell Features hinzu, um Hinweise zur Erkennung von Eingaben, die Sie bezeichnen oder klassifizieren möchten, bereitzustellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 7889f223b607912fd88c798b31ec028f97dfbbd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812924"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Features für Begriffslisten in Ihrer LUIS-App

Beim maschinellen Lernen ist ein *Feature* ein eindeutiges Merkmal oder Attribut der Daten, die Ihr System untersucht. 

Fügen Sie einem Sprachmodell Features hinzu, um Hinweise zur Erkennung von Eingaben, die Sie bezeichnen oder klassifizieren möchten, bereitzustellen. Features helfen LUIS Absichten und Entitäten zu erkennen, Features sind jedoch nicht selbst Absichten oder Entitäten. Stattdessen können Features Beispiele für verwandte Begriffe liefern.  

## <a name="what-is-a-phrase-list-feature"></a>Was ist ein Feature für eine Ausdrucksliste?
Eine Begriffsliste ist eine Liste von Wörtern oder Wortgruppen, die für Ihre App relevanter als andere Wörter in Äußerungen sind. Eine Begriffsliste ist Teil des Vokabulars der App-Domäne als weiterer Hinweis zu diesen Wörtern für LUIS. Erkenntnisse, die LUIS zu einem davon sammelt, werden automatisch auch auf die anderen angewandt. Diese Liste ist keine geschlossene [Listenentität](luis-concept-entity-types.md#types-of-entities) mit genauen Textübereinstimmungen.

Begriffslisten tragen nicht zur Wortstammerkennung bei, sodass Sie Beispiele für Äußerungen hinzufügen müssen, die unterschiedliche Wortstammerkennungen für alle wichtigen Wörter und Ausdrücke verwenden.

## <a name="phrase-lists-help-all-models"></a>Begriffslisten: hilfreich für alle Modelle

Begriffslisten sind nicht mit einer bestimmten Absicht oder Entität verknüpft, sondern kommen allen Absichten und Entitäten zugute. Sie dienen zur Verbesserung der Absichtserkennung und der Entitätsklassifizierung.

## <a name="how-to-use-phrase-lists"></a>Verwenden von Ausdruckslisten

Erstellen Sie eine Begriffsliste, wenn Ihre App Wörter oder Ausdrücke aufweist, die für die App wichtig sind, z.B.:

* Branchenbegriffe
* Jargon
* Abkürzungen
* Unternehmensspezifische Sprache
* Ausdrücke in einer anderen Sprache, die in Ihrer App jedoch häufig verwendet werden
* Schlüsselwörter und -ausdrücke in Ihren Beispieläußerungen

Nachdem Sie einige Wörter oder Ausdrücke eingegeben haben, verwenden Sie die Funktion **Empfehlen**, um ähnliche Werte zu ermitteln. Überprüfen Sie die verwandten Werte, bevor Sie sie Ihrer Begriffsliste als neue Werte hinzufügen.

|Listentyp|Zweck|
|--|--|
|Austauschbar|Synonyme oder Wörter, die beim Austausch mit einem anderen Wort in der Liste dieselbe Absicht ausdrücken und in dieselben Entitäten extrahiert werden.|
|Nicht austauschbar|App-Vokabular, das spezifisch für Ihre App ist und keinen allgemeinen Wörtern in der jeweiligen Sprache entspricht.|

### <a name="interchangeable-lists"></a>Listen austauschbarer Ausdrücke

Eine Liste mit *austauschbaren Begriffen* enthält Werte, die Synonyme sind. Angenommen, Sie nutzen verschiedene Gewässer und eine Beispieläußerung wie: 

* Welche Städte befinden sich in der Nähe der Great Lakes? 
* Welche Straße führt entlang Lake Havasu?
* Wo entspringt und mündet der Nil? 

Jede Äußerung sollte unabhängig vom Gewässer sowohl für die Absicht als auch die Entitäten gelten: 

* Welche Städte befinden sich in der Nähe von [Gewässer]?
* Welche Straße führt entlang [Gewässer]?
* Wo entspringt und mündet [Gewässer]? 

Da die Wörter oder Begriffe für das Gewässer Synonyme und in den Äußerungen untereinander austauschbar sind, verwenden Sie in der Begriffsliste die Einstellung **Austauschbar**. 

### <a name="non-interchangeable-lists"></a>Listen nicht austauschbarer Ausdrücke

Eine Liste nicht austauschbarer Begriffe ist ein Signal, mit dem die Erkennung in LUIS ausgelöst wird. Die Begriffsliste gibt Wörter oder Ausdrücke an, die wichtiger als andere Wörter sind. Dies hilft bei der Erkennung der Absicht und der Entitäten. Angenommen, Ihr Thema sind weltweite Reisen (also über Kulturen hinweg aber immer noch in einer einzigen Sprache). Es gibt Wörter und Ausdrücke, die für die App wichtig, aber nicht synonym sind. 

Verwenden Sie als weiteres Beispiel eine Liste nicht austauschbarer Begriffe für seltene, proprietäre und Fremdwörter. LUIS kann möglicherweise seltene und proprietäre Wörter sowie Fremdwörter (außerhalb der Kultur der App) nicht erkennen. Diese nicht austauschbare Einstellung gibt an, dass die seltenen Wortformen eine Klasse bilden, deren Erkennung LUIS erlernen sollte, die aber keine Synonyme oder untereinander austauschbar sind.

Fügen Sie einer Begriffsliste nicht alle möglichen Wörter oder Ausdrücke hinzu, sondern immer nur einige Wörter oder Ausdrücke, und führen Sie dann ein Training und eine erneute Veröffentlichung durch. 

Wenn die Begriffsliste Ihrer App mit der Zeit anwächst, können einige Ausdrücke möglicherweise verschiedene Formen (Synonyme) aufweisen. Übertragen Sie diese in eine andere Begriffsliste, die austauschbar ist. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Begriffslisten helfen bei der Erkennung einfach austauschbarer Entitäten
Austauschbare Begriffslisten sind eine gute Möglichkeit, die Leistung Ihrer LUIS-App zu optimieren. Wenn die App Probleme bei der Vorhersage von Äußerungen zu den richtigen Absichten oder der Erkennung von Entitäten hat, sollten Sie überlegen, ob die Äußerungen ungewöhnliche oder mehrdeutige Wörter enthalten. Diese Wörter sind gute Kandidaten für eine Ausdrucksliste.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Ausdruckslisten helfen durch ein besseres Kontextverständnis bei der Erkennung von Absichten
Eine Ausdrucksliste ist keine Anweisung an LUIS, einen strikten Abgleich auszuführen oder immer alle Begriffe in der Ausdrucksliste genau gleich zu bezeichnen. Sie stellt lediglich einen Hinweis dar. Angenommen, Sie verfügen über eine Begriffsliste, die angibt, dass „Patti“ und „Selma“ Namen sind, aber LUIS weiterhin Kontextinformationen verwenden kann, um zu erkennen, dass sie etwas anderes in „Reservierung für 2 Personen in Patti‘s Diner“ oder „zeige mir die Route nach Selma, Georgia“ bedeuten. 

Das Hinzufügen einer Ausdrucksliste stellt eine Alternative zum Hinzufügen zusätzlicher Beispieläußerungen zu einer Absicht dar. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Einsatzmöglichkeiten für Ausdruckslisten oder Listenentitäten
Während sowohl eine Ausdrucksliste als auch Listenentitäten Auswirkungen auf Äußerungen für alle Absichten haben können, unterscheiden sich diese Auswirkungen. Sie verwenden eine Ausdrucksliste, um die Vorhersage von Absichten zu beeinflussen. Sie verwenden eine Listenentität, um die Extraktion von Entitäten für eine genaue Textübereinstimmung zu beeinflussen. 

### <a name="use-a-phrase-list"></a>Wann Sie Ausdruckslisten verwenden
Mit einer Ausdrucksliste kann LUIS weiterhin den Kontext berücksichtigen und eine Generalisierung zum Identifizieren von ähnlichen Elementen ausführen, aber keine genaue Übereinstimmung wie bei Elementen in einer Liste. Wenn Sie Ihre LUIS-App in der Lage sein soll, zu generalisieren und neue Elemente in einer Kategorie zu identifizieren, verwenden Sie eine Ausdrucksliste. 

Wenn Sie neue Instanzen einer Entität erkennen möchten, z. B. einen Besprechungsplan, bei dem auch die Namen neuer Kontakte ermittelt werden, oder eine Inventur-App, bei der neue Produkte ermittelt werden sollen, verwenden Sie einen anderen Entitätstyp für das maschinelle Lernen, z. B. eine einfache Entität. Erstellen Sie anschließend eine Ausdrucksliste der Wörter und Ausdrücke, die LUIS beim Identifizieren von Wörtern hilft, die der Entität ähneln. Diese Liste unterstützt LUIS beim Erkennen von Beispielen für die Entität durch das Hinzufügen einer zusätzlichen Signifikanz zum Wert dieser Wörter. 

Ausdruckslisten ähneln einem domänenspezifischen Vokabular, das zur Verbesserung des Verständnisses von Absichten und Entitäten beiträgt. Allgemein empfiehlt sich die Verwendung einer Ausdrucksliste mit Substantiven wie Ortsnamen. Der Name einer Stadt kann mehrere Wörter sowie Bindestriche oder Apostrophe enthalten.
 
### <a name="dont-use-a-phrase-list"></a>Wann Sie keine Ausdruckslisten verwenden 
Eine Listenentität definiert explizit jeden Wert, den eine Entität annehmen kann. Es werden außerdem ausschließlich exakte übereinstimmende Werte identifiziert. Eine Listenentität kann sich für eine App eignen, in der alle Instanzen einer Entität bekannt sind und sich nicht häufig ändern. Dies gilt z.B. für die Gerichte auf der Speisekarte eines Restaurants, die sich selten ändern. Wenn Sie eine genaue Textübereinstimmung einer Entität benötigen, verwenden Sie keine Ausdrucksliste. 

## <a name="best-practices"></a>Bewährte Methoden
Informationen zu [Best Practices](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Hinzufügen von Features zu LUIS-Apps finden Sie unter [Hinzufügen von Features](luis-how-to-add-features.md).
