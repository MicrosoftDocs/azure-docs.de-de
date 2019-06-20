---
title: 'Schnellstart: Nachschlagen von Wörtern im bilingualen Wörterbuch, Python – Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie alternative Übersetzungen und Verwendungsbeispiele für einen angegebenen Text finden, indem Sie Python und die Textübersetzungs-REST-API verwenden.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: ef72e7f5a4974a9da96d03dc74bc7a8b01ff4d10
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515085"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-python"></a>Schnellstart: Nachschlagen von Wörtern im bilingualen Wörterbuch mithilfe von Python

In dieser Schnellstartanleitung wird beschrieben, wie Sie alternative Übersetzungen und Verwendungsbeispiele für einen angegebenen Text finden, indem Sie Python und die Textübersetzungs-REST-API verwenden.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Textübersetzungsressource benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* Python 2.7.x oder 3.x
* Ein Azure-Abonnementschlüssel für die Textübersetzung

## <a name="create-a-project-and-import-required-modules"></a>Erstellen eines Projekts und Importieren der erforderlichen Module

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt, oder erstellen Sie einen neuen Ordner auf dem Desktop. Kopieren Sie den folgenden Codeausschnitt in Ihrem Projekt bzw. Ihrem Ordner in die Datei `dictionary-lookup.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Wenn Sie diese Module bisher nicht verwendet haben, müssen Sie sie vor der Ausführung Ihres Programms installieren. Führen Sie zum Installieren dieser Pakete `pip install requests uuid` aus.

Der erste Kommentar weist Ihren Python-Interpreter, UTF-8-Codierung zu verwenden. Anschließend werden erforderliche Module importiert, um Ihren Abonnementschlüssel aus einer Umgebungsvariablen zu lesen, die HTTP-Anforderung und einen eindeutigen Bezeichner zu erstellen und die von der Textübersetzungs-API zurückgegebene JSON-Antwort zu verarbeiten.

## <a name="set-the-subscription-key-base-url-and-path"></a>Festlegen des Abonnementschlüssels, der Basis-URL und des Pfads

Dieses Beispiel liest den Textübersetzungs-Abonnementschlüssel aus der Umgebungsvariablen `TRANSLATOR_TEXT_KEY`. Wenn Sie mit Umgebungsvariablen nicht vertraut sind, können Sie `subscriptionKey` als Zeichenfolge festlegen und die Bedingungsanweisung auskommentieren.

Kopieren Sie diesen Code in Ihr Projekt:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key. Then, be sure to delete your "os" import.
# subscriptionKey = 'put_your_key_here'
```

Der globale Endpunkt der Textübersetzung ist als die `base_url` festgelegt. `path` legt die `dictionary/lookup`-Route fest und gibt die gewünschte Version der API (Version 3) an.

Die Parameter (`params`) werden zum Festlegen der Quelle und Ausgeben von Sprachen verwendet. In diesem Beispiel werden Englisch und Spanisch genutzt, also `en` und `es`.

>[!NOTE]
> Weitere Informationen zu Endpunkten, Routen und Anforderungsparametern finden Sie unter [Textübersetzungs-API 3.0: Wörterbuchsuche](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es';
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Hinzufügen von Headern

Eine Anforderung lässt sich am einfachsten authentifizieren, indem Sie den Abonnementschlüssel als `Ocp-Apim-Subscription-Key`-Header übergeben. Diese Vorgehensweise wird in diesem Beispiel verwendet. Alternativ können Sie den Abonnementschlüssel durch ein Zugriffstoken ersetzen und dieses als `Authorization`-Header übergeben, um Ihre Anforderung zu überprüfen. Weitere Informationen finden Sie unter [Authentifizierung](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopieren Sie diesen Codeausschnitt in Ihr Projekt:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-find-alternate-translations"></a>Erstellen einer Anforderung zum Suchen nach alternativen Übersetzungen

Definieren Sie die Zeichenfolge (oder Zeichenfolgen), für die Sie Übersetzungen ermitteln möchten:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

Erstellen Sie als Nächstes mithilfe des `requests`-Moduls eine POST-Anforderung. Sie umfasst drei Argumente: die verkettete URL, der Anforderungsheader und der Anforderungstext:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Ausgeben der Antwort

Im letzten Schritt werden die Ergebnisse ausgegeben. In diesem Codeausschnitt werden die Ergebnisse übersichtlicher gemacht, indem die Schlüssel sortiert, Einzüge festgelegt und Element- und Schlüsseltrennzeichen deklariert werden.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Das war's: Sie haben ein einfaches Programm erstellt, das die Textübersetzungs-API aufruft und eine JSON-Antwort zurückgibt. Führen Sie das Programm jetzt aus:

```console
python alt-translations.py
```

[Auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) finden Sie das vollständige Beispiel, falls Sie Ihren Code mit unserem Code vergleichen möchten.

## <a name="sample-response"></a>Beispiel für eine Antwort

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Abonnementschlüssel in Ihrem Programm hartcodiert haben, entfernen Sie ihn unbedingt, wenn Sie diese Schnellstartanleitung abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Python-Beispiele auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Weitere Informationen

Informieren Sie sich, wie Sie die Textübersetzungs-API für folgende Zwecke verwenden:

* [Übersetzen von Text](quickstart-python-translate.md)
* [Transliteration von Text](quickstart-python-transliterate.md)
* [Identifizieren der Sprache anhand der Eingabe](quickstart-python-detect.md)
* [Abrufen einer Liste der unterstützten Sprachen](quickstart-python-languages.md)
* [Bestimmen der Satzlänge aus einer Eingabe](quickstart-python-sentences.md)
