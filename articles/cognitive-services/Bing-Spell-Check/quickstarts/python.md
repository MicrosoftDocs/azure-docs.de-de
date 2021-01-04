---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der REST-API und Python – Bing-Rechtschreibprüfung'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API und Python zum Überprüfen von Rechtschreibung und Grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 27e5a336b0a7e6fa0e47e20ad0d3c6204cef7757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352659"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Python

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache Python-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit Korrekturvorschlägen zurück. 

Die Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst und mit den meisten Programmiersprachen kompatibel. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor eine neue Python-Datei, und fügen Sie die folgenden Importanweisungen hinzu:

   ```python
   import requests
   import json
   ```

2. Erstellen Sie Variablen für den Text, für den eine Rechtschreibprüfung ausgeführt werden soll, sowie für Ihren Abonnementschlüssel und Ihren Endpunkt für die Bing-Rechtschreibprüfung. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Erstellen der Parameter für die Anforderung

1. Erstellen Sie ein neues Wörterbuch mit `text` als Schlüssel und Ihrem Text als Wert.

    ```python
    data = {'text': example_text}
    ```

2. Fügen Sie die Parameter für Ihre Anforderung hinzu: 

   1. Weisen Sie dem `mkt`-Parameter mit dem `=`-Operator Ihren Marktcode zu. Der Marktcode ist der Code Lands bzw. der Region, aus dem/der Sie die Anforderung stellen. 

   1. Fügen Sie den Parameter `mode` mit dem Operator `&` hinzu, und weisen Sie dann den Rechtschreibprüfungsmodus zu. Der Modus kann entweder `proof` (fängt die meisten Rechtschreib-/Grammatikfehler ab) oder `spell` (fängt die meisten Rechtschreibfehler, aber nicht so viele Grammatikfehler ab) sein. 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Fügen Sie einen Header vom Typ `Content-Type` und dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Senden der Anforderung und Lesen der Antwort

1. Senden Sie die POST-Anforderung unter Verwendung der Anforderungsbibliothek.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Rufen Sie die JSON-Antwort ab, und geben Sie sie aus.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Ausführen der Anwendung

Geben Sie bei Verwenden der Befehlszeile den folgenden Befehl an, um die Anwendung auszuführen:

```bash
python <FILE_NAME>.py
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
> [Erstellen einer Single-Page-Web-App](../tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)