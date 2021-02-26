---
title: Von Media Services empfohlene Livestreaming-Encoder
description: Erfahren Sie etwas über die von Media Services empfohlenen lokalen Livestreaming-Encoder.
services: media-services
keywords: Codierung; Encoder; Medien
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955138"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Überprüfte lokale Livestreamingencoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services stellt ein [Liveereignis](/rest/api/media/liveevents) (Kanal) eine Pipeline zum Verarbeiten von Livestreaminginhalten dar. Es gibt zwei Arten, auf die Live-Eingabestreams vom Liveereignis empfangen werden können.

* Von einem lokalen Liveencoder wird ein RTMP- oder Smooth Streaming-Datenstrom (fragmentiertes MP4) mit Mehrfachbitrate an das Liveereignis gesendet, das nicht für die Livecodierung mit Media Services aktiviert ist. Die erfassten Streams durchlaufen Liveereignisse ohne weitere Verarbeitung. Diese Methode wird als **Pass-Through-Methode** bezeichnet. Um auf Clientseite ein Streaming mit adaptiver Bitrate zu ermöglichen, sollte der Liveencoder anstelle eines Einzelbitraten-Datenstroms Mehrfachbitraten-Datenströme an ein Pass-Through-Liveereignis senden. 

    Bei Verwendung von Mehrfachbitraten-Datenströmen für das Pass-Through-Liveereignis müssen die Video-GOP-Größe und die Videofragmente unterschiedlicher Bitraten synchronisiert werden, um ein unerwartetes Verhalten bei der Wiedergabe zu vermeiden.

  > [!TIP]
  > Die Verwendung der Pass-Through-Methode ist die wirtschaftlichste Form des Livestreamings.
 
* Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an das Liveereignis, das zum Ausführen der Livecodierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Liveereignis wird dann eine Livecodierung des Eingabestreams mit Einzelbitrate in einen (adaptiven) Videostream mit Mehrfachbitrate ausgeführt.

In diesem Artikel werden überprüfte lokale Livestreamingencoder behandelt. Die Überprüfung erfolgt durch den Anbieter direkt oder durch den Kunden. Microsoft Azure Media Services selbst führt keine vollständigen Tests oder strenge Überprüfungen der Encoder durch und überprüft auch nicht regelmäßig, ob neue Updates vorhanden sind. Anweisungen zum Überprüfen Ihres lokalen Liveencoders finden Sie unter [Überprüfen Ihres lokalen Encoders](become-on-premises-encoder-partner.md).

Ausführliche Informationen zur Livecodierung mit Media Services finden Sie unter [Livestreaming mit Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Anforderungen für Encoder

Encoder müssen TLS 1.2 unterstützen, wenn HTTPS- oder RTMPS-Protokolle verwendet werden.

## <a name="live-encoders-that-output-rtmp"></a>Liveencoder mit RTMP-Ausgabe

Media Services empfiehlt die Verwendung eines der nachfolgenden Liveencoder mit RTMP-Ausgabe. Die unterstützten URL-Schemas sind `rtmp://` und `rtmps://`.

Überprüfen Sie beim Streamen per RTMP in den Firewall- und/oder Proxyeinstellungen, ob die ausgehenden TCP-Ports 1935 und 1936 geöffnet sind.<br/><br/>
Überprüfen Sie beim Streamen per RTMPS in den Firewall- und/oder Proxyeinstellungen, ob die ausgehenden TCP-Ports 2935 und 2936 geöffnet sind.

> [!NOTE]
> Bei Verwendung der RTMPS-Protokolle müssen die Encoder TLS 1.2 unterstützen.

- Adobe Flash Media Live Encoder 3.2
- [Antix Digital](http://www.antixdigital.com/) StreamZ Live (zuvor Imagine Communication SelenioFlex Live)
- [Blackmagic ATEM Mini und ATEM Mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (Version 2.14.15 und höher)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 und Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (Version 13.0.2 oder höher aufgrund der TLS 1.2-Anforderung)
- Telestream Wirecast S (Es wird nur RTMP unterstützt. Keine RTMPS-Unterstützung aufgrund der erforderlichen Version von TLS 1.2 oder höher.)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> Diese Liste mit Encodern stellt nur eine Empfehlung dar. Microsoft führt keine regelmäßigen Tests oder Überprüfungen von Encodern durch. Von Encoderanbietern oder Open-Source-Projekten bereitgestellte Updates oder Breaking Changes können sich negativ auf die Kompatibilität auswirken. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Liveencoder mit Ausgabe im fragmentierten MP4-Format (Smooth Streaming-Erfassung)

Media Services empfiehlt den Einsatz eines der nachfolgenden Liveencoder, der Smooth Streaming mit Mehrfachbitrate (fragmentiertes MP4) ausgibt. Die unterstützten URL-Schemas sind `http://` und `https://`.

> [!NOTE]
> Bei Verwendung von HTTPS-Protokollen müssen Encoder TLS 1.2 unterstützen.

- Ateme TITAN Live
- [Antix Digital](http://www.antixdigital.com/) StreamZ Live (zuvor Imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elemental Live (Version 2.14.15 und höher aufgrund der TLS 1.2-Anforderung)
- Envivio-4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live und Hero 4K (UHD/HEVC)

> [!TIP]
>  Wenn Sie Liveveranstaltungen in mehreren Sprachen streamen (z.B. eine englische Audiospur und eine spanische Audiospur), können Sie dies mit dem Media Excel Live Encoder erreichen, der so konfiguriert ist, dass er den Livefeed an ein Pass-Through-Liveereignis sendet.

> [!WARNING]
> Diese Liste mit Encodern stellt nur eine Empfehlung dar. Microsoft führt keine regelmäßigen Tests oder Überprüfungen von Encodern durch. Von Encoderanbietern oder Open-Source-Projekten bereitgestellte Unterstützung oder Fehler können sich negativ auf die Kompatibilität auswirken. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurieren von lokalen Liveencodereinstellungen

Informationen über die gültigen Einstellungen für Ihren Liveereignistyp finden Sie unter [Vergleich von Liveereignistypen](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wiedergabeanforderungen

Damit Inhalte wiedergegeben werden können, müssen sowohl ein Audio- als auch ein Videostream vorhanden sein. Die Wiedergabe nur des Videostreams wird nicht unterstützt.

### <a name="configuration-tips"></a>Konfigurationstipps

- Verwenden Sie nach Möglichkeit eine Kabelverbindung zum Internet.
- Zum Bestimmen der erforderlichen Bandbreite müssen die Streamingbitraten verdoppelt werden. Wenngleich nicht bindend, ist diese einfache Regel hilfreich, um Netzwerküberlastungen zu verhindern.
- Bei der Verwendung softwarebasierter Encoder schließen Sie alle nicht benötigten Programme.
- Das Ändern der Encoderkonfiguration, nachdem die Pushübertragung begonnen hat, führt zu negativen Auswirkungen auf das Ereignis. Durch Konfigurationsänderungen kann das Ereignis instabil werden. 
- Testen und überprüfen Sie stets neue Versionen der Encodersoftware, um die Kompatibilität mit Azure Media Services sicherzustellen. Die hier aufgelisteten Encoder werden von Microsoft nicht erneut überprüft. Softwareanbieter überprüfen die Encoder meist direkt und zertifizieren sie selbst.
- Nehmen Sie sich zum Einrichten Ihres Ereignisses unbedingt ausreichend Zeit. Für Großereignisse empfiehlt es sich, eine Stunde vor dem Ereignis mit dem Setup zu beginnen.
- Verwenden Sie die H.264-Video- und die AAC-LC-Audiocodecausgabe.
- Verwenden Sie für den Liveereignistyp, den Sie übertragen, nur die unterstützten Auflösungen und Frameraten (eine Rate von 60 BpS wird beispielsweise derzeit abgelehnt).
- Stellen Sie sicher, dass eine zeitliche Ausrichtung anhand von Keyframe oder GOP übergreifend über Videoqualitäten hinweg vorliegt.
- Vergewissern Sie sich, dass jede Videoqualität durch einen eindeutigen Streamnamen gekennzeichnet ist.
- Verwenden Sie strenge CBR-Codierung, die für eine optimale Leistung bei adaptiver Bitrate empfohlen wird.

> [!IMPORTANT]
> Beobachten Sie die Hardwareressourcen des Computers (CPU, Arbeitsspeicher usw.), da das Hochladen von Fragmenten in die Cloud CPU- und E/A-Vorgänge mit sich bringt. Wenn Sie Einstellungen im Encoder ändern, achten Sie darauf, die Kanäle/Liveereignisse zurückzusetzen, damit die Änderung wirksam wird.

## <a name="see-also"></a>Weitere Informationen

[Livestreaming mit Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen des Encoders](become-on-premises-encoder-partner.md)
