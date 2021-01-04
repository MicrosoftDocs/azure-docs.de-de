---
title: 'Schnellstart: Senden einer Suchanforderung an die REST-API mithilfe von C# – Bing-Entitätssuche'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden einer Anforderung an die Bing-Entitätssuche-REST-API mit C#, und Sie erhalten eine JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a0a77faba971f328c6ae1c5a03f8faf7ccfcf60d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351485"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Entitätssuche-API zum ersten Mal aufzurufen und die JSON-Antwort anzuzeigen. Diese einfache C#-Anwendung sendet eine Nachrichtensuchabfrage an die API und zeigt die Antwort an. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs) verfügbar.

Die Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.


## <a name="prerequisites"></a>Voraussetzungen

- Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/).
- Falls Sie Linux oder MacOS verwenden, können Sie dieser Schnellstartanleitung folgen, indem Sie [Visual Studio Code](https://code.visualstudio.com/) und [.NET Core](/dotnet/core/install/macos) verwenden.
- [Kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Visual Studio eine neue C#-Konsolenprojektmappe. 
1. Fügen Sie das NuGet-Paket [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) hinzu.
    1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt.
    2. Wählen Sie **NuGet-Pakete verwalten** aus.
    3. Suchen Sie nach *Newtonsoft.Json*, wählen Sie das Paket aus, und installieren Sie es anschließend.
1. Fügen Sie dann die folgenden Namespaces in die Hauptcodedatei ein:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Erstellen Sie eine neue Klasse, und fügen Sie Variablen für den API-Endpunkt, Ihren Abonnementschlüssel und die Abfrage hinzu, die Sie durchsuchen möchten. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Senden einer Anforderung und Erhalten der API-Antwort

1. Erstellen Sie innerhalb der Klasse eine Funktion mit dem Namen `Search()`. In dieser Funktion erstellen Sie ein neues `HttpClient`-Objekt und fügen dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu.

2. Erstellen Sie den URI für die Anforderung, indem Sie den Host und Pfad kombinieren. Fügen Sie dann Ihren Markt hinzu, und führen Sie die URL-Codierung für Ihre Abfrage durch.

3. Warten Sie, bis `client.GetAsync()` eine HTTP-Antwort erhält, und speichern Sie die JSON-Antwort, indem Sie auf `ReadAsStringAsync()` warten.

4. Formatieren Sie die JSON-Zeichenfolge mit `JsonConvert.DeserializeObject()`, und geben Sie sie in der Konsole aus.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. Rufen Sie in der `Main()`-Methode Ihrer Anwendung die `Search()`-Funktion auf.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](../tutorial-bing-entities-search-single-page-app.md)

* [Was ist die Bing-Entitätssuche-API?](../overview.md )
* [Referenz zur Bing-Entitätssuche-API](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).