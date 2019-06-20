---
title: Übersicht über Livestreaming mit Azure Media Services v3| Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über das Livestreaming mit Azure Media Services v3.
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
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: fa09185e68c8d3a70562fe50c583ff872bf91e48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65556225"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Livestreaming mit Azure Media Services v3

Mit Azure Media Services können Sie Ihren Kunden Liveereignisse in der Azure Cloud anbieten. Um Liveereignisse mit Media Services zu streamen, benötigen Sie Folgendes:  

- Eine Kamera, mit der das Liveereignis aufgenommen wird.<br/>Ausrüstungsanregungen finden Sie unter [Simple and Portable Event Video Gear Setup]( https://link.medium.com/KNTtiN6IeT) (Einfache und tragbare Videoausrüstung für Veranstaltungen).

    Wenn Sie keinen Zugriff auf eine Kamera haben, können Sie mit Tools wie [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) einen Livefeed aus einer Videodatei generieren.
- Ein Livevideoencoder, der Signale von einer Kamera (oder einem anderen Gerät, etwa einem Laptop) in Streams konvertiert, die an Media Services gesendet werden. Der Datenfeed kann Signale zu Werbezwecken enthalten, beispielsweise SCTE-35-Markierungen.<br/>Eine Liste mit empfohlenen Livestreaming-Encodern finden Sie [hier](recommended-on-premises-live-encoders.md). Sehen Sie sich auch den folgenden Blog an: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Livestreamproduktion mit OBS).
- Komponenten in Media Services, mit denen das Liveereignis erfasst, in einer Vorschau angezeigt, paketiert, aufgezeichnet, verschlüsselt und an Ihre Kunden übertragen oder zur weiteren Verteilung an ein CDN gesendet wird.

Dieser Artikel enthält eine Übersicht und Anleitungen für Livestreaming mit Media Services sowie Links zu anderen relevanten Artikeln.

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dynamische Paketerstellung

Mit Media Services können Sie die [dynamische Paketerstellung](dynamic-packaging-overview.md) nutzen, mit der Sie Ihre Livestreams im [MPEG DASH-, HLS- und Smooth Streaming-Format](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) aus dem an den Dienst gesendeten Beitragsfeed als Vorschau anzeigen und übertragen können. Ihre Zuschauer können den Livestream mit einem beliebigen Player wiedergeben, der mit HLS, DASH oder Smooth Streaming kompatibel ist. Sie können den [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) in Ihren Webanwendungen oder mobilen Anwendungen nutzen, um Ihren Stream mit einem beliebigen dieser Protokolle bereitzustellen.

## <a name="dynamic-encryption"></a>Dynamische Verschlüsselung

Mit der dynamischen Verschlüsselung können Sie Ihre Live- oder On-Demand-Inhalte mit AES-128 oder einem der drei hauptsächlichen DRM-Systeme (Digital Rights Management) dynamisch verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. Weitere Informationen finden Sie unter [Dynamische Verschlüsselung](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dynamisches Manifest

Mithilfe der dynamischen Filterung kann die Anzahl von Spuren, Formaten, Bitraten und Präsentationszeitfenstern gesteuert werden, die an die Player gesendet werden. Weitere Informationen finden Sie unter [Filter und dynamische Manifeste](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Liveereignistypen

Für ein Liveereignis ist einer von zwei Typen möglich: Pass-Through und Livecodierung. Weitere Informationen zum Livestreaming in Media Services v3 finden Sie unter [Liveereignisse und Liveausgaben](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-Through

![Pass-Through](./media/live-streaming/pass-through.svg)

Wenn Sie das **Liveereignis** vom Typ „Pass-Through“ verwenden, stützen Sie sich auf Ihren lokalen Liveencoder, um einen Videostream mit mehreren Bitraten zu erzeugen und als Beitragsfeed an das Liveereignis zu senden (über RTMP oder das Protokoll für fragmentiertes MP4). Das Liveereignis leitet dann die eingehenden Videostreams ohne weitere Bearbeitung weiter. Ein Liveereignis vom Typ „Pass-Through“ ist für Liveereignisse mit langer Laufzeit oder für ein lineares 24x365-Livestreaming optimiert. 

### <a name="live-encoding"></a>Live Encoding  

![Livecodierung](./media/live-streaming/live-encoding.svg)

Wenn Sie die Livecodierung mit Media Services verwenden, konfigurieren Sie Ihren lokalen Liveencoder so, dass er ein Video mit einer einzelnen Bitrate als Beitragsfeed an das Liveereignis sendet (über RTMP oder das Protokoll für fragmentiertes MP4). Das Liveereignis codiert diesen eingehenden Stream mit einer einzelnen Bitrate in einen [Videostream mit mehreren Bitraten](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) und stellt ihn über Protokolle wie MPEG-DASH, HLS und Smooth Streaming für Wiedergabegeräte zur Verfügung. 

## <a name="live-streaming-workflow"></a>Workflow für das Livestreaming

Um die Livestreaming-Workflows in Media Services v3 nachvollziehen zu können, müssen Sie zunächst mit folgenden Konzepten vertraut sein: 

- [API für Streamingendpunkte](streaming-endpoint-concept.md)
- [API für Liveereignisse und Liveausgaben](live-events-outputs-concept.md)
- [API für Streaminglocators](streaming-locators-concept.md)

### <a name="general-steps"></a>Allgemeine Schritte

1. Vergewissern Sie sich in Ihrem Media Services-Konto, dass der **Streamingendpunkt** (Ursprung) ausgeführt wird. 
2. Erstellen Sie ein [Liveereignis](live-events-outputs-concept.md). <br/>Bei der Ereigniserstellung können Sie angeben, dass das Ereignis automatisch gestartet werden soll. Starten Sie alternativ das Ereignis, wenn Sie zum Starten des Streamings bereit sind.<br/> Wenn für den automatischen Start „true“ festgelegt ist, wird das Liveereignis direkt nach der Erstellung gestartet. Die Abrechnung beginnt, sobald das Liveereignis startet. Sie müssen für die Liveereignisressource explizit „Beenden“ auswählen, damit keine Gebühren mehr anfallen. Weitere Informationen finden Sie im Abschnitt [LiveEvent-Zustandswerte und Abrechnung](live-event-states-billing.md).
3. Rufen Sie die Erfassungs-URLs ab, und konfigurieren Sie Ihren lokalen Encoder vor Ort, um die URL zum Senden des Beitragsfeeds zu verwenden.<br/>Siehe [Empfohlene Livestreaming-Encoder](recommended-on-premises-live-encoders.md).
4. Rufen Sie die Vorschau-URL ab und verwenden Sie sie, um sich zu vergewissern, dass die Eingabe des Encoders auch tatsächlich empfangen wird.
5. Erstellen Sie ein neues **Medienobjekt**.
6. Erstellen Sie eine **Liveausgabe**, und verwenden Sie den Namen des erstellten Medienobjekts.<br/>Die **Liveausgabe** archiviert den Datenstrom im **Medienobjekt**.
7. Erstellen Sie einen **Streaminglocator** mit den integrierten Arten von **Streamingrichtlinien**.<br/>Wenn Sie beabsichtigen, den Inhalt zu verschlüsseln, lesen Sie [Übersicht über den Inhaltsschutz](content-protection-overview.md).
8. Listen Sie die Pfade auf dem **StreamingLocator** auf, um die zu verwendenden URLs zurückzugeben (diese sind deterministisch).
9. Rufen Sie den Hostnamen für den **Streamingendpunkt** (Ursprung) ab, von dem aus Sie streamen möchten.
10. Kombinieren Sie die URL aus Schritt 8 mit dem Hostnamen aus Schritt 9, um die vollständige URL zu erhalten.
11. Wenn Sie möchten, dass Ihr **Liveereignis** nicht mehr angesehen werden kann, müssen Sie das Streamen des Ereignisses beenden und den **Streaminglocator** löschen.

## <a name="other-important-articles"></a>Weitere wichtige Artikel

- [Empfohlene Liveencoder](recommended-on-premises-live-encoders.md)
- [Verwenden eines Cloud-DVR](live-event-cloud-dvr.md)
- [Vergleich der LiveEvent-Typen](live-event-types-comparison.md)
- [Zustandswerte und Abrechnung](live-event-states-billing.md)
- [Latenz](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)
* [Hinweise zur Migration von Media Services v2 zu v3](migrate-from-v2-to-v3.md)
