---
title: Extrahieren von Daten – LUIS
description: Extrahieren Sie Daten aus dem Äußerungstext mit Absichten und Entitäten. Erfahren Sie, welche Art von Daten von LUIS (Language Understanding Intelligent Service) extrahiert werden können.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025992"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahieren von Daten aus dem Äußerungstext mit Absichten und Entitäten
LUIS bietet Ihnen die Möglichkeit, Informationen aus Benutzeräußerungen in natürlicher Sprache zu erfassen. Die Informationen werden so extrahiert, dass sie von einem Programm, einer Anwendung oder einem Chatbot verwendet werden können. In den folgenden Abschnitten erfahren Sie anhand von JSON-Beispielen, welche Daten von Absichten und Entitäten zurückgegeben werden.

Am schwierigsten sind Machine-Learning-Daten zu extrahieren, da es sich nicht um genaue Textübereinstimmungen handelt. Das Extrahieren von Daten aus [Machine-Learning-Entitäten](luis-concept-entity-types.md) muss im Rahmen des [Erstellungszyklus](luis-concept-app-iteration.md) erfolgen, bis Sie sicher sind, dass Sie die erwarteten Daten erhalten.

## <a name="data-location-and-key-usage"></a>Speicherort der Daten und Schlüsselverwendung
Von LUIS werden Daten aus der Äußerung des Benutzers am veröffentlichten [Endpunkt](luis-glossary.md#endpoint) extrahiert. Die **HTTPS-Anforderung** (POST oder GET) enthält die Äußerung sowie einige optionale Konfigurationen wie z.B. Staging- oder Produktionsumgebung.

**V2 – Anforderung für Vorhersageendpunkt**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 – Anforderung für Vorhersageendpunkt**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Die App-ID (`appID`) finden Sie auf der Seite **Einstellungen** Ihrer LUIS-App sowie in der URL (nach `/apps/`), wenn Sie diese LUIS-App bearbeiten. Der `subscription-key` ist der Endpunktschlüssel, der für Abfragen an Ihre App verwendet wird. Sie können zwar Ihren kostenlosen Erstellungs-/Startschlüssel verwenden, während Sie sich mit LUIS vertraut machen, es ist aber wichtig, dass Sie den Endpunktschlüssel in einen Schlüssel ändern, der die [erwartete LUIS-Nutzung](luis-limits.md#key-limits) unterstützt. Die Einheit für das `timezoneOffset` ist Minuten.

Die **HTTPS-Antwort** enthält alle Informationen zur Absicht und den Entitäten, die LUIS anhand des aktuell veröffentlichten Modells auf dem Staging- oder Produktionsendpunkt ermitteln konnte. Die Endpunkt-URL befindet sich auf der [LUIS](luis-reference-regions.md)-Website auf der Seite **Schlüssel und Endpunkte** im Abschnitt **Verwalten**.

## <a name="data-from-intents"></a>Daten aus Absichten
Die wichtigste Angabe ist der **Name der Absicht** mit der höchsten Bewertung. Die Endpunktantwort lautet:

#### <a name="v2-prediction-endpoint-response"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).

* * *

|Datenobjekt|Datentyp|Speicherort der Daten|Wert|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

Wenn Ihr Chatbot oder die App, die den Aufruf an LUIS durchgeführt hat, eine Entscheidung basierend auf mehreren Absichtsbewertungen trifft, geben Sie die Bewertungen aller Absichten zurück.


#### <a name="v2-prediction-endpoint-response"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

Legen Sie den QueryString-Parameter `verbose=true` fest. Die Endpunktantwort lautet:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)

Legen Sie den QueryString-Parameter `show-all-intents=true` fest. Die Endpunktantwort lautet:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).

* * *

Die Absichten werden von der höchsten zur niedrigsten Bewertung sortiert.

|Datenobjekt|Datentyp|Speicherort der Daten|Wert|Ergebnis|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

Wenn Sie vordefinierte Domänen hinzufügen, gibt der Name der Absicht die Domäne, wie z.B. `Utilties` oder `Communication`, sowie die Absicht an:

#### <a name="v2-prediction-endpoint-response"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).

* * *

|Domain|Datenobjekt|Datentyp|Speicherort der Daten|Wert|
|--|--|--|--|--|
|Versorgungsunternehmen|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Kommunikation|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Daten von Entitäten
Die meisten Chatbots und Anwendungen benötigen mehr als den Namen der Absicht. Diese zusätzlichen, optionalen Daten stammen von Entitäten, die in der Äußerung erkannt wurden. Jeder Entitätstyp gibt andere Informationen zur Übereinstimmung zurück.

Ein einzelnes Wort oder ein Ausdruck in einer Äußerung kann mehreren Entitäten entsprechen. In diesem Fall wird jede übereinstimmende Entität mit ihrer Bewertung zurückgegeben.

Alle Entitäten werden im Array **entities** der Endpunktantwort zurückgegeben.

## <a name="tokenized-entity-returned"></a>Zurückgegebene tokenisierte Entität

Überprüfen Sie die [Tokenunterstützung](luis-language-support.md#tokenization) in LUIS.


## <a name="prebuilt-entity-data"></a>Daten vordefinierter Entität
[Vordefinierte](luis-concept-entity-types.md) Entitäten werden basierend auf einer Übereinstimmung mit einem regulären Ausdruck mithilfe des Open-Source-Projekts [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) ermittelt. Die vordefinierten Entitäten werden im Array „entities“ zurückgegeben. Dabei wird dem Typnamen das Präfix `builtin::` vorangestellt.

## <a name="list-entity-data"></a>Daten von Listenentitäten

[Listenentitäten](reference-entity-list.md) stellen eine feste, geschlossene Gruppe verwandter Wörter zusammen mit ihren Synonymen dar. LUIS ermittelt keine zusätzlichen Werte für Listenentitäten. Suchen Sie mithilfe des Features **Empfehlen** nach Vorschlägen für neue Wörter basierend auf der aktuellen Liste. Wenn mehr als eine Listenentität mit demselben Wert vorhanden ist, wird in der Endpunktabfrage jede Entität zurückgegeben.

## <a name="regular-expression-entity-data"></a>Daten von Entitäten aus regulären Ausdrücken

Bei einer [Entität vom Typ „Regulärer Ausdruck“](reference-entity-regular-expression.md) wird eine Entität anhand eines von Ihnen angegebenen regulären Ausdrucks extrahiert.

## <a name="extracting-names"></a>Extrahieren von Namen
Das Abrufen von Namen aus einer Äußerung ist schwierig, da es sich bei einem Namen um nahezu jede Kombination aus Buchstaben und Wörtern handeln kann. Je nach Art des Namens, den Sie extrahieren möchten, haben Sie verschiedene Optionen. Die folgenden Vorschläge sind keine Regeln, sondern eher Richtlinien.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Hinzufügen der vordefinierten Entitäten „PersonName“ und „GeographyV2“

Die Entitäten [PersonName](luis-reference-prebuilt-person.md) und [GeographyV2](luis-reference-prebuilt-geographyV2.md) sind in einigen [Sprachkulturen](luis-reference-prebuilt-entities.md) verfügbar.

### <a name="names-of-people"></a>Namen von Personen

Für Namen von Personen gelten je nach Sprache und Kultur nur wenige Formatvorgaben. Verwenden Sie entweder eine vordefinierte **[personName](luis-reference-prebuilt-person.md)** -Entität oder eine **[einfache Entität](luis-concept-entity-types.md)** mit Rollen für Vor- und Nachname.

Wenn Sie die einfache Entität verwenden, geben Sie unbedingt Beispiele an, bei denen die Vor- und Nachnamen an unterschiedlichen Positionen in der Äußerung, in Äußerungen von verschiedener Länge und in Äußerungen aller Absichten (einschließlich der Absicht „None“) verwendet werden. [Überprüfen](./luis-how-to-review-endpoint-utterances.md) Sie die Endpunktäußerungen regelmäßig, um alle Namen zu bezeichnen, die nicht richtig vorhergesagt wurden.

### <a name="names-of-places"></a>Namen von Orten

Namen von Orten sind feststehend und bekannt. Sie umfassen z. B. Städte, Landkreise, Bundesländer, Provinzen und Länder/Regionen. Verwenden Sie die vordefinierte Entität **[geographieV2](luis-reference-prebuilt-geographyv2.md)** , um Standortinformationen zu extrahieren.

### <a name="new-and-emerging-names"></a>Neue und sich entwickelnde Namen

Einige Apps müssen in der Lage sein, neue oder sich entwickelnde Namen, z.B. von Produkten oder Unternehmen, finden zu können. Solche Namen sind am schwersten zu extrahieren. Beginnen Sie mit einer **[einfachen Entität](luis-concept-entity-types.md#simple-entity)** , und fügen Sie eine [Ausdrucksliste](luis-concept-feature.md) hinzu. [Überprüfen](./luis-how-to-review-endpoint-utterances.md) Sie die Endpunktäußerungen regelmäßig, um alle Namen zu bezeichnen, die nicht richtig vorhergesagt wurden.

## <a name="patternany-entity-data"></a>Daten in Entitäten vom Typ „Pattern.any“

[Pattern.any](reference-entity-pattern-any.md) ist ein Platzhalter variabler Länge, der nur in der Vorlagenäußerung eines Musters verwendet wird, um zu kennzeichnen, wo die Entität beginnt und endet. Die im Muster verwendete Entität muss gefunden werden, damit das Muster angewendet wird.

## <a name="sentiment-analysis"></a>Stimmungsanalyse
Wenn beim [Veröffentlichen](luis-how-to-publish-app.md#sentiment-analysis) die Standpunktanalyse konfiguriert wird, enthält die JSON-Antwort von LUIS eine Standpunktanalyse. Weitere Informationen zu Standpunktanalysen finden Sie in der Dokumentation zur [Textanalyse](../text-analytics/index.yml).

## <a name="key-phrase-extraction-entity-data"></a>Daten von Entitäten zur Schlüsselbegriffserkennung
Von der [Entität zur Schlüsselbegriffserkennung](luis-reference-prebuilt-keyphrase.md) werden Schlüsselausdrücke in der Äußerung zurückgegeben, die von der [Textanalyse](../text-analytics/index.yml) bereitgestellt werden.

## <a name="data-matching-multiple-entities"></a>Datenabgleich bei mehreren Entitäten

LUIS gibt alle Entitäten zurück, die in der Äußerung ermittelt wurden. Folglich muss Ihr Chatbot unter Umständen eine Entscheidung basierend auf den Ergebnissen treffen.

## <a name="data-matching-multiple-list-entities"></a>Daten, die mehreren Listenentitäten entsprechen

Wenn ein Wort oder ein Ausdruck mit mehreren Listenentitäten übereinstimmt, wird bei der Endpunktabfrage jede Listenentität zurückgegeben.

Wenn die App bei der Abfrage `when is the best time to go to red rock?` das Wort `red` in mehreren Listen enthält, erkennt LUIS alle Entitäten und gibt als Teil der JSON-Endpunktantwort ein Array von Entitäten zurück.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Hinzufügen von Entitäten zu LUIS-Apps finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md).
