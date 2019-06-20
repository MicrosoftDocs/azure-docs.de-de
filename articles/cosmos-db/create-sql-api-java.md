---
title: Erstellen einer Azure Cosmos DB-Dokumentdatenbank mit Java
description: Hier finden Sie ein Java-Codebeispiel, mit dem Sie eine Verbindung mit der SQL-API von Azure Cosmos DB herstellen und diese API abfragen können.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: c97b974919c5aec4c902867604d207cda11e8594
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754295"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>Schnellstart: Erstellen einer Java-Anwendung mithilfe eines SQL-API-Kontos für Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (Vorschauversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In dieser Schnellstartanleitung wird gezeigt, wie Sie Ressourcen eines Azure Cosmos DB-Kontos für die [SQL-API](sql-api-introduction.md) mithilfe einer Java-Anwendung erstellen und verwalten. Sie erstellen zunächst ein Azure Cosmos DB-Konto für die SQL-API und anschließend mithilfe des [SQL Java SDK](sql-api-sdk-async-java.md) eine Java-App. Dann fügen Sie mithilfe der Java-Anwendung dem Cosmos DB-Konto Ressourcen hinzu. Die Anweisungen in dieser Schnellstartanleitung gelten für alle Betriebssysteme, unter denen Java ausgeführt werden kann. Nach Abschluss dieser Schnellstartanleitung können Sie Cosmos DB-Datenbanken und -Container sowohl über die Benutzeroberfläche als auch programmgesteuert erstellen und ändern.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Außerdem haben Sie folgende Möglichkeiten: 

* [Java Development Kit (JDK), Version 8](https://aka.ms/azure-jdks)
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Ein binäres [Maven](https://maven.apache.org/)-Archiv ([Download](https://maven.apache.org/download.cgi)/[Installationsanleitung](https://maven.apache.org/install.html))
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Dokumentdatenbank müssen Sie ein SQL-API-Konto mit Azure Cosmos DB erstellen.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Hinzufügen eines Containers

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Abfragen Ihrer Daten

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen wir nun mit der Verwendung von Code. Klonen Sie zunächst eine SQL-API-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie sie aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der App](#run-the-app) fortfahren. 

* `AsyncDocumentClient`-Initialisierung [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) bietet die clientseitige logische Darstellung für den Azure Cosmos-Datenbankdienst. Mit diesem Client werden Anforderungen für den Dienst konfiguriert und ausgeführt.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Erstellung der [Datenbank](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database)

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Erstellung von [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Dokumenterstellung mithilfe der [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)-Methode

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* SQL-Abfragen über JSON erfolgen mithilfe der [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable)-Methode.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Ausführen der App

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen zur Verbindungszeichenfolge abzurufen und die App mit den Endpunktinformationen zu starten. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren.


1. Wechseln Sie im Git-Terminalfenster mithilfe von `cd` in den Ordner mit dem Beispielcode.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Java-Pakete zu installieren.

    ```bash
    mvn package
    ```

3. Starten Sie im Git-Terminalfenster die Java-Anwendung mithilfe des folgenden Befehls. (Ersetzen Sie YOUR_COSMOS_DB_HOSTNAME durch den in Anführungszeichen gesetzten URI-Wert aus dem Portal und YOUR_COSMOS_DB_MASTER_KEY durch den in Anführungszeichen gesetzten Primärschlüssel aus dem Portal.)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Im Terminalfenster wird eine Benachrichtigung angezeigt, dass die FamilyDB-Datenbank erstellt wurde. 
    
4. Drücken Sie eine Taste, um die Datenbank zu erstellen, und dann ein weitere Taste, um die Sammlung zu erstellen. 

    Wechseln Sie in Ihrem Browser zurück zum Daten-Explorer, um zu überprüfen, ob er jetzt eine FamilyDB-Datenbank und eine FamilyCollection-Sammlung enthält.

5. Wechseln Sie in das Konsolenfenster, und drücken Sie eine Taste, um das erste Dokument zu erstellen, und dann eine weitere Taste, um das zweite Dokument zu erstellen. Wechseln Sie anschließend wieder zurück in den Daten-Explorer, um die Dokumente anzuzeigen. 

6. Drücken Sie eine Taste, um eine Abfrage auszuführen und die Ausgabe im Konsolenfenster anzuzeigen. 

7. Die App löscht die erstellten Ressourcen nicht. Gehen Sie zurück zum Portal, um in Ihrem Konto [die Ressourcen zu löschen](#clean-up-resources),  damit keine Gebühren anfallen.

    ![Konsolenausgabe](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mit dem Daten-Explorer ein Azure Cosmos-Konto, eine Dokumentdatenbank und einen Container erstellen und wie Sie das gleiche Ergebnis programmgesteuert mithilfe einer App erreichen. Sie können jetzt zusätzliche Daten in Ihren Azure Cosmos-Container importieren. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](import-data.md)
