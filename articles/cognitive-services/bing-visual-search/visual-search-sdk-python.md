---
title: 'Schnellstart: SDK für die visuelle Bing-Suche, Python'
titleSuffix: Azure Cognitive Services
description: Setup für die Python SDK-Konsolenanwendung für die thematische Suche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: eb115d1bfe033761c8dab82b693e4e694ff3bd2b
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734636"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit dem SDK für die visuelle Bing-Suche für Python

Verwenden Sie diese Schnellstartanleitung, um Erkenntnisse zu Bildern aus dem Dienst für die visuelle Bing-Suche mithilfe des Python SDK zu gewinnen. Zwar verfügt die visuelle Bing-Suche über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, doch bietet das SDK eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py). 

## <a name="prerequisites"></a>Voraussetzungen

* [Python](https://www.python.org/) 2.x oder 3.x
* Es wird empfohlen, eine [virtuelle Umgebung](https://docs.python.org/3/tutorial/venv.html) zu verwenden. Installieren und initialisieren Sie die virtuelle Umgebung mit dem [venv-Modul](https://pypi.python.org/pypi/virtualenv). Installieren Sie virtualenv für Python 2.7.
* Das SDK für die visuelle Bing-Suche für Python. Sie können das SDK mit den folgenden Befehlen installieren:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor eine neue Python-Datei, und fügen Sie die folgenden Importanweisungen hinzu. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Erstellen Sie Variablen für Ihren Abonnementschlüssel, die benutzerdefinierte Konfigurations-ID und das Bild, das Sie hochladen möchten. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Instanziieren des Clients

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Senden der Suchanforderung

1. Serialisieren Sie `VisualSearchRequest()` bei geöffneter Bilddatei, und übergeben Sie die Anforderung als `knowledge_request`-Parameter für `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Wenn Ergebnisse zurückgegeben wurden, geben sie diese, die Tags und die Aktionen im ersten Tag aus.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](tutorial-bing-visual-search-single-page-app.md)
