---
title: Erste Schritte mit der Bing-Spracheingabe-API von Microsoft in JavaScript | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Bing-Spracheingabe-API in Cognitive Services, um Anwendungen zu entwickeln, die gesprochene Audiosignale fortlaufend in Text konvertieren.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515212"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Erste Schritte mit der Spracherkennungs-API in JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Sie können Anwendungen entwickeln, die gesprochenes Audio mithilfe der Spracherkennungs-API in Text umwandeln. Mit dem von der JavaScript-Clientbibliothek verwendeten [WebSocket-Protokoll des Speech-Diensts](../API-Reference-REST/websocketprotocol.md) können Sie gleichzeitig sprechen und den transkribierten Text empfangen. Dieser Artikel hilft Ihnen bei den ersten Schritten mit der Spracherkennungs-API in JavaScript.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Spracherkennungs-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services. Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

> [!IMPORTANT]
> Rufen Sie einen Abonnementschlüssel ab. Für die Verwendung von Speech-Clientbibliotheken ist ein [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/) erforderlich.

## <a name="get-started"></a>Erste Schritte

In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie eine HTML-Beispielseite laden. Das Beispiel befindet sich unserem [GitHub-Repository](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Sie können das Beispiel **direkt über das Repository** oder **über eine lokale Kopie des Repositorys** öffnen.

> [!NOTE]
> Bei einigen Browsern wird das Mikrofon im Falle eines unsicheren Ursprungs blockiert. Es empfiehlt sich daher, das Beispiel/Ihre App per HTTPS zu hosten, damit es/sie auf allen unterstützten Browsern funktioniert.

### <a name="open-the-sample-directly"></a>Direktes Öffnen des Beispiels

Beziehen Sie wie weiter oben beschrieben einen Abonnementschlüssel. Öffnen Sie dann den [Link zum Beispiel](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Daraufhin wird die Seite in Ihrem Standardbrowser geladen und unter Verwendung von [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com) gerendert.

### <a name="open-the-sample-from-a-local-copy"></a>Öffnen des Beispiels über eine lokale Kopie

Wenn Sie das Beispiel lokal verwenden möchten, klonen Sie das folgende Repository:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Kompilieren Sie die TypeScript-Quellen, und bündeln Sie sie in einer einzelnen JavaScript-Datei. (Auf Ihrem Computer muss [npm](https://www.npmjs.com/) installiert sein.) Wechseln Sie zum Stamm des geklonten Repositorys, und führen Sie die folgenden Befehle aus:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Öffnen Sie `samples\browser\Sample.html` in Ihrem bevorzugten Browser.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Integration des SDK in Ihre eigene Webseite finden Sie [hier](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Anmerkungen

- Die Spracherkennungs-API unterstützt drei [Erkennungsmodi](../concepts.md#recognition-modes). Der Modus kann durch Aktualisieren der Funktion **Setup()** in der Datei „Sample.html“ geändert werden. Im Beispiel ist der Modus standardmäßig auf `Interactive` festgelegt. Wenn Sie den Modus ändern möchten, legen Sie den Parameter `SR.RecognitionMode.Interactive` auf einen anderen Modus fest. Ändern Sie den Parameter beispielsweise in `SR.RecognitionMode.Conversation`.
- Eine vollständige Liste mit unterstützten Sprachen finden Sie unter [Unterstützte Sprachen](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Verwandte Themen

- [JavaScript-Beispielrepository für die Spracherkennungs-API](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Erste Schritte mit der REST-API](GetStartedREST.md)
