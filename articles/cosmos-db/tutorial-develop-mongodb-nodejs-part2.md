---
title: Erstellen einer Node.js Express-App mit der Azure Cosmos DB-API für MongoDB (Teil 2)
description: Teil 2 der Tutorialreihe zur Erstellung einer MongoDB-App mit Angular und Note in Azure Cosmos DB unter Verwendung der gleichen APIs wie für MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097847"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Erstellen einer Angular-App mit der Azure Cosmos DB-API für MongoDB: Erstellen einer Node.js Express-App
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Tutorial zeigt, wie Sie eine in Node.js geschriebene neue App mit Express und Angular erstellen und mit Ihrem [Cosmos-Konto verbinden, das mit der API für MongoDB von Cosmos DB](mongodb-introduction.md) konfiguriert wurde.

Teil 2 des Tutorials baut auf der [Einführung](tutorial-develop-mongodb-nodejs.md) auf und umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Installieren von Angular-Befehlszeilenschnittstelle und TypeScript
> * Erstellen eines neuen Projekts mithilfe von Angular
> * Erstellen der App mithilfe des Express-Frameworks
> * Testen der App in Postman

## <a name="video-walkthrough"></a>Exemplarische Vorgehensweise per Video

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Teil des Tutorials wird vorausgesetzt, dass Sie sich das [Einführungsvideo](tutorial-develop-mongodb-nodejs.md) angesehen haben.

Weitere Voraussetzungen für dieses Tutorial: 
* [Node.js](https://nodejs.org/) ab Version 8.4.0
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor

> [!TIP]
> In diesem Tutorial wird die Anwendungserstellung Schritt für Schritt erläutert. Für den Fall, dass Sie das fertige Projekt herunterladen möchten, steht die fertige Anwendung auf GitHub im [Repository „angular-cosmosdb“](https://github.com/Azure-Samples/angular-cosmosdb) zur Verfügung.

## <a name="install-the-angular-cli-and-typescript"></a>Installieren von Angular-Befehlszeilenschnittstelle und TypeScript

1. Öffnen Sie eine Eingabeaufforderung (Windows) oder ein Terminalfenster (Mac), und installieren Sie die Angular-Befehlszeilenschnittstelle.

    ```bash
    npm install -g @angular/cli
    ```

2. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um TypeScript zu installieren: 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Erstellen eines neuen Projekts mithilfe der Angular-Befehlszeilenschnittstelle

1. Navigieren Sie an der Eingabeaufforderung zu dem Ordner, in dem Sie Ihr neues Projekt erstellen möchten, und führen Sie den weiter unten angegebenen Befehl aus. Dieser Befehl erstellt einen neuen Ordner und ein Projekt namens „angular-cosmosdb“ und installiert die für die neue App erforderlichen Angular-Komponenten. Er verwendet die Mindesteinstellungen (--minimal) und gibt mit dem Flag „--style scss“ an, dass das Projekt SAAS (eine CSS-ähnliche Syntax) verwendet.

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Wechseln Sie nach Abschluss des Befehls zum Ordner „src/client“.

    ```bash
    cd angular-cosmosdb
    ```

3. Öffnen Sie den Ordner in Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Erstellen der App mithilfe des Express-Frameworks

1. Klicken Sie in Visual Studio Code im Bereich **Explorer** mit der rechten Maustaste auf den Ordner **src** , klicken Sie auf **Neuer Ordner** , und nennen Sie den neuen Ordner *server*.

2. Klicken Sie im Bereich **Explorer** mit der rechten Maustaste auf den Ordner **server** , klicken Sie auf **Neue Datei** , und nennen Sie die neue Datei *index.js*.

3. Kehren Sie zur Eingabeaufforderung zurück, und verwenden Sie den unten angegebenen Befehl, um die Komponente „body-parser“ zu installieren. Mit dieser Komponente kann die App die über die APIs übergebenen JSON-Daten analysieren.

    ```bash
    npm i express body-parser --save
    ```

4. Kopieren Sie den folgenden Code, und fügen Sie ihn in Visual Studio Code in die Datei „index.js“ ein. Für diesen Code gilt Folgendes:
    * Er verweist auf Express.
    * Er integriert „body-parser“ zum Lesen von JSON-Daten aus dem Anforderungstext.
    * Er verwendet ein integriertes Feature namens „path“.
    * Er legt Stammvariablen fest, um leichter ermitteln zu können, wo sich unser Code befindet.
    * Er richtet einen Port ein.
    * Er initiiert Express.
    * Er teilt der App mit, wie die Middleware verwendet wird, die wir zum Bereitstellen des Servers verwenden.
    * Er stellt alles aus dem Ordner „dist“ bereit. (Hierbei handelt es sich um den statischen Inhalt.)
    * Er stellt die Anwendung bereit, und liefert „index.html“ für alle GET-Anforderungen, die auf dem Server nicht gefunden wurden (für Deep-Links).
    * Er startet den Server mit „app.listen“.
    * Er verwendet eine arrow-Funktion, um zu protokollieren, dass der Port aktiv ist.
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Klicken Sie in Visual Studio Code im Bereich **Explorer** mit der rechten Maustaste auf den Ordner **server** , und klicken Sie anschließend auf **Neue Datei**. Nennen Sie die neue Datei *routes.cs*. 

6. Kopieren Sie den unten angegebenen Code in **routes.js**. Für diesen Code gilt Folgendes:
   * Er verweist auf den Express-Router.
   * Er ruft die Helden ab.
   * Er gibt die JSON-Daten für einen definierten Helden zurück.

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Er speichert alle geänderten Dateien. 

8. Klicken Sie in Visual Studio Code auf die Schaltfläche **Debuggen** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: und auf die Zahnradschaltfläche :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png":::. Die neue Datei „launch.json“ wird in Visual Studio Code geöffnet. 

8. Ändern Sie `"${workspaceFolder}\\server"` in Zeile 11 der Datei „launch.json“ in `"program": "${workspaceRoot}/src/server/index.js"`, und speichern Sie die Datei.

9. Klicken Sie auf die Schaltfläche **Debuggen starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png":::, um die App auszuführen.

    Die App sollte ohne Fehler ausgeführt werden.

## <a name="use-postman-to-test-the-app"></a>Testen der App mithilfe von Postman

1. Öffnen Sie Postman, und geben Sie `http://localhost:3000/api/heroes` in das Feld „GET“ ein. 

2. Klicken Sie auf die Schaltfläche **Senden** , und rufen Sie die JSON-Antwort aus der App ab. 

    Diese Antwort zeigt, dass die App aktiv ist und lokal ausgeführt wird. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman mit der Anforderung und der Antwort":::


## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen eines Node.js-Projekts mithilfe der Angular-Befehlszeilenschnittstelle
> * Testen der App mithilfe von Postman

Im nächsten Teil des Tutorials wird die Benutzeroberfläche erstellt.

> [!div class="nextstepaction"]
> [Erstellen der Benutzeroberfläche mit Angular](tutorial-develop-mongodb-nodejs-part3.md)
