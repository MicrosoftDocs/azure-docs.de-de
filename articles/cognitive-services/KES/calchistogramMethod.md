---
title: CalcHistogram-Methode – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die CalcHistogram-Methode in der Knowledge Exploration Service-API (KES) verwenden können.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814174"
---
# <a name="calchistogram-method"></a>calchistogram-Methode
Die *calchistogram*-Methode berechnet die Objekte, die einem strukturierten Abfrageausdruck entsprechen, und berechnet die Verteilung ihrer Attributwerte.

## <a name="request"></a>Anforderung
`http://<host>/calchistogram?expr=<expr>[&options]` 

NAME|Wert|BESCHREIBUNG
----|-----|-----------
expr | Textzeichenfolge | Strukturierter Abfrageausdruck, der die Indexentitäten angibt, über die Histogramme berechnet werden sollen.
attributes | Textzeichenfolge (default="") | Durch Trennzeichen getrennte Liste von Attributen, die in der Antwort enthalten sein sollen.
count   | Zahl (default=10) | Anzahl der zurückzugebenden Ergebnisse.
offset  | Zahl (default=0) | Index des ersten zurückzugebenden Ergebnisses.

## <a name="response-json"></a>Antwort (JSON)
JSONPath | BESCHREIBUNG
----|----
$.expr | *expr*-Parameter aus der Anforderung.
$.num_entities | Gesamtanzahl der übereinstimmenden Entitäten.
$.histograms |  Ein Array von Histogrammen, eines für jedes angeforderte Attribut.
$.histograms[\*].attribute | Der Name des Attributs, über das das Histogramm berechnet wurde.
$.histograms[\*].distinct_values | Die Anzahl der unterschiedlichen Werte unter den übereinstimmenden Entitäten für dieses Attribut.
$.histograms[\*].total_count | Gesamtanzahl der Wertinstanzen unter den übereinstimmenden Entitäten für dieses Attribut.
$.histograms[\*].histogram | Histogrammdaten für dieses Attribut.
$.histograms[\*].histogram[\*].value | Attributwert.
$.histograms[\*].histogram[\*].logprob  | Gesamtwahrscheinlichkeit natürlicher Logarithmen der übereinstimmenden Elemente bei diesem Attributwert.
$.histograms[\*].histogram[\*].count    | Die Anzahl der übereinstimmenden Entitäten bei diesem Attributwert.
$.aborted | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.

### <a name="example"></a>Beispiel
Im Beispiel der wissenschaftlichen Veröffentlichungen berechnet die folgende Formel ein Histogramm zur Anzahl der Veröffentlichungen nach Jahr und Schlüsselwort für einen bestimmten Autor seit 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Die Antwort gibt an, dass es 37 Werke gibt, die dem Abfrageausdruck entsprechen.  Für das *Year*-Attribut gibt es 3 unterschiedliche Werte; einen für jedes Jahr seit 2013.  Die Gesamtanzahl der Werke über die 3 unterschiedlichen Werte beträgt „37“.  Für jedes *Year*-Attribut zeigt das Histogramm den Wert, die Gesamtwahrscheinlichkeit natürlicher Logarithmen und die Anzahl der übereinstimmenden Entitäten an.     

Das Histogramm für *Keyword* zeigt, dass es 34 verschiedene Schlüsselwörter gibt. Da ein Werk eventuell mehreren Schlüsselwörtern zugeordnet wird, kann die Gesamtanzahl (53) größer sein als die Anzahl der übereinstimmenden Entitäten.  Obwohl 34 unterschiedliche Werte vorhanden sind, enthält die Antwort aufgrund des „count=4“-Parameters nur die ersten 4.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
