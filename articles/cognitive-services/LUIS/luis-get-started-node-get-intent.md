---
title: Abrufen der Absicht, Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: In dieser Schnellstartanleitung bestimmen Sie mithilfe einer verfügbaren öffentlichen LUIS-App aus Konversationstext die Absicht eines Benutzers. Mithilfe von Node.js senden Sie die Absicht des Benutzers als Text an den HTTP-Vorhersageendpunkt der öffentlichen App.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: 161056aa2cfe43375d5e555197b57f85685ca219
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594108"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Schnellstart: Abrufen der Absicht mit Node.js

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Voraussetzungen

* Programmiersprache [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID der öffentlichen App: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> Die vollständige Node.js-Lösung steht im [GitHub-Repository mit **Azure-Beispielen**](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node) zur Verfügung.

## <a name="get-luis-key"></a>Abrufen des LUIS-Schlüssels

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Abrufen der Absicht im Browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Programmgesteuertes Abrufen der Absicht

Sie können mit Node.js auf die gleichen Ergebnisse zugreifen, die im vorherigen Schritt im Browserfenster angezeigt wurden.

1. Kopieren Sie den folgenden Codeausschnitt:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Erstellen Sie die `.env`-Datei mit dem folgenden Text, oder legen Sie diese Variablen in der Umgebung fest:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Legen Sie die Umgebungsvariable `LUIS_ENDPOINT_KEY` auf Ihren Schlüssel fest.

4. Installieren Sie Abhängigkeiten, indem Sie den folgenden Befehl an der Befehlszeile ausführen: `npm install`.

5. Führen Sie den Code mit `npm start` aus. Es werden die gleichen Werte wie zuvor im Browserfenster angezeigt.

## <a name="luis-keys"></a>LUIS-Schlüssel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Node.js-Datei.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Hinzufügen von Äußerungen](luis-get-started-node-add-utterance.md)
