---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit dem SDK für die Bing-Rechtschreibprüfung für C#'
titlesuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API zum Überprüfen der Rechtschreibung und Grammatik
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: adbb60c7ddbc72b8b7e5cb31c6909117ce3a10cb
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798358"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Schnellstart: Überprüfen der Rechtschreibung mit dem SDK für die Bing-Rechtschreibprüfung für C#

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
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Rufen Sie, falls vorhanden, die erste vorgeschlagene Korrektur ab. Geben Sie die Bewertungspunktzahl des Vorschlags und das erste vorgeschlagene Wort aus. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)