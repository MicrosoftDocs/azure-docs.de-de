---
title: Schreiben von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB
description: Erfahren Sie, wie Sie gespeicherte Prozeduren, Triggern und benutzerdefinierte Funktionen in Azure Cosmos DB definieren.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 18cedad34a6ca7d9a0ba18cd01c082f2878380a8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339817"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Schreiben von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB bietet eine in die Sprache integrierte transaktionale Ausführung von JavaScript, mit der Sie **gespeicherte Prozeduren** , **Trigger** und **benutzerdefinierte Funktionen** (User Defined Functions, UDFs) schreiben können. Wenn Sie die SQL-API in Azure Cosmos DB verwenden, können Sie die gespeicherten Prozeduren, Trigger und UDFs in der JavaScript-Sprache definieren. Sie können Ihre Logik in JavaScript schreiben und in der Datenbank-Engine ausführen. Sie können Trigger, gespeicherte Prozeduren und UDFs im [Azure-Portal](https://portal.azure.com/), mithilfe der [in die JavaScript-Sprache integrierten Abfrage-API in Azure Cosmos DB](javascript-query-api.md) und mit den [SQL-API-Client-SDKs von Cosmos DB](sql-api-dotnet-samples.md) erstellen und ausführen. 

Um eine gespeicherte Prozedur, einen Trigger und einer benutzerdefinierte Funktion aufzurufen, müssen Sie das entsprechende Element registrieren. Weitere Informationen finden Sie unter [Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Wenn Sie eine gespeicherte Prozedur in partitionierten Containern ausführen, muss in den Anforderungsoptionen ein Partitionsschlüsselwert angegeben werden. Gespeicherte Prozeduren gelten immer für einen bestimmten Partitionsschlüssel. Elemente, die einen anderen Partitionsschlüsselwert aufweisen, sind in der gespeicherten Prozedur nicht sichtbar. Dies gilt auch für Trigger.
> [!Tip]
> Cosmos unterstützt die Bereitstellung von Containern mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen. Weitere Informationen finden Sie unter [Erstellen eines Azure Cosmos DB-Containers mit serverseitiger Funktionalität](./manage-with-templates.md#create-sproc).

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Schreiben gespeicherter Prozeduren

Gespeicherte Prozeduren werden mithilfe von JavaScript geschrieben und können Elemente in einem Azure Cosmos-Container erstellen, aktualisieren, lesen, abfragen und löschen. Gespeicherte Prozeduren werden pro Auflistung registriert und können auf beliebige Dokumente und Anhänge angewendet werden, die sich in dieser Auflistung befinden.

Hier ist eine einfache gespeicherte Prozedur, die die Antwort „Hello World“ zurückgibt.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Das Kontextobjekt bietet Zugriff auf alle Vorgänge, die in Azure Cosmos DB ausgeführt werden können, sowie Zugriff auf die Anforderungs- und Antwortobjekte. In diesem Fall verwenden Sie das Antwortobjekt dazu, den Text der Antwort festzulegen, der an den Client zurückgesendet werden soll.

Nachdem eine gespeicherte Prozedur geschrieben wurde, muss sie bei einer Auflistung registriert werden. Weitere Informationen finden Sie im Artikel [Verwenden von gespeicherten Prozeduren in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures).

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Erstellen eines Elements mithilfe einer gespeicherten Prozedur

Wenn Sie ein Element mithilfe einer gespeicherten Prozedur erstellen, wird dieses Element in den Azure Cosmos-Container eingefügt, und es wird eine ID für das neu erstellte Element zurückgegeben. Das Erstellen eines Elements ist ein asynchroner Vorgang und hängt von den JavaScript-Rückruffunktionen ab. Die Rückruffunktion verfügt über zwei Parameter: einen für das Fehlerobjekt für den Fall eines Fehlers im Vorgang und einen anderen für einen Rückgabewert, in diesem Fall für das erstellte Objekt. Innerhalb des Rückrufs können Sie entweder die Ausnahme behandeln oder einen Fehler auslösen. Für den Fall, dass kein Rückruf bereitgestellt wird und ein Fehler auftritt, löst die Azure Cosmos DB-Laufzeitumgebung einen Fehler aus.

Die gespeicherte Prozedur enthält auch einen Parameter zum Festlegen der Beschreibung. Hierbei handelt es sich um einen booleschen Wert. Wenn der Parameter auf TRUE festgelegt wird und die Beschreibung fehlt, löst die gespeicherte Prozedur eine Ausnahme aus. Andernfalls wird der Rest der gespeicherten Prozedur weiter ausgeführt.

Die folgende exemplarische gespeicherte Prozedur akzeptiert ein Array neuer Azure Cosmos-Elemente als Eingabe, fügt sie in den Azure Cosmos-Container ein und gibt die Anzahl der eingefügten Elemente zurück. In diesem Beispiel nutzen wir das To-Do-Listen-Beispiel aus der [Schnellstartanleitung: Erstellen einer .NET-Web-App mit Azure Cosmos DB unter Verwendung der SQL-API und des Azure-Portals](create-sql-api-dotnet.md).

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Arrays als Eingabeparameter für gespeicherte Prozeduren

Beim Definieren einer gespeicherten Prozedur im Azure-Portal werden Eingabeparameter immer als Zeichenfolge an die gespeicherte Prozedur gesendet. Selbst wenn Sie ein Array von Zeichenfolgen als Eingabe übergeben, wird das Array in eine Zeichenfolge konvertiert und an die gespeicherte Prozedur gesendet. Zur Umgehung dieses Problems können Sie eine Funktion in Ihrer gespeicherten Prozedur definieren, mit der die Zeichenfolge als Array analysiert wird. Der folgende Code zeigt, wie Sie einen Zeichenfolgen-Eingabeparameter als Array analysieren:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Transaktionen in gespeicherten Prozeduren

Sie können Transaktionen mithilfe einer gespeicherten Prozedur in Elementen innerhalb eines Containers implementieren. Das folgende Beispiel verwendet Transaktionen in einer Football-Spiele-App, um Spieler in einem einzigen Vorgang zwischen zwei Mannschaften zu transferieren. Die gespeicherte Prozedur versucht, die beiden Azure Cosmos-Elemente zu lesen, die jeweils den als Argument übergebenen Spieler-IDs entsprechen. Wenn beide Spieler gefunden werden, aktualisiert die gespeicherte Prozedur die Elemente, indem die Mannschaften getauscht werden. Wenn im Verlauf des Vorgangs Fehler auftreten, löst die gespeicherte Prozedur eine JavaScript-Ausnahme aus, die die Transaktion implizit abbricht.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Gebundene Ausführung innerhalb von gespeicherten Prozeduren

Im Folgenden finden Sie ein Beispiel einer gespeicherten Prozedur, die Elemente per Massenvorgang in einen Azure Cosmos-Container importiert. Die gespeicherte Prozedur verarbeitet die gebundene Ausführung, indem sie den booleschen Rückgabewert von `createDocument` prüft und dann die Anzahl der Elemente verwendet, die bei jedem Aufruf der gespeicherten Prozedur eingefügt werden, um den batchübergreifenden Fortschritt nachzuverfolgen und zu übernehmen.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>Asynchrones await mit gespeicherten Prozeduren

Im Folgenden finden Sie ein Beispiel für eine gespeicherte Prozedur, die asynchrones await mit Zusagen mithilfe einer Hilfsfunktion verwendet. Die gespeicherte Prozedur fragt ein Element ab und ersetzt es.

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Schreiben von Triggern

Azure Cosmos DB unterstützt vorangestellte und nachgestellte Trigger. Vorangestellte Trigger werden vor dem Ändern eines Datenbankelements ausgeführt, nachgestellte Trigger danach.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Vorangestellte Trigger

Das folgende Beispiel zeigt, wie ein vorangestellter Trigger zum Überprüfen der Eigenschaften eines Azure Cosmos-Elements verwendet werden kann, das erstellt wird. In diesem Beispiel nutzen wir das To-Do-Listen-Beispiel aus der [Schnellstartanleitung: Erstellen einer .NET-Web-App mit Azure Cosmos DB unter Verwendung der SQL-API und des Azure-Portals](create-sql-api-dotnet.md), um eine Timestamp-Eigenschaft zu einem neu hinzugefügten Element hinzuzufügen, falls dieses keine solche Eigenschaft enthält.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Vorangestellte Trigger können keine Eingabeparameter übernehmen. Das Anforderungsobjekt im Trigger kann verwendet werden, um die Anforderungsnachricht zu verändern, die dem Vorgang zugeordnet ist. Im vorherigen Beispiel wird der vorangestellte Trigger ausgeführt, wenn ein Azure Cosmos-Element erstellt wird, und der Text der Anforderungsnachricht enthält das zu erstellende Element im JSON-Format.

Wenn Trigger registriert werden, können Sie die Vorgänge angeben, mit denen sie ausgeführt werden können. Dieser Trigger wurde mit `TriggerOperation.Create` als `TriggerOperation`-Wert erstellt, d.h., die Verwendung des Triggers in einem Ersetzungsvorgang – wie im folgenden Code gezeigt – ist nicht zulässig.

Beispiele für das Registrieren und Aufrufen eines vorangestellten Triggers finden Sie in den Artikeln [Vorangestellte Trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) und [Nachgestellte Trigger](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

### <a name="post-triggers"></a><a id="post-triggers"></a>Nachgestellte Trigger

Das folgende Beispiel zeigt einen nachgestellten Trigger. Dieser Trigger fragt das Metadatenelement ab und aktualisiert es mit den Details zum neu erstellten Element.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Ein wichtiger Aspekt, den es zu beachten gilt, ist die transaktionale Ausführung von Triggern in Azure Cosmos DB. Der nachgestellte Trigger wird im Rahmen der gleichen Transaktion für das zugrunde liegende Element ausgeführt. Tritt während der Ausführung des nachgestellten Triggers eine Ausnahme auf, ist die gesamte Transaktion nicht erfolgreich. Sämtliche Commits werden rückgängig gemacht, und eine Ausnahme wird zurückgegeben.

Beispiele für das Registrieren und Aufrufen eines vorangestellten Triggers finden Sie in den Artikeln [Vorangestellte Trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) und [Nachgestellte Trigger](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Schreiben von benutzerdefinierten Funktionen

Das folgende Beispiel erstellt eine benutzerdefinierte Funktion, um die Einkommenssteuer für verschiedene Einkommensgruppen zu berechnen. Diese benutzerdefinierte Funktion wird dann innerhalb einer Abfrage verwendet. Nehmen wir für dieses Beispiel an, dass ein Container namens „Incomes“ mit folgenden Eigenschaften vorhanden ist:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Das folgende Beispiel ist eine Funktionsdefinition, um die Einkommenssteuer für verschiedene Einkommensgruppen zu berechnen:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Beispiele für das Registrieren und Verwenden einer benutzerdefinierten Funktion finden Sie im Artikel [Verwenden von benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs).

## <a name="logging"></a>Protokollierung 

Wenn Sie gespeicherte Prozeduren, Trigger oder benutzerdefinierte Funktionen verwenden, können Sie die Schritte mithilfe des Befehls `console.log()` protokollieren. Mit diesem Befehl konzentriert sich eine Zeichenfolge auf das Debuggen, wenn `EnableScriptLogging` wie im folgenden Beispiel gezeigt auf TRUE festgelegt ist:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie weitere Konzepte und Vorgehensweisen zum Schreiben und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB kennen:

* [Registrieren und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Schreiben von gespeicherten Prozeduren und Triggern mit der JavaScript-Abfrage-API in Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Arbeiten mit der in die JavaScript-Sprache integrierten Abfrage-API in Azure Cosmos DB](javascript-query-api.md)