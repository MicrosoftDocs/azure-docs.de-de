---
title: 'Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit Node.js | Microsoft-Dokumentation'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche mit Node.js zu beginnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 43b46b4dd035efa3117ce23728db42bb11a9ab6c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790274"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Schnellstart: Aufrufen Ihres Endpunkts für die benutzerdefinierte Bing-Suche mit Node.js

Verwenden Sie diese Schnellstartanleitung, um mit dem Anfordern von Suchergebnissen von Ihrer Instanz der benutzerdefinierten Bing-Suche zu beginnen. Diese Anwendung ist zwar in JavaScript geschrieben, aber die API für die benutzerdefinierte Bing-Suche ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Instanz der benutzerdefinierten Bing-Suche. Weitere Informationen finden Sie unter [Schnellstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](quick-start.md).

- [Node.js](https://www.nodejs.org/)

- Die [JavaScript-Bibliothek für Anforderungen](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder einem Editor, und fügen Sie eine `require()`-Anweisung für die Bibliothek mit den Anforderungen hinzu. Erstellen Sie Variablen für Ihren Abonnementschlüssel, die benutzerdefinierte Konfigurations-ID und einen Suchbegriff. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Senden und Empfangen einer Suchanforderung 

1. Erstellen Sie eine Variable zum Speichern der Informationen, die in Ihrer Anforderung gesendet werden. Erstellen Sie die Anforderungs-URL, indem Sie Ihren Suchbegriff an den Abfrageparameter `q=` und Ihre benutzerdefinierte Konfigurations-ID der Suchinstanz an `customconfig=` anfügen. Trennen Sie die Parameter mit dem Zeichen `&` voneinander. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Verwenden Sie die JavaScript-Anforderungsbibliothek zum Senden einer Suchanforderung an Ihre Instanz der benutzerdefinierten Bing-Suche, und geben Sie Informationen zu den Ergebnissen aus, z.B. Name, URL und das Datum des letzten Crawl-Vorgangs der Webseite.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Web-App für die benutzerdefinierte Suche](./tutorials/custom-search-web-page.md)
