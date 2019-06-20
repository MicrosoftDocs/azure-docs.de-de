---
title: 'NoSQL-Tutorial: SQL-API für das Azure Cosmos DB Java SDK'
description: Ein NoSQL-Tutorial, in dem eine Onlinedatenbank und eine Java-Konsolenanwendung mit der SQL-API für Azure Cosmos DB erstellt werden. Azure SQL ist eine NoSQL-Datenbank für JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/22/2018
ms.author: sngun
ms.openlocfilehash: 24de1fe0695d0f7634d8f20f1876b5000496dbea
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475785"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>NoSQL-Tutorial: Erstellen einer Java-Konsolenanwendung mit der SQL-API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Vorschauversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Vorschauversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Willkommen beim NoSQL-Tutorial für die SQL-API für das Azure Cosmos DB Java SDK. Im Rahmen dieses Tutorials erstellen Sie eine Konsolenanwendung, mit der Azure Cosmos DB-Ressourcen erstellt und abgefragt werden können.

Folgendes wird behandelt:

* Erstellen eines Azure Cosmos DB-Kontos und Herstellen der Verbindung
* Konfigurieren Ihrer Visual Studio-Projektmappe
* Erstellen einer Onlinedatenbank
* Erstellen einer Sammlung
* Erstellen von JSON-Dokumenten
* Abfragen der Sammlung
* Erstellen von JSON-Dokumenten
* Abfragen der Sammlung
* Ersetzen eines Dokuments
* Löschen eines Dokuments
* Löschen der Datenbank

Lassen Sie uns anfangen.

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Schritt 1: Erstellen eines Azure Cosmos DB-Kontos
Wir erstellen nun ein Azure Cosmos DB-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie mit [Klonen des GitHub-Projekts](#GitClone) fortfahren. Falls Sie den Azure Cosmos DB-Emulator verwenden, können Sie die Schritte unter [Azure Cosmos DB-Emulator](local-emulator.md) zum Einrichten des Emulators ausführen und dann mit [Klonen des GitHub-Projekts](#GitClone) fortfahren.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Schritt 2: Klonen des GitHub-Projekts
Sie können beginnen, indem Sie das GitHub-Repository für [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started) (Erste Schritte mit Azure Cosmos DB und Java) klonen. Führen Sie in einem lokalen Verzeichnis beispielsweise Folgendes aus, um das Beispielprojekt lokal abzurufen:

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Das Verzeichnis enthält die Datei `pom.xml` für das Projekt und den Ordner `src` mit dem Java-Quellcode, einschließlich `Program.java`. Hiermit wird veranschaulicht, wie Sie einfache Vorgänge mit Azure Cosmos DB durchführen (beispielsweise das Erstellen von Dokumenten und Abfragen von Daten in einer Sammlung). Die Datei `pom.xml` enthält eine Abhängigkeit im [Azure Cosmos DB Java SDK in Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Schritt 3: Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto
Navigieren Sie als Nächstes wieder zum [Azure-Portal](https://portal.azure.com), um Ihren Endpunkt und den primären Hauptschlüssel abzurufen. Der Endpunkt und der Primärschlüssel von Azure Cosmos DB sind erforderlich, damit Ihre Anwendung weiß, womit die Verbindung hergestellt werden soll, und damit Azure Cosmos DB weiß, dass die Verbindung Ihrer Anwendung vertrauenswürdig ist.

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos DB-Konto, und klicken Sie auf **Schlüssel**. Kopieren Sie den URI aus dem Portal, und fügen Sie ihn in `https://FILLME.documents.azure.com` in der Datei „Program.java“ ein. Kopieren Sie anschließend den PRIMÄRSCHLÜSSEL aus dem Portal, und fügen Sie ihn in `FILLME` ein.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Screenshot des Azure-Portals, das vom NoSQL-Tutorial zum Erstellen einer Java-Konsolenanwendung verwendet wird. Zeigt ein Azure Cosmos DB-Konto, bei dem der AKTIVE Hub, die Schaltfläche „SCHLÜSSEL“ auf dem Blatt „Azure Cosmos DB-Konto“ sowie auf dem Blatt „Schlüssel“ die Werte URI, PRIMÄRSCHLÜSSEL und SEKUNDÄRSCHLÜSSEL hervorgehoben sind.][keys]

## <a name="step-4-create-a-database"></a>Schritt 4: Erstellen einer Datenbank
Ihre Azure Cosmos DB-[Datenbank](databases-containers-items.md#azure-cosmos-databases) kann mithilfe der [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Schritt 5: Erstellen einer Sammlung
> [!WARNING]
> Mit **createCollection** wird eine neue Sammlung mit reserviertem Durchsatz erstellt. Dies wirkt sich auf die Kosten aus. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Eine Sammlung kann mithilfe der [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Schritt 6: Erstellen von JSON-Dokumenten
Ein Dokument kann mithilfe der [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)-Methode der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Wir können jetzt eines oder mehrere Dokumente einfügen. Wenn Sie bereits über Daten verfügen, die Sie in der Datenbank speichern möchten, können Sie diese mithilfe des [Datenmigrationstools](import-data.md)von Azure Cosmos DB in eine Datenbank importieren.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagramm zur hierarchischen Beziehung zwischen dem Konto, der Onlinedatenbank, der Sammlung und den Dokumenten, die vom NoSQL-Tutorial zum Erstellen einer Java-Konsolenanwendung verwendet werden.](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Schritt 7: Abfragen von Azure Cosmos DB-Ressourcen
Azure Cosmos DB unterstützt umfassende [Abfragen](how-to-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente.  Der folgende Beispielcode veranschaulicht, wie Sie Dokumente in Azure Cosmos DB abfragen, indem Sie SQL-Syntax mit der [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)-Methode verwenden.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Schritt 8: Ersetzen eines JSON-Dokuments
Azure Cosmos DB unterstützt die Aktualisierung von JSON-Dokumenten per [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument)-Methode.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Schritt 9: Löschen eines JSON-Dokuments
Azure Cosmos DB unterstützt auch das Löschen von JSON-Dokumenten mit der [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument)-Methode.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Schritt 10: Löschen der Datenbank
Wenn Sie die erstellte Datenbank löschen, werden auch alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.) gelöscht.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Schritt 11: Ausführen der gesamten Java-Konsolenanwendung
Zum Ausführen der Anwendung in der Konsole navigieren Sie zum Projektordner, und kompilieren Sie mit Maven:
    
    mvn package

Wenn Sie `mvn package` ausführen, wird die aktuelle Azure Cosmos DB-Bibliothek aus Maven heruntergeladen, und die Datei `GetStarted-0.0.1-SNAPSHOT.jar` wird erzeugt. Führen Sie die App dann wie folgt aus:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Glückwunsch! Sie haben dieses NoSQL-Tutorial abgeschlossen und verfügen über eine funktionierende Java-Konsolenanwendung!

## <a name="next-steps"></a>Nächste Schritte
* Sind Sie an einem Tutorial zu Java-Web-Apps interessiert? Weitere Informationen finden Sie unter [Erstellen einer Java-Webanwendung mithilfe von Azure Cosmos DB](sql-api-java-application.md).
* Informieren Sie sich über das [Überwachen eines Azure Cosmos DB-Kontos](monitor-accounts.md).
* Fragen Sie unser Beispieldataset im [Query Playground](https://www.documentdb.com/sql/demo)ab.

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
