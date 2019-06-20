---
title: Migrieren nicht partitionierter Azure Cosmos DB-Container zu partitionierten Containern
description: Erfahren Sie, wie Sie all Ihre vorhandenen nicht partitionierten Container zu partitionierten Containern migrieren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241925"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrieren nicht partitionierter Container zu partitionierten Containern

Azure Cosmos DB unterstützt das Erstellen von Containern ohne Partitionsschlüssel. Derzeit können Sie nicht partitionierte Container mithilfe der Azure CLI und der Azure Cosmos DB SDKs (.NET, Java, Node.js) erstellen, deren Version 2.x oder früher entspricht. Sie können nicht partitionierte Container nicht über das Azure-Portal erstellen. Diese nicht partitionierten Container sind jedoch nicht elastisch und verfügen über eine feste Speicherkapazität von 10 GB und einen begrenzten Durchsatz von 10.000 RU/s.

Die nicht partitionierten Container sind veraltet. Sie sollten Ihre vorhandenen nicht partitionierten Container zu partitionierten Containern migrieren, um den Speicher und den Durchsatz zu skalieren. Azure Cosmos DB stellt einen systemdefinierten Mechanismus zum Migrieren Ihrer nicht partitionierten Container zu partitionierten Containern bereit. In diesem Artikel wird erläutert, wie Sie all Ihre vorhanden nicht partitionierten Container automatisch zu partitionierten Containern migrieren. Sie können das Feature für die automatische Migration nur verwenden, wenn Sie die V3-Version der SDKs für die jeweiligen Sprachen verwenden.

> [!NOTE] 
> Derzeit können Sie die in diesem Artikel beschriebenen Schritte nicht dazu nutzen, MongoDB- und Gremlin-API-Konten von Azure Cosmos DB zu migrieren. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrieren von Containern mithilfe des systemdefinierten Partitionsschlüssels

Azure Cosmos DB definiert einen systemdefinierten Partitionsschlüssel namens `/_partitionkey` für alle Container, die über keinen Partitionsschlüssel verfügen, um die Migration zu unterstützen. Sie können die Partitionsschlüsseldefinition nicht ändern, nachdem die Container migriert wurden. Die Definition eines Containers, der zu einem partitionierten Container migriert wird, lautet beispielsweise wie folgt: 

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Das vollständige Beispiel finden Sie im GitHub-Repository mit [.NET-Beispielen](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples). 
                      
## <a name="migrate-the-documents"></a>Migrieren der Dokumente

Während die Containerdefinition mit einer Partitionsschlüsseleigenschaft erweitert wird, werden die Dokumente im Container nicht automatisch migriert. Das heißt, der Pfad `/_partitionKey` der Partitionsschlüsseleigenschaft wird nicht automatisch zu den vorhandenen Dokumenten hinzugefügt. Sie müssen die vorhandenen Dokumente neu partitionieren, indem die Dokumente gelesen werden, die ohne einen Partitionsschlüssel erstellt wurden, und erneut mit der Eigenschaft `_partitionKey` in den Dokumenten geschrieben werden. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Zugreifen auf Dokumente ohne Partitionsschlüssel

Anwendungen können mithilfe einer speziellen Systemeigenschaft namens „CosmosContainerSettings.NonePartitionKeyValue“ auf die vorhandenen Dokumente zugreifen, die über keinen Partitionsschlüssel verfügen. Dies ist der Wert für die Dokumente, die nicht migriert wurden. Sie können diese Eigenschaft in allen CRUD- und Abfragevorgängen verwenden. Im folgenden Beispiel wird ein Ausschnitt zum Lesen eines einzelnen Dokuments aus NonePartitionKey veranschaulicht. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Das vollständige Beispiel für die Neupartitionierung der Dokumente finden Sie im GitHub-Repository mit [.NET-Beispielen](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples). 

## <a name="compatibility-with-sdks"></a>Kompatibilität mit SDKs

Ältere Versionen der Azure Cosmos DB SDKs wie V2.x.x und V1.x.x unterstützen die systemdefinierte Partitionsschlüsseleigenschaft nicht. Wenn Sie also die Containerdefinition eines älteren SDKs lesen, enthält diese keine Partitionsschlüsseldefinition, und die Container verhalten sich genau wie zuvor. Anwendungen, die mit den älteren Version des SDKs erstellt wurden, funktionieren weiterhin und unverändert mit nicht partitionierten Containern. 

Wenn ein migrierter Container von der neuesten bzw. der V3-Version eines SDKs verwendet wird und Sie den systemdefinierten Partitionsschlüssel in den neuen Dokumenten auffüllen, können Sie nicht mehr über ältere SDKs auf diese Dokumente zugreifen (Lese-, Update-, Lösch-, Abfragevorgänge).

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](account-overview.md)