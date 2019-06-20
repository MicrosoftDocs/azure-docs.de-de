---
title: Sprachunterstützung
titleSuffix: Azure Cognitive Services
description: LUIS bietet eine Reihe von Features innerhalb des Diensts. Nicht alle Funktionen besitzen die gleiche Sprachparität. Stellen Sie sicher, dass die Features, an denen Sie interessiert sind, in Ihrer gewünschten Sprache und Kultur unterstützt werden. Eine LUIS-App ist kulturspezifisch, und die Kultur kann nach dem Festlegen nicht mehr geändert werden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 8f067bc005c4de9ddc87ed598b1717f8fbb29a6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072376"
---
# <a name="language-and-region-support-for-luis"></a>Sprach- und Regionsunterstützung für LUIS

LUIS bietet eine Reihe von Features innerhalb des Diensts. Nicht alle Funktionen besitzen die gleiche Sprachparität. Stellen Sie sicher, dass die Features, an denen Sie interessiert sind, in Ihrer gewünschten Sprache und Kultur unterstützt werden. Eine LUIS-App ist kulturspezifisch, und die Kultur kann nach dem Festlegen nicht mehr geändert werden.

## <a name="multi-language-luis-apps"></a>LUIS-Apps mit mehreren Sprachen

Wenn Sie eine mehrsprachige LUIS-Clientanwendung benötigen, z.B. einen Chatbot, haben Sie einige Optionen. Wenn LUIS alle Sprachen unterstützt, entwickeln Sie eine LUIS-App für jede Sprache. Jede LUIS-App weist eine eindeutige App-ID und ein eindeutiges Endpunktprotokoll auf. Wenn Sie ein Sprachverständnis für eine Sprache bereitstellen müssen, die LUIS nicht unterstützt, können Sie mit der [Microsoft Translator-API](../Translator/translator-info-overview.md) die Äußerung in eine unterstützte Sprache übersetzen, die Äußerung an den LUIS-Endpunkt senden und die resultierenden Bewertungen empfangen.

## <a name="languages-supported"></a>Unterstützte Sprachen

LUIS versteht Äußerungen in den folgenden Sprachen:

| Sprache |Gebietsschema  |  Vordefinierte Domäne | Vordefinierte Entität | Ausdrucklistenempfehlungen | \**[Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Stimmung und<br>Schlüsselwörter)|
|--|--|:--:|:--:|:--:|:--:|
| Englisch (USA) |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinesisch](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Niederländisch |`nl-NL` |-|  -   |-|✔|
| Französisch (Frankreich) |`fr-FR` |-| ✔ |✔ |✔|
| Französisch (Kanada) |`fr-CA` |-|   -   |-|✔|
| Deutsch |`de-DE` |-| ✔ |✔ |✔|
| Italienisch |`it-IT` |-| ✔ |✔|✔|
| *[Japanisch](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Nur Schlüsselausdruck|
| Koreanisch |`ko-KR` |-|   -   |-|Nur Schlüsselausdruck|
| Portugiesisch (Brasilien) |`pt-BR` |-| ✔ |✔ |Nicht alle Unterkulturen|
| Spanisch (Spanien) |`es-ES` |-| ✔ |✔|✔|
| Spanisch (Mexiko)|`es-MX` |-|  -   |✔|✔|
| Türkisch | `tr-TR` |-|-|-|Nur Stimmung|


Die Sprachunterstützung variiert für [vordefinierte Entitäten](luis-reference-prebuilt-entities.md) und [vordefinierte Domänen](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Hinweise zur Unterstützung von Chinesisch

 - In der Kultur `zh-cn` erwartet LUIS den vereinfachten chinesischen Zeichensatz anstelle des traditionellen.
 - Die Namen der Absichten, Entitäten, Features und regulären Ausdrücke können in chinesischen oder lateinischen Zeichen vorliegen.
 - Informationen zu den in der Kultur `zh-cn` unterstützten vordefinierten Domänen finden Sie in der [Referenz zu vordefinierten Domänen](luis-reference-prebuilt-domains.md).
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Hinweise zur Unterstützung von Japanisch

 - Da LUIS keine syntaktische Analyse bietet und den Unterschied zwischen Keigo und informellem Japanisch nicht versteht, müssen Sie die unterschiedlichen Formalitätsstufen als Trainingsbeispiele für Ihre Anwendungen integrieren.
     - でございます ist nicht dasselbe wie です.
     - です ist nicht dasselbe wie だ.

### <a name="text-analytics-support-notes"></a>** Hinweise zur Textanalyseunterstützung
Die Textanalyse beinhaltet eine vorgefertigte keyPhrase-Entität und Standpunktanalyse. Für die Subkulturen `pt-PT` und `pt-BR` wird nur Portugiesisch unterstützt. Alle anderen Kulturen werden auf primärer Kulturebene unterstützt. Erfahren Sie mehr über [unterstützte Sprachen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) für die Textanalyse.

### <a name="speech-api-supported-languages"></a>Unterstützte Sprachen der Speech-API
Informationen zu den Sprachen für den Spracherkennungsmodus von Speech finden Sie unter [Unterstützte Sprachen](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) für Speech.

### <a name="bing-spell-check-supported-languages"></a>Unterstützte Sprachen der Bing-Rechtschreibprüfung
Eine Liste und den Status der unterstützten Sprachen finden Sie unter [Liste und Status der unterstützten Sprachen](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) für die Bing-Rechtschreibprüfung.

## <a name="rare-or-foreign-words-in-an-application"></a>Seltene oder fremdsprachliche Wörter in einer Anwendung
In der Kultur `en-us` lernt LUIS, die meisten englischen Wörter zu unterscheiden, einschließlich umgangssprachlicher. In der Kultur `zh-cn` lernt LUIS, die meisten chinesische Zeichen zu unterscheiden. Wenn Sie ein seltenes Wort in `en-us` bzw. ein seltenes Zeichen in `zh-cn` verwenden und sehen, dass LUIS das betreffende Wort bzw. Zeichen offenbar nicht unterscheiden kann, können Sie das Wort oder Zeichen einem [Ausdruckslistenfeature](luis-how-to-add-features.md) hinzufügen. Beispielsweise sollten Wörter außerhalb der Kultur der Anwendung, also fremdsprachliche Wörter, einem Ausdruckslistenfeature hinzugefügt werden. Diese Ausdrucksliste sollte als nicht austauschbar gekennzeichnet werden, um anzugeben, dass die seltenen Wortformen eine Klasse bilden, deren Erkennung LUIS erlernen sollte, die aber keine Synonyme oder untereinander austauschbar sind.

### <a name="hybrid-languages"></a>Hybridsprachen
Hybridsprachen kombinieren Wörter aus zwei Kulturen, z.B. Englisch und Chinesisch. Diese Sprachen werden in LUIS nicht unterstützt, da eine App auf einer einzelnen Kultur basiert.

## <a name="tokenization"></a>Tokenisierung
Zum Ausführen von maschinellem Lernen unterteilt LUIS eine Äußerung basierend auf der Kultur in [Token](luis-glossary.md#token).

|Sprache|  Jedes Leerzeichen oder Sonderzeichen | Zeichenebene|Zusammengesetzte Wörter|[Zurückgegebene tokenisierte Entität](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinesisch||✔||✔|
|Niederländisch|||✔|✔|
|Englisch (en-us)|✔ ||||
|Französisch (fr-FR)|✔||||
|Französisch (fr-CA)|✔||||
|Deutsch|||✔|✔|
|Italienisch|✔||||
|Japanisch||||✔|
|Koreanisch||✔||✔|
|Portugiesisch (Brasilien)|✔||||
|Spanisch (es-ES)|✔||||
|Spanisch (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Benutzerdefinierte Tokenizer-Versionen

Die folgenden Kulturen weisen benutzerdefinierte Tokenizer-Versionen auf:

|Kultur|Version|Zweck|
|--|--|--|
|Deutsch<br>`de-de`|1.0.0|Wendet die Tokenisierung auf Wörter an, indem sie mithilfe eines auf maschinellem Lernen basierenden Tokenizer getrennt werden, der versucht, zusammengesetzte Wörter in ihre einzelnen Komponenten zu zerlegen.<br>Wenn ein Benutzer `Ich fahre einen krankenwagen` als Äußerung eingibt, wird sie in `Ich fahre einen kranken wagen` geändert. Ermöglicht das unabhängige Markieren von `kranken` und `wagen` als unterschiedliche Entitäten.|
|Deutsch<br>`de-de`|1.0.2|Wendet die Tokenisierung auf Wörter an, indem bei Leerzeichen eine Trennung erfolgt.<br> Wenn ein Benutzer `Ich fahre einen krankenwagen` als Äußerung eingibt, bleibt es ein einzelnes Token. Daher ist `krankenwagen` als eine einzelne Entität gekennzeichnet. |

### <a name="migrating-between-tokenizer-versions"></a>Migrieren zwischen Tokenizer-Versionen
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Die Tokenisierung erfolgt auf App-Ebene. Die Tokenisierung auf Versionsebene wird nicht unterstützt. 

[Importieren Sie die Datei als neue App](luis-how-to-start-new-app.md#import-an-app-from-file), anstelle einer Version. Diese Aktion bedeutet, dass die neue App eine andere App-ID besitzt, aber die in der Datei angegebene Tokenizer-Version verwendet. 