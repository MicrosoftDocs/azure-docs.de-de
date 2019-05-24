---
title: 'Schnellstart: Senden einer Suchanforderung mit dem Bing-Entitätssuche-SDK für Node.js'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-Entitätssuche-SDK für Node.js nach Entitäten zu suchen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9c178b8278dd7854e4f79fbfae1bafc117a1970e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813659"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Schnellstart: Senden einer Suchanforderung mit dem Bing-Entitätssuche-SDK für Node.js

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-Entitätssuche-SDK für Node.js mit der Suche nach Entitäten zu beginnen. Die Bing-Entitätssuche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Voraussetzungen

* Die aktuelle Version von [Node.js](https://nodejs.org/en/download/)

* Das [Bing-Entitätssuche-SDK für Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Installation des Bing-Entitätssuche-SDK:

1. Führen Sie `npm install ms-rest-azure` in Ihrer Entwicklungsumgebung aus.
2. Führen Sie `npm install azure-cognitiveservices-entitysearch` in Ihrer Entwicklungsumgebung aus.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und fügen Sie die folgenden Anforderungen hinzu. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Erstellen Sie mithilfe Ihres Abonnementschlüssels eine Instanz von `CognitiveServicesCredentials`. Dann erstellen Sie damit eine Instanz des Suchclients.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Senden einer Anforderung und Erhalten einer Antwort

1. Senden Sie mit `entitiesOperations.search()` eine Suchanforderung für Entitäten. Nach dem Empfang einer Antwort geben Sie das `queryContext`-Element, die Anzahl der zurückgegebenen Ergebnisse, und die Beschreibung des ersten Ergebnisses aus.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](../tutorial-bing-entities-search-single-page-app.md)

* [Was ist die Bing-Entitätssuche-API?](../overview.md )