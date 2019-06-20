---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Python'
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
ms.openlocfilehash: e74cc01d9104ed4f26d857f3a99eb21e312a7f59
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389745"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Python

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache Python-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit Korrekturvorschlägen zurück. Diese Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor eine neue Python-Datei, und fügen Sie die folgende Importanweisung hinzu.

   ```python
   import requests
   import json
   ```

2. Erstellen Sie Variablen für den Text, für den eine Rechtschreibprüfung ausgeführt werden soll, sowie für Ihren Abonnementschlüssel und Ihren Endpunkt für die Bing-Rechtschreibprüfung.

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

2. Fügen Sie die Parameter für Ihre Anforderung hinzu. Fügen Sie Ihren Marktcode nach `mkt=` an. Der Marktcode ist das Land, aus dem Sie die Anforderung stellen. Fügen Sie außerdem Ihren Modus für die Rechtschreibprüfung nach `&mode=` an. Der Modus ist entweder `proof` (fängt die meisten Rechtschreib-/Grammatikfehler ab) oder `spell` (fängt die meisten Rechtschreib-, aber nicht so viele Grammatikfehler ab).

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
