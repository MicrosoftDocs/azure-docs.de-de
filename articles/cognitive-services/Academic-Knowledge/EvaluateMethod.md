---
title: Evaluate-Methode – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Geben Sie mithilfe der evaluate-Methode basierend auf einem Abfrageausdruck einen Satz von akademischen Entitäten zurück.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339085"
---
# <a name="evaluate-method"></a>evaluate-Methode

Die **evaluate**-REST-API wird verwendet, um basierend auf einem Abfrageausdruck einen Satz von akademischen Entitäten zurückgegeben.
<br>

**REST-Endpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Anforderungsparameter  

NAME     | Wert | Erforderlich?  | BESCHREIBUNG
-----------|-----------|---------|--------
**expr**       | Textzeichenfolge | Ja | Ein Abfrageausdruck, der angibt, welche Entitäten zurückgegeben werden sollen.
**model**      | Textzeichenfolge | Nein  | Der Name des Modells, das Sie abfragen möchten.  Derzeit wird als Standardwert *latest* verwendet.        
**attributes** | Textzeichenfolge | Nein<br>Standard: id | Eine durch Kommas getrennte Liste, die die Attributwerte in einer Antwort angibt. Bei den Attributnamen wird zwischen Groß- und Kleinschreibung unterschieden.
**count**        | Number | Nein<br>Standardwert: 10 | Anzahl der zurückzugebenden Ergebnisse.
**offset**     | Number |   Nein<br>Standardwert: 0    | Index des ersten zurückzugebenden Ergebnisses.
**orderby** |   Textzeichenfolge | Nein<br>Standard: by decreasing prob | Name eines Attributs, das für die Sortierung der Entitäten verwendet wird. Optional kann „aufsteigend/absteigend“ angegeben werden. Das Format lautet: *name:asc* or *name:desc*.
  
 <br>

## <a name="response-json"></a>Antwort (JSON)

NAME | BESCHREIBUNG
-------|-----   
**expr** |  Der *expr*-Parameter aus der Anforderung.
**entities** |  Ein Array mit 0 oder mehr Entitäten, die mit dem Abfrageausdruck übereinstimmten. Jede Entität enthält einen Wert Wahrscheinlichkeit natürlicher Logarithmen und die Werte der anderen angeforderten Attribute.
**aborted** | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.

<br>

#### <a name="example"></a>Beispiel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Ein Ausdruck wird in der Regel von einer Antwort auf die **interpret**-Methode abgerufen.  Sie können jedoch auch selbst Abfrageausdrücke erstellen (siehe [Syntax für Abfrageausdrücke](QueryExpressionSyntax.md)).  
  
Mit den Parametern *count* und *offset* kann eine große Anzahl von Ergebnissen erzielt werden, ohne eine einzige Anforderung zu senden, die zu einer großen (und möglicherweise langsamen) Antwort führt.  In diesem Beispiel verwendete die Anforderung den Ausdruck für die erste Interpretation aus der **interpret**-API-Antwort als Wert *expr*. Der *Count = 2*-Parameter gibt an, dass 2 Entitätsergebnisse angefordert werden. Und der Parameter *attributes=Ti,Y,CC,AA.AuN,AA.AuId* gibt an, dass für jedes Ergebnis Titel, Jahr, Zitatanzahl, Autorenname und Autoren-ID angefordert werden.  Eine Liste der Attribute finden Sie unter [Entitätsattribute](EntityAttributes.md).
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
