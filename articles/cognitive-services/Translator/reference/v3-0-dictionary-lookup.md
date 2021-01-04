---
title: 'Translator: Methode zur Wörterbuchsuche'
titleSuffix: Azure Cognitive Services
description: Die Methode zur Wörterbuchsuche stellt alternative Übersetzungen für ein Wort und eine kleine Anzahl von idiomatischen Ausdrücken bereit.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 6ada9c06f0befbea4b9aa85f95651e96dec595b7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021285"
---
# <a name="translator-30-dictionary-lookup"></a>Translator 3.0: Wörterbuchsuche

Die Suche stellt alternative Übersetzungen für ein Wort und eine kleine Anzahl von idiomatischen Ausdrücken bereit. Jede Übersetzung enthält die Wortart und eine Liste von Rückübersetzungen. Durch die Rückübersetzungen kann ein Benutzer die Übersetzung im Kontext nachvollziehen. Der Vorgang [Wörterbuchbeispiel](./v3-0-dictionary-examples.md) ermöglicht das Ausführen eines Drilldowns, um Beispiele für die Verwendung jedes Übersetzungspaars anzuzeigen.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

| Abfrageparameter  | BESCHREIBUNG |
| ------ | ----------- |
| api-version <img width=200/>   | **Erforderlicher Parameter**.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein. |
| from | **Erforderlicher Parameter**.<br/>Gibt die Sprache des Eingabetexts an. Sie müssen eine der zum `dictionary`-Bereich hinzugefügten [unterstützten Sprachen](./v3-0-languages.md) als Quellsprache auswählen. |
| zu   | **Erforderlicher Parameter**.<br/>Gibt die Sprache des Ausgabetexts an. Sie müssen eine der zum `dictionary`-Bereich hinzugefügten [unterstützten Sprachen](v3-0-languages.md) als Zielsprache auswählen. |


Anforderungsheader enthalten Folgendes:

| Header  | BESCHREIBUNG |
| ------ | ----------- |
| Authentifizierungsheader <img width=200/>  | **Erforderlicher Anforderungsheader**.<br/>Weitere Informationen finden Sie in den <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">verfügbaren Optionen für die Authentifizierung</a>. |
| Content-Type | **Erforderlicher Anforderungsheader**.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte: `application/json`. |
| Content-Length   | **Erforderlicher Anforderungsheader**.<br/>Die Länge des Anforderungstexts. |
| X-ClientTraceId   | **Optional:**<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Sie können diesen Header nur weglassen, wenn Sie die Ablaufverfolgungs-ID in die Abfragezeichenfolge über einen Abfrageparameter namens `ClientTraceId` einschließen. |

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`. Dieses repräsentiert den Begriff, nach dem gesucht werden soll.

```json
[
    {"Text":"fly"}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array darf höchstens 10 Elemente enthalten.
* Der Textwert eines Arrayelements kann 100 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `normalizedSource`: Eine Zeichenfolge, die die normalisierte Form des Ausgangsbegriffs angibt. Wenn die Anforderung beispielsweise „JOHN“ lautet, entspricht die normalisierte Form „john“. Der Inhalt dieses Felds wird zur Eingabe für das [Suchen von Beispielen](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Eine Zeichenfolge, die den Quellbegriff in der Form darstellt, die am besten für die Anzeige des Endbenutzers geeignet ist. Wenn die Eingabe beispielsweise „JOHN“ ist, entspricht die Anzeigeform der üblichen Schreibweise des Namens: „John“. 

  * `translations`: Eine Liste von Übersetzungen für den Quellbegriff. Jedes Listenelement ist ein Objekt mit den folgenden Zeichenfolgeneigenschaften:

    * `normalizedTarget`: Eine Zeichenfolge, die die normalisierte Form dieses Begriffs in der Zielsprache angibt. Dieser Wert sollte als Eingabe für das [Suchen von Beispielen](./v3-0-dictionary-examples.md) verwendet werden.

    * `displayTarget`: Eine Zeichenfolge, die den Begriff in der Zielsprache und in der Form darstellt, die am besten für die Anzeige des Endbenutzers geeignet ist. Im Allgemeinen unterscheidet sich dies nur durch die Groß- und Kleinschreibung von `normalizedTarget`. Für einen Eigennamen wie „Juan“ werden beispielsweise `normalizedTarget = "juan"` und `displayTarget = "Juan"` verwendet.

    * `posTag`: Eine Zeichenfolge, die diesen Begriff einem Tag für die Wortart zuordnet.

        | Tagname | BESCHREIBUNG  |
        |----------|--------------|
        | ADJ      | Adjektive   |
        | ADV      | Adverbien      |
        | CONJ     | Konjunktionen |
        | DET      | Determinative  |
        | MODAL    | Verben        |
        | NOUN     | Nomen        |
        | PREP     | Präpositionen |
        | PRON     | Pronomen     |
        | VERB     | Verben        |
        | OTHER    | Andere        |

        Implementierungshinweis: Diese Tags wurden durch das Markieren der Wortart für den englischen Begriff bestimmt, anschließend wird das häufigste Tag für jedes Quell-/Zielpaar verwendet. Wenn ein spanisches Wort also häufig in eine andere Wortart ins Englische übersetzt wird, können die Tags falsch sein (in Bezug auf das spanische Wort).

    * `confidence`: Ein Wert zwischen 0,0 und 1,0, der die „Zuverlässigkeit“ (bzw. die „Wahrscheinlichkeit in den Trainingsdaten“) dieses Übersetzungspaars angibt. Die Summe der Zuverlässigkeit für ein Quellwort kann 1,0 oder weniger betragen. 

    * `prefixWord`: Eine Zeichenfolge, die das Wort angibt, das als Präfix der Übersetzung angezeigt werden soll. Derzeit handelt es sich dabei um den an das grammatikalische Geschlecht des Nomens angepassten Determinativ (für Sprachen, für die dies relevant ist). Das Präfix des spanischen Worts „mosca“ ist „la“, da „mosca“ im Spanischen ein feminines Nomen ist. Dies hängt von der Übersetzung, nicht von der Quelle ab. Wenn es kein Präfix gibt, ist die Zeichenfolge leer.
    
    * `backTranslations`: Eine Liste von Rückübersetzungen des Ziels. Diese enthält beispielsweise Quellwörter, in die das Ziel übersetzt werden kann. Es wird sichergestellt, dass die Liste das angeforderte Quellwort enthält. Wenn das gesuchte Quellwort beispielsweise „fly“ ist, befindet sich „fly“ garantiert in der Liste `backTranslations`. Es kann jedoch nicht sichergestellt werden, dass es sich an erster Stelle befindet. Häufig ist dies nicht der Fall. Jedes Element der `backTranslations`-Liste ist ein Objekt, das von folgenden Zeichenfolgeneigenschaften beschrieben wird:

        * `normalizedText`: Eine Zeichenfolge, die die normalisierte Form des Quellbegriffs angibt, bei dem es sich um eine Rückübersetzung des Ziels handelt. Dieser Wert sollte als Eingabe für das [Suchen von Beispielen](./v3-0-dictionary-examples.md) verwendet werden.        

        * `displayText`: Eine Zeichenfolge, die den Quellbegriff, bei dem es sich um eine Rückübersetzung des Ziels handelt, in der Form angibt, die am besten für die Anzeige des Endbenutzers geeignet ist.

        * `numExamples`: Ein Integer, der die Anzahl der für dieses Übersetzungspaar verfügbaren Beispiele angibt. Tatsächliche Beispiele müssen mit einem separaten Aufruf für das [Suchen von Beispielen](./v3-0-dictionary-examples.md) abgerufen werden. Die Anzahl sollte in den meisten Fällen auf einer UX angezeigt werden. Auf der Benutzeroberfläche kann beispielsweise ein Link zur Rückübersetzung hinzugefügt werden, wenn die Anzahl der Beispiele größer als 0 (null) ist, und die Rückübersetzung kann als Nur-Text angezeigt werden, wenn es keine Beispiele gibt. Beachten Sie, dass die tatsächliche Anzahl von Beispielen, die von einem Aufruf für das [Suchen von Beispielen](./v3-0-dictionary-examples.md) zurückgegeben werden, kleiner als `numExamples` sein kann, da zusätzliche Filter angewendet werden können, um „schlechte“ Beispiele zu entfernen.
        
        * `frequencyCount`: Ein Integer, der die Häufigkeit dieses Übersetzungspaars in den Daten anzeigt. Der Hauptzweck dieses Felds ist das Verbessern der Benutzeroberfläche, indem Rückübersetzungen so sortiert werden, dass die häufigsten Begriffe oben stehen.

    > [!NOTE]
    > Wenn der gesuchte Begriff nicht im Wörterbuch vorhanden ist, lautet die Antwort zwar „200 (OK)“, aber die `translations`-Liste ist leer.

## <a name="examples"></a>Beispiele

In diesem Beispiel wird veranschaulicht, wie nach alternativen Übersetzungen für den englischen Begriff `fly` im Spanischen gesucht werden kann.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Dann wird folgende Antwort zurückgegeben (zur besseren Veranschaulichung gekürzt):

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

In diesem Beispiel wird dargestellt, was geschieht, wenn der gesuchte Begriff im gültigen Wörterbuchpaar nicht vorhanden ist.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Da der Begriff nicht im Wörterbuch gefunden wurde, enthält der Antworttext eine leere `translations`-Liste.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```