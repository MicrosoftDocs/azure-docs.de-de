---
title: 'Schnellstart: Suchen nach Videos mithilfe der REST-API und C# – Bing-Videosuche'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden von Videosuchanforderungen an die Bing-Videosuche-REST-API mit C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 10/22/2020
ms.author: clschott
ms.custom: devx-track-csharp
ms.openlocfilehash: 24e172edd47bd859e530536671b5e8df7e0d4c8f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352557"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Schnellstart: Suchen nach Videos mithilfe der Bing-Videosuche-REST-API und C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Videosuche-API zum ersten Mal aufzurufen. Mit dieser einfachen C#-Anwendung wird eine HTTP-Videosuchabfrage an die API gesendet und die JSON-Antwort angezeigt. Die Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Der Quellcode dieses Beispiels ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) mit zusätzlichen Fehlerbehandlungen, Funktionen und Codehinweisen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen Ihren Computer zur Ausführung von .NET Core einrichten. Die Installationsanweisungen finden Sie auf der Seite [.NET Core-Downloads](https://dotnet.microsoft.com/download). Sie können diese Anwendung unter Windows, Linux, macOS oder in einem Docker-Container ausführen. Sie müssen Ihren bevorzugten Code-Editor installieren. In den folgenden Beschreibungen wird [Visual Studio Code](https://code.visualstudio.com/) verwendet. Hierbei handelt es sich um einen plattformübergreifenden Open Source-Editor. Sie können jedoch auch ein beliebiges anderes Tool verwenden, mit dem Sie vertraut sind.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

Im ersten Schritt wird eine neue Anwendung erstellt. Öffnen Sie eine Eingabeaufforderung, und erstellen Sie ein neues Verzeichnis für Ihre Anwendung. Legen Sie das Verzeichnis als aktuelles Verzeichnis fest. Geben Sie den folgenden Befehl in ein Konsolenfenster ein:

```dotnetcli
dotnet new console --name VideoSearchClient
```

Sie müssen oben in Ihrer Main-Methode die folgende `using`-Anweisung hinzufügen, damit der C#-Compiler den Task und die JSON-Typen erkennt:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
```

Fügen Sie Variablen für Ihren Abonnementschlüssel, den Endpunkt und den Suchbegriff hinzu. Für den `uriBase`-Wert können Sie den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

```csharp
// Replace the accessKey string value with your valid access key.
const string _accessKey = "enter your key here";

// Or use the custom subdomain endpoint displayed in the Azure portal for your resource.
const string _uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";

const string _searchTerm = "kittens";
```

Aktualisieren Sie als Nächstes die Main-Methode, damit wir Async-Methoden verwenden können. Fügen Sie den Async-Modifizierer hinzu, und ändern Sie den Rückgabetyp in „Task“.

```csharp
static async Task Main(string[] args)
{
    
}
```

Nun verfügen Sie über ein Programm, mit dem keine Aktion ausgeführt wird, aber das eine asynchrone Vorgehensweise aufweist. Es gibt aber noch Raum zur Verbesserung.

## <a name="create-a-data-structure-to-hold-the-bing-video-search-api-response"></a>Erstellen einer Datenstruktur für die Antwort der Bing-Videosuche-API

Definieren Sie das Element `SearchResult` und eine `Video`-Klasse für die Ergebnisse der Videosuche. Sie können weitere Eigenschaften später hinzufügen, wenn Sie andere Felder aus dem JSON-Ergebnis benötigen.

```cscharp
class SearchResult
{
    [JsonPropertyName("totalEstimatedMatches")]
    public int TotalEstimatedMatches { get; set; }

    [JsonPropertyName("value")]
    public List<Video> Videos { get; set; }
}

class Video
{
    [JsonPropertyName("name")]
    public string Name { get; set; }

    [JsonPropertyName("description")]
    public string Description { get; set; }

    [JsonPropertyName("thumbnailUrl")]
    public string ThumbnailUrl { get; set; }

    [JsonPropertyName("contentUrl")]
    public string ContentUrl { get; set; }
}
```

## <a name="create-and-handle-a-video-search-request"></a>Erstellen und Verarbeiten einer Videosuchanforderung

Wir verwenden `HttpClient`, um den Aufruf der API durchzuführen. Zunächst müssen wir den Header `Ocp-Apim-Subscription-Key` und Ihren Zugriffsschlüssel hinzufügen. 

```csharp
using var client = new HttpClient();
client.BaseAddress = new Uri(_uriBase);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", _accessKey);
```

Erstellen Sie den URI für die Suchanforderung. Formatieren Sie den Suchbegriff `_searchTerm`, bevor Sie ihn an die Zeichenfolge anhängen.

```csharp
var response = await client.GetAsync($"?q={Uri.EscapeDataString(_searchTerm)}");
```

## <a name="process-the-result"></a>Verarbeiten des Ergebnisses

Wenn die Antwort erfolgreich war, können wir die JSON-Daten verarbeiten. Wir deserialisieren die JSON-Zeichenfolge in unser weiter oben erstelltes `SearchResult`-Element. Richten Sie eine Schleife zum Ergebnis ein (falls vorhanden), und geben Sie das Ergebnis in der Konsole aus.

```csharp
if (response.IsSuccessStatusCode)
{
    var json = await response.Content.ReadAsStringAsync();
    var result = JsonSerializer.Deserialize<SearchResult>(json);

    foreach (var video in result.Videos)
    {
        Console.WriteLine($"Name: {video.Name}");
        Console.WriteLine($"ContentUrl: {video.ContentUrl}");
        Console.WriteLine();
    }
}
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