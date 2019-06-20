---
title: 'Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit dem SDK für die visuelle Bing-Suche für C#'
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie ein Bild mit dem SDK für die visuelle Bing-Suche hochladen und Erkenntnisse dazu gewinnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: v-gedod
ms.openlocfilehash: 9fd693207b429ddc0a2bfdd772f4822f188ac3f7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884929"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit dem SDK für die visuelle Bing-Suche für C#

Verwenden Sie diese Schnellstartanleitung, um über den Dienst für die visuelle Bing-Suche mithilfe des C# SDK Erkenntnisse zu Bildern zu gewinnen. Die visuelle Bing-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden
* Das NuGet-Paket für die visuelle Suche. 
    - Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie im Menü `Manage NuGet Packages` (NuGet-Pakete verwalten) aus. Installieren Sie das `Microsoft.Azure.CognitiveServices.Search.VisualSearch`-Paket. Bei der Installation der NuGet-Pakete wird auch Folgendes installiert:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Visual Studio ein neues Projekt. Fügen Sie anschließend die folgenden Direktiven hinzu.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Instanziieren Sie den Client mit Ihrem Abonnementschlüssel.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Senden einer Suchanforderung 

1. Erstellen Sie einen `FileStream` für Ihre Bilder (in diesem Fall `TestImages/image.jpg`). Verwenden Sie anschließend den Client, um mit `client.Images.VisualSearchMethodAsync()` eine Suchanforderung zu senden. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Analysieren Sie die Ergebnisse der vorherigen Abfrage:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](tutorial-bing-visual-search-single-page-app.md)
