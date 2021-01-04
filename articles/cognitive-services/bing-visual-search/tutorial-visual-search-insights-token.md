---
title: Suchen ähnlicher Bilder in vorherigen Suchvorgängen mithilfe von Bildauswertungstoken und der API für die visuelle Bing-Suche
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Clientbibliothek der visuellen Bing-Suche, um URLs von Bildern aus vorherigen Suchvorgängen abzurufen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a9005082148803423ac20b4f18dd974bb5b43b80
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486860"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Tutorial: Suchen ähnlicher Bilder in vorherigen Suchvorgängen mithilfe eines Bildauswertungstokens

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Clientbibliothek der visuellen Suche bietet Ihnen die Möglichkeit, Bilder aus einer vorherigen Suche online zu finden, die ein `ImageInsightsToken` zurückgeben. Diese Anwendung ruft ein `ImageInsightsToken` ab und verwendet das Token in einer nachfolgenden Suche. Dann sendet sie das `ImageInsightsToken` an Bing und gibt Ergebnisse zurück, die URLs der Bing-Suche und URLs von ähnlichen online ermittelten Bildern enthalten.

Der vollständige Quellcode für dieses Tutorial steht mit zusätzlichen Fehlerbehandlungen und Anmerkungen auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs) bereit.

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Unter Linux/macOS können Sie diese Anwendung mit [Mono](https://www.mono-project.com/) ausführen.
* Die NuGet-Pakete für die visuelle Suche und die Bildersuche.
    - Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie im Menü **NuGet-Pakete verwalten** aus. Installieren Sie das `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-Paket und das `Microsoft.Azure.CognitiveServices.Search.ImageSearch`-Paket. Bei der Installation der NuGet-Pakete wird auch Folgendes installiert:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Abrufen von „ImageInsightsToken“ aus der Clientbibliothek der Bing-Bildersuche

Diese Anwendung verwendet ein `ImageInsightsToken`, das über die [Clientbibliothek der Bing-Bildersuche](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) abgerufen wurde. Erstellen Sie in einer neuen C#-Konsolenanwendung mithilfe von `ImageSearchClient()` einen Client zum Aufrufen der API. Verwenden Sie dann `SearchAsync()` mit Ihrer Abfrage:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Speichern Sie das erste Suchergebnis mithilfe von `imageResults.Value.First()`, und speichern Sie dann das `ImageInsightsToken` der Bildauswertung.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Dieses `ImageInsightsToken` wird in einer Anforderung an die visuelle Bing-Suche gesendet.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Hinzufügen von „ImageInsightsTokens“ zu einer Anforderung der visuellen Suche

Geben Sie das `ImageInsightsToken` für eine Anforderung für die visuelle Suche durch das Erstellen eines `ImageInfo`-Objekts aus dem in Antworten von der visuellen Bing-Suche enthaltenen `ImageInsightsToken` an.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Verwenden der visuellen Bing-Suche, um Bilder aus einem ImageInsightsToken zu finden

Das `VisualSearchRequest`-Objekt enthält in `ImageInfo` Informationen zum Bild, das gesucht werden soll. Mit der `VisualSearchMethodAsync()`-Methode werden die Ergebnisse abgerufen. Sie müssen keine Bildbinärdatei bereitstellen, da das Bild durch das Token dargestellt wird.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Durchlaufen der Ergebnisse der visuellen Suche

Die Ergebnisse der thematischen Suche sind `ImageTag`-Objekte. Jedes Tag enthält eine Liste mit `ImageAction`-Objekten. Jedes Objekt vom Typ `ImageAction` enthält ein Feld vom Typ `Data`. Hierbei handelt es sich um eine Liste mit Werten, die von der Art der Aktion abhängen. Sie können beispielsweise die `ImageTag`-Objekte in `visualSearchResults.Tags` durchlaufen und das `ImageAction`-Tag darin abrufen. Das folgende Beispiel gibt die Details von `PagesIncluding`-Aktionen aus:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Zum Abrufen der eigentlichen Bild-URLs aus Aktionstypen ist eine Umwandlung erforderlich, bei der ein `ActionType`-Element als `ImageModuleAction` gelesen wird, das ein `Data`-Element mit einer Werteliste enthält. Jeder Wert ist die URL eines Bilds.  Im Folgenden wird der Aktionstyp `PagesIncluding` in `ImageModuleAction` umgewandelt, und die Werte werden gelesen:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Weitere Informationen zu diesen Datentypen finden Sie unter [Bilder – thematische Suche](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Zurückgegebene URLs

Die vollständige Anwendung gibt die folgenden URLs zurück:

|ActionType  |URL  |
|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Wie oben gezeigt enthalten die Typen `TopicResults` und `ImageResults` Abfragen für verwandte Bilder. Die URLs sind Verknüpfungen zu Bing-Suchergebnissen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App für die Visuelle Suche](tutorial-bing-visual-search-single-page-app.md)