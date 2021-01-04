---
title: Wie werden Abfragen von Diagrammdaten in Azure Cosmos DB durchgeführt?
description: Hier erfahren Sie, wie Sie Diagrammdaten von Azure Cosmos DB mithilfe von Gremlin-Abfragen abfragen.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 545ffd303d2039a3c54088220c1fa74e742c750f
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360767"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Tutorial: Abfragen der Gremlin-API von Azure Cosmos DB mithilfe von Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Die [Gremlin-API](graph-introduction.md) von Azure Cosmos DB unterstützt [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-Abfragen. Dieser Artikel enthält Beispieldokumente und Abfragen, um Ihnen den Einstieg zu erleichtern. Eine detaillierte Gremlin-Referenz finden Sie im Artikel [Azure Cosmos DB Gremlin graph support](gremlin-support.md) (Azure Cosmos DB: Gremlin-Graphunterstützung).

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Abfragen von Daten mit Gremlin

## <a name="prerequisites"></a>Voraussetzungen

Diese Abfragen können nur funktionieren, wenn Sie über ein Azure Cosmos DB-Konto verfügen und Diagrammdaten im Container vorliegen. Sie haben beides nicht? Absolvieren Sie den [5-Minuten-Schnellstart](create-graph-dotnet.md) oder das [Entwicklertutorial](tutorial-query-graph.md) zum Erstellen eines Kontos und Füllen Ihrer Datenbank. Sie können die folgenden Abfragen ausführen, indem Sie die [Gremlin-Konsole](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) oder Ihren bevorzugten Gremlin-Treiber verwenden.

## <a name="count-vertices-in-the-graph"></a>Anzahl der Scheitelpunkte im Diagramm

Der folgende Codeausschnitt zeigt, wie Sie die Anzahl der Scheitelpunkte im Diagramm zählen:

```
g.V().count()
```

## <a name="filters"></a>Filter

Filter können Sie in den Gremlin-Schritten `has` und `hasLabel` anwenden und sie mithilfe von `and`, `or`, und `not` kombinieren, um komplexere Filter zu erstellen. Azure Cosmos DB bietet schemaunabhängige Indizierung aller Eigenschaften in den Scheitelpunkten und Graden für schnelle Abfragen:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projektion

Sie können bestimmte Eigenschaften in den Abfrageergebnissen mit dem `values`-Schritt projizieren:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Suchen verwandter Kanten und Scheitelpunkte

Bisher haben wir nur Abfrageoperatoren gesehen, die in jeder Datenbank funktionieren. Diagramme sind schnell und effizient bei Traversierungen, wenn Sie zu verwandten Kanten und Scheitelpunkten navigieren müssen. Wir suchen nun alle Freunde von Thomas. Hierzu suchen wir mit Schritt `outE` von Gremlin alle Out-Edges von Thomas und traversieren dann mit dem Gremlin-Schritt `inV` zu den In-Vertices dieser Kanten:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Die nächste Abfrage führt durch zweimaligen Aufruf von `outE` und `inV` zwei Hops aus, um alle „Freunde von Freunden“ von Thomas zu finden. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Mit Gremlin können Sie komplexere Abfragen erstellen und leistungsstarke Logik zur Graphdurchsuchung implementieren, einschließlich Mischen von Filterausdrücken, Ausführen von Schleifen mithilfe des `loop`-Schritts und Implementieren bedingter Navigation mithilfe des `choose`-Schritts. Erfahren Sie mehr darüber, was Sie mit [Gremlin-Unterstützung](gremlin-support.md) tun können!

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Sie haben gelernt, wie Sie Abfragen mithilfe von Graph durchführen. 

Sie können jetzt mit dem Abschnitt „Konzepte“ fortfahren, um weitere Informationen zu Cosmos DB zu erhalten.

> [!div class="nextstepaction"]
> [Globale Verteilung](distribute-data-globally.md) 

