---
title: 'Schnellstart: Erstellen einer Web-App mit der Azure Cosmos DB-API für MongoDB und Java SDK'
description: Erfahren Sie, wie Sie ein Java-Codebeispiel erstellen, mit dem Sie eine Verbindung herstellen und Abfragen über die Azure Cosmos DB-API für MongoDB durchführen können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 8a8fa9e15630aea3fb92571fd88a0ce01d3c9211
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349111"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Schnellstart: Erstellen einer Konsolen-App mit Java und der MongoDB-API in Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB für MongoDB-API-Konto im Azure-Portal und fügen Daten mithilfe einer über GitHub geklonten Java SDK-App hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit der Verbindungszeichenfolge `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` verwenden.
- [Java Development Kit (JDK), Version 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) 
- [Maven](https://maven.apache.org/download.cgi). Oder führen Sie `apt-get install maven` aus, um Maven zu installieren.
- [Git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Hinzufügen einer Sammlung

Geben Sie Ihrer neuen Datenbank den Namen **db** und der neuen Sammlung den Namen **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-mongodb-create-collection.md)] 

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

Die folgenden Codeausschnitte stammen alle aus der Datei *Program.java*.

Diese Konsolen-App verwendet den [Java-Treiber von MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

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

1. Wählen Sie im Azure Cosmos DB-Konto **Schnellstart** und dann **Java** aus, und kopieren Sie anschließend die Verbindungszeichenfolge in die Zwischenablage.

2. Öffnen Sie die Datei *Program.java*, und ersetzen Sie das Argument des MongoClientURI-Konstruktors durch die Verbindungszeichenfolge. Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert. 
    
## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Führen Sie `mvn package` in einem Terminal aus, um erforderliche NPM-Module zu installieren

2. Führen Sie `mvn exec:java -D exec.mainClass=GetStarted.Program` in einem Terminal aus, um Ihre Java-Anwendung zu starten.

Jetzt können Sie [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) verwenden, um Ihre neuen Daten abzufragen, anzupassen und mit diesen zu arbeiten.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB für MongoDB-API-Konto erstellen, eine Datenbank und einen Container mit dem Daten-Explorer erstellen und Daten mithilfe einer Java-Konsolen-App hinzufügen. Jetzt können Sie zusätzliche Daten in Ihre Cosmos-Datenbank importieren. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)