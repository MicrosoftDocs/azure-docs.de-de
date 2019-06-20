---
title: 'Schnellstart: Suchen nach Videos mithilfe der Bing-Videosuche-REST-API und C#'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden von Videosuchanforderungen an die Bing-Videosuche-REST-API mit C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: ea265ab876dffbb4140d8bbc48123eceaadfe34d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798447"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Schnellstart: Suchen nach Videos mithilfe der Bing-Videosuche-REST-API und C#

Mit diesem Schnellstart können Sie die Bing-Videosuche-API zum ersten Mal aufrufen und ein Suchergebnis in der JSON-Antwort anzeigen. Diese einfache C#-Anwendung sendet eine HTTP-Videosuchabfrage an die API und zeigt die Antwort an. Diese Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Der Quellcode dieses Beispiels ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) mit zusätzlichen Fehlerbehandlungen, Funktionen und Codehinweisen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/).
* Das [Json.NET](https://www.newtonsoft.com/json)-Framework, das als NuGet-Paket verfügbar ist
* Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie eine neue Konsolenprojektmappe in Visual Studio. Fügen Sie dann die folgenden Namespaces in die Hauptcodedatei ein.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Fügen Sie Variablen für Ihren Abonnementschlüssel, den Endpunkt und den Suchbegriff hinzu.

    ```csharp
    const string accessKey = "enter your key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    const string searchTerm = "kittens";
    ```

### <a name="create-a-struct-to-format-the-bing-video-search-api-response"></a>Erstellen einer Struktur zum Formatieren der Antwort der Bing-Videosuche-API

1. Definieren Sie eine `SearchResult`-Struktur, die die Bildersuchergebnisse und JSON-Headerinformationen enthält.

    ```csharp
    struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }
    ```

## <a name="create-and-handle-a-video-search-request"></a>Erstellen und Verarbeiten einer Videosuchanforderung

Erstellen Sie eine Methode mit der Bezeichnung `BingVideoSearch`, um die API aufzurufen, und legen Sie den Rückgabetyp auf die zuvor erstellte `SearchResult`-Struktur fest. Führen Sie in der Methode die folgenden Schritte aus:

1. Erstellen Sie den URI für die Suchanforderung. Beachten Sie, dass der Suchbegriff „toSearch“ formatiert werden muss, bevor er der Zeichenfolge angefügt wird.

    ```csharp
    
    static SearchResult BingVideoSearch(string toSearch){
    
        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Führen Sie die Webanforderung aus, indem Sie Ihren Schlüssel dem Header `Ocp-Acpim-Subscription-Key` hinzufügen und ein `HttpWebResponse`-Objekt zum Speichern der API-Antwort verwenden. Verwenden Sie dann ein `StreamReader`-Element zum Abrufen der JSON-Zeichenfolge.

    ```csharp
    //...
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    //...
    ```

## <a name="process-the-result"></a>Verarbeiten des Ergebnisses

1. Erstellen Sie das Suchergebnisobjekt, und extrahieren Sie die Bing-HTTP-Header. Geben Sie dann das `searchResult`-Objekt zurück. 

    ```csharp
    var searchResult = new SearchResult();
    searchResult.jsonResult = json;
    searchResult.relevantHeaders = new Dictionary<String, String>();

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

2. Anschließend können Sie die Antwort ausgeben.

    ```csharp
    Console.WriteLine(result.jsonResult);
    ```

## <a name="example-json-response"></a>JSON-Beispielantwort 

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen 

 [Worum handelt es sich bei der Bing-Videosuche-API?](../overview.md)
