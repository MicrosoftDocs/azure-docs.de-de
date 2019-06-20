---
title: Extrahieren von Daten
titleSuffix: Language Understanding - Azure Cognitive Services
description: Extrahieren Sie Daten aus dem Äußerungstext mit Absichten und Entitäten. Erfahren Sie, welche Art von Daten von LUIS (Language Understanding Intelligent Service) extrahiert werden können.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 15d6b0d28f926bdb39b35b763b89422cddcccc84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150694"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahieren von Daten aus dem Äußerungstext mit Absichten und Entitäten
LUIS bietet Ihnen die Möglichkeit, Informationen aus Benutzeräußerungen in natürlicher Sprache zu erfassen. Die Informationen werden so extrahiert, dass sie von einem Programm, einer Anwendung oder einem Chatbot verwendet werden können. In den folgenden Abschnitten erfahren Sie anhand von JSON-Beispielen, welche Daten von Absichten und Entitäten zurückgegeben werden.

Am schwierigsten sind Daten zu extrahieren, die maschinell gelernt wurden, da es sich nicht um genaue Textübereinstimmungen handelt. Das Extrahieren von Daten aus maschinell erlernten [Entitäten](luis-concept-entity-types.md) muss im Rahmen des [Erstellungszyklus](luis-concept-app-iteration.md) erfolgen, bis Sie sicher sind, dass Sie die erwarteten Daten erhalten.

## <a name="data-location-and-key-usage"></a>Speicherort der Daten und Schlüsselverwendung
LUIS stellt die Daten vom veröffentlichten [Endpunkt](luis-glossary.md#endpoint) bereit. Die **HTTPS-Anforderung** (POST oder GET) enthält die Äußerung sowie einige optionale Konfigurationen wie z.B. Staging- oder Produktionsumgebung.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

Die App-ID (`appID`) finden Sie auf der Seite **Einstellungen** Ihrer LUIS-App sowie in der URL (nach `/apps/`), wenn Sie diese LUIS-App bearbeiten. Der `subscription-key` ist der Endpunktschlüssel, der für Abfragen an Ihre App verwendet wird. Sie können zwar Ihren kostenlosen Erstellungs-/Startschlüssel verwenden, während Sie sich mit LUIS vertraut machen, es ist aber wichtig, dass Sie den Endpunktschlüssel in einen Schlüssel ändern, der die [erwartete LUIS-Nutzung](luis-boundaries.md#key-limits) unterstützt. Die Einheit für das `timezoneOffset` ist Minuten.

Die **HTTPS-Antwort** enthält alle Informationen zur Absicht und den Entitäten, die LUIS anhand des aktuell veröffentlichten Modells auf dem Staging- oder Produktionsendpunkt ermitteln konnte. Die Endpunkt-URL befindet sich auf der [LUIS](luis-reference-regions.md)-Website auf der Seite **Schlüssel und Endpunkte** im Abschnitt **Verwalten**.

## <a name="data-from-intents"></a>Daten aus Absichten
Die wichtigste Angabe ist der **Name der Absicht** mit der höchsten Bewertung. Beim [Schnellstart](luis-quickstart-intents-only.md) zu `MyStore` lautet die Endpunktantwort:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Datenobjekt|Datentyp|Speicherort der Daten|Wert|
|--|--|--|--|
|Absicht|string|topScoringIntent.intent|"GetStoreInfo"|

Wenn Ihr Chatbot oder die App, die den Aufruf an LUIS durchgeführt hat, eine Entscheidung basierend auf mehreren Absichtsbewertungen trifft, geben Sie die Bewertungen aller Absichten zurück, indem Sie den QueryString-Parameter `verbose=true` festlegen. Die Endpunktantwort lautet:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Die Absichten werden von der höchsten zur niedrigsten Bewertung sortiert.

|Datenobjekt|Datentyp|Speicherort der Daten|Wert|Punkte|
|--|--|--|--|:--|
|Absicht|string|intents[0].intent|"GetStoreInfo"|0.984749258|
|Absicht|string|intents[1].intent|"None"|0.0168218873|

Wenn Sie vordefinierte Domänen hinzufügen, gibt der Name der Absicht die Domäne, wie z.B. `Utilties` oder `Communication`, sowie die Absicht an:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domäne|Datenobjekt|Datentyp|Speicherort der Daten|Wert|
|--|--|--|--|--|
|Versorgungsunternehmen|Absicht|string|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Kommunikation|Absicht|string|intents[1].intent|<b>Communication</b>.StartOver"|
||Absicht|string|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Daten von Entitäten
Die meisten Chatbots und Anwendungen benötigen mehr als den Namen der Absicht. Diese zusätzlichen, optionalen Daten stammen von Entitäten, die in der Äußerung erkannt wurden. Jeder Entitätstyp gibt andere Informationen zur Übereinstimmung zurück.

Ein einzelnes Wort oder ein Ausdruck in einer Äußerung kann mehreren Entitäten entsprechen. In diesem Fall wird jede übereinstimmende Entität mit ihrer Bewertung zurückgegeben.

Alle Entitäten werden im Array **entities** der Antwort vom Endpunkt zurückgegeben:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Zurückgegebene tokenisierte Entität
Mehrere [Kulturen](luis-language-support.md#tokenization) geben das Entitätsobjekt zurück, bei dem der `entity`-Wert [tokenisiert](luis-glossary.md#token) wurde. Die von LUIS zurückgegebenen Werte für startIndex und endIndex im Entitätsobjekt entsprechen nicht dem neuen, tokenisierten Wert, sondern der ursprünglichen Abfrage, damit Sie die unformatierte Entität programmgesteuert extrahieren können. 

Im Deutschen wird z.B. das Wort `das Bauernbrot` in `das bauern brot` tokenisiert. Der tokenisierte Wert `das bauern brot` wird zurückgegeben, und der ursprüngliche Wert kann mithilfe von startIndex und endIndex der ursprünglichen Abfrage programmgesteuert bestimmt werden und ergibt `das Bauernbrot`.

## <a name="simple-entity-data"></a>Daten einfacher Entitäten

Eine [einfache Entität](luis-concept-entity-types.md) ist ein maschinell erlernter Wert. Dabei kann es sich um ein Wort oder einen Ausdruck handeln.

`Bob Jones wants 3 meatball pho`

In der vorherigen Äußerung wird `Bob Jones` als die einfache Entität `Customer` bezeichnet.

Die vom Endpunkt zurückgegebenen Daten enthalten den Namen der Entität, den in der Äußerung ermittelten Text, den Speicherort des erkannten Texts und die Bewertung:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Datenobjekt|Name der Entität|Wert|
|--|--|--|
|Entität vom Typ „Simple“|`Customer`|`bob jones`|

## <a name="composite-entity-data"></a>Daten zusammengesetzter Entitäten
[Zusammengesetzte](luis-concept-entity-types.md) Entitäten sind maschinell erlernt und können ein Wort oder einen Ausdruck enthalten. Betrachten Sie beispielsweise eine zusammengesetzte Entität der vordefinierten Entitäten `number` und `Location::ToLocation` bei der folgenden Äußerung:

`book 2 tickets to paris`

Beachten Sie, dass zwischen der Anzahl `2` und der ToLocation `paris` Wörter stehen, die nicht Teil der Entitäten sind. Die grüne Unterstreichung, die in einer bezeichneten Äußerung auf der [LUIS](luis-reference-regions.md)-Website verwendet wird, gibt eine zusammengesetzte Entität an.

![Entität vom Typ „Composite“](./media/luis-concept-data-extraction/composite-entity.png)

Zusammengesetzte Entitäten werden in einem Array vom Typ `compositeEntities` zurückgegeben. Dabei werden auch alle Entitäten in dieser zusammengesetzten Entität im `entities`-Array zurückgegeben:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Datenobjekt|Name der Entität|Wert|
|--|--|--|
|Vordefinierte Entität – number|"builtin.number"|"2"|
|Vordefinierte Entität – GeographyV2|"Location::ToLocation"|"paris"|

## <a name="list-entity-data"></a>Daten von Listenentitäten

Eine [Listenentität](luis-concept-entity-types.md) wird nicht maschinell erlernt. Sie stellt eine genaue Textübereinstimmung dar. Eine Liste stellt die enthaltenen Elemente zusammen mit Synonymen für diese Einträge dar. LUIS kennzeichnet jede Übereinstimmung für ein Element in einer Liste in der Antwort als eine Entität. Ein Synonym kann in mehreren Listen enthalten sein.

Angenommen, die App enthält die Liste `Cities`, die Variationen von Städtenamen einschließlich Ort des Flughafens (Sea-tac), Flughafencode (SEA), Postleitzahl (98101) und Vorwahl (206) ermöglicht.

|Listenelement|Elementsynonyme|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

In der Äußerung oben wird das Wort `paris` dem Element Paris als Teil der Listenentität `Cities` zugeordnet. Die Listenentität findet Übereinstimmungen sowohl des normalisierten Elementnamens als auch der Synonyme des Elements.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Eine weitere Beispieläußerung mit einem Synonym für Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Daten vordefinierter Entität
[Vordefinierte](luis-concept-entity-types.md) Entitäten werden basierend auf einer Übereinstimmung mit einem regulären Ausdruck mithilfe des Open-Source-Projekts [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) ermittelt. Die vordefinierten Entitäten werden im Array „entities“ zurückgegeben. Dabei wird dem Typnamen das Präfix `builtin::` vorangestellt. Der folgende Text ist eine Beispieläußerung mit den zurückgegebenen vordefinierten Entitäten:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Daten von Entitäten aus regulären Ausdrücken
Entitäten aus [regulären Ausdrücken](luis-concept-entity-types.md) werden basierend auf der Übereinstimmung eines regulären Ausdrucks mit einem Ausdruck, den Sie beim Erstellen der Entität angeben, ermittelt. Wenn Sie als Definition der Entität aus einem regulären Ausdruck `kb[0-9]{6}` verwenden, stellt die folgende JSON-Antwort eine Beispieläußerung für die zurückgegebenen RegEx-Entitäten für die Abfrage `When was kb123456 published?` dar:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Extrahieren von Namen
Das Abrufen von Namen aus einer Äußerung ist schwierig, da es sich bei einem Namen um nahezu jede Kombination aus Buchstaben und Wörtern handeln kann. Je nach Art des Namens, den Sie extrahieren möchten, haben Sie verschiedene Optionen. Die folgenden Vorschläge sind keine Regeln, sondern eher Richtlinien.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Hinzufügen der vordefinierten Entitäten „PersonName“ und „GeographyV2“

Die Entitäten [PersonName](luis-reference-prebuilt-person.md) und [GeographyV2](luis-reference-prebuilt-geographyV2.md) sind in einigen [Sprachkulturen](luis-reference-prebuilt-entities.md) verfügbar. 

### <a name="names-of-people"></a>Namen von Personen

Für Namen von Personen gelten je nach Sprache und Kultur nur wenige Formatvorgaben. Verwenden Sie entweder eine vordefinierte **[personName](luis-reference-prebuilt-person.md)** -Entität oder eine **[einfache Entität](luis-concept-entity-types.md#simple-entity)** mit [Rollen](luis-concept-roles.md) für Vor- und Nachname. 

Wenn Sie die einfache Entität verwenden, geben Sie unbedingt Beispiele an, bei denen die Vor- und Nachnamen an unterschiedlichen Positionen in der Äußerung, in Äußerungen von verschiedener Länge und in Äußerungen aller Absichten (einschließlich der Absicht „None“) verwendet werden. [Überprüfen](luis-how-to-review-endoint-utt.md) Sie die Endpunktäußerungen regelmäßig, um alle Namen zu bezeichnen, die nicht richtig vorhergesagt wurden.

### <a name="names-of-places"></a>Namen von Orten

Namen von Orten sind feststehend und bekannt. Sie umfassen z. B. Städte, Landkreise, Bundesländer, Provinzen und Länder/Regionen. Verwenden Sie die vordefinierte Entität **[geographieV2](luis-reference-prebuilt-geographyv2.md)** , um Standortinformationen zu extrahieren.

### <a name="new-and-emerging-names"></a>Neue und sich entwickelnde Namen

Einige Apps müssen in der Lage sein, neue oder sich entwickelnde Namen, z.B. von Produkten oder Unternehmen, finden zu können. Solche Namen sind am schwersten zu extrahieren. Beginnen Sie mit einer **[einfachen Entität](luis-concept-entity-types.md#simple-entity)** , und fügen Sie eine [Ausdrucksliste](luis-concept-feature.md) hinzu. [Überprüfen](luis-how-to-review-endoint-utt.md) Sie die Endpunktäußerungen regelmäßig, um alle Namen zu bezeichnen, die nicht richtig vorhergesagt wurden.

## <a name="pattern-roles-data"></a>Daten von Musterrollen
Rollen sind kontextabhängige Unterschiede von Entitäten.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Daten in Entitäten vom Typ „Pattern.any“
Entitäten vom Typ „Pattern.any“ haben eine variable Länge und werden in Vorlagenäußerungen von [Mustern](luis-concept-patterns.md) verwendet.

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Stimmungsanalyse
Wenn die Standpunktanalyse konfiguriert wurde, enthält die JSON-Antwort von LUIS eine Standpunktanalyse. Weitere Informationen zu Standpunktanalysen finden Sie in der Dokumentation zur [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

### <a name="sentiment-data"></a>Stimmungsdaten
Stimmungsdaten stellen eine Bewertung zwischen 1 und 0 dar, die eine eher positive (näher an 1) oder negative (näher an 0) Stimmung in den Daten angibt.

Wenn die Kultur `en-us` ist, lautet die Antwort:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Für alle anderen Kulturen lautet die Antwort:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Daten von Entitäten zur Schlüsselbegriffserkennung
Die Entität zu Schlüsselbegriffserkennung gibt Schlüsselausdrücke in der Äußerung zurück, die von der [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) bereitgestellt werden.

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Datenabgleich bei mehreren Entitäten

LUIS gibt alle Entitäten zurück, die in der Äußerung ermittelt wurden. Daher muss Ihr Chatbot seine Entscheidung basierend auf den Ergebnissen treffen. Eine Äußerung kann viele Entitäten enthalten:

`book me 2 adult business tickets to paris tomorrow on air france`

Der LUIS-Endpunkt kann die gleichen Daten in verschiedenen Entitäten erkennen:

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Daten, die mehreren Listenentitäten entsprechen

Wenn ein Wort oder ein Ausdruck mit mehreren Listenentitäten übereinstimmt, wird bei der Endpunktabfrage jede Listenentität zurückgegeben.

Wenn die App bei der Abfrage `when is the best time to go to red rock?` das Wort `red` in mehreren Listenentitäten enthält, erkennt LUIS alle Entitäten und gibt als Teil der JSON-Endpunktantwort ein Array von Entitäten zurück: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Hinzufügen von Entitäten zu LUIS-Apps finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md).
