---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Ruby'
titlesuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API zum Überprüfen von Rechtschreibung und Grammatik
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a3e65f9ad8e8a9c6876d1588ecaa94531206c6f4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389706"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Ruby

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Bing-Rechtschreibprüfungs-REST-API unter Verwendung von Ruby zum ersten Mal aufrufen. Diese einfache Anwendung sendet eine Anforderung an die API und gibt eine Liste mit nicht erkannten Wörtern sowie entsprechende Korrekturvorschläge zurück. Diese Anwendung ist zwar in Ruby geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) oder höher

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrem bevorzugten Editor oder in Ihrer bevorzugten IDE eine neue Ruby-Datei, und fügen Sie die folgenden Anforderungen hinzu. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel, Endpunkt-URI und Pfad. Erstellen Sie Ihre Anforderungsparameter, indem Sie den Parameter `mkt=` an Ihren Markt und `&mode` an den Prüfmodus `proof` anfügen.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Senden einer Rechtschreibprüfungsanforderung

1. Erstellen Sie einen URI auf der Grundlage Ihres Host-URIs, Ihres Pfads und Ihrer Parameterzeichenfolge. Legen Sie die zugehörige Abfrage so fest, dass sie den Text enthält, für den die Rechtschreibprüfung durchgeführt werden soll.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Erstellen Sie eine Anforderung mit dem oben erstellten URI. Fügen Sie Ihren Schlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Senden Sie die Anforderung.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Rufen Sie die JSON-Antwort ab, und geben Sie sie in der Konsole aus. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
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
