---
title: 'Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit C# | Microsoft-Dokumentation'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche in C# zu beginnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b892194a0e716aa3de218bc6edb6c38cdc898935
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338655"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit C# 

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um zu lernen, wie Sie Suchergebnisse von Ihrer Instanz der benutzerdefinierten Bing-Suche anfordern. Diese Anwendung ist zwar in C# geschrieben, aber die API für die benutzerdefinierte Bing-Suche ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Instanz der benutzerdefinierten Bing-Suche. Weitere Informationen finden Sie unter [Quickstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](quick-start.md).
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core)
- Eine beliebige Edition von [Visual Studio 2019 oder höher](https://www.visualstudio.com/downloads/)
- Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden.
- Das NuGet-Paket für die [benutzerdefinierte Bing-Suche](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0). 

   So installieren Sie das Paket in Visual Studio: 
     1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. 
     2. Suchen Sie nach *Microsoft.Azure.CognitiveServices.Search.CustomSearch*, wählen Sie das Paket aus, und installieren Sie es.

   Wenn Sie das NuGet-Paket für die benutzerdefinierte Bing-Suche installieren, werden von Visual Studio außerdem die folgenden Pakete installiert:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Erstellen Sie die folgenden Klassen, um die Suchergebnisse zu speichern, die von der API für die benutzerdefinierte Bing-Suche zurückgegeben werden:

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

3. Erstellen Sie in der main-Methode Ihres Projekts die folgenden Variablen für Ihren Abonnementschlüssel der API für die benutzerdefinierte Bing-Suche, die benutzerdefinierte Konfigurations-ID Ihrer Suchinstanz und einen Suchbegriff:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Erstellen Sie die Anforderungs-URL, indem Sie Ihren Suchbegriff an den Abfrageparameter `q=` und Ihre benutzerdefinierte Konfigurations-ID der Suchinstanz an den Parameter `customconfig=` anfügen. Trennen Sie die Parameter durch ein kaufmännisches Und-Zeichen (`&`) voneinander. Für den Wert der Variable `url` können Sie den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

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

- Durchlaufen Sie das Antwortobjekt, um Informationen zu den einzelnen Suchergebnissen anzuzeigen, z.B. Name, URL und das Datum des letzten Crawl-Vorgangs der Webseite.

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