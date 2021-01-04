---
title: Erstellen einer Web-App mit der API für MongoDB von Azure Cosmos DB und dem .NET SDK
description: Stellt ein .NET-Codebeispiel vor, mit dem Sie eine Verbindung herstellen und Abfragen über die API für MongoDB von Azure Cosmos DB durchführen können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d3c73bdc107ce1971e2958073ad5c100c6bf6bd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349196"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Erstellen einer .NET-Web-App mit der API für MongoDB von Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB ist die schnelle NoSQL-Datenbank von Microsoft mit offenen APIs für jede Größenordnung. Sie können schnell Dokument-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Cosmos DB zugrunde liegen. 

In diesem Schnellstart wird veranschaulicht, wie ein Cosmos-Konto mit der [API für MongoDB von Azure Cosmos DB](mongodb-introduction.md) erstellt wird. Anschließend erstellen Sie eine Web-App für Aufgabenlisten mit dem [MongoDB .NET-Treiber](https://docs.mongodb.com/ecosystem/drivers/csharp/) und stellen diese bereit.

## <a name="prerequisites-to-run-the-sample-app"></a>Voraussetzungen für das Ausführen der Beispiel-App

Zum Ausführen des Beispiels benötigen Sie [Visual Studio](https://www.visualstudio.com/downloads/) und ein gültiges Azure Cosmos DB-Konto.

Falls Sie noch nicht über Visual Studio verfügen, laden Sie [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) mit der beim Setup installierten Workload **ASP.NET und Webentwicklung** herunter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Das in diesem Artikel beschriebene Beispiel ist mit MongoDB.Driver-Version 2.6.1 kompatibel.

## <a name="clone-the-sample-app"></a>Klonen der Beispiel-App

Laden Sie die Beispiel-App zunächst von GitHub herunter. 

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Alternativ zur Verwendung von Git können Sie auch [das Projekt als ZIP-Datei herunterladen](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. 

Die folgenden Codeausschnitte stammen alle aus der Datei „Dal.cs“ im Verzeichnis „DAL“.

* Initialisieren Sie den Client.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Rufen Sie die Datenbank und die Sammlung ab.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Rufen Sie alle Dokumente ab.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Erstellen einer Aufgabe und Einfügen der Aufgabe in die Sammlung

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Ebenso können Sie Dokumente mit den Methoden [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) und [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) aktualisieren und löschen. 

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Cosmos-Konto im linken Navigationsbereich auf **Verbindungszeichenfolge**, und klicken Sie anschließend auf **Lese-/Schreibschlüssel**. Kopieren Sie im nächsten Schritt mithilfe der Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms den Benutzernamen, das Kennwort sowie den Host in die Datei „dal.cs“.

2. Öffnen Sie die Datei **dal.cs** im Verzeichnis **DAL**. 

3. Kopieren Sie den Wert des **Benutzernamens** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und legen Sie ihn in der Datei **dal.cs** als Wert des **Benutzernamens** fest. 

4. Kopieren Sie anschließend den Wert des **Hosts** aus dem Portal, und legen Sie ihn in der Datei **dal.cs** als Wert des **Hosts** fest. 

5. Kopieren Sie anschließend den Wert des **Kennworts** aus dem Portal, und legen Sie ihn in der Datei **dal.cs** als Wert des **Kennworts** fest. 

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Cosmos DB aktualisiert. 
    
## <a name="run-the-web-app"></a>Ausführen der Web-App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**. 

2. Geben Sie im NuGet-Feld **Durchsuchen** den Suchbegriff *MongoDB.Driver* ein.

3. Installieren Sie die Bibliothek **MongoDB.Driver** mithilfe der Ergebnisse. Dadurch wird das Paket „MongoDB.Driver“ mit all seinen Abhängigkeiten installiert.

4. Drücken Sie STRG+F5, um die Anwendung auszuführen. Ihre App wird im Browser angezeigt. 

5. Klicken Sie im Browser auf **Erstellen**, und erstellen Sie einige Aufgaben in Ihrer Aufgabenlisten-App.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Cosmos-Konto erstellen, eine Sammlung erstellen und eine Konsolen-App ausführen. Jetzt können Sie zusätzliche Daten in Ihre Cosmos-Datenbank importieren. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)