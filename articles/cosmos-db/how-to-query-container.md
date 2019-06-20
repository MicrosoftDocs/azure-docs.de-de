---
title: Abfragen von Containern in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Container in Azure Cosmos DB abfragen.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: cf14e005de3710f26bfdbab7cc0dac87e0cf000e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243747"
---
# <a name="query-an-azure-cosmos-container"></a>Abfragen eines Azure Cosmos-Containers

In diesem Artikel erfahren Sie, wie Sie einen Container (Sammlung, Diagramm oder Tabelle) in Azure Cosmos DB abfragen.

## <a name="in-partition-query"></a>Abfrage innerhalb einer Partition

Beim Abfragen von Daten aus Containern wird die Abfrage von Azure Cosmos DB automatisch verarbeitet, wenn für die Abfrage ein Partitionsschlüsselfilter angegeben ist. Die Abfrage wird an die Partitionen weitergeleitet, die den im Filter angegebenen Partitionsschlüsselwerten entsprechen. Die folgende Abfrage wird beispielsweise an die Partition `DeviceId` weitergeleitet, die alle Dokumente enthält, die dem Partitionsschlüsselwert `XMS-0001` entsprechen.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Partitionsübergreifende Abfrage

Die folgende Abfrage verfügt nicht über einen Filter für den Partitionsschlüssel (`DeviceId`) und wird an alle Partitionen verteilt, wo sie für den Partitionsindex ausgeführt wird. Legen Sie zum partitionsübergreifenden Ausführen einer Abfrage `EnableCrossPartitionQuery` auf „true“ (bzw. `x-ms-documentdb-query-enablecrosspartition` in der REST-API) fest.

Die Eigenschaft „EnableCrossPartitionQuery“ akzeptiert einen booleschen Wert. Wenn die Eigenschaft auf „true“ festgelegt ist und die Abfrage über keinen Partitionsschlüssel verfügt, verteilt Azure Cosmos DB die Abfrage auf die Partitionen. Für die Verteilung werden einzelne Abfragen an alle Partitionen gerichtet. Zum Lesen der Abfrageergebnisse müssen die Clientanwendungen die Ergebnisse aus „FeedResponse“ auf die Eigenschaft „ContinuationToken“ überprüfen. Um alle Ergebnisse zu lesen, durchlaufen Sie die Daten so lange, bis „ContinuationToken“ NULL ist. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB unterstützt die Aggregatfunktionen „COUNT“, „MIN“, „MAX“ und „AVG“ für Container mithilfe von SQL. Die Aggregatfunktionen für Container sind ab der SDK-Version 1.12.0 verfügbar. Abfragen müssen einen einzelnen Aggregate-Operator und einen einzelnen Wert in der Projektion enthalten.

## <a name="parallel-cross-partition-query"></a>Parallele partitionsübergreifende Abfrage

Azure Cosmos DB SDKs ab Version 1.9.0 unterstützen Optionen für die Ausführung paralleler Abfragen. Parallele partitionsübergreifende Abfragen ermöglichen partitionsübergreifende Abfragen mit geringer Wartezeit. Die folgende Abfrage ist z.B. so konfiguriert, dass sie partitionsübergreifend parallel ausgeführt wird.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Sie können die parallele Ausführung von Abfragen verwalten, indem Sie die folgenden Parameter optimieren:

- **MaxDegreeOfParallelism**: Legt die maximale Anzahl gleichzeitiger Verbindungen mit den Partitionen des Containers fest. Wenn Sie diese Eigenschaft auf „-1“ festlegen, verwaltet das SDK den Grad der Parallelität. Falls  `MaxDegreeOfParallelism` nicht angegeben oder auf 0 (Standardwert) festgelegt wird, besteht eine einzelne Netzwerkverbindung mit den Partitionen des Containers.

- **MaxBufferedItemCount**: Steuert das Verhältnis zwischen Abfragewartezeit und clientseitiger Arbeitsspeichernutzung. Wird diese Option weggelassen oder auf „-1“ festgelegt, verwaltet das SDK die Anzahl von Elementen, die während der Ausführung paralleler Abfragen gepuffert werden.

Bei gleichem Zustand der Sammlung gibt eine parallele Abfrage Ergebnisse in der gleichen Reihenfolge wie bei einer seriellen Ausführung zurück. Bei einer partitionsübergreifenden Abfrage mit Sortieroperatoren (ORDER BY, TOP) gibt das Azure Cosmos DB SDK die Abfrage parallel an verschiedene Partitionen aus. Es führt teilweise sortierte Ergebnisse auf der Clientseite zusammen, um global sortierte Ergebnisse zu generieren.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Partitionierung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Erstellen eines synthetischen Partitionsschlüssels)
