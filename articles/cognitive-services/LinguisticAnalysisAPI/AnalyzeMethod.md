---
title: Analyze-Methode – API für linguistische Analyse
titlesuffix: Azure Cognitive Services
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der analyze-Methode in der API für linguistische Analyse bestimmte Eingaben in natürlicher Sprache analysieren.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401184"
---
# <a name="analyze-method"></a>analyze-Methode

> [!IMPORTANT]
> Die Vorschauversion für die linguistische Analyse wurde am 9. August 2018 außer Betrieb genommen. Es wird empfohlen, [Azure Machine Learning-Textanalysemodule](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) für die Textverarbeitung und -analyse zu verwenden.

Mit der REST-API **analyze** kann eine bestimmte Eingabe in natürlicher Sprache analysiert werden.
Dies kann beispielsweise lediglich die Suche nach [Sätzen und Token](Sentences-and-Tokens.md) innerhalb dieser Eingabe, die Suche nach [Wortarttags](POS-tagging.md) oder die Suche nach der [Konstituentenstruktur](Constituency-Parsing.md) beinhalten.
Durch Auswahl der relevanten Analysetools können Sie die gewünschten Ergebnisse angeben.
Um alle verfügbaren Analysen aufzulisten, sehen Sie sich die **[Analysetools](AnalyzersMethod.md)** an.

Beachten Sie, dass Sie die Sprache der Eingabezeichenfolge angeben müssen.

**REST-Endpunkt**:
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Anforderungsparameter

NAME | Type | Erforderlich | BESCHREIBUNG
-----|-------|----------|------------
**language**    | Zeichenfolge | Ja | Die zwei Buchstaben des ISO-Sprachcodes, der für die Analyse verwendet werden soll. Beispiel: „en“ für Englisch.
**analyzerIds** | Liste von Zeichenfolgen | Ja | Liste von GUIDs der Analysetools, die angewendet werden sollen. Weitere Informationen finden Sie in der Dokumentation zu Analysetools.
**text**        | Zeichenfolge | Ja | Die zu analysierende Rohdateneingabe. Hierbei kann es sich um eine kurze Zeichenfolge handeln, z.B. ein Wort oder einen Ausdruck, einen vollständigen Satz oder einen ganzen Absatz bzw. eine ganze Abhandlung.

## <a name="response-json"></a>Antwort (JSON)

Ein Array von Analyseausgaben, eines für jedes in der Anforderung angegebene Attribut.

Die Ergebnisse sehen wie folgt aus:

NAME | Type | BESCHREIBUNG
-----|------|--------------
analyzerId | Zeichenfolge | GUID des angegebenen Analysetools
result | object | Ergebnis des Analysetools

Beachten Sie, dass der Typ des Ergebnisses vom Typ des Eingabeanalysetools abhängt.

### <a name="tokens-response-json"></a>Tokenantwort (JSON)

NAME | Type | BESCHREIBUNG
-----|------|-------------
result | Liste von Satzobjekten | Innerhalb des Texts identifizierte Satzbegrenzungen |
result[x].Offset | int | Startzeichenoffset eines jeden Satzes |
result[x].Len | int | Länge in Zeichen jedes Satzes |
result[x].Tokens | Liste von Tokenobjekten | Innerhalb des Satzes identifizierte Tokenbegrenzungen |
result[x].Tokens[y].Offset | int | Startzeichenoffset des Tokens |
result[x].Tokens[y].Len | int | Länge in Zeichen der Token |
result[x].Tokens[y].RawToken | Zeichenfolge | Die Zeichen innerhalb dieses Tokens vor der Normalisierung |
result[x].Tokens[y].NormalizedToken | Zeichenfolge | Eine normalisierte Form des Zeichens, die in einer [Analysestruktur](Constituency-Parsing.md) sicher verwendet werden kann, z.B., wird eine öffnende runde Klammer „(“ zu „-LRB-“. |

Beispieleingabe: „Dies ist ein Test. Hallo!“
JSON-Beispielantwort:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Antwort der Wortarttags (JSON)

Das Ergebnis ist eine Liste von Listen mit Zeichenfolgen.
Für jeden Satz gibt es eine Liste von Wortarttags, einen Wortarttag für jedes Token.
Um das Token zu finden, das dem jeweiligen Wortarttag entspricht, sollten Sie außerdem ein Tokenisierungsobjekt anfordern.

### <a name="constituency-tree-response-json"></a>Antwort der Konstituentenstruktur (JSON)

Das Ergebnis ist eine Liste von Zeichenfolgen, eine Analysestruktur für jeden in der Eingabe gefundenen Satz.
Die Analysestrukturen werden in eingeklammerter Form dargestellt.

## <a name="example"></a>Beispiel

`POST /analyze`

Anforderungstext: JSON-Nutzlast
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Antwort: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
