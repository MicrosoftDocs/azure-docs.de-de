---
title: Evaluate-Methode – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Evaluate-Methode in der Knowledge Exploration Service-API (KES) verwenden können.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814106"
---
# <a name="evaluate-method"></a>evaluate-Methode

Die *evaluate*-Methode wertet die Ausgabe eines strukturierten Abfrageausdrucks basierend auf den Indexdaten aus und gibt diese zurück.

Ein Ausdruck wird in der Regel von einer Antwort auf die interpret-Methode abgerufen.  Sie können jedoch auch selbst Abfrageausdrücke erstellen (siehe [Strukturierter Abfrageausdruck](Expressions.md)).  

## <a name="request"></a>Anforderung 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

NAME|Wert|BESCHREIBUNG
----|----|----
expr       | Textzeichenfolge | Strukturierter Abfrageausdruck, der eine Teilmenge von Indexentitäten auswählt.
attributes | Textzeichenfolge | Durch Trennzeichen getrennte Liste von Attributen, die in der Antwort enthalten sein sollen.
count      | Zahl (default=10) | Maximale Anzahl der zurückzugebenden Ergebnisse.
offset     | Zahl (default=0) | Index des ersten zurückzugebenden Ergebnisses.
orderby |   Textzeichenfolge | Name des Attributs, mit dem die Ergebnisse sortiert werden, gefolgt von einer optionalen Sortierreihenfolge (default=asc): „*attrname*[:(asc&#124;desc)]“.  Wenn kein Attribut angegeben wird, werden die Ergebnisse durch Verringern der Wahrscheinlichkeit natürlicher Logarithmen zurückgegeben.
timeout  | Zahl (default=1000) | Timeout in Millisekunden. Nur die vor Ablauf des Timeouts berechneten Ergebnisse werden zurückgegeben.

Mithilfe der Parameter *count* und *offset* kann eine große Anzahl von Ergebnissen inkrementell über mehrere Anforderungen abgerufen werden.
  
## <a name="response-json"></a>Antwort (JSON)
JSONPath|BESCHREIBUNG
----|----
$.expr | *expr*-Parameter aus der Anforderung.
$.entities | Array mit 0 oder mehr Objektentitäten, die dem strukturierten Abfrageausdruck entsprechen. 
$.aborted | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.

Jede Entität enthält einen *logprob*-Wert und die Werte der angeforderten Attribute.

## <a name="example"></a>Beispiel
Im Beispiel der wissenschaftlichen Veröffentlichungen übergibt die folgende Anforderung einen strukturierten Abfrageausdruck (möglicherweise aus der Ausgabe einer *interpret*-Anforderung) und ruft einige Attribute für die ersten zwei übereinstimmenden Entitäten ab:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Die Antwort enthält die ersten zwei („count=2“) übereinstimmenden Entitäten mit der höchsten Wahrscheinlichkeit.  Für jede Entität werden Attribute zu Titel, Jahr, Name des Autors und Autor-ID zurückgegeben.  Beachten Sie, dass die Struktur der kombinierten Attributwerte der Art und Weise entspricht, wie sie in der Datendatei angegeben werden. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
