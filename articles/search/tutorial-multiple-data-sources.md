---
title: C#-Tutorial zum Indizieren mehrerer Azure-Datenquellen
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie mithilfe von Indexern Daten aus mehreren Datenquellen in einen einzelnen Azure Cognitive Search-Index importieren. Dieses Tutorial und der Beispielcode gelten für C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df7dcf9859b6942662ae447af836f59985e2d11a
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509502"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Tutorial: Indizieren von mehreren Datenquellen mithilfe des .NET SDK

Azure Cognitive Search kann Daten aus mehreren Datenquellen importieren, analysieren und in einem einzelnen konsolidierten Suchindex indizieren. 

In diesem Tutorial werden C# und die Clientbibliothek [Azure.Search.Documents](/dotnet/api/overview/azure/search) im Azure SDK für .NET verwendet, um Beispielhoteldaten aus einer Azure Cosmos DB-Instanz zu indizieren und mit Hotelzimmerdetails aus Azure Blob Storage-Dokumenten zusammenzuführen. Das Ergebnis ist ein kombinierter Hotelsuchindex mit Hoteldokumenten und Zimmern als komplexe Datentypen.

Sie führen in diesem Tutorial die folgenden Aufgaben durch:

> [!div class="checklist"]
> * Hochladen von Beispieldaten und Erstellen von Datenquellen
> * Ermitteln des Dokumentschlüssels
> * Definieren und Erstellen des Index
> * Indizieren von Hoteldaten aus Azure Cosmos DB
> * Zusammenführen von Hotelzimmerdaten aus Blob Storage

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="overview"></a>Übersicht

In diesem Tutorial wird die neue Clientbibliothek [Azure.Search.Documents](/dotnet/api/overview/azure/search) (Version 11.x) verwendet, um mehrere Indexer zu erstellen und auszuführen. In diesem Tutorial richten Sie zwei Azure-Datenquellen so ein, dass Sie einen Indexer konfigurieren können, der zum Auffüllen eines einzelnen Suchindex Pullvorgänge für beide Quellen ausführt. Die beiden Datasets müssen einen gemeinsamen Wert aufweisen, um die Zusammenführung zu unterstützen. In diesem Beispiel ist dieses Feld eine ID. Solange es ein gemeinsames Feld zur Unterstützung der Zuordnung gibt, kann ein Indexer Daten aus unterschiedlichen Ressourcen zusammenführen: strukturierte Daten aus Azure SQL, unstrukturierte Daten aus Blobspeicher oder eine beliebige Kombination aus [unterstützten Datenquellen](search-indexer-overview.md#supported-data-sources) in Azure.

Eine fertige Version des Codes in diesem Tutorial finden Sie im folgenden Projekt:

* [multiple-data-sources/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Dieses Tutorial wurde aktualisiert, sodass es das Paket „Azure.Search.Documents“ (Version 11) verwendet. Eine frühere Version des .NET SDK finden Sie auf GitHub im [Codebeispiel „Microsoft.Azure.Search“ (Version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10).

## <a name="prerequisites"></a>Voraussetzungen

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage (in englischer Sprache)](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [NuGet-Paket „Azure Cognitive Search“ (Version 11.x) ](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Neuer](search-create-service-portal.md) oder [bereits vorhandener](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Suchdienst 

> [!Note]
> In diesem Tutorial können Sie den kostenlosen Dienst verwenden. Der kostenlose Suchdienst ist auf drei Indizes, drei Indexer und drei Datenquellen beschränkt. In diesem Tutorial wird davon jeweils eine Instanz erstellt. Vergewissern Sie sich zunächst, dass Ihr Dienst über genügend freie Kapazität für die neuen Ressourcen verfügt.

## <a name="1---create-services"></a>1\. Erstellen der Dienste

In diesem Tutorial wird Azure Cognitive Search für die Indizierung und für Abfragen verwendet, und Azure Cosmos DB wird für das eine Dataset und Azure-Blobspeicher für das andere Dataset genutzt. 

Alle Dienste sollten nach Möglichkeit in derselben Region und Ressourcengruppe erstellt werden, um eine möglichst große Nähe zu erreichen und die Verwaltung zu vereinfachen. In der Praxis können sich Ihre Dienste in einer beliebigen Region befinden.

In diesem Beispiel werden zwei kleine Datensätze verwendet, die sieben fiktive Hotels beschreiben. Ein Datensatz beschreibt die Hotels an sich und wird in eine Azure Cosmos DB-Datenbank geladen. Der andere Datensatz enthält Hotelzimmerdetails und wird in Form von sieben separaten JSON-Dateien bereitgestellt, die in Azure Blob Storage hochgeladen werden.

### <a name="start-with-cosmos-db"></a>Beginnen mit Cosmos DB

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zur Übersichtsseite Ihres Azure Cosmos DB-Kontos.

1. Wählen Sie **Daten-Explorer** und danach **Neue Datenbank** aus.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Erstellen einer neuen Datenbank" border="false":::

1. Geben Sie den Namen **hotel-rooms-db** ein. Übernehmen Sie für die übrigen Einstellungen die Standardwerte.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Konfigurieren der Datenbank" border="false":::

1. Erstellen Sie einen neuen Container. Verwenden Sie die soeben erstellte Datenbank. Geben Sie **hotels** als Containername ein, und verwenden Sie **/HotelId** als Partitionsschlüssel.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Hinzufügen eines Containers" border="false":::

1. Wählen Sie unterhalb von **hotels** den Eintrag **Elemente** aus, und klicken Sie auf der Befehlsleiste auf **Element hochladen**. Navigieren Sie im Projektordner zur Datei **cosmosdb/HotelsDataSubset_CosmosDb.json**, und wählen Sie die Datei aus.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Hochladen in die Azure Cosmos DB-Sammlung" border="false":::

1. Aktualisieren Sie mithilfe der Schaltfläche „Aktualisieren“ die Elementansicht in der Hotelsammlung. Daraufhin sollten sieben neue Datenbankdokumente aufgeführt werden.

1. Kopieren Sie eine Zeichenfolge von der Seite **Schlüssel** in Editor. Sie benötigen sie später für **appsettings.json**. Wenn Sie nicht den vorgeschlagenen Datenbanknamen „hotel-rooms-db“ verwendet haben, kopieren Sie außerdem den Datenbanknamen.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, navigieren Sie zu Ihrem Azure-Speicherkonto, und klicken Sie nacheinander auf **Blobs** und **+ Container**.

1. [Erstellen Sie einen Blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) mit dem Namen **hotel-rooms** zum Speichern der exemplarischen JSON-Hotelzimmerdateien. Sie können die öffentliche Zugriffsebene auf beliebige gültige Werte festlegen.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Erstellen eines Blobcontainers" border="false":::

1. Nachdem der Container erstellt wurde, öffnen Sie ihn, und wählen Sie auf der Befehlsleiste die Option **Hochladen** aus. Navigieren Sie zu dem Ordner, der die Beispieldateien enthält. Wählen Sie alle Dateien aus, und klicken Sie dann auf **Hochladen**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Hochladen von Dateien" border="false":::

1. Kopien Sie den Speicherkontonamen und eine Verbindungszeichenfolge von der Seite **Zugriffsschlüssel** in Editor. Sie benötigen beide Werte später für **appsettings.json**.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Die dritte Komponente, Azure Cognitive Search, können Sie [im Portal erstellen](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopieren eines Administrator-API-Schlüssels und einer URL für Azure Cognitive Search

Für die Authentifizierung bei Ihrem Suchdienst benötigen Sie die Dienst-URL und einen Zugriffsschlüssel.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Abrufen des Dienstnamens sowie der Administrator- und Abfrageschlüssel" border="false":::

Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="2---set-up-your-environment"></a>2\. Einrichten Ihrer Umgebung

1. Starten Sie Visual Studio, und wählen Sie im Menü **Extras** die Option **NuGet-Paket-Manager** und dann **NuGet-Pakete für Projektmappe verwalten...** aus. 

1. Suchen Sie auf der Registerkarte **Durchsuchen** nach **Azure.Search.Documents** (Version 11.0 oder höher), und führen Sie die Installation durch. Klicken Sie sich durch die zusätzlichen Dialogfelder, um die Installation abzuschließen.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Hinzufügen von Azure-Bibliotheken per NuGet" border="false":::

1. Suchen Sie nach den NuGet-Paketen **Microsoft.Extensions.Configuration** und **Microsoft.Extensions.Configuration.Json**, und installieren Sie sie ebenfalls.

1. Öffnen Sie die Projektmappendatei **/v11/AzureSearchMultipleDataSources.sln**.

1. Bearbeiten Sie im Projektmappen-Explorer die Datei **appsettings.json**, um Verbindungsinformationen hinzuzufügen.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Die ersten beiden Einträge sind die URL und die Administratorschlüssel für einen Suchdienst. Verwenden Sie den vollständigen Endpunkt, etwa `https://mydemo.search.windows.net`.

Die nächsten Einträge dienen zum Angeben der Kontonamen und Verbindungszeichenfolgeninformationen für die Azure Blob Storage- und die Azure Cosmos DB-Datenquelle.

## <a name="3---map-key-fields"></a>3\. Zuordnen von Schlüsselfeldern

Zum Zusammenführen von Inhalten ist es erforderlich, dass beide Datenströme im Suchindex auf dieselben Dokumente ausgerichtet sind. 

In Azure Cognitive Search werden die einzelnen Dokumente durch das Schlüsselfeld eindeutig identifiziert. Jeder Suchindex muss über genau ein Schlüsselfeld vom Typ `Edm.String`verfügen. Dieses Schlüsselfeld muss für jedes Dokument in einer Datenquelle vorhanden sein, die dem Index hinzugefügt wird. (Es ist gleichzeitig das einzige erforderliche Feld.)

Stellen Sie beim Indizieren von Daten aus mehreren Datenquellen sicher, dass jede eingehende Zeile bzw. jedes Dokument einen gemeinsamen Dokumentschlüssel enthält, um Daten aus zwei physisch getrennten Quelldokumenten im kombinierten Index in einem neuen Suchdokument zusammenzuführen. 

Häufig ist ein gewisses Maß an Vorausplanung erforderlich, um einen sinnvollen Dokumentschlüssel für Ihren Index zu bestimmen und sicherzustellen, dass er in beiden Datenquellen vorhanden ist. In dieser Demo ist der Schlüssel `HotelId` für jedes Hotel in Cosmos DB auch in den JSON-Blobs für die Zimmer im Blobspeicher vorhanden.

Azure Cognitive Search-Indexer können Feldzuordnungen verwenden, um im Zuge der Indizierung den Namen und sogar das Format von Datenfeldern zu ändern, damit Quelldaten dem korrekten Indexfeld zugeführt werden können. In Cosmos DB hat die Hotel-ID den Namen **`HotelId`** . In den JSON-Blobdateien für die Hotelzimmer lautet die Hotel-ID dagegen **`Id`** . Das Programm behandelt diese Diskrepanz, indem es das Feld **`Id`** aus den Blobs dem Schlüsselfeld **`HotelId`** im Indexer zuordnet.

> [!NOTE]
> Automatisch generierte Dokumentschlüssel (wie sie etwa standardmäßig von einigen Indexern erstellt werden) eignen sich häufig nicht besonders gut für kombinierte Indizes. Grundsätzlich empfiehlt es sich, einen aussagekräftigen, eindeutigen Schlüsselwert zu verwenden, der bereits in Ihren Datenquellen vorhanden ist oder diesen problemlos hinzugefügt werden kann.

## <a name="4---explore-the-code"></a>4\. Untersuchen des Codes

Sobald die Daten und Konfigurationseinstellungen vorhanden sind, kann das Beispielprogramm in **/v11/AzureSearchMultipleDataSources.sln** erstellt und ausgeführt werden.

Diese einfache C#/.NET-Konsolen-App führt folgende Aufgaben aus:

* Sie erstellt auf Grundlage der Datenstruktur der C#-Klasse „Hotel“ (die auch auf die Klassen „Address“ und „Room“ verweist) einen neuen Index.
* Sie erstellt eine neue Datenquelle und einen Indexer, der Azure Cosmos DB-Daten Indexfeldern zuordnet. Beides sind Objekte in Azure Cognitive Search.
* Sie führt den Indexer aus, um Hoteldaten aus Cosmos DB zu laden.
* Sie erstellt eine zweite Datenquelle und einen Indexer, der JSON-Blobdaten Indexfeldern zuordnet.
* Sie führt den zweiten Indexer aus, um Zimmerdaten aus dem Blobspeicher zu laden.

 Nehmen Sie sich vor dem Ausführen des Programms etwas Zeit, um sich den Code sowie die Index- und Indexerdefinitionen für dieses Beispiel etwas genauer anzusehen. Der relevante Code befindet sich in zwei Dateien:

  + **Hotel.cs** enthält das Schema, das den Index definiert.
  + **Program.cs** enthält Funktionen zum Erstellen des Azure Cognitive Search-Index, der Datenquellen und der Indexer sowie zum Laden der kombinierten Ergebnisse in den Index.

### <a name="create-an-index"></a>Erstellen eines Index

In diesem Beispielprogramm wird [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) verwendet, um einen Azure Cognitive Search-Index zu definieren und zu erstellen. Das SDK nutzt die Klasse [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder), um eine Indexstruktur auf der Grundlage einer C#-Datenmodellklasse zu generieren.

Das Datenmodell wird durch die Klasse „Hotel“ definiert, die auch Verweise auf die Klassen „Address“ und „Room“ enthält. „FieldBuilder“ führt ein Drilldown durch die verschiedenen Klassendefinitionen aus, um eine komplexe Datenstruktur für den Index zu generieren. Mithilfe von Metadatentags werden die Attribute der einzelnen Felder definiert, um beispielsweise anzugeben, ob das Feld durchsuchbar oder sortierbar ist.

Bereits vorhandene Indizes mit dem gleichen Namen werden vom Programm vor der Erstellung des neuen Index gelöscht (für den Fall, dass Sie dieses Beispiel mehrmals ausführen möchten).

Die folgenden Codeausschnitte aus der Datei **Hotel.cs** zeigen einzelne Felder gefolgt von einem Verweis auf eine andere Datenmodellklasse (Room[]), die wiederum in der Datei **Room.cs** definiert ist (nicht angezeigt).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

In der Datei **Program.cs** wird [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) mit einem Namen und einer Feldsammlung definiert, die durch die Methode `FieldBuilder.Build` generiert wird, und anschließend erstellt:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Erstellen der Azure Cosmos DB-Datenquelle und des zugehörigen Indexers

Das Hauptprogramm enthält auch Logik zum Erstellen der Azure Cosmos DB-Datenquelle für die Hoteldaten.

Dazu wird zunächst der Name der Azure Cosmos DB-Datenbank mit der Verbindungszeichenfolge verkettet. Anschließend wird ein [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)-Objekt definiert.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Nach Erstellung der Datenquelle richtet das Programm einen Azure Cosmos DB-Indexer namens **hotel-rooms-cosmos-indexer** ein.

Das Programm aktualisiert alle vorhandenen Indexer mit dem gleichen Namen. Dabei wird der vorhandene Indexer mit dem Inhalt des obigen Codes überschrieben. Er enthält auch Aktionen zum Zurücksetzen und Ausführen, falls Sie dieses Beispiel mehrmals ausführen möchten.

Im folgenden Beispiel wird ein Zeitplan für den Indexer definiert, sodass er einmal täglich ausgeführt wird. Sie können die Zeitplaneigenschaft aus diesem Aufruf entfernen, wenn der Indexer in Zukunft nicht mehr automatisch ausgeführt werden soll.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Dieses Beispiel enthält einen einfachen try-catch-Block, um Fehler zu melden, die ggf. bei der Ausführung auftreten.

Nach Ausführung des Azure Cosmos DB-Indexers enthält der Suchindex einen vollständigen Satz exemplarischer Hoteldokumente. Die Zimmerfelder für die einzelnen Hotels sind jedoch ein leeres Array, da die Azure Cosmos DB-Datenquelle Zimmerdetails auslässt. Als Nächstes führt das Programm einen Pullvorgang für Blob Storage aus, um die Zimmerdaten zu laden und zusammenzuführen.

### <a name="create-blob-storage-data-source-and-indexer"></a>Erstellen der Blob Storage-Datenquelle und des zugehörigen Indexers

Zum Abrufen der Zimmerdetails richtet das Programm zunächst eine Blob Storage-Datenquelle ein, um auf eine Gruppe individueller JSON-Blobdateien zu verweisen.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Nach Erstellung der Datenquelle richtet das Programm einen Blobindexer namens **hotel-rooms-blob-indexer** wie weiter unten gezeigt ein.

Die JSON-Blobs enthalten anstelle von **`HotelId`** ein Schlüsselfeld namens **`Id`** . Der Code weist den Indexer mithilfe der `FieldMapping`-Klasse an, den Wert des Felds **`Id`** an den Dokumentschlüssel **`HotelId`** im Index weiterzuleiten.

Blob Storage-Indexer können mithilfe von [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) einen Analysemodus angeben. Sie sollten verschiedene Analysemodi abhängig davon festlegen, ob Blobs ein einzelnes Dokument oder mehrere Dokumente innerhalb des gleichen Blobs darstellen. In diesem Beispiel stellt jedes Blob ein einzelnes JSON-Dokument dar, weshalb im Code der Analysemodus `json` verwendet wird. Weitere Informationen zu Indexer-Analyseparametern für JSON-Blobs finden Sie unter [Indizieren von JSON-Blobs mit dem Azure Search-Blobindexer](search-howto-index-json-blobs.md).

In diesem Beispiel wird ein Zeitplan für den Indexer definiert, sodass er einmal täglich ausgeführt wird. Sie können die Zeitplaneigenschaft aus diesem Aufruf entfernen, wenn der Indexer in Zukunft nicht mehr automatisch ausgeführt werden soll.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Da der Index bereits mit Hoteldaten aus der Azure Cosmos DB-Datenbank aufgefüllt wurde, aktualisiert der Blobindexer die im Index vorhandenen Dokumente und fügt die Zimmerdetails hinzu.

> [!NOTE]
> Wenn beide Datenquellen die gleichen schlüsselfremden Felder enthalten und die Daten in diesen Feldern nicht übereinstimmen, enthält der Index die Werte des zuletzt ausgeführten Indexers. In unserem Beispiel enthalten beide Datenquellen ein Feld namens **HotelName**. Sollten sich die Daten in diesem Feld bei Dokumenten mit dem gleichen Schlüsselwert aus irgendeinem Grund unterscheiden, werden im Index die Daten vom Typ **HotelName** aus der zuletzt indizierten Datenquelle gespeichert.

## <a name="5---search"></a>5\. Suchen

Nach Ausführung des Programms können Sie den aufgefüllten Suchindex über den [**Suchexplorer**](search-explorer.md) des Portals erkunden.

Öffnen Sie im Azure-Portal die Seite **Übersicht** für den Suchdienst, und suchen Sie in der Liste **Indexes** nach dem Index **hotel-rooms-sample**.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Liste mit den Azure Cognitive Search-Indizes" border="false":::

Klicken Sie in der Liste auf den Index „hotel-rooms-sample“. Daraufhin wird eine Suchexploreroberfläche für den Index angezeigt. Geben Sie eine Abfrage für einen Begriff wie „Luxury“ ein. Daraufhin sollte in den Ergebnissen mindestens ein Dokument angezeigt werden, und dieses Dokument sollte eine Liste mit Zimmerobjekten aus dem zugehörigen Zimmerarray enthalten.

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekte aus Azure Cognitive Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

Im Beispielcode wird eine Überprüfung auf vorhandene Objekte durchgeführt. Diese werden dann gelöscht oder aktualisiert, damit Sie das Programm erneut ausführen können.

Sie können auch das Portal verwenden, um Indizes, Indexer und Datenquellen zu löschen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link „Alle Ressourcen“ oder „Ressourcengruppen“ im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit der Erfassung von Daten aus mehreren Quellen vertraut gemacht haben, können Sie sich die Indexerkonfiguration näher ansehen, indem Sie mit Cosmos DB beginnen.

> [!div class="nextstepaction"]
> [Indizieren von Cosmos DB-Daten mithilfe eines Indexers in der kognitiven Azure-Suche](search-howto-index-cosmosdb.md)
