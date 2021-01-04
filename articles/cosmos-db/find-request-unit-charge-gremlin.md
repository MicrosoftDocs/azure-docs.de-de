---
title: Ermitteln der Gebühr für Anforderungseinheiten (Request Unit, RU) für Gremlin-API-Abfragen in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für Gremlin-Abfragen ermitteln, die für einen Azure Cosmos-Container ausgeführt wurden. Sie können das Azure-Portal oder die .NET- und Java-Treiber verwenden, um die RU-Gebühr zu ermitteln.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8393fb431c5148d3f4885135c90fe4d0b8970d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082010"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge, die in der Gremlin-API für Azure Cosmos DB ausgeführt werden
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Sie können sich Anforderungseinheiten als Währung zur Abstrahierung der Systemressourcen wie CPU, IOPS und Arbeitsspeicher vorstellen, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen. Weitere Informationen finden Sie im Artikel [Aspekte zu Anforderungseinheiten](request-units.md).

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RU) für einen beliebigen Vorgang ermitteln können, der für einen Container in der Gremlin-API für Azure Cosmos DB ausgeführt wurde. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Ermitteln der RU/s-Gebühr unter [API für MongoDB](find-request-unit-charge-mongodb.md), [Cassandra-API](find-request-unit-charge-cassandra.md), [SQL-API](find-request-unit-charge.md) und [Tabellen-API](find-request-unit-charge-table.md).

Von der Gremlin-API zurückgegebene Header werden benutzerdefinierten Statusattributen zugeordnet, die aktuell durch das Gremlin .NET SDK und das Gremlin Java SDK bereitgestellt werden. Die Anforderungsgebühr ist unter dem Schlüssel `x-ms-request-charge` verfügbar. Wenn Sie die Gremlin-API verwenden, bestehen mehrere Optionen zum Ermitteln des RU-Verbrauchs für einen Vorgang in einem Azure Cosmos-Container.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-graph-gremlin-console.md#create-a-database-account), und fügen Sie ihm Daten hinzu, oder wählen Sie ein vorhandenes Konto aus, das bereits Daten enthält.

1. Wechseln Sie zum Bereich **Daten-Explorer** , und wählen Sie dann den gewünschten Container aus.

1. Geben Sie eine gültige Abfrage ein, und wählen Sie dann **Execute Gremlin Query** (Gremlin-Abfrage ausführen) aus.

1. Wählen Sie **Query Stats** (Abfragestatistik) aus, um die tatsächliche Anforderungsgebühr für die ausgeführte Anforderung anzuzeigen.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Screenshot zum Abrufen der Gebühr für die Anforderung einer Gremlin-Abfrage im Azure-Portal":::

## <a name="use-the-net-sdk-driver"></a>Verwenden des .NET SDK-Treibers

Wenn Sie das [Gremlin .NET SDK](https://www.nuget.org/packages/Gremlin.Net/) verwenden, stehen Statusattribute unter der Eigenschaft `StatusAttributes` des Objekts `ResultSet<>` zur Verfügung:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET Framework- oder Core-Anwendung mithilfe eines Gremlin-API-Kontos für Azure Cosmos DB](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Verwenden des Java SDK-Treibers

Wenn Sie das [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) verwenden, können Sie Statusattribute durch Aufrufen der `statusAttributes()`-Methode für das `ResultSet`-Objekt abrufen:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Graphdatenbank in Azure Cosmos DB mithilfe des Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)