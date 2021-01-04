---
title: Nicht mehr verwendete Azure Cosmos DB-Leistungsebenen
description: Erfahren Sie etwas über die Leistungsstufen S1, S2 und S3, die zuvor in Azure Cosmos DB verfügbar waren.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4cb07d9d19d85cd8dff9a52eeeb7e173b60f4d6d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080769"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Aussondern der Leistungsebenen S1, S2 und S3
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT] 
> Die in diesem Artikel beschriebenen Leistungsebenen S1, S2 und S3 werden ausgesondert und sind für neue Azure Cosmos DB-Konten nicht mehr verfügbar.

Dieser Artikel enthält eine Übersicht über die Leistungsebenen S1, S2 und S3 und erläutert, wie die Sammlungen, die diese Leistungsebenen verwenden, zu Sammlungen mit nur einer Partition migriert werden können. Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- [Warum werden die Leistungsebenen S1, S2 und S3 außer Betrieb genommen?](#why-retired)
- [Welche Unterschiede weisen Sammlungen mit nur einer Partition und partitionierte Sammlungen gegenüber den Leistungsebenen S1, S2 und S3 auf?](#compare)
- [Was muss ich tun, um den unterbrechungsfreien Zugriff auf meine Daten sicherzustellen?](#uninterrupted-access)
- [Wie verändert sich meine Sammlung nach der Migration?](#collection-change)
- [Wie verändert sich meine Abrechnung nach der Migration zu Sammlungen mit nur einer Partition?](#billing-change)
- [Welche Möglichkeiten habe ich, wenn ich mehr als 20 GB Speicher benötige?](#more-storage-needed)
- [Kann ich vor der geplanten Migration zwischen den Leistungsebenen S1, S2 und S3 wechseln?](#change-before)
- [Wie kann ich die Migration von den Leistungsebenen S1, S2 und S3 zu Sammlungen mit nur einer Partition selbst durchführen?](#migrate-diy)
- [Inwieweit bin ich betroffen, wenn ich EA-Kunde bin?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Warum werden die Leistungsebenen S1, S2 und S3 ausgesondert?

Die Leistungsebenen S1, S2 und S3 bieten nicht die gleiche Flexibilität wie das Azure Cosmos DB-Standardangebot. Bei den Leistungsebenen S1, S2 und S3 waren sowohl der Durchsatz als auch die Speicherkapazität voreingestellt. Sie haben keine Elastizität bereitgestellt. Mit Azure Cosmos DB können Sie den Durchsatz und den Speicher jetzt anpassen, sodass Sie viel flexibler skalieren können, wenn sich Ihre Anforderungen ändern.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Welche Unterschiede weisen Sammlungen mit nur einer Partition und partitionierte Sammlungen gegenüber den Leistungsebenen S1, S2 und S3 auf?

Die folgende Tabelle enthält einen Vergleich der Durchsatz- und Speicheroptionen von Sammlungen mit nur einer Partition, partitionierten Sammlungen und den Leistungsebenen S1, S2 und S3. Hier ist ein Beispiel für die Region „USA, Osten 2“ angegeben:

| Namen des Kontingents  |Partitionierte Sammlung|Sammlung mit nur einer Partition|S1|S2|S3|
|---|---|---|---|---|---|
|Maximaler Durchsatz|Unbegrenzt|10.000 RU/s|250 RU/s|1\.000 RU/s|2.500 RU/s|
|Minimaler Durchsatz|2.500 RU/s|400 RU/s|250 RU/s|1\.000 RU/s|2.500 RU/s|
|Maximale Speichergröße|Unbegrenzt|20 GB|20 GB|20 GB|20 GB|
|Preis (monatlich)|Durchsatz: 6 USD/100 RU/s<br><br>Speicher: 0,25 USD/GB|Durchsatz: 6 USD/100 RU/s<br><br>Speicher: 0,25 USD/GB|25 USD|50 USD|100 USD|

Sind Sie EA-Kunde? Wenn ja, helfen Ihnen die Informationen unter [Inwieweit bin ich betroffen, wenn ich EA-Kunde bin?](#ea-customer) weiter.

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Was muss ich tun, um den unterbrechungsfreien Zugriff auf meine Daten sicherzustellen?

Wenn Sie über eine Sammlung der Leistungsebene S1, S2 oder S3 verfügen, sollten Sie das [.NET SDK](#migrate-diy) verwenden, um diese programmgesteuert zu einer Sammlung mit nur einer Partition zu migrieren. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Wie verändert sich meine Sammlung nach der Migration?

Wenn Sie über eine S1-Sammlung verfügen, können Sie diese zu einer Sammlung mit nur einer Partition und einem Durchsatz von 400 RU/s migrieren. 400 RU/s ist der niedrigste Durchsatz, der für Sammlungen mit nur einer Partition verfügbar ist. Die Kosten für 400 RU/s in einer Sammlung mit einer einzelnen Partition entsprechen ungefähr den Kosten für eine S1-Sammlung mit 250 RU/s. Sie erhalten die zusätzlichen 150 RU/s also ohne weitere Kosten.

Wenn Sie über eine S2-Sammlung verfügen, können Sie diese zu einer Sammlung mit nur einer Partition und 1.000 RU/s migrieren. Die Durchsatzstufe ändert sich für Sie nicht.

Wenn Sie über eine S3-Sammlung verfügen, können Sie diese zu einer Sammlung mit nur einer Partition und 2.500 RU/s migrieren. Die Durchsatzstufe ändert sich für Sie nicht.

In jedem dieser Fälle können Sie nach der Migration der Sammlung die Durchsatzstufe anpassen und je nach Bedarf zentral hoch- oder herunterskalieren, um Ihren Benutzer einen Zugriff mit geringer Latenz zu ermöglichen. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Wie verändert sich meine Abrechnung nach der Migration zu Sammlungen mit nur einer Partition?

Angenommen, Sie verfügen über zehn S1-Sammlungen mit jeweils 1 GB Speicher in der Region „USA, Osten“ und migrieren diese zehn S1-Sammlungen zu zehn Sammlungen mit nur einer Partition und 400 RU/s (Minimum). Ihre Rechnung sieht dann wie folgt aus, wenn Sie die zehn Sammlungen mit nur einer Partition einen ganzen Monat lang beibehalten:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Vergleich der S1-Preise für zehn Sammlungen mit dem Preis für zehn Sammlungen mit nur einer Partition" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Welche Möglichkeiten habe ich, wenn ich mehr als 20 GB Speicher benötige?

Es spielt keine Rolle, ob Sie eine Sammlung mit der Leistungsebene S1, S2 oder S3 oder eine Sammlung mit nur einer Partition verwenden, für die jeweils 20 GB Speicher verfügbar sind: Sie können das Azure Cosmos DB-Datenmigrationstool verwenden, um Ihre Daten zu einer partitionierten Sammlung mit praktisch unbegrenztem Speicher zu migrieren. Informationen zu den Vorteilen einer partitionierten Sammlung finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partitioning-overview.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kann ich vor der geplanten Migration zwischen den Leistungsebenen S1, S2 und S3 wechseln?

Nur für vorhandene Konten der Leistungsebenen S1, S2 und S3 können die Leistungsebenentarife programmgesteuert [mit dem .NET SDK](#migrate-diy) geändert werden. Wenn Sie von S1, S2 oder S3 zu einer Sammlung mit nur einer Partition wechseln, ist es nicht möglich, zu den Leistungsebenen S1, S2 oder S3 zurückzukehren.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Wie kann ich die Migration von den Leistungsebenen S1, S2 und S3 zu Sammlungen mit nur einer Partition selbst durchführen?

Sie können die Migration von den Leistungsebenen S1, S2 und S3 zu Sammlungen mit nur einer Partition programmgesteuert [mit dem .NET SDK](#migrate-diy) durchführen. Sie können diesen Schritt vor der geplanten Migration selbst ausführen, um von den flexiblen Durchsatzoptionen für Sammlungen mit nur einer Partition zu profitieren.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrieren zu Sammlungen mit nur einer Partition mit dem .NET SDK

In diesem Abschnitt wird nur das Ändern der Leistungsstufe einer Sammlung mithilfe der [SQL-.NET-API](sql-api-sdk-dotnet.md) behandelt. Die Vorgehensweise ist bei den anderen SDKs jedoch ähnlich.

Hier ist ein Codeausschnitt zum Ändern des Sammlungsdurchsatzes auf 5.000 Anforderungseinheiten pro Sekunde angegeben:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Besuchen Sie [MSDN](/dotnet/api/microsoft.azure.documents.client.documentclient) , um weitere Beispiele anzuzeigen und mehr über unsere Angebotsmethoden zu erfahren:

* [**ReadOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](/previous-versions/azure/dn975114(v=azure.100))

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Inwieweit bin ich betroffen, wenn ich EA-Kunde bin?

EA-Kunden genießen bis zum Ablauf ihres aktuellen Vertrags Preissicherheit.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Preisen und der Datenverwaltung mit Azure Cosmos DB finden Sie in folgenden Ressourcen:

1.  [Partitionierung von Daten in Cosmos DB](partitioning-overview.md). Es werden die Unterschiede zwischen Containern mit nur einer Partition und partitionierten Containern beschrieben, und Sie erhalten Tipps zur Implementierung einer Partitionierungsstrategie für die nahtlose Skalierung.
2.  [Cosmos DB-Preise](https://azure.microsoft.com/pricing/details/cosmos-db/). Enthält Informationen zu den Kosten für den Bereitstellungsdurchsatz und für den Speicherverbrauch.
3.  [Anforderungseinheiten](request-units.md): Enthält Informationen zum Verbrauch des Durchsatzes für verschiedene Vorgangstypen, z.B. Lesen, Schreiben, Abfragen.