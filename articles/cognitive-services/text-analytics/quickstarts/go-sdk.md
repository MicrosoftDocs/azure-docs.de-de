---
title: 'Schnellstart: Aufrufen des Textanalysediensts mit dem Go SDK'
titleSuffix: Azure Cognitive Services
description: Informationen und Codebeispiele für die ersten Schritte mit der Textanalyse-API in Microsoft Cognitive Services
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/23/2019
ms.author: aahi
ms.openlocfilehash: 44def29292bc882fdaa08ff76667742756f178b8
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299449"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-go-sdk"></a>Schnellstart: Aufrufen des Textanalysediensts mit dem Go SDK 
<a name="HOLTop"></a>

Verwenden Sie diese Schnellstartanleitung, um mit dem Textanalyse-SDK für Go in die Sprachanalyse einzusteigen. Dieser Artikel veranschaulicht, wie Sie Sprache erkennen, Stimmungen analysieren, Schlüsselbegriffe extrahieren und verknüpfte Entitäten erkennen. Die REST-API ist zwar mit den meisten Programmiersprachen kompatibel, das SDK bietet aber eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).

## <a name="prerequisites"></a>Voraussetzungen

* Textanalyse-[SDK für Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Außerdem benötigen Sie den [Endpunkt und den Zugriffsschlüssel](../How-tos/text-analytics-how-to-access-key.md) – beide wurden der Registrierung für Sie generiert.

## <a name="set-up-a-new-project"></a>Einrichten eines neuen Projekts

Erstellen Sie ein neues Go-Projekt in Ihrem bevorzugten Code-Editor bzw. Ihrer bevorzugten IDE. Fügen Sie dann der Go-Datei die folgende import-Anweisung hinzu:

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Fügen Sie Ihrem Projekt die folgenden Funktionen hinzu, da der Großteil der Parameter und Eigenschaften für diese Schnellstartanleitung Zeiger vom Typ „string“ und „bool“ erwartet.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

## <a name="create-text-analytics-client-and-authenticate-credentials"></a>Erstellen eines Textanalyseclients und Authentifizieren der Anmeldeinformationen

Erstellen Sie in der main-Funktion Ihres Projekts ein neues `TextAnalytics`-Objekt. Verwenden Sie die richtige Azure-Region für Ihr Textanalyseabonnement. Beispiel: `https://eastus.api.cognitive.microsoft.com`. Wenn Sie einen Schlüssel für die Testversion verwenden, müssen Sie den Speicherort aktualisieren.

```golang
//Replace 'eastus' with the correct region for your Text Analytics subscription
textAnalyticsClient := textanalytics.New("https://eastus.api.cognitive.microsoft.com")
```

Erstellen Sie eine Variable für den Schlüssel, und übergeben Sie sie an die Funktion `autorest.NewCognitiveServicesAuthorizer`, die dann an die Eigenschaft `authorizer` des Clients übergeben wird.

```golang
subscriptionKey := "<<subscriptionKey>>"
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Stimmungsanalyse

Erstellen Sie eine neue Funktion mit dem Namen `SentimentAnalysis()`, die den zuvor erstellten Client aufnimmt. Erstellen Sie eine Liste mit `MultiLanguageInput`-Objekten, die die zu analysierenden Dokumente enthält. Jedes Objekt enthält die Attribute `id`, `Language` und `text`. Das `text`-Attribut speichert den Text, der analysiert werden soll, `language` ist die Sprache des Dokuments, und `id` kann ein beliebiger Wert sein. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("1"),
            Text:StringPointer("This was a waste of my time. The speaker put me to sleep."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("2"),
            Text:StringPointer("No tengo dinero ni nada que dar..."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("it"),
            ID:StringPointer("3"),
            Text:StringPointer("L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Rufen Sie in der gleichen Funktion `textAnalyticsclient.Sentiment()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse, und drucken Sie die ID und den Stimmungswert jedes Dokuments. Eine gegen 0 tendierende Punktzahl deutet auf eine negative Stimmung hin, eine gegen 1 tendierende Punktzahl auf eine positive.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

Rufen Sie in der main-Funktion Ihres Projekts `SentimentAnalysis()` auf.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Spracherkennung

Erstellen Sie eine neue Funktion mit dem Namen `LanguageDetection()`, die den zuvor erstellten Client aufnimmt. Erstellen Sie eine Liste mit `LanguageInput`-Objekten, die die zu analysierenden Dokumente enthält. Jedes Objekt enthält die Attribute `id` und `text`. Das `text`-Attribut speichert den Text, der analysiert werden soll, und die `id` kann ein beliebiger Wert sein. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("1"),
            Text:StringPointer("Este es un document escrito en Español."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("2"),
            Text:StringPointer("这是一个用中文写的文件"),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Rufen Sie in der gleichen Funktion `textAnalyticsclient.DetectLanguage()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die ermittelte Sprache aus.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

Rufen Sie in der main-Funktion Ihres Projekts `LanguageDetection()` auf.

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000,
Document ID: 1 Detected Languages with Score: Spanish 1.000000,
Document ID: 2 Detected Languages with Score: Chinese_Simplified 1.000000,
```

## <a name="entity-recognition"></a>Entitätserkennung

Erstellen Sie eine neue Funktion mit dem Namen `ExtractEntities()`, die den zuvor erstellten Client aufnimmt. Erstellen Sie eine Liste mit `MultiLanguageInput`-Objekten, die die zu analysierenden Dokumente enthält. Jedes Objekt enthält die Attribute `id`, `language` und `text`. Das `text`-Attribut speichert den Text, der analysiert werden soll, `language` ist die Sprache des Dokuments, und `id` kann ein beliebiger Wert sein. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("1"),
            Text:StringPointer("La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Rufen Sie in der gleichen Funktion `call textAnalyticsclient.Entities()` auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die Bewertung der extrahierten Entitäten aus.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

Rufen Sie in der main-Funktion Ihres Projekts `ExtractEntities()` auf.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000

Document ID: 1
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 21  Length: 9   Score: 0.999756
        Name: Redmond (Washington)  Type: Location
            Offset: 60  Length: 7   Score: 0.991128
        Name: 21 kilómetros Type: Quantity  Sub-Type: Dimension

            Offset: 71  Length: 13  Score: 0.800000
        Name: Seattle   Type: Location
            Offset: 88  Length: 7   Score: 0.999878
```

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Erstellen Sie eine neue Funktion mit dem Namen `ExtractKeyPhrases()`, die den zuvor erstellten Client aufnimmt. Erstellen Sie eine Liste mit `MultiLanguageInput`-Objekten, die die zu analysierenden Dokumente enthält. Jedes Objekt enthält die Attribute `id`, `language` und `text`. Das `text`-Attribut speichert den Text, der analysiert werden soll, `language` ist die Sprache des Dokuments, und `id` kann ein beliebiger Wert sein.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("ja"),
            ID:StringPointer("0"),
            Text:StringPointer("猫は幸せ"),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("de"),
            ID:StringPointer("1"),
            Text:StringPointer("Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("2"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("3"),
            Text:StringPointer("A mi me encanta el fútbol!"),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Rufen Sie in der gleichen Funktion „textAnalyticsclient.KeyPhrases()“ auf, und rufen Sie das Ergebnis ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die extrahierten Schlüsselbegriffe aus.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

Rufen Sie in der main-Funktion Ihres Projekts `ExtractKeyPhrases()` auf.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
        幸せ

Document ID: 1
    Extracted Key Phrases:
        Stuttgart
        Hotel
        Fahrt
        Fu

Document ID: 2
    Extracted Key Phrases:
        cat
        veterinarian

Document ID: 3
    Extracted Key Phrases:
        fútbol
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Textanalyse mit Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Weitere Informationen

 [Übersicht über die Textanalyse](../overview.md) [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)
