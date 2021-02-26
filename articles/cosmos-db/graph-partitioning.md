---
title: 'Datenpartitionierung in Azure Cosmos DB: Gremlin-API'
description: Erfahren Sie, wie Sie in Azure Cosmos DB einen partitionierten Graphen verwenden können. In diesem Artikel werden auch die Anforderungen und bewährten Methoden für einen partitionierten Graphen beschrieben.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4d76bdcb385ed2fe4b8a697f24187b8e3d5addbc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988749"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Verwenden eines partitionierten Graphen in Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Eines der Hauptmerkmale der Gremlin-API in Azure Cosmos DB ist die Funktion, große Graphen mittels horizontaler Skalierung zu verarbeiten. Die Container können in Bezug auf Speicher und Durchsatz unabhängig skaliert werden. Sie können Container in Azure Cosmos DB erstellen, die automatisch so skaliert werden können, dass die Daten eines Graphen gespeichert werden. Die Daten werden basierend auf dem angegebenen **Partitionsschlüssel** automatisch abgeglichen.

Die Partitionierung erfolgt intern, wenn im Container voraussichtlich Daten mit einer Größe von über 20 GB gespeichert oder mehr als 10.000 Anforderungseinheiten (Request Units, RUs) pro Sekunde zugeteilt werden sollen. Daten werden automatisch auf Grundlage des von Ihnen angegebenen Partitionsschlüssels partitioniert. Der Partitionsschlüssel ist erforderlich, wenn Sie Graphcontainer mithilfe des Azure-Portals oder von Gremlin-Treibern ab Version 3.x erstellen. Der Partitionsschlüssel ist nicht erforderlich, wenn Sie Gremlin-Treiber bis Version 2.x verwenden.

Es gelten die gleichen allgemeinen Prinzipien wie für den [Azure Cosmos DB-Partitionierungsmechanismus](partitioning-overview.md) – abgesehen von einigen graphspezifischen Optimierungen, die weiter unten beschrieben sind.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Graphpartitionierung." border="false":::

## <a name="graph-partitioning-mechanism"></a>Graphpartitionierungsmechanismus

In den folgenden Richtlinien wird beschrieben, wie die Partitionierungsstrategie in Azure Cosmos DB funktioniert:

- **Knoten und Kanten werden als JSON-Dokumente gespeichert**.

- **Knoten erfordern einen Partitionsschlüssel**. Dieser Schlüssel bestimmt, in welcher Partition der Knoten mithilfe eines Hashalgorithmus gespeichert wird. Der Name der Eigenschaft „Partitionsschlüssel“ wird beim Erstellen eines neuen Containers definiert und hat folgendes Format: `/partitioning-key-name`.

- **Kanten werden mit ihren Quellknoten gespeichert**. Das heißt, dass über den jeweiligen Partitionsschlüssel für jeden Knoten definiert wird, wo dieser zusammen mit den zugehörigen ausgehenden Kanten gespeichert wird. Diese Optimierung erfolgt, um bei der Verwendung der Kardinalität `out()` in Graphenabfragen partitionsübergreifende Abfragen zu vermeiden.

- **Kanten enthalten Verweise auf die Knoten, auf die sie zeigen**. Alle Kanten werden mit den Partitionsschlüsseln und IDs der Knoten gespeichert, auf die sie zeigen. Diese Berechnung sorgt dafür, dass alle `out()`-Verzeichnisabfragen immer zugeordnete partitionierte Abfragen und keine blinden partitionsübergreifenden Abfragen sind.

- **Abfragen von Graphen müssen einen Partitionsschlüssel angeben**. Um die Vorteile der horizontalen Partitionierung in Azure Cosmos DB voll auszunutzen, muss bei Auswahl eines einzelnen Knotens nach Möglichkeit der Partitionsschlüssel angegeben werden. Es folgen Abfragen zur Auswahl eines oder mehrerer Knoten in einem partitionierten Graphen:

    - `/id` und `/label` werden als Partitionsschlüssel für einen Container in der Gremlin-API nicht unterstützt.


    - Auswählen eines Knotens anhand der ID und **befolgen des Schritts `.has()` zum Angeben der Partitionsschlüsseleigenschaft**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Auswählen eines Knotens durch **Angeben eines Tupels einschließlich Partitionsschlüssel und ID**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Angeben eines **Arrays von Tupeln mit Partitionsschlüsselwerten und IDs**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Auswählen einer Gruppe von Knoten mit deren IDs und **Angeben einer Liste von Partitionsschlüsselwerten**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Verwenden der **Partitionierungsstrategie** zu Beginn einer Abfrage und Angeben einer Partition für den restlichem Umfang der Gremlin-Abfrage:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Bewährte Methoden bei der Verwendung eines partitionierten Graphen

Befolgen Sie folgende Richtlinien, um die Leistung und Skalierbarkeit bei der Verwendung partitionierter Graphen in unbegrenzten Containern sicherzustellen:

- **Geben Sie bei der Abfrage eines Knotens immer den Partitionsschlüsselwert an**. Das Abrufen eines Knotens aus einer bekannten Partition ist eine Möglichkeit zur Leistungssteigerung. Alle nachfolgenden Adjazenzvorgänge werden immer einer Partition zugeordnet, da Kanten Referenz-ID und Partitionsschlüssel für ihre Zielknoten enthalten.

- **Verwenden Sie beim Abfragen von Kanten nach Möglichkeit die Ausgangsrichtung**. Wie bereits erwähnt, werden Kanten mit ihren Quellknoten in Ausgangsrichtung gespeichert. So wird die Wahrscheinlichkeit eines Rückgriffs auf partitionsübergreifende Abfragen minimiert, wenn die Daten und Abfragen unter Berücksichtigung dieses Musters entworfen werden. Im Gegensatz dazu ist die `in()`-Abfrage immer eine teure Auffächerungsabfrage.

- **Wählen Sie einen Partitionsschlüssel, der die Daten gleichmäßig auf die Partitionen verteilt**. Diese Entscheidung hängt stark vom Datenmodell der Lösung ab. Weitere Informationen zum Erstellen eines geeigneten Partitionsschlüssels finden Sie unter [Partitionierung und Skalierung in Azure Cosmos DB](partitioning-overview.md).

- **Optimieren Sie Abfragen, um Daten innerhalb der Grenzen einer Partition abzurufen**. Eine optimale Partitionierungsstrategie ist auf die Abfragemuster abgestimmt. Abfragen, die Daten aus einer einzelnen Partition abrufen, bieten die bestmögliche Leistung.

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit den folgenden Artikeln fortfahren:

* Erfahren Sie mehr über das [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md).
* Erfahren Sie mehr über die [Gremlin-Unterstützung in der Gremlin-API](gremlin-support.md).
* Erfahren Sie mehr in der [Einführung in die Gremlin-API](graph-introduction.md).
