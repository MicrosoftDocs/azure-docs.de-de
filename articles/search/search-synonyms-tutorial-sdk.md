---
title: Synonyme – C#-Beispiel – Azure Search
description: In diesem C#-Beispiel erfahren Sie, wie Sie einem Index in Azure Search das Feature „Synonyme“ hinzufügen. Eine Synonymzuordnung ist eine Liste der entsprechenden Begriffe. Felder mit Synonymunterstützung erweitern Abfragen, sodass sie den vom Benutzer bereitgestellten Begriff und alle zugehörigen Synonyme enthalten.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5b81e4b9a8773cc8e4cc76582ccf2df88565d3d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025170"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Beispiel: Hinzufügen von Synonymen für Azure Search in C#

Anhand von Synonymen wird eine Abfrage erweitert, indem Begriffe, die als semantisch gleichwertig angesehen werden, dem eingegebenen Begriff hinzugefügt werden. Es kann beispielsweise sein, dass sich für den Begriff „Auto“ auch Übereinstimmungen für Dokumente ergeben sollen, die die Begriffe „Automobil“ oder „Fahrzeug“ enthalten. 

In Azure Search werden Synonyme in einer *Synonymzuordnung* anhand von *Zuordnungsregeln* für gleichwertige Begriffe definiert. Dieses Beispiel behandelt die wesentlichen Schritte zum Hinzufügen und Verwenden von Synonymen bei einem vorhandenen Index. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Synonymzuordnung mithilfe der Klasse [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) 
> * Festlegen der Eigenschaft [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) in Feldern, die die Abfrageerweiterung über Synonyme unterstützen sollen

Sie können ein für Synonyme aktiviertes Feld auf die übliche Weise abfragen. Für den Zugriff auf Synonyme ist keine zusätzliche Abfragesyntax erforderlich.

Sie können mehrere Synonymzuordnungen erstellen, diese für den gesamten Dienst als Ressource bereitstellen, die für alle Indizes verfügbar ist, und dann auf Feldebene angeben, was jeweils verwendet werden soll. Beim Abfragen führt Azure Search zusätzlich zum Durchsuchen eines Index dann eine Suche in einer Synonymzuordnung durch, falls diese für die Felder einer Abfrage angegeben ist.

> [!NOTE]
> Synonyme können programmgesteuert, jedoch nicht im Portal erstellt werden. Falls Unterstützung für Synonyme auch für das Azure-Portal nützlich wäre, lassen Sie uns Feedback über [UserVoice](https://feedback.azure.com/forums/263029-azure-search) zukommen.

## <a name="prerequisites"></a>Voraussetzungen

Für das Tutorial gelten die folgenden Anforderungen:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search-Dienst](search-create-service-portal.md)
* [Microsoft.Azure.Search-.NET-Bibliothek](https://aka.ms/search-sdk)
* [Verwenden von Azure Search aus einer .NET-Anwendung](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Übersicht

Anhand von Vorher- und Nachher-Abfragen soll der Nutzen von Synonymen veranschaulicht werden. In diesem Beispiel wird eine Beispielanwendung verwendet, mit der Abfragen ausgeführt und Ergebnisse für einen Beispielindex zurückgegeben werden. Mit der Beispielanwendung wird ein kleiner Index mit dem Namen „hotels“ erstellt, der mit zwei Dokumenten gefüllt wird. Die Anwendung führt Suchabfragen mithilfe von Begriffen und Wortgruppen durch, die nicht im Index enthalten sind, aktiviert das Feature „Synonyme“ und führt die gleichen Suchvorgänge dann erneut durch. Im unten angegebenen Code ist der gesamte Ablauf dargestellt.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Die Schritte zum Erstellen und Auffüllen des Beispielindex sind unter [Verwenden von Azure Search aus einer .NET-Anwendung](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) beschrieben.

## <a name="before-queries"></a>Vorher-Abfragen

Führen Sie in `RunQueriesWithNonExistentTermsInIndex` Suchabfragen durch, die die Begriffe „five star“, „internet“ und „economy AND hotel“ enthalten.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Diese Begriffe sind in den beiden indizierten Dokumenten nicht enthalten, sodass wir für das `RunQueriesWithNonExistentTermsInIndex`-Element die folgende Ausgabe erhalten:
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Aktivieren von Synonymen

Die Aktivierung von Synonymen ist ein Prozess mit zwei Schritten. Zuerst ist es erforderlich, Synonymregeln zu definieren und hochzuladen, und anschließend können wir Felder konfigurieren, für die diese Regeln verwendet werden. Der Prozess ist unter `UploadSynonyms` und `EnableSynonymsInHotelsIndex` dargestellt.

1. Fügen Sie Ihrem Suchdienst eine Synonymzuordnung hinzu. In `UploadSynonyms` definieren wir vier Regeln in unserer Synonymzuordnung „desc-synonymmap“ und laden sie in den Dienst hoch.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Eine Synonymzuordnung muss dem Open-Source-Standardformat `solr` entsprechen. Das Format ist unter [Synonyme in Azure Search](search-synonyms.md) im Abschnitt `Apache Solr synonym format` beschrieben.

2. Konfigurieren Sie die durchsuchbaren Felder, um die Synonymzuordnung in der Indexdefinition zu verwenden. In `EnableSynonymsInHotelsIndex` aktivieren wir Synonyme für die beiden Felder `category` und `tags`, indem wir die `synonymMaps`-Eigenschaft des Namens der neu hochgeladenen Synonymzuordnung festlegen.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Wenn Sie eine Synonymzuordnung hinzufügen, sind keine Neuerstellungen des Index erforderlich. Sie können Ihrem Dienst eine Synonymzuordnung hinzufügen und dann vorhandene Felddefinitionen für Indizes anpassen, um die neue Synonymzuordnung zu verwenden. Das Hinzufügen von neuen Attributen hat keine Auswirkung auf die Verfügbarkeit der Indizes. Dies gilt auch für das Deaktivieren von Synonymen für ein Feld. Sie können die `synonymMaps`-Eigenschaft einfach auf eine leere Liste festlegen.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Nachher-Abfragen

Nachdem die Synonymzuordnung hochgeladen wurde und der Index für die Verwendung der Synonymzuordnung aktualisiert wurde, wird beim zweiten `RunQueriesWithNonExistentTermsInIndex`-Aufruf Folgendes ausgegeben:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Mit der ersten Abfrage wird das Dokument über die Regel `five star=>luxury` gefunden. Bei der zweiten Abfrage wird die Suche mit `internet,wifi` erweitert, und bei der dritten Abfrage wird sowohl `hotel, motel` als auch `economy,inexpensive=>budget` verwendet, um die Dokumente anhand der Übereinstimmungen zu finden.

Durch das Hinzufügen von Synonymen wird die Sucherfahrung nachhaltig verändert. In diesem Beispiel wurden mit den ursprünglichen Abfragen keine aussagekräftigen Ergebnisse zurückgegeben, obwohl die im Index enthaltenen Dokumente relevant waren. Durch die Aktivierung von Synonymen können wir einen Index erweitern, um gängige Begriffe einzubeziehen, ohne hierfür die zugrunde liegenden Daten des Index ändern zu können.

## <a name="sample-application-source-code"></a>Quellcode der Beispielanwendung
Den vollständigen Quellcode der in diesem Artikel besprochenen Beispielanwendung finden Sie auf [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die schnellste Möglichkeit zum Bereinigen des Systems nach einem Beispiel besteht darin, die Ressourcengruppe zu löschen, die den Azure Search-Dienst enthält. Sie können dann die Ressourcengruppe löschen, um alle darin enthaltenen Daten endgültig zu löschen. Der Name der Ressourcengruppe befindet sich im Portal auf der Seite „Übersicht“ des Azure Search-Diensts.

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel wurde die Funktion für Synonyme in C#-Code veranschaulicht, um Zuordnungsregeln zu erstellen und zu veröffentlichen sowie anschließend die Synonymzuordnung für eine Abfrage aufzurufen. Weitere Informationen finden Sie in der Referenzdokumentation für [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) und [REST-API](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Verwenden von Synonymen in Azure Search](search-synonyms.md)
