---
title: Übersicht über die dynamische Paketerstellung mit Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema finden Sie eine Übersicht über die dynamische Paketerstellung in Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: 78e3897ec653326bcd88a538a6ea7d33938659b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761949"
---
# <a name="dynamic-packaging"></a>Dynamische Paketerstellung

Microsoft Azure Media Services kann verwendet werden, um zahlreiche Medien-Quelldateiformate, Medienstreamingformate und Inhaltsschutzformate in verschiedenen Clienttechnologien wie iOS und XBOX bereitzustellen. Von den Clients werden verschiedene Protokolle verarbeitet. So ist für iOS ein HLS-Format (HTTP Live Streaming) erforderlich, für Xbox dagegen Smooth Streaming. Wenn Sie über MP4-Dateien (ISO Base Media 14496-12) mit adaptiver Bitrate (Multi-Bitrate) oder über Smooth Streaming-Dateien mit adaptiver Bitrate verfügen, die Sie für Clients bereitstellen möchten, von denen HLS, MPEG DASH oder Smooth Streaming unterstützt wird, können Sie die Vorteile der **dynamischen Paketerstellung** nutzen. Die Paketerstellung ist unabhängig von der Videoauflösung; SD, HD und UHD-4K werden unterstützt.

In Media Services stellt ein [Streamingendpunkt](streaming-endpoint-concept.md) einen dynamischen (Just-In-Time-)Paketerstellungs- und Ursprungsdienst dar, der Ihre Live- und On-Demand-Inhalte direkt in einer Clientplayeranwendung bereitstellen kann und dabei eines der allgemeinen Streamingmedienprotokolle (HLS oder DASH) verwendet. Die Feature für die dynamische Paketerstellung ist standardmäßig auf allen **Streamingendpunkten** (Standard oder Premium) vorhanden. 

Für die Nutzung der **dynamischen Paketerstellung** benötigen Sie ein **Medienobjekt** mit MP4-Dateien mit adaptiver Bitrate sowie Streamingkonfigurationsdateien, die für die dynamische Paketerstellung von Media Services erforderlich sind. Zum Abrufen der Dateien können Sie beispielsweise Ihre Zwischendatei (Quelldatei) mit Media Services codieren. Um Videos in dem codierten Medienobjekt für die Clientwiedergabe verfügbar zu machen, müssen Sie einen **Streaminglocator** sowie Streaming-URLs erstellen. Daraufhin erhalten Sie den Stream abhängig vom im Streamingclientmanifest angegebenen Format (HLS, DASH oder Smooth) in dem Protokoll, das Sie gewählt haben.

So müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Die entsprechende Antwort wird von Media Services basierend auf Clientanforderungen erstellt und verfügbar gemacht. 

Die dynamische Paketerstellung in Media Services wird sowohl für Livestreaming als auch für On-Demand-Streaming verwendet. 

## <a name="common-on-demand-workflow"></a>Allgemeiner On-Demand-Workflow

In diesem Abschnitt wird ein allgemeiner Media Services-Streamingworkflow mit dynamischer Paketerstellung beschrieben.

1. Laden Sie eine Eingabedatei (auch Mezzaninedatei genannt) hoch. Beispielformate: MP4, MOV oder MXF (eine Liste unterstützter Formate finden Sie unter [Von Media Encoder Standard unterstützte Formate](media-encoder-standard-formats.md)).
2. Codieren Sie Ihre Mezzaninedatei zu H.264-MP4-Sätzen mit adaptiver Bitrate.
3. Veröffentlichen Sie das Medienobjekt, das den MP4-Satz mit adaptiver Bitrate enthält. Zur Veröffentlichung wird ein **Streaminglocator** erstellt.
4. Erstellen Sie URLs für unterschiedliche Formate (HLS, Dash und Smooth Streaming). Der **Streamingendpunkt** sorgt dafür, dass das korrekte Manifest bereitgestellt wird und die Anforderungen für alle diese Formate korrekt verarbeitet werden.

Das folgende Diagramm zeigt das On-Demand-Streaming mit dem Workflow der dynamischen Paketerstellung.

![Dynamische Paketerstellung](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codieren als MP4-Dateien mit adaptiver Bitrate

Informationen zur [Codierung mit Media Services](encoding-concept.md) finden Sie in den folgenden Beispielen:

* [Codieren aus einer HTTPS-URL mithilfe von integrierten Voreinstellungen](job-input-from-http-how-to.md)
* [Codieren einer lokalen Datei mithilfe von integrierten Voreinstellungen](job-input-from-local-file-how-to.md)
* [Entwickeln einer benutzerdefinierten Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen](customize-encoder-presets-how-to.md)

Eine Liste mit Media Encoder Standard-Formaten und -Codecs finden Sie [hier](media-encoder-standard-formats.md).

## <a name="common-live-streaming-workflow"></a>Allgemeiner Workflow für das Livestreaming

Nachfolgend werden die Schritte in einem Workflow für das Livestreaming aufgeführt:

1. Erstellen Sie ein [Liveereignis](live-events-outputs-concept.md).
1. Rufen Sie die Erfassungs-URLs ab, und konfigurieren Sie Ihren lokalen Encoder vor Ort, um die URL zum Senden des Beitragsfeeds zu verwenden.
1. Rufen Sie die Vorschau-URL ab und verwenden Sie sie, um sich zu vergewissern, dass die Eingabe des Encoders auch tatsächlich empfangen wird.
1. Erstellen Sie ein neues **Medienobjekt**.
1. Erstellen Sie eine **Liveausgabe**, und verwenden Sie den Namen des erstellten Medienobjekts.<br/>Die **Liveausgabe** archiviert den Datenstrom im **Medienobjekt**.
1. Erstellen Sie einen **Streaminglocator** mit den integrierten Arten von **Streamingrichtlinien**.<br/>Wenn Sie beabsichtigen, den Inhalt zu verschlüsseln, lesen Sie [Übersicht über den Inhaltsschutz](content-protection-overview.md).
1. Listen Sie die Pfade auf dem **Streaminglocator** auf, um die zu verwendenden URLs zurückzugeben.
1. Rufen Sie den Hostnamen für den **Streamingendpunkt** ab, von dem aus Sie streamen möchten.
1. Erstellen Sie URLs für unterschiedliche Formate (HLS, Dash und Smooth Streaming). Der **Streamingendpunkt** sorgt dafür, dass das korrekte Manifest bereitgestellt wird und die Anforderungen für alle diese Formate korrekt verarbeitet werden.

Für ein Liveereignis ist einer von zwei Typen möglich: Pass-Through und Livecodierung. Weitere Informationen zum Livestreaming in Media Services v3 finden Sie unter [Übersicht zum Livestreaming](live-streaming-overview.md).

Das folgende Diagramm zeigt das Livestreaming mit dem Workflow der dynamischen Paketerstellung.

![Pass-Through](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Übermittlungsprotokolle

|Protocol|Beispiel|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Von der dynamischen Paketerstellung unterstützte Videocodecs

Die dynamische Paketerstellung unterstützt MP4-Dateien mit Video, das mit [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC oder AVC1) oder [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 oder hvc1) codiert wurde.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Von der dynamischen Paketerstellung unterstützte Audiocodecs

Die dynamische Paketerstellung unterstützt MP4-Dateien mit Audio, das mit [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 oder E-AC3), Dolby Atmos oder [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless) codiert wurde. Das Streaming von Dolby Atmos-Inhalten wird für Standards wie das MPEG-DASH-Protokoll mit fragmentiertem CSF- (Common Streaming Format) oder CMAF-MP4 (Common Media Application Format) und über HTTP Live Streaming (HLS) mit CMAF unterstützt.

> [!NOTE]
> Dateien mit [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)-Audio (AC3) werden von der dynamischen Paketerstellung nicht unterstützt, da es sich dabei um einen Legacy-Codec handelt.

## <a name="dynamic-encryption"></a>Dynamische Verschlüsselung

Mit der **dynamischen Verschlüsselung** können Sie Ihre Live- oder On-Demand-Inhalte mit AES-128 oder einem der drei hauptsächlichen DRM-Systeme (Digital Rights Management) dynamisch verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. Weitere Informationen finden Sie unter [Dynamische Verschlüsselung](content-protection-overview.md).

## <a name="manifests"></a>Manifeste 
 
Media Services unterstützt die Protokolle HLS, MPEG DASH und Smooth Streaming. Als Teil der **dynamischen Paketerstellung** werden die Streamingclientmanifeste (HLS Master Playlist, DASH Media Presentation Description (MPD) und Smooth Streaming) basierend auf dem Formatselektor in der URL dynamisch generiert. Informationen zu den Übermittlungsprotokollen finden Sie in [diesem Abschnitt](#delivery-protocols). 

Eine Manifestdatei enthält Streamingmetadaten wie etwa Typ (Audio, Video oder Text), Titelname, Start- und Endzeit, Bitrate (Qualität), Sprachen, Präsentationsfenster (variables Fenster oder feste Dauer) und Videocodec (FourCC). Sie weist den Player zudem zum Abrufen des nächsten Fragments an, indem Informationen zu den nächsten für die Wiedergabe verfügbaren Videofragmenten und den zugehörigen Speicherorten angezeigt werden. Fragmente (oder Segmente) sind die eigentlichen „Blöcke“ von Videoinhalten.

### <a name="hls-master-playlist"></a>HLS Master Playlist

Hier ein Beispiel für eine HLS-Manifestdatei: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>DASH Media Presentation Description (MPD)

Beispiel für ein DASH-Manifest:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Beispiel für ein Smooth Streaming-Manifest:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Dynamisches Manifest

Mithilfe der dynamischen Filterung kann die Anzahl von Spuren, Formaten, Bitraten und Präsentationszeitfenstern gesteuert werden, die an die Player gesendet werden. Weitere Informationen finden Sie unter [Filter und dynamische Manifeste](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Hochladen, Codieren und Streamen von Videos](stream-files-tutorial-with-api.md)

