---
title: 'Schnellstart: Erstellen eines Suchindex in .NET'
titleSuffix: Azure Cognitive Search
description: In diesem C#-Schnellstart erfahren Sie, wie Sie mit der Clientbibliothek „Azure.Search.Documents“ einen Index erstellen, Daten laden und Abfragen ausführen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 448f2b3e42e98d78652a005f5d1c11f55acdebb3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021183"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Schnellstart: Erstellen eines Suchindexes mithilfe Clientbibliothek „Azure.Search.Documents“

Verwenden Sie die neue [Clientbibliothek „Azure.Search.Documents“ (Version 11)](/dotnet/api/overview/azure/search.documents-readme), um eine .NET Core-Konsolenanwendung in C# zu erstellen, die einen Suchindex erstellt, lädt und abfragt.

Sie können den [Quellcode herunterladen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11), um mit einem fertigen Projekt zu beginnen, oder die Schritte in diesem Artikel ausführen, um ein eigenes zu erstellen.

> [!NOTE]
> Suchen Sie nach einer früheren Version? Dann finden Sie Informationen unter [Erstellen eines Suchindexes mithilfe von Microsoft.Azure.Search V10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, benötigen Sie die folgenden Tools und Dienste:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder [suchen Sie nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (beliebige Edition). Der Beispielcode wurde in der kostenlosen Community-Edition von Visual Studio 2019 getestet.

Beim Einrichten Ihres Projekts laden Sie das [NuGet-Paket „Azure.Search.Documents“](https://www.nuget.org/packages/Azure.Search.Documents/) herunter.

Das Azure SDK für .NET ist mit [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support) konform, das heißt, .NET Framework 4.6.1 und .NET Core 2.0 sind die Mindestanforderungen.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Stellen Sie Dienstverbindungsinformationen zusammen, und starten Sie dann Visual Studio, um ein neues Konsolen-App-Projekt zu erstellen, das unter .NET Core ausgeführt werden kann.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopieren eines Schlüssels und Endpunkts

Aufrufe, die an den Dienst gerichtet werden, erfordern jeweils einen URL-Endpunkt und einen Zugriffsschlüssel. Suchen Sie in einem ersten Schritt den API-Schlüssel und die URL, die Sie zu Ihrem Projekt hinzufügen möchten. Sie werden beide Werte bei der Erstellung des Clients in einem späteren Schritt angeben.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

2. Rufen Sie in **Einstellungen** > **Schlüssel** einen Administratorschlüssel für vollständige Rechte für den Dienst ab. Dieser ist erforderlich, wenn Sie Objekte erstellen oder löschen. Es gibt einen Primär- und einen Sekundärschlüssel, die austauschbar sind. Sie können beide verwenden.

   ![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

### <a name="install-the-nuget-package"></a>Installieren des NuGet-Pakets

Fügen Sie nach dem Erstellen des Projekts die Clientbibliothek hinzu. Das [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/) besteht aus einer Clientbibliothek, die alle APIs bereitstellt, die für die Arbeit mit einem Suchdienst in .NET verwendet werden.

1. Starten Sie Visual Studio, und erstellen Sie eine .NET Core-Konsolenanwendung.

1. Wählen Sie unter **Tools** > **NuGet-Paket-Manager** die Option **NuGet-Pakete für Projektmappe verwalten...** aus. 

1. Klicken Sie auf **Durchsuchen**.

1. Suchen Sie nach `Azure.Search.Documents`, und wählen Sie Version 11.0 oder höher aus.

1. Klicken Sie rechts auf **Installieren**, um die Assembly Ihrem Projekt und Ihrer Projektmappe hinzuzufügen.

### <a name="create-a-search-client"></a>Erstellen eines Suchclients

1. Ändern Sie in **Program.cs** den Namespace in `AzureSearch.SDK.Quickstart.v11`, und fügen Sie dann die folgenden `using`-Direktiven hinzu.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Erstellen Sie zwei Clients: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) erstellt den Index und [SearchClient](/dotnet/api/azure.search.documents.searchclient) lädt und fragt einen vorhandenen Index ab. Beide benötigen den Dienstendpunkt und einen Administrator-API-Schlüssel für die Authentifizierung mit Berechtigungen zum Erstellen/Löschen.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1\. Erstellen eines Index

In dieser Schnellstartanleitung wird ein Hotelindex erstellt, in den Sie Hoteldaten laden und Abfragen dafür ausführen. In diesem Schritt definieren Sie die Felder im Index. Jede Felddefinition enthält einen Namen, einen Datentyp und Attribute, die bestimmen, wie das Feld verwendet wird.

In diesem Beispiel werden aus Gründen der Einfachheit und Lesbarkeit synchrone Methoden der Bibliothek „Azure.Search.Documents“ verwendet. In Produktionsszenarien sollten Sie jedoch asynchrone Methoden verwenden, um Ihre App skalierbar und reaktionsfähig zu halten. So würden Sie beispielsweise [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) anstatt [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) verwenden.

1. Fügen Sie Ihrem Projekt eine leere Klassendefinition hinzu: **Hotel.cs**

1. Kopieren Sie den folgenden Code in **Hotel.cs**, um die Struktur eines Hoteldokuments zu definieren. Attribute für das Feld bestimmen die Verwendung in einer Anwendung. So muss beispielsweise jedem Feld, das einen Filterausdruck unterstützt, das Attribut `IsFilterable` zugewiesen werden.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   In der Azure.Search.Documents-Clientbibliothek können Sie [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) und [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) verwenden, um Felddefinitionen zu optimieren. Beide sind Ableitungen von [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) und können Ihren Code möglicherweise vereinfachen:

   + `SimpleField` kann ein beliebiger Datentyp sein, ist immer „nicht durchsuchbar“ (wird bei Volltextsuchabfragen ignoriert) und ist abrufbar (nicht ausgeblendet). Andere Attribute sind standardmäßig deaktiviert, können jedoch aktiviert werden. Sie können ein `SimpleField` für Dokument-IDs oder Felder verwenden, die nur in Filtern, Facets oder Bewertungsprofilen verwendet werden. Sofern dies der Fall ist, stellen Sie sicher, dass Sie alle Attribute anwenden, die für das Szenario erforderlich sind, z. B. `IsKey = true` für eine Dokument-ID. Weitere Informationen finden Sie unter [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) im Quellcode.

   + `SearchableField` muss eine Zeichenfolge sein und ist immer „durchsuchbar“ und „abrufbar“. Andere Attribute sind standardmäßig deaktiviert, können jedoch aktiviert werden. Da dieser Feldtyp durchsuchbar ist, unterstützt er Synonyme und die gesamte Palette der Eigenschaften des Analysetools. Weitere Informationen finden Sie unter [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) im Quellcode.

   Ob Sie die grundlegende `SearchField`-API oder eines der Hilfsmodelle verwenden, müssen Sie Filter-, Facet- und Sortierattribute explizit aktivieren. Beispielsweise müssen [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) und [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) explizit mit Attributen versehen werden, wie im obigen Beispiel gezeigt. 

1. Fügen Sie Ihrem Projekt eine zweite leere Klassendefinition hinzu: **Address.cs**.  Kopieren Sie den folgenden Code in die Klasse.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Erstellen Sie zwei weitere Klassen: **Hotel.Methods.cs** und **Address.Methods.cs** für ToString()-Außerkraftsetzungen. Diese Klassen werden verwendet, um Suchergebnisse in der Konsolenausgabe zu rendern.  Der Inhalt dieser Klassen ist in diesem Artikel nicht angegeben, aber Sie können den Code aus den [Dateien auf GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11) kopieren.

1. Erstellen Sie in **Program.cs** ein [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)-Objekt, und rufen Sie dann die [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)-Methode auf, um den Index in Ihrem Suchdienst auszudrücken. Der Index enthält auch ein [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)-Element, um die automatische Vervollständigung für die angegebenen Felder zu ermöglichen.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Laden von Dokumenten

Azure Cognitive Search durchsucht im Dienst gespeicherte Inhalte. In diesem Schritt laden Sie JSON-Dokumente, die dem soeben erstellten Hotelindex entsprechen.

Bei Azure Cognitive Search sind Suchdokumente Datenstrukturen, die sowohl Eingaben für die Indizierung als auch Ausgaben von Abfragen sind. Beispiele für Dokumenteingaben aus einer externen Datenquelle wären etwa Zeilen in einer Datenbank, Blobs in Blob Storage oder JSON-Dokumente auf dem Datenträger. In diesem Beispiel nehmen wir eine Abkürzung und betten JSON-Dokumente für vier Hotels direkt in den Code ein. 

Beim Hochladen von Dokumenten muss ein [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-Objekt verwendet werden. Ein `IndexDocumentsBatch`-Objekt enthält eine Sammlung von [Aktionen](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), und jede dieser Aktionen enthält wiederum ein Dokument und eine Eigenschaft, die Azure Cognitive Search mitteilt, welche Aktion ausgeführt werden soll ([„upload“, „merge“, „delete“ oder „mergeOrUpload“](search-what-is-data-import.md#indexing-actions)).

1. Erstellen Sie in **Program.cs** ein Array mit Dokumenten und Indexaktionen, und übergeben Sie es anschließend an `IndexDocumentsBatch`. Die folgenden Dokumente entsprechen dem Index „hotels-quickstart“ (gemäß Definition durch die Klasse „hotel“).

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Nachdem Sie das [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-Objekt initialisiert haben, können Sie es an den Index senden, indem Sie [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) für Ihr [SearchClient](/dotnet/api/azure.search.documents.searchclient)-Objekt aufrufen.

1. Fügen Sie „Main()“ die unten angegebenen Zeilen hinzu. Das Laden von Dokumenten erfolgt per „SearchClient“. Für den Vorgang werden aber auch Administratorrechte für den Dienst benötigt, der normalerweise „SearchIndexClient“ zugeordnet ist. Eine Möglichkeit zum Einrichten dieses Vorgangs ist das Abrufen von „SearchClient“ über „SearchIndexClient“ (in diesem Beispiel „adminClient“).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Da es sich hierbei um eine Konsolen-App handelt, die alle Befehle sequenziell ausführt, fügen Sie eine Wartezeit von 2 Sekunden zwischen der Indizierung und Abfragen hinzu.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Die Verzögerung von zwei Sekunden dient zur Kompensierung der Indizierung. Diese erfolgt asynchron, sodass alle Dokumente vor der Ausführung der Abfragen indiziert werden können. Die Programmierung einer solchen Verzögerung ist in der Regel nur in Demos, bei Tests und in Beispielanwendungen erforderlich.

## <a name="3---search-an-index"></a>3\. Durchsuchen eines Index

Abfrageergebnisse können abgerufen werden, sobald das erste Dokument indiziert wurde. Mit dem Testen des Index sollte aber gewartet werden, bis alle Dokumente indiziert wurden.

In diesem Abschnitt werden zwei Funktionen hinzugefügt: Abfragelogik und Ergebnisse. Verwenden Sie für Abfragen die [Search](/dotnet/api/azure.search.documents.searchclient.search)-Methode. Diese Methode akzeptiert sowohl Suchtext (die Abfragezeichenfolge) als auch andere [Optionen](/dotnet/api/azure.search.documents.searchoptions).

Die [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)-Klasse stellt die Ergebnisse dar.

1. Erstellen Sie in **Program.cs** eine **WriteDocuments**-Methode, mit der Suchergebnisse in der Konsole ausgegeben werden.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Erstellen Sie eine **RunQueries**-Methode, um Abfragen auszuführen und Ergebnisse zurückzugeben. Bei den Ergebnissen handelt es sich um Objekte vom Typ „Hotel“. In diesem Beispiel werden die Methodensignatur und die erste Abfrage veranschaulicht. Mit dieser Abfrage wird der SELECT-Parameter veranschaulicht, mit dem Sie das Ergebnis erstellen können, indem Sie ausgewählte Felder aus dem Dokument verwenden.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
        ```

1. In the second query, search on a term, add a filter that selects documents where Rating is greater than 4, and then sort by Rating in descending order. Filter is a boolean expression that is evaluated over [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) fields in an index. Filter queries either include or exclude values. As such, there is no relevance score associated with a filter query. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. Mit der dritten Abfrage wird „searchFields“ veranschaulicht, um einen Volltextsuche-Vorgang auf bestimmte Felder festzulegen.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. Mit der vierten Abfrage werden Facetten veranschaulicht, die zum Strukturieren einer Facettennavigationsstruktur verwendet werden können. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. Bei der fünften Abfrage wird ein bestimmtes Dokument zurückgegeben. Eine Dokumentsuche ist eine typische Antwort auf das OnClick-Ereignis in einem Resultset.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. Mit der letzten Abfrage wird die Syntax für die automatische Vervollständigung veranschaulicht. Hierbei wird die Teileingabe „sa“ eines Benutzers simuliert, für die sich zwei mögliche Übereinstimmungen in den „sourceFields“ ergeben, die der im Index definierten Vorschlagsfunktion zugeordnet sind.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Fügen Sie **RunQueries** für „Main()“ hinzu.

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Die obigen Abfragen verdeutlichen mehrere [Möglichkeiten zum Abgleichen von Begriffen in einer Abfrage](search-query-overview.md#types-of-queries): Volltextsuche, Filter und AutoVervollständigen.

Für die Volltextsuche und Filter wird die [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search)-Methode verwendet. Eine Suchabfrage kann in der `searchText`-Zeichenfolge und ein Filterausdruck kann in der [Filter](/dotnet/api/azure.search.documents.searchoptions.filter)-Eigenschaft der [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)-Klasse übergeben werden. Um ohne Suche zu filtern, übergeben Sie einfach `"*"` für den Parameter `searchText` der [Search](/dotnet/api/azure.search.documents.searchclient.search)-Methode. Wenn Sie ohne Filter suchen möchten, legen Sie die Eigenschaft `Filter` nicht fest, oder übergeben Sie einfach keine `SearchOptions`-Instanz.

## <a name="run-the-program"></a>Ausführen des Programms

Drücken Sie F5, um die App neu zu erstellen und das fertige Programm auszuführen. 

Die Ausgabe umfasst Nachrichten aus [Console.WriteLine](/dotnet/api/system.console.writeline), wobei Abfrageinformationen und -ergebnisse hinzugefügt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

In dieser C#-Schnellstartanleitung haben Sie eine Reihe von Aufgaben ausgeführt, um einen Index zu erstellen, Dokumente in den Index zu laden und Abfragen auszuführen. In einzelnen Phasen haben wir den Code zur besseren Lesbarkeit und zum besseren Verständnis ein wenig vereinfacht. Nachdem Sie sich mit den grundlegenden Konzepten vertraut gemacht haben, können Sie Ihr Wissen im nächsten Artikel mit alternativen Ansätzen und Konzepten vertiefen. 

> [!div class="nextstepaction"]
> [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)

Möchten Sie Ihre Cloudausgaben optimieren und dabei sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)