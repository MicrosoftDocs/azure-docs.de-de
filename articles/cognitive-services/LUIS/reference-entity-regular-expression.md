---
title: Entität vom Typ „RegEx“ (Regulärer Ausdruck) – LUIS
description: Ein regulärer Ausdruck ist am besten für unformatierten Text von Äußerungen geeignet. Die Groß-/Kleinschreibung sowie die Kultur werden ignoriert.  Reguläre Ausdrücke werden nach Änderungen durch die Rechtschreibprüfung auf Zeichenebene (nicht auf Tokenebene) angewandt.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025224"
---
# <a name="regular-expression-entity"></a>Entität vom Typ „RegEx“

Eine RegEx-Entität extrahiert eine Entität anhand eines regulären Ausdrucks, den Sie bereitstellen.

Ein regulärer Ausdruck ist am besten für unformatierten Text von Äußerungen geeignet. Die Groß-/Kleinschreibung sowie die Kultur werden ignoriert.  Reguläre Ausdrücke werden nach Änderungen durch die Rechtschreibprüfung auf Zeichenebene (nicht auf Tokenebene) angewandt. Wenn der reguläre Ausdruck zu komplex ist (z.B. mit zu vielen Klammern), können Sie den Ausdruck dem Modell nicht hinzufügen. Es wird ein Teil der [.NET Regex](/dotnet/standard/base-types/regular-expressions)-Bibliothek verwendet, aber nicht die gesamte Bibliothek.

**Diese Entität ist gut geeignet, wenn Folgendes gilt**:

* Die Daten sind einheitlich formatiert, und auch bei den Abweichungen herrscht Einheitlichkeit.
* Für den regulären Ausdruck sind nicht mehr als zwei Schachtelungsebenen erforderlich.

![Entität vom Typ „RegEx“](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Überlegungen zur Verwendung

Reguläre Ausdrücke finden möglicherweise mehr, als Sie erwarten. Ein Beispiel hierfür ist eine numerische Wortsuche, wie z. B. `one` und `two`. Ein Beispiel ist der folgende reguläre Ausdruck, der die Zahl `one` zusammen mit anderen Zahlen findet:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Dieser RegEx-Ausdruck findet auch alle Wörter, die auf diese Zahlen enden, wie z. B. `phone`. Um Probleme wie dieses zu beheben, stellen Sie sicher, dass bei Übereinstimmungen für reguläre Ausdrücke die Wortgrenzen berücksichtigt werden. Der reguläre Ausdruck mit Berücksichtigung von Wortgrenzen für dieses Beispiel wird im folgenden regulären Ausdruck verwendet:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON-Beispiel

Wenn Sie `kb[0-9]{6}` als Definition der RegEx-Entität verwenden, stellt die folgende JSON-Antwort eine Beispieläußerung für die zurückgegebenen RegEx-Entitäten für die folgende Abfrage dar:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)


Dies ist der JSON-Code, wenn `verbose=false` in der Abfragezeichenfolge festgelegt ist:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Dies ist der JSON-Code, wenn `verbose=true` in der Abfragezeichenfolge festgelegt ist:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Entitäten finden Sie hier:

* [Konzepte](luis-concept-entity-types.md)
* [Anleitung zum Erstellen](luis-how-to-add-entities.md)