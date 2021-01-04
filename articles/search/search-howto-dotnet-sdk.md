---
title: Verwenden von Azure.Search.Documents (Version 11) in .NET
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Suchobjekte in einer .NET-Anwendung mithilfe von C# und der Clientbibliothek Azure.Search.Documents (Version 11) erstellen und verwalten. Codeausschnitte demonstrieren, wie eine Verbindung mit dem Dienst hergestellt, Indizes erstellt und Abfragen ausgeführt werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ceead297ea726e256d806c08c22810b39296793
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917170"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung

In diesem Artikel wird erläutert, wie Sie Suchobjekte mit C# und der Clientbibliothek [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (Version 11) erstellen und verwalten.

## <a name="about-version-11"></a>Informationen zu Version 11

Das Azure SDK für .NET fügt eine neue [**Azure.Search.Documents**](/dotnet/api/overview/azure/search)-Clientbibliothek des Azure SDK-Teams hinzu, die funktionell identisch mit den [Microsoft.Azure.Search](/dotnet/api/overview/azure/search/client10)-Clientbibliotheken ist, jedoch wo möglich gängigere Ansätze und Konventionen verwendet. Beispiele sind die [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential)-Schlüsselauthentifizierung und [System.Text.Json.Serialization](/dotnet/api/system.text.json.serialization) für die JSON-Serialisierung.

Wie die Vorgängerversionen können Sie diese Bibliothek zu Folgendem verwenden:

+ Erstellen und Verwalten von Suchindizes, Datenquellen, Indexern, Skillsets und Synonymzuordnungen
+ Laden und Verwalten von Suchdokumenten in einem Index
+ Ausführen von Abfragen, ohne sich um die Details von HTTP und JSON kümmern zu müssen

Die Bibliothek wird als einzelnes [Azure.Search.Document-NuGet-Paket](https://www.nuget.org/packages/Azure.Search.Documents/) verteilt, das alle APIs umfasst, die für den programmgesteuerten Zugriff auf den Suchdienst erforderlich sind.

Die Clientbibliothek definiert Klassen wie `SearchIndex`, `SearchField` und `SearchDocument` sowie Operationen wie `SearchIndexClient.CreateIndex` und `SearchClient.Search` für die Klassen `SearchIndexClient` und `SearchClient`. Diese Klassen sind in die folgenden Namespaces aufgeteilt:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (Version 11) verwendet Version [`2020-06-30` der REST-API für Azure Cognitive Search](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30) als Ziel. 

Die Clientbibliothek bietet keine [Dienstverwaltungsvorgänge](/rest/api/searchmanagement/) wie das Erstellen und Skalieren von Suchdiensten und das Verwalten von API-Schlüsseln. Wenn Sie Ihre Ressourcen für die Suche in einer .NET-Anwendung verwalten müssen, können Sie die [Microsoft.Azure.Management.Search](/dotnet/api/overview/azure/search/management)-Bibliothek im Azure SDK für .NET verwenden.

## <a name="upgrade-to-v11"></a>Upgrade auf Version 11

Wenn Sie die vorherige Version des .NET SDK verwendet haben und ein Upgrade auf die aktuell allgemein verfügbare Version durchführen möchten, finden Sie unter [Upgrade auf Version 11 des Azure Cognitive Search .NET SDK](search-dotnet-sdk-migration-version-11.md) weitere Informationen.

## <a name="sdk-requirements"></a>SDK-Anforderungen

+ Visual Studio 2019 oder höher

+ Ihr eigener Dienst der kognitiven Azure-Suche. Zur Verwendung des SDK benötigen Sie den Namen Ihres Dienstes und mindestens einen API-Schlüssel. [Erstellen Sie einen Dienst im Portal](search-create-service-portal.md), wenn Sie über keinen verfügen.

+ Laden Sie das [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents) über **Extras** > **NuGet-Paket-Manager** > **NuGet-Pakete für Projektmappe verwalten…** in Visual Studio herunter. Suchen Sie nach dem Paketname `Azure.Search.Documents`.

Das Azure SDK für .NET ist mit [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support) konform, das heißt, .NET Framework 4.6.1 und .NET Core 2.0 sind die Mindestanforderungen.

## <a name="example-application"></a>Beispielanwendung

In diesem Artikel wird ein Beispiel verwendet, das auf dem [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)-Codebeispiel von GitHub basiert, um grundlegende Konzepte in Azure Cognitive Search zu veranschaulichen, z. B. wie ein Suchindex erstellt, geladen und abgefragt wird.

Nehmen Sie für den restlichen Artikel einen neuen Index namens „hotels“ an, der mit einigen Dokumenten aufgefüllt ist, sowie mehrere Abfragen, die mit den Ergebnissen übereinstimmen.

Unten sehen Sie das Hauptprogramm mit dem allgemeinen Flow:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Als Nächstes sehen Sie einen Screenshotausschnitt der Ausgabe unter der Annahme, dass Sie diese Anwendung mit einem gültigen Dienstnamen und API-Schlüsseln ausführen:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Console.WriteLine-Ausgabe des Beispielprogramms":::

### <a name="client-types"></a>Clienttypen

Die Clientbibliothek verwendet drei Clienttypen für verschiedene Vorgänge: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) zum Erstellen, Aktualisieren oder Löschen von Indizes, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) zum Laden oder Abfragen eines Index und [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) zum Arbeiten mit Indexern und Skillsets. In diesem Artikel werden hauptsächlich die ersten beiden behandelt. 

Für alle Clients ist mindestens der Dienstname oder der Endpunkt und ein API-Schlüssel erforderlich. In der Regel werden diese Informationen in einer Konfigurationsdatei angegeben, ähnlich zu den Angaben in der `appsettings.json`-Datei der [DotNetHowTo-Beispielanwendung](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Fügen Sie für Lesevorgänge in der Konfigurationsdatei Ihrem Programm `using Microsoft.Extensions.Configuration;` hinzu.

Die folgende Anweisung erstellt den Indexclient, der verwendet wird, um Indizes zu erstellen, zu aktualisieren oder zu löschen. Ein Suchendpunkt und ein API-Schlüssel für Administratoren wird benötigt.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

Die nächste Anweisung erstellt den Suchclient, der verwendet wird, um Dokumente zu laden oder Abfragen auszuführen. Für `SearchClient` ist ein Index erforderlich. Sie benötigen einen API-Schlüssel für Administratoren, um Dokumente zu laden. Sie können jedoch einen API-Schlüssel für Abfragen nutzen, um Abfragen auszuführen. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Wenn Sie einen ungültigen Schlüssel für den Importvorgang angeben (z. B. einen Abfrageschlüssel, statt eines Administratorschlüssels), gibt `SearchClient` beim ersten Aufruf einer Vorgangsmethode eine `CloudException`-Ausnahme mit der Fehlermeldung „Unzulässig“ zurück. Überprüfen Sie in diesem Fall den API-Schlüssel.
>

### <a name="deleting-the-index"></a>Löschen des Index

In den frühen Entwicklungsphasen sollten Sie eine [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex)-Anweisung einschließen, um einen laufenden Index zu löschen, damit Sie ihn mit aktualisierter Definition neu erstellen können. Der Beispielcode für Azure Cognitive Search umfasst oftmals einen Löschschritt, damit Sie das Beispiel noch mal ausführen können.

Die folgende Zeile ruft `DeleteIndexIfExists` auf:

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Diese Methode verwendet die bereitgestellte Klasse `SearchIndexClient`, um zu überprüfen, ob der Index vorhanden ist, und löscht ihn gegebenenfalls:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> Im Beispielcode dieses Artikels werden der Einfachheit halber die synchronen Methoden verwenden. In Ihren Anwendungen sollten Sie jedoch die asynchronen Methoden verwenden, damit sie skaliert werden können und reaktionsfähig bleiben. In der oben gezeigten Methode könnten Sie [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) zum Beispiel auch anstelle von [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) verwenden.
>

## <a name="create-an-index"></a>Erstellen eines Index

Sie können [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) verwenden, um einen Index zu erstellen. 

Die Methode unten erstellt ein neues [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex)-Objekt mit einer Liste von [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield)-Objekten, die das Schema des neuen Index definieren. Jedes Feld weist einen Namen, einen Datentyp und mehrere Attribute auf, die das Suchverhalten des Felds definieren. 

Felder können in einer Modellklasse mithilfe von [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) definiert werden. Die Klasse `FieldBuilder` verwendet Reflexion, um durch Untersuchen der öffentlichen Eigenschaften und Attribute der entsprechenden `Hotel`-Modellklasse eine Liste von `SearchField`-Objekten für den Index zu erstellen. Wir werden uns die Klasse `Hotel` später genauer ansehen.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Neben Feldern können Sie dem Index auch Bewertungsprofile, Vorschlagsfunktionen oder CORS-Optionen hinzufügen (diese Parameter fehlen im Beispiel, um es einfach zu halten). Weitere Informationen zum SearchIndex-Objekt und seinen Bestandteilen finden Sie in der [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex)-Eigenschaftenliste sowie im Referenzartikel zur [REST-API](/rest/api/searchservice/).

> [!NOTE]
> Sie können die Liste der `Field`-Objekte immer direkt erstellen, anstatt `FieldBuilder` bei Bedarf zu verwenden. Sie möchten z.B. vielleicht keine Modellklasse verwenden oder Sie müssen möglicherweise eine vorhandene Modellklasse verwenden, die Sie nicht durch Hinzufügen von Attributen ändern möchten.
>

### <a name="call-createindex-in-main"></a>Aufrufen von CreateIndex in Main()

Als Nächstes ruft `Main` die oben stehende Methode auf, um einen neuen Index mit dem Namen „hotels“ zu erstellen:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Verwenden einer Modellklasse für die Datendarstellung

Im DotNetHowTo-Beispiel werden Modellklassen für die Datenstrukturen [Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [Address](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs) und [Room](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) verwendet. `Hotel` verweist auf `Address`, ein komplexer Typ mit einzelner Ebene (ein mehrteiliges Feld) und `Room` (eine Sammlung mit mehrteiligen Feldern).

Sie können diese Typen verwenden, um den Index zu erstellen und zu laden und um die Antwort einer Abfrage zu strukturieren:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Eine alternative Methode besteht darin, einem Index die Felder direkt hinzuzufügen. Im folgenden Beispiel werden nur einige Felder gezeigt.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Felddefinitionen

Ihr Datenmodell in .NET und das zugehörige Indexschema sollten so konzipiert sein, dass sie die Suchfunktion unterstützen, die Sie Ihrem Endbenutzer bieten möchten. Jedes Objekt der obersten Ebene in .NET, z. B. ein Suchdokument in einem Suchindex, entspricht einem Suchergebnis, das Sie auf Ihrer Benutzeroberfläche präsentieren würden. In einer Hotelsuchanwendung können Ihre Endbenutzer beispielsweise nach Hotelnamen, der Ausstattung des Hotels oder den Merkmalen eines bestimmten Zimmers suchen. 

In jeder Klasse wird ein Feld mit einem Datentyp und Attributen definiert, die bestimmen, wie es verwendet wird. Der Name jeder öffentlichen Eigenschaft in jeder Klasse wird einem Feld mit dem gleichen Namen in der Indexdefinition zugeordnet. 

Sehen Sie sich den folgenden Codeausschnitt an, der mehrere Felddefinitionen von der „Hotel“-Klasse pullt. Beachten Sie, dass es sich bei „Address“ und „Rooms“ um C#-Typen mit eigenen Klassendefinitionen handelt. Sehen Sie sich dazu den Beispielcode an, wenn Sie sie anzeigen möchten. Bei beiden handelt es sich um komplexe Typen. Weitere Informationen finden Sie unter [Modellieren komplexer Datentypen in Azure Search](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Auswählen einer Feldklasse

Beim Definieren von Feldern können Sie die [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield)-Basisklasse verwenden, oder Sie nutzen abgeleitete Hilfsprogrammmodelle, die als „Vorlagen“ fungieren, mit vorab konfigurierten Eigenschaften.

Genau ein Feld in Ihrem Index muss als Dokumentschlüssel dienen (`IsKey = true`). Dabei muss es sich um eine Zeichenfolge handeln, und diese muss jedes Dokument eindeutig identifizieren können. Außerdem ist `IsHidden = true` erforderlich, das heißt, die Sichtbarkeit in den Suchergebnissen darf hier nicht gegeben sein.

| Feldtyp | Beschreibung und Verwendung |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Hierbei handelt es sich um eine Basisklasse, für die die meisten Eigenschaften auf NULL festgelegt sind, mit Ausnahme von einem erforderlichen Wert für `Name` und von `AnalyzerName`, wofür der Standardwert „Lucene“ gilt. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Hierbei handelt es sich um ein Hilfsprogrammmodell. Hierbei kann es sich um einen beliebigen Datentyp handeln, der immer nicht durchsuchbar (wird bei Volltextsuchabfragen ignoriert) und immer abrufbar (nicht ausgeblendet) ist. Andere Attribute sind standardmäßig deaktiviert, können jedoch aktiviert werden. Sie können ein `SimpleField` für Dokument-IDs oder Felder verwenden, die nur in Filtern, Facets oder Bewertungsprofilen verwendet werden. Sofern dies der Fall ist, stellen Sie sicher, dass Sie alle Attribute anwenden, die für das Szenario erforderlich sind, z. B. `IsKey = true` für eine Dokument-ID. Weitere Informationen finden Sie unter [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) im Quellcode. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Hierbei handelt es sich um ein Hilfsprogrammmodell. Hierbei muss es sich um eine Zeichenfolge handeln, die immer durchsuchbar und abrufbar ist. Andere Attribute sind standardmäßig deaktiviert, können jedoch aktiviert werden. Da dieser Feldtyp durchsuchbar ist, unterstützt er Synonyme und die gesamte Palette der Eigenschaften des Analysetools. Weitere Informationen finden Sie unter [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) im Quellcode. |

Ob Sie die grundlegende `SearchField`-API oder eines der Hilfsmodelle verwenden, müssen Sie Filter-, Facet- und Sortierattribute explizit aktivieren. Beispielsweise müssen [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) und [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) explizit mit Attributen versehen werden, wie im obigen Beispiel gezeigt.

#### <a name="adding-field-attributes"></a>Hinzufügen von Feldattributen

Beachten Sie, wie jedes Feld mit Attributen wie `IsFilterable`, `IsSortable`, `IsKey` und `AnalyzerName` ergänzt wird. Diese Attribute sind den [entsprechenden Feldattributen in einem Index der kognitiven Azure-Suche](/rest/api/searchservice/create-index) direkt zugeordnet. Die `FieldBuilder`-Klasse verwendet diese Eigenschaften, um Felddefinitionen für den Index zu erstellen.

#### <a name="field-type-mapping"></a>Feldtypzuordnung

Die .NET-Typen dieser Eigenschaften stimmen mit den entsprechenden Feldtypen in der Indexdefinition überein. Die Zeichenfolgeeigenschaft `Category` passt zum Beispiel zum Feld `category`, das den Typ `Edm.String` hat. Ähnliche Zuordnungen bestehen auch zwischen `bool?`, `Edm.Boolean`, `DateTimeOffset?` und `Edm.DateTimeOffset` usw. 

Haben Sie die `SmokingAllowed`-Eigenschaft bemerkt?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Das `JsonIgnore`-Attribut in dieser Eigenschaft weist `FieldBuilder` an, sie nicht als Feld an den Index zu serialisieren.  Dies ist eine gute Möglichkeit, clientseitig berechnete Eigenschaften zu erstellen, die Sie als Hilfsprogramme in Ihrer Anwendung nutzen können.  In diesem Fall spiegelt die `SmokingAllowed`-Eigenschaft wider, ob in einem `Room` in der `Rooms`-Sammlung Rauchen erlaubt. Wenn alle „false“ sind, bedeutet dies, dass im gesamten Hotel Rauchverbot gilt.

## <a name="load-an-index"></a>Laden eines Index

Im nächsten Schritt in `Main` wird der neu erstellte Index „hotels“ gefüllt. Zum Auffüllen des Index wird die folgende Methode verwendet: (Einige Codeteile wurden zur Veranschaulichung durch „....“ ersetzt. Beachten Sie die vollständige Beispiellösung für den vollständigen Datenauffüllungscode.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Diese Methode besteht aus vier Teilen. Im ersten Teil wird ein Array aus jeweils 3 `Hotel`-Objekten mit 3 `Room`-Objekten erstellt, das die Eingabedaten für den Upload in den Index bereitstellt. Diese Daten sind der Einfachheit halber fest codiert. In einer tatsächlichen Anwendung stammen Daten wahrscheinlich aus einer externen Datenquelle, z. B. einer SQL-­Datenbank.

Im zweiten Teil wird [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) mit den Dokumenten erstellt. Sie geben den Vorgang an, den Sie auf den Batch zum Zeitpunkt seiner Erstellung anwenden möchten, in diesem Fall durch Aufruf von [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload). Der Batch wird dann mit der Methode [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) in den Azure Cognitive Search-Index hochgeladen.

> [!NOTE]
> In diesem Beispiel werden nur Dokumente hochgeladen. Wenn Sie z.B. Änderungen in vorhandenen Dokumenten zusammenführen oder Dokumente löschen möchten, können Sie zum Erstellen von Batches `IndexDocumentsAction.Merge`, `IndexDocumentsAction.MergeOrUpload` oder `IndexDocumentsAction.Delete` aufrufen. Sie können auch verschiedene Vorgänge in einem einzigen Batch kombinieren, indem Sie `IndexBatch.New` aufrufen. Dieses akzeptiert eine Sammlung von `IndexDocumentsAction`-Objekten. Jedes dieser Objekte weist die kognitive Azure-Suche an, einen bestimmten Vorgang für das Dokument auszuführen. Sie können jede `IndexDocumentsAction` mit ihrem eigenen Vorgang erstellen, indem Sie die entsprechende Methode aufrufen, z.B. `IndexDocumentsAction.Merge`, `IndexAction.Upload` usw.
> 

Der dritte Teil dieser Methode ist ein Catch-Block, der einen wichtigen Fehlerfall bei der Indizierung abfängt. Falls der Suchdienst Dokumente im Batch nicht indizieren kann, löst `IndexDocuments` eine `IndexBatchException`-Ausnahme aus. Diese Ausnahme kann bei der Indizierung von Dokumenten auftreten, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, diesen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

Der letzte Teil der Methode `UploadDocuments` fügt eine Verzögerung von zwei Sekunden hinzu. Da die Indizierung in Ihrem Suchdienst asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchen zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

### <a name="call-uploaddocuments-in-main"></a>Aufrufen von UploadDocuments in Main()

Der folgende Codeausschnitt richtet eine Instanz von [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) mithilfe der [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient)-Methode von indexClient ein. indexClient verwendet einen API-Schlüssel für Administratoren für die dazugehörigen Anforderungen. Dieser ist erforderlich, um Dokumente laden oder aktualisieren zu können.

Ein alternativer Ansatz besteht darin, `SearchClient` direkt aufzurufen und dabei einen API-Schlüssel für Administratoren für `AzureKeyCredential` zu übergeben.

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Ausführen von Abfragen

Richten Sie zunächst eine `SearchClient`-Klasse ein, die den Suchendpunkt liest, und fragen Sie einen API-Schlüssel aus **appsettings.json** ab:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Definieren Sie dann eine Methode, die eine Abfrageanforderung sendet. 

Jedes Mal, wenn eine Abfrage von der Methode ausgeführt wird, erstellt diese Methode ein neues [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions)-Objekt. Mit diesem Objekt werden zusätzliche Abfrageoptionen wie Sortierung, Filter, Paging und Facettierung festgelegt. Bei dieser Methode werden die Eigenschaften `Filter`, `Select` und `OrderBy` für verschiedene Abfragen festgelegt. Weitere Informationen zur Syntax von Suchabfrageausdrücken finden Sie unter [Einfache Abfragesyntax in Azure Cognitive Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

Im nächsten Schritt wird die Suchabfrage bereits ausgeführt. Zum Ausführen der Suche wird die `SearchClient.Search`-Methode verwendet. Übergeben Sie für jede Abfrage den zu verwendenden Suchtext als Zeichenfolge (bzw. `"*"`, wenn kein Suchtext vorliegt) sowie die zuvor erstellten Suchoptionen. Außerdem geben wir `Hotel` als Typparameter für `SearchClient.Search` an, wodurch das SDK angewiesen wird, die Dokumente im Suchergebnis in Objekte des Typs `Hotel` zu deserialisieren.

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Definieren Sie als dritten Schritt eine Methode, die die Antwort schreibt und dabei jedes Dokument in die Konsole schreibt:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Aufrufen von RunQueries in Main()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Informationen zu Abfragekonstrukten

Lassen Sie uns die einzelnen Abfragen einmal näher betrachten. Hier ist der Code zum Ausführen der ersten Abfrage:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

In diesem Fall durchsuchen wir den gesamten Index nach dem Wort „motel“ in einem beliebigen Suchfeld und möchten nur die Hotelnamen abrufen, wie durch die Option `Select` angegeben. Dies sind die Ergebnisse:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Verwenden Sie in der zweiten Abfrage einen Filter, um Ergebnisse für „Room“ mit einem Preis pro Nacht unter 100 US-Dollar auszuwählen. Geben Sie in den Ergebnissen nur Hotel-ID und -Beschreibung zurück:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

Diese Abfrage verwendet einen `$filter`-OData-Ausdruck, `Rooms/any(r: r/BaseRate lt 100)`, um die Dokumente im Index zu filtern. Dadurch wird der [Any-Operator](./search-query-odata-collection-operators.md) verwendet, um „BaseRate lt 100“ auf jedes Element in der Sammlung „Rooms“ anzuwenden. Weitere Informationen finden Sie unter [OData-Filtersyntax](./query-odata-filter-orderby-syntax.md).

Suchen Sie in der dritten Abfrage nach den zwei besten, in jüngster Zeit renovierten Hotels, und zeigen Sie deren Hotelnamen und das Datum der letzten Renovierung an. Hier folgt der Code: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Suchen Sie in der letzten Abfrage nach allen Hotelnamen, die das Wort „hotel“ enthalten:

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

In diesem Abschnitt schließt sich der Kreis zur Einführung in das .NET SDK, hören Sie hier aber noch nicht auf. Im nächsten Abschnitt finden Sie weitere Ressourcen, in denen Sie mehr zum Programmieren mit Azure Cognitive Search erfahren.

## <a name="next-steps"></a>Nächste Schritte

+ Sehen Sie sich die API-Referenzdokumentation für [Azure.Search.Documents](/dotnet/api/azure.search.documents) und die [REST-API](/rest/api/searchservice/) an.

+ Sehen Sie sich unter [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) und [search-getting-started-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started) andere auf Azure.Search.Documents basierende Codebeispiele an.

+ Lesen Sie den Abschnitt [Namenskonventionen](/rest/api/searchservice/Naming-rules), um sich mit den Namensregeln für verschiedene Objekte vertraut zu machen.

+ Sehen Sie sich den Artikel zu [Unterstützten Datentypen](/rest/api/searchservice/Supported-data-types) an.