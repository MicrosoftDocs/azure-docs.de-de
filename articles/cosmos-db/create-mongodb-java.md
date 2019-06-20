---
title: Erstellen einer Konsolen-App mit der API für MongoDB von Azure Cosmos DB und dem Java SDK
description: Stellt ein Java-Codebeispiel vor, mit dem Sie eine Verbindung herstellen und Abfragen über die API für MongoDB von Azure Cosmos DB durchführen können.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 2a5cfc417f75e5bb08e416b3243903badcf572e1
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480487"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>Schnellstart: Erstellen einer Web-App mit der API für MongoDB von Azure Cosmos DB und dem Java SDK

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Cosmos DB zugrunde liegen. 

In diesem Schnellstart wird veranschaulicht, wie ein Cosmos-Konto mit der [API für MongoDB von Azure Cosmos DB](mongodb-introduction.md) erstellt wird. Anschließend erstellen Sie eine Konsolen-App mit dem [MongoDB Java-Treiber](https://docs.mongodb.com/ecosystem/drivers/java/) und stellen diese bereit. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie dieses Beispiel ausführen können, müssen folgende Voraussetzungen erfüllt sein:
* [Installieren des JDK für Azure und Azure Stack, JDK-Version 8] (https://aka.ms/azure-jdks)
* Maven (Führen Sie `apt-get install maven` aus, wenn Sie nicht über Maven verfügen)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Hinzufügen einer Sammlung

Geben Sie Ihrer neuen Datenbank den Namen **db** und der neuen Sammlung den Namen **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie jetzt eine App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Öffnen Sie dann den Code in Ihrem bevorzugten Editor. 

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. 

Die folgenden Codeausschnitte stammen alle aus der Datei „Program.java“.

* Der DocumentClient wird initialisiert.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Es werden eine neue Datenbank und eine neue Sammlung erstellt.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Einige Dokumente werden mit `MongoCollection.insertOne` eingefügt

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Einige Abfragen werden mit `MongoCollection.find` durchgeführt

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren.

1. Wählen Sie auf der Kontoseite **Schnellstart**, dann „Java“ aus, und kopieren Sie anschließend die Verbindungszeichenfolge in die Zwischenablage

2. Öffnen Sie die Datei `Program.java`, und ersetzen Sie das Argument des MongoClientURI-Konstruktors durch die Verbindungszeichenfolge. Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert. 
    
## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Führen Sie `mvn package` in einem Terminal aus, um erforderliche NPM-Module zu installieren

2. Führen Sie `mvn exec:java -D exec.mainClass=GetStarted.Program` in einem Terminal aus, um Ihre Java-Anwendung zu starten.

Jetzt können Sie [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) verwenden, um Ihre neuen Daten abzufragen, anzupassen und mit diesen zu arbeiten.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Cosmos-Konto erstellen, eine Sammlung erstellen und eine Konsolen-App ausführen. Jetzt können Sie zusätzliche Daten in Ihre Cosmos-Datenbank importieren.

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](mongodb-migrate.md)
