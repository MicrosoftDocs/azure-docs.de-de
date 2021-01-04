---
title: Regionen für den Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Eine Liste der verfügbaren Regionen und Endpunkte für den Speech-Dienst, einschließlich Spracherkennung, Sprachsynthese und Sprachübersetzung.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b872340c50c800ebe4d4d6c7ca56b3b327bf162
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972645"
---
# <a name="speech-service-supported-regions"></a>Vom Speech-Dienst unterstützte Regionen

Mithilfe des Speech-Diensts kann Ihre Anwendung Sprache in Text konvertieren, Sprachübersetzungen ausführen und Text in Sprache konvertieren. Der Dienst ist in mehreren Regionen mit eindeutigen Endpunkten für das Speech SDK und REST-APIs verfügbar.

Das Sprachportal zum Durchführen benutzerdefinierter Konfigurationen für Ihre Sprachfunktionen für alle Regionen finden Sie hier: https://speech.microsoft.com

Beim Erwägen einer Region sollten Sie die folgenden Punkte bedenken:

* Wenn Ihre Anwendung ein [Speech SDK](speech-sdk.md) verwendet, geben Sie beim Erstellen einer Sprachkonfiguration den Regionsbezeichner (z. B. `westus`) an.
* Wenn Ihre Anwendung eine der [REST-APIs](./overview.md#reference-docs) des Sprachdiensts verwendet, ist die Region Teil des Endpunkt-URIs, den Sie bei der Anforderung verwenden.
* Schlüssel, die für eine Region erstellt wurden, sind nur in dieser Region gültig. Der Versuch, sie mit anderen Regionen zu verwenden, führt zu Authentifizierungsfehlern.

## <a name="speech-sdk"></a>Sprach-SDK

Im [Speech SDK](speech-sdk.md) werden Regionen als Zeichenfolge angegeben (z.B. als Parameter für `SpeechConfig.FromSubscription` im Speech SDK für C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spracherkennung, Sprachsynthese und Übersetzung

Das Speech-Anpassungsportal ist hier verfügbar: https://speech.microsoft.com

Der Speech-Dienst ist in den folgenden Regionen für **Spracherkennung**, **Sprachsynthese** und **Übersetzung** verfügbar:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Wenn Sie das [Speech SDK](speech-sdk.md) verwenden, werden die Regionen durch den **Regionsbezeichner** angegeben (z. B. als Parameter von `SpeechConfig.FromSubscription`). Achten Sie darauf, dass die Region mit der Region Ihres Abonnements übereinstimmt.

### <a name="intent-recognition"></a>Absichtserkennung

Verfügbare Regionen für **Absichtserkennung** über das Speech SDK sind die folgenden:

| Globale Region | Region           | Regionsbezeichner |
| ------------- | ---------------- | -------------------- |
| Asia          | Asien, Osten        | `eastasia`           |
| Asia          | Asien, Südosten   | `southeastasia`      |
| Australien     | Australien (Osten)   | `australiaeast`      |
| Europa        | Nordeuropa     | `northeurope`        |
| Europa        | Europa, Westen      | `westeurope`         |
| Nordamerika | East US          | `eastus`             |
| Nordamerika | USA (Ost) 2        | `eastus2`            |
| Nordamerika | USA Süd Mitte | `southcentralus`     |
| Nordamerika | USA, Westen-Mitte  | `westcentralus`      |
| Nordamerika | USA (Westen)          | `westus`             |
| Nordamerika | USA, Westen 2        | `westus2`            |
| Südamerika | Brasilien Süd     | `brazilsouth`        |

Dies ist eine Teilmenge der Veröffentlichungsregionen, die vom [Language Understanding Intelligent Service (LUIS)](../luis/luis-reference-regions.md) unterstützt werden.

### <a name="voice-assistants"></a>Sprachassistenten

Das [Speech SDK](speech-sdk.md) unterstützt Funktionen des **Sprachassistenten** in den folgenden Regionen:

| Region         | Regionsbezeichner |
| -------------- | -------------------- |
| USA (Westen)        | `westus`             |
| USA, Westen 2      | `westus2`            |
| East US        | `eastus`             |
| USA (Ost) 2      | `eastus2`            |
| Europa, Westen    | `westeurope`         |
| Nordeuropa   | `northeurope`        |
| Asien, Südosten | `southeastasia`      |

### <a name="speaker-recognition"></a>Sprechererkennung

Sprechererkennung ist derzeit nur in der Region `westus` verfügbar.

## <a name="rest-apis"></a>REST-APIs

Der Speech-Dienst macht auch REST-Endpunkte für Spracherkennungs- und Sprachsyntheseanforderungen verfügbar.

### <a name="speech-to-text"></a>Spracherkennung

Die Referenzdokumentation zur Spracherkennung finden Sie unter [Spracherkennungs-REST-API](rest-speech-to-text.md).

Der Endpunkt für die REST-API weist das folgende Format auf:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Der Sprachparameter muss an die URL angefügt werden, um HTTP Fehler des Typs „4xx“ zu vermeiden. Das folgende Beispiel zeigt die Spracheinstellung „Englisch (USA)“ bei Verwendung des Endpunkt „USA, Westen“: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Text-zu-Sprache

Die Referenzdokumentation zur Sprachsynthese finden Sie unter [Text-to-Speech-REST-API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]