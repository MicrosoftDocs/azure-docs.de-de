---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit dem SDK für die Bing-Rechtschreibprüfung für C#'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API zum Überprüfen von Rechtschreibung und Grammatik
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f39dbd22372342786eacbccd31a10fc927c0892
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352625"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Schnellstart: Überprüfen der Rechtschreibung mit dem SDK für die Bing-Rechtschreibprüfung für C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um mit dem SDK für die Bing-Rechtschreibprüfung für C# mit der Rechtschreibprüfung zu beginnen. Die Bing-Rechtschreibprüfung verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Anwendungsabhängigkeiten

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/).
* [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) für Bing-Rechtschreibprüfung

Wählen Sie in Visual Studio im **Projektmappen-Explorer** die Option **NuGet-Pakete verwalten** aus, um Ihrem Projekt das SDK für die Bing-Rechtschreibprüfung hinzuzufügen. Fügen Sie das Paket `Microsoft.Azure.CognitiveServices.Language.SpellCheck` hinzu. Mit dem Paket werden auch die folgenden Abhängigkeiten installiert:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Visual Studio eine neue C#-Konsolenprojektmappe. Fügen Sie anschließend die folgende `using`-Anweisung hinzu.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Erstellen Sie eine neue Klasse. Erstellen Sie dann eine asynchrone Funktion mit dem Namen `SpellCheckCorrection()`, für die ein Abonnementschlüssel verwendet und die Rechtschreibprüfungsanforderung gesendet wird.

3. Instanziieren Sie den Client, indem Sie ein neues `ApiKeyServiceClientCredentials`-Objekt erstellen. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Senden der Anforderung und Lesen der Antwort

1. Führen Sie in der oben erstellten Funktion die folgenden Schritte aus. Senden Sie die Rechtschreibprüfungsanforderung mit dem Client. Fügen Sie den zu überprüfenden Text dem Parameter `text` hinzu, und legen Sie den Modus auf `proof` fest.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Rufen Sie, falls vorhanden, das erste Rechtschreibprüfungsergebnis ab. Geben Sie das erste zurückgegebene fehlerhafte Wort (Token), den Tokentyp und die Anzahl von Vorschlägen aus.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Rufen Sie die erste vorgeschlagene Korrektur ab, sofern vorhanden. Geben Sie die Bewertungspunktzahl des Vorschlags und das erste vorgeschlagene Wort aus. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Erstellen Sie Ihr Projekt, und führen Sie es aus. Wenn Sie Visual Studio verwenden, drücken Sie **F5**, um die Datei zu debuggen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](overview.md)
- [Referenzhandbuch zur C#-SDK-Bing-Rechtschreibprüfung](/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)