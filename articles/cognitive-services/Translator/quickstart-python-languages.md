---
title: 'Schnellstart: Abrufen einer Liste mit den unterstützten Sprachen, Python – Textübersetzung-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung rufen Sie eine Liste mit den für Übersetzung, Transliteration und Wörterbuchsuche unterstützten Sprachen sowie Beispiele ab. Hierzu verwenden Sie die Textübersetzungs-API mit Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: fe2ab3952b68243f7e95ebf754a42e58006f012b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514730"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Abrufen einer Liste unterstützter Sprachen mit Python

In dieser Schnellstartanleitung wird beschrieben, wie Sie Python und die Textübersetzungs-REST-API nutzen, um eine GET-Anforderung zu senden, mit der eine Liste mit den unterstützten Sprachen zurückgegeben wird.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* Python 2.7.x oder 3.x

## <a name="create-a-project-and-import-required-modules"></a>Erstellen eines Projekts und Importieren der erforderlichen Module

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in Ihr Projekt in eine Datei namens `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Wenn Sie diese Module bisher nicht verwendet haben, müssen Sie sie vor der Ausführung Ihres Programms installieren. Führen Sie zum Installieren dieser Pakete `pip install requests uuid` aus.

Der erste Kommentar weist Ihren Python-Interpreter, UTF-8-Codierung zu verwenden. Anschließend werden erforderliche Module importiert, um Ihren Abonnementschlüssel aus einer Umgebungsvariablen zu lesen, die HTTP-Anforderung und einen eindeutigen Bezeichner zu erstellen und die von der Textübersetzungs-API zurückgegebene JSON-Antwort zu verarbeiten.

## <a name="set-the-base-url-and-path"></a>Festlegen der Basis-URL und des Pfads

Der globale Endpunkt der Textübersetzung ist als die `base_url` festgelegt. `path` legt die `languages`-Route fest und gibt die gewünschte Version der API (Version 3) an.

>[!NOTE]
> Weitere Informationen zu Endpunkten, Routen und Anforderungsparametern finden Sie unter [Textübersetzungs-API 3.0: Sprachen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Hinzufügen von Headern

Die Anforderung zum Abrufen der unterstützten Sprachen erfordert keine Authentifizierung. Legen Sie `Content-type` als `application/json` fest, und fügen Sie `X-ClientTraceId` zur eindeutigen Identifizierung Ihrer Anforderung hinzu.

Kopieren Sie diesen Codeausschnitt in Ihr Projekt:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Erstellen einer Anforderung zum Abrufen einer Liste mit unterstützten Sprachen

Wir erstellen mit dem `requests`-Modul eine GET-Anforderung. Hierfür werden zwei Argumente verwendet, nämlich die verkettete URL und die Anforderungsheader:

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

[Auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) finden Sie das vollständige Beispiel, falls Sie Ihren Code mit unserem Code vergleichen möchten.

## <a name="sample-response"></a>Beispiel für eine Antwort

Die Abkürzung für das Land bzw. die Region finden Sie in [dieser Liste der Sprachen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Dieses Beispiel wurde gekürzt, um nur einen Ausschnitt des Ergebnisses anzuzeigen:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [Ermitteln alternativer Übersetzungen](quickstart-python-dictionary.md)
* [Bestimmen der Satzlänge aus einer Eingabe](quickstart-python-sentences.md)
