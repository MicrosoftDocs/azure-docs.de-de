---
title: Ermitteln der Gebühr für Anforderungseinheiten (Request Unit, RU) für eine Cassandra-API-Abfrage in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für Cassandra-Abfragen ermitteln, die für einen Azure Cosmos-Container ausgeführt wurden. Sie können das Azure-Portal oder die .NET- und Java-Treiber verwenden, um die RU-Gebühr zu ermitteln.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8f7db5dc48f7fec1bcf2bcd1291b67da7b4412c7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094811"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge, die in der Cassandra-API für Azure Cosmos DB ausgeführt werden
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Sie können sich Anforderungseinheiten als Währung zur Abstrahierung der Systemressourcen wie CPU, IOPS und Arbeitsspeicher vorstellen, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen. Weitere Informationen finden Sie im Artikel [Aspekte zu Anforderungseinheiten](request-units.md).

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RU) für einen beliebigen Vorgang ermitteln können, der für einen Container in der Cassandra-API von Azure Cosmos DB ausgeführt wurde. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Ermitteln der RU/s-Gebühr in den Artikeln zur [API für MongoDB](find-request-unit-charge-mongodb.md), [SQL-API](find-request-unit-charge.md), [Gremlin-API](find-request-unit-charge-gremlin.md) und [Tabellen-API](find-request-unit-charge-table.md).

Wenn Sie Vorgänge für die Cassandra-API von Azure Cosmos DB ausführen, wird die RU-Gebühr in der eingehenden Nutzlast als Feld namens `RequestCharge` zurückgegeben. Sie haben mehrere Optionen zum Abrufen der RU-Gebühr.

## <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

Wenn Sie das [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) verwenden, können Sie die eingehende Nutzlast unter der `Info`-Eigenschaft eines `RowSet`-Objekts abrufen:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Cassandra-App mithilfe des .NET-SDK und Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Wenn Sie das [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) verwenden, können Sie die eingehende Nutzlast durch Aufrufen der `getExecutionInfo()`-Methode für ein `ResultSet`-Objekt abrufen:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Cassandra-App mithilfe des Java SDK und Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)