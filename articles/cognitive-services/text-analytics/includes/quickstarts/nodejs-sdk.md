---
title: 'Schnellstart: Textanalyse-Clientbibliothek v3 für Node.js | Microsoft-Dokumentation'
description: Hier finden Sie Informationen zu den ersten Schritten mit der Textanalyse-Clientbibliothek v3 für Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 12/11/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 69a7e63a5dcd892c1085367bd9747ffae9a835bf
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97366381"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

[v3-Referenzdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme) | [Quellcode der v3-Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-Paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[v3-Referenzdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme) | [Quellcode der v3-Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-Paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Version 2.1](#tab/version-2)

[v2-Referenzdokumentation](/javascript/api/@azure/cognitiveservices-textanalytics) | [Quellcode der v2-Bibliothek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2-Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-Beispiele](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org/).
* Sobald Sie über Ihr Azure-Abonnement verfügen, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyseressource"  target="_blank"> erstellen Sie eine Textanalyseressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Textanalyse-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um das Analyze-Feature nutzen zu können.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp 

cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

Installieren Sie die NPM-Pakete vom Typ `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Installieren Sie die NPM-Pakete vom Typ `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installieren Sie die NPM-Pakete vom Typ `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 

---

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.
Erstellen Sie eine Datei namens `index.js`, und fügen Sie Folgendes hinzu:

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektmodell

Der Textanalyseclient ist ein Objekt vom Typ `TextAnalyticsClient`, das sich bei Azure mit Ihrem Schlüssel authentifiziert. Der Client verfügt über verschiedene Methoden zum Analysieren von Text als einzelne Zeichenfolge oder als Batch.

Text wird als `documents`-Liste an die API gesendet. Hierbei handelt es sich um `dictionary`-Objekte, die je nach verwendeter Methode eine Kombination aus den Attributen `id`, `text` und `language` enthalten. Das `text`-Attribut speichert den zu analysierenden Text in der Ausgangssprache (`language`), und `id` kann ein beliebiger Wert sein. 

Das Antwortobjekt ist eine Liste mit den Analyseinformationen für jedes Dokument. 

## <a name="code-examples"></a>Codebeispiele

* [Clientauthentifizierung](#client-authentication)
* [Standpunktanalyse](#sentiment-analysis) 
* [Opinion Mining](#opinion-mining)
* [Sprachenerkennung](#language-detection)
* [Erkennung benannter Entitäten](#named-entity-recognition-ner)
* [Entitätsverknüpfung](#entity-linking)
* Personenbezogene Informationen
* [Schlüsselbegriffserkennung](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientauthentifizierung

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

Erstellen Sie ein neues Objekt vom Typ `TextAnalyticsClient` mit Ihrem Schlüssel und Ihrem Endpunkt als Parameter.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie ein neues Objekt vom Typ `TextAnalyticsClient` mit Ihrem Schlüssel und Ihrem Endpunkt als Parameter.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Erstellen Sie ein [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient)-Objekt mit `credentials` und `endpoint` als Parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Stimmungsanalyse

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `analyzeSentiment()` des Clients auf, und rufen Sie das zurückgegebene Objekt `SentimentBatchResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Sie die ID des jeweiligen Dokuments sowie die Stimmung auf Dokumentebene zusammen mit Zuverlässigkeitsbewertungen aus. Das Ergebnis enthält die Stimmung auf Satzebene sowie Offsets, Länge und Zuverlässigkeitsbewertungen für jedes Dokument.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Opinion Mining

Zum Durchführen von Stimmungsanalyse mit Opinion Mining erstellen Sie ein Array aus Zeichenfolgen, die das zu analysierende Dokument enthalten. Rufen Sie die Methode `analyzeSentiment()` des Clients mit hinzugefügtem Optionsflag `includeOpinionMining: true` auf, und rufen Sie das zurückgegebene `SentimentBatchResult`-Objekt ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Sie die ID des jeweiligen Dokuments sowie die Stimmung auf Dokumentebene zusammen mit Zuverlässigkeitsbewertungen aus. Für jedes Dokument enthält das Ergebnis nicht nur die Stimmung auf Satzebene wie oben sondern auch die Stimmung auf Ansichts- und Meinungsebene.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `analyzeSentiment()` des Clients auf, und rufen Sie das zurückgegebene Objekt `SentimentBatchResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Sie die ID des jeweiligen Dokuments sowie die Stimmung auf Dokumentebene zusammen mit Zuverlässigkeitsbewertungen aus. Das Ergebnis enthält die Stimmung auf Satzebene sowie Offsets, Länge und Zuverlässigkeitsbewertungen für jedes Dokument.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Erstellen Sie eine Liste von Wörterbuchobjekten, die die Dokumente enthalten, die Sie analysieren möchten. Rufen Sie die [Sentiment()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-)-Methode des Clients auf, und rufen Sie das zurückgegebene [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) ab. Durchlaufen Sie die Liste der Ergebnisse, und drucken Sie die ID und den Stimmungswert jedes Dokuments. Eine gegen 0 tendierende Punktzahl deutet auf eine negative Stimmung hin, eine gegen 1 tendierende Punktzahl auf eine positive.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Spracherkennung

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `detectLanguage()` des Clients auf, und rufen Sie das zurückgegebene Ergebnis (`DetectLanguageResultCollection`) ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID sowie die entsprechende primäre Sprache aus.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `detectLanguage()` des Clients auf, und rufen Sie das zurückgegebene Ergebnis (`DetectLanguageResultCollection`) ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID sowie die entsprechende primäre Sprache aus.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Erstellen Sie eine Liste mit Wörterbuchobjekten, die Ihre Dokumente enthalten. Rufen Sie die [detectLanguage()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-)-Methode des Clients auf, und rufen Sie das zurückgegebene [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die Sprache aus.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

> [!NOTE]
> In Version `3.1`:
> * Die Entitätsverknüpfung ist eine separate Anforderung.

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, Typ, Untertyp, Offset, Länge und Bewertung aus.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entitätsverknüpfung

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeLinkedEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeLinkedEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, ID, Datenquelle, URL und Treffer aus. Jedes Objekt im Array `matches` enthält Offset, Länge und Bewertung für den Treffer.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Erkennung von personenbezogenen Informationen (Personally Identifying Information, PII)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizePiiEntities()` des Clients auf, und rufen Sie das Objekt `RecognizePIIEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, Typ und Bewertung aus.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> In Version `3.0`:
> * Die Entitätsverknüpfung ist eine separate Anforderung.

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, Typ, Untertyp, Offset, Länge und Bewertung aus.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entitätsverknüpfung

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeLinkedEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeLinkedEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, ID, Datenquelle, URL und Treffer aus. Jedes Objekt im Array `matches` enthält Offset, Länge und Bewertung für den Treffer.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> In Version 2.1 ist die Entitätsverknüpfung in der NER-Antwort enthalten.

Erstellen Sie eine Liste mit Objekten, die Ihre Dokumente enthält. Rufen Sie die [entities()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-)-Methode des Clients auf, und rufen Sie das [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult)-Objekt ab. Durchlaufen Sie die Liste der Ergebnisse, und geben Sie die ID jedes Dokuments aus. Drucken Sie für jede erkannte Entität ihren Wikipedia-Namen, den Typ und Untertypen (falls vorhanden) sowie die Fundstellen im Originaltext.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `extractKeyPhrases()` des Clients auf, und rufen Sie das zurückgegebene Objekt `ExtractKeyPhrasesResult` ab. Durchlaufen Sie die Ergebnisse, und geben Sie die ID und alle erkannten Schlüsselbegriffe aus.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `extractKeyPhrases()` des Clients auf, und rufen Sie das zurückgegebene Objekt `ExtractKeyPhrasesResult` ab. Durchlaufen Sie die Ergebnisse, und geben Sie die ID und alle erkannten Schlüsselbegriffe aus.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Erstellen Sie eine Liste mit Objekten, die Ihre Dokumente enthält. Rufen Sie die [keyPhrases()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-)-Methode des Clients auf, und rufen Sie das zurückgegebene [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult)-Objekt ab. Durchlaufen Sie die Ergebnisse, und geben Sie die ID und alle erkannten Schlüsselbegriffe aus.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchrones Verwenden der API mit dem Analyze-Vorgang

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)

> [!CAUTION]
> Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um Analyze-Vorgänge durchführen zu können.  

Erstellen Sie die neue Funktion `analyze_example()`, die die Funktion `beginAnalyze()` aufruft. Daraus resultiert ein zeitintensiver Vorgang, aus dem Ergebnisse abgerufen werden.

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Ausgabe

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Sie können den Analyze-Vorgang auch verwenden, um personenbezogene Informationen zu erkennen und Schlüsselbegriffe zu extrahieren. Die Analyze-Beispiele für [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/javascript/beginAnalyze.js) und [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src/beginAnalyze.ts) finden Sie auf GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

# <a name="version-21"></a>[Version 2.1](#tab/version-2)

Dieses Feature ist in Version 2.1 nicht verfügbar.

---

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```
