---
title: Erstellen einer Xamarin-App mit .NET und der API für MongoDB von Azure Cosmos DB
description: Stellt ein Xamarin-Codebeispiel vor, das Sie zum Verbinden mit und zum Abfragen der API für MongoDB von Azure Cosmos DB verwenden können.
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 339c6177de6e83f463efbc97e88a36ed4c52d97b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349094"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Erstellen einer Xamarin.Forms-App mit dem .NET-SDK und der API für MongoDB von Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen.

In diesem Schnellstart wird veranschaulicht, wie Sie mithilfe des Azure-Portals ein [Cosmos-Konto, das für die API für MongoDB von Azure Cosmos DB konfiguriert ist](mongodb-introduction.md), eine Dokumentendatenbank und eine Sammlung erstellen. Anschließend erstellen Sie eine Xamarin.Forms-Aufgaben-App mithilfe des [MongoDB .NET-Treibers](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Voraussetzungen für das Ausführen der Beispiel-App

Zum Ausführen des Beispiels benötigen Sie [Visual Studio](https://www.visualstudio.com/downloads/) oder [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) und ein gültiges Azure Cosmos DB-Konto.

Falls Sie noch nicht über Visual Studio verfügen, laden Sie [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) mit der beim Setup installierten Workload **Mobile Entwicklung mit .NET** herunter.

Wenn Sie es bevorzugen, unter Mac zu arbeiten, laden Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) herunter, und führen Sie das Setup aus.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Das in diesem Artikel beschriebene Beispiel ist mit MongoDB.Driver-Version 2.6.1 kompatibel.

## <a name="clone-the-sample-app"></a>Klonen der Beispiel-App

Laden Sie die Beispiel-App zunächst von GitHub herunter. Die App implementiert eine To-Do-App mit dem Dokumentspeichermodell von MongoDB.



# <a name="windows"></a>[Windows](#tab/windows)

1. Öffnen Sie unter Windows eine Eingabeaufforderung bzw. unter macOS ein Terminal, erstellen Sie einen neuen Ordner mit dem Namen „git-samples“, und schließen Sie dann das Fenster.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Alternativ zur Verwendung von Git können Sie auch [das Projekt als ZIP-Datei herunterladen](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren.

Die folgenden Codeausschnitte stammen alle aus der `MongoService`-Klasse, die sich unter dem folgenden Pfad befindet: src/TaskList.Core/Services/MongoService.cs.

* Initialisiert den Mongo-Client:
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Ruft einen Verweis auf die Datenbank und die Sammlung ab. Das .NET-MongoDB SDK erstellt automatisch die Datenbank und die Sammlung, wenn sie noch nicht vorhanden sind:
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Ruft alle Dokumente als Liste ab:
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Fragt bestimmte Dokumente ab:
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Erstellt eine Aufgabe und fügt sie in die Sammlung ein.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Aktualisiert eine Aufgabe in einer Sammlung.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Löscht eine Aufgabe aus einer Sammlung.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto im linken Navigationsbereich auf **Verbindungszeichenfolge**, und klicken Sie anschließend auf **Lese-/Schreibschlüssel**. In den nächsten Schritten verwenden Sie die Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms, um die primäre Verbindungszeichenfolge zu kopieren.

2. Öffnen Sie die Datei **APIKeys.cs** im Verzeichnis **Helpers** (Hilfsprogramme) des Projekts **TaskList.Core**.

3. Kopieren Sie den Wert Ihrer **primären Verbindungszeichenfolge** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und legen Sie ihn als Wert des Felds **ConnectionString** in der Datei **APIKeys.cs** fest.

4. Entfernen Sie `&replicaSet=globaldb` aus der Verbindungszeichenfolge. Sie erhalten einen Laufzeitfehler, wenn Sie diesen Wert nicht aus der Abfragezeichenfolge entfernen.

> [!IMPORTANT]
> Sie müssen das Schlüssel-Wert-Paar `&replicaSet=globaldb` aus der Abfragezeichenfolge der Verbindungszeichenfolge entfernen, um einen Laufzeitfehler zu vermeiden.

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert.

## <a name="run-the-app"></a>Ausführen der App

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.
2. Klicken Sie auf **Restore all NuGet packages** (Alle NuGet-Pakete wiederherstellen).
3. Klicken Sie mit der rechten Maustaste auf **TaskList.Android**, und wählen Sie **Als Startprojekt festlegen** aus.
4. Drücken Sie die F5-TASTE, um das Debuggen der Anwendung zu starten.
5. Wenn Sie die App unter iOS ausführen möchten, müssen Sie Ihren Computer zunächst mit einem Mac-Gerät verbinden (Anweisungen finden Sie [hier](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Klicken Sie mit der rechten Maustaste auf das Projekt **TaskList.iOS**, und wählen Sie **Als Startprojekt festlegen** aus.
7. Drücken Sie die F5-TASTE, um das Debuggen der Anwendung zu starten.

### <a name="visual-studio-for-mac"></a>Visual Studio für Mac

1. Wählen Sie in der Dropdownliste für Plattformen entweder „TaskList.iOS“ oder „TaskList.Android“ aus, je nachdem, auf welcher Plattform die App ausgeführt werden soll.
2. Drücken Sie BEFEHLSTASTE+EINGABETASTE, um das Debuggen der Anwendung zu starten.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen und eine Xamarin.Forms-App mithilfe der API für MongoDB ausführen. Jetzt können Sie zusätzliche Daten in Ihr Cosmos DB-Konto importieren.

> [!div class="nextstepaction"]
> [Importieren von Daten in Azure Cosmos DB, konfiguriert mit der API für MongoDB von Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)