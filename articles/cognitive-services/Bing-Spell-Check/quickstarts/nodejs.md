---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Node.js'
titlesuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API zum Überprüfen von Rechtschreibung und Grammatik
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: ba56f6d68df34fc9249d02c8aba9155ed41b5be8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388441"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Node.js

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache Node-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit nicht erkannten Wörtern sowie entsprechende Korrekturvorschläge zurück. Diese Anwendung ist zwar in Node.js geschrieben, aber bei der API handelt es sich um einen RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js 6](https://nodejs.org/en/download/) oder höher

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor eine neue JavaScript-Datei. Legen Sie die Genauigkeit fest, und erzwingen Sie `https`. Erstellen Sie anschließend Variablen für den Host, Pfad und Abonnementschlüssel Ihres API-Endpunkts.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Erstellen Sie Variablen für Ihre Suchparameter und den Text, den Sie überprüfen möchten. Fügen Sie Ihren Marktcode nach `mkt=` an. Der Marktcode ist das Land, aus dem Sie die Anforderung stellen. Fügen Sie außerdem Ihren Modus für die Rechtschreibprüfung nach `&mode=` an. Der Modus ist entweder `proof` (fängt die meisten Rechtschreib-/Grammatikfehler ab) oder `spell` (fängt die meisten Rechtschreib-, aber nicht so viele Grammatikfehler ab).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Erstellen der Anforderungsparameter

Erstellen Sie Ihre Anforderungsparameter, indem Sie ein neues Objekt mit einer Methode vom Typ `POST` erstellen. Fügen Sie Ihren Pfad hinzu, indem Sie Ihren Endpunktpfad und eine Abfragezeichenfolge anfügen. Fügen Sie dem `Ocp-Apim-Subscription-Key`-Header Ihren Abonnementschlüssel hinzu.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Erstellen eines Antworthandlers

Erstellen Sie eine Funktion mit dem Namen `response_handler`, um die JSON-Antwort von der API zu akzeptieren und auszugeben. Erstellen Sie eine Variable für den Antworttext. Fügen Sie die Antwort unter Verwendung von `response.on()` an, wenn ein `data`-Flag empfangen wird. Wenn ein Flag vom Typ `end` empfangen wird, geben Sie den JSON-Text in der Konsole aus.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Senden der Anforderung

Rufen Sie die API auf. Verwenden Sie hierzu `https.request()` mit Ihren Anforderungsparametern und dem Antworthandler. Schreiben Sie Ihren Text an die API, und beenden Sie die Anforderung anschließend.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Web-App mit einer einzelnen Seite](../tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
