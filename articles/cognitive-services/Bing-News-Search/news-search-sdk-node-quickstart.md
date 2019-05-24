---
title: 'Schnellstart: Durchführen einer Neuigkeitensuche – Bing-News-Suche-SDK für Node.js'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Bing-News-Suche-SDK für Node.js nach Nachrichten zu suchen und die Antwort zu verarbeiten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf13fe5279be9606197abda624c33dbc4f233b34
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798810"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Schnellstart: Durchführen einer Neuigkeitensuche mit dem Bing-News-Suche-SDK für Node.js

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des SDK für die Bing-News-Suche für Node.js mit der Suche nach Nachrichten zu beginnen. Die Bing-News-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/)

So richten Sie eine Konsolenanwendung mit dem SDK für die Bing-News-Suche ein:
1. Führen Sie `npm install ms-rest-azure` in Ihrer Entwicklungsumgebung aus.
2. Führen Sie `npm install azure-cognitiveservices-newssearch` in Ihrer Entwicklungsumgebung aus.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine Instanz der `CognitiveServicesCredentials`. Erstellen Sie Variablen für Ihren Abonnementschlüssel und einen Suchbegriff.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Instanziieren Sie den Client:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Senden einer Suchabfrage

1. Verwenden Sie den Client zur Suche mit einem Abfrageausdruck, in diesem Fall „Winter Olympics“:
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Der Code gibt `result.value` Elemente an der Konsole aus, ohne Text zu analysieren. Die Ergebnisse pro Kategorie (sofern vorhanden) schließen Folgendes ein:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](tutorial-bing-news-search-single-page-app.md)
