---
title: 'Speech Devices SDK: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit dem Speech-Geräte-SDK Der Speech-Dienst kann mit verschiedensten Geräten und Audioquellen verwendet werden. Beim Speech-Geräte-SDK handelt es sich um eine vorab optimierte Bibliothek, die mit spezifischen mikrofonfähigen Development Kits kombiniert wurde.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015272"
---
# <a name="what-is-the-speech-devices-sdk"></a>Was ist das Speech Devices SDK?

Der [Speech-Dienst](overview.md) kann mit verschiedensten Geräten und Audioquellen verwendet werden. Jetzt können Sie einen Schritt weiter gehen und Ihre Sprachanwendungen mit angepasster Hardware und Software nutzen. Beim Speech-Geräte-SDK handelt es sich um eine vorab optimierte Bibliothek, die mit spezifischen mikrofonfähigen Development Kits kombiniert wurde.

Anwendungsmöglichkeiten für das Speech-Geräte-SDK:

- Testen Sie neue Sprachszenarien schnell.
- Integrieren Sie den cloudbasierten Speech-Dienst einfacher auf Ihrem Gerät.
- Sorgen Sie für eine herausragende Benutzerfreundlichkeit für Ihre Kunden.

Das Speech-Geräte-SDK nutzt das [Speech-SDK](speech-sdk.md). Mithilfe unserer Audioverarbeitungsalgorithmus mit dem Mikrofonarray des Geräts, um das Audiosignal an den [Speech-Dienst](overview.md) zu senden. Es bietet eine präzise Fernfeld-[Spracherkennung](speech-to-text.md) mit Geräuschunterdrückung, Echounterdrückung, Beamforming und Hallunterdrückung.

Sie können auch das Speech-Geräte-SDK für die Erstellung von Ambient Devices verwenden, die Ihr eigenes [benutzerdefiniertes Schlüsselwort](./custom-keyword-basics.md) verwenden. Ein benutzerdefiniertes Schlüsselwort gibt einen Hinweis, der eine Benutzerinteraktion startet, die für Ihre Marke spezifisch ist.

Das Speech Devices SDK erleichtert die Entwicklung verschiedenster stimmaktivierter Szenarien, z. B. [Sprachassistenten](./voice-assistants.md), Drive-through-Bestellsysteme, [Unterhaltungstranskription](./conversation-transcription.md) und intelligente Lautsprecher. Es ist möglich, Benutzern mit Textausgaben oder mit Sprachausgaben in einer Standard- oder [benutzerdefinierten Stimme](./how-to-custom-voice-create-voice.md) zu antworten, Suchergebnisse bereitzustellen, [Übersetzungen](speech-translation.md) in andere Sprachen durchzuführen und vieles mehr. Wir freuen uns darauf, zu sehen, was Sie erstellen!

## <a name="get-the-speech-devices-sdk"></a>Abrufen des SDK für sprachaktivierte Geräte

### <a name="android"></a>Android

Das Speech Devices SDK für Android unterstützt das [Roobo v1](speech-devices-sdk-roobo-v1.md) und gleichwertige Geräte. Laden Sie für diese die aktuellste Version des [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android) herunter.


Wenn Sie über ein anderes Android-Gerät verfügen, etwa über ein Smartphone oder Tablet, beginnen Sie mit dem [Android Speech SDK](speech-sdk.md)


### <a name="windows"></a>Windows

Für Windows wird die Beispielanwendung als plattformübergreifende Java-Anwendung bereitgestellt. Laden Sie die neueste Version des [Speech-Geräte-SDK für die JRE](https://aka.ms/sdsdk-download-JRE) herunter.
Die Anwendung wird mit dem Speech SDK-Paket und der Eclipse Java-IDE (v4) unter 64-Bit-Windows erstellt. Sie wird in einer 64-Bit Java 8-JRE (Runtime Environment) ausgeführt.

### <a name="linux"></a>Linux

Für Linux wird die Beispielanwendung als plattformübergreifende Java-Anwendung bereitgestellt. Laden Sie die neueste Version des [Speech-Geräte-SDK für die JRE](https://aka.ms/sdsdk-download-JRE) herunter.
Die Anwendung wird mit dem Speech SDK-Paket und der Eclipse Java-IDE (v4) unter 64-Bit-Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) erstellt. Sie wird in einer 64-Bit Java 8-JRE (Runtime Environment) ausgeführt.

Zusätzliche Binärdateien werden bereitgestellt, um zukünftige Geräte, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC Speaker](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) und [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64). zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Auswählen Ihres Speech-Geräts](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)