---
title: JSON-Suchsyntax – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die JSON-Suchsyntax, die Sie in der Academic Knowledge-API verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336965"
---
# <a name="json-search-syntax"></a>JSON-Suchsyntax

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Die Knotennamen in einem Abfragepfad (_v0, v1, ..._ ) dienen als Knotennamen, die im Abfrageobjekt referenziert werden können; die Edgenamen (_e0, e1, ..._ ) im Pfad repräsentieren die entsprechenden Edgetypen. Wir können ein Sternchen _*_ als Knoten- oder Edgenamen verwenden (außer für den Startknoten, der angegeben werden muss), um zu erklären, dass es keine Einschränkungen für ein solches Element gibt. Beispielsweise ruft ein Abfragepfad `/v0/*/v1/e1/*/` Pfade aus dem Diagramm ab, ohne den Edgetypen _(v0, v1)_ einzuschränken. Die Abfrage hat auch keine Einschränkungen für das Ziel (den letzten Knoten) des Pfades.

Wenn ein Pfad nur einen Knoten enthält, z.B. _v0_, gibt die Abfrage einfach alle Entitäten zurück, die die Einschränkungen erfüllen. Ein auf den Startknoten angewandtes Einschränkungsobjekt heißt *Starting Query Object*, dessen Spezifikation wie folgt lautet.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Wenn ein Pfad mehr als nur einen Startknoten enthält, führt der Abfrageprozessor einen Diagrammdurchlauf nach dem angegebenen Pfadmuster durch. Wenn er an einem Knoten ankommt, werden die benutzerdefinierten Durchlaufaktionen ausgelöst, d.h. ob der Abfrageprozessor am aktuellen Knoten anhalten und zurückkehren soll oder das Diagramm weiter durchsucht wird. Wenn keine Durchlaufaktion angegeben wird, werden die Standardaktionen weitergeleitet. Die Standardaktion für einen Zwischenknoten besteht darin, das Diagramm weiter zu durchsuchen. Die Standardaktion für den letzten Knoten eines Pfads ist anhalten und zurückkehren. Ein Einschränkungsobjekt, das Durchlaufaktionen angibt, wird *Traversal Action Object* genannt. Die Spezifikation ist wie folgt angegeben:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Der POST-Text einer *JSON*-Suchabfrage sollte mindestens ein *Pfadmuster* enthalten. Durchlaufaktionsobjekte sind optional. Hier sind zwei Beispiele.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

