---
title: Übersicht zur Sprachübersetzung – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Mit der Sprachübersetzung können Sie Ihren Anwendungen, Tools und Geräten End-to-End- und Echtzeit-Sprachübersetzungen sowie mehrsprachige Übersetzungen hinzufügen. Die gleiche API kann für Speech-to-Speech- und für Speech-to-Text-Übersetzungen verwendet werden. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Sprachübersetzungsdiensts.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: Sprachübersetzung
ms.openlocfilehash: 36c2943d7cd5ce2c5c7c4a8501dc0033aa8c9cbc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015204"
---
# <a name="what-is-speech-translation"></a>Was ist Sprachübersetzung?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In dieser Übersicht lernen Sie die Vorteile und Funktionen des Sprachübersetzungsdiensts kennen, der eine mehrsprachige Sprache-zu-Sprache- und eine Sprache-zu-Text-Übersetzung von Audiostreams in Echtzeit ermöglicht. Mit dem Speech SDK haben Ihre Anwendungen, Tools und Geräte Zugriff auf Quelltranskriptionen und Übersetzungsausgaben für bereitgestelltes Audio. Die Zwischenergebnisse der Transkription und Übersetzung werden zurückgegeben, wenn Sprache erkannt wird, und die Endergebnisse können in synthetisierte Sprache konvertiert werden.

Der Übersetzungsengine von Microsoft liegen zwei unterschiedliche Ansätze zugrunde: statistische maschinelle Übersetzung (Statistical Machine Translation, SMT) und neuronale maschinelle Übersetzung (Neural Machine Translation, NMT). SMT verwendet erweiterte statistische Analysen, um die bestmöglichen Übersetzungen im Kontext einiger weniger Wörter zu schätzen. Mit NMT werden neuronale Netze verwendet, um genauere, natürlich klingende Übersetzungen bereitzustellen, indem der gesamte Kontext von Sätzen zur Übersetzung von Wörtern genutzt wird.

Heute verwendet Microsoft NMT für die Übersetzung in die gängigsten Sprachen. Alle [für die Speech-to-Speech-Übersetzung verfügbaren Sprachen](language-support.md#speech-translation) werden von NMT unterstützt. Bei der Speech-to-Speech-Übersetzung kann je nach Sprachkombination SMT oder NMT verwendet werden. Wenn die Zielsprache von NMT unterstützt wird, wird die gesamte Übersetzung über NMT abgewickelt. Wenn die Zielsprache nicht von NMT unterstützt wird, wird für die Übersetzung eine Kombination aus NMT und SMT mit Englisch als „Pivot“ zwischen den beiden Sprachen verwendet.

## <a name="core-features"></a>Wichtige Funktionen

* Sprache-zu-Text-Übersetzung mit Erkennungsergebnissen.
* Sprache-zu-Sprache-Übersetzung.
* Unterstützung des Übersetzens von Sprache in mehrere Zielsprachen.
* Zwischenergebnisse der Spracherkennung und Übersetzung.

## <a name="get-started"></a>Erste Schritte 

Verwenden Sie die [Schnellstartanleitung](get-started-speech-translation.md), um mit der Sprachübersetzung zu beginnen. Der Sprachübersetzungsdienst ist über das [Speech SDK](speech-sdk.md) und die [Speech CLI](spx-overview.md) verfügbar.

## <a name="sample-code"></a>Beispielcode

Beispielcode für das Speech SDK finden Sie auf GitHub. In den Beispielen werden gängige Szenarien wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und einmalige Erkennung/Übersetzung oder die Verwendung benutzerdefinierter Modelle behandelt.

* [Beispiele für Spracherkennung und Übersetzung (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrationsleitfäden

Wenn Ihre Anwendungen, Tools oder Produkte die [Sprachübersetzungs-API](./how-to-migrate-from-translator-speech-api.md) verwenden, finden Sie in den nachfolgend aufgelisteten Leitfäden Informationen zur Migration zum Speech-Dienst.

* [Migrieren von der Sprachübersetzungs-API zum Speech-Dienst](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenz

* [Speech SDK](./speech-sdk.md)
* [Speech-Geräte-SDK](speech-devices-sdk.md)
* [REST-API: Spracherkennung](rest-speech-to-text.md)
* [REST-API: Sprachsynthese](rest-text-to-speech.md)
* [REST-API: Batchtranskription und Anpassung](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Nächste Schritte

* Ausführen des [Schnellstarts](get-started-speech-translation.md) zur Sprachübersetzung
* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Abrufen des Speech SDK](speech-sdk.md)