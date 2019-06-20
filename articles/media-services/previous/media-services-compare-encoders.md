---
title: Azure On-Demand Media Encoder – Vergleich | Microsoft-Dokumentation
description: In diesem Thema werden die Codierungsfunktionen von **Media Encoder Standard** und **Media Encoder Premium Workflow** verglichen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465710"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Vergleich der Azure On-Demand Media Encoder  

In diesem Thema werden die Codierungsfunktionen von **Media Encoder Standard** und **Media Encoder Premium Workflow** verglichen.

## <a name="video-and-audio-processing-capabilities"></a>Verarbeitungsfunktionen für Video und Audio

In der folgenden Tabelle werden die Funktionen von Media Encoder Standard (MES) und Media Encoder Premium Workflow (MEPW) verglichen. 

|Funktion|Media Encoder Standard|Media Encoder Premium Workflow|
|---|---|---|
|Anwenden bedingter Logik beim Codieren<br/>(z.B. wenn die Eingabe in HD erfolgt, dann in 5.1-Audio codieren)|Nein|Ja|
|Untertitelung|Nein|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> mit Dialogue Intelligence™|Nein|Ja|
|Deinterlacing, Inverse Telecine|Basic|Broadcastqualität|
|Erkennen und Entfernen von schwarzen Rahmen <br/>(Pillarbox, Letterbox)|Nein|Ja|
|Erstellen von Miniaturansichten|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Schneiden/Kürzen und Zusammenfügen von Videos|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Ja|
|Überlagern von Audio oder Video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Überlagern von Grafiken|Aus Bildquellen|Aus Bild- und Textquellen|
|Mehrere Audiosprachspuren|Eingeschränkt|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Von Encodern verwendetes Abrechnungsverfahren
| Medienprozessorname | Geltende Preise | Notizen |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODER |Codierungsaufgaben werden basierend auf der Gesamtdauer in Minuten für die Ausgabe aller Mediendateien in Rechnung gestellt. Den entsprechenden Kostensatz finden Sie [hier][1] in der Spalte „ENCODER“. |
| **Media Encoder Premium Workflow** |PREMIUM ENCODER |Codierungsaufgaben werden basierend auf der Gesamtdauer in Minuten für die Ausgabe aller Mediendateien in Rechnung gestellt. Den entsprechenden Kostensatz finden Sie [hier][1] in der Spalte „PREMIUM ENCODER“. |

## <a name="input-containerfile-formats"></a>Eingabecontainer/Dateiformate
| Eingabecontainer/Dateiformate | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF |Ja |Ja |
| MPEG-2-Transportdatenstrom |Ja |Ja |
| MPEG-2-Programmdatenstrom |Ja |Ja |
| MPEG-4/MP4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (unkomprimiert, 8-Bit/10-Bit) |Ja |Ja |
| 3GPP/3GPP2 |Ja |Nein |
| Smooth Streaming-Dateiformat (PIFF 1.3) |Ja |Nein |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ja |Nein |
| Matroska/WebM |Ja |Nein |
| QuickTime (.mov) |Ja |Nein |

## <a name="input-video-codecs"></a>Codecs für Videoeingang
| Codecs für Videoeingang | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra |8-Bit 4:2:0 und 4:2:2 |Ja |
| Avid DNxHD (in MXF) |Ja |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen) |Bis zu 422 Profile |Ja |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Nein |Nein |
| MPEG-4 Teil 2 |Ja |Nein |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |Nein |
| Apple ProRes 422 |Ja |Nein |
| Apple ProRes 422 LT |Ja |Nein |
| Apple ProRes 422 HQ |Ja |Nein |
| Apple ProRes Proxy |Ja |Nein |
| Apple ProRes 4444 |Ja |Nein |
| Apple ProRes 4444 XQ |Ja |Nein |
| HEVC/H.265|Profil: Main|Profile: Main und Main 10|

## <a name="input-audio-codecs"></a>Codecs für Audioeingang
| Codecs für Audioeingang | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M und 302M, AES3-2003) |Nein |Ja |
| Dolby® E |Nein |Ja |
| Dolby® Digital (AC3) |Nein |Ja |
| Dolby® Digital Plus (E-AC3) |Nein |Ja |
| AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1) |Ja |Ja |
| MPEG Layer 2 |Ja |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |Nein |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ja |Nein |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |Nein |

## <a name="output-containerfile-formats"></a>Ausgabecontainer/Dateiformate
| Ausgabecontainer/Dateiformate | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Nein |Ja |
| MXF (OP1a, XDCAM und AS02) |Nein |Ja |
| DPP (einschließlich AS11) |Nein |Ja |
| GXF |Nein |Ja |
| MPEG-4/MP4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Nein |Ja |
| AVI (unkomprimiert, 8-Bit/10-Bit) |Nein |Ja |
| Smooth Streaming-Dateiformat (PIFF 1.3) |Nein |Ja |

## <a name="output-video-codecs"></a>Codecs für Videoausgang
| Codecs für Videoausgang | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Nur 8-Bit 4:2:0 |Ja |
| HEVC (H.265; 8 Bit und 10 Bit)  |Nein |Ja |
| Avid DNxHD (in MXF) |Nein |Ja |
| MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen) |Nein |Ja |
| MPEG-1 |Nein |Ja |
| Windows Media Video/VC-1 |Nein |Ja |
| Erstellung von JPEG-Miniaturansichten |Ja |Ja |
| Erstellung von PNG-Miniaturansichten |Ja |Ja |
| Erstellung von BMP-Miniaturansichten |Ja |Nein |

## <a name="output-audio-codecs"></a>Codecs für Audioausgang
| Codecs für Audioausgabe | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M und 302M, AES3-2003) |Nein |Ja |
| Dolby® Digital (AC3) |Nein |Ja |
| Dolby® Digital Plus (E-AC3) bis 7.1 |Nein |Ja |
| AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1) |Ja |Ja |
| MPEG Layer 2 |Nein |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Nein |Ja |
| Windows Media Audio |Nein |Ja |

>[!NOTE]
>Wenn Sie in Dolby® Digital (AC3) codieren, kann die Ausgabe nur in eine ISO MP4-Datei geschrieben werden.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwandte Artikel
* [Ausführen von Aufgaben für die erweiterte Codierung durch Anpassen der Media Encoder Standard-Voreinstellungen](media-services-custom-mes-presets-with-dotnet.md)
* [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
