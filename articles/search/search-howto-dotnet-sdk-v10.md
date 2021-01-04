---
title: Verwenden von Microsoft.Azure.Search (v10) in .NET
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Suchobjekte in einer .NET-Anwendung mit C# und Microsoft.Azure.Search (Version 10) des .NET SDK erstellen und verwalten. Codeausschnitte demonstrieren, wie eine Verbindung mit dem Dienst hergestellt, Indizes erstellt und Abfragen ausgeführt werden.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 11102d95bc2aba65e6bc3cba71805a67f195947b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681314"
---
# <a name="how-to-use-microsoftazuresearch-in-a-c-net-application"></a>Verwenden von Microsoft.Azure.Search in einer in C# geschriebenen .NET-Anwendung

In diesem Artikel erfahren Sie, wie Sie Suchobjekte mit C# und der Legacyclientbibliothek [**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) (Version 10) im Azure SDK für .NET erstellen und verwalten. 

Version 10 ist die letzte Version des Microsoft.Azure.Search-Pakets. In Zukunft erfolgt das Rollout neuer Features in [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) durch das Azure SDK-Team.

> [!NOTE]
> Für bereits vorhandene oder laufende Entwicklungsprojekte kann weiterhin Version 10 verwendet werden. Für neue Projekte sollten Sie jedoch die [neue Bibliothek](/dotnet/api/overview/azure/search.documents-readme) verwenden. Das gilt auch, wenn Sie neue Features nutzen möchten.

## <a name="about-version-10"></a>Informationen zu Version 10

Das SDK besteht aus wenigen Clientbibliotheken, mit denen Sie sowohl Ihre Indizes, Datenquellen, Indexer und Synonymzuordnungen verwalten als auch Dokumente hochladen und verwalten sowie Abfragen ausführen können, ohne die Details von HTTP und JSON berücksichtigen zu müssen. Alle diese Clientbibliotheken werden als NuGet-Pakete verteilt.

Das wichtigste NuGet-Paket ist `Microsoft.Azure.Search`, ein Metapaket, das alle anderen Pakete als Abhängigkeiten enthält. Verwenden Sie dieses Paket, wenn Sie gerade beginnen oder wenn Sie wissen, dass in Ihrer Anwendung alle Funktionen der kognitiven Azure-Suche benötigt werden.

Die anderen NuGet-Pakete im SDK sind:
 
  - `Microsoft.Azure.Search.Data`: Verwenden Sie dieses Paket, wenn Sie eine .NET-Anwendung mithilfe der kognitiven Azure-Suche entwickeln und Sie nur Dokumente in den Indizes abfragen oder aktualisieren möchten. Wenn Sie auch Indizes, Synonymzuordnungen oder andere Servicelevelressourcen erstellen oder aktualisieren müssen, verwenden Sie stattdessen das `Microsoft.Azure.Search`-Paket.
  - `Microsoft.Azure.Search.Service`: Verwenden Sie dieses Paket, wenn Sie in .NET eine Automatisierung zum Verwalten von Indizes der kognitiven Azure-Suche, Synonymzuordnungen, Indexern, Datenquellen oder anderen Ressourcen auf Dienstebene entwickeln. Wenn Sie nur Dokumente in Ihren Indizes abfragen oder aktualisieren müssen, verwenden Sie stattdessen das `Microsoft.Azure.Search.Data`-Paket. Wenn Sie alle Funktionen der kognitiven Azure-Suche benötigen, verwenden Sie stattdessen das Paket `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Common`: Gängige Typen, die in den .NET-Bibliotheken der kognitiven Azure-Suche benötigt werden. Sie müssen dieses Paket nicht direkt in Ihrer Anwendung verwenden. Es soll nur als Abhängigkeit verwendet werden.

Die unterschiedlichen Clientbibliothek definieren Klassen wie `Index`, `Field` und `Document` sowie Operationen wie `Indexes.Create` und `Documents.Search` für die Klassen `SearchServiceClient` und `SearchIndexClient`. Diese Klassen sind in die folgenden Namespaces aufgeteilt:

* [Microsoft.Azure.Search](/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](/dotnet/api/microsoft.azure.search.models)

Wenn Sie Feedback für ein zukünftiges Update des SDK geben möchten, besuchen Sie unsere [Feedbackseite](https://feedback.azure.com/forums/263029-azure-search/), oder erstellen Sie ein Issue auf [GitHub](https://github.com/azure/azure-sdk-for-net/issues), und geben Sie „Azure Cognitive Search“ im Titel an.

Das .NET SDK verwendet die Version [`2019-05-06` der REST-API für Azure Cognitive Search](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Microsoft.Azure.Search.Data/stable/2019-05-06) als Ziel. Diese Version enthält Unterstützung für [komplexe Typen](search-howto-complex-data-types.md), [KI-Anreicherung](cognitive-search-concept-intro.md), [AutoVervollständigen](/rest/api/searchservice/autocomplete) und den [JsonLines-Analysemodus](search-howto-index-json-blobs.md) beim Indizieren von Azure-Blobs. 

Dieses SDK unterstützt keine [Verwaltungsvorgänge](/rest/api/searchmanagement/) wie etwa das Erstellen und Skalieren von Suchdiensten und das Verwalten von API-Schlüsseln. Wenn Sie Ihre Ressourcen für die Suche in einer .NET-Anwendung verwalten möchten, können Sie das [.NET Management SDK für die kognitive Azure-Suche](/dotnet/api/overview/azure/search/management) verwenden.

## <a name="upgrade-to-v10"></a>Upgraden auf Version 10
Wenn Sie bereits eine ältere Version des .NET SDK für die kognitive Azure-Suche nutzen und auf die aktuelle allgemein verfügbare Version aktualisieren möchten, finden Sie [in diesem Artikel](search-dotnet-sdk-migration-version-9.md) eine entsprechende Anleitung.

## <a name="sdk-requirements"></a>SDK-Anforderungen
1. Visual Studio 2017 oder höher.
2. Ihr eigener Dienst der kognitiven Azure-Suche. Zur Verwendung des SDK benötigen Sie den Namen Ihres Dienstes und mindestens einen API-Schlüssel. [Create a service in the portal](search-create-service-portal.md) (+++Erstellen eines Dienstes im Portal).
3. Laden Sie das [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Search) mit dem .NET SDK für die kognitive Azure-Suche in Visual Studio über „NuGet-Pakete verwalten“ herunter. Suchen Sie einfach auf NuGet.org nach dem Paketnamen `Microsoft.Azure.Search` (oder einem der anderen oben aufgeführten Paketnamen, wenn Sie nur eine Teilmenge der Funktionen benötigen).

Das .NET SDK für die kognitive Azure-Suche unterstützt Anwendungen für .NET Framework 4.5.2 und höher sowie .NET Core 2.0 und höher.

## <a name="core-scenarios"></a>Schlüsselszenarien
In Ihrer Suchanwendung müssen Sie verschiedene Aktionen ausführen. In diesem Zusammenhang werden in diesem Lernprogramm die folgenden Schlüsselszenarien behandelt:

* Erstellen eines Index
* Füllen des Index mit Dokumenten
* Suche nach Dokumenten mit Volltextsuche und Filtern

Im nachfolgenden Beispielcode werden diese Szenarien veranschaulicht. Die Codeausschnitte können Sie gerne in Ihrer eigenen Anwendung übernehmen.

### <a name="overview"></a>Übersicht
Die nachfolgend untersuchte Beispielanwendung erstellt einen neuen Index mit dem Namen „hotels“, füllt diesen mit Dokumenten und führt danach einige Suchabfragen aus. Hier das Hauptprogramm mit dem allgemeinen Ablauf:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Den vollständigen Quellcode der in diesem Artikel besprochenen Beispielanwendung finden Sie auf [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Dieses gehen wir nun Schritt für Schritt durch. Zunächst muss das Objekt `SearchServiceClient`erstellt werden. Mit diesem Objekt können Indizes verwaltet werden. Zur Erstellung dieses Objekts müssen Sie den Namen des Diensts der kognitiven Azure-Suche sowie einen Administrator-API-Schlüssel angeben. Sie können diese Informationen in der Datei `appsettings.json` der [Beispielanwendung](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) eingeben.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Wenn Sie einen falschen Schlüssel angeben (z. B. einen Abfrageschlüssel, statt eines Admin-Schlüssels), gibt `SearchServiceClient` beim ersten Aufruf einer Operationsmethode (z. B. eines `Indexes.Create`) eine `CloudException` mit der Fehlermeldung „Forbidden“ (Unzulässig) aus. Überprüfen Sie in diesem Fall unsere API-Schlüssel.
> 
> 

Durch die nächsten Zeilen werden Methoden zum Erstellen eines Index mit dem Namen „hotels“ erstellt, wobei dieser, sofern er bereits vorhanden ist, zuerst gelöscht wird. Auf diese Methoden gehen wir später ein.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Danach muss der Index gefüllt werden. Zum Auffüllen des Index benötigen wir ein `SearchIndexClient`-Element. Dieses Objekt erhalten Sie entweder, indem Sie es erstellen oder indem Sie für `SearchServiceClient``Indexes.GetClient` aufrufen. Der Einfachheit halber verwenden wir die zweite Methode.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> In einer typischen Suchanwendung können Verwaltung und Füllung des Index getrennt von Suchabfragen erfolgen. `Indexes.GetClient` eignet sich zum Auffüllen eines Indexes, da Sie keine zusätzlichen `SearchCredentials` bereitstellen müssen. Vielmehr wird der Admin-Schlüssel, den Sie zum Erstellen des Objekts `SearchServiceClient` verwendet haben, auf das neue `SearchIndexClient`-Objekt übertragen. In dem Teil der Anwendung, in dem Abfragen ausgeführt werden, sollte das Objekt `SearchIndexClient` hingegen direkt erstellt werden, damit anstelle des Administratorschlüssel ein Abfrageschlüssel übergeben werden kann, der nur das Lesen von Daten zulässt. Dies entspricht dem Prinzip der geringsten Rechte und trägt dazu bei, die Anwendung sicherer zu machen. Weitere Informationen zu Admin- und Abfrageschlüsseln erhalten Sie [hier](/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nachdem das Objekt `SearchIndexClient`erstellt ist, kann der Index gefüllt werden. Zum Auffüllen des Index wird eine andere Methode verwendet, die später beschrieben wird.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Zum Schluss führen wir einige Suchabfragen aus und zeigen die Ergebnisse an. Dieses Mal verwenden wir einen anderen `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Wir werden uns die Methode `RunQueries` später genauer ansehen. Hier ist der Code zum Erstellen des neuen `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Diesmal verwenden wir einen Abfrageschlüssel, da wir keinen Schreibzugriff auf den Index benötigen. Sie können diese Informationen in der Datei `appsettings.json` der [Beispielanwendung](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) eingeben.

Wenn Sie diese Anwendung mit einem gültigen Dienstnamen und API-Schlüsseln ausführen, sollte die Ausgabe wie in diesem Beispiel aussehen: (Einige Konsolenausgaben wurden zur Veranschaulichung durch „....“ ersetzt.)

```output

Deleting index...

Creating index...

Uploading documents...

Waiting for documents to be indexed...

Search the entire index for the term 'motel' and return only the HotelName field:

Name: Secret Point Motel

Name: Twin Dome Motel


Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

HotelId: 2
Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Triple Landscape Hotel
Last renovated on: 9/20/2015 12:00:00 AM +00:00

Name: Twin Dome Motel
Last renovated on: 2/18/1979 12:00:00 AM +00:00


Search the hotel names for the term 'hotel':

HotelId: 3
Name: Triple Landscape Hotel
...

Complete.  Press any key to end application... 
```

Der vollständige Quellcode der Anwendung wird am Ende dieses Artikels bereitgestellt.

Nun sehen wir uns die von `Main`aufgerufenen Methoden näher an.

### <a name="creating-an-index"></a>Erstellen eines Index
Nachdem das Objekt `SearchServiceClient` erstellt ist, löscht `Main` den Index „hotels“, sofern dieser bereits vorhanden ist. Für diesen Löschvorgang wird die folgende Methode verwendet:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Diese Methode verwendet das bereitgestellte Objekt `SearchServiceClient` , um zu überprüfen, ob der Index vorhanden ist, und löscht ihn gegebenenfalls.

> [!NOTE]
> Der Beispielcode in diesem Artikel enthält der Einfachheit halber die synchronen Methoden des .NET SDK für die kognitive Azure-Suche. Für Ihre eigenen Anwendungen empfehlen wir aus Gründen der Skalierbarkeit und Reaktionsfähigkeit die asynchronen Methoden. In der oben gezeigten Methode können Sie `Exists` und `Delete` zum Beispiel auch durch `ExistsAsync` und `DeleteAsync` ersetzen.
> 
> 

Als Nächstes ruft `Main` die folgende Methode auf, um einen neuen Index mit dem Namen „hotels“ zu erstellen:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Diese Methode erstellt ein neues `Index`-Objekt mit einer Liste von `Field`-Objekten, die das Schema des neuen Index definiert. Jedes Feld weist einen Namen, einen Datentyp und mehrere Attribute auf, die das Suchverhalten des Felds definieren. Die Klasse `FieldBuilder` verwendet Reflexion, um durch Untersuchen der öffentlichen Eigenschaften und Attribute der entsprechenden `Hotel`-Modellklasse eine Liste von `Field`-Objekten für den Index zu erstellen. Wir werden uns die Klasse `Hotel` später genauer ansehen.

> [!NOTE]
> Sie können die Liste der `Field`-Objekte immer direkt erstellen, anstatt `FieldBuilder` bei Bedarf zu verwenden. Sie möchten z.B. vielleicht keine Modellklasse verwenden oder Sie müssen möglicherweise eine vorhandene Modellklasse verwenden, die Sie nicht durch Hinzufügen von Attributen ändern möchten.
>
> 

Neben Feldern können Sie dem Index auch Wertungsprofile, Vorschläge oder CORS-Optionen hinzufügen (diese Parameter fehlen im Beispiel, um es einfach zu halten). Weitere Informationen zum Indexobjekt und seinen Bestandteilen finden Sie in der [SDK-Referenz](/dotnet/api/microsoft.azure.search.models.index) sowie in der [Referenz zur REST-API für die kognitive Azure-Suche](/rest/api/searchservice/).

### <a name="populating-the-index"></a>Füllen des Index
Im nächsten Schritt in `Main` wird der neu erstellte Index gefüllt. Zum Auffüllen des Index wird die folgende Methode verwendet: (Einige Codeteile wurden zur Veranschaulichung durch „....“ ersetzt.  Beachten Sie die vollständige Beispiellösung für den vollständigen Datenauffüllungscode.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
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
        },
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
        },
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

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Diese Methode besteht aus vier Teilen. Im ersten Teil wird ein Array aus jeweils 3 `Hotel`-Objekten mit 3 `Room`-Objekten erstellt, das die Eingabedaten für den Upload in den Index bereitstellt. Diese Daten sind der Einfachheit halber fest codiert. In Ihrer eigenen Anwendung stammen Ihre Daten wahrscheinlich aus einer externen Datenquelle, z.B. einer SQL-­Datenbank.

Im zweiten Teil wird ein `IndexBatch` mit den Dokumenten erstellt. Sie geben den Vorgang an, den Sie auf den Batch zum Zeitpunkt seiner Erstellung anwenden möchten, in diesem Fall durch Aufruf von `IndexBatch.Upload`. Der Batch wird dann mit der Methode `Documents.Index` in den Index der kognitiven Azure-Suche hochgeladen.

> [!NOTE]
> In diesem Beispiel werden nur Dokumente hochgeladen. Wenn Sie z.B. Änderungen in vorhandenen Dokumenten zusammenführen oder Dokumente löschen möchten, können Sie zum Erstellen von Batches `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` oder `IndexBatch.Delete` aufrufen. Sie können auch verschiedene Vorgänge in einem einzigen Batch kombinieren, indem Sie `IndexBatch.New` aufrufen. Dieses akzeptiert eine Sammlung von `IndexAction`-Objekten. Jedes dieser Objekte weist die kognitive Azure-Suche an, einen bestimmten Vorgang für das Dokument auszuführen. Sie können jede `IndexAction` mit ihrem eigenen Vorgang erstellen, indem Sie die entsprechende Methode aufrufen, z.B. `IndexAction.Merge`, `IndexAction.Upload` usw.
> 
> 

Der dritte Teil dieser Methode ist ein Catch-Block, der einen wichtigen Fehlerfall bei der Indizierung abfängt. Wenn der Dienst der kognitiven Azure-Suche Dokumente des Batchs nicht indizieren kann, löst `Documents.Index` eine `IndexBatchException` aus. Diese Ausnahme kann bei der Indizierung von Dokumenten auftreten, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, diesen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

> [!NOTE]
> Sie können die Methode [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) zum Erstellen eines neuen Batches verwenden, der nur die Aktionen enthält, die in einem vorherigen Aufruf von `Index` zu einem Fehler geführt haben. In [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) finden Sie eine Beschreibung der ordnungsgemäßen Verwendung dieser Methode.
>
>

Der letzte Teil der Methode `UploadDocuments` fügt eine Verzögerung von zwei Sekunden hinzu. Da die Indizierung in Ihrem Dienst der kognitiven Azure-Suche asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchvorgänge zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Behandeln von Dokumenten durch das .NET-SDK
Vielleicht fragen Sie sich, wie mit dem .NET SDK für die kognitive Azure-Suche Instanzen einer benutzerdefinierten Klasse wie `Hotel` in einen Index hochgeladen werden können. Um diese Frage zu beantworten, sehen wir uns die Klasse `Hotel` an:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Als erstes ist zu beachten, dass der Name jeder öffentlichen Eigenschaft in der `Hotel`-Klasse einem Feld mit dem gleichen Namen in der Indexdefinition zugeordnet wird. Wenn Sie möchten, dass jedes Feld mit einem Kleinbuchstaben („Camel-Case-Schreibweise“) beginnt, können Sie das SDK anweisen, die Eigenschaftsnamen mit dem `[SerializePropertyNamesAsCamelCase]`-Attribut in der Klasse automatisch der Camel-Case-Schreibweise zuzuordnen. Dies ist ein typisches Szenario für .NET-Anwendungen, die Datenbindungen durchführen, wenn das Zielschema außerhalb der Kontrolle des Anwendungsentwicklers liegt, ohne gegen die Benennungsrichtlinien der „Pascal-Schreibweise“ in .NET verstoßen zu müssen.

> [!NOTE]
> Das .NET SDK für die kognitive Azure-Suche verwendet die [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)-Bibliothek, um die Objekte des benutzerdefinierten Modells in JSON zu serialisieren und aus JSON zu deserialisieren. Sie können diese Serialisierung bei Bedarf anpassen. Weitere Informationen finden Sie unter [Benutzerdefinierte Serialisierung mit JSON.NET](#JsonDotNet).
> 
> 

Als zweites ist zu beachten, dass jede Eigenschaft mit Attributen wie `IsFilterable`, `IsSearchable`, `Key` und `Analyzer` versehen ist. Diese Attribute sind den [entsprechenden Feldattributen in einem Index der kognitiven Azure-Suche](/rest/api/searchservice/create-index) direkt zugeordnet. Die `FieldBuilder`-Klasse verwendet diese Eigenschaften, um Felddefinitionen für den Index zu erstellen.

Der dritte bemerkenswerte Punkt an der Klasse `Hotel` sind die Datentypen der öffentlichen Eigenschaften. Die .NET-Typen dieser Eigenschaften stimmen mit den entsprechenden Feldtypen in der Indexdefinition überein. Die Zeichenfolgeeigenschaft `Category` passt zum Beispiel zum Feld `category`, das den Typ `Edm.String` hat. Ähnliche Zuordnungen bestehen auch zwischen `bool?`, `Edm.Boolean`, `DateTimeOffset?` und `Edm.DateTimeOffset` usw. Die jeweiligen Regeln für die Zuordnung eines Typs sind in der [Referenz zum .NET SDK für die kognitive Azure-Suche](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get) unter der `Documents.Get`-Methode dokumentiert. Die Klasse `FieldBuilder` übernimmt diese Zuordnung für Sie, aber es kann dennoch hilfreich sein, den Vorgang zu verstehen, falls Sie Serialisierungsprobleme beheben müssen.

Haben Sie die `SmokingAllowed`-Eigenschaft bemerkt?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Das `JsonIgnore`-Attribut in dieser Eigenschaft weist `FieldBuilder` an, sie nicht als Feld an den Index zu serialisieren.  Dies ist eine gute Möglichkeit, clientseitig berechnete Eigenschaften zu erstellen, die Sie als Hilfsprogramme in Ihrer Anwendung nutzen können.  In diesem Fall spiegelt die `SmokingAllowed`-Eigenschaft wider, ob in einem `Room` in der `Rooms`-Sammlung Rauchen erlaubt.  Wenn alle „false“ sind, bedeutet dies, dass im gesamten Hotel Rauchverbot gilt.

Einige Eigenschaften wie `Address` und `Rooms` sind Instanzen von .NET-Klassen.  Diese Eigenschaften stellen komplexere Datenstrukturen dar und erfordern daher Felder mit einem [komplexen Datentyp](./search-howto-complex-data-types.md) im Index.

Die Eigenschaft `Address` stellt einen Satz von mehreren Werten in der `Address`-Klasse dar, die nachfolgend definiert sind:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Diese Klasse enthält die Standardwerte zur Beschreibung von Adressen in den USA oder Kanada. Mit solchen Typen können Sie logische Felder im Index gruppieren.

Die `Rooms`-Eigenschaft stellt ein Array von `Room`-Objekten dar:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Ihr Datenmodell in .NET und das zugehörige Indexschema sollten so konzipiert sein, dass sie die Sucherfahrung unterstützen, die Sie Ihrem Endbenutzer bieten möchten. Jedes Objekt der obersten Ebene in .NET, d. h. Dokument im Index, entspricht einem Suchergebnis, das Sie auf Ihrer Benutzeroberfläche präsentieren würden. In einer Hotelsuchanwendung können Ihre Endbenutzer beispielsweise nach Hotelnamen, der Ausstattung des Hotels oder den Merkmalen eines bestimmten Zimmers suchen. Weiter unten wird auf einige Abfragebeispiele eingegangen.

Diese Möglichkeit, eigene Klassen zur Interaktion mit Dokumenten im Index zu verwenden, funktioniert in beide Richtungen. Denn ebenso können Sie das SDK, wie im nächsten Abschnitt gezeigt, beim Abrufen von Suchergebnissen anweisen, diese automatisch in einen Typ Ihrer Wahl zu deserialisieren.

> [!NOTE]
> Das .NET SDK für die kognitive Azure-Suche unterstützt durch die Klasse `Document`, einer Schlüssel-Wert-Zuordnung von Feldnamen zu Feldwerten, auch dynamisch typisierte Dokumente. Diese Art der Typisierung eignet sich für Szenarien, in denen Sie bei der Entwicklung noch nicht wissen, wie das Indexschema aussehen soll, oder in denen eine Bindung an bestimmte Modellklassen unpassend ist. Alle Methoden des SDK, die Dokumente verarbeiten, verfügen über Überladungen, welche die Klasse `Document` verwenden, wie auch stark typisierte Überladungen, die einen generischen Typparameter verwenden. Nur letztere Art von Überladung wird im Beispielcode dieses Lernprogramms verwendet. Die [`Document`-Klasse](/dotnet/api/microsoft.azure.search.models.document) erbt von `Dictionary<string, object>`.
> 
>

**Argumente für die Verwendung von Nullable-Datentypen**

Beim Entwerfen eigener Modellklassen für die Zuordnung zu einem Index der kognitiven Azure-Suche wird empfohlen, Eigenschaften von Werttypen, z. B. `bool` und `int`, als Eigenschaften zu deklarieren, die NULL-Werte zulassen (z. B. `bool?` anstelle von `bool`). Wenn Sie eine Eigenschaft verwenden, die keine NULL-Werte zulässt, müssen Sie **garantieren** , dass keine Dokumente im Index einen NULL-Wert für das entsprechende Feld enthalten. Dies kann weder mit dem SDK noch mit dem Dienst der kognitiven Azure-Suche erzwungen werden.

Dies ist nicht nur von hypothetischer Bedeutung: Stellen Sie sich ein Szenario vor, bei dem Sie ein neues Feld einem vorhandenen Index vom Typ `Edm.Int32` hinzufügen. Nach dem Aktualisieren der Indexdefinition enthalten alle Dokumente einen NULL-Wert für das neue Feld (da in der kognitiven Azure-Suche alle Typen NULL-Werte zulassen). Wenn Sie für dieses Feld anschließend eine Modellklasse mit einer `int`-Eigenschaft verwenden, die keine NULL-Werte zulässt, erhalten Sie beim Abrufen von Dokumenten folgendes `JsonSerializationException`-Element:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Aus diesem Grund empfehlen wir als bewährte Methode, in Ihren Modellklassen Typen zu verwenden, die NULL-Werte zulassen.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Benutzerdefinierte Serialisierung mit JSON.NET
Das SDK verwendet JSON.NET zum Serialisieren und Deserialisieren von Dokumenten. Sie können die Serialisierung und Deserialisierung bei Bedarf anpassen, indem Sie eigene Instanzen von `JsonConverter` oder `IContractResolver` definieren. Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm). Dies kann nützlich sein, wenn Sie eine vorhandene Modellklasse aus der Anwendung für die Verwendung mit der kognitiven Azure-Suche und andere fortgeschrittenere Szenarien anpassen möchten. Bei einer benutzerdefinierten Serialisierung bieten sich zum Beispiel folgende Möglichkeiten:

* Ein- oder Ausschließen bestimmter Eigenschaften der Modellklasse bei der Speicherung als Dokumentfelder
* Zuordnen zwischen Eigenschaftennamen im Code und Feldnamen im Index
* Erstellen Sie benutzerdefinierte Attribute, die für die Zuordnung von Eigenschaften zu Dokumentfelder verwendet werden können.

Beispiele für die Implementierung der benutzerdefinierten Serialisierung in den Komponententests für das .NET SDK für die kognitive Azure-Suche finden Sie auf GitHub. Ein guter Ausgangspunkt ist [dieser Ordner](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Er enthält Klassen, die in den Tests für die benutzerdefinierte Serialisierung verwendet werden.

### <a name="searching-for-documents-in-the-index"></a>Suchen nach Dokumenten im Index
Im letzten Schritt der Beispielanwendung wird nach Dokumenten im Index gesucht:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Jedes Mal, wenn eine Abfrage ausgeführt wird, erstellt diese Methode zunächst ein neues `SearchParameters`-Objekt. Mit diesem Objekt werden zusätzliche Abfrageoptionen wie Sortierung, Filter, Paging und Facettierung festgelegt. Bei dieser Methode legen die Eigenschaften `Filter`, `Select`, `OrderBy` und `Top` für verschiedene Abfragen fest. Alle `SearchParameters`-Eigenschaften wird [hier](/dotnet/api/microsoft.azure.search.models.searchparameters) behandelt.

Im nächsten Schritt wird die Suchabfrage bereits ausgeführt. Zum Ausführen der Suche wird die `Documents.Search`-Methode verwendet. Für jede Abfrage übergeben wir den zu verwendenden Suchtext als Zeichenfolge (bzw. `"*"`, wenn kein Suchtext vorliegt) sowie die zuvor erstellten Suchparameter. Außerdem geben wir `Hotel` als Typparameter für `Documents.Search` an, wodurch das SDK angewiesen wird, die Dokumente im Suchergebnis in Objekte des Typs `Hotel` zu deserialisieren.

> [!NOTE]
> Weitere Informationen zur Syntax von Suchabfrageausdrücken finden Sie [hier](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Schließlich geht diese Methode nach jeder Abfrage durch alle Übereinstimmungen im Suchergebnis, wobei sie jedes Dokument auf der Konsole ausgibt:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Lassen Sie uns die einzelnen Abfragen einmal näher betrachten. Hier ist der Code zum Ausführen der ersten Abfrage:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

In diesem Fall durchsuchen wir den gesamten Index nach dem Wort „motel“ in einem beliebigen Suchfeld und möchten nur die Hotelnamen abrufen, wie durch Parameter `Select` angegeben. Dies sind die Ergebnisse:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Die nächste Abfrage ist ein wenig interessanter.  Wir möchten wir Hotels mit einem Übernachtungspreis von weniger als 100 € suchen, und nur die Hotel-ID und Beschreibung sollen zurückgegeben werden:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Diese Abfrage verwendet einen OData `$filter`-Ausdruck, `Rooms/any(r: r/BaseRate lt 100)`, um die Dokumente im Index zu filtern. Dadurch wird der [Any-Operator](./search-query-odata-collection-operators.md) verwendet, um „BaseRate lt 100“ auf jedes Element in der Sammlung „Rooms“ anzuwenden. Weitere Informationen zur in der kognitiven Azure-Suche unterstützten OData-Syntax finden Sie [hier](./query-odata-filter-orderby-syntax.md).

Hier sehen Sie die Ergebnisse der Abfrage:

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

Anschließend möchten wir die zwei besten, in jüngster Zeit renovierten Hotels suchen und deren Hotelnamen und das Datum der letzten Renovierung anzeigen. Hier folgt der Code: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

In diesem Fall verwenden wir erneut OData-Syntax, um den Parameter `OrderBy` auf `lastRenovationDate desc` festzulegen. Wir legen zudem auch `Top` auf 2 fest, um sicherzustellen, dass wir nur die zwei obersten Dokumente abrufen. Wie zuvor geben wir `Select` an, um festzulegen, welche Felder zurückgegeben werden sollen.

Dies sind die Ergebnisse:

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

Zum Schluss möchten wir alle Hotelnamen suchen, für die das Wort „Hotel“ zutrifft:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Dies sind die Ergebnisse mit allen Feldern, da wir die Eigenschaft `Select` nicht festgelegt haben:

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

Mit diesem Schritt ist das Lernprogramm abgeschlossen. Gerne können Sie sich aber noch weiter mit dem Programm vertraut machen. ** Im Abschnitt „Nächste Schritte“ finden Sie eine Zusammenstellung weiterer Ressourcen zur Einarbeitung in die kognitive Azure-Suche.

## <a name="next-steps"></a>Nächste Schritte
* Nutzen Sie die Referenzen für das [.NET SDK](/dotnet/api/microsoft.azure.search) und die [REST-API](/rest/api/searchservice/).
* Lesen Sie den Abschnitt [Namenskonventionen](/rest/api/searchservice/Naming-rules) , um sich mit den Namensregeln für Objekte vertraut zu machen.
* Machen Sie sich mit den [unterstützten Datentypen](/rest/api/searchservice/Supported-data-types) in der kognitiven Azure-Suche vertraut.