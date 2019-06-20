---
title: Erstellen von Azure Cosmos DB-Containern mit großen Partitionsschlüsseln über das Azure-Portal und mithilfe verschiedener SDKs
description: Erfahren Sie, wie Sie einen Container in Azure Cosmos DB mit einem großen Partitionsschlüssel über das Azure-Portal und mithilfe verschiedener SDKs erstellen können.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 33f871564b7c8435395db6b97122ba6a75800271
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225996"
---
# <a name="create-containers-with-large-partition-key"></a>Erstellen von Containern mit großen Partitionsschlüsseln

Azure Cosmos DB verwendet hashbasierte Partitionsschemas, um Daten horizontal skalieren zu können. Alle Azure Cosmos DB-Container, die vor dem 3. März 2019 erstellt wurden, verwenden eine Hashfunktion, die den Hash aus den ersten 100 Byte des Partitionsschlüssels berechnet. Wenn es mehrere Partitionsschlüssel gibt, deren ersten 100 Byte übereinstimmen, werden diese logischen Partitionen vom Dienst als dieselbe logische Partition angesehen. Das kann zu Problemen führen. Z. B. kann es sein, dass das Partitionsgrößenkontingent nicht richtig ist oder dass eindeutige Indizes für alle Partitionsschlüssel angewendet werden. Zur Behebung dieses Problems werden große Partitionsschlüssel eingeführt. Azure Cosmos DB unterstützt jetzt große Partitionsschlüssel mit einer Größe von bis zu 2 KB. 

Große Partitionsschlüssel werden durch eine Funktionalität einer verbesserten Version der Hashfunktion unterstützt, durch die ein eindeutiger Hash für große Partitionsschlüssel mit einer Größe von bis zu 2 KB generiert werden kann. Diese Hashversion wird auch für Szenarios mit hoher Partitionsschlüsselkardinalität unabhängig von der Größe des Partitionsschlüssels empfohlen. Die Partitionsschlüsselkardinalität wird als Anzahl der eindeutigen logischen Partitionen definiert, z. B. ~30.000 logische Partitionen in einem Container. In diesem Artikel wird beschrieben, wie Sie einen Container in Azure Cosmos DB mit einem großen Partitionsschlüssel über das Azure-Portal und mithilfe verschiedener SDKs erstellen können. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Erstellen eines großen Partitionsschlüssels (.NET SDK Version 2)

Wenn Sie das .NET SDK verwenden, um einen Container mit einem großen Partitionsschlüssel zu erstellen, sollten Sie die Eigenschaft `PartitionKeyDefinitionVersion.V2` angeben. Im folgenden Beispiel wird veranschaulicht, wie Sie die Version-Eigenschaft im PartitionKeyDefinition-Objekt angeben und auf PartitionKeyDefinitionVersion.V2 festlegen können:

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Erstellen eines großen Partitionsschlüssels (Azure-Portal) 

Aktivieren Sie beim Erstellen eines neuen Containers über das Azure-Portal die Option **My partition key is larger than 100 bytes** (Mein Partitionsschlüssel ist größer als 100 Byte), um einen großen Partitionsschlüssel zu erstellen. Das Verwenden großer Partitionsschlüssel ist für alle neuen Container standardmäßig aktiviert. Deaktivieren Sie das Kontrollkästchen, wenn Sie keine großen Partitionsschlüssel benötigen oder wenn Sie Anwendungen verwenden, die mit SDK-Versionen vor 1.18 ausgeführt werden.

![Erstellen eines großen Partitionsschlüssels über das Azure-Portal](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>Unterstützte SDK-Versionen

Die Schlüssel für große Partitionen werden mit den folgenden Mindestversionen von SDKs unterstützt:

|SDK-Typ  | Mindestversion   |
|---------|---------|
|.Net     |    1.18     |
|Java sync     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST-API | Version höher als `2017-05-03` unter Verwendung des Anforderungsheaders `x-ms-version`.|

Derzeit können Sie in Power BI und Azure Logic Apps keine Container mit großem Partitionsschlüssel verwenden. Sie können Container ohne große Partitionsschlüssel in diesen Anwendungen verwenden. 
 
## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](account-overview.md)


