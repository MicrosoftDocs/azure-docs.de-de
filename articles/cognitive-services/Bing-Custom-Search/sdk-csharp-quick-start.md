---
title: 'Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit dem C# SDK | Microsoft-Dokumentation'
titleSuffix: Azure Cognitive Services
description: Einrichten des SDK für die benutzerdefinierte Suche für die Konsolenanwendung in C#
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 9e13edce77819d5ef8cfc3b6becff9fb82224a83
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595964"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit dem C# SDK 

Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche mit dem C# SDK zu beginnen. Die benutzerdefinierte Bing-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK für die benutzerdefinierte Bing-Suche ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Instanz der benutzerdefinierten Bing-Suche. Weitere Informationen finden Sie unter [Schnellstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](quick-start.md).
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/)
- Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden
- Ein installiertes [NuGet-Paket für die benutzerdefinierte Suche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie im Menü **NuGet-Pakete verwalten** aus. Installieren Sie das `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-Paket. Bei der Installation des NuGet-Pakets für die benutzerdefinierte Suche werden gleichzeitig die folgenden Assemblys installiert:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Instanziieren Sie den Suchclient mit Ihrem API-Schlüssel in der main-Methode Ihrer Anwendung.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Senden der Suchanforderung und Erhalten einer Antwort
    
1. Senden Sie eine Suchabfrage, indem Sie die `SearchAsync()`-Methode Ihres Clients verwenden, und speichern Sie die Antwort. Ersetzen Sie `YOUR-CUSTOM-CONFIG-ID` durch die Konfigurations-ID Ihrer Instanz (Sie finden die ID im [Portal für die benutzerdefinierte Bing-Suche](https://www.customsearch.ai/)). In diesem Beispiel wird nach „Xbox“ gesucht.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Die Methode `SearchAsync()` gibt ein `WebData`-Objekt zurück. Verwenden Sie das Objekt, um alle gefundenen Webseiten (`WebPages`) zu durchlaufen. Dieser Code findet das erste Ergebnis der Webseite und gibt `Name` und `URL` der Webseite aus.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
