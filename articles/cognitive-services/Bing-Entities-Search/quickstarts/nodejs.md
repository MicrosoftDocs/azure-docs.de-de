---
title: 'Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von Node.js'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden einer Anforderung an die Bing-Entitätssuche-REST-API mit C#, und Sie erhalten eine JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 177e0ef0adab3b683657a9b872300cb38185d795
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384569"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von Node.js

Verwenden Sie diese Schnellstartanleitung, um die Bing-Entitätssuche-API zum ersten Mal aufzurufen und die JSON-Antwort anzuzeigen. Diese einfache JavaScript-Anwendung sendet eine Nachrichtensuchabfrage an die API und zeigt die Antwort an. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Diese Anwendung ist zwar in JavaScript geschrieben, aber die API ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

* Die aktuelle Version von [Node.js](https://nodejs.org/en/download/)

* Die [JavaScript-Bibliothek für Anforderungen](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und legen Sie die Genauigkeit sowie die HTTPS-Anforderungen fest.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Erstellen Sie Variablen für den API-Endpunkt, Ihren Abonnementschlüssel und eine Suchabfrage.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Fügen Sie die Markt- und Abfrageparameter zu einer Zeichenfolge namens `query` hinzu. Fügen Sie Ihrer Abfrage unbedingt eine URL-Codierung vom Typ `encodeURI()` hinzu.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Verarbeiten und Analysieren der Antwort

1. Definieren Sie eine Funktion mit der Bezeichnung `response_handler`, die einen HTTP-Aufruf (`response`) als Parameter verwendet. Führen Sie innerhalb dieser Funktion die folgenden Schritte aus:

    1. Definieren Sie eine Variable, die den Text der JSON-Antwort enthält.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Speichern Sie den Text der Antwort, wenn das Flag **data** aufgerufen wird.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Wird ein Flag vom Typ **end** angezeigt wird, analysieren Sie den JSON-Code, und zeigen Sie ihn an.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Send a request (Anforderung senden)

1. Erstellen Sie eine Funktion mit dem Namen `Search`, um eine Suchanforderung zu senden. Führen Sie darin die folgenden Schritte aus:

   1. Erstellen Sie ein JSON-Objekt mit Ihren Anforderungsparametern: Verwenden Sie `Get` für die Methode, und fügen Sie Ihre Host- und Pfadinformationen hinzu. Fügen Sie dem `Ocp-Apim-Subscription-Key`-Header Ihren Abonnementschlüssel hinzu. 
   2. Verwenden Sie `https.request()`, um die Anforderung mit dem zuvor erstellten Antworthandler und Ihren Suchparametern zu senden.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. Rufen Sie die Funktion `Search()` auf.

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](../tutorial-bing-entities-search-single-page-app.md)

* [Was ist die Bing-Entitätssuche-API?](../overview.md )
* [Referenz zur Bing-Entitätssuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
