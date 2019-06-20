---
title: 'Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von Ruby'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden einer Anforderung an die Bing-Entitätssuche-REST-API mit Ruby und Erhalten einer JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b5653ffbfeb22bc59c48dd92b558178fcd89b2de
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384497"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Schnellstart für die Bing-Entitätssuche-API mit Ruby

Verwenden Sie diese Schnellstartanleitung, um die Bing-Entitätssuche-API zum ersten Mal aufzurufen und die JSON-Antwort anzuzeigen. Diese einfache Ruby-Anwendung sendet eine Nachrichtensuchabfrage an die API und zeigt die Antwort an. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb) verfügbar.

Diese Anwendung ist zwar in Ruby geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) oder höher

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE oder einem beliebigen Code-Editor eine Ruby-Nachrichtendatei, und importieren Sie die folgenden Pakete.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Erstellen Sie Variablen für Ihren API-Endpunkt, die News-Suche-URL, Ihren Abonnementschlüssel und eine Suchabfrage.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formatieren und Senden einer API-Anforderung

1. Erstellen Sie die Parameterzeichenfolge für Ihre Anforderung, indem Sie Ihre Marktvariable an den Parameter `?mkt=` anfügen. Codieren Sie Ihre Anforderung, und fügen Sie sie an den Parameter `&q=` an. Kombinieren Sie Ihren API-Host, den Pfad und die Parameter für Ihre Anforderung, und wandeln Sie sie in ein URI-Objekt um.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Verwenden Sie die Variablen aus dem letzten Schritt, um die Anforderung zu erstellen. Fügen Sie dem `Ocp-Apim-Subscription-Key`-Header Ihren Abonnementschlüssel hinzu.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Senden der Anforderung und Ausgeben der Antwort

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
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
