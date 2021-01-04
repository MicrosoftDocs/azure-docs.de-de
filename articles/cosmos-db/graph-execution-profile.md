---
title: Verwenden des Ausführungsprofils zur Auswertung von Abfragen in der Gremlin-API für Azure Cosmos DB
description: Es wird beschrieben, wie Sie für Ihre Gremlin-Abfragen die Problembehandlung durchführen und sie verbessern, indem Sie den Schritt „Ausführungsprofil“ verwenden.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087484"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Gewusst wie: Verwenden des Schritts „Ausführungsprofil“ zum Auswerten Ihrer Gremlin-Abfragen
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Dieser Artikel enthält eine Übersicht über die Verwendung des Schritts „Ausführungsprofil“ für Graphdatenbanken der Gremlin-API für Azure Cosmos DB. In diesem Schritt werden relevante Informationen zur Problembehandlung und zu Abfrageoptimierungen bereitgestellt. Er ist mit allen Gremlin-Abfragen kompatibel, die für ein Cosmos DB-Gremlin-API-Konto ausgeführt werden können.

Fügen Sie zum Verwenden dieses Schritts einfach den Funktionsaufruf `executionProfile()` am Ende Ihrer Gremlin-Abfrage an. **Ihre Gremlin-Abfrage wird ausgeführt** , und als Ergebnis des Vorgangs wird ein JSON-Antwortobjekt mit dem Ausführungsprofil der Abfrage zurückgegeben.

Beispiel:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Nach dem Aufrufen des Schritts `executionProfile()` ergibt sich als Antwort ein JSON-Objekt, das den ausgeführten Gremlin-Schritt, die benötigte Gesamtdauer und ein Array mit den Cosmos DB-Runtimeoperatoren, zu denen die Anweisung geführt hat, enthält.

> [!NOTE]
> Diese Implementierung für „Ausführungsprofil“ ist nicht in der Apache Tinkerpop-Spezifikation definiert. Der Schritt gilt spezifisch für die Implementierung der Gremlin-API für Azure Cosmos DB.


## <a name="response-example"></a>Antwortbeispiel

Dies ist ein mit Anmerkungen versehenes Beispiel für die Ausgabe, die zurückgegeben wird:

> [!NOTE]
> Dieses Beispiel enthält Kommentare, mit denen die allgemeine Struktur der Antwort beschrieben wird. Die richtigen executionProfile-Antworten enthalten keine Kommentare.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Im executionProfile-Schritt wird die Gremlin-Abfrage ausgeführt. Dies umfasst die Schritte `addV` oder `addE`, die zur Erstellung führen und mit denen die in der Abfrage angegebenen Änderungen committet werden. Dies führt dazu, dass auch die von der Gremlin-Abfrage generierten Anforderungseinheiten in Rechnung gestellt werden.

## <a name="execution-profile-response-objects"></a>Antwortobjekte des Ausführungsprofils

Die Antwort der Funktion „executionProfile()“ führt zu einer Hierarchie mit JSON-Objekten mit der folgenden Struktur:
  - **Gremlin-Vorgangsobjekt** : Steht für den gesamten Gremlin-Vorgang, der ausgeführt wurde. Es enthält die folgenden Eigenschaften.
    - `gremlin`: Die explizite Gremlin-Anweisung, die ausgeführt wurde.
    - `totalTime`: Die Zeit in Millisekunden für die Ausführung des Schritts. 
    - `metrics`: Ein Array mit allen Cosmos DB-Runtimeoperatoren, die für die Abfrage ausgeführt wurden. Diese Liste ist in der Reihenfolge der Ausführung sortiert.
    
  - **Cosmos DB-Runtimeoperatoren** : Steht für die Komponenten des gesamten Gremlin-Vorgangs. Diese Liste ist in der Reihenfolge der Ausführung sortiert. Jedes Objekt enthält die folgenden Eigenschaften:
    - `name`: Name des Operators. Dies ist der Typ des Schritts, der ausgewertet und ausgeführt wurde. Die Tabelle unten enthält weitere Informationen.
    - `time`: Zeitraum in Millisekunden, der für einen bestimmten Operator benötigt wurde.
    - `annotations`: Enthält zusätzliche spezifische Informationen zum ausgeführten Operator.
    - `annotations.percentTime`: Prozentsatz des gesamten Zeitraums, der zum Ausführen des spezifischen Operators benötigt wurde.
    - `counts`: Anzahl von Objekten, die von diesem Operator über die Speicherebene zurückgegeben wurden. Diese Angabe ist im Skalarwert `counts.resultCount` enthalten.
    - `storeOps`: Steht für einen Speichervorgang, der eine oder mehrere Partitionen umfassen kann.
    - `storeOps.fanoutFactor`: Steht für die Anzahl von Partitionen, auf die von diesem spezifischen Speichervorgang zugegriffen wurde.
    - `storeOps.count`: Steht für die Anzahl von Ergebnissen, die von diesem Speichervorgang zurückgegeben wurden.
    - `storeOps.size`: Steht für die Größe des Ergebnisses eines bestimmten Speichervorgangs in Byte.

Gremlin-Runtimeoperator für Cosmos DB|BESCHREIBUNG
---|---
`GetVertices`| In diesem Schritt wird ein Satz mit Objekten, die über Prädikate verfügen, von der Persistenzebene abgerufen. 
`GetEdges`| In diesem Schritt werden die Kanten abgerufen, die an eine Gruppe von Scheitelpunkten angrenzen. Dieser Schritt kann zu einem oder mehreren Speichervorgängen führen.
`GetNeighborVertices`| Dieser Schritt enthält die Scheitelpunkte, die mit einer Gruppe von Kanten verbunden sind. Die Kanten enthalten die Partitionsschlüssel und IDs der Quell- und Zielscheitelpunkte.
`Coalesce`| Dieser Schritt gilt jeweils für die Auswertung von zwei Vorgängen, wenn der Gremlin-Schritt `coalesce()` ausgeführt wird.
`CartesianProductOperator`| In diesem Schritt wird ein kartesisches Produkt zwischen zwei Datasets berechnet. Normalerweise erfolgt die Ausführung jeweils, wenn die Prädikate `to()` oder `from()` verwendet werden.
`ConstantSourceOperator`| In diesem Schritt wird ein Ausdruck berechnet, um als Ergebnis einen konstanten Wert zu erhalten.
`ProjectOperator`| Der Schritt umfasst das Vorbereiten und Serialisieren einer Antwort anhand des Ergebnisses der vorherigen Vorgänge.
`ProjectAggregation`| In diesem Schritt wird eine Antwort für einen Aggregatvorgang vorbereitet und serialisiert.

> [!NOTE]
> Diese Liste wird weiter aktualisiert, wenn neue Operatoren hinzugefügt werden.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Beispiele zur Analyse der Antwort eines Ausführungsprofils

Hier sind Beispiele für häufige Optimierungen angegeben, die anhand der Antwort des Ausführungsprofils ermittelt werden können:
  - „Blinde“ Auffächerungsabfrage
  - Nicht gefilterte Abfrage

### <a name="blind-fan-out-query-patterns"></a>Muster von „blinden“ Auffächerungsabfragen

Angenommen, für einen **partitionierten Graphen** ergibt sich die folgende Antwort eines Ausführungsprofils:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Hieraus können die folgenden Schlüsse gezogen werden:
- Die Abfrage ist eine Suche nach einer einzelnen ID, da die Gremlin-Anweisung das Muster `g.V('id')` aufweist.
- Anhand der Metrik `time` lässt sich erkennen, dass die Latenz dieser Abfrage wahrscheinlich hoch ist, da sie [für einen einzelnen Punktlesevorgang über 10 ms beträgt](./introduction.md#guaranteed-speed-at-any-scale).
- Wenn wir uns das `storeOps`-Objekt ansehen, ist erkennbar, dass `fanoutFactor` den Wert `5` hat. Dies bedeutet, dass von diesem Vorgang auf [5 Partitionen](./partitioning-overview.md) zugegriffen wurde.

Als Schlussfolgerung dieser Analyse können wir festhalten, dass von der ersten Abfrage auf mehr Partitionen als nötig zugegriffen wird. Dies kann geändert werden, indem der Partitionierungsschlüssel in der Abfrage als Prädikat angegeben wird. Dies führt zu einer niedrigeren Latenz und zu geringeren Kosten pro Abfrage. Informieren Sie sich eingehender über die [Graphpartitionierung](graph-partitioning.md). Eine besser geeignete Abfrage ist `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Muster von nicht gefilterten Abfragen

Vergleichen Sie die folgenden beiden Antworten von Ausführungsprofilen. Der Einfachheit halber verwenden wir in diesen Beispielen einen einzelnen partitionierten Graphen.

Mit der ersten Abfrage werden alle Scheitelpunkte mit der Bezeichnung `tweet` abgerufen, und anschließend werden die angrenzenden Scheitelpunkte abgerufen:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Beachten Sie das Profil derselben Abfrage, aber jetzt mit dem zusätzlichen Filter `has('lang', 'en')`, bevor Sie die angrenzenden Scheitelpunkte untersuchen:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Diese beiden Abfragen haben zum gleichen Ergebnis geführt. Für das erste Ergebnis sind aber mehr Anforderungseinheiten erforderlich, da ein größeres anfängliches Dataset durchlaufen werden musste, bevor die angrenzenden Elemente abgefragt wurden. Es sind Anzeichen für dieses Verhalten zu erkennen, wenn wir die folgenden Parameter beider Antworten vergleichen:
- Der Wert `metrics[0].time` ist in der ersten Antwort höher. Dies ist ein Hinweis darauf, dass die Auflösung dieses einzelnen Schritts länger gedauert hat.
- Auch der Wert `metrics[0].counts.resultsCount` ist in der ersten Antwort höher. Dies weist darauf hin, dass das anfängliche Arbeitsdataset größer war.

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die [unterstützten Gremlin-Features](gremlin-support.md) von Azure Cosmos DB. 
* Informieren Sie sich über die [Gremlin-API in Azure Cosmos DB](graph-introduction.md).