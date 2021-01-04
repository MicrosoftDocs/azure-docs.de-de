---
title: Troubleshooting für die Ausnahme „Nicht gefunden“ in Azure Cosmos DB
description: Erfahren Sie mehr über die Diagnose und Behebung der Ausnahme „Nicht gefunden“.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 2df401f7871d631ba317fb670783cad086b9a351
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "96017558"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnose und Troubleshooting für die Ausnahme „Nicht gefunden“ in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der HTTP-Statuscode 404 steht dafür, dass die Ressource nicht mehr vorhanden ist.

## <a name="expected-behavior"></a>Erwartetes Verhalten
Es gibt viele gültige Szenarien, in denen eine Anwendung eine 404-Meldung erwartet und das Szenario ordnungsgemäß verarbeitet.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Eine Ausnahme „Nicht gefunden“ wurde für ein Element zurückgegeben, das vorhanden sein sollte oder vorhanden ist.
Im Folgenden sind mögliche Ursachen für das Zurückgeben des Statuscodes 404 in Fällen aufgeführt, in denen das betreffende Element vorhanden sein sollte oder vorhanden ist.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>Die Lesesitzung für das eingegebene Sitzungstoken ist nicht verfügbar

#### <a name="solution"></a>Lösung:
1. Aktualisieren Sie Ihr aktuelles SDK auf die neueste verfügbare Version. Die häufigsten Ursachen für diesen speziellen Fehler wurden in den neuesten SDK-Versionen behoben.

### <a name="race-condition"></a>Racebedingung
Es sind mehrere SDK-Clientinstanzen vorhanden, und der Lesevorgang ist vor dem Schreibvorgang erfolgt.

#### <a name="solution"></a>Lösung:
1. Die standardmäßig für Azure Cosmos DB festgelegte Kontokonsistenz ist die Sitzungskonsistenz. Wenn ein Element erstellt oder aktualisiert wird, wird bei der Antwort ein Sitzungstoken zurückgegeben, das zwischen den SDK-Instanzen weitergegeben werden kann. Damit wird sichergestellt, dass bei der Leseanforderung aus einem Replikat mit dieser Änderung gelesen wird.
1. Ändern Sie die [Konsistenzebene](./consistency-levels.md) in eine [höhere Ebene](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>Ungültige Kombination aus Partitionsschlüssel und ID
Die Kombination aus Partitionsschlüssel und ID ist ungültig.

#### <a name="solution"></a>Lösung:
Korrigieren Sie die Anwendungslogik, die die falsche Kombination verursacht. 

### <a name="invalid-character-in-an-item-id"></a>Ungültiges Zeichen in einer Element-ID
Ein Element mit einem [ungültigen Zeichen](/dotnet/api/microsoft.azure.documents.resource.id?preserve-view=true&view=azure-dotnet#remarks) in der Element-ID wird in Azure Cosmos DB eingefügt.

#### <a name="solution"></a>Lösung:
Ändern Sie die ID in einen anderen Wert, der keine Sonderzeichen enthält. Wenn das Ändern der ID nicht möglich ist, können Sie die ID mit Base64 codieren, um keine Sonderzeichen zu verwenden. Base64 kann weiterhin einen Namen mit dem ungültigen Zeichen „ / “ ausgeben, das ersetzt werden muss.

Bei Elementen, die bereits in den Container eingefügt wurden, kann die ID ersetzt werden, indem RID-Werte anstelle von namensbasierten Verweisen verwendet werden.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Bereinigung nach Ablauf der Gültigkeitsdauer
Für das Element wurde die Eigenschaft [Gültigkeitsdauer (TTL)](./time-to-live.md) festgelegt. Das Element wurde bereinigt, da die Gültigkeitsdauer abgelaufen war.

#### <a name="solution"></a>Lösung:
Ändern Sie die Eigenschaft für die Gültigkeitsdauer, um zu verhindern, dass das Element bereinigt wird.

### <a name="lazy-indexing"></a>Verzögerte Indizierung
Die [verzögerte Indizierung](index-policy.md#indexing-mode) ist noch nicht auf dem aktuellen Stand.

#### <a name="solution"></a>Lösung:
Warten Sie, bis die Indizierung auf den aktuellen Stand gebracht wurde, oder ändern Sie die Indizierungsrichtlinie.

### <a name="parent-resource-deleted"></a>Übergeordnete Ressource gelöscht
Die Datenbank oder der Container, in der bzw. dem sich das Element befindet, wurde gelöscht.

#### <a name="solution"></a>Lösung:
1. [Stellen Sie die übergeordnete Ressource wieder her](./online-backup-and-restore.md#request-data-restore-from-a-backup), oder erstellen Sie die Ressourcen neu.
1. Erstellen Sie eine neue Ressource, um die gelöschte Ressource zu ersetzen.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. Bei Container-/Sammlungsnamen wird zwischen Groß- und Kleinschreibung unterschieden
Bei Container-/Sammlungsnamen wird in Cosmos DB zwischen Groß- und Kleinschreibung unterschieden.

#### <a name="solution"></a>Lösung:
Stellen Sie sicher, dass Sie beim Herstellen einer Verbindung mit Cosmos DB den exakten Namen verwenden.

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
