---
title: 'Datenpartitionierung in Azure Cosmos DB: Gremlin-API'
description: Erfahren Sie, wie Sie in Azure Cosmos DB einen partitionierten Graphen verwenden können. In diesem Artikel werden auch die Anforderungen und bewährten Methoden für einen partitionierten Graphen beschrieben.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888407"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Verwenden eines partitionierten Graphen in Azure Cosmos DB

Eines der Hauptmerkmale der Gremlin-API in Azure Cosmos DB ist die Funktion, große Graphen mittels horizontaler Skalierung zu verarbeiten. Die horizontale Skalierung erfolgt über die [Partitionierungsfunktionen in Azure Cosmos DB](partition-data.md). Die Container können in Bezug auf Speicher und Durchsatz unabhängig skaliert werden. Sie können Container in Azure Cosmos DB erstellen, die automatisch so skaliert werden können, dass die Daten eines Graphen gespeichert werden. Die Daten werden basierend auf dem angegebenen **Partitionsschlüssel** automatisch abgeglichen.

In diesem Dokument werden die Besonderheiten der Partitionierung von Graphendatenbanken und ihre Auswirkungen auf Knoten und Kanten beschrieben.

## <a name="requirements-for-partitioned-graph"></a>Anforderungen für einen partitionierten Graphen

Die folgenden Details sind zu beachten, wenn Sie einen Container mit einem partitionierten Graphen erstellen:

- **Die Partitionierung ist erforderlich**, wenn im Container voraussichtlich Daten mit einer Größe von über 10 GB gespeichert werden oder wenn mehr als 10.000 Anforderungseinheiten (Request Units, RUs) pro Sekunde zugeordnet werden sollen.

- **Knoten und Kanten werden als JSON-Dokumente gespeichert**.

- **Knoten erfordern einen Partitionsschlüssel**. Dieser Schlüssel bestimmt, in welcher Partition der Knoten mithilfe eines Hashalgorithmus gespeichert wird. Der Name dieses Partitionsschlüssels ist eine Zeichenfolge mit einem Wort ohne Leer- und Sonderzeichen. Der Partitionsschlüssel wird beim Erstellen eines neuen Containers definiert und hat folgendes Format: `/partitioning-key-name`.

- **Kanten werden mit ihren Quellknoten gespeichert**. Das heißt, dass über den jeweiligen Partitionsschlüssel für jeden Knoten definiert wird, wo dieser zusammen mit den zugehörigen ausgehenden Kanten gespeichert wird. Dies erfolgt, um bei der Verwendung der Kardinalität `out()` in Graphenabfragen partitionsübergreifende Abfragen zu vermeiden.

- **Abfragen von Graphen müssen einen Partitionsschlüssel angeben**. Um die Vorteile der horizontalen Partitionierung in Azure Cosmos DB voll auszunutzen, muss bei Auswahl eines einzelnen Knotens nach Möglichkeit der Partitionsschlüssel angegeben werden. Es folgen Abfragen zur Auswahl eines oder mehrerer Knoten in einem partitionierten Graphen:

    - `/id` und `/label` werden als Partitionsschlüssel für einen Container in der Gremlin-API nicht unterstützt.


    - Auswählen eines Knotens anhand der ID und **befolgen des Schritts `.has()` zum Angeben der Partitionsschlüsseleigenschaft**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Auswählen eines Knotens durch **Angeben eines Tupels einschließlich Partitionsschlüssel und ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Angeben eines **Arrays von Tupeln mit Partitionsschlüsselwerten und IDs**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Auswählen einer Gruppe von Knoten und **Angeben einer Liste von Partitionsschlüsselwerten**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Bewährte Methoden bei der Verwendung eines partitionierten Graphen

Befolgen Sie folgende Richtlinien, um die Leistung und Skalierbarkeit bei der Verwendung partitionierter Graphen in unbegrenzten Containern sicherzustellen:

- **Geben Sie bei der Abfrage eines Knotens immer den Partitionsschlüsselwert an**. Das Abrufen eines Knotens aus einer bekannten Partition ist eine Möglichkeit zur Leistungssteigerung.

- **Verwenden Sie beim Abfragen von Kanten nach Möglichkeit die Ausgangsrichtung**. Wie bereits erwähnt, werden Kanten mit ihren Quellknoten in Ausgangsrichtung gespeichert. So wird die Wahrscheinlichkeit eines Rückgriffs auf partitionsübergreifende Abfragen minimiert, wenn die Daten und Abfragen unter Berücksichtigung dieses Musters entworfen werden.

- **Wählen Sie einen Partitionsschlüssel, der die Daten gleichmäßig auf die Partitionen verteilt**. Diese Entscheidung hängt stark vom Datenmodell der Lösung ab. Weitere Informationen zum Erstellen eines geeigneten Partitionsschlüssels finden Sie unter [Partitionierung und Skalierung in Azure Cosmos DB](partition-data.md).

- **Optimieren Sie Abfragen, um Daten innerhalb der Grenzen einer Partition abzurufen**. Eine optimale Partitionierungsstrategie ist auf die Abfragemuster abgestimmt. Abfragen, die Daten aus einer einzelnen Partition abrufen, bieten die bestmögliche Leistung.

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit den folgenden Artikeln fortfahren:

* Erfahren Sie mehr über das [Partitionieren und Skalieren in Azure Cosmos DB](partition-data.md).
* Erfahren Sie mehr über die [Gremlin-Unterstützung in der Gremlin-API](gremlin-support.md).
* Erfahren Sie mehr in der [Einführung in die Gremlin-API](graph-introduction.md).
