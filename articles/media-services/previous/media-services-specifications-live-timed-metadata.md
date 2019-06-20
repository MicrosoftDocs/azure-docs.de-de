---
title: Azure Media Services – Signalisierung von zeitgesteuerten Metadaten beim Livestreaming | Microsoft-Dokumentation
description: In dieser Spezifikation werden zwei Modi allgemein beschrieben, die von Media Services für die Signalisierung von zeitgesteuerten Metadaten beim Livestreaming verwendet werden. Dies schließt Unterstützung für Signale für allgemeine zeitgesteuerte Metadaten sowie SCTE-35-Signalisierung für die nahtlose Einfügung von Werbeinhalten ein.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459112"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalisieren von zeitgesteuerten Metadaten beim Livestreaming 


## <a name="1-introduction"></a>1 Einführung 
Um die Einfügung von Werbeinhalten oder benutzerdefinierten Inhalten auf Clientplayern zu erleichtern, nutzen Broadcaster oftmals im Video eingebettete Metadaten mit Zeitinformationen. Um diese Szenarien zu ermöglichen, bieten Media Services Unterstützung für den Transport von zeitgesteuerten Metadaten zugleich mit den Medien, vom Einspeisepunkt des Livestreamingkanals bis zur Clientanwendung.
In dieser Spezifikation werden zwei Modi allgemein beschrieben, die von Media Services für die Signalisierung von zeitgesteuerten Metadaten beim Livestreaming verwendet werden:

1. [SCTE-35]-Signalisierung gemäß den in [SCTE-67] beschriebenen empfohlenen Verfahren

2. Ein allgemeiner Signalisierungsmodus für zeitgesteuerte Metadaten für Nachrichten, die nicht [SCTE-35]-konform sind

### <a name="12-conformance-notation"></a>1.2 Konformität der Schlüsselbegriffe
Die Schlüsselwörter "MUSS", "DARF NICHT", "ERFORDERLICH", "SOLL", "SOLL NICHT", "SOLLTE", "SOLLTE NICHT", "EMPFOHLEN", "KANN" und "OPTIONAL" in diesem Dokument sind wie in RFC 2119 zu verstehen.

### <a name="13-terms-used"></a>1.3 Terminologie

| Begriff              | Definition                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Präsentationszeit | Die Zeit, zu der ein Ereignis einem Benutzer präsentiert wird. Diese Zeit stellt den Moment auf der Medienzeitachse dar, in dem ein Betrachter das Ereignis zu sehen bekommt. Beispielsweise ist die Präsentationszeit für einen SCTE-35-Befehl „splice_info()“ die „splice_time()“. |
| Ankunftszeit      | Der Zeitpunkt, zu dem eine Ereignismeldung eintrifft. Diese Zeit unterscheidet sich normalerweise von der Präsentationszeit des Ereignisses, da Ereignismeldungen vor der Präsentationszeit des Ereignisses gesendet werden.                                     |
| Platzsparende Spur      | Medienspur, die nicht fortlaufend ist und mit einer übergeordneten Spur oder Steuerungsspur zeitsynchronisiert ist.                                                                                                                                    |
| Origin            | Der Azure Media-Streamingdienst                                                                                                                                                                                                |
| Kanalsenke      | Der Azure Media-Livestreamingdienst                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming-Protokoll                                                                                                                                                                                               |
| DASH              | Dynamisches adaptives Streaming über HTTP                                                                                                                                                                                             |
| Smooth            | Smooth Streaming-Protokoll                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG-2-Transportdatenströme                                                                                                                                                                                                         |
| RTMP              | Real-Time Multimedia Protocol (Echtzeit-Multimediaprotokoll)                                                                                                                                                                                                    |
| uimsbf            | Unsigned integer, most significant bit first (Ganzzahl ohne Vorzeichen, höchstwertiges Bit zuerst).                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 Erfassung von zeitgesteuerten Metadaten
## <a name="21-rtmp-ingest"></a>2.1 RTMP-Erfassung
RTMP unterstützt zeitgesteuerte Metadatensignale, die als in den RTMP-Stream eingebettete AMF-Positionsnachrichten gesendet werden. Das Senden der Positionsnachrichten kann einige Zeit vor dem erforderlichen Eintreten des eigentlichen Ereignisses oder der Werbeeinblendung erfolgen. Zur Unterstützung dieses Szenarios wird die tatsächliche Zeit der Einfügung oder des Segments innerhalb der Positionsnachricht gesendet. Weitere Informationen finden Sie unter [AMF0].

In der folgenden Tabelle ist das Format der AMF-Nachrichtennutzlast beschrieben, die von Media Services erfasst wird.

Der Name der AMF-Nachricht kann verwendet werden, um mehrere Ereignisströme vom gleichen Typ zu unterscheiden.  Für [SCTE-35]-Nachrichten MUSS der Name der AMF-Nachricht „onAdCue“ sein, wie in [SCTE-67] empfohlen.  Alle nicht aufgeführten Felder MÜSSEN ignoriert werden, um zukünftige Erneuerungen dieses Entwurfs nicht zu behindern.

### <a name="signal-syntax"></a>Signalsyntax
Für den einfachen RTMP-Modus unterstützt Media Services eine einzelne AMF-Positionsnachricht mit dem Namen „onAdCue“ im folgenden Format:

### <a name="simple-mode"></a>Einfacher Modus

| Feldname | Feldtyp | Erforderlich? | Beschreibungen                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Erforderlich | Die Ereignisnachricht.  Ist „SpliceOut“, um eine einfache Einfügung anzugeben.                                              |
| id         | string     | Erforderlich | Ein eindeutiger Bezeichner, der die Einfügung oder das Segment beschreibt. Identifiziert diese Instanz der Nachricht                            |
| duration   | Number     | Erforderlich | Die Dauer der Einfügung. Die Einheit sind Sekundenbruchteile.                                                                |
| elapsed    | Number     | Optional | Wenn das Signal zur Unterstützung der Abstimmung wiederholt wird, gibt dieses Feld die Menge der Präsentationszeit an, die seit dem Anfang der Einfügung verstrichen ist. Die Einheit sind Sekundenbruchteile. Bei der Verwendung des einfachen Modus sollte dieser Wert die ursprüngliche Dauer der Einfügung nicht überschreiten.                                                  |
| time       | Number     | Erforderlich | Die Zeit der Einfügung in der Präsentationszeit. Die Einheit sind Sekundenbruchteile.                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35-Modus

| Feldname | Feldtyp | Erforderlich? | Beschreibungen                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Erforderlich | Die Ereignisnachricht.  Für [SCTE-35]-Nachrichten MUSS dies die in base64 (IETF RFC 4648) binär codierte splice_info_section() sein, damit Nachrichten [SCTE-67]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können.                                              |
| type       | string     | Erforderlich | Ein URN oder eine URL, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten MUSS dies „urn:scte:scte35:2013a:bin“ sein, damit Nachrichten [SCTE-67]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können.  |
| id         | string     | Erforderlich | Ein eindeutiger Bezeichner, der die Einfügung oder das Segment beschreibt. Gibt die Instanz der Nachricht an.  Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf.|
| duration   | Number     | Erforderlich | Die Dauer des Ereignisses oder der Werbeeinblendung, sofern bekannt. Wenn sie unbekannt ist, sollte der Wert 0 sein.                                                                 |
| elapsed    | Number     | Optional | Wenn das [SCTE-35]-Werbesignal zur Abstimmung wiederholt wird, gibt dieses Feld die Menge der Präsentationszeit an, die seit dem Anfang der Einfügung verstrichen ist. Die Einheit sind Sekundenbruchteile. Im [SCTE-35]-Modus darf dieser Wert die ursprünglich angegebene Dauer der Einfügung oder des Segments überschreiten.                                                  |
| time       | Number     | Erforderlich | Die Präsentationszeit des Ereignisses oder der Werbeeinblendung.  Die Präsentation und Dauer SOLLTEN an den Datenstrom-Zugriffspunkten, (Stream Access Points, SAPs) von Typ 1 oder 2 ausgerichtet sein, wie in [ISO-14496-12], Anhang I, beschrieben. Für HLS-Ausgang SOLLTEN Zeit und Dauer an den Segmentgrenzen ausgerichtet sein. Die Präsentationszeit und die Dauer verschiedener Ereignisnachrichten innerhalb des gleichen Ereignisdatenstroms DÜRFEN sich NICHT überschneiden. Die Einheit sind Sekundenbruchteile.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 Stornierung und Updates

Nachrichten können durch das Senden mehrerer Nachrichten mit der gleichen Präsentationszeit und ID storniert oder aktualisiert werden. Ein Ereignis wird durch die Präsentationszeit und die ID eindeutig identifiziert, und die letzte für eine bestimmte Präsentationszeit empfangene Nachricht, die den Vorlaufbedingungen entspricht, ist die Nachricht, für die die Aktion ausgeführt wird. Das aktualisierte Ereignis ersetzt alle zuvor empfangenen Nachrichten. Die Vorlaufbedingung beträgt vier Sekunden. Aktionen werden für Nachrichten ausgeführt, die mindestens vier Sekunden vor der Präsentationszeit empfangen wurden.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Fragmentierte MP4-Erfassung (Smooth Streaming)
Informationen zu den Anforderungen für die Live-Datenstromerfassung finden Sie unter [LIVE-FMP4]. Die folgenden Abschnitte enthalten Detailinformationen zur Erfassung von zeitgesteuerten Präsentationsmetadaten.  Zeitgesteuerte Präsentationsmetadaten werden als Sparsespur erfasst, die sowohl in der Live Server Manifest Box (siehe dazu MS-SSTR) als auch in der Movie Box (‚moov‘) definiert ist.  Jedes Sparsefragment besteht aus einer Movie Fragment Box (‚moof‘) und einer Media Data Box (‚mdat‘), wobei es sich bei der mdat-Box um die binäre Nachricht handelt.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box
Die Sparsespur MUSS in der Live Server Manifest Box mit dem Eintrag \<textstream\> deklariert sein, und für sie MÜSSEN die folgenden Attribute festgelegt sein:

| **Attributname** | **Feldtyp** | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Erforderlich      | MUSS „0“ sein, was eine Spur mit einer unbekannten, variablen Bitrate angibt.                                                                                                                                                                                                 |
| parentTrackName    | string         | Erforderlich      | MUSS der Name der übergeordneten Spur sein, an deren Zeitachse die Zeitcodes der Sparsespur ausgerichtet sind. Die übergeordnete Spur darf ihrerseits keine Sparsespur sein.                                                                                                                    |
| manifestOutput     | Boolean        | Erforderlich      | MUSS „true“ sein, um anzugeben, dass die Sparsespur im Smooth-Clientmanifest eingebettet ist.                                                                                                                                                               |
| Subtype            | string         | Erforderlich      | MUSS der aus vier Buchstaben bestehende Code „DATA“ sein.                                                                                                                                                                                                                         |
| Schema             | string         | Erforderlich      | MUSS ein URN oder eine URL sein, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten MUSS dies „urn:scte:scte35:2013a:bin“ sein, damit Nachrichten [SCTE-67]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können. |
| trackName          | string         | Erforderlich      | MUSS der Name der Sparsespur sein. Der Spurname kann verwendet werden, um mehrere Ereignisstreams mit dem gleichen Schema zu unterscheiden. Jeder eindeutige Ereignisstream muss einen eindeutigen Spurnamen aufweisen.                                                                           |
| timescale          | Number         | Optional      | MUSS die Zeitskala der übergeordneten Spur sein.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Movie Box

Die Movie Box (‚moov‘) folgt auf die Live Server Manifest Box als Teil des Datenstromheaders für eine Sparsespur.

Die moov-Box SOLLTE eine **TrackHeaderBox (‚tkhd‘)** gemäß der Definition in [ISO-14496-12] mit den folgenden Bedingungen enthalten:

| **Feldname** | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | SOLLTE 0 sein, da die Spurbox 0 (null) Muster enthält und die Dauer der Muster in der Spurbox 0 ist. |

-------------------------------------

Die moov-Box SOLLTE eine **HandlerBox (‚hdlr‘)** gemäß der Definition in [ISO-14496-12] mit den folgenden Bedingungen enthalten:

| **Feldname** | **Feldtyp**          | **Erforderlich?** | **Beschreibung**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | SOLLTE ‚meta‘ sein. |

-------------------------------------

Die stsd-Box SOLLTE eine MetaDataSampleEntry-Box mit einem gemäß [ISO-14496-12] definierten Codierungsnamen enthalten.  Beispielsweise sollte der Codierungsname für SCTE-35-Nachrichten ‚scte‘ sein.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box und Media Data Box

Fragmente von Sparsespuren bestehen aus einer Movie Fragment Box (‚moof‘) und einer Media Data Box (‚mdat‘).

Die MovieFragmentBox („moof“) MUSS ein Feld **TrackFragmentExtendedHeaderBox („uuid“)** gemäß Definition in [MS-SSTR] mit den folgenden Feldern enthalten:

| **Feldname**         | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | MUSS die Ankunftszeit des Ereignisses sein. Dieser Wert richtet die Nachricht an der übergeordneten Spur aus.   |
| fragment_duration      | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | MUSS die Dauer des Ereignisses sein. Die Dauer kann 0 (null) sein, um anzugeben, dass die Dauer unbekannt ist. |

------------------------------------


Die MediaDataBox (‚mdat‘) MUSS das folgende Format aufweisen:

| **Feldname**          | **Feldtyp**                   | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Bestimmt das Format des Inhalts der mdat-Box. Nicht erkannte Versionen werden ignoriert. Aktuell wird nur der Wert 1 unterstützt.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Gibt die Instanz der Nachricht an. Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf; d.h., die Verarbeitung einer beliebigen von mehreren Ereignisnachrichtenboxen mit gleicher ID ist ausreichend.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Die Summe von „fragment_absolute_time“, die in „TrackFragmentExtendedHeaderBox“ angegeben ist, und das „presentation_time_delta“ MUSS die Präsentationszeit des Ereignisses bilden. Die Präsentation und Dauer SOLLTEN an den Datenstrom-Zugriffspunkten, (Stream Access Points, SAPs) von Typ 1 oder 2 ausgerichtet sein, wie in [ISO-14496-12], Anhang I, beschrieben. Für HLS-Ausgang SOLLTEN Zeit und Dauer an den Segmentgrenzen ausgerichtet sein. Die Präsentationszeit und die Dauer verschiedener Ereignisnachrichten innerhalb des gleichen Ereignisdatenstroms DÜRFEN sich NICHT überschneiden. |
| message                 | Bytearray                       | Erforderlich      | Die Ereignisnachricht. Für [SCTE-35]-Nachrichten ist die Nachricht die binäre „splice_info_section()“, trotz einer anders lautenden Empfehlung in [SCTE-67]. Für [SCTE-35]-Nachrichten MUSS dies „splice_info_section()“ sein, damit Nachrichten [SCTE-67]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können. Für [SCTE-35]-Nachrichten ist die binäre „splice_info_section()“ die Nutzlast der mdat-Box, die NICHT base64-codiert ist.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 Stornierung und Updates
Nachrichten können durch das Senden mehrerer Nachrichten mit der gleichen Präsentationszeit und ID storniert oder aktualisiert werden.  Das Ereignis ist durch die Präsentationszeit und die ID eindeutig identifiziert. Die letzte für eine bestimmte Präsentationszeit empfangene Nachricht, die den Vorlaufbedingungen entspricht, ist die Nachricht, für die die Aktion ausgeführt wird. Die aktualisierte Nachricht ersetzt alle zuvor empfangenen Nachrichten.  Die Vorlaufbedingung beträgt vier Sekunden. Aktionen werden für Nachrichten ausgeführt, die mindestens vier Sekunden vor der Präsentationszeit empfangen wurden. 


## <a name="3-timed-metadata-delivery"></a>3 Zustellung von zeitgesteuerten Metadaten

Ereignisdatenstrom-Daten sind für Media Services nicht transparent. Media Services übergibt einfach drei Informationsstücke zwischen dem Erfassungsendpunkt und dem Clientendpunkt. Die folgenden Eigenschaften werden gemäß [SCTE-67] und/oder dem Streamingprotokoll des Clients an den Client zugestellt:

1.  Scheme: ein URN oder eine URL, der bzw. die das Schema der Nachricht angibt.

2.  Presentation Time: die Präsentationszeit des Ereignisses auf der Medienzeitachse.

3.  Duration: die Dauer des Ereignisses.

4.  ID: ein optionaler eindeutiger Bezeichner für das Ereignis.

5.  Message: die Ereignisdaten.


## <a name="31-smooth-streaming-delivery"></a>3.1 Smooth Streaming-Zustellung

Informationen zur Behandlung von Sparsespuren finden Sie in [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Beispiel für ein Smooth-Clientmanifest
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS-Zustellung
Zeitgesteuerte Metadaten für Apple HTTP Live Streaming (HLS) können in der Segmentwiedergabeliste innerhalb eines benutzerdefinierten M3UTags eingebettet sein.  Die Anwendungsebene kann die M3U-Wiedergabeliste analysieren und M3U-Tags verarbeiten. Azure Media Services bettet zeitgesteuerte Metadaten in das in [HLS] definierte EXT-X-CUE-Tag ein.  Das EXT-X-CUE-Tag wird aktuell von DynaMux für Nachrichten vom Typ ADI3 verwendet.  Um sowohl SCTE-35- als auch Nicht-SCTE-35-Nachrichten zu unterstützen, legen Sie die Attribute des EXT-X-CUE-Tags wie unten definiert fest:

| **Attributname** | **Typ**                      | **Erforderlich?**                             | **Beschreibung**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Die Nachricht als base64-Zeichenfolge codiert, wie in [IETF RFC 4648](https://tools.ietf.org/html/rfc4648) beschrieben. Für [SCTE-35]-Nachrichten ist dies die base64-codierte „splice_info_section()“.                                                                                                |
| TYPE               | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Ein URN oder eine URL, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten nimmt der Typ den Sonderwert „scte35“ an.                                                                                                                                |
| ID                 | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Eindeutiger Bezeichner für das Ereignis. Wenn die ID bei der Erfassung der Nachricht nicht angegeben wird, erstellt Azure Media Services eine eindeutige ID.                                                                                                                                          |
| DURATION           | Dezimale Gleitkommazahl | Erforderlich                                  | Die Dauer des Ereignisses. Wenn sie unbekannt ist, sollte der Wert 0 sein. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                           |
| ELAPSED            | Dezimale Gleitkommazahl | Optional, für gleitendes Fenster jedoch erforderlich | Wenn das Signal wiederholt wird, um ein gleitendes Präsentationsfenster zu unterstützen, MUSS dieses Feld gleich dem Betrag der seit dem Beginn des Ereignisses verstrichenen Präsentationszeit sein. Die Einheit sind Sekundenbruchteile. Dieser Wert darf die ursprünglich angegebene Dauer der Einfügung oder des Segments überschreiten. |
| TIME               | Dezimale Gleitkommazahl | Erforderlich                                  | Die Präsentationszeit des Ereignisses. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                                                    |


Die Anwendungsschicht des HLS-Players verwendet TYPE, um das Format der Nachricht zu bestimmen, die Nachricht zu decodieren, die erforderlichen Zeitkonvertierungen anzuwenden und das Ereignis zu verarbeiten.  Die Ereignisse sind in der Segmentwiedergabeliste der übergeordneten Spur entsprechend dem Ereigniszeitstempel zeitsynchronisiert.  Sie werden vor dem nächstgelegenen Segment (#EXTINF-Tag) eingefügt.

#### <a name="hls-segment-playlist-example"></a>Beispiel für eine HLS-Segmentwiedergabeliste
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>HLS-Nachrichtenbehandlung

Ereignisse werden in der Segmentwiedergabeliste jeder Video- und Audiospur signalisiert. Die Position des EXT-X-CUE-Tags MUSS immer entweder unmittelbar vor dem ersten HLS-Segment (für splice_out oder Segmentstart) oder unmittelbar nach dem letzten HLS-Segment (für splice_in oder Segmentende) sein, auf das sich die TIME- und DURATION-Attribute beziehen, wie für [HLS] erforderlich.

Wenn ein gleitendes Präsentationsfenster aktiviert ist, MUSS das EXT-X-CUE-Tag ausreichend oft wiederholt werden, dass die Einfügung oder das Segment jederzeit vollständig in der Segmentwiedergabeliste beschrieben ist, und das ELAPSED-Attribut MUSS verwendet werden, um die Aktivitätsdauer der Einfügung oder des Segments anzugeben, wie für [HLS] erforderlich.

Wenn ein gleitendes Präsentationsfenster aktiviert ist, werden die EXT-X-CUE-Tags aus der Segmentwiedergabeliste entfernt, wenn sich die Medienzeit, auf die sie sich beziehen, aus dem gleitenden Präsentationsfenster herausbewegt hat.

## <a name="33--dash-delivery"></a>3.3 DASH-Zustellung
[DASH] stellt drei Verfahren zum Signalisieren von Ereignissen bereit:

1.  Im MPD signalisierte Ereignisse
2.  In-Band in der Darstellung signalisierte Ereignisse (unter Verwendung der Event Message Box (‚emsg‘)
3.  Eine Kombination aus 1 und 2

Im MPD signalisierte Ereignisse sind beim VOD-Streaming nützlich, da Clients beim Download der MPD sofort Zugriff auf alle Ereignisse haben. Die In-Band-Lösung ist für Live-Streaming nützlich, da Clients das MPD nicht erneut herunterladen müssen. Für zeitbasierte Segmentierung bestimmt der Client die URL für das nächste Segment durch Hinzufügen von Dauer und Zeitstempel des aktuellen Segments. Wenn bei dieser Anforderung ein Fehler auftritt, geht der Client von einer Diskontinuität aus und lädt das MPD herunter; andernfalls setzt er das Streaming ohne Download des MPDs fort.

Azure Media Services führt sowohl Signalisierung im MPD und In-Band-Signalisierung mithilfe der Event Message Box aus.

#### <a name="mpd-signaling"></a>MPD-Signalisierung

Ereignisse werden im MPD mithilfe des EventStream-Elements signalisiert, das innerhalb des Period-Elements vorkommt.

Das EventStream-Element weist die folgenden Attribute auf:

| **Attributname** | **Typ**                | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | Zeichenfolge                  | Erforderlich      | Gibt das Schema der Nachricht an. Das Schema ist auf den Wert des Scheme-Attributs in der Live Server Manifest-Box festgelegt. Der Wert SOLLTE ein URN oder eine URL zur Angabe des Nachrichtenschemas sein, z.B. „urn:scte:scte35:2013a:bin“.                                                                |
| value              | Zeichenfolge                  | Optional      | Ein zusätzlicher Zeichenfolgenwert, der von den Besitzern des Schemas zum Anpassen der Semantik der Nachricht verwendet wird. Um mehrere Ereignisdatenströme mit gleichem Schema zu unterscheiden, MUSS der Wert auf den Namen des Ereignisdatenstroms festgelegt werden (‚trackName‘ für Smooth-Erfassung oder AMF-Nachrichtenname für RTMP-Erfassung). |
| Timescale          | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Zeitskala in Sekundenbruchteilen der Zeit- und Dauerfelder innerhalb der emsg-Box.                                                                                                                                                                                                       |


Im EventStream-Element sind null oder mehr Ereigniselemente enthalten, die folgende Attribute aufweisen:

| **Attributname**  | **Typ**                | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64-Bit-Ganzzahl ohne Vorzeichen | Optional      | MUSS die Medienpräsentationszeit des Ereignisses relativ zum Start des Zeitraums sein. Die Präsentationszeit und die Dauer SOLLTEN an den Datenstrom-Zugriffspunkten, (Stream Access Points, SAPs) von Typ 1 oder Typ 2 ausgerichtet sein, wie in [ISO-14496-12] Anhang I definiert. |
| duration            | 32-Bit-Ganzzahl ohne Vorzeichen | Optional      | Die Dauer des Ereignisses. Diese MUSS ausgelassen werden, wenn die Dauer unbekannt ist.                                                                                                                                                 |
| id                  | 32-Bit-Ganzzahl ohne Vorzeichen | Optional      | Gibt die Instanz der Nachricht an. Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf. Wenn die ID bei der Erfassung der Nachricht nicht angegeben wird, erstellt Azure Media Services eine eindeutige ID.             |
| Ereigniselementwert | Zeichenfolge                  | Erforderlich      | Die Ereignisnachricht als base64-Zeichenfolge, wie in [IETF RFC 4648](https://tools.ietf.org/html/rfc4648) beschrieben.                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>XML-Syntax und Beispiel für DASH-Manifestsignalisierung (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Beachten Sie, dass „presentationTime“ die Präsentationszeit des Ereignisses ist, nicht die Ankunftszeit der Nachricht.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 In-Band-Event Message Box-Signalisierung
Für einen In-Band-Ereignisdatenstrom muss das MPD ein InbandEventStream-Element auf der Adaptation Set-Ebene aufweisen.  Dieses Element weist ein obligatorisches schemeIdUri-Attribut und ein optionales timescale-Attribut auf, die ebenfalls in der Event Message Box (‚emsg‘) vorkommen.  Es sollten keine Ereignisnachrichtenfelder mit Schemabezeichnern vorhanden sein, die nicht im MPD definiert sind. Wenn ein DASH-Client ein Ereignisnachrichtenfeld mit einem Schemanamen erkennt, der nicht im MPD definiert ist, wird vom Client erwartet, es zu ignorieren.
Die Event Message Box (‚emsg‘) stellt Signalisierung für allgemeine Ereignisse bereit, die auf die Medienpräsentationszeit bezogen sind. Sofern vorhanden, muss eine emsg-Box immer vor einer moof-Box platziert sein.

### <a name="dash-event-message-box-emsg"></a>DASH Event Message Box ‚emsg‘
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Die Felder der DASHEventMessageBox sind unten definiert:

| **Feldname**          | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | Zeichenfolge                  | Erforderlich      | Gibt das Schema der Nachricht an. Das Schema ist auf den Wert des Scheme-Attributs in der Live Server Manifest-Box festgelegt. Der Wert SOLLTE ein URN oder eine URL sein, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten nimmt dies den Sonderwert „urn:scte:scte35:2013a:bin“ an, obwohl [SCTE-67] eine andere Empfehlung enthält. |
| Wert                   | Zeichenfolge                  | Erforderlich      | Ein zusätzlicher Zeichenfolgenwert, der von den Besitzern des Schemas zum Anpassen der Semantik der Nachricht verwendet wird. Um mehrere Ereignisdatenströme mit gleichem Schema zu unterscheiden, wird der Wert auf den Namen des Ereignisdatenstroms festgelegt (‚trackName‘ für Smooth-Erfassung oder AMF-Nachrichtenname für RTMP-Erfassung).                                                                  |
| Timescale               | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Zeitskala in Sekundenbruchteilen der Zeit- und Dauerfelder innerhalb der emsg-Box.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Das Delta der Medienpräsentationszeit aus der Präsentationszeit des Ereignisses und der frühesten Präsentationszeit in diesem Segment. Die Präsentationszeit und die Dauer SOLLTEN an den Datenstrom-Zugriffspunkten, (Stream Access Points, SAPs) von Typ 1 oder Typ 2 ausgerichtet sein, wie in [ISO-14496-12] Anhang I definiert.                                                                                            |
| event_duration          | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Dauer des Ereignisses, oder 0xFFFFFFFF, um eine unbekannte Dauer anzugeben.                                                                                                                                                                                                                                                                                          |
| id                      | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Gibt die Instanz der Nachricht an. Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf. Wenn die ID bei der Erfassung der Nachricht nicht angegeben wird, erstellt Azure Media Services eine eindeutige ID.                                                                                                                                                    |
| Message_data            | Bytearray              | Erforderlich      | Die Ereignisnachricht. Für [SCTE-35]-Nachrichten sind die Nachrichtendaten die binäre „splice_info_section()“, trotz einer anders lautenden Empfehlung in [SCTE-67].                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 DASH-Nachrichtenbehandlung

Ereignisse werden sowohl für Video- als auch für Audiospuren innerhalb der emsg-Box In-Band signalisiert.  Die Signalisierung erfolgt für alle Segmentanforderungen, deren „presentation_time_delta“ 15 Sekunden oder weniger beträgt. Wenn ein gleitendes Präsentationsfenster aktiviert ist, werden Ereignisnachrichten aus dem MPD entfernt, wenn die Summe von Zeit und Dauer der Ereignisnachricht kleiner als die Zeit der Mediendaten im Manifest ist.  Anders ausgedrückt: Die Ereignisnachrichten werden aus dem Manifest entfernt, wenn die Medienzeit, auf die sie sich beziehen, sich aus dem gleitenden Präsentationsfenster heraus bewegt hat.

## <a name="4-scte-35-ingest"></a>4 SCTE-35-Erfassung

[SCTE-35]-Nachrichten werden im binären Format gemäß dem Schema **„urn:scte:scte35:2013a:bin“** bei Smooth-Erfassung und gemäß dem Typ **„scte35“** bei RTMP-Erfassung erfasst. Um die Konvertierung der [SCTE-35]-Zeitgebung, die auf MPEG-2-Transportdatenstrom-Präsentationszeitstempeln (PTS) basiert, zu erleichtern, wird in Form der Ereignispräsentationszeit (dem Feld „fragment_absolute_time“ bei Smooth-Erfassung und dem Feld „time“ bei RTMP-Erfassung) eine Zuordnung zwischen PTS (pts_time + pts_adjustment der splice_time()) und der Medienzeitachse bereitgestellt. Die Zuordnung ist erforderlich, da ein Rollover des 33-Bit PTS-Werts ungefähr alle 26,5 Stunden erfolgt.

Für Smooth-Datenstromerfassung ist es erforderlich, dass die Media Data Box (‚mdat‘) die in Tabelle 8-1 von [SCTE-35] definierte **splice_info_section()** enthalten MUSS. Bei der RTMP-Erfassung wird das cue-Attribut der AMF-Nachricht auf die base64-codierte **splice_info_section()** festgelegt. Wenn die Nachrichten das oben beschriebene Format aufweisen, werden sie [SCTE-67]-konform an HLS-, Smooth- und Dash-Clients gesendet.


## <a name="5-references"></a>5 Referenzen

**[SCTE-35]** ANSI/SCTE 35 2013a – Digital Program Insertion Cueing Message for Cable, 2013a (Positionsnachricht für die Einfügung von digitalen Programmen im Kabel, 2013a)

**[SCTE-67]**  ANSI/SCTE 67 2014 – empfohlene Vorgehensweise für SCTE 35: Digital Program Insertion Cueing Message for Cable (Positionsnachricht für die Einfügung von digitalen Programmen im Kabel)

**[DASH]** ISO/IEC 23009-1 2014 – Information Technology – Dynamic adaptive streaming over HTTP (Informationstechnologie – Dynamisches adaptives Streaming über HTTP) (DASH) – Teil 1: Media Presentation description und segment formats (Medienpräsentationsbeschreibung und -segmentformate), 2. Auflage

**[HLS]** [„HTTP Live Streaming“, draft-pantos-http-live-streaming-14, 14. Oktober 2014](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [„Microsoft Smooth Streaming Protocol“, 15. Mai 2014](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf) (Microsoft Smooth-Streaming-Protokoll)

**[AMF0]** [„Action Message Format AMF0“](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) (Aktionsnachrichtenformat AMF0)

**[LIVE-FMP4]** [Azure Media Services Fragmented MP4 Live Ingest Specification](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) (Spezifikation der Live-Erfassung von fragmentiertem MP4 in Azure Media Services)

**[ISO-14496-12]** ISO/IEC 14496-12: Part 12 ISO base media file format, Fourth Edition 2012-07-15 (Teil 12 ISO-Mediendatei-Basisformat, 4. Auflage, 15. Juli 2012).

**[RTMP]** [„Adobe’s Real-Time Messaging Protocol“, December 21, 2012](https://www.adobe.com/devnet/rtmp.html) (Adobe Echtzeit-Nachrichtenprotokoll, 21. Dezember 2012) 

------------------------------------------

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Media Services-Lernpfade an.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
