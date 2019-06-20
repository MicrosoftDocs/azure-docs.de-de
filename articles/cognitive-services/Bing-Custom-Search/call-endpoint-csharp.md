---
title: 'Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit C# | Microsoft-Dokumentation'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche in C# zu beginnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 267b50e15d39fc5a0df763cea2e2b79f9b23d151
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595808"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit C# 

Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche zu beginnen. Diese Anwendung ist zwar in C# geschrieben, aber die API für die benutzerdefinierte Bing-Suche ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

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

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Erstellen Sie die folgenden Klassen, um die Suchergebnisse zu speichern, die von der API für die benutzerdefinierte Bing-Suche zurückgegeben werden.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. Erstellen Sie in der main-Methode Ihres Projekts Variablen für Ihren Abonnementschlüssel der API für die benutzerdefinierte Bing-Suche, die benutzerdefinierte Konfigurations-ID Ihrer Suchinstanz und einen Suchbegriff.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Erstellen Sie die Anforderungs-URL, indem Sie Ihren Suchbegriff an den Abfrageparameter `q=` und Ihre benutzerdefinierte Konfigurations-ID der Suchinstanz an `customconfig=` anfügen. Trennen Sie die Parameter mit dem Zeichen `&` voneinander. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Senden und Empfangen einer Suchanforderung 

1. Erstellen Sie einen Anforderungsclient, und fügen Sie Ihren Abonnementschlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Führen Sie die Suchanforderung aus, und rufen Sie die Antwort als JSON-Objekt ab.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Lassen Sie die Ergebnisse verarbeiten und anzeigen.

1. Durchlaufen Sie das Antwortobjekt, um Informationen zu den einzelnen Suchergebnissen anzuzeigen, z.B. Name, URL und das Datum des letzten Crawl-Vorgangs der Webseite.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Web-App für die benutzerdefinierte Suche](./tutorials/custom-search-web-page.md)
