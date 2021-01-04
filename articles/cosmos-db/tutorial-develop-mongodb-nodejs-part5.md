---
title: Verbinden der Angular-App mit der Azure Cosmos DB-API für MongoDB unter Verwendung von Mongoose
description: Dieses Tutorial beschreibt, wie Sie eine Node.js-Anwendung erstellen, indem Sie Angular und Express verwenden, um die in Cosmos DB gespeicherten Daten zu verwalten. In diesem Teil verwenden Sie Mongoose, um eine Verbindung mit Azure Cosmos DB herzustellen.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097803"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Erstellen einer Angular-App mit der Azure Cosmos DB-API für MongoDB: Verwenden von Mongoose zum Herstellen einer Verbindung mit Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Tutorial zeigt, wie Sie eine Node.js-App mit Express und Angular erstellen und mit Ihrem [Cosmos-Konto verbinden, das mit der Cosmos DB-API für MongoDB](mongodb-introduction.md) konfiguriert wurde. Dieser Artikel beschreibt Teil 5 des Tutorials und baut auf [Teil 4](tutorial-develop-mongodb-nodejs-part4.md) auf.

In diesem Teil des Tutorials führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit Cosmos DB mithilfe von Mongoose.
> * Abrufen der Cosmos DB-Verbindungszeichenfolge.
> * Erstellen des Hero-Modells.
> * Erstellen des Hero-Diensts zum Abrufen von Hero-Daten.
> * Führen Sie die App lokal aus.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Bevor Sie mit diesem Tutorial beginnen, führen Sie die Schritte in [Teil 4](tutorial-develop-mongodb-nodejs-part4.md) aus.

* Für dieses Tutorial ist es erforderlich, dass Sie die Azure CLI lokal ausführen. Hierfür muss Azure CLI Version 2.0 oder höher installiert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli).

* In diesem Tutorial wird die Anwendungserstellung Schritt für Schritt erläutert. Für den Fall, dass Sie das fertige Projekt herunterladen möchten, steht die fertige Anwendung auf GitHub im [Repository „angular-cosmosdb“](https://github.com/Azure-Samples/angular-cosmosdb) zur Verfügung.

## <a name="use-mongoose-to-connect"></a>Verwenden von Mongoose zu Herstellen einer Verbindung

Mongoose ist eine ODM-Bibliothek (Object Data Modeling, Objektdatenmodellierung) für MongoDB und Node.js. Sie können Mongoose verwenden, um eine Verbindung mit Ihrem Azure Cosmos DB-Konto herzustellen. Verwenden Sie die folgenden Schritte, um Mongoose zu installieren und eine Verbindung mit Azure Cosmos DB herzustellen:

1. Installieren Sie das npm-Modul von Mongoose, eine API, die für die Kommunikation mit MongoDB verwendet wird.

    ```bash
    npm i mongoose --save
    ```

1. Erstellen Sie im Ordner **server** eine Datei namens **mongo.js**. Dieser Datei fügen Sie die Verbindungsdetails Ihres Azure Cosmos DB-Kontos hinzu.

1. Kopieren Sie den folgenden Code in die Datei **mongo.js**. Dieser Code bietet die folgenden Funktionen:

   * Er erfordert Mongoose.
   * Er setzt die Mongo-Zusage außer Kraft, um die grundlegende Zusage zu verwenden, die in ES6/ES2015 und höhere Versionen integriert ist.
   * Er ruft eine Umgebungsdatei auf, mit der Sie bestimmte Aspekte abhängig von der aktuellen Phase (Staging, Produktion oder Entwicklung) einrichten können. Sie erstellen diese Datei im nächsten Abschnitt.
   * Er enthält die MongoDB-Verbindungszeichenfolge, die in der Umgebungsdatei festgelegt wird.
   * Er erstellt eine connect-Funktion, die Mongoose aufruft.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. Erstellen Sie im Explorer-Bereich unter **server** einen Ordner mit dem Namen **environment**. Erstellen Sie im Ordner **environment** eine Datei namens **environment.js**.

1. Aus der Datei „mongo.js“ müssen wir Werte für die Parameter `dbName`, `key` und `cosmosPort` einbinden. Kopieren Sie den folgenden Code in die Datei **environment.js** :

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Um Ihre Anwendung mit Azure Cosmos DB zu verbinden, müssen Sie die Konfigurationseinstellungen für die Anwendung aktualisieren. Verwenden Sie die folgenden Schritte, um die Einstellungen zu aktualisieren: 

1. Rufen Sie im Azure-Portal die Portnummer, den Azure Cosmos DB-Kontonamen und die Primärschlüsselwerte für Ihr Azure Cosmos DB-Konto ab.

1. Ändern Sie in der Datei **environment.js** den Wert von `port` in 10255. 

    ```javascript
    const port = 10255;
    ```

1. Ändern Sie in der Datei **environment.js** den Wert von `accountName` in den Namen des Azure Cosmos DB-Kontos, das Sie in [Teil 4](tutorial-develop-mongodb-nodejs-part4.md) des Tutorials erstellt haben. 

1. Rufen Sie den Primärschlüssel für das Azure Cosmos DB-Konto mithilfe des folgenden CLI-Befehls im Terminalfenster ab: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> ist der Name des Azure Cosmos DB-Kontos, das Sie in [Teil 4](tutorial-develop-mongodb-nodejs-part4.md) des Tutorials erstellt haben.

1. Kopieren Sie den Primärschlüssel als Wert für `key` in die Datei **environment.js**.

Ihre Anwendung verfügt jetzt über alle erforderlichen Informationen für eine Verbindung mit Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Erstellen eines hero-Modells

Nun müssen Sie das Schema der Daten definieren, die in Azure Cosmos DB gespeichert werden sollen, indem Sie eine Modelldatei definieren. Verwenden Sie die folgenden Schritte, um ein _Hero-Modell_ zu erstellen, das das Schema der Daten definiert:

1. Erstellen Sie im Explorer-Bereich unter dem Ordner **server** eine Datei namens **hero.model.js**.

1. Kopieren Sie den folgenden Code in die Datei **hero.model.js**. Dieser Code bietet die folgenden Funktionen:

   * Er erfordert Mongoose.
   * Er erstellt ein neues Schema mit einer ID, einem Namen und einem Spruch.
   * Er erstellt ein Modell mithilfe des Schemas.
   * Er exportiert das Modell. 
   * Er nennt die Sammlung **Heroes** (anstelle von **Heros** , was der Standardname der Sammlung ist, der auf Mongoose-Pluralnamensregeln basiert).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Erstellen eines hero-Diensts

Nachdem Sie das Hero-Modell erstellt haben, müssen Sie einen Dienst definieren, um die Daten zu lesen und Auflistungs-, Erstellungs-, Lösch- und Aktualisierungsvorgänge auszuführen. Verwenden Sie die folgenden Schritte, um einen _Hero-Dienst_ zu erstellen, der die Daten aus Azure Cosmos DB abfragt:

1. Erstellen Sie im Explorer-Bereich unter dem Ordner **server** eine Datei namens **hero.service.js**.

1. Kopieren Sie den folgenden Code in die Datei **hero.service.js**. Dieser Code bietet die folgenden Funktionen:

   * Er ruft das Modell ab, das Sie erstellt haben.
   * Er stellt eine Verbindung mit der Datenbank her.
   * Er erstellt eine `docquery`-Variable, die mithilfe der `hero.find`-Methode eine Abfrage definiert, die alle hero-Elemente zurückgibt.
   * Er führt eine Abfrage mit der `docquery.exec`-Funktion und einer Zusage zum Abrufen einer Liste mit allen hero-Elemente ab, wobei der Antwortstatus 200 lauten muss. 
   * Er sendet eine Fehlermeldung zurück, wenn der Status 500 lautet.
   * Er ruft die hero-Elemente ab, weil wir Module verwenden. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Konfigurieren von Routen

Nun müssen Sie Routen einrichten, um die URLs für get-, create-, read- und delete-Anforderungen zu verarbeiten. Die Routingmethoden geben Rückruffunktionen an (auch als _Handlerfunktionen_ bezeichnet). Diese Funktionen werden aufgerufen, wenn die Anwendung eine Anforderung für den angegebenen Endpunkt und die angegebene HTTP-Methode empfängt. Verwenden Sie die folgenden Schritte, um den Hero-Dienst hinzuzufügen und Ihre Routen zu definieren:

1. Kommentieren Sie in Visual Studio Code in der Datei **routes.js** die Funktion `res.send` aus, die die Beispieldaten für die hero-Elemente sendet. Fügen Sie stattdessen eine Zeile mit einem Aufruf der Funktion `heroService.getHeroes` hinzu.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. Verwenden Sie in der Datei **routes.js**`require` für den Hero-Dienst, damit dieser erforderlich ist:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. Aktualisieren Sie in der Datei **hero.service.js** die `getHeroes`-Funktion wie folgt, damit sie die Parameter `req` und `res` annimmt:

    ```javascript
    function getHeroes(req, res) {
    ```

An dieser Stelle sollten wir uns kurz den vorherigen Code genauer ansehen. Zuerst sehen wir uns die Datei „Index.js“ an, die den Knotenserver einrichtet. Beachten Sie, dass sie Ihre Routen einrichtet und definiert. Anschließend kommuniziert die Datei „routes.js“ mit dem Hero-Dienst und weist ihn an, Ihre Funktionen wie **getHeroes** abzurufen und die Anforderung und Antwort zu übergeben. Die Datei „hero.service.js“ ruft das Modell ab und stellt eine Verbindung mit Mongo her. Dann führt sie **getHeroes** aus, wenn sie aufgerufen wird, und gibt die Antwort 200 zurück. 

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die App nun mit den folgenden Schritten aus:

1. Speichern Sie in Visual Studio Code alle Änderungen. Wählen Sie auf der linken Seite die Schaltfläche **Debuggen** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: und dann die Schaltfläche **Debuggen starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: aus.

1. Wechseln Sie nun in den Browser. Öffnen Sie die **Entwicklertools** und die **Registerkarte „Netzwerk“** . Navigieren Sie zu `http://localhost:3000`. Dort wird Ihre Anwendung angezeigt.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Neues Azure Cosmos DB-Konto im Azure-Portal":::

Noch sind keine hero-Elemente in der App gespeichert. Im nächsten Teil dieses Tutorials fügen wir Put-, Push- und Delete-Funktionen hinzu. Dann können wir hero-Elemente über die Benutzeroberfläche hinzufügen, aktualisieren und löschen, indem wir Mongoose-Verbindungen mit unserer Azure Cosmos-Datenbank verwenden. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, können Sie die Ressourcengruppe, das Azure Cosmos DB-Konto und alle zugehörigen Ressourcen löschen. Führen Sie die folgenden Schritte aus, um die Ressourcengruppe zu löschen:

 1. Navigieren Sie zu der Ressourcengruppe, in der Sie das Azure Cosmos DB-Konto erstellt haben.
 1. Wählen Sie die Option **Ressourcengruppe löschen**.
 1. Bestätigen Sie dann den Namen der zu löschenden Ressourcengruppe, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit Teil 6 des Tutorials fort, um der App Post-, Put- und Delete-Funktionen hinzuzufügen:

> [!div class="nextstepaction"]
> [Teil 6: Hinzufügen von Post-, Put- und Delete-Funktionen zur App](tutorial-develop-mongodb-nodejs-part6.md)