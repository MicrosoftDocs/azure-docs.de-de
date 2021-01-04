---
title: Hinzufügen von CRUD-Funktionen zu einer Angular-App mit der Azure Cosmos DB-API für MongoDB
description: Teil 6 der Tutorialreihe zur Erstellung einer MongoDB-App mit Angular und Note in Azure Cosmos DB unter Verwendung der gleichen APIs wie für MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081313"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Erstellen einer Angular-App mit der API für MongoDB von Azure Cosmos DB: Hinzufügen von CRUD-Funktionen zur App
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Tutorial zeigt, wie Sie eine in Node.js geschriebene neue App mit Express und Angular erstellen und mit Ihrem [Cosmos-Konto verbinden, das mit der API für MongoDB von Cosmos DB](mongodb-introduction.md) konfiguriert wurde. Teil 6 des Tutorials baut auf [Teil 5](tutorial-develop-mongodb-nodejs-part5.md) auf und umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Erstellen von Post-, Put- und Delete-Funktionen für den hero-Dienst
> * Ausführen der App

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Teil des Tutorials wird vorausgesetzt, dass Sie die Schritte aus [Teil 5](tutorial-develop-mongodb-nodejs-part5.md) des Tutorials ausgeführt haben.

> [!TIP]
> In diesem Tutorial wird die Anwendungserstellung Schritt für Schritt erläutert. Für den Fall, dass Sie das fertige Projekt herunterladen möchten, steht die fertige Anwendung auf GitHub im [Repository „angular-cosmosdb“](https://github.com/Azure-Samples/angular-cosmosdb) zur Verfügung.

## <a name="add-a-post-function-to-the-hero-service"></a>Hinzufügen einer Post-Funktion zum hero-Dienst

1. Öffnen Sie in Visual Studio Code die Dateien **routes.js** und **hero.service.js** mit der Schaltfläche **Editor teilen** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png":::.

    Wie Sie sehen, wird in Zeile 7 von „routes.js“ die Funktion `getHeroes` in Zeile 5 von **hero.service.js** aufgerufen.  Die gleiche Zuordnung muss für die Post-, Put- und Delete-Funktionen erstellt werden. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="„routes.js“ und „hero.service.js“ in Visual Studio Code":::
    
    Kümmern wir uns zunächst um den Code für den hero-Dienst. 

2. Kopieren Sie den folgenden Code, und fügen Sie ihn nach der Funktion `getHeroes` und vor `module.exports` in **hero.service.js** ein. Für diesen Code gilt Folgendes:  
   * Er verwendet das hero-Modell, um einen neuen Helden zu veröffentlichen.
   * Er überprüft anhand der Antworten, ob ein Fehler vorliegt, und gibt den Statuswert 500 zurück.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Aktualisieren Sie `module.exports` in **hero.service.js** mit der neuen Funktion `postHero`. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. Fügen Sie in **routes.js** nach dem Router `get` einen Router für die Funktion `post` hinzu. Diese Router veröffentlicht jeweils einen einzelnen Helden. Diese Struktur der Routerdatei zeigt auf übersichtliche Weise alle verfügbaren API-Endpunkte und überlässt die eigentliche Arbeit der Datei **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Führen Sie die App aus, um sich zu vergewissern, dass alles geklappt hat. Speichern Sie in Visual Studio Code Ihre gesamten Änderungen, und wählen Sie links die Schaltfläche **Debuggen** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: und dann die Schaltfläche **Debuggen starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png"::: aus.

6. Wechseln Sie nun wieder zu Ihrem Internetbrowser, drücken Sie F12 (auf den meisten Computern), um die Entwicklungstools zu öffnen, und navigieren Sie zur Registerkarte „Netzwerk“. Navigieren Sie zu `http://localhost:3000`, um sich die über das Netzwerk abgewickelten Aufrufe anzusehen.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Chrome-Registerkarte „Netzwerk“ mit der Netzwerkaktivität":::

7. Fügen Sie einen neuen Helden hinzu, indem Sie die Schaltfläche **Add New Hero** (Neuen Helden hinzufügen) wählen. Geben Sie die ID „999“, den Namen „Fred“ und den Spruch „Hello“ ein, und wählen Sie anschließend **Speichern**. Auf der Registerkarte sehen Sie, dass Sie eine POST-Anforderung für einen neuen Helden gesendet haben. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Chrome-Registerkarte „Netzwerk“ mit der Netzwerkaktivität für Get- und Post-Funktionen":::

    Im nächsten Schritt fügen wir der App die Put- und die Delete-Funktion hinzu.

## <a name="add-the-put-and-delete-functions"></a>Hinzufügen der Put- und der Delete-Funktion

1. Fügen Sie in **routes.js** nach dem Router „post“ die Router `put` und `delete` hinzu.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Kopieren Sie den folgenden Code, und fügen Sie ihn nach der Funktion `checkServerError` in **hero.service.js** ein. Für diesen Code gilt Folgendes:
   * Er erstellt die Funktionen `put` und `delete`.
   * Er überprüft, ob der Held gefunden wurde.
   * Er führt eine Fehlerbehandlung durch. 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Exportieren Sie in **hero.service.js** die neuen Module:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Da wir den Code nun aktualisiert haben, können Sie in Visual Studio Code die Schaltfläche **Neu starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: auswählen.

5. Aktualisieren Sie die Seite in Ihrem Internetbrowser, und wählen Sie die Schaltfläche **Add New Hero** (Neuen Helden hinzufügen). Fügen Sie einen neuen Helden mit der ID „9“, dem Namen „Starlord“ und dem Spruch „Hi“ hinzu. Wählen Sie die Schaltfläche **Save** (Speichern), um den neuen Helden zu speichern.

6. Wählen Sie als Nächstes den Helden **Starlord** aus, ändern Sie den Spruch von „Hi“ in „Bye“, und wählen Sie anschließend die Schaltfläche **Save** (Speichern). 

    Nun können Sie die ID auf der Registerkarte „Netzwerk“ auswählen, um die Nutzlast anzuzeigen. In der Nutzlast sehen Sie, dass der Spruch jetzt „Bye“ lautet.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="Heroes-App und Registerkarte „Netzwerk“ mit der Nutzlast"::: 

    Sie können auch einen der Helden über die Benutzeroberfläche löschen und sich ansehen, wie lange die Ausführung des Löschvorgangs dauert. Wählen Sie hierzu für den Helden „Fred“ die Schaltfläche „Delete“ (Löschen).

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="Heroes-App und die Registerkarte „Netzwerk“ mit der Ausführungsdauer der Funktionen"::: 

    Wenn Sie die Seite aktualisieren, wird auf der Registerkarte „Netzwerk“ angezeigt, wie lange das Abrufen der Helden dauert. Die Zeiten sind zwar kurz, vieles hängt jedoch davon ab, wo auf der Welt sich Ihre Daten befinden und ob sie in einer Region in der Nähe Ihrer Benutzer georepliziert werden. Weitere Informationen zur Georeplikation finden Sie im nächsten Tutorial, das in Kürze veröffentlicht wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Hinzufügen von Post-, Put- und Delete-Funktionen zur App 

Wir fügen demnächst weitere Videos für diese Tutorialreihe hinzu. Schauen Sie bald wieder vorbei!

