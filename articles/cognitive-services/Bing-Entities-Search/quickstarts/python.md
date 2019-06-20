---
title: 'Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von Python'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden einer Anforderung an die Bing-Entitätssuche-REST-API mit Python und Erhalten einer JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 43f01b39ed01e94fe21283997b5981e7fa49bde5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384523"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von Python

Verwenden Sie diese Schnellstartanleitung, um die Bing-Entitätssuche-API zum ersten Mal aufzurufen und die JSON-Antwort anzuzeigen. Diese einfache Python-Anwendung sendet eine Nachrichtensuchabfrage an die API und zeigt die Antwort an. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Diese Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

* [Python](https://www.python.org/downloads/) 2.x oder 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor eine neue Python-Datei, und fügen Sie die folgenden Importe hinzu. Erstellen Sie Variablen für Ihren Abonnementschlüssel, den Endpunkt, den Markt und eine Suchabfrage. Ihren Endpunkt finden Sie auf dem Azure-Dashboard.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Erstellen Sie eine Anforderungs-URL, indem Sie die Marktvariable an den Parameter `?mkt=` anfügen. Führen Sie die URL-Codierung für Ihre Anforderung durch, und fügen Sie sie an den Parameter `&q=` an. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Senden einer Anforderung und Erhalten einer Antwort

1. Erstellen Sie eine Funktion mit dem Namen `get_suggestions()`. Führen Sie anschließend die folgenden Schritte aus.
   1. Fügen Sie Ihren Abonnementschlüssel einem Wörterbuch mit `Ocp-Apim-Subscription-Key` als Schlüssel hinzu.
   2. Verwenden Sie `http.client.HTTPSConnection()`, um ein HTTPS-Clientobjekt zu erstellen. Senden Sie eine `GET`-Anforderung, indem Sie `request()` mit Ihrem Pfad und den Parametern sowie den Headerinformationen verwenden.
   3. Speichern Sie die Antwort mit `getresponse()`, und geben Sie `response.read()` zurück.

      ```python
      def get_suggestions ():
       headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
       conn = http.client.HTTPSConnection (host)
       conn.request ("GET", path + params, None, headers)
       response = conn.getresponse ()
       return response.read()
      ```

2. Rufen Sie `get_suggestions()` auf, und geben Sie die JSON-Antwort aus.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

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

* [Was ist die Bing-Entitätssuche-API?](../search-the-web.md)
* [Referenz zur Bing-Entitätssuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
