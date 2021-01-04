---
title: 'Schnellstart: C#-Clientbibliothek für Bing-Bildersuche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 247aea49d60c2d953d8b6dff37d22188a5442a1f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625522"
---
Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit der Bing-Bildersuche-Clientbibliothek aus. 

Die Clientsuchbibliothek ist ein Wrapper für die REST-API und enthält die gleichen Funktionen. 

Sie erstellen eine C#-Anwendung, die eine Bildersuchabfrage sendet, die JSON-Antwort analysiert und die URL des ersten zurückgegebenen Bilds anzeigt.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/vs/whatsnew/), wenn Sie Windows verwenden
* [VS Code](https://code.visualstudio.com) mit [.NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install), wenn Sie macOS oder Linux verwenden
* [Ein kostenloses Azure-Abonnement](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

## <a name="create-a-console-project"></a>Erstellen eines Konsolenprojekts

Erstellen Sie zunächst eine neue C#-Konsolenanwendung.

## <a name="create-a-console-project"></a>Erstellen eines Konsolenprojekts

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Erstellen Sie in Visual Studio eine neue Konsolenprojektmappe mit dem Namen *BingImageSearch*.
    
1. Fügen Sie das [NuGet-Paket für die Cognitive Services-Bildersuche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) hinzu.
    1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt.
    1. Wählen Sie **NuGet-Pakete verwalten** aus.
    1. Suchen Sie nach *Microsoft.Azure.CognitiveServices.Search.ImageSearch*, wählen Sie das Paket aus, und installieren Sie es.
    
# <a name="vs-code"></a>[VS-Code](#tab/vscode)

1. Öffnen Sie das Terminalfenster in VS Code.
1. Erstellen Sie ein neues Konsolenprojekt mit dem Namen *BingImageSearch*, indem Sie den folgenden Code im Terminalfenster eingeben:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Öffnen Sie in VS Code den Ordner *BingImageSearch*.
1. Fügen Sie das [NuGet-Paket für die Cognitive Services-Bildersuche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) hinzu, indem Sie den folgenden Code im Terminalfenster eingeben:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Initialisieren der Anwendung


1. Ersetzen Sie alle `using`-Anweisungen in *Program.cs* durch folgenden Code:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. Erstellen Sie in der `Main`-Methode Ihres Projekts Variablen für Ihren gültigen Abonnementschlüssel, die von Bing zurückzugebenden Bildergebnisse und einen Suchbegriff. Instanziieren Sie dann den Client für die Bildersuche mit dem Schlüssel.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Senden einer Suchabfrage mit dem Client
    
Verwenden Sie in der `Main`-Methode den Client, um mit einem Abfragetext eine Suche durchzuführen:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analysieren und Anzeigen des ersten Bildergebnisses

Analysieren Sie die Bildergebnisse, die in der Antwort zurückgegeben werden. 

Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und geben Sie einige Details aus:

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](../../overview.md)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [.NET-Beispiele für das Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentation zu Azure Cognitive Services](../../../index.yml)
* [Referenz zur Bing-Bildersuche-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)