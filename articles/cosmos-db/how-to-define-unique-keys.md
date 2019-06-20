---
title: Definieren von eindeutigen Schlüsseln für einen Azure Cosmos-Container
description: Erfahren Sie, wie Sie eindeutige Schlüssel für einen Azure Cosmos-Container definieren.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: fb9872d2fd41066899ff9198915d573bfb4a0b84
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240972"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definieren von eindeutigen Schlüsseln für einen Azure Cosmos-Container

In diesem Artikel sind die verschiedenen Methoden zum Definieren von [eindeutigen Schlüsseln](unique-keys.md) dargestellt, wenn ein Azure Cosmos-Container erstellt wird. Derzeit kann dieser Vorgangs über das Azure-Portal oder eines der SDKs ausgeführt werden.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie den gewünschten Container aus.

1. Klicken Sie auf **Neuer Container**.

1. Klicken Sie im Dialogfeld **Container hinzufügen** auf **+ Eindeutigen Schlüssel hinzufügen**, um einen eindeutigen Schlüsseleintrag hinzuzufügen.

1. Geben Sie die Pfade der Einschränkung für eindeutige Schlüssel ein.

1. Fügen Sie bei Bedarf weitere eindeutige Einträge hinzu, indem Sie auf **+ Eindeutigen Schlüssel hinzufügen** klicken.

![Screenshot von Einschränkungseinträgen für eindeutige Schlüssel im Azure-Portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Verwenden des .NET SDK v2

Wenn ein neuer Container mit dem [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) erstellt wird, kann ein `UniqueKeyPolicy`-Objekt verwendet werden, um Einschränkungen für eindeutige Schlüssel zu definieren.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Wenn ein neuer Container mit dem [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) erstellt wird, kann ein `UniqueKeyPolicy`-Objekt verwendet werden, um Einschränkungen für eindeutige Schlüssel zu definieren.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");
// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");
// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);
// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);
// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);
// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Verwenden des Node.js SDK

Wenn ein neuer Container mit dem [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) erstellt wird, kann ein `UniqueKeyPolicy`-Objekt verwendet werden, um Einschränkungen für eindeutige Schlüssel zu definieren.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Verwenden des Python SDK

Wenn ein neuer Container mit dem [Python SDK](https://pypi.org/project/azure-cosmos/) erstellt wird, können Einschränkungen für eindeutige Schlüssel als Teil des Verzeichnisses angegeben werden, das als Parameter übergeben wird.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Partitionierung](partition-data.md)
- Erfahren Sie, [wie Indizierung funktioniert](index-overview.md).