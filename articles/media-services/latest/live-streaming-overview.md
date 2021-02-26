---
title: Übersicht über Livestreaming
description: Dieser Artikel bietet eine Übersicht über das Livestreaming mit Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b552dcc0e6766316e55e9cdda6e462b2d4abfd2b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955918"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Livestreaming mit Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Azure Media Services können Sie Ihren Kunden Liveereignisse in der Azure Cloud anbieten. Um Liveereignisse mit Media Services zu streamen, benötigen Sie Folgendes:  

- Eine Kamera, mit der das Liveereignis aufgenommen wird.<br/>Ausrüstungsanregungen finden Sie unter [Simple and Portable Event Video Gear Setup]( https://link.medium.com/KNTtiN6IeT) (Einfache und tragbare Videoausrüstung für Veranstaltungen).

    Wenn Sie keinen Zugriff auf eine Kamera haben, können Sie mit Tools wie [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) einen Livefeed aus einer Videodatei generieren.
- Ein Livevideoencoder, der Signale von einer Kamera (oder einem anderen Gerät, etwa einem Laptop) in Streams konvertiert, die an Media Services gesendet werden. Der Datenfeed kann Signale zu Werbezwecken enthalten, beispielsweise SCTE-35-Markierungen.<br/>Eine Liste mit empfohlenen Livestreaming-Encodern finden Sie [hier](recommended-on-premises-live-encoders.md). Sehen Sie sich auch den folgenden Blog an: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Livestreamproduktion mit OBS).
- Komponenten in Media Services, mit denen das Liveereignis erfasst, in einer Vorschau angezeigt, paketiert, aufgezeichnet, verschlüsselt und an Ihre Kunden übertragen oder zur weiteren Verteilung an ein CDN gesendet wird.

Für Kunden, die Inhalte für große Zielgruppen im Internet bereitstellen möchten, empfehlen wir, ein CDN für den [Streamingendpunkt](streaming-endpoint-concept.md) zu aktivieren.

Dieser Artikel enthält eine Übersicht und Anleitungen für Livestreaming mit Media Services sowie Links zu anderen relevanten Artikeln.
 
> [!NOTE]
> Sie können das [Azure-Portal](https://portal.azure.com/) für Folgendes nutzen: Verwalten von v3-[Liveereignissen](live-events-outputs-concept.md), Anzeigen von v3-[Objekten](assets-concept.md) und Abrufen von Informationen zum Zugreifen auf APIs. Verwenden Sie für alle anderen Verwaltungsaufgaben (etwa für Transformationen und Aufträge) die [REST-API](/rest/api/media/), die [CLI](/cli/azure/ams) oder eins der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging-and-delivery"></a>Dynamische Paketerstellung und Bereitstellung

Mit Media Services können Sie die [dynamische Paketerstellung](dynamic-packaging-overview.md) nutzen, mit der Sie Ihre Livestreams im [MPEG DASH-, HLS- und Smooth Streaming-Format](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) aus dem an den Dienst gesendeten Beitragsfeed als Vorschau anzeigen und übertragen können. Ihre Zuschauer können den Livestream mit einem beliebigen Player wiedergeben, der mit HLS, DASH oder Smooth Streaming kompatibel ist. Sie können den [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) in Ihren Webanwendungen oder mobilen Anwendungen nutzen, um Ihren Stream mit einem beliebigen dieser Protokolle bereitzustellen.

## <a name="dynamic-encryption"></a>Dynamische Verschlüsselung

Mit der dynamischen Verschlüsselung können Sie Ihre Live- oder On-Demand-Inhalte mit AES-128 oder einem der drei hauptsächlichen DRM-Systeme (Digital Rights Management) dynamisch verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. Weitere Informationen finden Sie unter [Dynamische Verschlüsselung](content-protection-overview.md).

> [!NOTE]
> Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="dynamic-filtering"></a>Dynamisches Filtern

Mithilfe der dynamischen Filterung kann die Anzahl von Spuren, Formaten, Bitraten und Präsentationszeitfenstern gesteuert werden, die an die Player gesendet werden. Weitere Informationen finden Sie unter [Filter und dynamische Manifeste](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Liveereignistypen

[Liveereignisse](/rest/api/media/liveevents) sorgen für das Erfassen und Verarbeiten von Livevideofeeds. Ein Liveereignis kann entweder auf eine *Pass-Through*- (ein lokaler Liveencoder sendet einen Stream mit mehreren Bitraten) oder *Livecodierung* (ein lokaler Liveencoder sendet einen Stream mit Einzelbitrate) festgelegt werden. Weitere Informationen zum Livestreaming in Media Services v3 finden Sie unter [Liveereignisse und Liveausgaben](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-Through

![Diagramm, das zeigt, wie die Video- und Audiofeeds von einem Passthrough-Liveereignis erfasst und verarbeitet werden.](./media/live-streaming/pass-through.svg)

Wenn Sie das **Liveereignis** vom Typ „Pass-Through“ verwenden, stützen Sie sich auf Ihren lokalen Liveencoder, um einen Videostream mit mehreren Bitraten zu erzeugen und als Beitragsfeed an das Liveereignis zu senden (über RTMP oder das Eingabeprotokoll für fragmentiertes MP4). Das Liveereignis wird dann ohne jede weitere Transcodierung über die eingehenden Videostreams an die dynamische Paketerstellung (Streamingendpunkt) übertragen. Ein Liveereignis vom Typ „Pass-Through“ ist für Liveereignisse mit langer Laufzeit oder für ein lineares 24x365-Livestreaming optimiert. 

### <a name="live-encoding"></a>Live Encoding  

![Livecodierung](./media/live-streaming/live-encoding.svg)

Wenn Sie die Cloudcodierung mit Media Services verwenden, konfigurieren Sie Ihren lokalen Liveencoder so, dass er ein Einzelbitraten-Video als Beitragsfeed (bis zu 32 MBit/s aggregiert) an das Liveereignis sendet (über RTMP oder das Eingabeprotokoll für fragmentiertes MP4). Vom Liveereignis wird der eingehende Einzelbitraten-Stream in [Mehrfachbitraten-Videostreams](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) mit unterschiedlichen Auflösungen transcodiert, um die Übermittlung zu verbessern und die Bereitstellung für Wiedergabegeräte über Protokolle gemäß Branchenstandard wie MPEG-DASH, Apple HTTP Live Streaming (HLS) und Microsoft Smooth Streaming zu ermöglichen. 

### <a name="live-transcription-preview"></a>Livetranskription (Preview)

Die Livetranskription ist ein Feature, das Sie mit Liveereignissen verwenden können, bei denen es sich um Pass-Through oder Live Encoding handelt. Weitere Informationen finden Sie unter [Livetranskription](live-transcription.md). Wenn dieses Feature aktiviert wird, nutzt der Dienst das [Spracherkennungsfeature](../../cognitive-services/speech-service/speech-to-text.md) von Cognitive Services, um den gesprochenen Text in der eingehenden Audiodatei in Text zu konvertieren. Dieser Text wird dann mitsamt Video- und Audiodaten in den MPEG-DASH- und HLS-Protokollen für die Übermittlung zur Verfügung gestellt.

> [!NOTE]
> Derzeit ist die Livetranskription als Previewfeature in der Region „USA, Westen 2“ verfügbar.

## <a name="live-streaming-workflow"></a>Workflow für das Livestreaming

Um die Livestreaming-Workflows in Media Services v3 nachvollziehen zu können, müssen Sie zunächst mit folgenden Konzepten vertraut sein: 

- [Streamingendpunkte](streaming-endpoint-concept.md)
- [Liveereignisse und Liveausgaben](live-events-outputs-concept.md)
- [Streaminglocators](streaming-locators-concept.md)

### <a name="general-steps"></a>Allgemeine Schritte

1. Vergewissern Sie sich in Ihrem Media Services-Konto, dass der **Streamingendpunkt** (Ursprung) ausgeführt wird. 
2. Erstellen Sie ein [Liveereignis](live-events-outputs-concept.md). <br/>Bei der Ereigniserstellung können Sie angeben, dass das Ereignis automatisch gestartet werden soll. Starten Sie alternativ das Ereignis, wenn Sie zum Starten des Streamings bereit sind.<br/> Wenn für den automatischen Start „true“ festgelegt ist, wird das Liveereignis direkt nach der Erstellung gestartet. Die Abrechnung beginnt, sobald das Liveereignis startet. Sie müssen für die Liveereignisressource explizit „Beenden“ auswählen, damit keine Gebühren mehr anfallen. Weitere Informationen finden Sie im Abschnitt [LiveEvent-Zustandswerte und Abrechnung](live-event-states-billing.md).
3. Rufen Sie die Erfassungs-URLs ab, und konfigurieren Sie Ihren lokalen Encoder vor Ort, um die URL zum Senden des Beitragsfeeds zu verwenden.<br/>Siehe [Empfohlene Livestreaming-Encoder](recommended-on-premises-live-encoders.md).
4. Rufen Sie die Vorschau-URL ab und verwenden Sie sie, um sich zu vergewissern, dass die Eingabe des Encoders auch tatsächlich empfangen wird.
5. Erstellen Sie ein neues **Medienobjekt**. 

    Jeder Liveausgabe ist ein Medienobjekt zugeordnet, das zum Aufzeichnen des Videos im zugeordneten Azure Blob Storage-Container verwendet wird. 
6. Erstellen Sie eine **Liveausgabe**, und verwenden Sie den von Ihnen erstellten Medienobjektnamen, damit der Datenstrom im Medienobjekt archiviert werden kann.

    Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. Wenn Sie die Liveausgabe löschen, werden das zugrunde liegende Medienobjekt und dessen Inhalt nicht gelöscht.
7. Erstellen Sie einen **Streaminglocator** mit den [integrierten Arten von Streamingrichtlinien](streaming-policy-concept.md).

    Zum Veröffentlichen der Liveausgabe müssen Sie einen Streaminglocator für das zugehörige Medienobjekt erstellen. 
8. Listen Sie die Pfade auf dem **StreamingLocator** auf, um die zu verwendenden URLs zurückzugeben (diese sind deterministisch).
9. Rufen Sie den Hostnamen für den **Streamingendpunkt** (Ursprung) ab, von dem aus Sie streamen möchten.
10. Kombinieren Sie die URL aus Schritt 8 mit dem Hostnamen aus Schritt 9, um die vollständige URL zu erhalten.
11. Wenn Sie möchten, dass Ihr **Liveereignis** nicht mehr angesehen werden kann, müssen Sie das Streamen des Ereignisses beenden und den **Streaminglocator** löschen.
12. Wenn die Streamingereignisse beendet sind und Sie die zuvor bereitgestellten Ressourcen bereinigen möchten, führen Sie das folgende Verfahren aus:

    * Beenden Sie die Datenstromeingabe vom Encoder.
    * Beenden Sie das Liveereignis. Nach Beendigung des Liveereignisses fallen dafür keine Kosten mehr an. Wenn Sie den Kanal erneut starten, weist er die gleiche Erfassungs-URL auf, damit Sie den Encoder nicht erneut konfigurieren müssen.
    * Sie können Ihren Streamingendpunkt beenden, sofern Sie das Archiv Ihres Liveereignisses nicht als bedarfsgesteuerten Datenstrom bereitstellen möchten. Hat das Liveereignis den Status „Beendet“, fallen dafür keine Kosten an.

Das Medienobjekt, in dem die Liveausgabe archiviert wird, wird automatisch zu einem bedarfsgesteuerten Medienobjekt, wenn die Liveausgabe gelöscht wird. Sie müssen alle Liveausgaben löschen, bevor ein Liveereignis angehalten werden kann. Sie können ein optionales [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body)-Flag verwenden, um Liveausgaben beim Anhalten automatisch zu entfernen. 

> [!TIP]
> Im Artikel [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md) wird der Code untersucht, der die oben beschriebenen Schritte implementiert.

## <a name="other-important-articles"></a>Weitere wichtige Artikel

- [Empfohlene Liveencoder](recommended-on-premises-live-encoders.md)
- [Verwenden eines Cloud-DVR](live-event-cloud-dvr.md)
- [Vergleich der Features der LiveEvent-Typen](live-event-types-comparison.md)
- [Zustandswerte und Abrechnung](live-event-states-billing.md)
- [Latenz](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Lesen Sie den Artikel mit [häufig gestellten Fragen](frequently-asked-questions.md#live-streaming).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart für Livestreaming](live-events-wirecast-quickstart.md)
* [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)
* [Hinweise zur Migration von Media Services v2 zu v3](migrate-v-2-v-3-migration-introduction.md)