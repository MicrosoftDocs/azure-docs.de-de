---
title: Sprachübersetzung mit den Azure-Sprachdiensten
titlesuffix: Azure Cognitive Services
description: Mit den Sprachdiensten können Sie Ihren Anwendungen, Tools und Geräten End-to-End- und Echtzeit-Sprachübersetzungen sowie mehrsprachige Übersetzungen hinzufügen. Die gleiche API kann für Speech-to-Speech- und für Speech-to-Text-Übersetzungen verwendet werden.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 84d212129c5225fd0efebfca5640cfc3d32e8a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072411"
---
# <a name="what-is-speech-translation"></a>Was ist Sprachübersetzung?

Die Sprachübersetzung aus den Azure-Sprachdiensten ermöglicht eine mehrsprachige Sprache-zu-Sprache- und eine Sprache-zu-Text-Übersetzung von Audiostreams in Echtzeit. Mit dem Speech SDK haben Ihre Anwendungen, Tools und Geräte Zugriff auf Quelltranskriptionen und Übersetzungsausgaben für bereitgestelltes Audio. Die Zwischenergebnisse der Transkription und Übersetzung werden zurückgegeben, wenn Sprache erkannt wird, und die Endergebnisse können in synthetisierte Sprache konvertiert werden.

Der Übersetzungsengine von Microsoft liegen zwei unterschiedliche Ansätze zugrunde: statistische maschinelle Übersetzung (Statistical Machine Translation, SMT) und neuronale maschinelle Übersetzung (Neural Machine Translation, NMT). SMT verwendet erweiterte statistische Analysen, um die bestmöglichen Übersetzungen im Kontext einiger weniger Wörter zu schätzen. Mit NMT werden neuronale Netze verwendet, um genauere, natürlich klingende Übersetzungen bereitzustellen, indem der gesamte Kontext von Sätzen zur Übersetzung von Wörtern genutzt wird.

Heute verwendet Microsoft NMT für die Übersetzung in die gängigsten Sprachen. Alle [für die Speech-to-Speech-Übersetzung verfügbaren Sprachen](language-support.md#speech-translation) werden von NMT unterstützt. Bei der Speech-to-Speech-Übersetzung kann je nach Sprachkombination SMT oder NMT verwendet werden. Wenn die Zielsprache von NMT unterstützt wird, wird die gesamte Übersetzung über NMT abgewickelt. Wenn die Zielsprache nicht von NMT unterstützt wird, wird für die Übersetzung eine Kombination aus NMT und SMT mit Englisch als „Pivot“ zwischen den beiden Sprachen verwendet.

## <a name="core-features"></a>Wichtige Funktionen

In der folgenden Tabelle sind die Funktionen des Speech SDK und der REST-APIs aufgelistet:

| Anwendungsfall | SDK | REST |
|----------|-----|------|
| Sprache-zu-Text-Übersetzung mit Erkennungsergebnissen. | Ja | Nein |
| Sprache-zu-Sprache-Übersetzung. | Ja | Nein |
| Zwischenergebnisse der Spracherkennung und Übersetzung. | Ja | Nein |

## <a name="get-started-with-speech-translation"></a>Erste Schritte mit der Sprachübersetzung

Wir bieten Schnellstarts an, die so konzipiert sind, dass Sie in weniger als 10 Minuten Code ausführen können. In dieser Tabelle werden Schnellstarts Sprachübersetzungen nach Sprache aufgelistet.

| Schnellstart | Plattform | API-Referenz |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Beispielcode

Beispielcode für das Speech SDK finden Sie auf GitHub. In den Beispielen werden gängige Szenarien wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und einmalige Erkennung/Übersetzung oder die Verwendung benutzerdefinierter Modelle behandelt.

* [Beispiele für Spracherkennung und Übersetzung (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrationsleitfäden

> [!WARNING]
> Die Sprachübersetzung wird am 15. Oktober 2019 eingestellt.

Wenn Ihre Anwendungen, Tools oder Produkte die Sprachübersetzung verwenden, finden Sie in den nachfolgend aufgelisteten Leitfäden Informationen zur Migration zu den Sprachdiensten.

* [Migrieren von der Sprachübersetzungs-API zum Spracherkennungsdienst](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenz

* [Speech SDK](speech-sdk-reference.md)
* [Speech-Geräte-SDK](speech-devices-sdk.md)
* [REST-API: Spracherkennung](rest-speech-to-text.md)
* [REST-API: Sprachsynthese](rest-text-to-speech.md)
* [REST-API: Batchtranskription und Anpassung](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nächste Schritte

* [Beziehen eines kostenlosen Abonnementschlüssels für die Spracherkennungsdienste](get-started.md)
* [Abrufen des Speech SDK](speech-sdk.md)
