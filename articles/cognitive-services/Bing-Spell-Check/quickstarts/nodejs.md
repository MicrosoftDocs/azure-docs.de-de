---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der REST-API und Node.js – Bing-Rechtschreibprüfung'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API und Node.js zum Überprüfen von Rechtschreibung und Grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 182ccc972fce40adc88ed66b01c92e778f6479a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352693"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Node.js

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache JavaScript-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit Korrekturvorschlägen zurück. 

Die Anwendung ist zwar in JavaScript geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js 6](https://nodejs.org/en/download/) oder höher

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor eine neue JavaScript-Datei. Legen Sie die Genauigkeit fest, und erzwingen Sie `https`. Erstellen Sie anschließend Variablen für den Host, Pfad und Abonnementschlüssel Ihres API-Endpunkts. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Erstellen Sie Variablen für Ihre Suchparameter und den Text, den Sie überprüfen möchten: 

   1. Weisen Sie dem `mkt`-Parameter mit dem `=`-Operator Ihren Marktcode zu. Der Marktcode ist der Code Lands bzw. der Region, aus dem/der Sie die Anforderung stellen. 

   1. Fügen Sie den Parameter `mode` mit dem Operator `&` hinzu, und weisen Sie dann den Rechtschreibprüfungsmodus zu. Der Modus kann entweder `proof` (fängt die meisten Rechtschreib-/Grammatikfehler ab) oder `spell` (fängt die meisten Rechtschreibfehler, aber nicht so viele Grammatikfehler ab) sein.

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Erstellen der Anforderungsparameter

Erstellen Sie Ihre Anforderungsparameter, indem Sie ein neues Objekt mit einer Methode vom Typ `POST` erstellen. Fügen Sie Ihren Pfad hinzu, indem Sie Ihren Endpunktpfad und eine Abfragezeichenfolge anfügen. Fügen Sie dem `Ocp-Apim-Subscription-Key`-Header anschließend Ihren Abonnementschlüssel hinzu.

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

Erstellen Sie eine Funktion mit dem Namen `response_handler`, um die JSON-Antwort von der API zu akzeptieren und auszugeben. Erstellen Sie eine Variable für den Antworttext. Fügen Sie die Antwort unter Verwendung von `response.on()` an, wenn ein `data`-Flag empfangen wird. Nachdem ein Flag vom Typ `end` empfangen wird, geben Sie den JSON-Text in der Konsole aus.

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


## <a name="run-the-application"></a>Ausführen der Anwendung

1. Erstellen Sie Ihr Projekt, und führen Sie es aus.

1. Geben Sie bei Verwenden der Befehlszeile den folgenden Befehl an, um die Anwendung zu erstellen und auszuführen:

   ```bash
   node <FILE_NAME>.js
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
> [Erstellen einer Single-Page-Web-App](../tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)