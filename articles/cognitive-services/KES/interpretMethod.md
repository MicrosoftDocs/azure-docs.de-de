---
title: Interpret-Methode – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Interpret-Methode in der Knowledge Exploration Service-API (KES) verwenden können.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814134"
---
# <a name="interpret-method"></a>interpret-Methode

Die *interpret*-Methode akzeptiert eine Abfragezeichenfolge in natürlicher Sprache und gibt formatierte Interpretationen der Benutzerabsicht basierend auf der Grammatik und den Indexdaten zurück.  Als interaktives Suchfeature kann diese Methode aufgerufen werden, während die einzelnen Zeichen vom Benutzer eingegeben werden. Hierbei ist der Parameter *complete* auf 1 festgelegt, um Vorschläge der automatischen Vervollständigung zu aktivieren.

## <a name="request"></a>Anforderung

`http://<host>/interpret?query=<query>[&<options>]`

NAME|Wert| BESCHREIBUNG
----|----|----
query    | Textzeichenfolge | Vom Benutzer eingegeben Abfrage.  Wenn der complete-Parameter auf „1“ gesetzt ist, wird die Abfrage als Präfix für die Generierung von Vorschlägen der automatischen Vervollständigung für Abfragen interpretiert.        
complete | „0“ (Standardwert) oder „1“ | „1“ bedeutet, dass Vorschläge für die automatische Vervollständigung basierend auf der Grammatik und den Indexdaten generiert werden.         
count    | Zahl (default=10) | Maximale Anzahl der zurückzugebenden Interpretationen.         
offset   | Zahl (default=0) | Index der ersten zurückzugebenden Interpretation.  Zum Beispiel gibt *count=2&offset=0* die Interpretationen „0“ und „1“ zurück. *count=2&offset=2* gibt die Interpretationen „2“ und „3“ zurück.       
timeout  | Zahl (default=1000) | Timeout in Millisekunden. Nur die vor Ablauf des Timeouts gefundenen Interpretationen werden zurückgegeben.

Mithilfe der Parameter *count* und *offset* kann eine große Anzahl von Ergebnissen inkrementell über mehrere Anforderungen abgerufen werden.

## <a name="response-json"></a>Antwort (JSON)

JSONPath     | BESCHREIBUNG
---------|---------
$.query |Der *query*-Parameter aus der Anforderung.
$.interpretations   |Array mit 0 oder mehr Möglichkeiten, die Eingabeabfrage an die Grammatik anzupassen.
$.interpretations[\*].logprob   |Die relative Logarithmuswahrscheinlichkeit der Interpretation (<= 0).  Höhere Werte sind wahrscheinlicher.
$.interpretations[\*].parse |XML-Zeichenfolge, die zeigt, wie die einzelnen Teile der Abfrage interpretiert wurden.
$.interpretations[\*].rules |Ein Array von 1 oder mehreren, in der Grammatik definierten Regeln, die bei der Interpretation aufgerufen wurden.
$.interpretations[\*].rules[\*].name    |Name der Regel.
$.interpretations[\*].rules[\*].output  |Semantische Ausgabe der Regel.
$.interpretations[\*].rules[\*].output.type |Datentyp der semantischen Ausgabe.
$.interpretations[\*].rules[\*].output.value|Wert der semantischen Ausgabe.  
$.aborted | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.

### <a name="parse-xml"></a>XML-Analyse

Bei der XML-Analyse wird die (abgeschlossene) Abfrage mit Informationen darüber versehen, inwiefern sie den Regeln in der Grammatik und den Attributen im Index entspricht.  Im Folgenden finden Sie ein Beispiel aus dem Bereich der wissenschaftlichen Veröffentlichungen:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

Das `<rule>`-Element begrenzt den Bereich in der Abfrage entsprechend der Regel, die durch das jeweilige `name`-Attribut angegeben ist.  Sie kann geschachtelt sein, wenn die Analyse Regelverweise in der Grammatik einschließt.

Das `<attr>`-Element begrenzt den Bereich in der Abfrage entsprechend des Indexattributs, das durch das jeweilige `name`-Attribut angegeben ist.  Wenn die Übereinstimmung ein Synonym in der Eingabeabfrage beinhaltet, enthält das `canonical`-Attribut den kanonischen Wert entsprechend des Synonyms aus dem Index.

## <a name="example"></a>Beispiel

Im Beispiel der wissenschaftlichen Veröffentlichungen gibt die folgende Anforderung bis zu zwei Vorschläge der automatischen Vervollständigung für die Präfixabfrage „papers by jaime“ zurück:

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Die Antwort enthält die beiden („count=2“) wahrscheinlichsten Interpretationen, in die die Teilabfrage „papers by jaime“ vervollständigt wird: „papers by jaime teevan“ und „papers by jaime green“.  Der Dienst erzeugte Abfragevervollständigungen, anstatt nur exakte Übereinstimmungen für den Autor „jaime“ zu berücksichtigen, da in der Anforderung „complete=1“ angegeben wurde. Beachten Sie, dass der kanonische Wert „j l green“ mit dem Synonym „jamie green“ übereinstimmt, wie in der Analyse angegeben.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Wenn die semantische Ausgabe wie in diesem Beispiel vom Typ „query“ ist, können die übereinstimmenden Objekte abgerufen werden, indem *output.value* über den Parameter *expr* an die [*evaluate*](evaluateMethod.md)-API übergeben wird.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
