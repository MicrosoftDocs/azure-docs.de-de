---
title: 'Schnellstart: C#-Clientbibliothek für die benutzerdefinierte Bing-Suche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 4698649eace23e8428dffb9801294beb22ac126a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371839"
---
Hier finden Sie Informationen zu den ersten Schritten mit der Clientbibliothek für die benutzerdefinierte Bing-Suche für C#. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Die API für die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten werbefreien Suchbenutzeroberfläche für Themen, die Sie interessieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Verwenden Sie die Clientbibliothek für die benutzerdefinierten Bing-Suche für C# für Folgendes:
* Ermitteln Sie über Ihre Instanz der benutzerdefinierten Bing-Suche Suchergebnisse im Web.

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Voraussetzungen

- Eine Instanz der benutzerdefinierten Bing-Suche. Weitere Informationen finden Sie unter [Schnellstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](../../quick-start.md).
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/)
- Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden
- Das NuGet-Paket für die [benutzerdefinierte Bing-Suche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie im Menü **NuGet-Pakete verwalten** aus. Installieren Sie das `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-Paket. Bei der Installation des NuGet-Pakets für die benutzerdefinierte Suche werden gleichzeitig die folgenden Assemblys installiert:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Web-App für die benutzerdefinierte Suche](../../tutorials/custom-search-web-page.md)