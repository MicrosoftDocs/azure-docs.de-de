---
title: Optimieren der Speicherkosten in Azure Cosmos DB
description: In diesem Artikel wird erläutert, wie Sie für die in Azure Cosmos DB gespeicherten Daten die Speicherkosten optimieren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080849"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimieren der Speicherkosten in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bietet unbegrenzten Speicher und Durchsatz. Im Gegensatz zum Durchsatz, den Sie für Ihre Azure Cosmos-Container oder -Datenbanken konfigurieren bzw. bereitstellen müssen, wird der Speicher auf Nutzungsbasis abgerechnet. Kosten fallen nur für den logischen Speicher an, den Sie nutzen. Sie müssen keinen Speicher im Voraus reservieren. Der Speicher wird auf Grundlage der Daten, die Sie Ihrem Azure Cosmos-Container hinzufügen bzw. aus diesem entfernen, automatisch zentral hoch- und herunterskaliert.

## <a name="storage-cost"></a>Speicherkosten

Speicher wird in der Einheit GB abgerechnet. Von Ihren Daten und der Indizierung wird lokaler SSD-gestützter Speicher verwendet. Der insgesamt genutzte Speicher entspricht dem erforderlichen Speicher für die Daten und Indizes, die in allen Regionen, in denen Sie Azure Cosmos DB nutzen, verwendet werden. Wenn Sie ein Azure Cosmos-Konto global über drei Regionen replizieren, zahlen Sie die Gesamtspeicherkosten in jeder der drei Regionen. Mit dem [Kapazitätsplanungstool](https://www.documentdb.com/capacityplanner) können Sie Ihre Speicheranforderungen schätzen. Die Kosten für Speicher in Azure Cosmos DB betragen 0,25 US-Dollar pro GB und Monat. Die jeweils aktuellen Preise finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/). Sie können Warnungen einrichten, um den von Ihrem Azure Cosmos-Container genutzten Speicher zu bestimmen und Ihre Speicheranforderungen zu überwachen (siehe [Überwachen von Microsoft Azure Cosmos DB](./monitor-cosmos-db.md)).

## <a name="optimize-cost-with-item-size"></a>Optimieren der Kosten über die Elementgröße

Für eine optimale Leistung und Kostenvorteile erwartet Azure Cosmos DB eine Elementgröße von 2 MB oder kleiner. Wenn Sie ein Datenelement speichern müssen, das größer als 2 MB ist, sollten Sie über eine Umgestaltung des Elementeschemas nachdenken. In den seltenen Fällen, in denen das Schema nicht neu gestaltet werden kann, können Sie das Element in Unterelemente aufteilen und diese mit einem allgemeinen Bezeichner (ID) logisch verknüpfen. Alle Funktionen von Azure Cosmos DB arbeiten einheitlich und verwenden diesen logischen Bezeichner als Anker.

## <a name="optimize-cost-with-indexing"></a>Optimieren der Kosten über die Indizierung

Standardmäßig werden die Daten automatisch indiziert, wodurch sich die Gesamtmenge des genutzten Speichers erhöhen kann. Sie können jedoch benutzerdefinierte Indizierungsrichtlinien verwenden, um diesen zusätzlichen Aufwand zu reduzieren. Eine automatische Indizierung, die nicht über eine Richtlinie optimiert wurde, beträgt etwa 10 bis 20 % der Elementgröße. Durch Entfernen oder Anpassen der Indizierungsrichtlinien brauchen Sie keine Extrakosten für Schreibvorgänge zu bezahlen und benötigen keine zusätzliche Durchsatzkapazität. Das Konfigurieren benutzerdefinierter Indizierungsrichtlinien wird unter [Indizierung in Azure Cosmos DB](index-policy.md) beschrieben. Wenn Sie bereits mit relationalen Datenbanken gearbeitet haben, denken Sie vielleicht, dass „alles indizieren“ eine Verdoppelung des Speichers (oder mehr) bedeutet. In Azure Cosmos DB fällt die Indizierung im Durchschnitt wesentlich weniger ins Gewicht. In Azure Cosmos DB fällt der Speicheraufwand infolge der Indizierung, selbst bei der automatischen Indizierung, in der Regel gering aus (10-20 %), da die Datenbank für einen kleinen „Speicherfußabdruck“ ausgelegt ist. Durch die Verwaltung der Indizierungsrichtlinie können Sie den Kompromiss zwischen Indexfußabdruck und Abfrageleistung in einer noch feiner abgestimmten Weise steuern.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimieren der Kosten über Gültigkeitsdauer und Änderungsfeed

Wenn Sie die Daten nicht mehr benötigen, können Sie diese elegant über die [Gültigkeitsdauer](time-to-live.md) oder den [Änderungsfeed](change-feed.md) aus Ihrem Azure-Cosmos-Konto löschen, oder Sie migrieren die alten Daten in einen anderen Datenspeicher wie Azure Blob Storage oder Azure Data Warehouse. Mit der Gültigkeitsdauer (Time to Live, TTL) bietet Azure Cosmos DB die Möglichkeit, Elemente nach einem bestimmten Zeitraum automatisch aus einem Container zu löschen. Standardmäßig können Sie die Gültigkeitsdauer auf der Containerebene festlegen und den Wert für einzelne Elemente außer Kraft setzen. Wenn Sie eine Gültigkeitsdauer auf Container- oder Elementebene festgelegt haben, werden die betroffenen Elemente nach Ablauf der angegebenen Zeit (seit der letzten Änderung der Elemente) automatisch von Azure Cosmos DB entfernt. Mithilfe des Änderungsfeeds können Sie Daten entweder in einen anderen Container in Azure Cosmos DB oder in einen externen Datenspeicher migrieren. Die Migration verursacht keine Ausfallzeit, und nach der Migration können Sie die Gültigkeitsdauer entweder löschen oder konfigurieren, um den Azure Cosmos-Quellcontainer zu löschen.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimieren der Kosten mit Rich-Media-Datentypen 

Wenn Sie Rich-Media-Datentypen (z. B. Videos, Bilder usw.) speichern möchten, stehen in Azure Cosmos DB eine Reihe von Optionen zur Auswahl. Mit einer dieser Optionen können Sie Rich-Media-Typen als Azure-Cosmos-Elemente speichern. Pro Element gibt es einen Grenzwert von 2 MB. Sie können diesen Grenzwert umgehen, indem Sie die Datenelemente in mehrere Unterelemente aufteilen und verketten. Oder Sie speichern diese Elemente in Azure Blob Storage und verwenden die Metadaten, um von Ihren Azure Cosmos-Elementen darauf zu verweisen. Bei diesem Ansatz gibt es eine Reihe von Vor- und Nachteilen. Beim ersten Ansatz erhalten Sie neben den regulären Azure-Cosmos-Elementen die beste Leistung in Form von Latenz, Durchsatz-SLAs sowie sofort einsatzbereiten globalen Verteilungsfunktionen für die Rich-Media-Datentypen. Diese Unterstützung bedingt jedoch einen höheren Preis. Indem Sie die Medien in Azure Blob Storage speichern, können Sie Ihre Gesamtkosten senken. Falls die Latenz eine Rolle spielt, könnten Sie für die Rich-Media-Dateien, auf die von Azure-Cosmos-Elementen verwiesen wird, Premium-Speicher verwenden. Dieser ist nativ in das CDN integriert, um die Images vom Edge-Server zu niedrigeren Kosten bereitzustellen und die geografische Einschränkung zu umgehen. Der Nachteil bei diesem Szenario ist, dass Sie es mit zwei Diensten – Azure Cosmos DB und Azure Blob Storage – zu tun haben, wodurch möglicherweise die Betriebskosten steigen. 

## <a name="check-storage-consumed"></a>Überprüfen der Speichernutzung

Zum Überprüfen der Speichernutzung eines Azure Cosmos-Containers können Sie eine HEAD- oder GET-Anforderung für den Container ausführen und die Header `x-ms-request-quota` und `x-ms-request-usage` prüfen. Wenn Sie mit dem .NET SDK arbeiten, können Sie alternativ mit den Eigenschaften [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100)) und [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) den genutzten Speicher abrufen.

## <a name="using-sdk"></a>Verwenden des SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
