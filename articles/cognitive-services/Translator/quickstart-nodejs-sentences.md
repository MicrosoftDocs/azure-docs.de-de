---
title: 'Schnellstart: Ermitteln der Satzlänge, Node.js: Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Satzlängen (in Zeichen) ermitteln, indem Sie Node.js und die Textübersetzungs-REST-API verwenden.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 2046c0712d392771cc973de9a431191173533435
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515138"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-with-nodejs"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Ermitteln der Satzlänge mit Node.js

In dieser Schnellstartanleitung wird beschrieben, wie Sie Satzlängen (in Zeichen) ermitteln, indem Sie Node.js und die Textübersetzungs-REST-API verwenden.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Textübersetzungsressource benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* [Node 8.12.x oder höher](https://nodejs.org/en/)
* Ein Azure-Abonnementschlüssel für die Textübersetzung

## <a name="create-a-project-and-import-required-modules"></a>Erstellen eines Projekts und Importieren der erforderlichen Module

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in Ihr Projekt in eine Datei namens `sentence-length.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Wenn Sie diese Module bisher nicht verwendet haben, müssen Sie sie vor der Ausführung Ihres Programms installieren. Führen Sie zum Installieren dieser Pakete `npm install request uuidv4` aus.

Diese Module sind zum Erstellen der HTTP-Anforderung und eines eindeutigen Bezeichners für den `'X-ClientTraceId'`-Header erforderlich.

## <a name="set-the-subscription-key"></a>Festlegen des Abonnementschlüssels

Dieser Code liest den Textübersetzungs-Abonnementschlüssel aus der Umgebungsvariablen `TRANSLATOR_TEXT_KEY`. Wenn Sie mit Umgebungsvariablen nicht vertraut sind, können Sie `subscriptionKey` als Zeichenfolge festlegen und die Bedingungsanweisung auskommentieren.

Kopieren Sie diesen Code in Ihr Projekt:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Konfigurieren der Anforderung

Mit der über das Anforderungsmodul zur Verfügung gestellten `request()`-Methode können Sie die HTTP-Methode, die URL, Anforderungsparameter und den JSON-Text als `options`-Objekt übergeben. In diesem Codeausschnitt wird die Anforderung konfiguriert:

>[!NOTE]
> Weitere Informationen zu Endpunkten, Routen und Anforderungsparametern finden Sie unter [Textübersetzungs-API 3.0: BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

Eine Anforderung lässt sich am einfachsten authentifizieren, indem Sie den Abonnementschlüssel als `Ocp-Apim-Subscription-Key`-Header übergeben. Diese Vorgehensweise wird in diesem Beispiel verwendet. Alternativ können Sie den Abonnementschlüssel durch ein Zugriffstoken ersetzen und dieses als `Authorization`-Header übergeben, um Ihre Anforderung zu überprüfen. Weitere Informationen finden Sie unter [Authentifizierung](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Senden der Anforderung und Ausgeben der Antwort

Erstellen Sie als Nächstes mithilfe der `request()`-Methode die Anforderung. Sie verwendet das im vorherigen Abschnitt erstellte `options`-Objekt als erstes Argument und gibt dann die übersichtlicher gestaltete JSON-Antwort aus.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> In diesem Beispiel wird die HTTP-Anforderung im `options`-Objekt definiert. Das Anforderungsmodul unterstützt außerdem praktische Methoden wie `.post` und `.get`. Weitere Informationen finden Sie unter [Convenience methods](https://github.com/request/request#convenience-methods) (Praktische Methoden).

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Das war's: Sie haben ein einfaches Programm erstellt, das die Textübersetzungs-API aufruft und eine JSON-Antwort zurückgibt. Führen Sie das Programm jetzt aus:

```console
node sentence-length.js
```

[Auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) finden Sie das vollständige Beispiel, falls Sie Ihren Code mit unserem Code vergleichen möchten.

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
> [Node.js-Beispiele auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Weitere Informationen

Sie können die Textübersetzungs-API nicht nur für die Spracherkennung, sondern auch für Folgendes verwenden:

* [Übersetzen von Text](quickstart-nodejs-translate.md)
* [Transliteration von Text](quickstart-nodejs-transliterate.md)
* [Identifizieren der Sprache anhand der Eingabe](quickstart-nodejs-detect.md)
* [Ermitteln alternativer Übersetzungen](quickstart-nodejs-dictionary.md)
* [Abrufen einer Liste der unterstützten Sprachen](quickstart-nodejs-languages.md)
