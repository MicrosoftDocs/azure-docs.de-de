---
title: 'Schnellstart: Suchen nach Bildern – Bing-Bildersuche-SDK für Python'
titleSuffix: Azure Cognitive Services
description: Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Python-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 69553c11ed0e9b07370fa1af3acc65b6605d1a8a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385060"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Schnellstart: Suchen nach Bildern mit dem Bing-Bildersuche-SDK für Python

Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Python-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
Rufen Sie unter **Suchen** einen [Cognitive Services-Zugriffsschlüssel](https://azure.microsoft.com/try/cognitive-services/) ab.  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

* [Python 2.7 oder 3.4](https://www.python.org/) und höher

* Das [Bildersuche-SDK in Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) für Python
    * Installation mithilfe von `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor ein neues Python-Skript und die folgenden Importe:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und einen Suchbegriff.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Erstellen eines Clients für die Bildersuche

1. Erstellen Sie eine `CognitiveServicesCredentials`-Instanz, und instanziieren Sie damit den Client:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Senden Sie eine Suchabfrage an die Bing-Bildersuche-API:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Lassen Sie die Ergebnisse verarbeiten und anzeigen.

Analysieren Sie die Bildergebnisse, die in der Antwort zurückgegeben werden.


Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und drucken Sie die Details aus, z.B. eine Miniaturansichts-URL, die ursprüngliche URL und die Gesamtzahl der zurückgegebenen Bilder.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Abrufen eines kostenlosen Cognitive Services-Zugriffsschlüssels](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Python-Beispiele für das Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentation zu Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenz zur Bing-Bildersuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
