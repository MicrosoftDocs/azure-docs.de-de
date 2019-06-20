---
title: 'Schnellstart: Suchen nach Bildern – Bing-Bildersuche-SDK für C#'
description: Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache C#-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a50ca8dfda1e51cdc27728975e40dcbf8ce6dbd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384206"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-c"></a>Schnellstart: Suchen nach Bildern mit dem Bing-Bildersuche-SDK für C#

Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache C#-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/vs/whatsnew/).
* Das [NuGet-Paket für die Cognitive Services-Bildersuche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0)

Um das Bing-Bildersuche-SDK in Visual Studio zu installieren, verwenden Sie die Option **NuGet-Pakete verwalten** im **Projektmappen-Explorer**.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

Erstellen Sie zunächst eine neue C#-Konsolenanwendung in Visual Studio. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

Erstellen Sie in der Main-Methode Ihres Projekts Variablen für Ihren gültigen Abonnementschlüssel, die von Bing zurückzugebenden Bildergebnisse und einen Suchbegriff. Instanziieren Sie dann den Client für die Bildersuche mit dem Schlüssel.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Senden einer Suchabfrage mit dem Client

Verwenden Sie den Client, um mit einem Abfragetext eine Suche durchzuführen:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analysieren und Anzeigen des ersten Bildergebnisses

Analysieren Sie die Bildergebnisse, die in der Antwort zurückgegeben werden.
Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und drucken Sie die Details aus, z.B. eine Miniaturansichts-URL, die ursprüngliche URL und die Gesamtzahl der zurückgegebenen Bilder.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Abrufen eines kostenlosen Cognitive Services-Zugriffsschlüssels](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [.NET-Beispiele für das Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentation zu Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenz zur Bing-Bildersuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
