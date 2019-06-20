---
title: Interpret-Methode – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die interpret-Methode, um formatierte Interpretationen von Benutzerabfragezeichenfolgen auf der Grundlage von Academic Graph-Daten und der Academic-Grammatik in Microsoft Cognitive Services zurückzugeben.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: b679f1da0ada3e61fca79cdb985a43dc445877ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61338451"
---
# <a name="interpret-method"></a>interpret-Methode

Die **interpret**-REST-API nimmt einen Benutzerabfragezeichenfolge (d.h. eine Abfrage, die von einem Benutzer Ihrer Anwendung eingegeben wurde) und gibt formatierte Interpretationen der Benutzerabsicht basierend auf den Academic Graph-Daten der Academic-Grammatik zurück.

Um ein interaktives Erlebnis zu ermöglichen, können Sie diese Methode nach jedem vom Benutzer eingegebenen Zeichen wiederholt aufrufen. In diesem Fall sollten Sie den **complete**-Parameter auf 1 setzen, AutoVervollständigen-Vorschläge zu ermöglichen. Wenn für Ihre Anwendung keine automatische Vervollständigung erforderlich ist, legen Sie für den **complete**-Parameter 0 fest.

**REST-Endpunkt:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Anforderungsparameter

NAME     | Wert | Erforderlich?  | BESCHREIBUNG
---------|---------|---------|---------
**query**    | Textzeichenfolge | Ja | Vom Benutzer eingegeben Abfrage.  Wenn der complete-Parameter auf „1“ gesetzt ist, wird die Abfrage als Präfix für die Generierung von Vorschlägen der automatischen Vervollständigung für Abfragen interpretiert.        
**model**    | Textzeichenfolge | Nein  | Der Name des Modells, das Sie abfragen möchten.  Derzeit wird als Standardwert *latest* verwendet.        
**complete** | 0 oder 1 | Nein<br>Standard: 0  | 1 bedeutet, dass Vorschläge für die automatische Vervollständigung basierend auf der Grammatik und Diagrammdaten generiert werden.         
**count**    | Number | Nein<br>Standard: 10 | Maximale Anzahl der zurückzugebenden Interpretationen.         
**offset**   | Number | Nein<br>Standard: 0  | Index der ersten zurückzugebenden Interpretation. Zum Beispiel gibt *count=2&offset=0* die Interpretationen „0“ und „1“ zurück. *count=2&offset=2* gibt die Interpretationen 2 und 3 zurück.       
**timeout**  | Number | Nein<br>Standard: 1.000 | Timeout in Millisekunden. Nur die vor Ablauf des Timeouts gefundenen Interpretationen werden zurückgegeben.

<br>
  
## <a name="response-json"></a>Antwort (JSON)

NAME     | BESCHREIBUNG
---------|---------
**query** |Der *query*-Parameter aus der Anforderung.
**interpretations** |Ein Array von 0 oder mehr verschiedenen Möglichkeiten, um Benutzereingaben mit der Grammatik abzugleichen.
**interpretations[x].logprob**  |Die relative natürliche Logarithmuswahrscheinlichkeit der Interpretation. Größere Werte sind wahrscheinlicher.
**interpretations[x].parse**  |Eine XML-Zeichenfolge, die zeigt, wie die einzelnen Teile der Abfrage interpretiert wurden.
**interpretations[x].rules**  |Ein Array von 1 oder mehreren, in der Grammatik definierten Regeln, die bei der Interpretation aufgerufen wurden. Für die Academic Knowledge-API gibt es nur eine Regel.
**interpretations[x].rules[y].name**  |Name der Regel.
**interpretations[x].rules[y].output**  |Ausgabe der Regel.
**interpretations[x].rules[y].output.type** |Der Datentyp der Ausgabe der Regel.  Für die Academic Knowledge-API gibt es nur „query“
**interpretations[x].rules[y].output.value**  |Die Ausgabe der Regel. In der Academic Knowledge-API ist dies eine Abfrageausdruck-Zeichenfolge, die an die evaluate- und calchistogram-Methoden weitergegeben werden kann.
**aborted** | „true“, wenn ein Timeout bei der Anforderung aufgetreten ist.

<br>

#### <a name="example"></a>Beispiel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Die untenstehende Antwort enthält die beiden (aufgrund des Parameters *count=2*) wahrscheinlichsten Interpretationen, die die partielle Benutzereingabe *papers by jaime* vervollständigen: *papers by jaime teevan* and *papers by jaime green*.  Der Service erzeugte Abfragevervollständigungen, anstatt nur exakte Übereinstimmungen für den Autor *jaime* zu berücksichtigen, da die Anforderung *complete=1* angegeben wurde. Beachten Sie, dass der kanonische Wert *j l green* mit dem Synonym *jamie green* übereinstimmt, wie in der Analyse angegeben.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Um Entitätsergebnisse für eine Interpretation abzurufen, verwenden Sie *output.value* aus der **interpret**-API, und geben Sie sie über den Parameter *expr* an die **evaluate**-API weiter. In diesem Beispiel lautet die Abfrage für die erste interpretation: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
