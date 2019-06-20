---
title: Tutorial zur globalen Verteilung von Azure Cosmos DB mit der SQL-API
description: Hier erfahren Sie, wie Sie die globale Verteilung von Azure Cosmos DB mithilfe der SQL-API einrichten.
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/10/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 4f97d1f052cd8684674eecf479133051f2cfb76e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480554"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Einrichten der globalen Verteilung von Azure Cosmos DB mithilfe der SQL-API

In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal die globale Verteilung von Azure Cosmos DB einrichten und dann mithilfe der SQL-API eine Verbindung herstellen.

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit den [SQL-APIs](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Herstellen einer Verbindung mit einer bevorzugten Region mithilfe der SQL-API

Um von der [globalen Verteilung](distribute-data-globally.md)zu profitieren, können Clientanwendungen in einer Liste die Reihenfolge angeben, in der Regionen bei Dokumentvorgängen bevorzugt verwendet werden sollen. Dies lässt sich durch Einrichten einer Verbindungsrichtlinie erreichen. Basierend auf der Azure Cosmos DB-Kontokonfiguration, der aktuellen regionalen Verfügbarkeit und der angegebenen Liste der bevorzugten Regionen wählt das SQL SDK den optimalen Endpunkt für Schreib- und Lesevorgänge aus.

Diese Liste wird beim Initialisieren einer Verbindung mithilfe der SQL SDKs angegeben. Die SDKs akzeptieren einen optionalen Parameter „PreferredLocations“, bei dem es sich um eine sortierte Liste von Azure-Regionen handelt.

Das SDK sendet automatisch alle Schreibvorgänge an die aktuell für solche Vorgänge ausgewählte Region.

Alle Lesevorgänge werden an die erste verfügbare Region in der Liste der bevorzugten Regionen gesendet. Wenn bei der Anforderung ein Fehler auftritt, führt der Client ein Failover zur nächsten Region auf der Liste durch, usw.

Die SDKs versuchen nur, in den in „PreferredLocations“ angegebenen Regionen Lesevorgänge auszuführen. Wenn ein Datenbankkonto in vier Regionen verfügbar ist, der Client aber nur zwei Leseregionen (non-write) für PreferredLocations angibt, werden keine Lesevorgänge aus einer Leseregion verarbeitet, die nicht in PreferredLocations angegeben ist. Wenn die in den PreferredLocations angegebenen Leseregionen nicht verfügbar sind, werden Lesevorgänge aus der Schreibregion verarbeitet.

Die Anwendung kann die vom SDK ausgewählten aktuellen Schreib- und Leseendpunkte anhand von zwei Eigenschaften überprüfen: WriteEndpoint und ReadEndpoint. Diese stehen im SDK der Version 1.8 und höher zur Verfügung.

Wenn die PreferredLocations-Eigenschaft nicht festgelegt ist, werden alle Anforderungen von der aktuellen Schreibregion verarbeitet.

## <a name="net-sdk"></a>.NET SDK
Das SDK kann ohne Codeänderungen verwendet werden. In diesem Fall sendet das SDK automatisch sowohl Lese- als auch Schreibvorgänge an die aktuelle Schreibregion.

Im .NET SDK, Version 1.8 oder höher, besitzt der ConnectionPolicy-Parameter für den DocumentClient-Konstruktor folgende Eigenschaft: Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Diese Eigenschaft weist den Typ „Collection `<string>` “ auf und sollte eine Liste mit Regionsnamen enthalten. Die Zeichenfolgenwerte sind gemäß der Spalte mit den Regionsnamen auf der Seite [Azure-Regionen][regions] formatiert und enthalten keine Leerzeichen vor und nach dem letzten Zeichen.

Die aktuellen Schreib- und Leseendpunkte sind in DocumentClient.WriteEndpoint bzw. DocumentClient.ReadEndpoint verfügbar.

> [!NOTE]
> Die URLs für die Endpunkte sollten nicht als langfristige Konstanten betrachtet werden. Sie können jederzeit vom Dienst aktualisiert werden. Das SDK verarbeitet diese Änderung automatisch.
>
>

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

## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS-, JavaScript- und Python-SDKs
Das SDK kann ohne Codeänderungen verwendet werden. In diesem Fall sendet das SDK sowohl Lese- als auch Schreibvorgänge an die aktuelle Schreibregion.

In Version 1.8 oder höher jedes SDKs weist der ConnectionPolicy-Parameter für den DocumentClient-Konstruktor eine neue Eigenschaft auf: DocumentClient.ConnectionPolicy.PreferredLocations. Bei diesem Parameter handelt es sich um ein Zeichenfolgenarray, das eine Liste mit Regionsnamen akzeptiert. Die Namen sind gemäß der Spalte mit den Regionsnamen auf der Seite [Azure-Regionen][regions] formatiert. Sie können auch die vordefinierten Konstanten im bereitgestellten AzureDocuments.Regions-Objekt verwenden.

Die aktuellen Schreib- und Leseendpunkte sind in DocumentClient.getWriteEndpoint bzw. DocumentClient.getReadEndpoint verfügbar.

> [!NOTE]
> Die URLs für die Endpunkte sollten nicht als langfristige Konstanten betrachtet werden. Sie können jederzeit vom Dienst aktualisiert werden. Das SDK verarbeitet diese Änderung automatisch.
>
>

Im Folgenden finden Sie ein Codebeispiel für NodeJS/Javascript. Python und Java folgen demselben Muster.

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
Sobald ein Datenbankkonto in mehreren Regionen zur Verfügung gestellt wurde, können Clients mithilfe einer GET-Anforderung über den folgenden URI die Verfügbarkeit des Kontos abfragen.

    https://{databaseaccount}.documents.azure.com/

Der Dienst gibt eine Liste der Regionen und der zugehörigen Azure Cosmos DB-Endpunkt-URIs für die Replikate zurück. Die aktuelle Schreibregion wird in der Antwort angegeben. Der Client kann dann wie folgt den geeigneten Endpunkt für alle weiteren REST-API-Anforderungen auswählen.

Beispielantwort

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


* Alle PUT-, POST- und DELETE-Anforderungen müssen an den angegebenen Schreib-URI gesendet werden.
* Alle GET-Anforderungen sowie weitere Anforderungen ohne Schreibzugriff (z.B. Abfragen) können an einen beliebigen vom Client ausgewählten Endpunkt gesendet werden.

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

