---
title: Synonyme – C#-Beispiel
titleSuffix: Azure Cognitive Search
description: In diesem C#-Beispiel erfahren Sie, wie Sie einem Index in der kognitiven Azure-Suche das Feature „Synonyme“ hinzufügen. Eine Synonymzuordnung ist eine Liste der entsprechenden Begriffe. Felder mit Synonymunterstützung erweitern Abfragen, sodass sie den vom Benutzer bereitgestellten Begriff und alle zugehörigen Synonyme enthalten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b97b223ac180df7f8eb07ad8eaab66847f50776
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422993"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Beispiel: Hinzufügen von Synonymen für die kognitive Azure-Suche in C#

Anhand von Synonymen wird eine Abfrage erweitert, indem Begriffe, die als semantisch gleichwertig angesehen werden, dem eingegebenen Begriff hinzugefügt werden. Es kann beispielsweise sein, dass sich für den Begriff „Auto“ auch Übereinstimmungen für Dokumente ergeben sollen, die die Begriffe „Automobil“ oder „Fahrzeug“ enthalten. 

In der kognitiven Azure-Suche werden Synonyme in einer *Synonymzuordnung* anhand von *Zuordnungsregeln* für gleichwertige Begriffe definiert. Dieses Beispiel behandelt die wesentlichen Schritte zum Hinzufügen und Verwenden von Synonymen bei einem vorhandenen Index.

In diesem Beispiel lernen Sie Folgendes:

> [!div class="checklist"]
> * Sie erstellen eine Synonymzuordnung mithilfe der [SynonymMap-Klasse](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Sie legen die [SynonymMapsName-Eigenschaft](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) in Feldern fest, die die Abfrageerweiterung über Synonyme unterstützen sollen.

Sie können ein für Synonyme aktiviertes Feld auf die übliche Weise abfragen. Für den Zugriff auf Synonyme ist keine zusätzliche Abfragesyntax erforderlich.

Sie können mehrere Synonymzuordnungen erstellen, diese für den gesamten Dienst als Ressource bereitstellen, die für alle Indizes verfügbar ist, und dann auf Feldebene angeben, was jeweils verwendet werden soll. Beim Abfragen führt die kognitive Azure-Suche zusätzlich zum Durchsuchen eines Index dann eine Suche in einer Synonymzuordnung durch, falls diese für die Felder einer Abfrage angegeben ist.

> [!NOTE]
> Synonyme können programmgesteuert, jedoch nicht im Portal erstellt werden.

## <a name="prerequisites"></a>Voraussetzungen

Für das Tutorial gelten die folgenden Anforderungen:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Dienst für die kognitive Azure-Suche](search-create-service-portal.md)
* [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/)

Wenn Sie noch nicht mit der .NET-Clientbibliothek vertraut sind, finden Sie weitere Informationen unter [Verwendung von Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Beispielcode

Den vollständigen Quellcode der hier verwendeten Beispielanwendung finden Sie auf [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Übersicht

Anhand von Vorher- und Nachher-Abfragen soll der Nutzen von Synonymen veranschaulicht werden. In diesem Beispiel führt eine Beispielanwendung Abfragen für einen mit zwei Dokumenten aufgefüllten Beispielindex „hotels“ aus und gibt Ergebnisse zurück. Zuerst führt die Anwendung Suchabfragen mit Begriffen und Ausdrücken aus, die im Index nicht vorhanden sind. Danach aktiviert der Code das Synonymfeature und gibt dieselben Abfragen erneut aus. Dieses Mal werden Ergebnisse basierend auf den Übereinstimmungen in der Synonymzuordnung zurückgegeben. 

Im unten angegebenen Code ist der gesamte Ablauf dargestellt.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Vorher-Abfragen

Führen Sie in `RunQueriesWithNonExistentTermsInIndex` Suchabfragen durch, die die Begriffe „five star“, „internet“ und „economy AND hotel“ enthalten.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Diese Begriffe sind keinem der beiden indizierten Dokumenten enthalten, sodass wir für die erste Abfrage `RunQueriesWithNonExistentTermsInIndex` folgende Ausgabe erhalten: **kein Dokument stimmte überein**.

## <a name="enable-synonyms"></a>Aktivieren von Synonymen

Nachdem die Vorher-Abfragen ausgeführt wurde, aktiviert der Beispielcode die Verwendung von Synonymen. Die Aktivierung von Synonymen ist ein Prozess mit zwei Schritten. Als Erstes werden Synonymregeln definiert und hochgeladen. Zweitens werden Felder zur Verwendung konfiguriert. Der Prozess ist unter `UploadSynonyms` und `EnableSynonymsInHotelsIndex` dargestellt.

1. Fügen Sie Ihrem Suchdienst eine Synonymzuordnung hinzu. In `UploadSynonyms` definieren wir vier Regeln in unserer Synonymzuordnung „desc-synonymmap“ und laden sie in den Dienst hoch.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Konfigurieren Sie die durchsuchbaren Felder, um die Synonymzuordnung in der Indexdefinition zu verwenden. In `AddSynonymMapsToFields` aktivieren wir Synonyme für die beiden Felder `category` und `tags`, indem wir die `SynonymMapNames`-Eigenschaft des Namens der neu hochgeladenen Synonymzuordnung festlegen.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Wenn Sie eine Synonymzuordnung hinzufügen, sind keine Neuerstellungen des Index erforderlich. Sie können Ihrem Dienst eine Synonymzuordnung hinzufügen und dann vorhandene Felddefinitionen für Indizes anpassen, um die neue Synonymzuordnung zu verwenden. Das Hinzufügen von neuen Attributen hat keine Auswirkung auf die Verfügbarkeit der Indizes. Dies gilt auch für das Deaktivieren von Synonymen für ein Feld. Sie können die `SynonymMapNames`-Eigenschaft einfach auf eine leere Liste festlegen.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Nachher-Abfragen

Nachdem die Synonymzuordnung hochgeladen wurde und der Index für die Verwendung der Synonymzuordnung aktualisiert wurde, wird beim zweiten `RunQueriesWithNonExistentTermsInIndex`-Aufruf Folgendes ausgegeben:

```
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

Mit der ersten Abfrage wird das Dokument über die Regel `five star=>luxury` gefunden. Bei der zweiten Abfrage wird die Suche mit `internet,wifi` erweitert, und bei der dritten Abfrage wird sowohl `hotel, motel` als auch `economy,inexpensive=>budget` verwendet, um die Dokumente anhand der Übereinstimmungen zu finden.

Durch das Hinzufügen von Synonymen wird die Sucherfahrung nachhaltig verändert. In diesem Beispiel wurden mit den ursprünglichen Abfragen keine aussagekräftigen Ergebnisse zurückgegeben, obwohl die im Index enthaltenen Dokumente relevant waren. Durch die Aktivierung von Synonymen können wir einen Index erweitern, um gängige Begriffe einzubeziehen, ohne hierfür die zugrunde liegenden Daten des Index ändern zu können.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die schnellste Möglichkeit zum Bereinigen des Systems nach einem Beispiel besteht darin, die Ressourcengruppe zu löschen, die den Dienst für die kognitive Azure-Suche enthält. Sie können dann die Ressourcengruppe löschen, um alle darin enthaltenen Daten endgültig zu löschen. Der Name der Ressourcengruppe befindet sich im Portal auf der Seite „Übersicht“ des Azure Cognitive Search-Diensts.

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel wurde die Funktion für Synonyme in C#-Code veranschaulicht, um Zuordnungsregeln zu erstellen und zu veröffentlichen sowie anschließend die Synonymzuordnung für eine Abfrage aufzurufen. Weitere Informationen finden Sie in der Referenzdokumentation für [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) und [REST-API](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Verwenden von Synonymen in der kognitiven Azure-Suche](search-synonyms.md)