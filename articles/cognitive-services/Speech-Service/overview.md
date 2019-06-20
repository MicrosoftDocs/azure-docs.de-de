---
title: Was sind die Azure Speech-Dienste?
titleSuffix: Azure Cognitive Services
description: Die Azure Speech-Dienste dienen zur Vereinheitlichung von Spracherkennung, Sprachsynthese und Sprachübersetzung in einem einzelnen Azure-Abonnement. Über das Speech SDK, das Speech Devices SDK oder REST-APIs können Sie Ihren Anwendungen, Tools und Geräten komfortabel Sprachfunktionen hinzufügen. Fügen Sie einem vorhandenen Chatbot Sprachfunktionen hinzu, nutzen Sie die Sprachsynthese in einer Übersetzungsanwendung, oder transkribieren Sie umfangreiche Daten aus einem Callcenter.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: dc0871c0b16246fa178072e9d9f266d4c02ba1c4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002986"
---
# <a name="what-are-the-speech-services"></a>Was sind Speech-Dienste?

Azure Speech-Dienste dienen zur Vereinheitlichung von Spracherkennung, Sprachsynthese und Sprachübersetzung in einem einzelnen Azure-Abonnement. Über das [Speech SDK](speech-sdk-reference.md), das [Speech Devices SDK](https://aka.ms/sdsdk-quickstart) oder [REST-APIs](rest-apis.md) können Sie Ihren Anwendungen, Tools und Geräten komfortabel mit Sprachfunktionen ausstatten.

> [!IMPORTANT]
> Speech-Dienste haben die Bing-Spracheingabe-API sowie Sprachübersetzung und Custom Speech abgelöst. Eine Migrationsanleitung finden Sie unter *Anleitungen > Migration*.

Die Azure Speech-Dienste umfassen folgende Features. Verwenden Sie die Links in der Tabelle, um weitere Informationen zu gängigen Anwendungsfällen für das jeweilige Feature zu erhalten oder sich die API-Referenz anzusehen.

| Dienst | Feature | BESCHREIBUNG | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spracherkennung](speech-to-text.md) | Spracherkennung | Die Spracherkennung wandelt Audiodatenströme in Echtzeit in Text um, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. In Kombination mit [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) können Sie Benutzerabsichten aus transkribierter Sprache ableiten und auf Sprachbefehle reagieren. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch-Transkription](batch-transcription.md) | Die Batch-Transkription ermöglicht eine asynchrone Spracherkennung/Transkription großer Datenmengen. Dieser REST-basierte Dienst verwendet den gleichen Endpunkt wie die Anpassung und Modellverwaltung. | Nein  | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Unterhaltungstranskription](conversation-transcription-service.md) | Dieser Dienst ermöglicht Spracherkennung, Sprecheridentifikation und Diarisierung in Echtzeit. Er eignet sich optimal für das Transkribieren persönlicher Besprechungen, wobei zwischen Sprechern unterschieden werden kann. | Ja | Nein  |
| | [Erstellen von benutzerdefinierten Sprachmodellen](#customize-your-speech-experience) | Wenn Sie die Spracherkennung für die Erkennung und Transkription in einer individuellen Umgebung verwenden, können Sie benutzerdefinierte Akustik-, Sprach- und Aussprachemodelle erstellen, um Umgebungsgeräusche zu kompensieren oder branchenspezifisches Vokabular zu berücksichtigen. | Nein  | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text-zu-Sprache](text-to-speech.md) | Text-zu-Sprache | Die Sprachsynthese konvertiert Eingabetext in menschenähnliche synthetische Sprache. Sie haben die Wahl zwischen Standardstimmen und neuronalen Stimmen. (Weitere Informationen finden Sie unter [Sprach- und Regionsunterstützung für die API für den Spracherkennungsdienst](language-support.md).) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Erstellen benutzerdefinierter Stimmen](#customize-your-speech-experience) | Erstellen Sie spezielle benutzerdefinierte Voicefonts für Ihre Marke oder Ihr Produkt. | Nein  | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Sprachübersetzung](speech-translation.md) | Sprachübersetzung | Die Sprachübersetzung ermöglicht Echtzeitübersetzungen in mehreren Sprachen für Ihre Anwendungen, Tools und Geräte. Verwenden Sie diesen Dienst für die Übersetzung von Sprache in Sprache und Sprache in Text. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nein  |
| [Virtuelle Voice-First-Assistenten](voice-first-virtual-assistants.md) | Virtuelle Voice-First-Assistenten | Individuelle virtuelle Assistenten, die Azure Speech-Dienste verwenden, ermöglichen es Entwicklern, natürliche, menschenähnliche Konversationsschnittstellen für ihre Anwendungen und Umgebungen zu erstellen. Der Direct Line Speech-Kanal des Bot Framework erweitert diese Möglichkeiten, indem er einen koordinierten, orchestrierten Einstiegspunkt zu einem kompatiblen Bot bietet, der Voice-In/Voice-Out-Interaktionen mit geringer Latenz und hoher Zuverlässigkeit ermöglicht. | [Ja](voice-first-virtual-assistants.md) | Nein  |

## <a name="news-and-updates"></a>Neuigkeiten und Aktualisierungen

Hier finden Sie Neuigkeiten im Zusammenhang mit den Azure Speech-Diensten.

* Mai 2019: Dokumentation für [Unterhaltungstranskription](conversation-transcription-service.md), [Callcentertranskription](call-center-transcription.md) und [virtuelle Voice-First-Assistenten](voice-first-virtual-assistants.md) ist jetzt verfügbar.
* Mai 2019
    * Speech SDK 1.5.1 veröffentlicht. Eine vollständige Liste der Aktualisierungen, Verbesserungen und bekannten Probleme finden Sie in den [Versionshinweisen](releasenotes.md).
    * Speech SDK 1.5.0 veröffentlicht. Eine vollständige Liste der Aktualisierungen, Verbesserungen und bekannten Probleme finden Sie in den [Versionshinweisen](releasenotes.md).
* April 2019: Das Speech SDK 1.4.0 mit Unterstützung von Sprachsynthese (Beta) für C++, C# und Java unter Windows und Linux wurde veröffentlicht. Darüber hinaus unterstützt das SDK nun die Audioformate MP3 und Opus/Ogg für C++ und C# unter Linux. Eine vollständige Liste der Aktualisierungen, Verbesserungen und bekannten Probleme finden Sie in den [Versionshinweisen](releasenotes.md).
* März 2019: Ein neuer Endpunkt für die Sprachsynthese (Text-to-Speech, TTS) steht nun zur Verfügung, der eine vollständige Liste mit den in einer bestimmten Region verfügbaren Stimmen zurückgibt. Darüber hinaus werden jetzt neue Regionen für TTS unterstützt. Weitere Informationen finden Sie unter [Text-to-speech REST API](rest-text-to-speech.md) (Text-to-Speech-REST-API).

## <a name="try-speech-services"></a>Ausprobieren der Speech-Dienste

Wir stellen Schnellstartanleitungen für die gängigsten Programmiersprachen bereit. Diese sind jeweils so konzipiert, dass Sie in weniger als zehn Minuten über ausführbaren Code verfügen. Die folgende Tabelle enthält die beliebtesten Schnellstartanleitungen für die einzelnen Features. Über die Navigationsleiste auf der linken Seite gelangen Sie auf Wunsch zu weiteren Programmiersprachen und Plattformen.

| Spracherkennung (SDK) | Sprachsynthese (SDK) | Übersetzung (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Spracherkennung und Sprachsynthese verfügen ebenfalls über REST-Endpunkte und die zugehörigen Schnellstartanleitungen.

Nachdem Sie Gelegenheit hatten, die Speech-Dienste zu verwenden, erfahren Sie in unserem Tutorial, wie Sie mithilfe des Speech SDK und LUIS Absichten in gesprochener Sprache erkennen.

* [Tutorial: Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#](how-to-recognize-intents-from-speech-csharp.md)
* [Tutorial: Erstellen einer Flask-App zum Übersetzen von Text, Analysieren der Stimmung und Synthetisieren von Sprache, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Abrufen des Beispielcodes

Auf GitHub steht Beispielcode für jeden der Azure Speech-Dienste zur Verfügung. In den Beispielen werden gängige Szenarien wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und einmalige Erkennung oder die Verwendung benutzerdefinierter Modelle behandelt. Über die folgenden Links gelangen Sie zu den SDK- und REST-Beispielen:

* [Beispiele für Spracherkennung, Sprachsynthese und Sprachübersetzung (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch transcription samples (REST) (Batchtranskriptionsbeispiele (REST))](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Beispiele für die Sprachsynthese (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Anpassen der Speech-Funktionen

Die Azure Speech-Dienste funktionieren bestens mit integrierten Modellen, können auf Wunsch aber auch speziell für Ihr Produkt oder Ihre Umgebung angepasst und optimiert werden. Die Anpassungsoptionen reichen von der Feinabstimmung des Akustikmodells bis hin zu individuellen Voicefonts für Ihre Marke. Nachdem Sie ein benutzerdefiniertes Modell erstellt haben, können Sie es mit allen Azure Speech-Diensten verwenden.

| Spracherkennungsdienst | Modell | BESCHREIBUNG |
|----------------|-------|-------------|
| Spracherkennung | [Akustikmodell](how-to-customize-acoustic-models.md) | Erstellen Sie ein benutzerdefiniertes Akustikmodell für Anwendungen, Tools oder Geräte, die in speziellen Umgebungen (etwa in einem Auto oder in einer Produktionshalle) verwendet werden, in denen jeweils besondere Aufzeichnungsverhältnisse herrschen. Beispiele wären etwa Sprecher mit Akzent, bestimmte Hintergrundgeräusche oder die Verwendung eines bestimmten Mikrofons für die Aufzeichnung. |
| | [Sprachmodell](how-to-customize-language-model.md) | Erstellen Sie ein benutzerdefiniertes Sprachmodell, um die Transkription von spezifischem Vokabular und spezifischer Grammatik (beispielsweise aus dem Medizin- oder IT-Bereich) zu verbessern. |
| | [Aussprachemodell](how-to-customize-pronunciation.md) | Durch ein benutzerdefiniertes Aussprachemodell können Sie die phonetische Form und Darstellung eines Worts oder Begriffs definieren. Dies ist für die Verarbeitung angepasster Benennungen wie Produktnamen oder Akronymen hilfreich. Erforderlich ist zu Beginn lediglich eine Aussprachedatei – eine einfache TXT-Datei. |
| Sprachsynthese | [Voicefont](how-to-customize-voice-font.md) | Mithilfe benutzerdefinierter Voicefonts können Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen. Für den Einstieg reicht bereits eine kleine Datenmenge aus. Je mehr Daten Sie bereitstellen, desto natürlicher und menschenähnlicher klingt Ihr Voicefont. |

## <a name="reference-docs"></a>Referenz

* [Speech SDK](speech-sdk-reference.md)
* [Speech-Geräte-SDK](speech-devices-sdk.md)
* [REST-API: Spracherkennung](rest-speech-to-text.md)
* [REST-API: Sprachsynthese](rest-text-to-speech.md)
* [REST-API: Batchtranskription und Anpassung](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Beziehen eines kostenlosen Abonnementschlüssels für die Speech-Dienste](get-started.md)
