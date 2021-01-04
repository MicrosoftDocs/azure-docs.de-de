---
title: 'Tutorial: Tutorial zur globalen Verteilung von Azure Cosmos DB mit der SQL-API'
description: 'Tutorial: Hier erfahren Sie, wie Sie die globale Verteilung von Azure Cosmos DB mithilfe der SQL-API mit .NET, Java, Python und verschiedenen anderen SDKs einrichten.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: aacb8d4ffb98d553b17aa52e4c4b11a4837dc1c6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337879"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Tutorial: Einrichten der globalen Verteilung von Azure Cosmos DB mithilfe der SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal die globale Verteilung von Azure Cosmos DB einrichten und dann mithilfe der SQL-API eine Verbindung herstellen.

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit den [SQL-APIs](./introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Herstellen einer Verbindung mit einer bevorzugten Region mithilfe der SQL-API

Um von der [globalen Verteilung](distribute-data-globally.md)zu profitieren, können Clientanwendungen in einer Liste die Reihenfolge angeben, in der Regionen bei Dokumentvorgängen bevorzugt verwendet werden sollen. Basierend auf der Azure Cosmos DB-Kontokonfiguration, der aktuellen regionalen Verfügbarkeit und der angegebenen Liste der bevorzugten Regionen wählt das SQL SDK den optimalen Endpunkt für Schreib- und Lesevorgänge aus.

Diese Liste wird beim Initialisieren einer Verbindung mithilfe der SQL SDKs angegeben. Die SDKs akzeptieren einen optionalen Parameter `PreferredLocations`, bei dem es sich um eine sortierte Liste von Azure-Regionen handelt.

Das SDK sendet automatisch alle Schreibvorgänge an die aktuell für solche Vorgänge ausgewählte Region. Alle Lesevorgänge werden an die erste verfügbare Region in der Liste der bevorzugten Regionen gesendet. Wenn bei der Anforderung ein Fehler auftritt, führt der Client ein Failover zur nächsten Region auf der Liste durch.

Das SDK versucht nur in den Regionen, die als bevorzugte Regionen angegeben wurden, Lesevorgänge auszuführen. Wenn beispielsweise ein Azure Cosmos-Konto in vier Regionen verfügbar ist, der Client aber nur zwei Leseregionen (non-write) in `PreferredLocations` angibt, werden keine Lesevorgänge aus einer Leseregion verarbeitet, die nicht in `PreferredLocations` angegeben ist. Wenn die in der Liste `PreferredLocations` angegebenen Leseregionen nicht verfügbar sind, werden Lesevorgänge aus der Schreibregion verarbeitet.

Die Anwendung kann die vom SDK ausgewählten aktuellen Schreib- und Leseendpunkte anhand von zwei Eigenschaften überprüfen: `WriteEndpoint` und `ReadEndpoint`. Diese stehen im SDK der Version 1.8 und höher zur Verfügung. Wenn die Eigenschaft `PreferredLocations` nicht festgelegt ist, werden alle Anforderungen von der aktuellen Schreibregion verarbeitet.

Wenn Sie die bevorzugten Regionen nicht angeben, aber die `setCurrentLocation`-Methode verwendet haben, füllt das SDK die bevorzugten Regionen automatisch basierend auf der aktuellen Region auf, in der der Client ausgeführt wird. Das SDK ordnet die Regionen auf Grundlage der Nähe einer Region zur aktuellen Region an.

## <a name="net-sdk"></a>.NET SDK

Das SDK kann ohne Codeänderungen verwendet werden. In diesem Fall sendet das SDK automatisch sowohl Lese- als auch Schreibvorgänge an die aktuelle Schreibregion.

Im .NET SDK, Version 1.8 oder höher, besitzt der ConnectionPolicy-Parameter für den DocumentClient-Konstruktor folgende Eigenschaft: Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Diese Eigenschaft weist den Typ „Collection `<string>` “ auf und sollte eine Liste mit Regionsnamen enthalten. Die Zeichenfolgenwerte sind gemäß der Spalte mit den Regionsnamen auf der Seite [Azure-Regionen][regions] formatiert und enthalten keine Leerzeichen vor und nach dem ersten und letzten Zeichen.

Die aktuellen Schreib- und Leseendpunkte sind in DocumentClient.WriteEndpoint bzw. DocumentClient.ReadEndpoint verfügbar.

> [!NOTE]
> Die URLs für die Endpunkte sollten nicht als langfristige Konstanten betrachtet werden. Sie können jederzeit vom Dienst aktualisiert werden. Das SDK verarbeitet diese Änderung automatisch.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Wenn Sie das .NET V2 SDK verwenden, legen Sie die bevorzugte Region mithilfe der Eigenschaft `PreferredLocations` fest.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Sie können auch die Eigenschaft `SetCurrentLocation` verwenden und dem SDK erlauben, die bevorzugte Region basierend auf der Nähe auszuwählen.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Wenn Sie das .NET V3 SDK verwenden, legen Sie die bevorzugte Region mithilfe der Eigenschaft `ApplicationPreferredRegions` fest.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Sie können auch die Eigenschaft `ApplicationRegion` verwenden und dem SDK erlauben, die bevorzugte Region basierend auf der Nähe auszuwählen.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Die URLs für die Endpunkte sollten nicht als langfristige Konstanten betrachtet werden. Sie können jederzeit vom Dienst aktualisiert werden. Das SDK verarbeitet diese Änderung automatisch.
>
>

Im Folgenden finden Sie ein Codebeispiel für Node.js/Javascript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

Der folgende Code zeigt, wie Sie mit dem Python SDK bevorzugte Standorte festlegen:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java V4 SDK

Der folgende Code zeigt, wie Sie mit dem Java SDK bevorzugte Standorte festlegen:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Synchrone API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Sobald ein Datenbankkonto in mehreren Regionen zur Verfügung gestellt wurde, können Clients mithilfe einer GET-Anforderung über den URI `https://{databaseaccount}.documents.azure.com/` die Verfügbarkeit des Kontos abfragen.

Der Dienst gibt eine Liste der Regionen und der zugehörigen Azure Cosmos DB-Endpunkt-URIs für die Replikate zurück. Die aktuelle Schreibregion wird in der Antwort angegeben. Der Client kann dann wie folgt den geeigneten Endpunkt für alle weiteren REST-API-Anforderungen auswählen.

Beispielantwort

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Alle PUT-, POST- und DELETE-Anforderungen müssen an den angegebenen Schreib-URI gesendet werden.
* Alle GET-Anforderungen sowie weitere Anforderungen ohne Schreibzugriff (z. B. Abfragen) können an einen beliebigen vom Client ausgewählten Endpunkt gesendet werden.

Bei Schreibanforderungen an schreibgeschützte Regionen tritt der HTTP-Fehler 403 („Verboten“) auf.

Wenn sich nach der anfänglichen Ermittlungsphase des Clients die Schreibregion ändert, tritt bei nachfolgenden Schreibanforderungen an die vorherige Schreibregion der HTTP-Fehler 403 („Verboten“) auf. Der Client sollte dann erneut eine GET-Anforderung senden, um die Liste der Regionen erneut abzurufen und die aktualisierte Schreibregion zu empfangen.

Das ist alles, und dieses Tutorial ist abgeschlossen. Informationen dazu, wie Sie die Konsistenz Ihres global replizierten Kontos verwalten, finden Sie unter [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md). Weitere Informationen zur Funktionsweise der globalen Datenbankreplikation in Azure Cosmos DB finden Sie unter [Globale Verteilung von Daten mit Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit den SQL-APIs

Sie können jetzt mit dem nächsten Tutorial fortfahren, um zu erfahren, wie Sie mit dem lokalen Azure Cosmos DB-Emulator lokal entwickeln können.

> [!div class="nextstepaction"]
> [Lokales Entwickeln mit dem Emulator](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/