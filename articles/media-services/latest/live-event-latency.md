---
title: LiveEvent-Latenz in Azure Media Services | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht über die LiveEvent-Latenz und zeigt, wie Sie eine niedrige Latenz festlegen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702786"
---
# <a name="live-event-latency-in-media-services"></a>Liveereignislatenz in Media Services

Dieser Artikel zeigt, wie Sie eine niedrige Latenz für ein [Liveereignis](https://docs.microsoft.com/rest/api/media/liveevents) festlegen. Außerdem werden typische Ergebnisse beschrieben, die bei der Verwendung der Einstellungen für geringe Latenz und verschiedenen Playern angezeigt werden. Die Ergebnisse variieren basierend auf dem CDN und der Netzwerklatenz.

Um die neue Funktion **LowLatency** zu nutzen, legen Sie **StreamOptionsFlag** für **LiveEvent** auf **LowLatency** fest. Legen Sie beim Erstellen von [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) für die HLS-Wiedergabe [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) auf „1“ fest. Sobald der Stream bereit ist, können Sie im [Azure Media Player](https://ampdemo.azureedge.net/) (AMP-Demoseite) die Option für die Wiedergabe auf „Low Latency Heuristics Profile“ (Heuristische Profile mit geringer Latenz) festlegen.

> [!NOTE]
> Derzeit dient das LowLatency HeuristicProfile in Azure Media Player der Wiedergabe von Streams im MPEG-DASH-Protokoll mit dem CSF- oder CMAF-Format (z.B. `format=mdp-time-csf` oder `format=mdp-time-cmaf`). 

Das folgende .NET-Beispiel zeigt, wie Sie **LowLatency** für **LiveEvent** festlegen:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Das vollständige Beispiel finden Sie hier: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latenz von Liveereignissen

Die folgenden Tabellen zeigen übliche Latenzergebnisse (wenn das Flag „LowLatency“ aktiviert ist) in Media Services, gemessen ab dem Zeitpunkt, zu dem der Beitragsfeed den Dienst erreicht, bis zum Zeitpunkt, zu dem ein Benutzer die Wiedergabe im Player betrachtet. Um die geringe Latenz optimal zu nutzen, sollten Sie Ihre Encodereinstellungen auf 1 Sekunde GOP-Länge („Group Of Pictures“) abstimmen. Bei Verwendung einer höheren GOP-Länge minimieren Sie den Bandbreitenverbrauch und verringern die Bitrate bei gleicher Framerate. Dies ist besonders vorteilhaft bei Videos mit wenig Bewegung.

### <a name="pass-through"></a>Pass-Through 

||Niedrige Latenz aktiviert (2 Sek. GOP)|Niedrige Latenz aktiviert (1 Sek. GOP)|
|---|---|---|
|DASH in AMP|10 Sek.|8 Sek.|
|HLS in nativem iOS-Player|14 Sek.|10 Sek.|

### <a name="live-encoding"></a>Live Encoding

||Niedrige Latenz aktiviert (2 Sek. GOP)|Niedrige Latenz aktiviert (1 Sek. GOP)|
|---|---|---|
|DASH in AMP|14 Sek.|10 Sek.|
|HLS in nativem iOS-Player|18 Sek.|13 Sek.|

> [!NOTE]
> Die End-to-End-Latenz hängt vom lokalen Netzwerk ab und kann von einer CDN-Cachingebene beeinträchtigt werden. Testen Sie Ihre genauen Konfigurationen.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)

