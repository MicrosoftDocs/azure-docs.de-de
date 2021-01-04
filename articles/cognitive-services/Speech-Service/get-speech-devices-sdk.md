---
title: Abrufen des SDK für sprachaktivierte Geräte
titleSuffix: Azure Cognitive Services
description: Der Speech-Dienst kann mit verschiedensten Geräten und Audioquellen verwendet werden. Jetzt können Sie einen Schritt weiter gehen und Ihre Sprachanwendungen mit angepasster Hardware und Software nutzen. In diesem Artikel erfahren Sie, wie Sie auf das Speech Devices SDK zugreifen und mit der Entwicklung beginnen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026368"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Abrufen des Cognitive Services Speech-Geräte-SDK

Beim Speech Devices SDK handelt es sich um eine vorab optimierte Bibliothek für die Verwendung mit speziell entwickelten Development Kits und verschiedenen Mikrofonarraykonfigurationen.

## <a name="choose-a-development-kit"></a>Auswählen eines Development Kits

|Geräte|Spezifikation|BESCHREIBUNG|Szenarien|
|--|--|--|--|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Ethernet, HDMI, USB-Kamera <br>Linux|Ein branchenübliches Speech Devices SDK zur Anpassung des Microsoft-Mikrofonarrays und zur Unterstützung erweiterter E/A wie HDMI/Ethernet und weiterer USB-Peripheriegeräte <br> [Kontakt mit Urbetter aufnehmen](http://www.urbetter.com/products_56/278.html)|Unterhaltungstranskription, Bildung, Kliniken, Roboter, OTT-Box, Sprach-Agent, Drive-through|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[Setup](speech-devices-sdk-roobo-v1.md) / [Schnellstart](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Audioausgang, EA <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Das erste Speech Devices SDK zur Anpassung des Microsoft-Mikrofonarrays und Front Processing SDK für die Entwicklung hochwertiger Transkriptions- und Sprachszenarien|Unterhaltungstranskription, Smart Speaker, Sprach-Agent, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Setup](../../kinect-dk/set-up-azure-kinect-dk.md) / [Schnellstart](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|Array mit 7 Mikrofonen, RGB und Tiefenkameras <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Ein Entwicklerkit mit erweiterten KI-Sensoren (künstliche Intelligenz) für ausgereifte Modelle zum maschinellen Sehen und Sprachmodelle. Es kombiniert ein erstklassiges räumliches Mikrofonarray und eine Tiefenkamera mit einer Videokamera und einem Lagesensor – alles in einem kleinen Gerät mit mehreren Modi, Optionen und SDKs für verschiedene Computetypen.|Unterhaltungstranskription, Robotik, Smart Building|
|Roobo Smart Audio Dev Kit 2<br>[Einrichtung](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Bluetooth, EA <br>Linux|Das Speech Devices SDK der 2. Generation, das ein alternatives Betriebssystem und mehr Features in einem kostengünstigen Referenzentwurf umfasst|Unterhaltungstranskription, Smart Speaker, Sprach-Agent, Wearable|


## <a name="download-the-speech-devices-sdk"></a>Herunterladen des Speech-Geräte-SDK

Laden Sie das [Speech Devices SDK](./speech-devices-sdk.md) herunter.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Speech-Geräte-SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)