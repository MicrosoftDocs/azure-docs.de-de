---
title: 'Schnellstart: Tabellen-API mit Node.js: Azure Cosmos DB'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Table-API von Azure Cosmos DB eine Anwendung mit dem Azure-Portal und Node.js erstellen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: a9f5a307bfefedf74f884e39b482bd90454fc5c8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096868"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Schnellstart: Erstellen einer Tabellen-API-App mit Node.js und Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

In dieser Schnellstartanleitung erstellen Sie ein Azure Cosmos DB-Tabellen-API-Konto und verwenden den Daten-Explorer und eine über GitHub geklonte Node.js-App, um Tabellen und Entitäten zu erstellen. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit dem URI `https://localhost:8081` und dem Schlüssel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` verwenden.
- [Node.js 0.10.29 oder höher](https://nodejs.org/)
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

> [!IMPORTANT] 
> Zum Arbeiten mit den allgemein verfügbaren Tabellen-API-SDKs müssen Sie ein neues Tabellen-API-Konto erstellen. Tabellen-API-Konten, die während der Vorschau erstellt wurden, werden von den allgemein verfügbaren SDKs nicht unterstützt.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Hinzufügen einer Tabelle

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie jetzt eine Tabellen-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können.

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
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

> [!TIP]
> Eine ausführlichere exemplarische Vorgehensweise mit einem ähnlichen Code finden Sie im Artikel mit dem [Beispiel für die Cosmos DB-Tabellen-API](table-storage-how-to-use-nodejs.md). 

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit dem Abschnitt zum [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) in diesem Dokument fortfahren.

* Der folgende Code zeigt, wie eine Tabelle in Azure Storage erstellt wird:

  ```javascript
  storageClient.createTableIfNotExists(tableName, function (error, createResult) {
    if (error) return callback(error);

    if (createResult.isSuccessful) {
      console.log("1. Create Table operation executed successfully for: ", tableName);
    }
  }

  ```

* Der folgende Code zeigt, wie Daten in die Tabelle eingefügt werden:

  ```javascript
  var customer = createCustomerEntityDescriptor("Harp", "Walter", "Walter@contoso.com", "425-555-0101");

  storageClient.insertOrMergeEntity(tableName, customer, function (error, result, response) {
    if (error) return callback(error);

    console.log("   insertOrMergeEntity succeeded.");
  }
  ```

* Der folgende Code zeigt, wie Daten aus der Tabelle abgefragt werden:

  ```javascript
  console.log("6. Retrieving entities with surname of Smith and first names > 1 and <= 75");

  var storageTableQuery = storage.TableQuery;
  var segmentSize = 10;

  // Demonstrate a partition range query whereby we are searching within a partition for a set of entities that are within a specific range. 
  var tableQuery = new storageTableQuery()
      .top(segmentSize)
      .where('PartitionKey eq ?', lastName)
      .and('RowKey gt ?', "0001").and('RowKey le ?', "0075");
  
  runPageQuery(tableQuery, null, function (error, result) {
  
      if (error) return callback(error);
  
  ```

* Der folgende Code zeigt, wie Daten aus der Tabelle gelöscht werden:

  ```javascript
  storageClient.deleteEntity(tableName, customer, function entitiesQueried(error, result) {
      if (error) return callback(error);
  
      console.log("   deleteEntity succeeded.");
  }
  ```
  
## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Verbindungszeichenfolge** aus. 

    :::image type="content" source="./media/create-table-nodejs/connection-string.png" alt-text="Anzeigen und Kopieren der erforderlichen Informationen zur Verbindungszeichenfolge aus dem Bereich „Verbindungszeichenfolge“":::

2. Kopieren Sie den Wert für „PRIMÄRE VERBINDUNGSZEICHENFOLGE“ mithilfe der Kopierschaltfläche auf der rechten Seite.

3. Öffnen Sie die Datei *app.config* , und fügen Sie den Wert in „connectionString“ in Zeile 3 ein. 

    > [!IMPORTANT]
    > Wenn Ihr Endpunkt „documents.azure.com“ verwendet, bedeutet dies, dass Sie über ein Vorschaukonto verfügen und ein [neues Tabellen-API-Konto](#create-a-database-account) erstellen müssen, um mit dem allgemein verfügbaren Tabellen-API-SDK zu arbeiten.
    >

3. Speichern Sie die Datei *app.config*.

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert. 

## <a name="run-the-app"></a>Ausführen der App

1. Wechseln Sie im Terminalfenster von Git mithilfe von `cd` zum Ordner „storage-table-java-getting-started“.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. Führen Sie den folgenden Befehl aus, um die Module [azure], [node-uuid], [nconf] und [async] lokal zu installieren und um einen Eintrag für sie in der Datei *package.json* zu speichern:

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. Führen Sie im Terminalfenster von Git die folgenden Befehle aus, um die Node.js-Anwendung zu starten:

    ```
    node ./tableSample.js 
    ```

    Im Konsolenfenster werden die Tabellendaten angezeigt, die der neuen Tabellendatenbank in Azure Cosmos DB hinzugefügt werden.

    Jetzt können Sie zum Daten-Explorer zurückkehren, um diese neuen Daten anzuzeigen, abzufragen, anzupassen und mit ihnen zu arbeiten. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, eine Tabelle mit dem Daten-Explorer erstellen und eine Node.js-App ausführen, um Tabellendaten hinzuzufügen.  Jetzt können Sie Ihre Daten mit der Table-API abfragen.  

> [!div class="nextstepaction"]
> [Importieren von Daten in die Table-API](table-import.md)