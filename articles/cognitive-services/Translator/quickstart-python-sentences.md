---
title: 'Schnellstart: Abrufen von Satzlängen, Python – Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Satzlängen (in Zeichen) ermitteln, indem Sie Python und die Textübersetzungs-REST-API verwenden.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 9ab2ebe5556e6850845392fc574c35809730a41a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515072"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-python"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Ermitteln der Satzlänge mit Python

In dieser Schnellstartanleitung wird beschrieben, wie Sie Satzlängen (in Zeichen) ermitteln, indem Sie Python und die Textübersetzungs-REST-API verwenden.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Textübersetzungsressource benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* Python 2.7.x oder 3.x
* Ein Azure-Abonnementschlüssel für die Textübersetzung

## <a name="create-a-project-and-import-required-modules"></a>Erstellen eines Projekts und Importieren der erforderlichen Module

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in Ihr Projekt in eine Datei namens `sentence-length.py`.

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
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Der globale Endpunkt der Textübersetzung ist als die `base_url` festgelegt. `path` legt die `breaksentence`-Route fest und gibt die gewünschte Version der API (Version 3) an.

Die Parameter (`params`) in diesem Beispiel werden verwendet, um die Sprache für den angegebenen Text festzulegen. `params` sind für die `breaksentence`-Route nicht erforderlich. Wenn die Angabe in der Anforderung weggelassen wird, versucht die API, die Sprache des angegebenen Texts zu erkennen, und stellt diese Informationen mit einer Zuverlässigkeitsbewertung in der Antwort bereit.

>[!NOTE]
> Weitere Informationen zu Endpunkten, Routen und Anforderungsparametern finden Sie unter [Textübersetzungs-API 3.0: Sprachen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'
params = '&language=en'
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

## <a name="create-a-request-to-determine-sentence-length"></a>Erstellen einer Anforderung zum Ermitteln der Satzlänge

Definieren Sie den Satz (bzw. die Sätze), deren Länge Sie bestimmen möchten:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
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
python sentence-length.py
```

[Auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) finden Sie das vollständige Beispiel, falls Sie Ihren Code mit unserem Code vergleichen möchten.

## <a name="sample-response"></a>Beispiel für eine Antwort

```json
[
    {
        "sentLen": [
            13,
            11,
            22
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
* [Ermitteln alternativer Übersetzungen](quickstart-python-dictionary.md)
* [Abrufen einer Liste der unterstützten Sprachen](quickstart-python-languages.md)
