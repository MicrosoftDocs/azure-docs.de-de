---
title: 'Schnellstart: Vorschlagen von Suchabfragen mit der Bing-Vorschlagssuche-REST-API und Node.js'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie schnell loslegen und mit der Bing-Vorschlagssuche-API und Node.js Suchbegriffe in Echtzeit vorschlagen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 03545aafa1cbf6efa82ae063e7e966443b1d5e8b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106120"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-nodejs"></a>Schnellstart: Vorschlagen von Suchabfragen mit der Bing-Vorschlagssuche-REST-API und Node.js

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Bing-Vorschlagssuche-API aufrufen und die JSON-Antwort lesen. Diese einfache Node.js-Anwendung sendet eine partielle Suchabfrage an die API und gibt Vorschläge für Suchvorgänge zurück. Diese Anwendung ist zwar in JavaScript geschrieben, aber die API ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingAutosuggestv7.js).

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js 6](https://nodejs.org/en/download/) oder höher

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und legen Sie die Genauigkeit sowie die HTTPS-Anforderungen fest.
    
    ```javascript
    'use strict';
    
    let https = require ('https');
    ```

2. Erstellen Sie Variablen für Ihren den Host und Pfad Ihres API-Endpunkts, Ihren Abonnementschlüssel, Ihren [Marktcode](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes) und eine Suchabfrage. Verwenden Sie den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md), der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```javascript
    // Replace the subscriptionKey string value with your valid subscription key.
    let subscriptionKey = 'enter key here';
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/Suggestions';
    
    let mkt = 'en-US';
    let query = 'sail';
    ```

## <a name="construct-the-search-request-and-query"></a>Erstellen Sie die Suchanforderung und -abfrage.

1. Erstellen Sie eine Parameterzeichenfolge für die Abfrage, indem Sie den Marktcode an den Parameter `mkt=` und Ihre Abfrage an den Parameter `q=` anfügen.

    ```javascript 
    let params = '?mkt=' + mkt + '&q=' + query;
    ```

2. Erstellen Sie eine Funktion mit dem Namen `get_suggestions()`. Verwenden Sie die Variablen aus den vorherigen Schritten, um eine Such-URL für die API-Anforderung zu formatieren. Der Suchbegriff muss URL-codiert werden, bevor er an die API gesendet wird.

    ```javascript
    let get_suggestions = function () {
      let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
          'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
      };
    //...
    }
    ```

 1. Verwenden Sie in der gleichen Funktion die Bibliothek für Anforderungen, um Ihre Abfrage an die API zu senden. `response_handler` wird im nächsten Abschnitt definiert.
    
    ```javascript
        //...
        let req = https.request(request_params, response_handler);
        req.end();
    ```

## <a name="create-a-search-handler"></a>Erstellen eines Suchhandlers

1. Definieren Sie eine Funktion mit der Bezeichnung `response_handler`, die einen HTTP-Aufruf (`response`) als Parameter verwendet. Führen Sie innerhalb dieser Funktion die folgenden Schritte aus:
    
    1. Definieren Sie eine Variable, die den Text der JSON-Antwort enthält.  

        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Speichern Sie den Text der Antwort, wenn das Flag `data` aufgerufen wird.
        
        ```javascript
        response.on ('data', function (d) {
        body += d;
        });
        ```

    3. Wird ein Flag vom Typ `end` angezeigt, geben Sie die Antwort mithilfe von `JSON.parse()` und `JSON.stringify()` aus.
    
        ```javascript
        response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
            console.log (body__);
        });
        response.on ('error', function (e) {
            console.log ('Error: ' + e.message);
        });
        ```

2. Rufen Sie `get_suggestions()` auf, um die Anforderung an die Bing-Vorschlagssuche-API zu senden.

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](../tutorials/autosuggest.md)

- [Worum handelt es sich bei der Bing-Vorschlagssuche?](../get-suggested-search-terms.md)
- [Referenz für die Bing-Vorschlagssuche-API V7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
