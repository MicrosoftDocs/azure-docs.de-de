---
title: 'Schnellstart: Durchführen einer Neuigkeitensuche – Bing-News-Suche-SDK für C#'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Bing-News-Suche-SDK für C# nach Nachrichten zu suchen und die Antwort zu verarbeiten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9e0a4da2662b9d4b4b775631705e7b4fcc1eecea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798674"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Schnellstart: Durchführen einer Neuigkeitensuche mit dem Bing-News-Suche-SDK für C#

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-News-Suche-SDK für C# mit der Suche nach Nachrichten zu beginnen. Die Bing-News-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/).
* Das [Json.NET](https://www.newtonsoft.com/json)-Framework, das als NuGet-Paket verfügbar ist
* Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden

* Das [NuGet-Paket mit dem SDK für die Bing-News-Suche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). Bei der Installation dieses Pakets wird auch Folgendes installiert:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Navigieren Sie in Visual Studio über den Projektmappen-Explorer zur Option `Manage NuGet Packages`, um mit dem SDK für die Bing-News-Suche eine Konsolenanwendung einzurichten.  Fügen Sie das Paket `Microsoft.Azure.CognitiveServices.Search.NewsSearch` hinzu.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Visual Studio eine neue C#-Konsolenprojektmappe. Fügen Sie dann Folgendes in die Hauptcodedatei ein:
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Erstellen Sie eine Variable für Ihren API-Schlüssel und einen Suchbegriff, und instanziieren Sie dann damit den Client für die Nachrichtensuche.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Senden einer Anforderung und Analysieren des Ergebnisses

1. Verwenden Sie den Client, um eine Suchanforderung an den Bing-News-Suche-Dienst zu senden:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Wurden Ergebnisse zurückgegeben, analysieren Sie sie:

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](tutorial-bing-news-search-single-page-app.md)
