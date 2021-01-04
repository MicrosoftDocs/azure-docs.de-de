---
title: Behandeln von Abfrageproblemen bei Verwendung der Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie Probleme mit Abfragen der API für MongoDB in Azure Cosmos DB identifizieren, diagnostizieren und beheben.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 88ef081c75a64b5cb7517ba6994834b3a64a0e6f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340888"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Behandeln von Abfrageproblemen bei Verwendung der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In diesem Artikel werden Sie durch einen allgemeinen empfohlenen Ansatz für die Problembehandlung von Abfragen in Azure Cosmos DB geführt. Sie sollten die in diesem Artikel beschriebenen Schritte zwar nicht als umfassende Verteidigung gegen mögliche Abfrageprobleme ansehen, hier sind jedoch die gängigsten Leistungstipps zusammengefasst. Sie können diesen Artikel als Ausgangspunkt für die Problembehandlung von langsamen oder aufwendigen Abfragen in der Azure Cosmos DB-API für MongoDB verwenden. Wenn Sie die Azure Cosmos DB-Core-API (SQL) verwenden, finden Sie weitere Informationen im Artikel [Behandeln von Problemen bei Verwendung von Azure Cosmos DB](troubleshoot-query-performance.md).

Abfrageoptimierungen in Azure Cosmos DB lassen sich grob in folgende Kategorien unterteilen:

- Optimierungen, die die Anzahl der verbrauchten Anforderungseinheiten (Request Units, RUs) der Abfrage verringern
- Optimierungen ausschließlich zum Verringern der Latenz

Durch Reduzieren der Anzahl der verbrauchten RUs einer Abfrage können Sie in der Regel auch die Wartezeit verringern.

Dieser Artikel enthält Beispiele, die Sie mit dem [Dataset „Nutrition“](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) neu erstellen können.

> [!NOTE] 
> In diesem Artikel wird davon ausgegangen, dass Sie die Version 3.6 der Azure Cosmos DB-API für MongoDB verwenden. Einige Abfragen, die in Version 3.2 schlecht funktionieren, wurden in Version 3.6 erheblich verbessert. Führen Sie ein Upgrade auf die Version 3.6 durch, indem Sie eine [Supportanfrage](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) einreichen.

## <a name="use-explain-command-to-get-metrics"></a>Verwenden des $explain-Befehls zum Abrufen von Metriken

Beim Optimieren einer Abfrage in Azure Cosmos DB ist der erste Schritt immer das [Abrufen der verbrauchten RU](find-request-unit-charge-mongodb.md) für Ihre Abfrage. Als grobe Richtlinie empfiehlt es sich, nach Möglichkeiten zu suchen, die verbrauchten RU für Anfragen mit mehr als 50 verbrauchten RU zu verringern. 

Zusätzlich zum Abrufen der verbrauchten RU sollten Sie den `$explain` -Befehl verwenden, um die Metriken für die Abfrage- und Indexnutzung abzurufen. Im folgenden Beispiel wird eine Abfrage ausgeführt und der `$explain`-Befehl verwendet, um Abfrage- und Indexnutzungsmetriken anzuzeigen:

**$explain-Befehl:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Ausgabe:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

Die Ausgabe des `$explain`-Befehls ist langwierig und enthält ausführliche Informationen zur Abfrageausführung. Im Allgemeinen gibt es jedoch einige Abschnitte, in denen Sie sich auf die Optimierung der Abfrageleistung konzentrieren sollten:

| Metrik | Beschreibung | 
| ------ | ----------- |
| `timeInclusiveMS` | Wartezeit der Back-End-Abfrage |
| `pathsIndexed` | Zeigt die von der Abfrage verwendeten Indizes an. | 
| `pathsNotIndexed` | Zeigt Indizes an, die von der Abfrage verwendet werden können, falls verfügbar. | 
| `shardInformation` | Zusammenfassung der Abfrageleistung für eine bestimmte [physische Partition](./partitioning-overview.md#physical-partitions) | 
| `retrievedDocumentCount` | Anzahl der von der Abfrage-Engine geladenen Dokumente | 
| `outputDocumentCount` | Anzahl der in den Abfrageergebnissen zurückgegebenen Dokumente | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Geschätzte zusätzliche Abfragewartezeit aufgrund der Ratenbegrenzung | 

Vergleichen Sie nach dem Abrufen der Abfragemetriken die `retrievedDocumentCount` mit der `outputDocumentCount` für Ihre Abfrage. Verwenden Sie diesen Vergleich zum Identifizieren der relevanten Abschnitte, auf die in diesem Artikel verwiesen werden soll. Bei `retrievedDocumentCount` handelt es sich um die Anzahl von Dokumenten, die die Abfrage-Engine laden soll. Bei der `outputDocumentCount` handelt es sich um die Anzahl von Dokumenten, die für die Ergebnisse der Abfrage benötigt wurden. Wenn `retrievedDocumentCount` deutlich höher ist als `outputDocumentCount`, konnte mindestens ein Teil der Abfrage einen Index nicht nutzen, und es musste eine Überprüfung durchgeführt werden.

Die folgenden Abschnitte sollen Ihnen helfen, die relevanten Abfrageoptimierungen für Ihr Szenario besser zu verstehen.

### <a name="querys-ru-charge-is-too-high"></a>Die Anzahl der verbrauchten RUs der Abfrage ist zu hoch

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Die Anzahl der abgerufenen Dokumente ist deutlich höher als die Anzahl der Ausgabedokumente

- [Nur die erforderlichen Indizes einschließen.](#include-necessary-indexes)

- [Verstehen, welche Aggregationsvorgänge den Index verwenden.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Die Anzahl der abgerufenen Dokumente entspricht ungefähr der Anzahl der Ausgabedokumente

- [Minimieren Sie partitionsübergreifende Abfragen.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Die Anzahl der verbrauchten RUs der Abfrage ist akzeptabel, aber die Latenz ist immer noch zu hoch

- [Erhöhen Sie die Nähe.](#improve-proximity)

- [Erhöhen Sie den bereitgestellten Durchsatz.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Abfragen, bei denen die Anzahl der abgerufenen Dokumente die Anzahl der Ausgabedokumente überschreitet

 Bei der `retrievedDocumentCount` handelt es sich um die Anzahl von Dokumenten, die die Abfrage-Engine laden musste. Die `outputDocumentCount` ist die Anzahl von Dokumenten, die von der Abfrage zurückgegebenen wurden. Wenn die `retrievedDocumentCount` deutlich höher ist als die `outputDocumentCount`, konnte mindestens ein Teil der Abfrage einen Index nicht nutzen, und es musste eine Überprüfung durchgeführt werden.

Nachstehend finden Sie ein Beispiel für eine Überprüfungsabfrage, die nicht vollständig vom Index bedient wurde:

**$explain-Befehl:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Ausgabe:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount` (8618) ist deutlich höher als `outputDocumentCount` (1), was impliziert, dass diese Abfrage eine Dokumentenüberprüfung erforderte. 

### <a name="include-necessary-indexes"></a>Nur die erforderlichen Indizes einschließen

Sie sollten das `pathsNotIndexed`-Array überprüfen und diese Indizes hinzufügen. In diesem Beispiel sollten die Pfade `foodGroup` und `description` indiziert werden.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Die bewährten Methoden der Indizierung in der Azure Cosmos DB-API für MongoDB unterscheiden sich von MongoDB. In der API für MongoDB von Azure Cosmos DB werden zusammengesetzte Indizes nur in Abfragen verwendet, die effizient nach verschiedenen Eigenschaften sortieren müssen. Wenn Sie Abfragen mit Filtern für mehrere Eigenschaften durchführen, sollten Sie für jede dieser Eigenschaften einzelne Feldindizes erstellen. Abfrageprädikate können mehrere einzelne Feldindizes verwenden.

[Platzhalterindizes](mongodb-indexing.md#wildcard-indexes) können die Indizierung vereinfachen. Anders als in MongoDB können von Platzhalterindizes mehrere Felder in Abfrageprädikaten unterstützt werden. Für die Abfrageleistung ist es unerheblich, ob Sie einen einzelnen Platzhalterindex verwenden oder einen separaten Index für jede Eigenschaft erstellen. Das Hinzufügen eines Platzhalterindexes für alle Eigenschaften ist die einfachste Möglichkeit, um alle Abfragen zu optimieren.

Sie können jederzeit neue Indizes hinzufügen, ohne dass sich dies auf die Schreib- oder Leseverfügbarkeit auswirkt. Sie können den [Fortschritt der Indextransformation nachverfolgen](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Verstehen, welche Aggregationsvorgänge den Index verwenden

In den meisten Fällen werden bei Aggregationsvorgängen in der API für MongoDB von Azure Cosmos DB partielle Indizes verwendet. Normalerweise wendet die Abfrage-Engine zuerst Gleichheits- und Bereichsfilter an und verwendet dann Indizes. Nach Anwendung dieser Filter kann die Abfrage-Engine zusätzliche Filter auswerten und neu sortieren, um die übrigen Dokumente bei Bedarf zum Berechnen des Aggregats zu laden. 

Hier sehen Sie ein Beispiel:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

In diesem Fall können Indizes die `$match`-Phase optimieren. Durch das Hinzufügen eines Index für `foodGroup` wird die Abfrageleistung erheblich verbessert. Wie in MongoDB sollten Sie `$match` so früh wie möglich in der Aggregationspipeline platzieren, damit die Indizes optimal genutzt werden können.

In der Azure Cosmos DB-API für MongoDB werden Indizes nicht für die eigentliche Aggregation verwendet, die in diesem Fall `$max` ist. Durch das Hinzufügen eines Indexes für `version` wird die Abfrageleistung nicht verbessert.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Abfragen, bei denen die Anzahl der abgerufenen Dokumente der Anzahl der Ausgabedokumente entspricht

Wenn die `retrievedDocumentCount` ungefähr der `outputDocumentCount` entspricht, musste die Abfrage-Engine nicht viele unnötige Dokumente überprüfen.

### <a name="minimize-cross-partition-queries"></a>Minimieren Sie partitionsübergreifende Abfragen

Azure Cosmos DB verwendet die [Partitionierung](partitioning-overview.md), um einzelne Container zu skalieren, wenn die Anzahl von Anforderungseinheiten und die Datenspeicheranforderungen steigen. Jede physische Partition verfügt über einen separaten und unabhängigen Index. Wenn Ihre Abfrage einen Gleichheitsfilter enthält, der mit dem Partitionsschlüssel Ihres Containers übereinstimmt, müssen Sie nur den Index der relevanten Partition überprüfen. Durch diese Optimierung wird die Gesamtanzahl der für die Abfrage erforderlichen RUs reduziert. [Erfahren Sie mehr über die Unterschiede zwischen Abfragen innerhalb einer Partition und zwischen Partitionen](how-to-query-container.md).

Im Fall einer großen Anzahl von bereitgestellten Anforderungseinheiten (über 30.000) oder einer großen Menge an gespeicherten Daten (über ca. 100 GB) verfügen Sie wahrscheinlich über einen ausreichend großen Container, um eine deutliche Reduzierung der Anzahl der verbrauchten RUs für die Abfrage zu erreichen. 

Anhand des `shardInformation`-Arrays können Sie die Abfragemetriken für jede einzelne physische Partition nachvollziehen. Die Anzahl der eindeutigen `shardKeyRangeId`-Werte ist die Anzahl der physischen Partitionen, auf denen die Abfrage ausgeführt werden musste. In diesem Beispiel wurde die Abfrage auf vier physischen Partitionen ausgeführt. Dabei ist es wichtig zu verstehen, dass die Ausführung völlig unabhängig von der Indexnutzung ist. Anders ausgedrückt: Partitionsübergreifende Abfragen können weiterhin Indizes verwenden.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimierungen zum Verringern der Abfragelatenz

In vielen Fällen ist die Anzahl der verbrauchten RUs akzeptabel, aber die Abfragelatenz immer noch zu hoch. Die folgenden Abschnitte bieten eine Übersicht über Tipps zum Verringern der Abfragelatenz. Wenn Sie dieselbe Abfrage mehrmals für dasselbe Dataset ausführen, wird in der Regel jedes Mal die gleiche Anzahl von RUs verbraucht. Die Abfragelatenz kann zwischen den einzelnen Abfrageausführungen variieren.

### <a name="improve-proximity"></a>Verbessern der Nähe

Abfragen, die in einer anderen Region als das Azure Cosmos DB-Konto ausgeführt werden, haben eine höhere Latenz als in derselben Region ausgeführte Abfragen. Wenn Sie beispielsweise Code auf Ihrem Desktopcomputer ausgeführt haben, sollten Sie eine um zehn oder Hunderte (oder mehr) Millisekunden höhere Latenz erwarten, als wenn die Abfrage auf einem virtuellen Computer in derselben Azure-Region wie Azure Cosmos DB ausgeführt wurde. Es ist einfach, [Daten in Azure Cosmos DB global zu verteilen](tutorial-global-distribution-mongodb.md), um sicherzustellen, dass sich Ihre Daten näher an Ihrer App befinden.

### <a name="increase-provisioned-throughput"></a>Erhöhen des bereitgestellten Durchsatzes

In Azure Cosmos DB wird der bereitgestellte Durchsatz in Anforderungseinheiten (Request Units, RUs) gemessen. Angenommen, Sie verfügen über eine Abfrage, die 5 RUs an Durchsatz verbraucht. Wenn Sie z. B. 1.000 RUs bereitstellen, könnten Sie diese Abfrage 200-mal pro Sekunde ausführen. Wenn Sie versuchen würden, die Abfrage auszuführen, wenn nicht genug Durchsatz verfügbar ist, würde Azure Cosmos DB eine Ratenbegrenzung der Anforderungen durchführen. Die Azure Cosmos DB-API für MongoDB wird diese Abfrage nach einer kurzen Wartezeit automatisch erneut ausführen. Gedrosselte Anforderungen nehmen mehr Zeit in Anspruch, sodass eine Erhöhung des bereitgestellten Durchsatzes die Abfragelatenz verbessern kann.

Der Wert `estimatedDelayFromRateLimitingInMilliseconds` bietet einen Eindruck von den potenziellen Wartezeitvorteilen, wenn Sie den Durchsatz erhöhen.

## <a name="next-steps"></a>Nächste Schritte

* [Beheben von Problemen mit der Abfrageleistung (SQL-API)](troubleshoot-query-performance.md)
* [Verwalten der Indizierung in der Azure Cosmos DB-API für MongoDB](mongodb-indexing.md)