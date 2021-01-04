---
title: Schnellstartanleitung für C# (Legacy)
titleSuffix: Azure Cognitive Search
description: In dieser Schnellstartanleitung für C# wird Version 10 der Clientbibliothek (Microsoft.Azure.Search) zum Erstellen, Laden und Abfragen eines Suchindexes verwendet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8dc2eb898c12e374bc503c5a05f00eb20667443b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701839"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Schnellstart: Erstellen eines Suchindex mithilfe der Legacyclientbibliothek Microsoft.Azure.Search v10

Bei diesem Artikel handelt es sich um den Schnellstart für C# für die Legacyclientbibliothek [**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) (Version 10), die nun von der Clientbibliothek [**zure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) (Version 11) abgelöst wird.

> [!NOTE]
> Wenn Sie über bestehende oder laufende Entwicklungsprojekte verfügen, können Sie weiterhin Version 10 verwenden. Für neue Projekte oder um neue Features zu verwenden, sollten Sie auf die [neue Bibliothek](/dotnet/api/overview/azure/search.documents-readme) umstellen.

## <a name="about-this-quickstart"></a>Informationen zu dieser Schnellstartanleitung

Erstellen Sie mithilfe von Visual Studio und den [Clientbibliotheken von „Microsoft.Azure.Search“](/dotnet/api/overview/azure/search/client10) in C# eine .NET Core-Konsolenanwendung, die einen Azure Cognitive Search-Index erstellt, lädt und abfragt. 

In diesem Artikel wird die Erstellung der Anwendung erläutert. Sie können auch [die vollständige Anwendung herunterladen und ausführen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10).

> [!NOTE]
> Der Democode in diesem Artikel enthält der Einfachheit halber die synchronen Methoden von Version 10 des Azure Cognitive Search .NET SDK. In Produktionsszenarien sollten dagegen zur Gewährleistung der Skalierbarkeit und Reaktionsfähigkeit der eigenen Anwendungen die asynchronen Methoden verwendet werden. So können Sie beispielsweise `Create` und `Delete` durch `CreateAsync` und `DeleteAsync` ersetzen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (beliebige Edition). Der Beispielcode und die Anleitung wurden in der kostenlosen Community-Edition getestet.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Aufrufe, die an den Dienst gerichtet werden, erfordern jeweils einen URL-Endpunkt und einen Zugriffsschlüssel. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

2. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   Rufen Sie auch den Abfrageschlüssel ab. Es empfiehlt sich, Abfrageanforderungen mit schreibgeschütztem Zugriff auszugeben.

![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="set-up-your-environment"></a>Einrichten Ihrer Umgebung

Öffnen Sie zunächst Visual Studio, und erstellen Sie ein neues Konsolen-App-Projekt, das mit .NET Core ausgeführt werden kann.

### <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

Das [Paket „Microsoft.Azure.Search“](https://www.nuget.org/packages/Microsoft.Azure.Search/) besteht aus einigen Clientbibliotheken, die als NuGet-Pakete verteilt werden.

Verwenden Sie für dieses Projekt die Version 10 des NuGet-Pakets `Microsoft.Azure.Search` sowie das neueste NuGet-Paket `Microsoft.Extensions.Configuration.Json`.

1. Wählen Sie unter **Tools** > **NuGet-Paket-Manager** die Option **NuGet-Pakete für Projektmappe verwalten...** aus. 

1. Klicken Sie auf **Durchsuchen**.

1. Suchen Sie nach `Microsoft.Azure.Search`, und wählen Sie Version 10 aus.

1. Klicken Sie rechts auf **Installieren**, um die Assembly Ihrem Projekt und Ihrer Projektmappe hinzuzufügen.

1. Wiederholen Sie diese Schritte für `Microsoft.Extensions.Configuration.Json`, und wählen Sie dabei mindestens die Version 2.2.0 aus.


### <a name="add-azure-cognitive-search-service-information"></a>Hinzufügen von Azure Cognitive Search-Dienstinformationen

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element...** aus. 

1. Suchen Sie unter „Neues Element hinzufügen“ nach „JSON“. Daraufhin wird eine Liste mit JSON-bezogenen Elementtypen zurückgegeben.

1. Wählen Sie **JSON-Datei** aus, nennen Sie die Datei „appsettings.json“, und klicken Sie auf **Hinzufügen**. 

1. Fügen Sie die Datei Ihrem Ausgabeverzeichnis hinzu. Klicken Sie mit der rechten Maustaste auf „appsettings.json“, und wählen Sie **Eigenschaften** aus. Wählen Sie unter **In Ausgabeverzeichnis kopieren** die Option **Kopieren, wenn neuer** aus.

1. Kopieren Sie den folgenden JSON-Code in Ihre neue JSON-Datei. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Setzen Sie für den Namen des Suchdiensts (YOUR-SEARCH-SERVICE-NAME) und den Admin-API-Schlüssel (YOUR-ADMIN-API-KEY) gültige Werte ein. Ist Ihr Dienstendpunkt also beispielsweise `https://mydemo.search.windows.net`, lautet der Dienstname `mydemo`.

### <a name="add-class-method-files-to-your-project"></a>Hinzufügen von Klassendateien vom Typ „.Method“ zu Ihrem Projekt

Dieser Schritt ist erforderlich, um in der Konsole eine sinnvolle Ausgabe zu erzeugen. Wenn Ergebnisse im Konsolenfenster ausgegeben werden, müssen einzelne Felder aus dem Objekt „Hotel“ als Zeichenfolgen zurückgegeben werden. In diesem Schritt wird zur Durchführung dieser Aufgabe [ToString()](/dotnet/api/system.object.tostring) implementiert, indem Sie den erforderlichen Code in zwei neue Dateien kopieren.

1. Fügen Sie Ihrem Projekt zwei leere Klassendefinitionen hinzu: „Address.Methods.cs“ und „Hotel.Methods.cs“.

1. Überschreiben Sie in „Address.Methods.cs“ den Standardinhalt mit dem folgenden Code ([Zeilen 1 bis 25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25)).

1. Kopieren Sie in „Hotel.Methods.cs“ die [Zeilen 1 bis 68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1\. Erstellen des Index

Der Hotelindex setzt sich aus einfachen und komplexen Feldern zusammen. Ein einfaches Feld ist beispielsweise „HotelName“ oder „Description“. Bei komplexen Feldern handelt es sich beispielsweise um eine Adresse mit Unterfeldern oder um eine Zimmerauflistung. Wenn ein Index komplexe Typen enthält, isolieren Sie die komplexen Felddefinitionen in separaten Klassen.

1. Fügen Sie Ihrem Projekt zwei leere Klassendefinitionen hinzu: „Address.cs“ und „Hotel.cs“.

1. Überschreiben Sie in „Address.cs“ den Standardinhalt mit dem folgenden Code:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. In „Hotel.cs“ definiert die Klasse die allgemeine Struktur des Index (einschließlich Verweisen auf die Adressklasse).

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Attribute für das Feld bestimmen die Verwendung in einer Anwendung. So muss beispielsweise jedem Feld, das in eine Volltextsuche einbezogen werden soll, das Attribut `IsSearchable` zugewiesen werden. 
    
    > [!NOTE]
    > Im .NET SDK müssen Felder explizit mit den Attributen [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable), [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable), [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) und [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable) versehen werden. Dieses Verhalten steht im Gegensatz zur Rest-API, die implizit die Zuordnung basierend auf dem Datentyp aktiviert (einfache Zeichenfolgenfelder können beispielsweise automatisch durchsucht werden).

    Zur eindeutigen Identifizierung der einzelnen Dokumente muss Ihr Index vom Typ `string` genau ein Feld vom Typ *key* enthalten. In diesem Schema ist `HotelId` der Schlüssel.

    In diesem Index wird für die Beschreibungsfelder die optionale Eigenschaft [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) verwendet, um die Lucene-Standardanalyse zu überschreiben. Für das Feld `description_fr` wird die französische Lucene-Analyse ([FrLucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)) verwendet, da in diesem Feld französischer Text gespeichert wird. Für `description` wird die optionale Sprachanalyse von Microsoft ([EnMicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)) verwendet.

1. Erstellen Sie in „Program.cs“ eine Instanz der Klasse [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient), um eine Verbindung mit dem Dienst herzustellen, und verwenden Sie dabei gespeicherte Werte aus der Konfigurationsdatei der Anwendung (appsettings.json). 

   `SearchServiceClient` verfügt über eine Eigenschaft vom Typ [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes). Diese stellt alle Methoden bereit, die Sie zum Erstellen, Auflisten, Aktualisieren oder Löschen von Azure Cognitive Search-Indizes benötigen. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Verwenden Sie in Ihrer Anwendung nach Möglichkeit nur eine einzelne Instanz von `SearchServiceClient`, um zu vermeiden, dass zu viele Verbindungen geöffnet werden. Klassenmethoden sind threadsicher und ermöglichen eine solche gemeinsame Verwendung.

   Die Klasse verfügt über mehrere Konstruktoren. Für den gewünschten Konstruktor werden der Name Ihres Suchdiensts und ein `SearchCredentials` -Objekt als Parameter verwendet. `SearchCredentials` umfasst Ihren API-Schlüssel.

    In der Indexdefinition können Sie die Objekte vom Typ `Field` am einfachsten erstellen, indem Sie die Methode `FieldBuilder.BuildForType` aufrufen und dabei eine Modellklasse für den Typparameter übergeben. Eine Modellklasse verfügt über Eigenschaften, die den Felder Ihres Index zugeordnet werden. Durch diese Zuordnung können Sie Dokumente aus Ihrem Suchindex an Instanzen Ihrer Modellklasse binden.

    > [!NOTE]
    > Falls Sie keine Modellklasse verwenden möchten, können Sie `Field`-Objekte auch direkt erstellen, um Ihren Index zu definieren. Sie können den Namen des Felds zusammen mit dem Datentyp (oder dem Analyzer bei Zeichenfolgenfeldern) für den Konstruktor angeben. Sie können auch andere Eigenschaften festlegen, z. B. `IsSearchable` oder `IsFilterable`, um nur einige zu nennen.
    >

1. Drücken Sie F5, um die App und den Index zu erstellen. 

    Nach erfolgreicher Erstellung des Projekts wird ein Konsolenfenster geöffnet, in dem Statusmeldungen für das Löschen und Erstellen des Index angezeigt werden. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Laden von Dokumenten

In Azure Cognitive Search sind Dokumente Datenstrukturen, bei denen es sich sowohl um Eingaben für die Indizierung als auch um Ausgaben von Abfragen handeln kann. Beispiele für Dokumenteingaben aus einer externen Datenquelle wären etwa Zeilen in einer Datenbank, Blobs in Blob Storage oder JSON-Dokumente auf dem Datenträger. In diesem Beispiel nehmen wir eine Abkürzung und betten JSON-Dokumente für vier Hotels direkt in den Code ein. 

Beim Hochladen von Dokumenten muss ein Objekt vom Typ [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch) verwendet werden. Ein Objekt vom Typ `IndexBatch` enthält eine Sammlung mit Objekten vom Typ [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction), und jedes dieser Objekte enthält wiederum ein Dokument und eine Eigenschaft, die Azure Cognitive Search mitteilt, welche Aktion ausgeführt werden soll ([„upload“, „merge“, „delete“ oder „mergeOrUpload“](search-what-is-data-import.md#indexing-actions)).

1. Erstellen Sie in „Program.cs“ ein Array mit Dokumenten und Indexaktionen, und übergeben Sie es anschließend an `IndexBatch`. Die folgenden Dokumente entsprechen dem Index „hotel-quickstart“ (gemäß Definition durch die Klassen „hotel“ und „address“).

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Nachdem Sie das Objekt `IndexBatch` initialisiert haben, können Sie es an den Index senden, indem Sie [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) für Ihr Objekt vom Typ [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient) aufrufen. `Documents` ist eine Eigenschaft von `SearchIndexClient`, die Methoden zum Hinzufügen, Ändern, Löschen oder Abfragen von Dokumenten in Ihrem Index bereitstellt.

    Der Block `try`/`catch`, der den Aufruf der Methode `Index` umgibt, fängt Indizierungsfehler ab, die bei starker Auslastung Ihres Diensts auftreten können. In Produktionscode können Sie die Indizierung der nicht erfolgreich indizierten Dokumente zu einem späteren Zeitpunkt noch einmal ausführen, einen Protokolleintrag erstellen und den Vorgang fortsetzen (wie im Beispiel gezeigt) oder eine andere Lösung verwenden, die die Datenkonsistenzanforderungen Ihrer Anwendung erfüllt.

    Die Verzögerung von zwei Sekunden dient zur Kompensierung der Indizierung. Diese erfolgt asynchron, sodass alle Dokumente vor der Ausführung der Abfragen indiziert werden können. Die Programmierung einer solchen Verzögerung ist in der Regel nur in Demos, bei Tests und in Beispielanwendungen erforderlich.

1. Kommentieren Sie in „Program.cs“ unter „main“ die Zeilen für „2 - Load documents“ aus. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Drücken Sie F5, um die App neu zu erstellen. 

    Nach erfolgreicher Erstellung des Projekts wird ein Konsolenfenster geöffnet, in dem Statusmeldungen ausgegeben werden – diesmal mit einer Meldung zum Hochladen von Dokumenten. Im Azure-Portal sollte der Index „hotels-quickstart“ auf der Seite **Übersicht** des Suchdiensts nun über vier Dokumente verfügen.

Weitere Informationen zur Verarbeitung von Dokumenten finden Sie unter [Behandeln von Dokumenten durch das .NET SDK](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3\. Durchsuchen eines Index

Abfrageergebnisse können abgerufen werden, sobald das erste Dokument indiziert wurde. Mit dem Testen des Index sollte aber gewartet werden, bis alle Dokumente indiziert wurden. 

In diesem Abschnitt werden zwei Funktionen hinzugefügt: Abfragelogik und Ergebnisse. Verwenden Sie für Abfragen die Methode [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search). Diese Methode akzeptiert sowohl Suchtext als auch andere [Parameter](/dotnet/api/microsoft.azure.search.models.searchparameters). 

Die Klasse [`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) stellt die Ergebnisse dar.


1. Erstellen Sie in „Program.cs“ eine WriteDocuments-Methode, die Suchergebnisse in der Konsole ausgibt.

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

1. Erstellen Sie eine RunQueries-Methode, um Abfragen auszuführen und Ergebnisse zurückzugeben. Bei den Ergebnissen handelt es sich um Objekte vom Typ „Hotel“. Mithilfe des Parameters „select“ können individuelle Felder angezeigt werden. Ist ein Feld nicht in den Parameter „select“ eingeschlossen, ist die zugehörige Eigenschaft vom Typ „Hotel“ NULL.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Es gibt zwei [Abgleichsmethoden für Begriffe in einer Abfrage](search-query-overview.md#types-of-queries): Volltextsuche und Filter. Bei der Volltextsuche werden Felder vom Typ `IsSearchable` in Ihrem Index nach einem oder mehreren Begriffen durchsucht. Ein Filter ist ein boolescher Ausdruck, der für Felder vom Typ `IsFilterable` in einem Index ausgewertet wird. Volltextsuche und Filter können separat oder zusammen verwendet werden.

    Suchen und Filtern erfolgt mithilfe der `Documents.Search` -Methode. Eine Suchabfrage kann im Parameter `searchText` und ein Filterausdruck kann in der Eigenschaft `Filter` der Klasse `SearchParameters` übergeben werden. Um ohne Suche zu filtern, übergeben Sie einfach `"*"` für den Parameter `searchText`. Wenn Sie ohne Filter suchen möchten, legen Sie die Eigenschaft `Filter` nicht fest, oder übergeben Sie einfach keine `SearchParameters`-Instanz.

1. Kommentieren Sie in „Program.cs“ unter „main“ die Zeilen für „3 - Search“ aus. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Damit ist die Lösung fertig. Drücken Sie F5, um die App neu zu erstellen und das fertige Programm auszuführen. 

    Neben den gleichen Meldungen wie zuvor enthält die Ausgabe nun auch Abfrageinformationen und Ergebnisse.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

In dieser C#-Schnellstartanleitung haben Sie eine Reihe von Aufgaben ausgeführt, um einen Index zu erstellen, Dokumente in den Index zu laden und Abfragen auszuführen. In einzelnen Phasen haben wir den Code zur besseren Lesbarkeit und zum besseren Verständnis ein wenig vereinfacht. Nachdem Sie sich mit den grundlegenden Konzepten vertraut gemacht haben, können Sie Ihr Wissen im nächsten Artikel mit alternativen Ansätzen und Konzepten vertiefen. 

Bei dem Beispielcode und dem Index handelt es sich jeweils um erweiterte Versionen, die auf diesem Artikel aufbauen. Im nächsten Beispiel wird eine Zimmersammlung hinzugefügt, und es werden andere Klassen und Aktionen verwendet. Außerdem wird die Verarbeitung eingehender beleuchtet.

> [!div class="nextstepaction"]
> [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)

Möchten Sie Ihre Cloudausgaben optimieren und dabei sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)