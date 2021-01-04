---
title: Parametrisierte Abfragen in Azure Cosmos DB
description: Hier erfahren Sie, wie parametrisierte SQL-Abfragen stabile Verarbeitung und Schutz von Benutzereingaben bieten und eine versehentliche Offenlegung von Daten durch SQL-Injektion verhindern.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549156"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametrisierte Abfragen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB unterstützt Abfragen mit Parametern, die durch die bekannte @-Notation ausgedrückt werden. Parametrisiertes SQL bietet stabile Fehlerbehandlung und Schutz von Benutzereingaben und verhindert eine versehentliche Offenlegung von Daten durch SQL-Injektion.

## <a name="examples"></a>Beispiele

Sie können z.B. eine Abfrage erstellen, die `lastName` und `address.state` als Parameter verwendet, und sie für unterschiedliche `lastName`- und `address.state`-Werte auf Grundlage von Benutzereingaben ausführen.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Sie können diese Anforderung dann als parametrisierte JSON-Abfrage wie die folgende an Azure Cosmos DB senden:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Im folgenden Beispiel wird das Argument für TOP mit einer parametrisierten Abfrage festgelegt:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameterwerte können alle gültigen JSON-Werte sein: Zeichenfolgen, Zahlen, boolesche Werte, Null, Arrays oder verschachteltes JSON. Da Azure Cosmos DB schemalos ist, werden Parameter für keinen Typ überprüft.

Unter den folgenden Links finden Sie Beispiele für parametrisierte Abfragen im jeweiligen Azure Cosmos DB SDK:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Nächste Schritte

- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdaten](modeling-data.md)
