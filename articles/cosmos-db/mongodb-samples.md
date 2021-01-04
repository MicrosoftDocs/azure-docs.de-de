---
title: Verwenden der API für MongoDB von Azure Cosmos DB zum Erstellen einer Node.js-App
description: Ein Tutorial, das mithilfe der API für MongoDB von Azure Cosmos DB eine Onlinedatenbank erstellt.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: devx-track-js
ms.openlocfilehash: c2117c535cca679d3a7a3b75491bf60ab9567ceb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098211"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Erstellen einer App mit Node.js und der API für MongoDB von Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](./sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Node.js für MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Dieses Beispiel zeigt Ihnen, wie Sie eine Konsolenanwendung mit Node.js und der API für MongoDB von Azure Cosmos DB erstellen.

Um dieses Beispiel zu verwenden, müssen Sie Folgendes tun:

* [Erstellen](create-mongodb-dotnet.md#create-account) Sie ein Cosmos-Konto, das für die Verwendung der API für MongoDB von Azure Cosmos DB konfiguriert ist.
* Rufen Sie die Informationen zur [Verbindungszeichenfolge](connect-mongodb-account.md) ab.

## <a name="create-the-app"></a>Erstellen der App

1. Erstellen Sie eine *app.js* -Datei, kopieren Sie den unten stehenden Code, und fügen Sie ihn in die Datei ein.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Optional:** Bei Verwendung des **MongoDB-Node.js 2.2-Treibers** muss der folgende Codeausschnitt ersetzt werden:

    Original:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Ersetzen durch:

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Ändern Sie die folgenden Variablen in der *app.js* -Datei gemäß Ihren Kontoeinstellungen (informieren Sie sich darüber, wie Sie Ihre [Verbindungszeichenfolge](connect-mongodb-account.md) finden):

    > [!IMPORTANT]
    > Bei Verwendung des **MongoDB-Node.js 3.0-Treibers** müssen Sonderzeichen im Cosmos DB-Kennwort codiert werden. Das Gleichheitszeichen (=) muss als „%3D“ codiert werden.
    >
    > Beispiel: Das Kennwort *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* muss wie folgt codiert werden: *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Bei Verwendung des **MongoDB-Node.js 2.2-Treibers** müssen Sonderzeichen im Cosmos DB-Kennwort nicht codiert werden.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Öffnen Sie Ihr bevorzugtes Terminal, führen Sie **npm install mongodb --save** aus, und führen Sie dann Ihre App mit **node app.js** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.