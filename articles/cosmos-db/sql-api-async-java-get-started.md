---
title: 'Tutorial: Erstellen einer Java-App mit dem Async Java SDK zum Verwalten eines SQL-API-Kontos in Azure Cosmos DB'
description: In diesem Tutorial wird veranschaulicht, wie Sie mithilfe einer Async Java-Anwendung Daten in einem SQL-API-Konto in Azure Cosmos DB speichern und darauf zuzugreifen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b46914728c684fe4b28cb1325afb1e0b662522ad
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475815"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Erstellen einer Java-App mit dem Async Java SDK zum Verwalten der in einem SQL-API-Konto gespeicherten Daten

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Vorschauversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Vorschauversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als Entwickler besitzen Sie möglicherweise Anwendungen, die NoSQL-Dokumentdaten verwenden. Sie können das SQL-API-Konto in Azure Cosmos DB verwenden, um diese Dokumentdaten zu speichern und darauf zuzugreifen. In diesem Tutorial erfahren Sie, wie Sie mit dem Async Java SDK eine Java-Anwendung erstellen, um Dokumentdaten zu speichern und darauf zuzugreifen. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos-Kontos und Herstellen der Verbindung
> * Konfigurieren Ihrer Lösung
> * Erstellen einer Sammlung
> * Erstellen von JSON-Dokumenten
> * Abfragen der Sammlung

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über die folgenden Ressourcen verfügen:

* Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren. 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8 oder höher](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie mit den folgenden Schritten ein Azure Cosmos-Konto:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Klonen des GitHub-Repositorys

Klonen Sie das GitHub-Repository für [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) (Erste Schritte mit Azure Cosmos DB und Java). Führen Sie in einem lokalen Verzeichnis beispielsweise Folgendes aus, um das Beispielprojekt lokal abzurufen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Das Verzeichnis enthält die Datei `pom.xml` und den Ordner `src/main/java/com/microsoft/azure/cosmosdb/sample` mit Java-Quellcode (einschließlich `Main.java`). Das Projekt enthält Code zum Ausführen von Vorgängen mit Azure Cosmos DB. Hierzu zählen beispielsweise das Erstellen von Dokumenten sowie das Abfragen von Daten in einer Auflistung. Die Datei `pom.xml` enthält eine Abhängigkeit im [Azure Cosmos DB Java SDK in Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Herstellen einer Verbindung mit einem Azure Cosmos-Konto

Kehren Sie als Nächstes zum [Azure-Portal](https://portal.azure.com) zurück, um Ihren Endpunkt und den primären Hauptschlüssel abzurufen. Der Endpunkt und der Primärschlüssel von Azure Cosmos DB sind erforderlich, damit Ihre Anwendung weiß, womit die Verbindung hergestellt werden soll, und damit Azure Cosmos DB weiß, dass die Verbindung Ihrer Anwendung vertrauenswürdig ist. Die Datei `AccountSettings.java` enthält den Primärschlüssel und URI-Werte. 

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos-Konto, und klicken Sie auf **Schlüssel**. Kopieren Sie den URI und den PRIMÄRSCHLÜSSEL aus dem Portal, und fügen Sie diese Angaben in die Datei `AccountSettings.java` ein. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Screenshot: Abrufen von Schlüsseln über das Portal][keys]

## <a name="initialize-the-client-object"></a>Initialisieren des Clientobjekts

Initialisieren Sie das Clientobjekt, indem Sie die in der Datei „AccountSettings.java“ definierten Werte für Host-URI und Primärschlüssel verwenden.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Erstellen einer Datenbank

Erstellen Sie Ihre Azure Cosmos DB-Datenbank mithilfe der `createDatabaseIfNotExists()`-Methode der DocumentClient-Klasse. Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Erstellen einer Sammlung

Sie können eine Sammlung mithilfe der `createDocumentCollectionIfNotExists()`-Methode der DocumentClient-Klasse erstellen. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik.

> [!WARNING]
> Mit **createCollection** wird eine neue Sammlung mit reserviertem Durchsatz erstellt. Dies wirkt sich auf die Kosten aus. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Erstellen von JSON-Dokumenten

Erstellen Sie ein Dokument mithilfe der createDocument-Methode der DocumentClient-Klasse. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Wir können jetzt eines oder mehrere Dokumente einfügen. In der Datei „src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java“ ist die Familie mit den JSON-Dokumenten definiert. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Abfragen von Azure Cosmos DB-Ressourcen

Azure Cosmos DB unterstützt umfassende Abfragen der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode veranschaulicht, wie Sie Dokumente in Azure Cosmos DB mithilfe der SQL-Syntax mit der `queryDocuments`-Methode abfragen.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Ausführen Ihrer Java-Konsolenanwendung

Zum Ausführen der Anwendung in der Konsole navigieren Sie zum Projektordner, und kompilieren Sie mit Maven:

```bash
mvn package
```

Wenn Sie `mvn package` ausführen, wird die aktuelle Azure Cosmos DB-Bibliothek aus Maven heruntergeladen, und die Datei `GetStarted-0.0.1-SNAPSHOT.jar` wird erzeugt. Führen Sie die App dann wie folgt aus:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Sie haben dieses NoSQL-Tutorial abgeschlossen und verfügen nun über eine funktionierende Java-Konsolenanwendung.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Azure Cosmos-Konto und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine Java-App mit dem Async Java SDK zum Verwalten von SQL-API-Daten in Azure Cosmos DB erstellen. Sie können jetzt mit dem nächsten Artikel fortfahren:

> [!div class="nextstepaction"]
> [Node.js-Tutorial: Erstellen einer Node.js-Konsolenanwendung mit dem JavaScript SDK zum Verwalten der SQL-API-Daten für Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
