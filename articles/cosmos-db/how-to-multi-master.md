---
title: Konfigurieren von Multimaster-Features in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Multimaster in Ihren Anwendungen in Azure Cosmos DB konfigurieren.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 1d9fa7380f62165d360888fd8cb03919f1736297
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244746"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurieren von Multimaster in Ihren Anwendungen, die Azure Cosmos DB verwenden

Damit Sie das Multimaster-Feature in Ihren Anwendungen nutzen können, müssen Sie Multiregionen-Schreibvorgänge (Schreibvorgänge in mehreren Regionen) aktivieren und die Multihoming-Funktionalität in Azure Cosmos DB konfigurieren. Um Multihoming zu konfigurieren, legen Sie die Region fest, in der die Anwendung bereitgestellt wird.

## <a id="netv2"></a>.NET SDK v2

Um Multimaster in Ihrer Anwendung zu aktivieren, legen Sie `UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `SetCurrentLocation` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Azure Cosmos DB repliziert wird:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (Vorschauversion)

Um Multimaster in Ihrer Anwendung zu aktivieren, legen Sie `UseCurrentRegion` auf die Region fest, in der die Anwendung bereitgestellt und wo Cosmos DB repliziert wird:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Um Multimaster in Ihrer Anwendung zu aktivieren, legen Sie `policy.setUsingMultipleWriteLocations(true)` fest, und legen Sie `policy.setPreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt und wo Cosmos DB repliziert wird:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js, JavaScript und TypeScript SDK

Um Multimaster in Ihrer Anwendung zu aktivieren, legen Sie `connectionPolicy.UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `connectionPolicy.PreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Cosmos DB repliziert wird:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

Um Multimaster in Ihrer Anwendung zu aktivieren, legen Sie `connection_policy.UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `connection_policy.PreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Cosmos DB repliziert wird.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel:

* [Verwalten von Konsistenzebenen in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttypen und Konfliktauflösungsrichtlinien](conflict-resolution-policies.md)
* [Hochverfügbarkeit mit Azure Cosmos DB](high-availability.md)
* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Auswählen der richtigen Konsistenzebene](consistency-levels-choosing.md)
* [Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung](consistency-levels-tradeoffs.md)
* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](consistency-levels-tradeoffs.md)
* [Globales Skalieren von bereitgestelltem Durchsatz](scaling-throughput.md)
* [Globale Verteilung: Im Hintergrund](global-dist-under-the-hood.md)
