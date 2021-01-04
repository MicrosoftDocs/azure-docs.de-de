---
title: Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge in der Azure Cosmos DB-API für MongoDB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für MongoDB-Abfragen ermitteln, die für einen Azure Cosmos-Container ausgeführt wurden. Sie können das Azure-Portal oder die .NET-, Java- und Node.js-Treiber von MongoDB verwenden.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b7d880183ac5f920bbed1a85d7660db6a8f21462
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078474"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge, die in der Azure Cosmos DB-API für MongoDB ausgeführt werden
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Sie können sich Anforderungseinheiten als Währung zur Abstrahierung der Systemressourcen wie CPU, IOPS und Arbeitsspeicher vorstellen, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen. Weitere Informationen finden Sie im Artikel [Aspekte zu Anforderungseinheiten](request-units.md).

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RU) für einen beliebigen Vorgang ermitteln können, der für einen Container in der Azure Cosmos DB-API für MongoDB ausgeführt wurde. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Ermitteln der RU/s-Gebühr in den Artikeln zur [SQL-API](find-request-unit-charge.md), [Cassandra-API](find-request-unit-charge-cassandra.md), [Gremlin-API](find-request-unit-charge-gremlin.md) und [Tabellen-API](find-request-unit-charge-table.md).

Die RU-Gebühr wird durch einen benutzerdefinierten [Datenbankbefehl](https://docs.mongodb.com/manual/reference/command/) namens `getLastRequestStatistics` verfügbar gemacht. Der Befehl gibt ein Dokument zurück, das den Namen des letzten ausgeführten Vorgangs sowie dessen Anforderungsgebühr und Dauer enthält. Wenn Sie die Azure Cosmos DB-API für MongoDB verwenden, haben Sie mehrere Optionen zum Abrufen der RU-Gebühr.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), und fügen Sie ihm Daten hinzu, oder wählen Sie ein vorhandenes Konto aus, das bereits Daten enthält.

1. Wechseln Sie zum Bereich **Daten-Explorer** , und wählen Sie dann den gewünschten Container aus.

1. Wählen Sie **Neue Abfrage** aus.

1. Geben Sie eine gültige Abfrage ein, und wählen Sie dann **Abfrage ausführen** aus.

1. Wählen Sie **Query Stats** (Abfragestatistik) aus, um die tatsächliche Anforderungsgebühr für die ausgeführte Anforderung anzuzeigen.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Screenshot: Anforderungsgebühr für eine MongoDB-Abfrage im Azure-Portal":::

## <a name="use-the-mongodb-net-driver"></a>Verwenden des .NET-Treibers von MongoDB

Wenn Sie den [offiziellen MongoDB .NET-Treiber](https://docs.mongodb.com/ecosystem/drivers/csharp/) verwenden, können Sie Befehle durch Aufrufen der `RunCommand`-Methode für ein `IMongoDatabase`-Objekt ausführen. Diese Methode erfordert eine Implementierung der abstrakten Klasse `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET-Web-App mithilfe einer Azure Cosmos DB-API für MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Verwenden des Java-Treibers von MongoDB


Wenn Sie den [offiziellen MongoDB Java-Treiber](https://mongodb.github.io/mongo-java-driver/) verwenden, können Sie Befehle durch Aufrufen der `runCommand`-Methode für ein `MongoDatabase`-Objekt ausführen:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Web-App mithilfe der Azure Cosmos DB-API für MongoDB und des Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Verwenden des Node.js-Treibers von MongoDB

Wenn Sie den [offiziellen MongoDB Node.js-Treiber](https://mongodb.github.io/node-mongodb-native/) verwenden, können Sie Befehle durch Aufrufen der `command`-Methode für ein `db`-Objekt ausführen:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Weitere Informationen finden Sie unter [Quickstart: Migrieren einer vorhandenen MongoDB-Node.js-Web-App zu Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)