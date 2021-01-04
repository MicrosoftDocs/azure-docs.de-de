---
title: 'Schnellstart: Python-Clientbibliothek für benutzerdefinierte Bing-Suche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 92df45e38a926b4a791315f731e1d1cb078d5852
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371599"
---
Steigen Sie in die Clientbibliothek der benutzerdefinierten Bing-Suche für Python ein. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Die API für die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten werbefreien Suchbenutzeroberfläche für Themen, die Sie interessieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Verwenden Sie die Clientbibliothek der benutzerdefinierten Bing-Suche für Python wie folgt:
* Ermitteln Sie über Ihre Instanz der benutzerdefinierten Bing-Suche Suchergebnisse im Web.

[Referenzdokumentation](/python/api/azure-cognitiveservices-search-customsearch/?view=azure-python) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [Paket (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Voraussetzungen

- Eine Instanz der benutzerdefinierten Bing-Suche. Weitere Informationen finden Sie unter [Schnellstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](../../quick-start.md).
- Python [2.x oder 3.x](https://www.python.org/) 
- Das [SDK für die benutzerdefinierte Bing-Suche für Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Installieren der Python-Clientbibliothek

Installieren Sie die Clientbibliothek für die benutzerdefinierte Bing-Suche mithilfe des folgenden Befehls:

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

Erstellen Sie in Ihrem bevorzugten Editor oder in Ihrer bevorzugten IDE eine neue Python-Datei, und fügen Sie die folgenden Importe hinzu:

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Erstellen eines Clients für die Suche und Senden einer Anforderung

1. Erstellen Sie eine Variable für Ihren Abonnementschlüssel und den Endpunkt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Erstellen Sie eine Instanz von `CustomSearchClient`. Verwenden Sie dabei ein Objekt vom Typ `CognitiveServicesCredentials` mit dem Abonnementschlüssel. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Senden Sie eine Suchanforderung mit `client.custom_instance.search()`. Fügen Sie Ihren Suchbegriff an den Parameter `query` an, und legen Sie `custom_config` auf Ihre benutzerdefinierte Konfigurations-ID fest, um Ihre Suchinstanz zu verwenden. Die ID finden Sie im [Portal für die benutzerdefinierte Bing-Suche](https://www.customsearch.ai/) auf der Registerkarte **Produktion**.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Anzeigen der Suchergebnisse

Sofern bei der Suche Webseiten gefunden wurden, rufen Sie das erste Ergebnis ab, und geben Sie Name, URL und die Gesamtanzahl gefundener Webseiten aus.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Web-App für die benutzerdefinierte Suche](../../tutorials/custom-search-web-page.md)