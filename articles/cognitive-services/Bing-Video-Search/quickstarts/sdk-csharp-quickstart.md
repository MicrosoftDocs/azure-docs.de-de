---
title: 'Schnellstart: Suchen nach Videos mithilfe des Bing-Videosuche-SDK für C#'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um Videosuchanforderungen mit dem Bing-Videosuche-SDK für C# zu senden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: e10d9088f6de3b7a3a638cdbe18f51425d206b7b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798180"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Schnellstart: Ausführen einer Videosuche mit dem Bing-Videosuche-SDK für C#

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-Videosuche-SDK für C# mit der Suche nach Nachrichten zu beginnen. Die Bing-Videosuche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). Er enthält weitere Anmerkungen und Features.

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/).
* Das Json.NET-Framework, das als [NuGet-Paket](https://www.nuget.org/packages/Newtonsoft.Json/) verfügbar ist.

Wählen Sie in Visual Studio im **Projektmappen-Explorer** die Option **NuGet-Pakete verwalten** aus, um Ihrem Projekt das SDK für die Bing-Videosuche hinzuzufügen. Fügen Sie das Paket `Microsoft.Azure.CognitiveServices.Search.VideoSearch` hinzu.

Beim Installieren des [[NuGet-Pakets mit dem SDK für die Videosuche]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) werden auch die folgenden Abhängigkeiten installiert:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Visual Studio eine neue C#-Konsolenprojektmappe. Fügen Sie dann Folgendes in die Hauptcodedatei ein:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instanziieren Sie den Client, indem Sie ein neues `ApiKeyServiceClientCredentials`-Objekt mit Ihrem Abonnementschlüssel erstellen und den Konstruktor aufrufen.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Senden einer Suchanforderung und Verarbeiten der Ergebnisse

1. Verwenden Sie den Client, um eine Suchanforderung zu senden. Verwenden Sie „SwiftKey“ für die Suchabfrage.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Wenn Ergebnisse zurückgegeben wurden, können Sie das erste Ergebnis mit `videoResults.Value[0]` abrufen. Geben Sie anschließend die ID, den Titel und die URL des Videos aus.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Webanwendung](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen 

* [Worum handelt es sich bei der Bing-Videosuche-API?](../overview.md)
* [Cognitive Services SDK-Beispiele für .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
