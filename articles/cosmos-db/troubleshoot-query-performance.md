---
title: Behandeln von Problemen bei Verwendung von Azure Cosmos DB
description: Erfahren Sie, wie Sie Probleme mit SQL-Abfragen in Azure Cosmos DB identifizieren, diagnostizieren und beheben.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/02/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6875fc53a651b89fcfe88d3217ff86bd21204f6c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524304"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Behandeln von Problemen bei Verwendung von Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden Sie durch einen allgemeinen empfohlenen Ansatz für die Problembehandlung von Abfragen in Azure Cosmos DB geführt. Sie sollten die in diesem Artikel beschriebenen Schritte zwar nicht als umfassende Verteidigung gegen mögliche Abfrageprobleme ansehen, hier sind jedoch die gängigsten Leistungstipps zusammengefasst. Sie können diesen Artikel als Ausgangspunkt für das Troubleshooting von langsamen oder aufwendigen Abfragen in der Core (SQL)-API von Azure Cosmos DB verwenden. Sie können auch [Diagnoseprotokolle](cosmosdb-monitor-resource-logs.md) verwenden, um Abfragen zu identifizieren, die langsam sind oder beträchtliche Durchsatzmengen verbrauchen. Wenn Sie die Azure Cosmos DB-API für MongoDB verwenden, wird das [Handbuch zur Problembehandlung bei Abfragen mit der Azure Cosmos DB-API für MongoDB](mongodb-troubleshoot-query.md) empfohlen.

Abfrageoptimierungen in Azure Cosmos DB lassen sich grob in folgende Kategorien unterteilen:

- Optimierungen, die die Anzahl der verbrauchten Anforderungseinheiten (Request Units, RUs) der Abfrage verringern
- Optimierungen ausschließlich zum Verringern der Latenz

Durch Senken der RU-Gebühr einer Abfrage können Sie in der Regel auch die Wartezeit verringern.

Dieser Artikel enthält Beispiele, die Sie mit dem [Dataset „Nutrition“](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) neu erstellen können.

## <a name="common-sdk-issues"></a>Allgemeine SDK-Probleme

Bevor Sie diesen Leitfaden lesen, sollten Sie sich mit einigen gängigen SDK-Problemen vertraut machen, die nicht im Zusammenhang mit der Abfrage-Engine stehen.

- Befolgen Sie diese [Tipps zur SDK-Leistungssteigerung](performance-tips.md).
    - [Handbuch zur Problembehandlung beim .NET SDK](troubleshoot-dot-net-sdk.md)
    - [Handbuch zur Problembehandlung beim Java SDK](troubleshoot-java-sdk-v4-sql.md)
- Das SDK ermöglicht das Festlegen eines `MaxItemCount`-Werts für Ihre Abfragen, Sie können jedoch keine Mindestanzahl von Elementen angeben.
    - Der Code sollte jede Seitengröße von 0 bis zur `MaxItemCount` verarbeiten können.
- Manchmal können Abfragen sogar dann leere Seiten enthalten, wenn auf einer zukünftigen Seite Ergebnisse vorhanden sind. Dies könnte folgende Gründe haben:
    - Das SDK führt mehrere Netzwerkaufrufe durch.
    - Die Abfrage benötigt lange Zeit zum Abrufen der Dokumente.
- Bei allen Abfragen gibt es ein Fortsetzungstoken, das die Fortsetzung der jeweiligen Abfrage zulässt. Stellen Sie sicher, die Abfrage vollständig ablaufen zu lassen. Informieren Sie sich genauer über [Verarbeiten mehrerer Seiten mit Ergebnissen](sql-query-pagination.md#handling-multiple-pages-of-results).

## <a name="get-query-metrics"></a>Abrufen von Abfragemetriken

Beim Optimieren einer Abfrage in Azure Cosmos DB ist der erste Schritt immer das [Abrufen der Abfragemetriken](profile-sql-api-query.md) für Ihre Abfrage. Diese Metriken sind auch über das Azure-Portal verfügbar. Sobald Sie Ihre Abfrage im Data Explorer ausführen, werden die Abfragemetriken neben der Registerkarte **Ergebnisse** angezeigt:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Abrufen von Abfragemetriken" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Vergleichen Sie nach dem Abrufen der Abfragemetriken die **Anzahl der abgerufenen Dokumente** mit der **Anzahl der Ausgabedokumente** für Ihre Abfrage. Verwenden Sie diesen Vergleich zum Identifizieren der relevanten Abschnitte, auf die in diesem Artikel verwiesen werden soll.

Bei der **Anzahl der abgerufenen Dokumente** handelt es sich um die Anzahl von Dokumenten, die die Abfrage-Engine laden musste. Bei der **Anzahl der Ausgabedokumente** handelt es sich um die Anzahl von Dokumenten, die für die Ergebnisse der Abfrage benötigt wurden. Wenn die **Anzahl der abgerufenen Dokumente** deutlich höher ist als die **Anzahl der Ausgabedokumente**, konnte mindestens ein Teil der Abfrage einen Index nicht nutzen, und es musste eine Überprüfung durchgeführt werden.

Die folgenden Abschnitte sollen Ihnen helfen, die relevanten Abfrageoptimierungen für Ihr Szenario besser zu verstehen.

### <a name="querys-ru-charge-is-too-high"></a>Die Anzahl der verbrauchten RUs der Abfrage ist zu hoch

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Die Anzahl der abgerufenen Dokumente ist deutlich höher als die Anzahl der Ausgabedokumente

- [Schließen Sie die erforderlichen Pfade in die Indizierungsrichtlinie ein.](#include-necessary-paths-in-the-indexing-policy)

- [Informieren Sie sich, welche Systemfunktionen den Index verwenden.](#understand-which-system-functions-use-the-index)

- [Verbessern der Ausführung von Systemfunktionen mit Zeichenfolgen.](#improve-string-system-function-execution)

- [Informieren Sie sich, welche Aggregatabfragen den Index verwenden.](#understand-which-aggregate-queries-use-the-index)

- [Optimieren Sie Abfragen, die sowohl über einen Filter als auch über eine ORDER BY-Klausel verfügen.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimieren Sie JOIN-Ausdrücken mithilfe einer Unterabfrage.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Die Anzahl der abgerufenen Dokumente entspricht ungefähr der Anzahl der Ausgabedokumente

- [Minimieren Sie partitionsübergreifende Abfragen.](#minimize-cross-partition-queries)

- [Optimieren Sie Abfragen mit Filtern für mehrere Eigenschaften.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optimieren Sie Abfragen, die sowohl über einen Filter als auch über eine ORDER BY-Klausel verfügen.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Die Anzahl der verbrauchten RUs der Abfrage ist akzeptabel, aber die Latenz ist immer noch zu hoch

- [Erhöhen Sie die Nähe.](#improve-proximity)

- [Erhöhen Sie den bereitgestellten Durchsatz.](#increase-provisioned-throughput)

- [Erhöhen Sie den Wert für MaxConcurrency.](#increase-maxconcurrency)

- [Erhöhen Sie den Wert für MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Abfragen, bei denen die Anzahl der abgerufenen Dokumente die Anzahl der Ausgabedokumente überschreitet

 Bei der **Anzahl der abgerufenen Dokumente** handelt es sich um die Anzahl von Dokumenten, die die Abfrage-Engine laden musste. Die **Anzahl der Ausgabedokumente** ist die Anzahl von Dokumenten, die von der Abfrage zurückgegebenen wurden. Wenn die **Anzahl der abgerufenen Dokumente** deutlich höher ist als die **Anzahl der Ausgabedokumente**, konnte mindestens ein Teil der Abfrage einen Index nicht nutzen, und es musste eine Überprüfung durchgeführt werden.

Nachstehend finden Sie ein Beispiel für eine Überprüfungsabfrage, die nicht vollständig vom Index bedient wurde:

Abfrage:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Abfragemetriken:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Die **Anzahl der abgerufenen Dokumente** (60.951) ist deutlich höher als die **Anzahl der Ausgabedokumente** (7). Dies ist der Grund, weshalb diese Abfrage zu einer Dokumentüberprüfung geführt hat. In diesem Fall verwendet die Systemfunktion [UPPER()](sql-query-upper.md) keinen Index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Einschließen der erforderlichen Pfade in die Indizierungsrichtlinie

Ihre Indizierungsrichtlinie sollte alle Eigenschaften abdecken, die in `WHERE`-Klauseln, `ORDER BY`-Klauseln, `JOIN` und den meisten Systemfunktionen enthalten sind. Der in der Indizierungsrichtlinie angegebene gewünschte Pfad sollte den Eigenschaften in den JSON-Dokumenten entsprechen.

> [!NOTE]
> Bei Eigenschaften in der Azure Cosmos DB-Indizierungsrichtlinie wird die Groß-/Kleinschreibung beachtet

Wenn Sie die folgende einfache Abfrage für das [Nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)-Dataset ausführen, werden Sie eine deutlich niedrigere Anzahl von verbrauchten RUs beobachten, wenn die Eigenschaft in der `WHERE`-Klausel indiziert wird:

#### <a name="original"></a>Original

Abfrage:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Indizierungsrichtlinie:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Verbrauchte RUs:** 409,51 RUs

#### <a name="optimized"></a>Optimiert

Aktualisierte Indizierungsrichtlinie:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Verbrauchte RUs:** 2,98 RUs

Sie können der Indizierungsrichtlinie jederzeit Eigenschaften hinzufügen, ohne dass sich dies auf die Schreib- oder Leseverfügbarkeit auswirkt. Sie können den [Fortschritt der Indextransformation nachverfolgen](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Ermitteln der Systemfunktionen, die den Index verwenden

Die meisten Systemfunktionen verwenden Indizes. Es folgt eine Liste einiger allgemeiner Zeichenfolgenfunktionen, die Indizes verwenden:

- StartsWith
- Enthält
- RegexMatch
- Left
- Teilzeichenfolge, aber nur, wenn die erste num_expr den Wert „0“ hat

Nachstehend sind einige allgemeine Systemfunktionen aufgeführt, die den Index nicht verwenden und jedes Dokument laden müssen, wenn sie in einer `WHERE`-Klausel verwendet werden:

| **Systemfunktion**                     | **Vorschläge für Optimierungen**             |
| --------------------------------------- |------------------------------------------------------------ |
| Upper/Lower                         | Normalisieren Sie die Groß-/Kleinschreibung beim Einfügen, statt die Systemfunktion zum Normalisieren der Daten für Vergleiche zu verwenden. Eine Abfrage wie ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` wird zu ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Berechnen Sie die aktuelle Zeit vor der Abfrageausführung, und verwenden Sie diesen Zeichenfolgenwert in der `WHERE`-Klausel. |
| Mathematische Funktionen (Nicht-Aggregate) | Wenn Sie in der Abfrage einen Wert häufig berechnen müssen, sollten Sie diesen Wert als Eigenschaft in Ihrem JSON-Dokument speichern. |

Wenn er in der `SELECT`-Klausel verwendet wird, wirken sich ineffiziente Systemfunktionen nicht auf die Verwendung von Indizes durch Abfragen aus.

### <a name="improve-string-system-function-execution"></a>Verbessern der Ausführung von Systemfunktionen mit Zeichenfolgen

Bei einigen Systemfunktionen, die Indizes verwenden, können Sie die Abfrageausführung verbessern, indem Sie der Abfrage eine `ORDER BY`-Klausel hinzufügen. 

Genauer gesagt kann jede Systemfunktion, deren RU-Gebühr zunimmt, wenn die Kardinalität der Eigenschaft ansteigt, von einer `ORDER BY`-Klausel in der Abfrage profitieren. Diese Abfragen führen einen Indexscan durch, und durch das Sortieren der Abfrageergebnisse kann die Abfrage effizienter werden.

Diese Optimierung kann die Ausführung für die folgenden Systemfunktionen verbessern:

- StartsWith (mit „case-insensitive = true“)
- StringEquals (mit „case-insensitive = true“)
- Enthält
- RegexMatch
- EndsWith

Sehen Sie sich beispielsweise die folgende Abfrage mit `CONTAINS` an. `CONTAINS` verwendet Indizes. Manchmal ist jedoch nach dem Hinzufügen des entsprechenden Indexes weiterhin eine sehr hohe RU-Gebühr zu beobachten, wenn Sie die Abfrage unten ausführen.

Ursprüngliche Abfrage:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Sie können die Abfrageausführung verbessern, indem Sie `ORDER BY` hinzufügen:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Die gleiche Optimierung kann bei Abfragen mit zusätzlichen Filtern helfen. In diesem Fall empfiehlt es sich, der `ORDER BY`-Klausel auch Eigenschaften mit Gleichheitsfiltern hinzuzufügen.

Ursprüngliche Abfrage:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Sie können die Abfrageausführung verbessern, indem Sie `ORDER BY` und [einen zusammengesetzten Index](index-policy.md#composite-indexes) für (c.name, c.town) hinzufügen:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Verstehen, welche Aggregatabfragen den Index verwenden

In den meisten Fällen wird der Index von Aggregatsystemfunktionen in Azure Cosmos DB verwendet. Je nach den Filtern oder zusätzlichen Klauseln in einer Aggregatabfrage muss die Abfrage-Engine jedoch möglicherweise eine große Anzahl von Dokumenten laden. Normalerweise wendet die Abfrage-Engine zuerst Gleichheits-und Bereichsfilter an. Nach Anwendung dieser Filter kann die Abfrage-Engine zusätzliche Filter auswerten und neu sortieren, um die übrigen Dokumente bei Bedarf zum Berechnen des Aggregats zu laden.

So ist bei diesen zwei Beispielabfragen die Abfrage mit einem Gleichheits- und `CONTAINS`-Systemfunktionsfilter generell effizienter als eine Abfrage mit nur einem `CONTAINS`-Systemfunktionsfilter. Der Grund: Der Gleichheitsfilter wird zuerst angewendet und verwendet den Index, bevor Dokumente für den teureren `CONTAINS`-Filter geladen werden müssen.

Abfrage mit nur `CONTAINS`-Filter – höhere RU-Gebühr:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Abfrage sowohl mit Gleichheitsfilter als auch `CONTAINS`-Filter – niedrigere RU-Gebühr:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Nachfolgend sind zusätzliche Beispiele für Aggregatabfragen aufgeführt, die den Index nicht vollständig verwenden:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Abfragen mit Systemfunktionen, die den Index nicht verwenden

Sie sollten sich auf der relevanten [Seite für die Systemfunktion](sql-query-system-functions.md) informieren, ob sie den Index verwendet.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Aggregatabfragen mit benutzerdefinierten Funktionen (User-Defined Functions, UDFs)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Abfragen mit GROUP BY

Die RU-Gebühr von Abfragen mit `GROUP BY` steigt, wenn die Multiplizität der Eigenschaften in der `GROUP BY`-Klausel zunimmt. In der unten stehenden Abfrage steigt die RU-Gebühr der Abfrage beispielsweise, wenn die Anzahl von eindeutigen Beschreibungen zunimmt.

Die RU-Gebühr einer Aggregatfunktion mit einer `GROUP BY`-Klausel ist höher als die RU-Gebühr einer reinen Aggregatfunktion. Weil die Abfrage-Engine in diesem Beispiel jedes Dokument laden muss, das mit dem Filter `c.foodGroup = "Sausages and Luncheon Meats"` übereinstimmt, wird die RU-Gebühr erwartungsgemäß hoch sein.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Wenn Sie vorhaben, dieselben Aggregatabfragen häufig auszuführen, ist es möglicherweise effizienter, eine materialisierte Sicht in Echtzeit mit dem [Azure Cosmos DB-Änderungsfeed](change-feed.md) zu erstellen, als einzelne Abfragen auszuführen.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Optimieren Sie Abfragen, die sowohl über einen Filter als auch über eine ORDER BY-Klausel verfügen

Obwohl Abfragen mit einem Filter und einer `ORDER BY`-Klausel normalerweise einen Bereichsindex verwenden, sind sie effizienter, wenn sie von einem zusammengesetzten Index bedient werden können. Zusätzlich zum Ändern der Indizierungsrichtlinie sollten Sie der `ORDER BY`-Klausel alle Eigenschaften im zusammengesetzten Index hinzufügen. Durch diese Änderung der Abfrage wird sichergestellt, dass der zusammengesetzte Index verwendet wird.  Sie können die Auswirkung beobachten, indem Sie eine Abfrage für das [Nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)-Dataset ausführen:

#### <a name="original"></a>Original

Abfrage:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Indizierungsrichtlinie:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Verbrauchte RUs:** 44,28 RUs

#### <a name="optimized"></a>Optimiert

Aktualisierte Abfrage (enthält beide Eigenschaften in der `ORDER BY`-Klausel):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Aktualisierte Indizierungsrichtlinie:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Verbrauchte RUs:** 8,86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimieren von JOIN-Ausdrücken mithilfe einer Unterabfrage

Mehrwertige Unterabfragen können `JOIN`-Ausdrücke optimieren, indem Prädikate nach jedem select-many-Ausdruck statt nach allen Kreuzprodukten in der `WHERE`-Klausel gepusht werden.

Angenommen, die folgende Abfrage wird ausgeführt:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Verbrauchte RUs:** 167,62 RUs

Bei dieser Abfrage ermittelt der Index alle Dokumente, die ein Tag mit dem Namen `infant formula`, `nutritionValue` größer 0 und `amount` größer 1 aufweisen. Der `JOIN`-Ausdruck ermittelt hier das Kreuzprodukt aller Elemente der tags-, nutrients- und servings-Arrays für jedes übereinstimmende Dokument, bevor ein Filter angewendet wird. Die `WHERE`-Klausel wendet dann das Filterprädikat auf jedes `<c, t, n, s>`-Tupel an.

Wenn z. B. ein übereinstimmendes Dokument 10 Elemente in jedem der drei Arrays hat, wird auf 1 × 10 × 10 × 10 (d. h. 1.000) Tupel erweitert. Die Verwendung von Unterabfragen kann hier helfen, indem verknüpfte Arrayelemente herausgefiltert werden, bevor der Joinvorgang mit dem nächsten Ausdruck ausgeführt wird.

Diese Abfrage entspricht der vorherigen, verwendet aber Unterabfragen:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Verbrauchte RUs:** 22,17 RUs

Es wird davon ausgegangen, dass nur ein Element im tags-Array dem Filter entspricht, und es gibt fünf Elemente sowohl für das nutrients- als auch das servings-Array. Die `JOIN`-Ausdrücke werden im Gegensatz zu 1.000 Elementen in der ersten Abfrage auf 1 × 1 × 5 × 5 = 25 Elemente erweitert.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Abfragen, bei denen die Anzahl der abgerufenen Dokumente der Anzahl der Ausgabedokumente entspricht

Wenn die **Anzahl der abgerufenen Dokumente** ungefähr der **Anzahl der Ausgabedokumente** entspricht, musste die Abfrage-Engine nicht viele unnötige Dokumente überprüfen. Bei vielen Abfragen, z. B. bei Abfragen, die das `TOP`-Schlüsselwort verwenden, kann die **Anzahl der abgerufenen Dokumente** die **Anzahl der Ausgabedokumente** um 1 überschreiten. Sie müssen sich darum nicht kümmern.

### <a name="minimize-cross-partition-queries"></a>Minimieren Sie partitionsübergreifende Abfragen

Azure Cosmos DB verwendet die [Partitionierung](partitioning-overview.md), um einzelne Container zu skalieren, wenn die Anzahl von Anforderungseinheiten und die Datenspeicheranforderungen steigen. Jede physische Partition verfügt über einen separaten und unabhängigen Index. Wenn Ihre Abfrage einen Gleichheitsfilter enthält, der mit dem Partitionsschlüssel Ihres Containers übereinstimmt, müssen Sie nur den Index der relevanten Partition überprüfen. Durch diese Optimierung wird die Gesamtanzahl der für die Abfrage erforderlichen RUs reduziert.

Im Fall einer großen Anzahl von bereitgestellten Anforderungseinheiten (über 30.000) oder einer großen Menge an gespeicherten Daten (über ca. 100 GB) verfügen Sie wahrscheinlich über einen ausreichend großen Container, um eine deutliche Reduzierung der Anzahl der verbrauchten RUs für die Abfrage zu erreichen.

Wenn Sie beispielsweise einen Container mit dem Partitionsschlüssel „foodGroup“ erstellen, müssen die folgenden Abfragen nur eine einzige physische Partition überprüfen:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Abfragen, die über einen `IN`-Filter mit dem Partitionsschlüssel verfügen, überprüfen lediglich die relevanten Partitionen, sodass es nicht zu einer Auffächerung kommt:

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Abfragen, die Bereichsfilter für den Partitionsschlüssel oder keine Filter für den Partitionsschlüssel enthalten, müssen „aufgefächert“ werden und den Index jeder physischen Partition überprüfen:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimieren von Abfragen mit Filtern für mehrere Eigenschaften

Obwohl Abfragen mit Filtern für mehrere Eigenschaften normalerweise einen Bereichsindex verwenden, sind sie effizienter, wenn sie von einem zusammengesetzten Index bedient werden können. Bei kleinen Datenmengen hat diese Optimierung keine spürbaren Auswirkungen. Bei großen Datenmengen kann sie sich jedoch als hilfreich erweisen. Sie können höchstens einen Ungleichheitsfilter pro zusammengesetztem Index optimieren. Wenn Ihre Abfrage mehrere Ungleichheitsfilter enthält, wählen Sie einen davon aus, der den zusammengesetzten Index verwendet. Der Rest verwendet weiterhin Bereichsindizes. Der Ungleichheitsfilter muss im zusammengesetzten Index zuletzt definiert werden. Erfahren Sie mehr über [zusammengesetzte Indizes](index-policy.md#composite-indexes).

Nachstehend finden Sie einige Beispiele für Abfragen, die mit einem zusammengesetzten Index optimiert werden könnten:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Hier ist der entsprechende zusammengesetzte Index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimierungen zum Verringern der Abfragelatenz

In vielen Fällen ist die Anzahl der verbrauchten RUs akzeptabel, aber die Abfragelatenz immer noch zu hoch. Die folgenden Abschnitte bieten eine Übersicht über Tipps zum Verringern der Abfragelatenz. Wenn Sie dieselbe Abfrage mehrmals für dasselbe Dataset ausführen, wird in der Regel jedes Mal die gleiche Anzahl von RUs verbraucht. Die Abfragelatenz kann zwischen den einzelnen Abfrageausführungen variieren.

### <a name="improve-proximity"></a>Verbessern der Nähe

Abfragen, die in einer anderen Region als das Azure Cosmos DB-Konto ausgeführt werden, haben eine höhere Latenz als in derselben Region ausgeführte Abfragen. Wenn Sie beispielsweise Code auf Ihrem Desktopcomputer ausgeführt haben, sollten Sie eine um zehn oder Hunderte (oder mehr) Millisekunden höhere Latenz erwarten, als wenn die Abfrage auf einem virtuellen Computer in derselben Azure-Region wie Azure Cosmos DB ausgeführt wurde. Es ist einfach, [Daten in Azure Cosmos DB global zu verteilen](distribute-data-globally.md), um sicherzustellen, dass sich Ihre Daten näher an Ihrer App befinden.

### <a name="increase-provisioned-throughput"></a>Erhöhen des bereitgestellten Durchsatzes

In Azure Cosmos DB wird der bereitgestellte Durchsatz in Anforderungseinheiten (Request Units, RUs) gemessen. Angenommen, Sie verfügen über eine Abfrage, die 5 RUs an Durchsatz verbraucht. Wenn Sie z. B. 1.000 RUs bereitstellen, könnten Sie diese Abfrage 200-mal pro Sekunde ausführen. Wenn Sie versuchen würden, die Abfrage auszuführen, wenn nicht genug Durchsatz verfügbar ist, würde Azure Cosmos DB einen HTTP-Fehler mit dem Statuscode 429 zurückgeben. Alle aktuellen Core (SQL)-API SDKs versuchen, diese Abfrage nach einer kurzen Wartezeit automatisch erneut auszuführen. Gedrosselte Anforderungen nehmen mehr Zeit in Anspruch, sodass eine Erhöhung des bereitgestellten Durchsatzes die Abfragelatenz verbessern kann. Im Azure-Portal können Sie auf dem Blatt **Metriken** die [Gesamtanzahl der (gedrosselten) Anforderungen](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) anzeigen.

### <a name="increase-maxconcurrency"></a>Erhöhen des Werts für MaxConcurrency

Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Wenn Sie MaxConcurrency also auf die Anzahl von Partitionen festlegen, ist die Wahrscheinlichkeit am höchsten, dass die bestmögliche Leistung für die Abfrage erzielt wird, sofern alle anderen Systembedingungen unverändert bleiben. Wenn Sie die Anzahl der Partitionen nicht kennen, können Sie MaxConcurrency (oder bei älteren SDK-Versionen MaxDegreesOfParallelism) auf eine hohe Zahl festlegen. Das System wählt den Mindestwert (Anzahl der Partitionen, vom Benutzer bereitgestellte Eingabe) als maximalen Grad an Parallelität aus.

### <a name="increase-maxbuffereditemcount"></a>Erhöhen des Werts für MaxBufferedItemCount

Abfragen sind so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch der Ergebnisse verarbeitet. Ein Vorababruf führt zu einer Verbesserung der allgemeinen Latenz einer Abfrage. Durch Festlegen von MaxBufferedItemCount wird die Anzahl von vorab abgerufenen Ergebnissen begrenzt. Wenn Sie diesen Wert auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Anzahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten. Wenn Sie diesen Wert auf „-1“ festlegen, kann das System die Anzahl der zu puffernden Elemente automatisch festlegen.

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten u. a. Informationen dazu, wie Sie RUs pro Abfrage messen und Ausführungsstatistiken zum Optimieren Ihrer Abfragen erhalten:

* [Abrufen von SQL-Abfrageausführungsmetriken und Analysieren der Abfrageleistung mit dem .NET SDK](profile-sql-api-query.md)
* [Optimieren der Abfrageleistung mit Azure Cosmos DB](./sql-api-query-metrics.md)
* [Tipps zur Leistungssteigerung für das .NET SDK](performance-tips.md)
* [Leistungstipps für das Java v4 SDK](performance-tips-java-sdk-v4-sql.md)