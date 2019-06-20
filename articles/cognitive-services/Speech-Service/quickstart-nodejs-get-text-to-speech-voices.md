---
title: 'Schnellstart: Auflisten von Stimmen für Text-to-Speech, Node.js – Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Node.js die vollständige Liste der Standard- und neuronalen Stimmen für eine Region bzw. einen Endpunkt erhalten. Die Liste wird als JSON zurückgegeben, und die Verfügbarkeit der Stimmen variiert je nach Region.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 08936ca0fe2fe10c332df146edd541c75df325e0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067577"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Schnellstart: Abrufen der Liste der Stimmen für Text-to-Speech mit Node.js

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Node.js die vollständige Liste der Standard- und neuronalen Stimmen für eine Region bzw. einen Endpunkt erhalten. Die Liste wird als JSON zurückgegeben, und die Verfügbarkeit der Stimmen variiert je nach Region. Eine Liste der unterstützten Regionen finden Sie unter [Regionen](regions.md).

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Ressource für den Speech-Dienst benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](get-started.md) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* [Node 8.12.x oder höher](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Editor
* Ein Azure-Abonnementschlüssel für die Speech-Dienste. [Hier erhalten Sie einen kostenlosen Schlüssel.](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Erstellen eines Projekts und der erforderlichen Abhängigkeiten

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Node.js-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in Ihr Projekt in eine Datei namens `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Wenn Sie diese Module bisher nicht verwendet haben, müssen Sie sie vor der Ausführung Ihres Programms installieren. Führen Sie zum Installieren dieser Pakete `npm install request request-promise` aus.

## <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Alle Anforderungen an die Text-to-Speech-REST-API erfordern ein Zugriffstoken für die Authentifizierung. Um ein Zugriffstoken anzufordern, ist ein Austausch erforderlich. In dieser Funktion wird Ihr Abonnementschlüssel für den Speech-Dienst unter Verwendung des `issueToken`-Endpunkts gegen ein Zugriffstoken ausgetauscht.

In diesem Beispiel wird vorausgesetzt, dass Ihr Abonnement für den Speech-Dienst in der Region „USA, Westen“ enthalten ist. Wenn Sie eine andere Region verwenden, aktualisieren Sie den Wert für `uri`. Eine vollständige Liste finden Sie unter [Regionen](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopieren Sie diesen Code in Ihr Projekt:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Weitere Informationen zur Authentifizierung finden Sie unter [Authentifizieren mit einem Authentifizierungstoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Im nächsten Abschnitt erstellen wir die Funktion, um die Liste der Stimmen zu erhalten und die JSON-Ausgabe in einer Datei zu speichern.

## <a name="make-a-request-and-save-the-response"></a>Senden der Anforderung und Speichern der Antwort

An dieser Stelle werden Sie die Anforderung erstellen und die Liste der zurückgegebenen Stimmen speichern. In diesem Beispiel wird davon ausgegangen, dass Sie den Endpunkt „USA, Westen“ verwenden. Wenn Ihre Ressource in einer anderen Region registriert ist, stellen Sie sicher, dass Sie die `uri` aktualisieren. Weitere Informationen finden Sie unter [Speech-Dienstregionen](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Als Nächstes fügen Sie erforderliche Header für die Anforderung hinzu. Abschließend stellen Sie eine Anforderung an den Dienst. Wenn die Anforderung erfolgreich war und der Statuscode 200 zurückgegeben wird, wird die Antwort in eine Datei geschrieben.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Sie sind fast fertig. Der letzte Schritt ist das Erstellen einer asynchronen Funktion. Diese Funktion liest Ihren Abonnementschlüssel aus einer Umgebungsvariablen, ruft ein Token ab, wartet, bis die Anforderung abgeschlossen wurde, schreibt dann die JSON-Antwort in eine Datei.

Wenn Sie mit Umgebungsvariablen nicht vertraut sind oder es vorziehen, mit Ihrem Abonnementschlüssel zu testen, der als Zeichenfolge hartcodiert ist, ersetzen Sie `process.env.SPEECH_SERVICE_KEY` durch Ihren Abonnementschlüssel als Zeichenfolge.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Sie können Ihre Beispiel-App jetzt ausführen. Navigieren Sie über die Befehlszeile (oder Terminalsitzung) zu Ihrem Projektverzeichnis, und führen Sie Folgendes aus:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie unbedingt alle vertraulichen Informationen wie etwa Abonnementschlüssel aus dem Quellcode Ihrer Beispiel-App.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Node.js-Beispiele auf GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zur Text-to-Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Erstellen benutzerdefinierter Voicefonts](how-to-customize-voice-font.md)
* [Aufzeichnen von Sprachbeispielen zum Erstellen einer benutzerdefinierten Stimme](record-custom-voice-samples.md)
