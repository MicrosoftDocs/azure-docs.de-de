---
title: CalcHistogram-Methode – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die CalcHistogram-Methode, um die Verteilung von Attributwerten für einen Satz von Dokumententitäten zu berechnen.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498830"
---
# <a name="calchistogram-method"></a>calchistogram-Methode

Die **calchistogram**-REST-API wird verwendet, um die Verteilung von Attributwerten für einen Satz von Dokumententitäten zu berechnen.          


**REST-Endpunkt:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Anforderungsparameter

NAME  |Wert | Erforderlich?  |BESCHREIBUNG
-----------|----------|--------|----------
**expr**    |Textzeichenfolge | Ja  |Ein Abfrageausdruck, der die Entitäten angibt, über die Histogramme berechnet werden sollen.
**model** |Textzeichenfolge | Nein |Wählen Sie den Namen des Modells aus, das Sie abfragen möchten.  Derzeit wird als Standardwert *latest* verwendet.
**attributes** | Textzeichenfolge | Nein<br>Standard: | Eine durch Kommas getrennte Liste, die die Attributwerte in einer Antwort angibt. Bei den Attributnamen wird zwischen Groß- und Kleinschreibung unterschieden.
**count** |Number | Nein<br>Standardwert: 10 |Anzahl der zurückzugebenden Ergebnisse.
**offset**  |Number | Nein<br>Standardwert: 0 |Index des ersten zurückzugebenden Ergebnisses.
**timeout**  |Number | Nein<br>Standardwert: 1000 |Timeout in Millisekunden. Nur die vor Ablauf des Timeouts gefundenen Interpretationen werden zurückgegeben.

## <a name="response-json"></a>Antwort (JSON)

NAME | BESCHREIBUNG
--------|---------
**expr**  |Der „expr“-Parameter aus der Anforderung.
**num_entities** | Gesamtanzahl der übereinstimmenden Entitäten.
**histograms** |  Ein Array von Histogrammen, eines für jedes, in der Anforderung angegebene Attribut.
**histograms[x].attribute** | Der Name des Attributs, über das das Histogramm berechnet wurde.
**histograms[x].distinct_values** | Die Anzahl der unterschiedlichen Werte unter den übereinstimmenden Entitäten für dieses Attribut.
**histograms[x].total_count** | Gesamtanzahl der Wertinstanzen unter den übereinstimmenden Entitäten für dieses Attribut.
**histograms[x].histogram** | Histogrammdaten für dieses Attribut.
**histograms[x].histogram[y].value** |  Ein Wert für das Attribut.
**histograms[x].histogram[y].logprob**  |Gesamtwahrscheinlichkeit natürlicher Logarithmen der übereinstimmenden Elemente bei diesem Attributwert.
**histograms[x].histogram[y].count**  |Die Anzahl der übereinstimmenden Entitäten bei diesem Attributwert.
**aborted** | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.


#### <a name="example"></a>Beispiel:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>In diesem Beispiel können wir, um ein Histogramm der Anzahl der Veröffentlichungen pro Jahr für einen bestimmten Autor seit 2010 zu erzeugen, zuerst den Abfrageausdruck mit Hilfe der **interpret**-API erzeugen: *papers by jaime teevan after 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Der Ausdruck in der ersten Interpretation, die von der interpret-API zurückgegeben wird, lautet *And(Composite(AA.AuN=='jaime teevan'),Y>2012)* .
<br>Dieser Ausdruckswert wird dann an die **calchistogram**-API weitergegeben. Der *attributes=Y,F.FN*-Parameter gibt an, dass die Verteilungen der Dokumentanzahl z.B. nach „Year“ und „Field of Study“ geordnet sein sollen:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Die Antwort auf diese Anforderung zeigt zunächst an, dass es 37 Dokumente gibt, die dem Abfrageausdruck entsprechen.  Für das *Year*-Attribut gibt es 3 unterschiedliche Werte; einen für jedes Jahr seit 2012 (d.h. 2013, 2014 und 2015), wie in der Abfrage angegeben.  Die Gesamtanzahl der Dokumente über die 3 unterschiedlichen Werte beträgt „37“.  Für jedes *Year*-Attribut zeigt das Histogramm den Wert, die Gesamtwahrscheinlichkeit natürlicher Logarithmen und die Anzahl der übereinstimmenden Entitäten an.     

Das Histogramm für *Field of Study* zeigt, dass es 34 unterschiedliche Forschungsbereiche gibt. Da ein Dokument eventuell mehreren Forschungsbereichen zugeordnet wird, kann die Gesamtanzahl (53) größer sein als die Anzahl der übereinstimmenden Entitäten.  Obwohl 34 unterschiedliche Werte vorhanden sind, enthält die Antwort aufgrund des *count=4*-Parameters nur die ersten 4.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
