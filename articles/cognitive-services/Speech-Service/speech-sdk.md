---
title: Informationen zum Speech SDK – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Das Speech SDK (Software Development Kit) macht viele der Funktionen des Spracherkennungsdienstes verfügbar und erleichtert Ihnen das Entwickeln sprachaktivierter Anwendungen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 9356cbb6eb210d6745d90e8ad001917e73592cd3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96021984"
---
# <a name="about-the-speech-sdk"></a>Informationen zum Speech SDK

Das Speech SDK (Software Development Kit) macht viele der Funktionen des Speech-Dienstes verfügbar und ermöglicht Ihnen das Entwickeln sprachaktivierter Anwendungen. Das Speech SDK ist in vielen Programmiersprachen und für alle Plattformen verfügbar.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Funktionen für Szenarien

Das Speech SDK macht eine Vielzahl von Features aus dem Spracherkennungsdienst verfügbar, jedoch nicht alle. Die Funktionen des Speech SDK sind häufig mit Szenarien verknüpft. Das Speech SDK eignet sich ideal für Echtzeit- und Nicht-Echtzeitszenarien, für die Verwendung lokaler Geräte, Dateien, von Azure-Blobspeicher und sogar für Eingabe-und Ausgabestreams. Wenn ein Szenario für das Speech SDK nicht zugänglich ist, suchen Sie nach einer REST-API-Alternative.

### <a name="speech-to-text"></a>Spracherkennung

Die [Spracherkennung](speech-to-text.md) (auch als *Speech-to-Text* bezeichnet) wandelt Audiodatenströme in Text um, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. In Kombination mit [Language Understanding (LUIS)](../luis/index.yml) können Sie Benutzerabsichten aus transkribierter Sprache ableiten und auf Sprachbefehle reagieren. Verwenden Sie die [Sprachübersetzung](speech-translation.md), um Spracheingaben mit einem einzigen Aufruf in eine andere Sprache zu übersetzen. Weitere Informationen finden Sie unter [Spracherkennung – Grundlagen](./get-started-speech-to-text.md).

**Spracherkennung (SR), Begriffsliste, Absicht, Übersetzung und lokale Container** sind auf den folgenden Plattformen verfügbar:

  - C++/Windows und Linux und macOS
  - C# (Framework und .NET Core)/Windows, UWP, Unity, Xamarin, Linux und macOS
  - Java (JRE und Android)
  - JavaScript (Browser und NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (nur SR)

### <a name="text-to-speech"></a>Text-zu-Sprache

[Text-zu-Sprache](text-to-speech.md) (auch als *Sprachsynthese* bezeichnet) konvertiert Text in menschenähnliche synthetische Sprache. Beim Eingabetext handelt es sich um Zeichenfolgenliterale, oder Sie können [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) verwenden. Weitere Informationen zu standardmäßigen oder neuronalen Stimmen finden Sie unter [Sprach- und Stimmunterstützung für Text-zu-Sprache](language-support.md#text-to-speech).

**Text-zu-Sprache** ist auf den folgenden Plattformen verfügbar:

  - C++/Windows und Linux
  - C#/Windows, UWP und Unity
  - Java (JRE und Android)
  - Python
  - Swift
  - Objective-C
  - Die TTS-REST-API kann in allen anderen Situationen verwendet werden.

### <a name="voice-assistants"></a>Sprachassistenten

[Sprachassistenten](voice-assistants.md), die das Speech SDK verwenden, ermöglichen es Entwicklern, natürliche Konversationsschnittstellen für ihre Anwendungen und Umgebungen zu erstellen, die der menschlichen Sprache nahekommen. Der Sprachassistentendienst ermöglicht die schnelle, zuverlässige Interaktion zwischen einem Gerät und einem Assistenten. Die Implementierung verarbeitet Aufgaben über den Direct Line Speech-Kanal des Bot Framework oder den integrierten Dienst Benutzerdefinierte Befehle (Vorschauversion). Darüber hinaus können Sprachassistenten benutzerdefinierte Stimmen verwenden, die über das [Custom Voice-Portal](https://aka.ms/customvoice) erstellt werden, um eine unverwechselbare Sprachausgabe zu erzielen.

**Sprach-Assistent** ist auf den folgenden Plattformen verfügbar:

  - C++/Windows und Linux und macOS
  - C#/Windows
  - Java/Windows und Linux und macOS und Android (Speech Devices SDK)

#### <a name="keyword-spotting"></a>Schlüsselworterkennung

Das Konzept der [-Schlüsselworterkennung](./custom-keyword-basics.md) wird im Speech SDK unterstützt. Bei der Schlüsselworterkennung wird ein Schlüsselwort in der Sprache erkannt, und nach der Erfassung des Schlüsselworts folgt eine Aktion. Mit dem Schlüsselwort „Hey Cortana“ wird beispielsweise der Cortana-Assistent aktiviert.

**Schlüsselworterkennung** ist auf den folgenden Plattformen verfügbar:

  - C++/Windows und Linux
  - C#/Windows und Linux
  - Python/Windows und Linux
  - Java/Windows und Linux und Android (Speech Devices SDK)
  - Die Funktionalität zur Schlüsselworterkennung (Keyword Spotting, KWS) kann eventuell mit allen Mikrofontypen verwendet werden, offiziell wird KWS derzeit jedoch nur für die Mikrofonarrays in der Azure Kinect DK-Hardware oder im Speech-Geräte-SDK unterstützt.

### <a name="meeting-scenarios"></a>Besprechungsszenarien

Das Speech SDK eignet sich perfekt für die Transkription von Besprechungsszenarien, egal, ob aus einer Unterhaltung von einem einzelnen Gerät oder von mehreren Geräten.

#### <a name="conversation-transcription"></a>Unterhaltungstranskription

Die [Unterhaltungstranskription](conversation-transcription.md) ermöglicht die Echtzeit- (und asynchrone) Spracherkennung, die Sprecheridentifikation und die Satzzuschreibung zu den einzelnen Sprechern (auch als *Diarisierung* bezeichnet). Er eignet sich optimal für das Transkribieren persönlicher Besprechungen, wobei zwischen Sprechern unterschieden werden kann.

**Unterhaltungstranskription** ist auf den folgenden Plattformen verfügbar:

  - C++/Windows und Linux
  - C# (Framework und .NET Core)/Windows und UWP und Linux
  - Java/Windows und Linux und Android (Speech Devices SDK)

#### <a name="multi-device-conversation"></a>Konversation mit mehreren Geräten

Über die [Konversation mit mehreren Geräten](multi-device-conversation.md) verbinden Sie mehrere Geräte oder Clients in einer Konversation, um sprach- oder textbasierte Nachrichten mit einfacher Unterstützung von Transkription und Übersetzung zu senden.

**Konversation mit mehreren Geräten** ist auf den folgenden Plattformen verfügbar:

  - C++/Windows
  - C# (Framework und .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Benutzerdefinierte/Agent-Szenarios

Das Speech SDK kann für die Transkription in Callcenterszenarien verwendet werden, in denen Telefoniedaten generiert werden.

#### <a name="call-center-transcription"></a>Callcentertranskription

Die [Callcentertranskription](call-center-transcription.md) ist ein häufiges Szenario für die Spracherkennung zum Transkribieren großer Mengen von Telefoniedaten, die aus verschiedenen Systemen stammen können, z. B. Systemen für interaktive Sprachantworten (Interactive Voice Response, IVR). Die neuesten Spracherkennungsmodelle des Speech-Diensts zeichnen sich durch das Transkribieren dieser Telefoniedaten auch in solchen Fällen aus, in denen die Daten für den Menschen schwer verständlich sind.

**Callcentertranskription** steht über die REST-API des Batch-Sprachdiensts zur Verfügung und kann in jeder Situation verwendet werden.

### <a name="codec-compressed-audio-input"></a>Per Codec komprimierte Audioeingabe

Einige der Speech SDK-Programmiersprachen unterstützen per Codec komprimierte Audioeingabestreams. Weitere Informationen finden Sie unter <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">Verwenden komprimierter Audioeingabeformate<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

**Per Codec komprimierte Audioeingabe** ist auf den folgenden Plattformen verfügbar:

  - C++/Linux
  - C#/Linux
  - Java/Linux, Android und iOS

## <a name="rest-api"></a>REST-API

Obwohl das Speech SDK viele Features des Spracherkennungsdiensts abdeckt, sollten Sie in einigen Szenarien die REST-API verwenden.

### <a name="batch-transcription"></a>Batch-Transkription

Die [Batch-Transkription](batch-transcription.md) ermöglicht eine asynchrone Spracherkennung/Transkription großer Datenmengen. Die Batch-Transkription ist nur über die REST-API möglich. Außer der Konvertierung von Sprachaudiodaten in Text sind mit der Batch-Spracherkennung auch die Diarisierung und Stimmungsanalyse möglich.

## <a name="customization"></a>Anpassung

Der Spracherkennungsdienst bietet über seine Standardmodelle hervorragende Funktionen – von Spracherkennung und Text-to-Speech bis hin zu Sprachübersetzung. Gelegentlich empfiehlt es sich, die Baseline-Leistung für Ihren konkreten Anwendungsfall zu erhöhen. Der Spracherkennungsdienst bietet eine Vielzahl von Anpassungstools, die ohne Code auskommen und mit denen Sie über benutzerdefinierte Modelle auf Grundlage Ihrer Daten einen Wettbewerbsvorteil erzielen können. Diese Modelle sind nur für Sie und Ihre Organisation verfügbar.

### <a name="custom-speech-to-text"></a>Benutzerdefinierte Spracherkennung

Wenn Sie die Spracherkennung für die Erkennung und Transkription in einer individuellen Umgebung verwenden, können Sie benutzerdefinierte Akustik-, Sprach- und Aussprachemodelle erstellen, um Umgebungsgeräusche zu kompensieren oder branchenspezifisches Vokabular zu berücksichtigen. Die Erstellung und Verwaltung von Custom Speech-Modellen ohne Code ist über das [Custom Speech-Portal](https://aka.ms/customspeech) möglich. Nach seiner Veröffentlichung kann das Custom Speech-Modell vom Speech SDK genutzt werden.

### <a name="custom-text-to-speech"></a>Benutzerdefinierte Sprachsynthese

Bei der benutzerdefinierten Sprachsynthese (auch als Custom Voice bezeichnet) handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Die Erstellung und Verwaltung von Custom Voice-Modellen ohne Code ist über das [Custom Voice-Portal](https://aka.ms/customvoice) möglich. Nach seiner Veröffentlichung kann das Custom Voice-Modell vom Speech SDK genutzt werden.

## <a name="get-the-speech-sdk"></a>Abrufen des Speech SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Browser](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
* [Erkennen von Sprache in C#](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)