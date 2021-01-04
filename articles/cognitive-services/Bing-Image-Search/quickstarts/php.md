---
title: 'Schnellstart: Suchen nach Bildern mithilfe der Bing-Bildersuche-REST-API und PHP'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um Bildsuchanforderungen mithilfe von PHP an die Bing-Bildersuche-REST-API zu senden und JSON-Antworten zu empfangen.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf5478dfb15665047e39d51c71ec9e85a436fe91
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341851"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-php"></a>Schnellstart: Suchen nach Bildern mithilfe der Bing-Bildersuche-REST-API und PHP

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diesen Schnellstart, um die Bing-Bildersuche-API zum ersten Mal aufzurufen und eine JSON-Antwort zu erhalten. Die einfache Anwendung in diesem Artikel sendet eine Suchabfrage und zeigt die Rohdatenergebnisse an.

Die Anwendung ist zwar in PHP geschrieben, aber die API ist ein RESTful-Webdienst, der mit jeder Programmiersprache kompatibel ist, mit der das Senden von HTTP-Anforderungen und das Analysieren von JSON-Code möglich ist.

Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/php/Search/BingWebSearchv7.php).

## <a name="prerequisites"></a>Voraussetzungen

* [PHP 5.6.x oder höher](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Weitere Informationen finden Sie unter [Cognitive Services-Preise: Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

Führen Sie die folgenden Schritte aus, um diese Anwendung auszuführen:

1. Achten Sie darauf, dass die Unterstützung des sicheren HTTP in Ihrer `php.ini`-Datei aktiviert ist. Unter Windows befindet sich diese Datei in *C:\windows*.
2. Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues PHP-Projekt.
3. Definieren Sie den API-Endpunkt, Ihren Abonnementschlüssel und einen Suchbegriff. Der Endpunkt kann der globale Endpunkt im folgenden Code oder der Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) sein, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```php
    $endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';
    // Replace the accessKey string value with your valid access key.
    $accessKey = 'enter key here';
    $term = 'tropical ocean';
    ```

## <a name="construct-and-perform-an-http-request"></a>Erstellen und Ausführen einer HTTP-Anforderung

1. Verwenden Sie die Variablen aus dem vorherigen Schritt, um eine HTTP-Anforderung für die Bildersuche-API vorzubereiten.

    ```php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                            'header' => $headers,
                            'method' => 'GET' ));
    ```

2. Senden Sie die Webanforderung, und rufen Sie die JSON-Antwort ab.

    ```php
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);
    ```

## <a name="process-and-print-the-json"></a>Verarbeiten und Ausgeben der JSON-Antwort

Verarbeiten Sie die zurückgegebene JSON-Antwort, und geben Sie sie aus.

```php
$headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }
    return array($headers, $result);
```

## <a name="example-json-response"></a>JSON-Beispielantwort

Antworten der Bing-Bildersuche-API werden im JSON-Format zurückgegeben. Diese Beispielantwort wurde gekürzt, damit nur ein Ergebnis angezeigt wird.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche-API?](../overview.md)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Details zu den Preisen für die Bing-Suche-APIs](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Dokumentation zu Azure Cognitive Services](../../index.yml)
* [Referenz zur Bing-Bildersuche-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)