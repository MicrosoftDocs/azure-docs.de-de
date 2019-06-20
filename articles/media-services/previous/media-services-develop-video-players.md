---
title: Entwickeln von Videoplayeranwendungen
description: Das Thema enthält Links zu Player-Frameworks und Plug-Ins, mit denen Sie Ihre eigenen Clientanwendungen entwickeln können, die Streamingmedien aus Media Services nutzen.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465642"
---
# <a name="develop-video-player-applications"></a>Entwickeln von Videoplayeranwendungen
## <a name="overview"></a>Übersicht
Azure Media Services stellt die Tools zur Verfügung, die zum Erstellen leistungsstarker, dynamischer Clientplayeranwendungen für die gängigsten Plattformen erforderlich sind, darunter: iOS-Geräte, Android-Geräte, Windows, Windows Phone, Xbox und Set-Top-Boxen. Dieses Thema enthält auch Links zu SDKs und Player-Frameworks, mit denen Sie Ihre eigenen Clientanwendungen entwickeln können, die Streamingmedien aus Azure Media Services verarbeiten.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) ist ein Webvideoplayer für die Wiedergabe von Medieninhalten aus Microsoft Azure Media Services auf einer Vielzahl von Browsern und Geräten. Der Azure Media Player nutzt Industriestandards wie HTML5, MSE (Media Source Extensions) und EME (Encrypted Media Extensions) für ein funktionsreiches adaptives Streaming. Wenn diese Standards auf einem Gerät oder in einem Browser nicht verfügbar sind, verwendet Azure Media Player Flash und Silverlight als Fallback-Technologien. Unabhängig von der verwendeten Wiedergabetechnologie müssen Entwickler eine einheitliche Schnittstelle für JavaScript für den Zugriff auf APIs verwenden. Dies ermöglicht die Inhaltswiedergabe von Azure Media Services über eine breite Palette von Geräten und Browsern ohne jeglichen Zusatzaufwand.

Mit Microsoft Azure Media Services können Inhalte im DASH-, Smooth Streaming- und HLS-Format wiedergegeben werden. Der Azure Media Player erkennt diese unterschiedlichen Formate und übernimmt automatisch die beste Verknüpfung basierend auf den Funktionen von Plattform und Browser. Microsoft Azure Media Services ermöglichen auch die dynamische Verschlüsselung von Medienobjekten mit PlayReady-Verschlüsselung oder AES-128-Bit-Umschlagverschlüsselung. Der Azure Media Player ermöglicht bei entsprechender Konfiguration die Entschlüsselung von PlayReady- und AES-128-Bit-verschlüsselten Inhalten. 

Weitere Informationen finden Sie unter:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player-Dokumentation](https://aka.ms/ampdocs) 
* [Azure Media Player – Blog für die ersten Schritte](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Melden Sie sich an, um immer über die neuesten Informationen zu Azure Media Player auf dem Laufenden zu sein.](https://aka.ms/ampsignup)
* [Hinzufügen von neuen Funktionsanfragen, Ideen und Feedback](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Weitere Tools zum Erstellen von Playeranwendungen
Sie können auch die folgenden SDKs verwenden:

* [Smooth Streaming Client SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-App](media-services-build-smooth-streaming-apps.md)
* [Microsoft-Medienplattform: Player-Framework](https://playerframework.codeplex.com/) 
* [HTML5 Player Framework-Dokumentation](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming-Plug-In für OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Lizenzierung des Microsoft® Smooth Streaming Client Porting Kit](https://aka.ms/sspk) 
* [Entwickeln von Videoanwendungen für Xbox](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Werbung
Azure Media Services bietet Unterstützung für das Einfügen von Werbeeinblendungen mithilfe der Windows Media-Plattform: Player-Frameworks. Player-Frameworks mit Unterstützung für Werbeeinblendungen sind für Windows 8, Silverlight, Windows Phone 8 und iOS-Geräte verfügbar. Jedes Player-Framework enthält Beispielcode, der zeigt, wie eine Playeranwendung implementiert werden kann. Es gibt drei verschiedene Arten von Werbung, die Sie in Ihre Medien einfügen können:

Linear – Werbeeinblendungen auf dem kompletten Frame, die das Hauptvideo unterbrechen

Nicht linear – Overlay-Werbeeinblendungen, die während der Wiedergabe angezeigt werden; normalerweise ein Logo oder ein anderes statisches Bild im Player

Begleitend – Werbeeinblendungen, die außerhalb des Players angezeigt werden

Werbeeinblendungen können an jeder Stelle auf der Zeitachse des Hauptvideos platziert werden. Sie müssen den Player anweisen, welche Werbeeinblendungen wann wiedergegeben werden. Dies erfolgt mithilfe einer Reihe von Standarddateien im XML-Format: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) und Digital Video Player Ad Interface Definition (VPAID). VAST-Dateien geben die anzuzeigenden Werbeeinblendungen an. VMAP-Dateien geben an, wann die verschiedenen Werbeeinblendungen wiedergegeben werden, sie enthalten VAST-XML. MAST-Dateien sind eine weitere Möglichkeit zum Anordnen von Werbeeinblendungen, die auch VAST-XML enthalten können. VPAID-Dateien definieren eine Schnittstelle zwischen Video-Player und Werbeeinblendung oder Werbungsserver. Weitere Informationen finden Sie unter [Inserting Ads](https://msdn.microsoft.com/library/dn387398.aspx)(in englischer Sprache).

Informationen zu Untertiteln und der Unterstützung von Werbeeinblendungen in Livestreaming-Videos finden Sie unter [Arbeiten mit Azure Media Services-Livestreaming](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[dash.js-Repository von GitHub (in englischer Sprache)](https://github.com/Dash-Industry-Forum/dash.js)

