---
title: Optimieren von Anforderungseinheiten und Kosten zum Ausführen von Abfragen in Azure Cosmos DB
description: Erfahren Sie, wie Sie die berechneten Anforderungseinheiten (RUs) für eine Abfrage auswerten und die Abfrage in Hinblick auf Leistung und Kosten optimieren können.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 2d1ac054abf4bb8228bdb5cc20d79cb751af7a33
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967442"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimieren der Abfragekosten in Azure Cosmos DB

Azure Cosmos DB bietet eine umfangreiche Sammlung von Datenbankvorgängen einschließlich relationalen und hierarchischen Abfragen, die für die Elemente in einem Container ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstatt sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für die verschiedenen Datenbankvorgänge zum Verarbeiten einer Anforderung erforderlich sind. In diesem Artikel wird beschrieben, wie Sie die berechneten Anforderungseinheiten für eine Abfrage auswerten und die Abfrage in Hinblick auf Leistung und Kosten optimieren. 

Die Abfragen in Azure Cosmos DB werden in der Regel in Bezug auf den Durchsatz (absteigend von den schnellsten/effizientesten zu den langsameren/weniger effizienten Abfragen) wie folgt sortiert:  

* GET-Operation für einen einzelnen Partitions- und Elementschlüssel

* Abfrage mit einer Filterklausel in einem einzelnen Partitionsschlüssel

* Abfrage ohne eine Gleichheits- oder Bereichsfilterklausel in einer Eigenschaft

* Abfrage ohne Filter

Abfragen, die Daten von einer oder mehreren Partitionen lesen, verursachen eine höhere Latenz und verbrauchen eine höhere Anzahl von Anforderungseinheiten. Da jede Partition über eine automatische Indizierung für alle Eigenschaften verfügt, kann die Abfrage effizient über den Index verarbeitet werden. Mithilfe der Optionen für die Parallelverarbeitung können Sie partitionsübergreifende Abfragen schneller machen. Weitere Informationen zur Partitionierung und zu Partitionsschlüsseln finden Sie unter [Partitionieren in Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Auswerten der berechneten Anforderungseinheiten für eine Abfrage

Nachdem Sie einige Daten in Ihren Azure-Cosmos-Containern gespeichert haben, können Sie den Daten-Explorer im Azure-Portal verwenden, um Ihre Abfragen zu erstellen und auszuführen. Sie können auch mithilfe des Daten-Explorers die Kosten für die Abfragen abrufen. Diese Methode bietet Ihnen einen Überblick über die tatsächlichen Gebühren, die bei typischen Abfragen und Vorgängen anfallen, die Ihr System unterstützt.

Sie können die Kosten für die Abfragen auch programmgesteuert mithilfe der SDKs abrufen. Zum Messen des Aufwands für einen Vorgang wie Erstellen, Aktualisieren oder Löschen überprüfen Sie den Header `x-ms-request-charge`, wenn Sie die REST-API verwenden. Bei Verwendung des .NET- oder Java-SDKs ist `RequestCharge` die entsprechende Eigenschaft zum Abrufen der Anforderungsgebühr. Diese Eigenschaft ist in ResourceResponse oder FeedResponse zu finden.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktoren mit Einfluss auf die berechneten Anforderungseinheiten für eine Abfrage

Die Anforderungseinheiten für Abfragen hängen von einer Reihe Faktoren ab, zum Beispiel von der Anzahl der geladenen/zurückgegebenen Azure Cosmos-Elemente, von der Anzahl der Suchvorgänge im Index, die Zeit zum Kompilieren der Abfrage usw. Azure Cosmos DB garantiert, dass die gleiche Abfrage, wenn sie für die gleichen Daten ausgeführt wird, selbst bei wiederholter Ausführung immer die gleiche Anzahl von Anforderungseinheiten verbraucht. Das Abfrageprofil, das Abfrageausführungsmetriken verwendet, bietet Ihnen einen guten Überblick darüber, wie die Anforderungseinheiten verbraucht werden.  

In einigen Fällen sehen Sie möglicherweise eine Sequenz von 200 und 429 Antworten und variable Anforderungseinheiten in einer ausgelagerten Ausführung von Abfragen, was daran liegt, dass Abfragen auf Basis der verfügbaren Anforderungseinheiten so schnell wie möglich ausgeführt werden. Sie sehen möglicherweise auch, dass die Ausführung einer Abfrage in mehrere Seiten/Roundtrips zwischen Server und Client aufgeteilt werden. 10.000 Elemente können beispielsweise in Form mehrerer Seiten zurückgegeben werden, die jeweils anhand des für diese Seite ausgeführten Rechenaufwands berechnet werden. Wenn Sie die Summen dieser Seiten addieren, erhalten Sie die gleiche Anzahl an Anforderungseinheiten, die Sie für die gesamte Abfrage erhalten würden.  

## <a name="metrics-for-troubleshooting"></a>Metriken zur Problembehandlung

Die von den Abfragen verbrauchte Leistung und der Durchsatz hängen hauptsächlich vom Funktionsrumpf der benutzerdefinierten Funktionen (UDFs) ab. Wie lange die Ausführung der Abfrage in der benutzerdefinierten Funktion (UDF) dauert und wie viel Anforderungseinheiten verbraucht werden, können Sie am einfachsten herausfinden, indem Sie die Abfragemetriken aktivieren. Wenn Sie das .NET-SDK verwenden, finden Sie hier einige Beispiele für Abfragemetriken, die vom SDK zurückgegeben werden:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Bewährte Methoden für die Kostenoptimierung von Abfragen 

Berücksichtigen Sie bei der Kostenoptimierung von Abfragen die folgenden bewährten Methoden:

* **Zusammenstellen von mehreren Entitätstypen** 

   Versuchen Sie, mehrere Entitätstypen in einem oder einigen wenigen Containern zusammenzustellen. Diese Methode bietet nicht nur Vorteile aus preislicher Sicht, sondern wirkt sich auch positiv auf die Ausführung von Abfragen und Transaktionen aus. Abfragen werden auf einen einzelnen Container begrenzt, und atomische Transaktionen für mehrere Datensätze über gespeicherte Prozeduren/Trigger hinweg werden auf einen Partitionsschlüssel in einem einzelnen Container begrenzt. Durch das Zusammenstellen von Entitäten im selben Container können Sie die Anzahl der Netzwerk-Roundtrips zum Auflösen von Beziehungen zwischen den Datensätzen reduzieren. Dadurch erhöht sich die End-to-End-Leistung und ermöglicht atomische Transaktionen über mehrere Datensätze für ein größeres Dataset. In Folge sinken die Kosten. Wenn in Ihrem Szenario die Zusammenstellung mehrerer Entitätstypen in einem oder einigen wenigen Containern schwierig ist, liegt dies in der Regel daran, dass Sie eine vorhandene Anwendung migrieren und keine Codeänderungen vornehmen möchten. In diesem Fall sollten Sie die Bereitstellung des Durchsatzes auf Datenbankebene in Betracht ziehen.  

* **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

   Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten (RUs) für einen Vorgang verbraucht werden, zum Beispiel die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets. All diese Faktoren beeinflussen die Kosten von Abfragevorgängen. 

   Die im Header der Anforderung zurückgegebene Anforderungsgebühr gibt die Kosten für eine bestimmte Abfrage an. Wenn eine Abfrage beispielsweise 1000 Elemente mit einer Größe von 1 KB zurückgibt, belaufen sich die Kosten für den Vorgang auf 1000. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie im Artikel zu den [Anforderungseinheiten](request-units.md) sowie im Rechner für Anforderungseinheiten. 

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Preismodell für Azure Cosmos DB](how-pricing-works.md)
* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
* Weitere Informationen unter [Reservierte Azure Cosmos DB-Kapazität](cosmos-db-reserved-capacity.md)

