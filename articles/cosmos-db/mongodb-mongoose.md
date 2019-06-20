---
title: Verbinden einer Node.js Mongoose-Anwendung mit Azure Cosmos DB
description: Erfahren Sie, wie Sie über das Mongoose-Framework Daten in Azure Cosmos DB speichern und verwalten.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 23275bc639b445b55cafb72c929514541ba00660
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333238"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Verbinden einer Node.js Mongoose-Anwendung mit Azure Cosmos DB

In diesem Tutorial wird veranschaulicht, wie das [Mongoose-Framework](https://mongoosejs.com/) verwendet werden kann, wenn Daten in Cosmos DB gespeichert werden. Für diese exemplarische Vorgehensweise wird die API für MongoDB von Azure Cosmos DB verwendet. Für diejenigen, die Mongoose noch nicht kennen: Mongoose ist ein Objektmodellierungsframework für MongoDB in Node.js und stellt eine schnörkellose schemabasierte Lösung bereit, mit der Sie Ihre Anwendungsdaten modellieren können.

Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Cosmos DB zugrunde liegen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) Version v0.10.29 oder höher.

## <a name="create-a-cosmos-account"></a>Erstellen eines Cosmos-Kontos

Erstellen Sie ein Cosmos-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen und mit dem Einrichten Ihrer Node.js-Anwendung fortfahren. Wenn Sie den Azure Cosmos DB-Emulator verwenden, führen Sie die unter [Azure Cosmos DB-Emulator](local-emulator.md) aufgeführten Schritte zum Einrichten des Emulators aus, und fahren Sie dann mit dem Einrichten Ihrer Node.js-Anwendung fort.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Einrichten der Node.js-Anwendung

>[!Note]
> Wenn Sie sich nur den Beispielcode ansehen möchten, statt die Anwendung einzurichten, klonen Sie das [Beispiel](https://github.com/Azure-Samples/Mongoose_CosmosDB), das für dieses Tutorial verwendet wird, und erstellen Sie die Node.js Mongoose-Anwendung in Azure Cosmos DB.

1. Um eine Node.js-Anwendung in dem von Ihnen gewünschten Ordner zu erstellen, führen Sie den folgenden Befehl an einer Eingabeaufforderung für den Knoten aus.

    ```npm init```

    Beantworten Sie die Fragen. Danach ist das Projekt einsatzbereit.

1. Fügen Sie im Ordner eine neue Datei hinzu, und nennen Sie diese ```index.js```.
1. Installieren Sie die erforderlichen Pakete, indem Sie eine der ```npm install```-Optionen verwenden:
   * Mongoose:```npm install mongoose@5 --save```

     > [!Note]
     > Die Mongoose-Beispielverbindung unten basiert auf Mongoose 5+, das sich seit früheren Versionen geändert hat.
    
   * Dotenv (wenn Sie Ihre Geheimnisse aus einer Datei ENV-Datei laden möchten):```npm install dotenv --save```

     >[!Note]
     > Das ```--save```-Flag bewirkt, dass die Abhängigkeit zur Datei „package.json“ hinzugefügt wird.

1. Importieren Sie die Abhängigkeiten in Ihre „index.js“-Datei.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Fügen Sie Ihre Cosmos DB-Verbindungszeichenfolge und Ihren Cosmos DB-Namen in der ```.env```-Datei hinzu.

    ```JavaScript
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Stellen Sie über das Mongoose-Framework eine Verbindung mit Cosmos DB her, indem Sie den folgenden Code am Ende von „index.js“ hinzufügen.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Hier werden die Umgebungsvariablen mithilfe des npm-Pakets „dotenv“ über „process.env.{Variablenname}“ geladen.

    Sobald Sie eine Verbindung mit Azure Cosmos DB hergestellt haben, können Sie damit beginnen, Objektmodelle in Mongoose einzurichten.

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Einschränkungen für die Verwendung von Mongoose mit Cosmos DB

Für jedes Modell, das Sie erstellen, erstellt Mongoose eine neue Sammlung. Dies ist (aufgrund des sammlungsbezogenen Abrechnungsmodells von Cosmos DB) möglicherweise nicht die kostengünstigste Methode, wenn Sie mehrere Objektmodelle mit wenigen Daten verwenden.

In dieser exemplarischen Vorgehensweise werden beide Modelle behandelt. Zuerst wird das Modell vorgestellt, in dem pro Sammlung Daten eines Typs gespeichert werden. Dies ist das faktische Verhalten für Mongoose.

Mongoose hat auch ein Konzept, das als [Diskriminatoren](https://mongoosejs.com/docs/discriminators.html) bezeichnet wird. Diskriminatoren sind ein Mechanismus zu Schemavererbung. Sie ermöglichen Ihnen, mehrere Modelle mit sich überschneidenden Schemas aufgesetzt auf derselben zugrunde liegenden MongoDB-Sammlung zu haben.

Sie können die verschiedenen Datenmodelle in derselben Sammlung speichern und dann zur Abfragezeit eine Filterklausel verwenden, um nur die benötigten Daten zu abzurufen.

### <a name="one-collection-per-object-model"></a>Eine Sammlung pro Objektmodell

Das Mongoose-Standardverhalten sieht so aus, dass jedes Mal, wenn Sie ein Objektmodell erstellen, eine MongoDB-Sammlung erstellt wird. In diesem Abschnitt wird erläutert, wie sich dies mit der API für MongoDB von Azure Cosmos DB erreichen lässt. Diese Methode empfiehlt sich, wenn Sie Objektmodelle mit großen Datenmengen haben. Dies ist das Standardausführungsmodell für Mongoose, weshalb Sie möglicherweise damit vertraut sind, wenn Sie mit Mongoose vertraut sind.

1. Öffnen Sie erneut Ihre ```index.js```-Datei.

1. Erstellen Sie die Schemadefinition für „Family“.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Erstellen Sie ein Objekt für „Family“.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Speichern Sie das Objekt schließlich in Cosmos DB. Dadurch wird eine Sammlung unter der Hauptebene erstellt.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Erstellen Sie nun ein weiteres Schema und Objekt. Erstellen Sie dieses Mal ein Schema für Urlaubsziele (VacationDestinations), an denen die Familien interessiert sein könnten.
   1. Erstellen Sie das Schema so wie beim letzten Mal.
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Erstellen Sie ein Beispielobjekt (Sie können mehrere Objekte zu diesem Schema hinzufügen), und speichern Sie es.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Nachdem Sie zum Azure-Portal navigiert sind, werden nun zwei Sammlungen angezeigt, die in Cosmos DB erstellt wurden.

    ![Node.js-Tutorial – Momentaufnahme des Azure-Portals, in dem ein Azure Cosmos DB-Konto angezeigt wird, wobei mehrere Sammlungsnamen hervorgehoben sind – Node-Datenbank][multiple-coll]

1. Lesen Sie schließlich die Daten aus Cosmos DB. Da das Mongoose-Standardausführungsmodell verwendet wird, sind die Lesevorgänge mit beliebigen anderen Lesevorgängen mit Mongoose identisch.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Verwenden von Mongoose-Diskriminatoren, um Daten in einer einzigen Sammlung zu speichern

In dieser Methode werden [Mongoose-Diskriminatoren](https://mongoosejs.com/docs/discriminators.html) verwendet, um möglichst geringe Kosten für jede Sammlung zu erreichen. Diskriminatoren ermöglichen es Ihnen, einen unterscheidenden Schlüssel (discriminatorKey) zu definieren, wodurch Sie die Möglichkeit erhalten, unterschiedliche Objektmodelle zu speichern und nach diesen zu unterscheiden und zu filtern.

Sie erstellen ein Basisobjektmodell, definieren einen Differenzierungsschlüssel und fügen „Family“ und „VacationDestinations“ dem Basismodell als Erweiterung hinzu.

1. Richten Sie die Basiskonfiguration ein, und definieren Sie den Diskriminatorschlüssel.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Definieren Sie als Nächstes das allgemeine Objektmodell.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Definieren Sie nun das „Family“-Modell. Beachten Sie hier, dass ```commonModel.discriminator``` anstelle von ```mongoose.model``` verwendet wird. Außerdem wird die Basiskonfiguration zum Mongoose-Schema hinzugefügt. Somit ist der Diskriminatorschlüssel (discriminatorKey) hier gleich ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Fügen Sie auf gleiche Weise ein weiteres Schema hinzu, und zwar diesmal für die Urlaubsziele (VacationDestinations). Hierfür ist der Diskriminatorschlüssel (discriminatorKey) gleich ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Erstellen Sie schließlich Objekte für das Modell, und speichern Sie das Modell.
   1. Fügen Sie Objekte zum „Family“-Modell hinzu.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Fügen Sie als Nächstes Objekte zum „VacationDestinations“-Modell hinzu, und speichern Sie es.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Wenn Sie nun zum Azure-Portal zurückkehren, sehen Sie, dass es nur eine Sammlung namens ```alldata``` gibt, die sowohl die „Family“- als auch die „VacationDestinations“-Daten enthält.

    ![Node.js-Tutorial – Momentaufnahme des Azure-Portals, in dem ein Azure Cosmos DB-Konto angezeigt wird, wobei der Sammlungsname hervorgehoben ist – Node-Datenbank][alldata]

1. Beachten Sie auch, dass jedes Objekt ein weiteres Attribut namens ```__type``` hat, das es Ihnen erleichtert, zwischen den beiden Objektmodellen zu unterscheiden.

1. Lesen Sie schließlich die Daten, die in Azure Cosmos DB gespeichert sind. Mongoose übernimmt das Filtern von Daten anhand des Modells. Daher müssen Sie nicht anders vorgehen, wenn Sie Daten lesen. Geben Sie einfach Ihr Modell an (in diesem Fall ```Family_common```), und Mongoose verwaltet das Filtern über „DiscriminatorKey“.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Wie Sie sehen, ist ein Arbeiten mit Mongoose-Diskriminatoren einfach. Wenn Sie eine App haben, in der das Mongoose-Framework verwendet wird, bietet Ihnen dieses Tutorial somit eine Möglichkeit, Ihre Anwendung für die API für MongoDB von Azure Cosmos einzurichten und auszuführen, ohne dass zu viele Änderungen erforderlich sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
