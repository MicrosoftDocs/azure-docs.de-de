---
title: 'Tutorial: Zuschneiden eines Bilds mithilfe des Bing Visual Search SDK'
description: Verwenden Sie das Bing Visual Search SDK, um Erkenntnisse zu bestimmten Bildbereichen zu gewinnen.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ad73d7086a2066517c24a8b10a7f54503535942
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490294"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: Zuschneiden eines Bilds mithilfe des Bing Visual Search SDK für C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mithilfe des Bing Visual Search SDK können Sie ein Bild vor der Suche nach ähnlichen Onlinebildern zuschneiden. Diese Anwendung schneidet ein Bild mit mehreren Personen auf eine einzelne Person zu und gibt anschließend Suchergebnisse mit ähnlichen Onlinebildern zurück.

Der vollständige Quellcode für diese Anwendung ist zusammen mit zusätzlichen Fehlerbehandlungen und Anmerkungen auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) verfügbar.

In diesem Tutorial wird Folgendes veranschaulicht:

> [!div class="checklist"]
> * Senden einer Anforderung unter Verwendung des Bing Visual Search SDK
> * Zuschneiden eines Bildbereichs für die Suche mit der visuellen Bing-Suche
> * Empfangen und Verarbeiten der Antwort
> * Suchen nach den URLs von Aktionselementen in der Antwort

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden
* Ein installiertes [NuGet-Paket für die benutzerdefinierte Suche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie im Menü **NuGet-Pakete verwalten** aus. Installieren Sie das `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-Paket. Bei der Installation des NuGet-Pakets für die benutzerdefinierte Suche werden gleichzeitig die folgenden Assemblys installiert:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Angeben des Bereichs für den Bildzuschnitt

Diese Anwendung schneidet das Bild des Führungsteams von Microsoft auf einen bestimmten Bereich zu. Der Zuschneidebereich wird durch eine linke obere und eine rechte untere Koordinate definiert und als Prozentsatz des Gesamtbilds dargestellt:  

![Führungsteam von Microsoft](./media/MS_SrLeaders.jpg)

Zum Zuschneiden des Bilds wird anhand des Zuschneidebereichs ein Objekt vom Typ `ImageInfo` erstellt, und das Objekt `ImageInfo` wird in ein Element vom Typ `VisualSearchRequest` geladen. Das Objekt `ImageInfo` enthält auch die URL des Bilds:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Suchen nach Bildern, die dem Zuschneidebereich ähneln

Die Variable `VisualSearchRequest` enthält Informationen zum Zuschneidebereich des Bilds sowie die dazugehörige URL: Mit der `VisualSearchMethodAsync()`-Methode werden die Ergebnisse abgerufen:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Rufen Sie die URL-Daten von `ImageModuleAction` ab

Die Ergebnisse der visuellen Bing-Suche sind Objekte vom Typ `ImageTag`. Jedes Tag enthält eine Liste mit `ImageAction`-Objekten. Jedes Objekt vom Typ `ImageAction` enthält ein Feld vom Typ `Data`. Hierbei handelt es sich um eine Liste mit Werten, die von der Art der Aktion abhängen.

Sie können die unterschiedlichen Arten mit dem folgenden Code ausgeben:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Die vollständige Anwendung gibt Folgendes zurück:

|ActionType  |URL  |
|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2f www.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Wie oben zu sehen, enthält der Aktionstyp `Entity` eine Bing-Suchabfrage, die Informationen zu einer erkennbaren Person, einem Ort oder einem Gegenstand zurückgibt. Die Typen `TopicResults` und `ImageResults` enthalten Abfragen für verwandte Bilder. Die URLs in der Liste sind Verknüpfungen zu Bing-Suchergebnissen.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Abrufen von URLs für Bilder vom Typ `PagesIncluding` `ActionType`

Zum Abrufen der eigentlichen Bild-URLs ist eine Umwandlung erforderlich, bei der ein `ActionType`-Element als `ImageModuleAction` gelesen wird, das ein `Data`-Element mit einer Werteliste enthält. Jeder Wert ist die URL eines Bilds. Im Folgenden wird der Aktionstyp `PagesIncluding` in `ImageModuleAction` umgewandelt, und die Werte werden gelesen:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App für die Visuelle Suche](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen
> [Was ist die API für die Visuelle Bing-Suche?](./overview.md)