---
title: 'C#-Tutorial: Optimieren der Indizierung mit der Push-API'
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Daten mithilfe der Push-API von Azure Cognitive Search effizient indizieren. Dieses Tutorial und der Beispielcode gelten für C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/12/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d22ff5c863617a3feb2a08d4b1889d0a7c10cd3a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693397"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Tutorial: Optimieren der Indizierung mit der Push-API

Von Azure Cognitive Search werden [zwei grundlegende Ansätze](search-what-is-data-import.md) für das Importieren von Daten in einen Suchindex unterstützt: Sie können die Daten programmgesteuert an den Index *pushen* oder einen [Azure Cognitive Search-Indexer](search-indexer-overview.md) auf eine unterstützte Datenquelle verweisen, um die Daten zu *pullen*.

In diesem Tutorial erfahren Sie, wie Sie Daten effizient mithilfe des [Pushmodells](search-what-is-data-import.md#pushing-data-to-an-index) indizieren, indem Sie Anforderungen zu einem Batch zusammenfassen und eine Wiederholungsstrategie mit exponentiellem Backoff verwenden. Sie können die [Beispielanwendung herunterladen und ausführen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). In diesem Artikel werden die zentralen Aspekte der Anwendung sowie Faktoren erläutert, die beim Indizieren von Daten zu berücksichtigen sind.

In diesem Tutorial werden C# und das [.NET SDK](/dotnet/api/overview/azure/search) verwendet, um die folgenden Aufgaben durchzuführen:

> [!div class="checklist"]
> * Erstellen eines Index
> * Testen verschiedener Batchgrößen, um die effizienteste Größe zu ermitteln
> * Asynchrones Indizieren von Batches
> * Erhöhen der Indizierungsgeschwindigkeit durch Verwendung mehrerer Threads
> * Verwenden einer Wiederholungsstrategie mit exponentiellem Backoff, um die Indizierung für nicht erfolgreiche Dokumente zu wiederholen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial sind folgende Dienste und Tools erforderlich:

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (beliebige Edition). Der Beispielcode und die Anleitung wurden in der kostenlosen Community-Edition getestet.

+ [Erstellen Sie einen Dienst für die kognitive Azure-Suche](search-create-service-portal.md), oder [suchen Sie nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in Ihrem aktuellen Abonnement.

<a name="get-service-info"></a>

## <a name="download-files"></a>Herunterladen von Dateien

Den Quellcode für dieses Tutorial finden Sie im GitHub-Repository [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) im Ordner [optimize-data-indexing/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11).

## <a name="key-considerations"></a>Wichtige Aspekte

Beim Pushen von Daten an einen Index gibt es einige wichtige Aspekte, die sich auf die Indizierungsgeschwindigkeit auswirken. Weitere Informationen zu diesen Faktoren finden Sie im Artikel [Indizieren großer Datasets in der kognitiven Azure-Suche](search-howto-large-index.md).

Im Anschluss finden Sie sechs wichtige Faktoren, die zu berücksichtigen sind:

+ **Dienstebene und Anzahl von Partitionen/Replikaten:** Zusätzlich hinzugefügte Partitionen und eine höhere Ebene tragen zur Beschleunigung der Indizierung bei.
+ **Indexschema:** Zusätzlich hinzugefügte Felder und Feldeigenschaften wie etwa *searchable*, *facetable* oder *filterable* tragen zur Verlangsamung der Indizierung bei.
+ **Batchgröße:** Die optimale Batchgröße hängt von Ihrem Indexschema und Dataset ab.
+ **Anzahl von Threads/Workern:** Von einem einzelnen Thread wird nicht die volle Indizierungsgeschwindigkeit genutzt.
+ **Wiederholungsstrategie:** Zur Optimierung der Indizierung sollte eine Wiederholungsstrategie mit exponentiellem Backoff verwendet werden.
+ **Datenübertragungsgeschwindigkeit im Netzwerk:** Die Datenübertragungsgeschwindigkeit kann ein limitierender Faktor sein. Indizieren Sie Daten innerhalb Ihrer Azure-Umgebung, um die Datenübertragungsgeschwindigkeit zu erhöhen.


## <a name="1---create-azure-cognitive-search-service"></a>1\. Erstellen eines Azure Cognitive Search-Diensts

Den für dieses Tutorial erforderlichen Azure Cognitive Search-Dienst können Sie [über das Portal erstellen](search-create-service-portal.md). Es empfiehlt sich, den gleichen Tarif zu verwenden, den Sie auch in Ihrer Produktionsumgebung verwenden möchten, um die Indizierungsgeschwindigkeit genau testen und optimieren zu können.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Abrufen eines Administrator-API-Schlüssels und einer URL für Azure Cognitive Search

Für API-Aufrufe benötigen Sie die Dienst-URL und einen Zugriffsschlüssel. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   ![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

## <a name="2---set-up-your-environment"></a>2\. Einrichten Ihrer Umgebung

1. Starten Sie Visual Studio, und öffnen Sie **OptimizeDataIndexing.sln**.
1. Öffnen Sie im Projektmappen-Explorer die Datei **appsettings.json**, um Verbindungsinformationen anzugeben.

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3\. Untersuchen des Codes

Nach der Aktualisierung von *appsettings.json* kann das Programm in **OptimizeDataIndexing.sln** erstellt und ausgeführt werden.

Dieser Code basiert auf der [C#-Schnellstartanleitung](search-get-started-dotnet.md). Der Artikel enthält ausführlichere Informationen zu den Grundlagen im Zusammenhang mit der Verwendung des .NET SDK.

Diese einfache C#/.NET-Konsolen-App führt folgende Aufgaben aus:

+ Sie erstellt auf der Grundlage der Datenstruktur der C#-Klasse „Hotel“ (die auch auf die Klasse „Address“ verweist) einen neuen Index.
+ Sie testet verschiedene Batchgrößen, um die effizienteste Größe zu ermitteln.
+ Sie indiziert Daten asynchron:
    + Unter Verwendung mehrerer Threads zur Erhöhung der Indizierungsgeschwindigkeit
    + Unter Verwendung einer Wiederholungsstrategie mit exponentiellem Backoff, um die Indizierung für nicht erfolgreiche Elemente zu wiederholen

 Nehmen Sie sich vor dem Ausführen des Programms etwas Zeit, um sich den Code sowie die Indexdefinitionen für dieses Beispiel etwas genauer anzusehen. Der relevante Code befindet sich in verschiedenen Dateien:

  + **Hotel.cs** und **Address.cs** enthalten das Schema zum Definieren des Index.
  + **DataGenerator.cs** enthält eine einfache Klasse, um die Erstellung großer Mengen an Hoteldaten zu vereinfachen.
  + **ExponentialBackoff.cs** enthält Code zur Optimierung des Indizierungsprozesses, wie unten beschrieben.
  + **Program.cs** enthält Funktionen zum Erstellen und Löschen des Azure Cognitive Search-Index, zum Indizieren von Datenbatches sowie zum Testen verschiedener Batchgrößen.

### <a name="creating-the-index"></a>Erstellen des Index

In diesem Beispielprogramm wird das .NET SDK verwendet, um einen Azure Cognitive Search-Index zu definieren und zu erstellen. Das SDK nutzt die Klasse `FieldBuilder`, um eine Indexstruktur auf der Grundlage einer C#-Datenmodellklasse zu generieren.

Das Datenmodell wird durch die Klasse „Hotel“ definiert, die auch Verweise auf die Klasse „Address“ enthält. „FieldBuilder“ führt ein Drilldown durch die verschiedenen Klassendefinitionen aus, um eine komplexe Datenstruktur für den Index zu generieren. Mithilfe von Metadatentags werden die Attribute der einzelnen Felder definiert, um beispielsweise anzugeben, ob das Feld durchsuchbar oder sortierbar ist.

Die folgenden Codeausschnitte aus der Datei **Hotel.cs** zeigen, wie ein einzelnes Feld und ein Verweis auf eine andere Datenmodellklasse angegeben werden können.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

In der Datei **Program.cs** wird der Index mit einem Namen und einer Feldsammlung definiert, die durch die Methode `FieldBuilder.Build(typeof(Hotel))` generiert wird, und anschließend erstellt:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Generieren von Daten

In der Datei **DataGenerator.cs** wird eine einfache Klasse implementiert, um Testdaten zu generieren. Der einzige Zweck dieser Klasse besteht darin, die Generierung einer großen Anzahl von Dokumenten mit einer eindeutigen ID für die Indizierung zu vereinfachen.

Durch Ausführen der folgenden Codezeilen können Sie eine Liste mit 100.000 Hotels mit jeweils eindeutiger ID generieren:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Zu Testzwecken stehen in diesem Beispiel zwei Hotelgrößen zur Verfügung: **small** (klein) und **large** (groß).

Das Schema Ihres Index kann erhebliche Auswirkungen auf die Indizierungsgeschwindigkeit haben. Es empfiehlt sich daher, diese Klasse nach Absolvierung des Tutorials zu konvertieren, um Daten zu generieren, die dem gewünschten Indexschema entsprechen.

## <a name="4---test-batch-sizes"></a>4\. Testen von Batchgrößen

Von Azure Cognitive Search werden folgende APIs unterstützt, um einzelne oder mehrere Dokumente in einen Index zu laden:

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST-API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction-Klasse](/dotnet/api/azure.search.documents.models.indexdocumentsaction) oder [IndexDocumentsBatch-Klasse](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

Wenn Sie Dokumente in Batches indizieren, verbessert sich die Indizierungsleistung erheblich. Diese Batches können bis zu 1.000 Dokumente oder bis zu etwa 16 MB pro Batch umfassen.

Die Bestimmung der optimalen Batchgröße für Ihre Daten ist ein wichtiger Faktor bei der Optimierung der Indizierungsgeschwindigkeit. Die optimale Batchgröße wird hauptsächlich durch die beiden folgenden Faktoren beeinflusst:

+ Indexschema
+ Datengröße

Da die optimale Batchgröße von Ihrem Index und von Ihren Daten abhängt, empfiehlt es sich, durch Testen verschiedener Batchgrößen zu ermitteln, bei welcher Größe die Indizierung für Ihr Szenario am schnellsten ist.

Die folgende Funktion veranschaulicht einen einfachen Ansatz zum Testen von Batchgrößen:

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Da nicht alle Dokumente die gleiche Größe haben (auch wenn dies in diesem Beispiel der Fall ist), schätzen wir die Größe der Daten, die wir an den Suchdienst senden. Hierzu verwenden wir die folgende Funktion. Von dieser wird das Objekt zunächst in das JSON-Format konvertiert und anschließend die Größe in Bytes bestimmt. Dadurch können wir die effizientesten Batchgrößen im Hinblick auf die Indizierungsgeschwindigkeit (MB/s) ermitteln.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

Für die Funktion sind ein Element vom Typ `SearchClient` sowie die Anzahl von Versuchen erforderlich, die für die einzelnen Batchgrößen getestet werden sollen. Da die Indizierungszeiten bei den einzelnen Batches etwas variieren können, wird jeder Batch standardmäßig dreimal getestet, um die Ergebnisse statistisch relevanter zu machen.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

Wenn Sie die Funktion ausführen, sollte in Ihrer Konsole eine Ausgabe wie die folgende angezeigt werden:

   ![Ausgabe der Funktion zum Testen der Batchgröße](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Ausgabe der Funktion zum Testen der Batchgröße")

Ermitteln Sie, welche Batchgröße am effizientesten ist, und verwenden Sie sie im nächsten Schritt des Tutorials. Unter Umständen ist bei verschiedenen Batchgrößen eine Stabilisierung auf einem bestimmten Niveau in MB/s zu beobachten.

## <a name="5---index-data"></a>5\. Indizieren von Daten

Nach Ermittlung der zu verwendenden Batchgröße besteht der nächste Schritt darin, die Daten zu indizieren. In diesem Beispiel werden folgende Schritte unternommen, um Daten effizient zu indizieren:

+ Es werden mehrere Threads/Worker verwendet.
+ Es wird eine Wiederholungsstrategie mit exponentiellem Backoff implementiert.

### <a name="use-multiple-threadsworkers"></a>Verwenden mehrerer Threads/Worker

Zur bestmöglichen Nutzung der Indizierungsgeschwindigkeit von Azure Cognitive Search müssen wahrscheinlich mehrere Threads verwendet werden, um Batchindizierungsanforderungen parallel an den Dienst zu senden.  

Die optimale Threadanzahl hängt von einigen der wichtigen Aspekte ab, die bereits weiter oben erwähnt wurden. Sie können dieses Beispiel ändern und mit einer anderen Threadanzahl testen, um die optimale Threadanzahl für Ihr Szenario zu ermitteln. Solange Sie jedoch mehrere parallel ausgeführte Threads verwenden, sollten von einem Großteil der Effizienzsteigerungen profitieren.

Im Zuge der Erhöhung der Anforderungen für den Suchdienst werden möglicherweise [HTTP-Statuscodes](/rest/api/searchservice/http-status-codes) mit dem Hinweis zurückgegeben, dass die Anforderung nicht vollständig erfolgreich war. Zwei gängige HTTP-Statuscodes im Zusammenhang mit der Indizierung sind:

+ **503 Dienst nicht verfügbar**: Dieser Fehler bedeutet, dass die Auslastung des Systems sehr hoch ist und Ihre Anforderungen aktuell nicht verarbeitet werden können.
+ **207 Multi-Status**: Dieser Fehler bedeutet, dass der Vorgang für einige Dokumente erfolgreich war, bei mindestens einem Dokument aber ein Fehler aufgetreten ist.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementieren einer Wiederholungsstrategie mit exponentiellem Backoff

Im Falle eines Fehlers sollten Anforderungen unter Verwendung einer [Wiederholungsstrategie mit exponentiellem Backoff](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff) wiederholt werden.

Anforderungen mit 503-Fehlern und anderen Fehlern werden vom .NET SDK von Azure Cognitive Search automatisch wiederholt. Für die Wiederholung bei 207-Fehlern muss allerdings eine eigene Logik implementiert werden. Eine Wiederholungsstrategie kann auch mithilfe von Open-Source-Tools wie [Polly](https://github.com/App-vNext/Polly) implementiert werden.

In diesem Beispiel wird eine eigene Wiederholungsstrategie mit exponentiellem Backoff implementiert. Hierzu werden zunächst einige Variablen definiert – unter anderem `maxRetryAttempts` und die anfängliche Verzögerung (`delay`) bei einer nicht erfolgreichen Anforderung:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Die Ergebnisse des Indizierungsvorgangs werden in der Variable `IndexDocumentResult result` gespeichert. Diese Variable ist wichtig, weil Sie mit ihr überprüfen können, ob bei Dokumenten im Batch Fehler aufgetreten sind (siehe unten). Bei partiellen Fehlern wird anhand der IDs der fehlerhaften Dokumente ein neues Batch erstellt.

`RequestFailedException`-Ausnahmen sollten ebenfalls abgefangen werden, weil sie darauf hindeuten, dass die gesamte Anforderung nicht erfolgreich war.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Der Code für das exponentielle Backoff wird in eine Funktion eingeschlossen, damit er mühelos aufgerufen werden kann.

Anschließend wird eine weitere Funktion erstellt, um die aktiven Threads zu verwalten. Der Einfachheit halber ist diese Funktion hier nicht enthalten. Sie finden sie aber in [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). Die Funktion kann mit dem folgenden Befehl aufgerufen werden. `hotels` sind hierbei die Daten, die wir hochladen möchten, `1000` ist die Batchgröße, und `8` ist die Anzahl paralleler Threads:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

Wenn Sie die Funktion ausführen, sollte eine Ausgabe wie die folgende angezeigt werden:

![Ausgabe der Datenindizierungsfunktion](media/tutorial-optimize-data-indexing/index-data-start.png "Ausgabe der Datenindizierungsfunktion")

Sollte bei einem Batch von Dokumenten ein Fehler auftreten, wird eine Ausgabe zurückgegeben, die den Fehler sowie die Information enthält, dass der Batch wiederholt wird:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

Nach Abschluss der Funktionsausführung können Sie überprüfen, ob dem Index alle Dokumente hinzugefügt wurden.

## <a name="6---explore-index"></a>6\. Untersuchen des Index

Nach Ausführung des Programms können Sie den aufgefüllten Suchindex programmgesteuert oder über den [**Suchexplorer**](search-explorer.md) des Portals erkunden.

### <a name="programatically"></a>Programmgesteuert

Bei der Überprüfung der Dokumentanzahl in einem Index stehen Ihnen hauptsächlich zwei Optionen zur Verfügung: die [API zum Zählen der Dokumente](/rest/api/searchservice/count-documents) und die [API zum Abrufen der Indexstatistik](/rest/api/searchservice/get-index-statistics). Bei beiden ist möglicherweise etwas zusätzliche Zeit für die Aktualisierung erforderlich. Machen Sie sich also keine Sorgen, falls die Anzahl zurückgegebener Dokumente niedriger ist als zunächst erwartet.

#### <a name="count-documents"></a>Dokumentenanzahl

Mit dem Vorgang zum Zählen von Dokumenten wird die Anzahl der in einem Suchindex enthaltenen Dokumente abgerufen:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Indexstatistiken abrufen

Mit dem Vorgang zum Abrufen der Indexstatistik werden Informationen zur Dokumentanzahl für den aktuellen Index sowie zur Speichernutzung zurückgegeben. Die Aktualisierung der Indexstatistik dauert länger als die Aktualisierung der Dokumentanzahl.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Azure-Portal

Öffnen Sie im Azure-Portal die Seite **Übersicht** für den Suchdienst, und suchen Sie in der Liste **Indexes** nach dem Index **optimize-indexing**.

  ![Liste mit den Azure Cognitive Search-Indizes](media/tutorial-optimize-data-indexing/portal-output.png "Liste mit den Azure Cognitive Search-Indizes")

*Dokumentanzahl* und *Speichergröße* basieren auf der [API zum Abrufen der Indexstatistik](/rest/api/searchservice/get-index-statistics), und die Aktualisierung kann mehrere Minuten dauern.

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekte aus Azure Cognitive Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

Im Beispielcode dieses Tutorials wird eine Überprüfung auf bereits vorhandene Indizes durchgeführt. Diese werden gelöscht, damit Sie Ihren Code erneut ausführen können.

Die Indizes können auch über das Portal gelöscht werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit dem Konzept der effizienten Datenerfassung vertraut sind, können Sie sich als Nächstes ausführlicher mit der Lucene-Abfragearchitektur und mit der Funktionsweise der Volltextsuche in Azure Cognitive Search beschäftigen.

> [!div class="nextstepaction"]
> [Funktionsweise der Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)