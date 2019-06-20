---
title: 'Schnellstart: Suchen nach Bildern – Bing-Bildersuche-REST-API und Python'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um Bildsuchanforderungen mithilfe von Python an die Bing-Bildersuche-REST-API zu senden und JSON-Antworten zu empfangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 59c7a85ae564981029163bf99a8e44e72237f709
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383658"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Schnellstart: Suchen nach Bildern mithilfe der Bing-Bildersuche-REST-API und Python

In diesem Schnellstart erfahren Sie, wie Sie Suchanforderungen an die Bing-Bildersuche-API senden. Diese Python-Anwendung sendet eine Suchabfrage an die API und zeigt die URL des ersten Bilds in den Ergebnissen an. Diese Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Sie können dieses Beispiel als Jupyter Notebook in [MyBinder](https://mybinder.org) ausführen, indem Sie auf das Binder-Badge klicken:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.


## <a name="prerequisites"></a>Voraussetzungen

* [Python 2.x oder 3.x](https://www.python.org/)
* [PIL (Python Imaging Library)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor eine neue Python-Datei, und importieren Sie die folgenden Module: Erstellen Sie Variablen für Ihren Abonnementschlüssel, den Suchendpunkt und den Suchbegriff.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Fügen Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu, indem Sie ein Wörterbuch erstellen und den Schlüssel als Wert hinzufügen. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Erstellen und Senden einer Suchanforderung

1. Erstellen Sie ein Wörterbuch für die Parameter der Suchanforderung. Fügen Sie dem Parameter `q` Ihren Suchbegriff hinzu. Verwenden Sie „public“ (öffentlich) für den Parameter `license`, um nach Bildern in der öffentlichen Domäne zu suchen. Verwenden Sie „photo“ (Foto) für `imageType`, um ausschließlich nach Fotos zu suchen.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Rufen Sie mithilfe der Bibliothek `requests` die Bing-Bildersuche-API auf. Fügen Sie der Anforderung Ihren Header und die Parameter hinzu, und geben Sie die Antwort als JSON-Objekt zurück. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Anzeigen der Antwort

1. Erstellen Sie mithilfe der matplotlib-Bibliothek eine neue Abbildung mit vier Spalten und vier Zeilen. 

2. Durchlaufen Sie die Zeilen und Spalten der Abbildung, und fügen Sie jedem Bereich mithilfe der Methode `Image.open()` der PIL-Bibliothek ein Miniaturbild hinzu. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Verwenden Sie `plt.show()`, um die Abbildung zu zeichnen und die Bilder anzuzeigen.

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

* [Was ist die Bing-Bildersuche-API?](../overview.md)  
* [Details zu den Preisen](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) für die Bing-Suche-APIs 
* [Abrufen eines kostenlosen Cognitive Services-Zugriffsschlüssels](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentation zu Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenz zur Bing-Bildersuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
