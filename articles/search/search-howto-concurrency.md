---
title: Verwalten gleichzeitiger Schreibvorgänge bei Ressourcen
titleSuffix: Azure Cognitive Search
description: Verwenden Sie die optimistische Nebenläufigkeit, um während der Ausführung von Update- und Löschvorgängen Konflikte in Indizes, Indexern und Datenquellen der kognitiven Azure-Suche zu vermeiden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 1cb8d578c05166f88ed7e91681dd6b5f15b1e3e5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358642"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Verwalten der Parallelität in der kognitiven Azure-Suche

Beim Verwalten von Ressourcen der kognitiven Azure-Suche wie Indizes und Datenquellen ist es wichtig, dass Ressourcen auf sichere Weise aktualisiert werden, insbesondere wenn unterschiedliche Komponenten der Anwendung gleichzeitig auf Ressourcen zugreifen. Wenn zwei Clients eine Ressource gleichzeitig unkoordiniert aktualisieren, können Racebedingungen auftreten. Um dies zu verhindern, folgt die kognitive Azure-Suche dem *Modell der optimistischen Nebenläufigkeit*. Es gibt keine Sperren für eine Ressource. Stattdessen ist für jede Ressource, die durch die Ressourcenversion angegeben wird, ein ETag vorhanden, damit Sie Anforderungen erstellen können, die ein versehentliches Überschreiben verhindern.

> [!Tip]
> Der konzeptionelle Code in einer [C#-Beispiellösung](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) dient der Erläuterung der Funktionsweise der Parallelitätssteuerung in der kognitiven Azure-Suche. Mit dem Code werden Bedingungen erstellt, die die Parallelitätssteuerung aufrufen. Den meisten Entwickler genügt es, das [Codefragment unten](#samplecode) durchzulesen. Wenn Sie es jedoch ausführen möchten, bearbeiten Sie die Datei „appsettings.json“, indem Sie den Dienstnamen und einen Admin-API-Schlüssel hinzufügen. Wenn die Dienst-URL von `http://myservice.search.windows.net` lautet, ist der Dienstname `myservice`.

## <a name="how-it-works"></a>Funktionsweise

Die optimistische Nebenläufigkeit ist über Prüfungen von Zugriffsbedingungen in API-Aufrufen implementiert, die in Indizes, Indexer, Datenquellen und synonymMap-Ressourcen schreiben.

Alle Ressourcen verfügen über ein [*Entity Tag (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag), das Informationen zur Objektversion bereitstellt. Indem Sie zuerst das ETag überprüfen, können Sie in typischen Workflows (abrufen, lokal ändern, aktualisieren) gleichzeitige Updates vermeiden, indem Sie sicherstellen, dass das ETag der Ressource mit dem der lokalen Kopie übereinstimmt.

+ Die REST-API verwendet ein [ETag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) im Anforderungsheader.
+ Das ETag wird vom .NET SDK über ein accessCondition-Objekt festgelegt, wobei der [If-Match | If-Match-None-Header](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) für die Ressource gesetzt wird. Objekte, die ETags verwenden, z. B. [SynonymMap.ETag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) und [SearchIndex.ETag](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag), weisen ein accessCondition-Objekt auf.

Jedes Mal, wenn Sie eine Ressource aktualisieren, ändert sich dessen ETag automatisch. Wenn Sie die Parallelitätsverwaltung implementieren, fügen Sie lediglich eine Vorbedingung für die Updateanforderung hinzu, die verlangt, dass die Remoteressource das gleiche ETag wie die Kopie der Ressource hat, die Sie auf dem Client geändert haben. Wenn ein gleichzeitig ausgeführter Prozess die Remoteressource bereits geändert hat, stimmt das ETag nicht mit der Vorbedingung überein und die Anforderung gibt den Fehler „HTTP 412“ aus. Wenn Sie das .NET SDK verwenden, erfolgt dies in Form einer `CloudException`, bei der die `IsAccessConditionFailed()`-Erweiterungsmethode „true“ zurückgibt.

> [!Note]
> Es gibt nur einen Mechanismus für die Parallelität. Dieser wird immer verwendet, unabhängig von der für Ressourcenupdates verwendeten API.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Anwendungsfälle und Beispielcode

Der folgende Code veranschaulicht accessCondition-Prüfungen bei Updatevorgängen für Schlüssel:

+ Updatefehler, wenn die Ressource nicht mehr vorhanden ist
+ Updatefehler, wenn sich die Ressourcenversion geändert hat

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Beispielcode aus dem [DotNetETagsExplainer-Programm](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Entwurfsmuster

Ein Entwurfsmuster für die Implementierung der optimistischen Nebenläufigkeit muss eine Schleife enthalten, in der die Zugriffsbedingungsprüfung, ein Test der Zugriffsbedingung und optional ein Abruf der aktualisierten Ressource wiederholt werden, bevor versucht wird, die Änderungen erneut anzuwenden.

Dieser Codeausschnitt veranschaulicht das Hinzufügen einer synonymMap zu einem bereits vorhandenen Index. Dieser Code stammt aus [Synonyme – C#-Beispiel für die kognitive Azure-Suche](search-synonyms-tutorial-sdk.md).

Im Codeausschnitt wird der Index „hotels“ abgerufen, in einem Updatevorgang die Objektversion überprüft, eine Ausnahme ausgelöst, wenn der Vorgang fehlschlägt, und der Vorgang dann (bis zu drei Mal) wiederholt, wobei zunächst der Index vom Server abgerufen wird, um die aktuelle Version zu erhalten.

```csharp
        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum sicheren Aktualisieren eines vorhandenen Index im Kontext erhalten Sie unter [Synonyme – C#-Beispiel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

Versuchen Sie, eines der folgenden Beispiele so zu ändern, dass es ETags oder AccessCondition-Objekte enthält.

+ [REST-API-Beispiel auf GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-Beispiel auf GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Diese Lösung umfasst das Projekt „DotNetEtagsExplainer“ mit dem in diesem Artikel vorgestellten Code.

## <a name="see-also"></a>Weitere Informationen

[Allgemeine HTTP-Anforderung und Antwort-Header](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-Statuscodes](/rest/api/searchservice/http-status-codes)
[Indexvorgänge (REST-API)](/rest/api/searchservice/index-operations)