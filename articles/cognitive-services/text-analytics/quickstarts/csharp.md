---
title: 'Schnellstart: Aufrufen des Textanalysediensts mithilfe des Azure SDK für .NET und C#'
titleSuffix: Azure Cognitive Services
description: Informationen und Codebeispiele für den Einstieg in die Verwendung des Textanalysediensts mit C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: assafi
ms.openlocfilehash: a8b41f6853e9c91e64de903960b880e44f22ed55
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297797"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Schnellstart: Verwenden des .NET SDK mit C# für das Aufrufen des Textanalysediensts
<a name="HOLTop"></a>

Dieser Schnellstart unterstützt Sie bei den ersten Schritten mit dem Azure SDK für .NET und C# für die Sprachanalyse. Obwohl die [Textanalyse](//go.microsoft.com/fwlink/?LinkID=759711)-REST-API mit den meisten Programmiersprachen kompatibel ist, bietet das SDK eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren.

> [!NOTE]
> Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Technische Informationen finden Sie im SDK für .NET unter der [Referenz für die Textanalyse](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher]
* Textanalyse-[SDK für .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Sie benötigen außerdem den [Endpunkt und den Zugriffsschlüssel](../How-tos/text-analytics-how-to-access-key.md), die bei der Registrierung für Sie generiert wurden.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Erstellen der Visual Studio-Projektmappe und Installieren des SDK

1. Erstellen Sie ein neues Projekt vom Typ Konsolen-App (.NET Core). [Öffnen Sie Visual Studio.](https://visualstudio.microsoft.com/vs/)
1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **NuGet-Pakete für Projektmappe verwalten** aus.
1. Wählen Sie die Registerkarte **Durchsuchen** aus. Suchen Sie nach **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Authentifizieren Ihrer Anmeldeinformationen

1. Fügen Sie der Hauptklassendatei (standardmäßig „Program.cs“) die folgenden `using`-Anweisungen hinzu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Erstellen Sie eine neue `ApiKeyServiceClientCredentials`-Klasse, um die Anmeldeinformationen zu speichern und sie jeder Anforderung hinzuzufügen.

    ```csharp
    /// <summary>
    /// Allows authentication to the API by using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Aktualisieren Sie die `Program`-Klasse. Fügen Sie ein Konstantenelement für Ihren Textanalyse-Abonnementschlüssel und ein weiteres für den Dienstendpunkt hinzu. Denken Sie daran, die richtige Azure-Region für Ihr Textanalyse-Abonnement zu verwenden.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Für eine sichere Bereitstellung von Geheimnissen in Produktionssystemen empfehlen wir die Verwendung von [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Erstellen eines Textanalyseclients

Rufen Sie in der `Main`-Funktion des Projekts die gewünschte Beispielmethode auf. Übergeben Sie die Parameter `Endpoint` und `SubscriptionKey`, die Sie definiert haben.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

In den folgenden Abschnitten wird das Aufrufen der einzelnen Dienstfunktionen beschrieben.

## <a name="perform-sentiment-analysis"></a>Ausführen von Standpunktanalysen

1. Erstellen Sie eine neue `SentimentAnalysisExample()`-Funktion, die den zuvor erstellten Client annimmt.
2. Erstellen Sie eine Liste mit `MultiLanguageInput`-Objekten, die die zu analysierenden Dokumente enthält.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. Rufen Sie in der gleichen Funktion `client.SentimentAsync()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse. Geben Sie die ID und die Stimmungsbewertung für jedes Dokument aus. Eine gegen 0 tendierende Punktzahl deutet auf eine negative Stimmung hin, eine gegen 1 tendierende Punktzahl auf eine positive.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="perform-language-detection"></a>Ausführen einer Sprachenerkennung

1. Erstellen Sie eine neue `DetectLanguageExample()`-Funktion, die den zuvor erstellten Client annimmt.
2. Erstellen Sie eine Liste mit `LanguageInput`-Objekten, die Ihre Dokumente enthält.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. Rufen Sie in der gleichen Funktion `client.DetectLanguageAsync()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse. Geben Sie die ID und die erste zurückgegebene Sprache für jedes Dokument aus.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="perform-entity-recognition"></a>Ausführen von Entitätserkennungen

1. Erstellen Sie eine neue `RecognizeEntitiesExample()`-Funktion, die den zuvor erstellten Client annimmt.
2. Erstellen Sie eine Liste mit `MultiLanguageBatchInput`-Objekten, die Ihre Dokumente enthält.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. Rufen Sie in der gleichen Funktion `client.EntitiesAsync()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse. Geben Sie die ID jedes Dokuments aus. Geben Sie für jede erkannte Entität ihren Wikipedia-Namen, den Typ und die Untertypen (falls vorhanden) sowie die Fundorte im Originaltext aus.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="perform-key-phrase-extraction"></a>Ausführen von Schlüsselwortextraktionen

1. Erstellen Sie eine neue `KeyPhraseExtractionExample()`-Funktion, die den zuvor erstellten Client annimmt.
2. Erstellen Sie eine Liste mit `MultiLanguageBatchInput`-Objekten, die Ihre Dokumente enthält.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. Rufen Sie in der gleichen Funktion `client.KeyPhrasesAsync()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse. Geben Sie die ID und alle erkannten Schlüsselausdrücke jedes Dokuments aus.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Textanalyse mit Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Übersicht über die Textanalyse](../overview.md)
* [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)
