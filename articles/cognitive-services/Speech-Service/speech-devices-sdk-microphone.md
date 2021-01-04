---
title: Empfehlungen zum Mikrofonarray für das Speech-Geräte-SDK
titleSuffix: Azure Cognitive Services
description: Empfehlungen zum Mikrofonarray für das Speech-Geräte-SDK Diese Arraygeometrien werden für Microsoft Audio Stack empfohlen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015340"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Empfehlungen zum Mikrofonarray für das Speech-Geräte-SDK

In diesem Artikel erfahren Sie, wie Sie ein Mikrofonarray für das Speech-Geräte-SDK entwerfen.

Das Speech-Geräte-SDK funktioniert am besten mit einem Mikrofonarray, das gemäß der folgenden Richtlinien, einschließlich der Mikrofongeometrie und der Komponentenauswahl, entworfen wurde. Es sind auch Hinweise zur Integration und zu elektrischen Überlegungen enthalten.

## <a name="microphone-geometry"></a>Mikrofongeometrie

Die folgenden Arraygeometrien werden für Microsoft Audio Stack empfohlen. Die Lokalisierung von Soundquellen und die Unterdrückung von Umgebungsgeräuschen wird durch eine größere Anzahl von Mikrofonen verbessert. Dies hängt jedoch von den jeweiligen Anwendungen, Benutzerszenarien und dem Formfaktor des Geräts ab.

| Mikrofone und Geometrie | Kreisförmiges Array | Kreisförmiges Array | Lineares Array | Lineares Array |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Anzahl Mikrofone | 7 | 4 | 4 | 2 |
| Geometrie | 6 außen, 1 Mitte, Radius = 42,5 mm, gleichmäßige Abstände | 3 außen, 1 Mitte, Radius = 42,5 mm, gleichmäßige Abstände | Länge = 120 mm, Abstand = 40 mm | Abstand = 40 mm |

Mikrofonkanäle sollten entsprechend der für die einzelnen Arrays oben dargestellten Nummerierung angeordnet werden, die ab 0 erhöht wird. Der Microsoft Audio Stack benötigt einen zusätzlichen Referenzstream der Audiowiedergabe, um die Echounterdrückung durchzuführen.

## <a name="component-selection"></a>Komponentenauswahl

Mikrofonkomponenten müssen so ausgewählt werden, dass ein Signal rausch- und verzerrungsfrei wiedergegeben wird.

Bei der Auswahl von Mikrofonen werden folgende Eigenschaften empfohlen:

| Parameter | Empfohlen |
| --------- | ----------- |
| SNR | \> 65 dB (1 kHz-Signal, 94 dBSPL, bewerteter Schalldruckpegel) |
| Amplitudenanpassung | ± 1 dB bei 1 kHz |
| Phasenanpassung | ± 2° bei 1 kHz |
| Akustischer Überlastpunkt (AOP) | \> = 120 dBSPL (THD = 10 %) |
| Bitrate | Mindestens 24 Bit |
| Samplingrate | Mindestens 16 kHz\* |
| Frequenzgang | ± 3 dB, 200 – 8000 Hz Floating Mask\* |
| Zuverlässigkeit | Lagertemperaturbereich -40 °C bis 70 °C<br />Betriebstemperaturbereich -20 °C bis 55 °C |

\*_Für hochwertige Kommunikationsanwendungen (VoIP) können höhere Samplingraten oder „breitere“ Frequenzbereiche erforderlich sein._

Eine gute Komponentenauswahl muss mit einer guten elektroakustischen Integration einhergehen, damit die Leistungsfähigkeit der verwendeten Komponenten nicht beeinträchtigt wird. In besonderen Anwendungsfällen können auch zusätzliche Anforderungen bestehen (z.B. Betriebstemperaturbereiche).

## <a name="microphone-array-integration"></a>Integration von Mikrofonarrays

Die Leistung des im Gerät integrierten Mikrofonarrays kann je nach Komponentenspezifikation variieren. Es ist wichtig, dass Sie sicherstellen, dass die Mikrofone nach der Integration gut abgestimmt sind. Daher sollte die gemessene Geräteleistung nach fester Verstärkung oder Entzerrung den folgenden Empfehlungen entsprechen:

| Parameter          | Empfohlen                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz-Signal, 94 	dBSPL, bewerteter Schalldruckpegel) |
| Ausgangsempfindlichkeit | -26 dBFS/Pa bei 1 kHz (empfohlen)                  |
| Amplitudenanpassung | ± 2 dB, 200 – 8000 Hz                                |
| THD%\*             | ≤ 1 %, 200 – 8000 Hz, 94 dBSPL, 5. Ordnung             |
| Frequenzgang | ± 6 dB, 200 – 8000 Hz Band-pass filter\*\*              |

\*\*_Ein Lautsprecher mit niedriger Verzerrung ist zum Messen des THD-Werts (Total Harmonic Distortion, harmonische Verzerrung) erforderlich (z. B. Neumann KH120)._

\*\*_Für hochwertige Kommunikationsanwendungen (VoIP) können „breitere“ Frequenzbereiche erforderlich sein._

## <a name="speaker-integration-recommendations"></a>Empfehlungen zur Lautsprecherintegration

Da für Spracherkennungsgeräte mit Lautsprechern eine Echounterdrückung erforderlich ist, werden zusätzliche Empfehlungen für die Lautsprecherauswahl und deren Integration bereitgestellt.

| Parameter | Empfohlen |
| --------- | ----------- |
| Aspekte der Linearität | Keine nicht lineare Verarbeitung nach Lautsprecherreferenz, da andernfalls ein hardwarebasierter Loopback-Referenzstream erforderlich ist |
| Lautsprecher-Loopback | Über WASAPI, private APIs, benutzerdefiniertes ALSA-Plug-In (Linux) oder Firmwarekanal bereitgestellt |
| THD % | Drittel-Oktavbänder mindestens 5. Ordnung, Wiedergabe mit 70 dBa bei 0,8 m ≤ 6,3 %, 315 – 500 Hz ≤ 5 %, 630 – 5000 Hz |
| Echokopplung an Mikrofone | \> -10 dB TCLw anhand der Methode von ITU-T G.122 Annex B.4, normalisiert auf Mikrofonpegel<br />TCLw = gemessener TCLw \+ (gemessener Pegel - Soll-Ausgangsempfindlichkeit)<br />TCLw = gemessener TCLw \+ (gemessener Pegel - (-26)) |

## <a name="integration-design-architecture"></a>Integrationsarchitektur

Die folgenden Architekturrichtlinien sind notwendig, wenn Sie Mikrofone in ein Gerät integrieren:

| Parameter | Empfehlung |
| --------- | -------------- |
| Gemeinsamkeit der Mikrofonanschlüsse | Alle Mikrofonanschlüsse im Array haben dieselbe Länge |
| Abmessungen der Mikrofonanschlüsse | Anschlussgröße Ø 0,8 – 1,0 mm, Anschlusslänge/Anschlussdurchmesser \< 2 |
| Mikrofonversiegelung         | Dichtungen einheitlich in Stapelbauweise ausgeführt; Empfehlung: \> 70 % Verdichtungsverhältnis für Schaumstoffdichtungen |
| Zuverlässigkeit von Mikrofonen     | Um ein Eindringen von Staub zu vermeiden, sollte ein Netzgitter verwendet werden (zwischen Leiterplatte für Mikrofone mit Anschluss unten und Dichtung/oberer Abdeckung) |
| Mikrofonisolierung       | Gummidichtungen und Schwingungsentkopplung über die Struktur, insbesondere zur Isolierung von Schwingungswegen aufgrund integrierter Lautsprecher |
| Samplingtakt      | Die Audioaufnahme des Geräts darf keinen Jitter, keine Ausfälle und nur geringe Abweichung aufweisen |
| Aufzeichnungsfähigkeit   | Das Gerät muss Rohdatenströme einzelner Kanäle gleichzeitig aufzeichnen können |
| USB                 | Alle USB-Audioeingabegeräte müssen Deskriptoren gemäß den [Spezifikationen für USB-Audiogeräte Rev3](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) festlegen |
| Mikrofongeometrie | Treiber müssen [Deskriptoren für die Geometrie des Mikrofonarrays](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) ordnungsgemäß implementieren |
| Erkennbarkeit     | Geräte dürfen keine nicht erkennbaren oder nicht steuerbaren auf Hardware, Firmware oder Software von Drittanbietern basierte nicht lineare Audioverarbeitungsalgorithmen zum/vom Gerät aufweisen |
| Aufnahmeformat      | Aufnahmeformate müssen eine minimale Samplingrate von 16 kHz und eine empfohlene Tiefe von 24 Bit aufweisen |

## <a name="electrical-architecture-considerations"></a>Überlegungen zur elektrischen Architektur

Gegebenenfalls können Arrays mit einem USB-Host (z.B. einem SoC, auf dem Microsoft Audio Stack ausgeführt wird) und Schnittstellen zu Sprachdiensten oder anderen Anwendungen verbunden werden.

Hardwarekomponenten wie die PDM-zu-TDM-Konvertierung sollten sicherstellen, dass der dynamische Bereich und das SNR der Mikrofone bei erneutem Sampling erhalten bleiben.

Alle Audio-MCUs sollten High-Speed USB Audio 2.0 unterstützen, um die notwendige Bandbreite für bis zu sieben Kanäle bei höheren Samplingraten und Bittiefen bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Speech-Geräte-SDK](speech-devices-sdk.md)