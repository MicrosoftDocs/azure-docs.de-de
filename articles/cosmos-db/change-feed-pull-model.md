---
title: Pullmodell für Änderungsfeed
description: Erfahren Sie, wie Sie das Pullmodell für den Azure Cosmos DB-Änderungsfeed verwenden, um den Änderungsfeed zu lesen. Erfahren Sie außerdem etwas über die Unterschiede zwischen dem Pullmodell und dem Änderungsfeedprozessor.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/09/2021
ms.reviewer: sngun
ms.openlocfilehash: ee05cbdfb2634ed7c299f736b3343ce2dfbd3520
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548402"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Pullmodell für den Änderungsfeed in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit dem Pullmodell für den Änderungsfeed können Sie den Azure Cosmos DB-Änderungsfeed in Ihrem eigenen Tempo verwenden. Wie Sie bereits beim [Änderungsfeedprozessor](change-feed-processor.md) können Sie das Pullmodell für den Änderungsfeed verwenden, um die Verarbeitung von Änderungen über mehrere Änderungsfeedconsumer zu parallelisieren.

> [!NOTE]
> Das Pullmodell für den Änderungsfeed ist derzeit ausschließlich als [Vorschauversion im Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) verfügbar. Für andere SDK-Versionen ist noch keine Vorschauversion verfügbar.

## <a name="comparing-with-change-feed-processor"></a>Vergleich mit dem Änderungsfeedprozessor

In vielen Szenarien können Sie den Änderungsfeed entweder mit dem [Änderungsfeedprozessor](change-feed-processor.md) oder mit dem Pullmodell verarbeiten. Die Fortsetzungstoken des Pullmodells und der Leasecontainer des Änderungsfeedprozessors sind beides „Lesezeichen“ für das zuletzt verarbeitete Element (oder den Batch von Elementen) im Änderungsfeed.

Es ist jedoch nicht möglich, Fortsetzungstoken in einen Leasecontainer (oder umgekehrt) zu konvertieren.

> [!NOTE]
> In den meisten Fällen ist es am einfachsten, zum Lesen des Änderungsfeeds den [Änderungsfeedprozessor](change-feed-processor.md) zu verwenden.

In folgenden Szenarien sollten Sie die Verwendung des Pullmodells in Erwägung ziehen:

- Lesen der Änderungen von einem bestimmten Partitionsschlüssel
- Steuern der Geschwindigkeit, mit der der Client Änderungen für die Verarbeitung empfängt
- Ausführen eines einmaligen Lesevorgangs über die vorhandenen Daten im Änderungsfeed (z. B. für eine Datenmigration)

Im Folgenden sind einige wichtige Unterschiede zwischen dem Änderungsfeedprozessor und dem Pullmodell aufgeführt:

|Funktion  | Change Feed Processor| Pull-Modell |
| --- | --- | --- |
| Verfolgen der aktuellen Position bei der Verarbeitung des Änderungsfeeds | Lease (gespeichert in einem Azure Cosmos DB-Container) | Fortsetzungstoken (im Arbeitsspeicher gespeichert oder manuell persistent gespeichert) |
| Möglichkeit zur Wiederholung vergangener Änderungen | Ja, mit Pushmodell | Ja, mit Pullmodell|
| Pullen zukünftiger Änderungen | Automatische Überprüfung auf Änderungen basierend auf einem vom Benutzer angegebenen `WithPollInterval` | Manuell |
| Verhalten ohne neue Änderungen | Automatisches Warten auf `WithPollInterval` und neue Überprüfung | Ausnahme muss abgefangen werden und nochmal überprüft werden |
| Verarbeiten von Änderungen aus dem gesamten Container | Ja, mit automatischer Parallelisierung über mehrere Threads/Computer hinweg, die denselben Container verwenden| Ja, mit manueller Parallelisierung mithilfe von FeedTokens |
| Verarbeiten von Änderungen von nur einem Partitionsschlüssel | Nicht unterstützt | Ja|
| Supportebene | Allgemein verfügbar | Vorschau |

> [!NOTE]
> Im Gegensatz zum Lesen mithilfe des Änderungsfeedprozessors müssen Sie Fälle explizit behandeln, bei denen keine neuen Änderungen vorliegen. 

## <a name="consuming-an-entire-containers-changes"></a>Verwenden der Änderungen eines vollständigen Containers

Sie können einen `FeedIterator` erstellen, um den Änderungsfeed mithilfe des Pullmodells zu verarbeiten. Wenn Sie erstmalig einen `FeedIterator` erstellen, müssen Sie einen erforderlichen `ChangeFeedStartFrom`-Wert angeben, der sowohl die Anfangsposition für das Lesen von Änderungen als auch den gewünschten `FeedRange` umfasst. Der `FeedRange` ist ein Bereich von Partitionsschlüsselwerten und gibt die Elemente an, die mit diesem speziellen `FeedIterator` aus dem Änderungsfeed gelesen werden.

Optional können Sie `ChangeFeedRequestOptions` zum Festlegen eines `PageSizeHint` angeben. Der `PageSizeHint` gibt die maximale Anzahl von Elementen an, die auf einer einzelnen Seite zurückgegeben werden.

Der `FeedIterator` ist in zwei Varianten verfügbar. Zusätzlich zu den unten aufgeführten Beispielen, die Entitätsobjekte zurückgeben, können Sie die Antwort auch mit `Stream`-Unterstützung abrufen. Datenströme ermöglichen Ihnen, Daten zu lesen, ohne sie zuvor deserialisieren und auf Clientressourcen speichern zu müssen.

Im Folgenden finden Sie ein Beispiel für das Abrufen eines `FeedIterator`, der Entitätsobjekte zurückgibt (in diesem Fall ein `User`-Objekt):

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());
```

Im Folgenden finden Sie ein Beispiel für das Abrufen eines `FeedIterator`, der einen `Stream` zurückgibt:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());
```

Wenn Sie keinen `FeedRange` für einen `FeedIterator` angeben, können Sie den Änderungsfeed eines vollständigen Containers in Ihrem eigenen Tempo verarbeiten. Im folgenden Beispiel wird das Lesen aller Änderungen am aktuellen Zeitpunkt begonnen:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Da es sich beim Änderungsfeed im Grunde um eine unbegrenzte Liste von Elementen handelt, die alle zukünftigen Schreib- und Updatevorgänge enthält, ist der Wert von `HasMoreResults` immer TRUE. Wenn Sie versuchen, den Änderungsfeed zu lesen, und keine neuen Änderungen vorliegen, wird eine Ausnahme ausgelöst. Im obigen Beispiel wird diese Ausnahme behandelt, indem 5 Sekunden gewartet wird, bevor nochmal nach Änderungen geprüft wird.

## <a name="consuming-a-partition-keys-changes"></a>Verwenden der Änderungen eines Partitionsschlüssels

In einigen Fällen möchten Sie möglicherweise nur die Änderungen eines bestimmten Partitionsschlüssels verarbeiten. Sie können einen `FeedIterator` für einen bestimmten Partitionsschlüssel abrufen und die Änderungen auf die gleiche Weise wie für einen vollständigen Container verarbeiten.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedMode.Incremental, 
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Verwenden von FeedRange für die Parallelisierung

Beim [Änderungsfeedprozessor](change-feed-processor.md) wird die Arbeit automatisch auf mehrere Consumer verteilt. Beim Pullmodell für den Änderungsfeed können Sie `FeedRange` verwenden, um die Verarbeitung des Änderungsfeeds zu parallelisieren. Ein `FeedRange` stellt einen Bereich von Partitionsschlüsselwerten dar.

Das folgende Beispiel zeigt, wie Sie eine Liste von Bereichen für Ihren Container abrufen:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Wenn Sie eine Liste von FeedRanges für Ihren Container abrufen, erhalten Sie einen `FeedRange` pro [physischer Partition](partitioning-overview.md#physical-partitions).

Mithilfe eines `FeedRange` können Sie dann einen `FeedIterator` erstellen, um die Verarbeitung des Änderungsfeeds parallel auf mehrere Computer oder Threads zu verteilen. Im Gegensatz zum vorherigen Beispiel, in dem gezeigt wurde, wie Sie einen `FeedIterator` für den gesamten Container oder einen einzelnen Partitionsschlüssel abrufen, können Sie mithilfe von FeedRanges mehrere FeedIterators abrufen, über die der Änderungsfeed parallel verarbeitet werden kann.

Wenn Sie FeedRanges verwenden möchten, benötigen Sie einen Orchestratorprozess, der sie abruft und auf diese Computer verteilt. Diese Verteilung könnte wie folgt erfolgen:

* Verwenden von `FeedRange.ToJsonString` und Verteilen dieses Zeichenfolgenwerts. Die Consumer können diesen Wert mit `FeedRange.FromJsonString` verwenden.
* Wenn die Verteilung bereits bearbeitet wird: Übergeben eines Verweises auf das `FeedRange`-Objekt

Das folgende Beispiel zeigt, wie Sie vom Anfang des Änderungsfeeds des Containers lesen und dafür zwei hypothetische, getrennte Computer verwenden, die parallel lesen:

Computer 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Computer 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Speichern von Fortsetzungstoken

Sie können die Position des `FeedIterator` speichern, indem Sie ein Fortsetzungstoken erstellen. Ein Fortsetzungstoken ist ein Zeichenfolgenwert, der die letzten verarbeiteten Änderungen Ihres FeedIterators nachverfolgt. Dadurch kann der `FeedIterator` zu einem späteren Zeitpunkt an dieser Stelle fortgesetzt werden. Der folgende Code liest den Änderungsfeed seit der Erstellung des Containers. Wenn keine weiteren Änderungen mehr verfügbar sind, wird ein Fortsetzungstoken persistent gespeichert, sodass der Änderungsfeed später weiter verarbeitet werden kann.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.ContinuationToken(continuation));
```

Solange der Cosmos-Container vorhanden ist, läuft das Fortsetzungstoken eines FeedIterators nie ab.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Änderungsfeeds](change-feed.md)
* [Verwenden des Änderungsfeedprozessors](change-feed-processor.md)
* [Auslösen von Azure Functions](change-feed-functions.md)