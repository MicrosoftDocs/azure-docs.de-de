---
title: Graph Search-Methode – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die graph search-Methode in der Academic Knowledge-API, um eine Reihe von akademischen Entitäten basierend auf bestimmten Diagrammmustern zurückzugeben.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339124"
---
# <a name="graph-search-method"></a>graph search-Methode

Die **graph search**-REST-API wird verwendet,um einen Satz von akademischen Entitäten basierend auf den gegebenen Diagrammmustern zurückzugeben.  Die Antwort ist ein Satz von Diagrammpfaden, die die vom Benutzer festgelegten Einschränkungen erfüllen. Ein Diagrammpfad ist eine verschachtelte Folge von Diagrammknoten und Edges in Form von _v0, e0, v1, e1, ...., vn_, wobei _v0_ der Anfangsknoten des Pfades ist.
<br>

**REST-Endpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Anforderungsparameter  

NAME     | Wert | Erforderlich?  | BESCHREIBUNG
-----------|-----------|---------|--------
**mode**       | Textzeichenfolge | Ja | Der Name des Modus, den Sie verwenden möchten. Der Wert lautet entweder *json* oder *lambda*.

Die graph search-Methode muss über eine HTTP POST-Anforderung aufgerufen werden. The POST-Anforderung muss den Content-Type-Header enthalten: **application/json**.

##### <a name="json-search"></a>JSON-Suche 

Für die *json*-Suche ist der POST-Text ein JSON-Objekt. Das JSON-Objekt beschreibt ein Pfadmuster mit benutzerdefinierten Einschränkungen (siehe [Spezifikation des JSON-Objekts](JSONSearchSyntax.md) für die *json*-Suche).


##### <a name="lambda-search"></a>Lamdba-Suche

Für die *Lambda*-Suche ist der POST-Text eine einfache Textzeichenfolge. Der POST-Text ist eine LIKQ-Lambda-Abfragezeichenfolge, die eine einzelne C#-Anweisung ist (siehe die [Spezifikation der Abfragezeichenfolge ](LambdaSearchSyntax.md) für die *lambda*-Suche). 

<br>

## <a name="response-json"></a>Antwort (JSON)

NAME | BESCHREIBUNG
-------|-----   
**results** | Ein Array mit 0 oder mehr Entitäten, die mit den Abfrageausdruck übereinstimmen. Jede Entität enthält die Werte der angeforderten Attribute. Dieses Feld ist vorhanden, wenn die Anforderung erfolgreich verarbeitet wurde.
**error** | HTTP-Statuscodes. Dieses Feld ist vorhanden, wenn bei der Anforderung ein Fehler aufgetreten ist.
**message** | Fehlermeldung. Dieses Feld ist vorhanden, wenn bei der Anforderung ein Fehler aufgetreten ist.

Wenn die Abfrage nicht innerhalb von _800 ms_ verarbeitet werden kann, wird ein _timeout_-Fehler zurückgegeben. 

<br>

#### <a name="example"></a>Beispiel:

##### <a name="json-search"></a>JSON-Suche
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Wenn Sie bei der *json*-Suche die Dokumente erhalten wollen, deren Titel „graph engine“ enthalten und von „bin shao“ geschrieben wurden, können wir die Abfrage wie folgt spezifizieren.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Die Ausgabe einer Abfrage ist ein Array von Diagrammpfaden. Ein Diagrammpfad ist ein Array von Knotenobjekten, die den im Abfragepfad angegebenen Knoten entsprechen. Diese Knotenobjekte haben mindestens eine Eigenschaft *CellID*, die die Entitäts-ID repräsentiert. Andere Eigenschaften können durch Angabe der Eigenschaftsnamen über den SELECT-Operator eines [*Durchlaufaktionsobjektes*](JSONSearchSyntax.md) abgerufen werden.

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Lamdba-Suche 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Für die *lambda*-Suchen können wir, wenn wir die Autoren-IDs eines bestimmten Dokuments erhalten wollen, eine Abfrage wie die folgende schreiben.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Die Ausgabe einer *Lambda*-Suchabfrage ist ebenfalls ein Array von Diagrammpfaden:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Diagrammschema

Das Diagrammschema eignet sich zum Schreiben von Diagrammsuchabfragen. Es ist in folgender Abbildung dargestellt.

![Microsoft Academic-Diagrammschema](./Images/AcademicGraphSchema.png)
