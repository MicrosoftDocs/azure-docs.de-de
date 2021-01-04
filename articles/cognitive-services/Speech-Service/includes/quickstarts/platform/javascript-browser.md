---
title: 'Schnellstart: Speech SDK für JavaScript (Browser) – Plattformeinrichtung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform mit dem Speech Service SDK für die Verwendung von JavaScript (Browser) einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188282"
---
In diesem Leitfaden ist beschrieben, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für JavaScript zur Verwendung mit einer Webseite installieren.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Erstellen eines neuen Websiteordners

Erstellen Sie einen neuen leeren Ordner. Wenn Sie das Beispiel auf einem Webserver hosten möchten, stellen Sie sicher, dass der Webserver auf den Ordner zugreifen kann.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Entpacken des Speech SDK für JavaScript in diesen Ordner

Laden Sie das Speech SDK als [ZIP-Paket](https://aka.ms/csspeech/jsbrowserpackage) herunter, und entpacken Sie es in den neu erstellten Ordner. Fünf Dateien werden entpackt:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` Eine für Menschen lesbare Version des Speech SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Eine Zuordnungsdatei, die zum Debuggen von SDK-Code verwendet wird.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` Objektdefinitionen, die mit TypeScript verwendet werden.
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Eine minimierte Version des Speech SDK.
* `speech-processor.js` Code zum Verbessern der Leistung in einigen Browsern.

## <a name="create-an-indexhtml-page"></a>Erstellen einer Seite „index.html“

Erstellen Sie eine neue Datei im Ordner `index.html`, und öffnen Sie diese Datei mit einem Text-Editor.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]