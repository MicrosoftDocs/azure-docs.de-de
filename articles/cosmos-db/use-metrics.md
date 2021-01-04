---
title: Überwachen und Debuggen mit Metriken in Azure Cosmos DB
description: Verwenden Sie Metriken in Azure Cosmos DB zum Beheben häufiger Probleme und Überwachen der Datenbank.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 243f6f26be592e2db82d8f46df3de9aafcd2078b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996983"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Überwachen und Debuggen mit Metriken in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bietet Metriken für Durchsatz, Speicher, Konsistenz, Verfügbarkeit und Latenz. Das Azure-Portal bietet eine aggregierte Ansicht dieser Metriken. Sie können Azure Cosmos DB-Metriken auch über die Azure Monitor-API anzeigen. Bei den Dimensionswerten für die Metriken, z. B. dem Containernamen, wird die Groß-/Kleinschreibung nicht berücksichtigt. Daher müssen Sie bei Zeichenfolgenvergleichen für diese Dimensionswerte Vergleiche verwenden, bei denen die Groß-/Kleinschreibung nicht berücksichtigt wird. Informationen zum Anzeigen von Metriken von Azure Monitor finden Sie im Artikel [Abrufen von Metriken von Azure Monitor](./monitor-cosmos-db.md).

Dieser Artikel behandelt häufige Anwendungsfälle und zeigt, wie Azure Cosmos DB-Metriken zum Analysieren und Beheben der Probleme verwendet werden können. Metriken werden alle fünf Minuten erfasst und sieben Tage lang gespeichert.

## <a name="view-metrics-from-azure-portal"></a>Anzeigen von Metriken über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie den Bereich **Metriken**. Der Bereich „Metriken“ zeigt standardmäßig Metriken für den Speicher, Index und die Anforderungseinheiten für alle Datenbanken in Ihrem Azure Cosmos-Konto an. Sie können diese Metriken pro Datenbank, Container oder Region filtern. Sie können die Metriken auch mit einer bestimmten Zeitgranularität filtern. Weitere Informationen zu den Durchsatz-, Speicher-, Verfügbarkeits-, Latenz- und Konsistenzmetriken werden auf separaten Registerkarten bereitgestellt. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB-Leistungsmetriken im Azure-Portal":::

Die folgenden Metriken sind im Bereich **Metriken** verfügbar: 

* **Durchsatzmetrik**: Diese Metrik zeigt die Anzahl der genutzten oder fehlerhaften Anforderungen an (Antwortcode 429), da der Durchsatz oder die bereitgestellte Speicherkapazität für den Container überschritten wurde.

* **Speichermetrik**: Diese Metrik zeigt die Größe der Daten und die Indexnutzung an.

* **Verfügbarkeitsmetrik**: Diese Metrik zeigt den Prozentsatz erfolgreicher Anforderungen von der Gesamtzahl der Anforderungen pro Stunde an. Die Erfolgsrate wird durch Azure Cosmos DB-SLAs definiert.

* **Latenzmetrik**: Diese Metrik zeigt die Wartezeit beim Lesen und Schreiben an, die von Azure Cosmos DB in der Region, in der Ihr Konto ausgeführt wird, gemessen wird. Sie können die regionsübergreifende Wartezeit für ein georepliziertes Konto visualisieren. Diese Metrik stellt nicht die End-to-End-Anforderungswartezeit dar.

* **Konsistenzmetrik**: Diese Metrik informiert über die Konsistenz für das ausgewählte Konsistenzmodell. Diese Metrik zeigt für Konten in mehreren Regionen auch die Wartezeit für die Replikation zwischen den ausgewählten Regionen an.

* **Systemmetrik**: Diese Metrik zeigt an, wie viele Metadatenanforderungen von der primären Partition bereitgestellt werden. Diese hilft auch beim Identifizieren der gedrosselten Anforderungen.

In den folgenden Abschnitten werden allgemeine Szenarios erläutert, in denen Sie Azure Cosmos DB-Metriken verwenden können. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Ermitteln der Anzahl von Anforderungen, die erfolgreich ausgeführt werden oder Fehler verursachen

Rufen Sie zunächst das [Azure-Portal](https://portal.azure.com) auf, und navigieren Sie zum Blatt **Metriken**. Suchen Sie im Blatt das Diagramm **Anzahl von Anforderungen pro Minute, die die Kapazität überschritten haben. In diesem Diagramm wird die Summe der nach Statuscode segmentierten Anforderungen für jede einzelne Minute angezeigt. Weitere Informationen zu HTTP-Statuscodes finden Sie unter [HTTP Status Codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) (HTTP-Statuscodes für Azure Cosmos DB).

Der häufigste Fehlerstatuscode ist 429 (Ratenbegrenzung/Drosselung). Dieser Fehler besagt, dass Anforderungen an Azure Cosmos DB den bereitgestellten Durchsatz überschreiten. Die gängigste Lösung für dieses Problem ist das [Hochskalieren der Anforderungseinheiten](./set-throughput.md) (Request Units, RUs) für die angegebene Sammlung.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Anzahl von Anforderungen pro Minute":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Ermitteln der partitionsübergreifenden Durchsatzverteilung

Für jede skalierbare Anwendung ist eine gute Kardinalität der Partitionsschlüssel von wesentlicher Bedeutung. Um die nach Partitionen aufgeschlüsselte Verteilung des Durchsatzes jedes partitionierten Containers zu bestimmen, navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Blatt **Metriken**. Auf der Registerkarte **Durchsatz** wird im Diagramm **Maximal genutzte RU/Sekunde je physische Partition** die Speicheraufschlüsselung angezeigt. Die folgende Abbildung zeigt ein Beispiel für eine schlechte Verteilung der Daten, die sich an der extremen Partition am linken Rand erkennen lässt.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Einzelne Partition mit starker Auslastung":::

Eine ungleichmäßige Verteilung des Durchsatzes kann *Hot*-Partitionen verursachen, die zu gedrosselten Anforderungen führen können und möglicherweise eine Neupartitionierung erfordern. Weitere Informationen zum Partitionieren in Azure Cosmos DB finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Ermitteln der partitionsübergreifenden Speicherverteilung

Für jede skalierbare Anwendung ist eine gute Kardinalität der Partition von wesentlicher Bedeutung. Um die nach Partitionen aufgeschlüsselte Speicherverteilung jedes partitionierten Containers zu bestimmen, wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Blatt „Metriken“. Auf der Registerkarte „Speicher“ wird die Speicheraufschlüsselung im Diagramm „Von wichtigen Partitionsschlüsseln genutzter Daten- und Indexspeicher“ angezeigt. Die folgende Abbildung zeigt eine schlechte Verteilung des Datenspeichers, wie die verzerrte Partition ganz links verdeutlicht.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Beispiel für eine schlechte Datenverteilung":::

Sie können bestimmen, welcher Partitionsschlüssel die Verteilung verzerrt, indem Sie auf die Partition im Diagramm klicken.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Partitionsschlüssel verzerrt die Verteilung":::

Nachdem Sie den Partitionsschlüssel identifiziert haben, der die verzerrte Verteilung verursacht, müssen Sie möglicherweise den Container mit einem Partitionsschlüssel für eine gleichmäßigere Verteilung neu partitionieren. Weitere Informationen zum Partitionieren in Azure Cosmos DB finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Vergleichen der Datengröße mit der Indexgröße

In Azure Cosmos DB ist die Summe des Speicherverbrauchs die Kombination von Datengröße und Indexgröße. In der Regel ist die Indexgröße ein Bruchteil der Datengröße. Im [Azure-Portal](https://portal.azure.com) wird im Blatt „Metriken“ auf der Registerkarte „Speicher“ die Aufschlüsselung des Speicherverbrauchs auf der Grundlage von Daten und Index angezeigt.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Wenn Sie Indexspeicher sparen möchten, können Sie die [Indizierungsrichtlinie](index-policy.md) anpassen.

## <a name="debug-why-queries-are-running-slow"></a>Debuggen der Ursachen für eine langsame Ausführung von Abfragen

Azure Cosmos DB bietet in den SDKs der SQL-API Statistiken zur Ausführung von Abfragen.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* liefert Details zur Ausführungsdauer jeder Komponente der Abfrage. Die häufigste Ursache für die langsame Ausführung von Abfragen sind Überprüfungen (d. h. die Abfrage konnte die Indizes nicht nutzen). Dieses Problem kann durch eine bessere Filterbedingung behoben werden.

## <a name="next-steps"></a>Nächste Schritte

Jetzt wissen Sie, wie Sie mit den Metriken im Azure-Portal Problembereiche überwachen und Probleme beheben können. In den folgenden Artikeln erfahren Sie mehr darüber, wie Sie die Datenbankleistung verbessern können:

* Informationen zum Anzeigen von Metriken von Azure Monitor finden Sie im Artikel [Abrufen von Metriken von Azure Monitor](./monitor-cosmos-db.md). 
* [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md)
* [Leistungstipps für Azure Cosmos DB](performance-tips.md)