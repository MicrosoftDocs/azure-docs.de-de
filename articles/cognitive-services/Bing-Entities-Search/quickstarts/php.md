---
title: 'Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von PHP'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden einer Anforderung an die Bing-Entitätssuche-REST-API mit PHP, und Sie erhalten eine JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 69624f7383b37b9d23f3fb8879f804e7169c1c29
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384555"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-php"></a>Schnellstart: Senden einer Suchanforderung an die Bing-Entitätssuche-REST-API mithilfe von PHP

Verwenden Sie diese Schnellstartanleitung, um die Bing-Entitätssuche-API zum ersten Mal aufzurufen und die JSON-Antwort anzuzeigen. Diese einfache PHP-Anwendung sendet eine Nachrichtensuchabfrage an die API und zeigt die Antwort an. 

Diese Anwendung ist zwar in PHP geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

* [PHP 5.6.x](https://php.net/downloads.php) oder höher

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="search-entities"></a>Durchsuchen von Entitäten

Führen Sie die folgenden Schritte aus, um diese Anwendung auszuführen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues PHP-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$subscriptionKey = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsoft.com";
$path = "/bing/v7.0/entities";

$mkt = "en-US";
$query = "italian restaurants near me";

function search ($host, $path, $key, $mkt, $query) {

    $params = '?mkt=' . $mkt . '&q=' . urlencode ($query);

    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$result = search ($host, $path, $subscriptionKey, $mkt, $query);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Antwort**

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
