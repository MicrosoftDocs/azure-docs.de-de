---
title: Optimieren der Kosten für Anforderungen in Azure Cosmos DB
description: In diesem Artikel wird erläutert, wie Sie die Kosten bei der Ausgabe von Anforderungen in Azure Cosmos DB optimieren können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 3f4c33a88d9a8fbf2c3d64135d93da54cf75fab3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097501"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Optimieren der Anforderungskosten in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel wird beschrieben, wie Lese- und Schreibanforderungen in [Anforderungseinheiten](request-units.md) übersetzt werden und wie die Kosten dieser Anforderungen optimiert werden können. Lesevorgänge umfassen Punktlesevorgänge und Abfragen. Schreibvorgänge umfassen Einfüge-, Ersetzungs-, Lösch- und Upsert-Vorgänge für Elemente.

Azure Cosmos DB bietet eine umfangreiche Sammlung von Datenbankvorgängen, die für die Elemente in einem Container ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstatt sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (Request Unit, RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für die verschiedenen Datenbankvorgänge zum Verarbeiten einer Anforderung erforderlich sind.

## <a name="measuring-the-ru-charge-of-a-request"></a>Messen der RU-Gebühr für eine Anforderung

Es ist wichtig, die RU-Gebühren für Ihre Anforderungen zu messen, um die tatsächlichen Kosten zu verstehen und auch die Effektivität Ihrer Optimierungen zu bewerten. Sie können diese Kosten über das Azure-Portal oder mithilfe eines der SDKs anhand der von Azure Cosmos DB zurückgegebenen Antwort abrufen. Ausführliche Anweisungen dazu finden Sie unter [Ermitteln der Gebühr für eine Anforderungseinheit in Azure Cosmos DB](find-request-unit-charge.md).

## <a name="reading-data-point-reads-and-queries"></a>Lesen von Daten: Punktlesevorgänge und Abfragen

Lesevorgänge in Azure Cosmos DB werden in der Regel (absteigend von den schnellsten/effizientesten zu den langsameren/weniger effizienten Vorgängen) in Bezug auf den RU-Verbrauch wie folgt sortiert:  

* Punktlesevorgänge (Suche nach Schlüssel-Wert-Paar anhand einer einzelnen Element-ID und einem einzelnen Partitionsschlüssel)
* Abfrage mit einer Filterklausel in einem einzelnen Partitionsschlüssel
* Abfrage ohne eine Gleichheits- oder Bereichsfilterklausel in einer Eigenschaft
* Abfrage ohne Filter

### <a name="role-of-the-consistency-level"></a>Rolle der Konsistenzebene

Bei Verwendung der [Konsistenzebene](consistency-levels.md) **Stark** oder **Begrenzte Veraltung** werden die RU-Kosten für einen Lesevorgang (Punktlesevorgang oder Abfrage) verdoppelt.

### <a name="point-reads"></a>Punktlesevorgänge

Der einzige Faktor, der sich auf die RU-Gebühr für einen Punktlesevorgang auswirkt (neben der verwendeten Konsistenzebene), ist die Größe des abgerufenen Elements. Die folgende Tabelle enthält die RU-Kosten von Punktlesevorgängen für Elemente mit einer Größe von 1 KB und 100 KB.

| **Elementgröße** | **Kosten eines Punktlesevorgangs** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RUs |

Da Punktlesevorgänge (Suchen nach Schlüssel-Wert-Paaren anhand der Element-ID) die effizientesten Lesevorgänge sind, sollten Sie sicherstellen, dass Ihre Element-ID einen aussagekräftigen Wert aufweist, damit Sie die Elemente nach Möglichkeit mit einem Punktlesevorgang (anstelle einer Abfrage) abrufen können.

### <a name="queries"></a>Abfragen

Die Anforderungseinheiten für Abfragen hängen von einer Reihe Faktoren ab, zum Beispiel von der Anzahl der geladenen/zurückgegebenen Azure Cosmos-Elemente, von der Anzahl der Suchvorgänge im Index, die Zeit zum Kompilieren der Abfrage usw. Azure Cosmos DB garantiert, dass die gleiche Abfrage, wenn sie für die gleichen Daten ausgeführt wird, selbst bei wiederholter Ausführung immer die gleiche Anzahl von Anforderungseinheiten verbraucht. Das Abfrageprofil, das Abfrageausführungsmetriken verwendet, bietet Ihnen einen guten Überblick darüber, wie die Anforderungseinheiten verbraucht werden.  

In einigen Fällen sehen Sie möglicherweise eine Sequenz von 200 und 429 Antworten und variable Anforderungseinheiten in einer ausgelagerten Ausführung von Abfragen, was daran liegt, dass Abfragen auf Basis der verfügbaren Anforderungseinheiten so schnell wie möglich ausgeführt werden. Sie sehen möglicherweise auch, dass die Ausführung einer Abfrage in mehrere Seiten/Roundtrips zwischen Server und Client aufgeteilt werden. 10.000 Elemente können beispielsweise in Form mehrerer Seiten zurückgegeben werden, die jeweils anhand des für diese Seite ausgeführten Rechenaufwands berechnet werden. Wenn Sie die Summen dieser Seiten addieren, erhalten Sie die gleiche Anzahl an Anforderungseinheiten, die Sie für die gesamte Abfrage erhalten würden.

#### <a name="metrics-for-troubleshooting-queries"></a>Metriken für Abfragen zur Problembehandlung

Die von Abfragen verbrauchte Leistung und der Durchsatz (einschließlich benutzerdefinierter Funktionen) hängen hauptsächlich vom Funktionsrumpf ab. Wie lange die Ausführung der Abfrage in der benutzerdefinierten Funktion (UDF) dauert und wie viele Anforderungseinheiten verbraucht werden, können Sie am einfachsten herausfinden, indem Sie die Abfragemetriken aktivieren. Wenn Sie das .NET-SDK verwenden, finden Sie hier einige Beispiele für Abfragemetriken, die vom SDK zurückgegeben werden:

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

#### <a name="best-practices-to-cost-optimize-queries"></a>Bewährte Methoden für die Kostenoptimierung von Abfragen 

Berücksichtigen Sie bei der Kostenoptimierung von Abfragen die folgenden bewährten Methoden:

* **Zusammenstellen von mehreren Entitätstypen**

   Versuchen Sie, mehrere Entitätstypen in einem oder einigen wenigen Containern zusammenzustellen. Diese Methode bietet nicht nur Vorteile aus preislicher Sicht, sondern wirkt sich auch positiv auf die Ausführung von Abfragen und Transaktionen aus. Abfragen werden auf einen einzelnen Container begrenzt, und atomische Transaktionen für mehrere Datensätze über gespeicherte Prozeduren/Trigger hinweg werden auf einen Partitionsschlüssel in einem einzelnen Container begrenzt. Durch das Zusammenstellen von Entitäten im selben Container können Sie die Anzahl der Netzwerk-Roundtrips zum Auflösen von Beziehungen zwischen den Datensätzen reduzieren. Dadurch erhöht sich die End-to-End-Leistung und ermöglicht atomische Transaktionen über mehrere Datensätze für ein größeres Dataset. In Folge sinken die Kosten. Wenn in Ihrem Szenario die Zusammenstellung mehrerer Entitätstypen in einem oder einigen wenigen Containern schwierig ist, liegt dies in der Regel daran, dass Sie eine vorhandene Anwendung migrieren und keine Codeänderungen vornehmen möchten. In diesem Fall sollten Sie die Bereitstellung des Durchsatzes auf Datenbankebene in Betracht ziehen.  

* **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

   Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten (RUs) für einen Vorgang verbraucht werden, zum Beispiel die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets. All diese Faktoren beeinflussen die Kosten von Abfragevorgängen. 

Durch die Verwendung eines Modells mit bereitgestelltem Durchsatz bietet Azure Cosmos DB eine vorhersagbare Leistung in Hinblick auf Durchsatz und Latenz. Der bereitgestellte Durchsatz wird in Form von [Anforderungseinheiten](request-units.md) pro Sekunde bzw. RUs/Sek. dargestellt. Eine Anforderungseinheit (RU) ist eine logische Abstraktion der Compute-Ressourcen wie CPU, Arbeitsspeicher, E/A usw., die zum Ausführen einer Anforderung erforderlich sind. Der bereitgestellte Durchsatz (RUs/Sek.) wird reserviert und fest dem Container oder der Datenbank zugeordnet, um vorhersagbaren Durchsatz und vorhersehbare Latenz bereitzustellen. Mit dem bereitgestellten Durchsatz kann Azure Cosmos DB für eine vorhersagbare und konsistente Leistung sorgen, die niedrige Latenzzeiten und eine hohe Verfügbarkeit auf jeder Skalierungsstufe garantiert. Anforderungseinheiten stellen eine normalisierte Währung dar, welche die Überlegungen bezüglich der Menge der für eine Anwendung benötigten Ressourcen vereinfacht.

Die im Header der Anforderung zurückgegebene Anforderungsgebühr gibt die Kosten für eine bestimmte Abfrage an. Wenn eine Abfrage beispielsweise 1000 Elemente mit einer Größe von 1 KB zurückgibt, belaufen sich die Kosten für den Vorgang auf 1000. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie im Artikel zu den [Anforderungseinheiten](request-units.md) sowie im Rechner für Anforderungseinheiten.

## <a name="writing-data"></a>Schreiben von Daten

Die RU-Kosten für das Schreiben eines Elements hängen von Folgendem ab:

- Elementgröße
- Anzahl der durch die [Indizierungsrichtlinie](index-policy.md) abgedeckten und zu indizierenden Eigenschaften

Das Einfügen eines 1 KB-Elements mit weniger als fünf zu indizierenden Eigenschaften kostet ca. 5 RUs. Das Ersetzen eines Elements kostet zweimal so viel wie das Einfügen desselben Elements.

### <a name="optimizing-writes"></a>Optimieren von Schreibvorgängen

Die beste Möglichkeit, die RU-Kosten von Schreibvorgängen zu optimieren, ist das Anpassen der Elementgröße und der Anzahl der indizierten Eigenschaften.

- Das Speichern sehr großer Elemente in Azure Cosmos DB führt zu hohen RU-Gebühren und kann als Antimuster angesehen werden. Speichern Sie insbesondere keine Binärinhalte oder großen Textblöcke, die nicht abgefragt werden müssen. Es hat sich bewährt, diese Art von Daten in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) abzulegen und einen Verweis (oder Link) auf das Blob in dem Element zu speichern, das Sie in Azure Cosmos DB schreiben.
- Wenn Sie die Indizierungsrichtlinie so optimieren, dass nur die Eigenschaften indiziert werden, nach denen Ihre Abfragen filtern, kann dies in Bezug auf die von Schreibvorgängen verbrauchten RUs einen großen Unterschied machen. Wenn Sie einen neuen Container erstellen, wird mit der Standardindizierungsrichtlinie jede in den Elementen gefundene Eigenschaft indiziert. Dies ist zwar ein guter Standard für Entwicklungsaktivitäten, doch wird eine erneute Auswertung und [Anpassung Ihrer Indizierungsrichtlinie](how-to-manage-indexing-policy.md) dringend empfohlen, wenn Sie in die Produktionsumgebung wechseln oder Ihre Workload einen hohen Datenverkehr empfängt.

Bei einer Massenerfassung von Daten wird zudem die Verwendung der [Azure Cosmos DB-BulkExecutor-Bibliothek](bulk-executor-overview.md) empfohlen, da sie zur Optimierung des RU-Verbrauchs bei solchen Vorgängen entworfen wurde. Optional können Sie auch [Azure Data Factory](../data-factory/introduction.md) verwenden, die auf derselben Bibliothek basiert.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
* Weitere Informationen unter [Reservierte Azure Cosmos DB-Kapazität](cosmos-db-reserved-capacity.md)
