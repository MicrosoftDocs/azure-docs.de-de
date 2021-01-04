---
title: 'Schnellstart: Ausführen einer Suche mit Python – Bing-Websuche-API'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Senden von Anforderungen an die REST-API der Bing-Websuche mit Python, und Sie erhalten eine JSON-Antwort.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 5a9eff1ef012e66f661ee99852a8d82f117a45c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350516"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Schnellstart: Verwenden von Python zum Aufrufen der Bing-Websuche-API  

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Websuche-API zum ersten Mal aufzurufen. Diese Python-Anwendung sendet eine Suchanforderung an die API und zeigt die JSON-Antwort an. Die Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst und mit den meisten Programmiersprachen kompatibel.

Dieses Beispiel wird als Jupyter-Notebook unter [MyBinder](https://mybinder.org) ausgeführt. Wählen Sie zum Ausführen den Badge zum Starten von Binder aus:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Voraussetzungen

* [Python 2.x oder 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definieren von Variablen

1. Ersetzen Sie den Wert von `subscription_key` durch einen gültigen Abonnementschlüssel aus Ihrem Azure-Konto.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Deklarieren Sie den Endpunkt für die Bing-Websuche-API. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Optional können Sie die Suchabfrage auch anpassen, indem Sie den Wert für `search_term` ersetzen.

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Erstellen einer Anforderung

In diesem Code wird die `requests`-Bibliothek verwendet, um die Bing-Websuche-API aufzurufen und die Ergebnisse als JSON-Objekt zurückzugeben. Der API-Schlüssel wird im `headers`-Wörterbuch übergeben, und der Suchbegriff und die Abfrageparameter werden im `params`-Wörterbuch übergeben. 

Eine vollständige Liste der Optionen und Parameter finden Sie unter [Bing-Websuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatieren und Anzeigen der Antwort

Das `search_results`-Objekt enthält die Suchergebnisse und die Metadaten, wie verwandte Abfragen und Seiten. In diesem Code wird die `IPython.display`-Bibliothek verwendet, um die Antwort in Ihrem Browser zu formatieren und anzuzeigen.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Beispielcode auf GitHub

Informationen dazu, wie Sie diesen Code lokal ausführen, finden Sie um vollständigen [Beispiel auf GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einzelseiten-App für die Bing-Websuche-API](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]