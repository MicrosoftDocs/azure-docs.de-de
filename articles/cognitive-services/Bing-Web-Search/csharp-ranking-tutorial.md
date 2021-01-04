---
title: Anzeigen von Suchergebnissen nach Rang
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die RankingResponse-Antwort von Bing verwenden, um nach Rang sortierte Suchergebnisse anzuzeigen.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 077c715616e377d8b296e53fdd5a861f944ab940
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349502"
---
# <a name="build-a-console-app-search-client-in-c"></a>Erstellen einer Suchclient-Konsolen-App in C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Tutorial erfahren Sie, wie Sie eine einfache .NET Core-Konsolen-App erstellen, mit der Benutzer die Bing-Websuche-API abfragen und nach Rang sortierte Ergebnisse anzeigen können.

In diesem Tutorial wird Folgendes veranschaulicht:

- Erstellen einer einfachen Abfrage für die Bing-Websuche-API
- Anzeigen von Abfrageergebnissen nach Rang

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Erstellen einer Ressource für die Bing-Suche"  target="_blank"> im Azure-Portal eine Ressource für die Bing-Suche <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
* [Visual Studio-IDE](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-console-app-project"></a>Erstellen eines neuen Konsolen-App-Projekts

Erstellen Sie in Visual Studio durch Drücken von `Ctrl`+`Shift`+`N` ein Projekt.

Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C# > Klassischer Windows-Desktop > Konsolen-App (.NET Framework)** .

Nennen Sie die Anwendung **MyConsoleSearchApp**, und klicken Sie anschließend auf **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Hinzufügen des NuGet-Pakets „JSON.net“ zum Projekt

„JSON.net“ ermöglicht die Arbeit mit den von der API zurückgegebenen JSON-Antworten. Fügen Sie Ihrem Projekt das entsprechende NuGet-Paket hinzu:

- Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten...** aus.
- Suchen Sie auf der Registerkarte **Durchsuchen** nach `Newtonsoft.Json`. Wählen Sie die neueste Version aus, und klicken Sie auf **Installieren**.
- Klicken Sie im Fenster **Änderungen überprüfen** auf die Schaltfläche **OK**.
- Schließen Sie die Visual Studio-Registerkarte **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Hinzufügen eines Verweises auf „System.Web“

Dieses Tutorial basiert auf der Assembly `System.Web`. Fügen Sie Ihrem Projekt einen Verweis auf diese Assembly hinzu:

- Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie anschließend auf **Verweis hinzufügen...** .
- Klicken Sie auf **Assemblys > Framework**, scrollen Sie nach unten, und aktivieren Sie das Kontrollkästchen für **System.Web**
- Klicken Sie auf **OK**.

## <a name="add-some-necessary-using-statements"></a>Hinzufügen einiger erforderlicher using-Anweisungen

Für den Code in diesem Tutorial sind drei zusätzliche using-Anweisungen erforderlich. Fügen Sie unter den vorhandenen Anweisungen vom Typ `using` (am Anfang von **Program.cs**) die folgenden Anweisungen hinzu:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Anfordern einer Benutzerabfrage

Öffnen Sie **Program.cs** im **Projektmappen-Explorer**. Aktualisieren Sie die Methode `Main()`:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Diese Methode:

- Fordert eine Benutzerabfrage an
- Ruft `RunQueryAndDisplayResults(userQuery)` auf, um die Abfrage auszuführen und die Ergebnisse anzuzeigen
- Wartet auf eine Benutzereingabe, um zu verhindern, dass sich das Konsolenfenster sofort wieder schließt

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Suchen nach Abfrageergebnissen unter Verwendung der Bing-Websuche-API

Fügen Sie als Nächstes eine Methode hinzu, die die API abfragt und die Ergebnisse anzeigt:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Diese Methode:

- Erstellt eine `HttpClient`-Instanz zum Abfragen der Websuche-API
- Legt den HTTP-Header `Ocp-Apim-Subscription-Key` fest, der von Bing verwendet wird, um die Anforderung zu authentifizieren
- Führt die Anforderung aus und verwendet „JSON.net“, um die Ergebnisse zu deserialisieren
- Ruft `DisplayAllRankedResults(responseObjects)` auf, um alle Ergebnisse nach Rang sortiert anzuzeigen

Der Wert von `Ocp-Apim-Subscription-Key` muss auf Ihren Abonnementschlüssel festgelegt werden.

## <a name="display-ranked-results"></a>Anzeigen von nach Rang sortierten Ergebnissen

Bevor wir darauf eingehen, wie Sie nach Rang sortierte Ergebnisse anzeigen, sehen wir uns eine exemplarische Antwort der Websuche an:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

Das JSON-Objekt `rankingResponse` ([Dokumentation](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) beschreibt die korrekte Anzeigereihenfolge für Suchergebnisse. Es enthält mindestens eine der folgenden priorisierten Gruppen:

- `pole`: Die Suchergebnisse, die am sichtbarsten sein und beispielsweise über dem Hauptbereich und der Randleiste angezeigt werden sollen.
- `mainline`: Die Suchergebnisse, die in der Hauptlinie angezeigt werden sollen.
- `sidebar`: Die Suchergebnisse, die in der Randleiste angezeigt werden sollen. Ist keine Randleiste vorhanden, werden die Ergebnisse unterhalb des Hauptbereichs angezeigt.

Der JSON-Code der Rangfolgeantwort kann eine oder mehrere der Gruppen enthalten.

Fügen Sie in **Program.cs** die folgende Methode hinzu, um die Ergebnisse in der richtigen Reihenfolge anzuzeigen:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Diese Methode:

- Durchläuft die Gruppen vom Typ `rankingResponse` aus der Antwort
- Ruft `DisplaySpecificResults(...)` auf, um die Elemente in den einzelnen Gruppen anzuzeigen

Fügen Sie in **Program.cs** die beiden folgenden Methoden hinzu:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Diese Methoden geben gemeinsam die Suchergebnisse an die Konsole aus.

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung aus. Die Ausgabe sollte in etwa wie folgt aussehen:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwenden der Rangfolge beim Anzeigen von Ergebnissen finden Sie [hier](rank-results.md).