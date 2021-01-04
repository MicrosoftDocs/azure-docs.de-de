---
title: Migrieren nicht partitionierter Azure Cosmos-Container zu partitionierten Containern
description: Erfahren Sie, wie Sie all Ihre vorhandenen nicht partitionierten Container zu partitionierten Containern migrieren.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: edb6114406922d55c439ae7426a2be933bba4aee
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334088"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrieren nicht partitionierter Container zu partitionierten Containern
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB unterstützt das Erstellen von Containern ohne Partitionsschlüssel. Derzeit können Sie nicht partitionierte Container mithilfe der Azure CLI und der Azure Cosmos DB SDKs (.NET, Java, Node.js) erstellen, deren Version 2.x oder früher entspricht. Sie können nicht partitionierte Container nicht über das Azure-Portal erstellen. Diese nicht partitionierten Container sind jedoch nicht elastisch und verfügen über eine feste Speicherkapazität von 20 GB und einen begrenzten Durchsatz von 10.000 RU/s.

Die nicht partitionierten Container sind veraltet. Sie sollten Ihre vorhandenen nicht partitionierten Container zu partitionierten Containern migrieren, um den Speicher und den Durchsatz zu skalieren. Azure Cosmos DB stellt einen systemdefinierten Mechanismus zum Migrieren Ihrer nicht partitionierten Container zu partitionierten Containern bereit. In diesem Artikel wird erläutert, wie Sie all Ihre vorhanden nicht partitionierten Container automatisch zu partitionierten Containern migrieren. Sie können das Feature für die automatische Migration nur verwenden, wenn Sie die V3-Version der SDKs für die jeweiligen Sprachen verwenden.

> [!NOTE]
> Derzeit können Sie die in diesem Artikel beschriebenen Schritte nicht dazu nutzen, MongoDB- und Gremlin-API-Konten von Azure Cosmos DB zu migrieren.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrieren von Containern mithilfe des systemdefinierten Partitionsschlüssels

Um die Migration zu unterstützen, bietet Azure Cosmos DB einen systemdefinierten Partitionsschlüssel namens `/_partitionkey` für alle Container, die keinen Partitionsschlüssel haben. Sie können die Partitionsschlüsseldefinition nicht ändern, nachdem die Container migriert wurden. Die Definition eines Containers, der zu einem partitionierten Container migriert wird, lautet beispielsweise wie folgt:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Nachdem der Container migriert wurde, können Sie Dokumente erstellen, indem Sie die `_partitionKey`-Eigenschaft sowie die anderen Eigenschaften des Dokuments auffüllen. Die Eigenschaft `_partitionKey` stellt den Partitionsschlüssel Ihrer Dokumente dar.

Es ist wichtig, den richtigen Partitionsschlüssel auszuwählen, um den bereitgestellten Durchsatz optimal zu nutzen. Weitere Informationen finden Sie im Artikel [Partitionierung in Azure Cosmos DB](partitioning-overview.md).

> [!NOTE]
> Sie können systemdefinierte Partitionsschlüssel nur nutzen, wenn Sie die neueste bzw. die V3-Version der SDKs für die jeweiligen Sprachen verwenden.

Im folgenden Beispiel wird ein Beispielcode zum Erstellen und Lesen eines Dokuments mit dem systemdefinierten Partitionsschlüssel veranschaulicht:

**JSON-Darstellung des Dokuments**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Das vollständige Beispiel finden Sie im GitHub-Repository mit [.NET-Beispielen][1].
                      
## <a name="migrate-the-documents"></a>Migrieren der Dokumente

Während die Containerdefinition mit einer Partitionsschlüsseleigenschaft erweitert wird, werden die Dokumente im Container nicht automatisch migriert. Das heißt, der Pfad `/_partitionKey` der Partitionsschlüsseleigenschaft wird nicht automatisch zu den vorhandenen Dokumenten hinzugefügt. Sie müssen die vorhandenen Dokumente neu partitionieren, indem die Dokumente gelesen werden, die ohne einen Partitionsschlüssel erstellt wurden, und erneut mit der Eigenschaft `_partitionKey` in den Dokumenten geschrieben werden.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Zugreifen auf Dokumente ohne Partitionsschlüssel

Anwendungen können mithilfe der speziellen Systemeigenschaft PartitionKey.None auf die vorhandenen Dokumente zugreifen, die nicht über einen Partitionsschlüssel verfügen. Dies ist der Wert für die Dokumente, die nicht migriert wurden. Sie können diese Eigenschaft in allen CRUD- und Abfragevorgängen verwenden. Im folgenden Beispiel wird ein Ausschnitt zum Lesen eines einzelnen Dokuments aus NonePartitionKey veranschaulicht. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Das vollständige Beispiel für die Neupartitionierung der Dokumente finden Sie im GitHub-Repository mit [.NET-Beispielen][1]. 

## <a name="compatibility-with-sdks"></a>Kompatibilität mit SDKs

Ältere Versionen der Azure Cosmos DB SDKs wie V2.x.x und V1.x.x unterstützen die systemdefinierte Partitionsschlüsseleigenschaft nicht. Wenn Sie also die Containerdefinition eines älteren SDKs lesen, enthält diese keine Partitionsschlüsseldefinition, und die Container verhalten sich genau wie zuvor. Anwendungen, die mit den älteren Version des SDKs erstellt wurden, funktionieren weiterhin und unverändert mit nicht partitionierten Containern. 

Wenn ein migrierter Container von der neuesten bzw. der V3-Version eines SDKs verwendet wird und Sie den systemdefinierten Partitionsschlüssel in den neuen Dokumenten auffüllen, können Sie nicht mehr über ältere SDKs auf diese Dokumente zugreifen (Lese-, Update-, Lösch-, Abfragevorgänge).

## <a name="known-issues"></a>Bekannte Probleme

**Das Abfragen der Anzahl von Elementen, die ohne Partitionsschlüssel mit dem V3 SDK eingefügt wurden, kann zu einem höheren verbrauchten Durchsatz führen.**

Wenn Sie beim V3 SDK die mit dem V2 SDK eingefügten Elemente oder die mit dem V3 SDK eingefügten Elemente mit dem Parameter `PartitionKey.None` abfragen, kann die Zählerabfrage mehr RU/s verbrauchen, wenn der Parameter `PartitionKey.None` in den FeedOptions bereitgestellt wird. Es wird empfohlen, den Parameter `PartitionKey.None` nicht anzugeben, wenn keine anderen Elemente mit einem Partitionsschlüssel eingefügt werden.

Wenn neue Elemente mit anderen Werten für den Partitionsschlüssel eingefügt werden, wird die Abfrage nach solchen Elementanzahlen durch die Übergabe des entsprechenden Schlüssels in `FeedOptions` keine Probleme verursachen. Wenn Sie nach dem Einfügen neuer Dokumente mit dem Partitionsschlüssel nur die Anzahl der Dokumente ohne den Wert des Partitionsschlüssels abfragen müssen, kann diese Abfrage wieder zu höheren RU/s führen, ähnlich wie bei den regulären partitionierten Sammlungen.

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration