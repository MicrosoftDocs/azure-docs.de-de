---
title: Absichten und Entitäten – LUIS
titleSuffix: Azure Cognitive Services
description: Eine einzelne Absicht stellt eine Aufgabe oder Aktion dar, die der Benutzer ausführen möchte. Sie ist ein Zweck oder Ziel, das in einer Äußerung des Benutzers ausgedrückt wurde. Definieren Sie einen Satz von Absichten, die Aktionen entsprechen, die Benutzer in Ihrer Anwendung ausführen möchten.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025907"
---
# <a name="intents-in-your-luis-app"></a>Absichten in Ihrer LUIS-App

Eine Absicht stellt eine Aufgabe oder Aktion dar, die der Benutzer ausführen möchte. Sie ist ein Zweck oder Ziel, das in einer [Äußerung](luis-concept-utterance.md) des Benutzers ausgedrückt wurde.

Definieren Sie einen Satz von Absichten, die Aktionen entsprechen, die Benutzer in Ihrer Anwendung ausführen möchten. Eine Reise-App definiert z.B. mehrere Absichten:

Absichten einer Reise-App   |   Beispiele für Äußerungen   |
------|------|
 FlugBuchen     |   „Buche mir einen Flug nach Rio in der nächsten Woche“ <br/> „Fliege mich am 24. nach Rio“ <br/> „Ich benötige ein Flugticket am nächsten Sonntag nach Rio De Janeiro“    |
 Grußformel     |   „Hallo“ <br/>„Guten Tag“ <br/>„Guten Morgen“  |
 CheckWeather | „Wie ist das Wetter in Boston?“ <br/> „Zeige mir die Vorhersage für das Wochenende“ |
 Keine         | „Zeige mit ein Keksrezept“<br>„Haben die Lakers gewonnen?“ |

Alle Anwendungen enthalten die vordefinierte Absicht „[None](#none-intent)“, die als Fallbackabsicht fungiert.

## <a name="prebuilt-domains-provide-intents"></a>Vordefinierte Domänen stellen Absichten bereit
Zusätzlich zu den von Ihnen definierten Absichten können Sie die von einer [vordefinierten Domäne](./howto-add-prebuilt-models.md) vordefinierten Absichten verwenden.

## <a name="return-all-intents-scores"></a>Zurückgeben von Bewertungen für alle Absichten
Sie weisen eine Äußerung einer einzigen Absicht zu. Wenn LUIS eine Äußerung am Endpunkt empfängt, gibt es standardmäßig die am besten bewertete Absicht für die Äußerung zurück.

Wenn Sie die Scores für alle Absichten der Äußerung benötigen, können Sie in der Abfragezeichenfolge der Vorhersage-API ein Flag angeben.

|Version der Vorhersage-API|Flag|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Absichten im Vergleich zu Entitäten
Die Absicht stellt die Aktion dar, die die Anwendung für den Benutzer ausführen soll. Sie basiert auf der gesamten Äußerung. Ein Äußerung kann nur eine Absicht mit der höchsten Bewertung, aber viele Entitäten enthalten.

<a name="how-do-intents-relate-to-entities"></a>

Erstellen Sie eine Absicht, wenn die _Benutzerabsicht_ eine Aktion in der Clientanwendung auslösen würde. Dies kann z. B. ein Aufruf der checkweather()-Funktion sein. Erstellen Sie dann Entitäten zur Darstellung der Parameter, die zum Ausführen der Aktion erforderlich sind.

|Intent   | Entität | Beispieläußerung   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | What's the weather like in `Seattle` `tomorrow`? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Show me the forecast for `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Absichten vordefinierter Domänen

[Vordefinierte Domänen](./howto-add-prebuilt-models.md) stellen Absichten mit Äußerungen bereit.

## <a name="none-intent"></a>Absicht „None“

Die Absicht **None** wird erstellt, aber absichtlich leer gelassen. Die Absicht **None** ist eine erforderliche Absicht, die weder gelöscht noch umbenannt werden kann. Füllen Sie sie mit Äußerungen, die außerhalb Ihres Themenbereichs liegen.

Die Absicht **None** ist die Fallbackabsicht, die in jeder App wichtig ist und 10 % der gesamten Äußerungen umfassen sollte. Sie dient dazu, LUIS Äußerungen beizubringen, die in der App-Domäne (dem Themenbereich) nicht wichtig sind. Wenn Sie der Absicht **None** keine Äußerungen hinzufügen, zwingt LUIS eine Äußerung von außerhalb der Domäne in eine Absicht der Domäne. Dadurch werden die Vorhersagebewertungen verzerrt, da LUIS die falsche Absicht für die Äußerung erlernt.

Wenn eine Äußerung als Absicht „None“ vorhergesagt wird, kann die Clientanwendung weitere Fragen stellen oder ein Menü bereitstellen, über das der Benutzer gültige Auswahlmöglichkeiten angeben kann.

## <a name="negative-intentions"></a>Negative Absichten
Wenn Sie negative und positive Absichten ermitteln möchten, z.B. „ich **möchte** ein Auto“ und „ich möchte **kein** Auto“, können Sie zwei Absichten erstellen (eine positive und eine negative) und jeder entsprechende Äußerungen hinzufügen. Sie können aber auch eine einzige Absicht erstellen und die beiden positiven und negativen Begriffe als Entität kennzeichnen.

## <a name="intents-and-patterns"></a>Absichten und Muster

Wenn Sie über Beispieläußerungen verfügen, die ganz oder teilweise als regulärer Ausdruck definiert werden können, sollten Sie die [Entität des regulären Ausdrucks](luis-concept-entity-types.md#regular-expression-entity) zusammen mit einem [Muster](luis-concept-patterns.md) verwenden.

Die Verwendung einer Entität eines regulären Ausdrucks garantiert die Datenextraktion, sodass das Muster übereinstimmt. Der Musterabgleich stellt sicher, dass eine genaue Absicht zurückgegeben wird.

## <a name="intent-balance"></a>Balance zwischen Absichten
Die Äußerungen der Absichten einer App-Domäne sollten gleichmäßig auf die Absichten verteilt sein. Sie sollten nicht einer Absicht 10 Äußerungen und einer anderen 500 Äußerungen zuordnen. Dies ist nicht ausgeglichen. In dieser Situation überprüfen Sie die Absicht mit den 500 Äußerungen, um zu ermitteln, ob ein Großteil der Absichten in einem [Muster](luis-concept-patterns.md) neu organisiert werden können.

Die Absicht **None** ist in dieser Balance nicht enthalten. Diese Absicht sollte etwa 10 % der gesamten Äußerungen in der App enthalten.

## <a name="intent-limits"></a>Grenzwerte für Absichten
Informieren Sie sich über die [Grenzwerte](luis-limits.md#model-boundaries), um zu erfahren, wie viele Absichten Sie einem Modell hinzufügen können.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Wenn mehr als die maximale Anzahl von Absichten erforderlich ist
Überprüfen Sie zunächst, ob Ihr System zu viele Absichten verwendet.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Können mehrere Absichten in einer Absicht mit Entitäten zusammengefasst werden?
Absichten, die sich sehr ähneln, erschweren LUIS die Unterscheidung. Absichten müssen variabel genug sein, um die Hauptaufgaben zu erfassen, die der Benutzer erfragt, sie müssen aber nicht jeden Pfad Ihres Code abdecken. Beispielsweise sind BookFlight und FlightCustomerService möglicherweise separate Absichten in einer Reise-App, aber BookInternationalFlight und BookDomesticFlight sind sich zu ähnlich. Wenn Ihr System sie voneinander unterscheiden muss, verwenden Sie Entitäten oder andere Programmlogik anstelle von Absichten.

### <a name="dispatcher-model"></a>Dispatchmodell
Erfahren Sie mehr über das Kombinieren von LUIS- und QnA Maker-Apps mit dem [Dispatchmodell](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Anfordern von Hilfe für Apps mit einer erheblichen Anzahl von Absichten
Wenn die Verringerung der Anzahl von Absichten oder das Aufteilen eines Teils Ihrer Absichten auf mehrere Apps für Sie nicht funktioniert, wenden Sie sich an den Support. Wenn Ihr Azure-Abonnement Supportdienste umfasst, wenden Sie sich an den [technischen Support von Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Entitäten](luis-concept-entity-types.md), die wichtige Wörter mit Relevanz für eine Absichten sind
* Erfahren Sie, wie Sie in Ihrer LUIS-App [Absichten hinzufügen und verwalten](luis-how-to-add-intents.md).
* Informieren Sie sich über [bewährte Methoden](luis-concept-best-practices.md) im Zusammenhang mit Absichten.