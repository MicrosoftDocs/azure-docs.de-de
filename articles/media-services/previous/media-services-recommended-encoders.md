---
title: Informationen zu von Azure Media Services empfohlenen Encodern | Microsoft-Dokumentation
description: In diesem Artikel werden die von Azure Media Services empfohlenen lokalen Encoder aufgelistet.
services: media-services
keywords: Codierung; Encoder; Medien
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 11456a8dc55a98778bf44225bf659e506e2d3a3f
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510055"
---
# <a name="recommended-on-premises-encoders"></a>Empfohlene lokale Encoder

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Beim Livestreaming mit Azure Media Services können Sie angeben, wie der Kanal den Eingabedatenstrom empfangen soll. Wenn Sie sich für die Verwendung eines lokalen Encoders mit einem Livecodierkanal entscheiden, sollte der Encoder einen qualitativ hochwertigen Datenstrom mit Einzelbitrate als Ausgabe übertragen. Ziehen Sie dagegen einen lokalen Encoder mit Pass-Through-Kanal vor, dann muss Ihr Encoder einen Stream mit Mehrfachbitrate als Ausgabe übertragen. Weitere Informationen finden Sie unter [Livestreaming mit lokalen Encodern](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Anforderungen für Encoder

Encoder müssen TLS 1.2 unterstützen, wenn HTTPS- oder RTMPS-Protokolle verwendet werden.

## <a name="live-encoders-that-output-rtmp"></a>Liveencoder mit RTMP-Ausgabe 

Azure Media Services empfiehlt die Verwendung eines der nachfolgenden Live-Encoder mit RTMP-Ausgabe:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (Version 13.0.2 oder höher aufgrund der TLS 1.2-Anforderung)

  Encoder müssen TLS 1.2 unterstützen, wenn RTMPS-Protokolle verwendet werden.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Liveencoder mit Ausgabe im fragmentierten MP4-Format 

Azure Media Services empfiehlt die Verwendung eines der nachfolgenden Liveencoder, der fragmentierte MP4 mit Mehrfachbitrate (Smooth Streaming) ausgibt:

- Media Excel Hero Live und Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (Version 2.14.15 und höher aufgrund der TLS 1.2-Anforderung)

  Encoder müssen TLS 1.2 unterstützen, wenn HTTPS-Protokolle verwendet werden.
- Envivio-4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Ein Live-Encoder kann einen Datenstrom mit Einzelbitrate an einen Pass-Through-Kanal senden, doch wird von dieser Konfiguration abgeraten, weil in diesem Fall ein Streaming mit adaptiver Bitrate auf Clientseite nicht möglich ist.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>So werden Sie Partner für lokale Encoder

Wenn Sie Azure Media Services-Partner für lokale Encoder werden, unterstützt Media Services Ihr Produkt, indem es Ihren Encoder Unternehmenskunden empfiehlt. Um Partner für lokale Encoder zu werden, müssen Sie die Kompatibilität Ihres lokalen Encoders mit Media Services bestätigen. Führen Sie hierzu die folgenden Überprüfungsschritte aus:

Pass-Through-Kanalüberprüfung
1. Erstellen Sie ein Azure Media Services-Konto bzw. melden Sie sich bei Ihrem Konto an.
2. Erstellen und starten Sie einen **Pass-Through-Kanal**.
3. Konfigurieren Sie Ihren Encoder so, dass er einen Livestream mit Mehrfachbitrate ausgibt.
4. Erstellen Sie ein veröffentlichtes Live-Ereignis.
5. Lassen Sie Ihren Live-Encoder ungefähr 10 Minuten lang laufen.
6. Beenden Sie das Live-Ereignis.
7. Starten Sie einen Streaming-Endpunkt, verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um das archivierte Medienobjekt zu betrachten und sicherzustellen, dass die Wiedergabe in keiner der Qualitätsstufen sichtbare Störungen aufweist (oder beobachten und validieren Sie dies alternativ über die Vorschau-URL während der Live-Sitzung vor Schritt 6).
8. Vermerken Sie die Medienobjekt-ID, die veröffentlichte Streaming-URL für das Livearchiv und die Einstellungen sowie die verwendete Version Ihres Live-Encoders.
9. Setzen Sie jeweils nach Erstellen einer Probe den Kanalzustand zurück.
10. Wiederholen Sie die Schritte 3 bis 9 für alle Konfigurationen, die von Ihrem Encoder unterstützt werden (mit und ohne Werbesignalisierung oder Untertiteln sowie in verschiedenen Codiergeschwindigkeiten).

Livecodierkanal-Überprüfung
1. Erstellen Sie ein Azure Media Services-Konto bzw. melden Sie sich bei Ihrem Konto an.
2. Erstellen und starten Sie einen **Livecodierkanal**.
3. Konfigurieren Sie den Encoder für die Übertragung eines Livestreams mit Einzelbitrate.
4. Erstellen Sie ein veröffentlichtes Live-Ereignis.
5. Lassen Sie Ihren Live-Encoder ungefähr 10 Minuten lang laufen.
6. Beenden Sie das Live-Ereignis.
7. Starten Sie einen Streaming-Endpunkt, verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um das archivierte Medienobjekt zu betrachten und sicherzustellen, dass die Wiedergabe in keiner der Qualitätsstufen sichtbare Störungen aufweist (oder beobachten und validieren Sie dies alternativ über die Vorschau-URL während der Live-Sitzung vor Schritt 6).
8. Vermerken Sie die Medienobjekt-ID, die veröffentlichte Streaming-URL für das Livearchiv und die Einstellungen sowie die verwendete Version Ihres Live-Encoders.
9. Setzen Sie jeweils nach Erstellen einer Probe den Kanalzustand zurück.
10. Wiederholen Sie die Schritte 3 bis 9 für alle Konfigurationen, die von Ihrem Encoder unterstützt werden (mit und ohne Werbesignalisierung oder Untertiteln sowie in verschiedenen Codiergeschwindigkeiten).

Überprüfung der Lebensdauer
1. Erstellen Sie ein Azure Media Services-Konto bzw. melden Sie sich bei Ihrem Konto an.
2. Erstellen und starten Sie einen **Pass-Through-Kanal**.
3. Konfigurieren Sie Ihren Encoder so, dass er einen Livestream mit Mehrfachbitrate ausgibt.
4. Erstellen Sie ein veröffentlichtes Live-Ereignis.
5. Lassen Sie Ihren Live-Encoder für mindestens eine Woche laufen.
6. Verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um das Live-Streaming gelegentlich zu beobachten (oder archivierte Medienobjekte), um sicherzustellen, dass die Wiedergabe keine sichtbaren Störungen aufweist.
7. Beenden Sie das Live-Ereignis.
8. Vermerken Sie die Medienobjekt-ID, die veröffentlichte Streaming-URL für das Livearchiv und die Einstellungen sowie die verwendete Version Ihres Live-Encoders.

Senden Sie Ihre vermerkten Einstellungen und die Live-Archivparameter abschließend an Media Services (per E-Mail an amsstreaming@microsoft.com). Nach Erhalt werden von Media Services Bestätigungstests mit den mit Ihrem Live-Encoder erstellten Proben durchgeführt. Setzen Sie sich bei Fragen zu dieser Vorgehensweise mit Media Services in Verbindung.
