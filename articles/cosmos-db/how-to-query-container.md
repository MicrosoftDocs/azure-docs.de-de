---
title: Abfragen von Containern in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Abfragen innerhalb einer Partition und partitionsübergreifende Abfragen für Container in Azure Cosmos DB ausführen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 0f08ca84597b08b9a236b7bfb0fc9c849423a752
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335890"
---
# <a name="query-an-azure-cosmos-container"></a>Abfragen eines Azure Cosmos-Containers
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie einen Container (Sammlung, Diagramm oder Tabelle) in Azure Cosmos DB abfragen. Insbesondere wird erläutert, wie Abfragen innerhalb einer Partition und partitionsübergreifende Abfragen in Azure Cosmos DB funktionieren.

## <a name="in-partition-query"></a>Abfrage innerhalb einer Partition

Beim Abfragen von Daten aus Containern wird die Abfrage von Azure Cosmos DB automatisch verarbeitet, wenn für die Abfrage ein Partitionsschlüsselfilter angegeben ist. Die Abfrage wird an die [physischen Partitionen](partitioning-overview.md#physical-partitions) weitergeleitet, die den im Filter angegebenen Partitionsschlüsselwerten entsprechen.

Stellen Sie sich z. B. die folgende Abfrage mit einem Gleichheitsfilter für `DeviceId` vor. Wenn diese Abfrage für einen Container ausgeführt wird, der auf `DeviceId` partitioniert ist, filtert diese Abfrage eine einzelne physische Partition.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Wie im vorherigen Beispiel filtert auch diese Abfrage eine einzelne Partition. Durch das Hinzufügen des zusätzlichen Filters für `Location` ändert sich nichts:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Im folgenden finden Sie eine Abfrage mit einem Bereichsfilter für den Partitionsschlüssel, der nicht auf eine einzelne physische Partition beschränkt ist. Eine Abfrage innerhalb einer Partition muss einen Gleichheitsfilter aufweisen, der den Partitionsschlüssel enthält:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Partitionsübergreifende Abfrage

Die folgende Abfrage enthält keinen Filter für den Partitionsschlüssel (`DeviceId`). Daher muss sie auf alle physischen Partitionen ausgefächert und für die Indizes der einzelnen Partitionen ausgeführt werden:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Jede physische Partition hat einen eigenen Index. Wenn Sie daher eine partitionsübergreifende Abfrage für einen Container ausführen, führen Sie also tatsächlich eine Abfrage *pro* physischer Partition aus. Azure Cosmos DB aggregiert automatisch Ergebnisse über verschiedene physische Partitionen.

Die Indizes in verschiedenen physischen Partitionen sind voneinander unabhängig. Es gibt keinen globalen Index in Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Parallele partitionsübergreifende Abfrage

Azure Cosmos DB SDKs ab Version 1.9.0 unterstützen Optionen für die Ausführung paralleler Abfragen. Parallele partitionsübergreifende Abfragen ermöglichen partitionsübergreifende Abfragen mit geringer Wartezeit.

Sie können die parallele Ausführung von Abfragen verwalten, indem Sie die folgenden Parameter optimieren:

- **MaxConcurrency** : Legt die maximale Anzahl gleichzeitiger Verbindungen mit den Partitionen des Containers fest. Wenn Sie diese Eigenschaft auf `-1` festlegen, verwaltet das SDK den Grad der Parallelität. Wenn  `MaxConcurrency` auf `0` festgelegt ist, gibt es eine einzelne Netzwerkverbindung mit den Partitionen des Containers.

- **MaxBufferedItemCount** : Steuert das Verhältnis zwischen Abfragewartezeit und clientseitiger Arbeitsspeichernutzung. Wird diese Option weggelassen oder auf „-1“ festgelegt, verwaltet das SDK die Anzahl von Elementen, die während der Ausführung paralleler Abfragen gepuffert werden.

Aufgrund der Fähigkeit von Azure Cosmos DB, partitionsübergreifende Abfragen zu parallelisieren, wird die Abfragelatenz im Allgemeinen gut skaliert, da das System [physische Partitionen](partitioning-overview.md#physical-partitions) hinzufügt. Die RU-Rechnungstellung steigt jedoch erheblich, wenn die Gesamtzahl der physischen Partitionen zunimmt.

Bei einer partitionsübergreifenden Abfrage führen Sie im Wesentlichen eine separate Abfrage pro einzelner physischer Partition durch. Zwar wird bei partitionsübergreifenden Abfragen der Index verwendet, sofern verfügbar, sie sind aber immer noch nicht so effizient wie Abfragen innerhalb von Partitionen.

## <a name="useful-example"></a>Nützliches Beispiel

Die folgende Analogie macht partitionsübergreifende Abfragen besser verständlich:

Angenommen, Sie sind ein Auslieferungsfahrer, der Pakete in verschiedenen Gebäudekomplexen zustellen muss. Zu jedem Gebäudekomplex gibt es eine Liste mit sämtlichen Wohneinheitennummern. Wir können jeden Gebäudekomplex mit einer physischen Partition und jede Liste mit dem Index der physischen Partition vergleichen.

Abfragen innerhalb der Partition und partitionsübergreifende Abfragen lassen sich mit folgendem Beispiel vergleichen:

### <a name="in-partition-query"></a>Abfrage innerhalb einer Partition

Wenn der Auslieferungsfahrer den richtigen Gebäudekomplex (physische Partition) kennt, kann er sofort zum richtigen Gebäude fahren. Der Fahrer kann anhand der Liste der Wohneinheitennummern des Gebäudekomplexes (Index) schnell die entsprechenden Pakete zustellen. In diesem Fall verschwendet der Fahrer keine Zeit und Aufwand, um zu einem Gebäudekomplex zu fahren und zu überprüfen, ob ein Paketempfänger dort wohnt.

### <a name="cross-partition-query-fan-out"></a>Partitionsübergreifende Abfrage (Ausfächern)

Wenn der Auslieferungsfahrer den richtigen Gebäudekomplex (physische Partition) nicht kennt, muss er zu jedem einzelnen Gebäude fahren und die Liste mit allen Wohneinheitennummern (den Index) überprüfen. Sobald er an einem Gebäudekomplex ankommt, kann er immer noch die Liste der Adressen der einzelnen Bewohner verwenden. Allerdings muss er die Liste jedes Gebäudekomplexes überprüfen, unabhängig davon, ob dort Paketempfänger wohnen oder nicht. So funktionieren partitionsübergreifende Abfragen. Es kann zwar der Index genutzt werden (es muss nicht an jede einzelne Tür geklopft werden), aber der Index jeder physischen Partition muss separat überprüft werden.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Partitionsübergreifende Abfrage (beschränkt auf einige physische Partitionen)

Wenn der Auslieferungsfahrer weiß, dass alle Paketempfänger in einigen wenigen Gebäudekomplexen leben, muss er nicht zu jedem einzelnen fahren. Während die Fahrt zu einigen wenigen Gebäudekomplexen immer noch mehr Aufwand erfordert, als nur ein einzelnes Gebäude aufzusuchen, spart der Auslieferungsfahrer trotzdem viel Zeit und Aufwand. Wenn der Filter einer Abfrage den Partitionsschlüssel mit dem `IN`-Schlüsselwort enthält, werden nur die Indizes der relevanten physischen Partition auf Daten überprüft.

## <a name="avoiding-cross-partition-queries"></a>Vermeiden partitionsübergreifender Abfragen

Bei den meisten Containern sind einige partitionsübergreifende Abfragen unvermeidlich. Einige partitionsübergreifende Abfragen sind OK! Fast alle Abfragevorgänge werden partitionsübergreifend unterstützt (sowohl logische Partitionsschlüssel als auch physische Partitionen). Azure Cosmos DB bietet auch viele Optimierungen in Abfrage-Engine und Client-SDKs, um die Abfrageausführung physische Partitionen übergreifend zu parallelisieren.

Bei den meisten Szenarien mit vielen Lesevorgängen sollten Sie einfach die gängigste Eigenschaft in den Abfragefiltern auswählen. Außerdem sollten Sie sicherstellen, dass Ihr Partitionsschlüssel anderen [bewährten Methoden für die Auswahl von Partitionsschlüsseln](partitioning-overview.md#choose-partitionkey) entspricht.

Die Vermeidung partitionsübergreifender Abfragen ist in der Regel nur bei großen Containern von Bedeutung. Jedes Mal, wenn Sie den Index einer physischen Partition auf Ergebnisse überprüfen, werden Ihnen mindestens ungefähr 2,5 RUs in Rechnung gestellt, auch wenn keine Elemente in der physischen Partition mit dem Filter der Abfrage übereinstimmen. Wenn Sie also nur über eine (oder nur ein paar) physische Partitionen verfügen, verbrauchen partitionsübergreifende Abfragen nicht wesentlich mehr RUs als Abfragen innerhalb von Partitionen.

Die Anzahl der physischen Partitionen ist an die Menge der bereitgestellten RUs gebunden. Jede physische Partition ermöglicht bis zu 10.000 bereitgestellte RUs und kann bis zu 50 GB Daten speichern. Azure Cosmos DB verwaltet physische Partitionen automatisch für Sie. Die Anzahl der physischen Partitionen in Ihrem Container hängt von dem bereitgestellten Durchsatz und dem verbrauchten Speicher ab.

Sie sollten versuchen, partitionsübergreifende Abfragen zu vermeiden, wenn die Workload die folgenden Kriterien erfüllt:
- Sie planen die Bereitstellung von über 30.000 RUs.
- Sie planen, mehr als 100 GB Daten zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Partitionierung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Erstellen eines synthetischen Partitionsschlüssels)
