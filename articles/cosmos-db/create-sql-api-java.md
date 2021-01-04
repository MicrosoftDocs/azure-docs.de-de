---
title: 'Schnellstart: Verwenden von Java zum Erstellen einer Dokumentdatenbank mithilfe von Azure Cosmos DB'
description: Diese Schnellstartanleitung enthält ein Java-Codebeispiel, mit dem Sie eine Verbindung mit der SQL-API von Azure Cosmos DB herstellen und diese API abfragen können.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090408"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Schnellstart: Erstellen einer Java-App zum Verwalten von Azure Cosmos DB-SQL-API-Daten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK V4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-SQL-API-Konto im Azure-Portal mithilfe einer über GitHub geklonten Java-App. Sie erstellen zunächst über das Azure-Portal ein Azure Cosmos DB-SQL-API-Konto und anschließend mithilfe des SQL Java SDK eine Java-App. Dann fügen Sie mithilfe der Java-Anwendung dem Cosmos DB-Konto Ressourcen hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

> [!IMPORTANT]  
> Diese Schnellstartanleitung gilt nur für Azure Cosmos DB Java SDK V4. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-java-v4.md) zu Azure Cosmos DB Java SDK V4, im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos), in den [Tipps zur Leistungssteigerung](performance-tips-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4 und im [Leitfaden zur Problembehandlung](troubleshoot-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4. Wenn Sie aktuell eine ältere Version als V4 verwenden, lesen Sie den Leitfaden [Migrieren zu Azure Cosmos DB Java SDK V4](migrate-java-v4-sdk.md), um Hilfe beim Aktualisieren auf V4 zu erhalten.
>

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit dem URI `https://localhost:8081` und dem Schlüssel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` verwenden.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) Die Umgebungsvariable `JAVA_HOME` muss auf den Ordner verweisen, in dem das JDK installiert ist.
- Ein [binäres Maven-Archiv](https://maven.apache.org/download.cgi). Führen Sie unter Ubuntu `apt-get install maven` aus, um Maven zu installieren.
- [Git](https://www.git-scm.com/downloads). Führen Sie unter Ubuntu `sudo apt-get install git` aus, um Git zu installieren.

## <a name="introductory-notes"></a>Einführende Hinweise

*Die Struktur eines Cosmos DB-Kontos.* Ein Cosmos DB- *Konto* enthält unabhängig von der API oder der Programmiersprache null oder mehr *Datenbanken* , eine *Datenbank* (DB) enthält null oder mehr *Container* , und ein *Container* enthält null oder mehr Elemente, wie im folgenden Diagramm zu sehen:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entitäten in einem Azure Cosmos-Konto" border="false":::

Weitere Informationen zu Datenbanken, Containern und Elementen finden Sie [hier](account-databases-containers-items.md). Einige wichtige Eigenschaften werden auf der Containerebene definiert. Hierzu zählen unter anderem der *bereitgestellte Durchsatz* und der *Partitionsschlüssel*. 

Der bereitgestellte Durchsatz wird in Anforderungseinheiten (Request Units, *RUs* ) gemessen. Diese haben einen Preis und wirken sich erheblich auf die Betriebskosten des Kontos aus. Der bereitgestellte Durchsatz kann container- oder datenbankspezifisch ausgewählt werden. In der Regel wird jedoch die containerspezifische Durchsatzangabe bevorzugt. Weitere Informationen zur Durchsatzbereitstellung finden Sie [hier](set-throughput.md).

Wenn Elemente in einen Cosmos DB-Container eingefügt werden, wird die Datenbank horizontal skaliert, indem weitere Speicher- und Computeressourcen zur Bewältigung der Anforderungen hinzugefügt werden. Speicher- und Computekapazität werden in diskreten Einheiten ( *Partitionen* ) hinzugefügt. In Ihren Dokumenten muss ein Feld als Partitionsschlüssel ausgewählt werden, durch den jedes Dokument einer Partition zugeordnet wird. Zur Verwaltung von Partitionen wird jeder Partition ein ungefähr gleicher Slice aus dem Bereich der Partitionsschlüsselwerte zugewiesen. Es empfiehlt sich daher, einen relativ zufälligen oder gleichmäßig verteilten Partitionsschlüssel zu wählen. Andernfalls fallen für einige Partitionen deutlich mehr Anforderungen an als für andere. Diese werden als *heiße Partitionen* bzw. *kalte Partitionen* bezeichnet und sollten vermieden werden. Weitere Informationen zur Partitionierung finden Sie [hier](partitioning-overview.md).

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

Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der App](#run-the-app) fortfahren. 


# <a name="sync-api"></a>[Synchronisierungs-API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Verwalten von Datenbankressourcen mithilfe der synchronen API (sync)

* `CosmosClient`-Initialisierung `CosmosClient` bietet die clientseitige logische Darstellung für den Azure Cosmos-Datenbankdienst. Mit diesem Client werden Anforderungen für den Dienst konfiguriert und ausgeführt.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Erstellung von `CosmosDatabase`:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Erstellung von `CosmosContainer`:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Erstellung eines Elements mithilfe der Methode `createItem`:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Punktlesevorgänge werden mithilfe der Methode `readItem` ausgeführt.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-Abfragen über JSON erfolgen mithilfe der `queryItems`-Methode:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Asynchrone API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Verwalten von Datenbankressourcen mithilfe der asynchronen API (async)

* Aufrufe der asynchronen API werden sofort abgeschlossen, ohne auf eine Antwort vom Server zu warten. Die folgenden Codeausschnitte enthalten geeignete Entwurfsmuster, um die obigen Verwaltungsaufgaben unter Verwendung der asynchronen API durchzuführen.

* `CosmosAsyncClient`-Initialisierung `CosmosAsyncClient` bietet die clientseitige logische Darstellung für den Azure Cosmos-Datenbankdienst. Mit diesem Client werden asynchrone Anforderungen für den Dienst konfiguriert und ausgeführt.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* Erstellung von `CosmosAsyncDatabase`:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Erstellung von `CosmosAsyncContainer`:

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Für die Erstellung wird genau wie bei der synchronen API die Methode `createItem` verwendet. Dieses Beispiel zeigt, wie Sie zahlreiche asynchrone Anforderungen vom Typ `createItem` effizient ausgeben können. Hierzu wird ein reaktiver Stream abonniert, der die Anforderungen sowie Benachrichtigungen ausgibt. Da dieses einfache Beispiel bis zum Abschluss ausgeführt und dann beendet wird, werden Instanzen vom Typ `CountDownLatch` verwendet, um sicherzustellen, dass das Programm nicht während der Elementerstellung beendet wird. **Bei der asynchronen Programmierung dürfen asynchrone Vorgänge nicht zu einer Blockierung führen. In der Praxis werden Anforderungen im Rahmen einer main()-Schleife generiert, die unbegrenzt ausgeführt wird, sodass kein Latch für asynchrone Aufrufe benötigt wird.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Punktlesevorgänge werden genau wie bei der synchronen API mithilfe der Methode `readItem` ausgeführt.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* SQL-Abfragen über JSON werden genau wie bei der synchronen API mithilfe der Methode `queryItems` ausgeführt.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Ausführen der App

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen zur Verbindungszeichenfolge abzurufen und die App mit den Endpunktinformationen zu starten. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren.

1. Wechseln Sie im Git-Terminalfenster mithilfe von `cd` in den Ordner mit dem Beispielcode.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Java-Pakete zu installieren.

    ```bash
    mvn package
    ```

3. Starten Sie im Git-Terminalfenster die Java-Anwendung mithilfe des folgenden Befehls. Ersetzen Sie „SYNCASYNCMODE“ durch `sync` oder `async` (je nachdem, welchen Beispielcode Sie ausführen möchten), ersetzen Sie „YOUR_COSMOS_DB_HOSTNAME“ durch den in Anführungszeichen gesetzten URI-Wert aus dem Portal und „YOUR_COSMOS_DB_MASTER_KEY“ durch den in Anführungszeichen gesetzten Primärschlüssel aus dem Portal.

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Im Terminalfenster wird eine Benachrichtigung angezeigt, dass die FamilyDB-Datenbank erstellt wurde. 
    
4. Die App erstellt eine Datenbank mit dem Namen `AzureSampleFamilyDB`.
5. Die App erstellt einen Container mit dem Namen `FamilyContainer`.
6. Die App führt Punktlesevorgänge mithilfe der Objekt-IDs und des Partitionsschlüsselwerts aus (in unserem Beispiel „lastName“). 
7. Die App fragt Elemente ab, um alle Familien mit dem entsprechenden Nachnamen („Andersen“, „Wakefield“, „Johnson“) abzurufen.

7. Die App löscht die erstellten Ressourcen nicht. Gehen Sie zurück zum Portal, um in Ihrem Konto [die Ressourcen zu löschen](#clean-up-resources),  damit keine Gebühren anfallen.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-SQL-API-Konto erstellen, eine Dokumentdatenbank und einen Container mit dem Daten-Explorer erstellen und eine Java-App ausführen, um das gleiche Ergebnis programmgesteuert zu erreichen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](import-data.md)
