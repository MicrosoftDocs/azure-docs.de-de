---
title: Azure Media Services-Terminologie und -Konzepte – Azure | Microsoft-Dokumentation
description: Dieses Thema bietet eine kurze Übersicht über die Terminologie und die Konzepte von Azure Media Services und stellt Links zu weiteren Details zur Verfügung.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1e76569c7f5157dce681d15ec8d499b90e080102
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762304"
---
# <a name="media-services-concepts"></a>Media Services-Konzepte

Dieses Thema bietet eine kurze Übersicht über die Azure Media Services-Terminologie und -Konzepte. Darüber hinaus enthält der Artikel Links zu Artikeln mit ausführlicheren Erläuterungen zu Konzepten und Funktionen von Media Services v3. 

Es empfiehlt sich, sich vor Entwicklungsbeginn mit den grundlegenden Konzepten vertraut zu machen, die in diesen Themen behandelt werden.

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Begriff

In diesem Abschnitt wird beschrieben, inwiefern sich einige allgemeine Begriffe aus der Branche der Media Services v3-API zuordnen lassen.

### <a name="live-event"></a>Liveereignis

Ein **Liveereignis** stellt eine Pipeline zum Erfassen, Transcodieren (optional) und Packen von Video- und Audiolivestreams sowie von Echtzeit-Metadaten dar.

Für Kunden, die von Media Services v2-APIs migrieren, ersetzt das **Liveereignis** die **Channel**-Entität in v2. Weitere Informationen finden Sie unter [Hinweise zur Migration von Media Services v2 zu v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Streamingendpunkt (Paketerstellung und Ursprung)

Ein **Streamingendpunkt** stellt einen dynamischen (Just-In-Time-) Paketerstellungs- und Ursprungsdienst dar, der Ihre Live- und On-Demand-Inhalte direkt an eine Clientplayeranwendung bereitstellen kann und dabei eines der allgemeinen Streamingmedienprotokolle (HLS oder DASH) verwendet. Zudem sorgt der **Streamingendpunkt** für eine dynamische (Just-In-Time-)Verschlüsselung zu branchenführenden DRMs.

In der Medienstreamingbranche wird dieser Dienst häufig als **Paketerstellungs-Manager** oder **Ursprung** bezeichnet.  JITP (Just-in-time-Packager, Just-In-Time-Paketerstellungs-Manager) oder JITE (Just-in-time-Encryption, Just-In-Time-Verschlüsselung) sind weitere in der Branche häufig verwendete Begriffe für diese Funktion. 
 
## <a name="cloud-upload-and-storage"></a>Clouduploads und Cloudspeicherung

Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen und Ihre digitalen Dateien in **Medienobjekte** hochladen.

- [Clouduploads und Cloudspeicherung](storage-account-concept.md)
- [Medienobjektkonzept](assets-concept.md)

## <a name="encoding"></a>Codieren

Sobald Sie Ihre hochwertigen digitalen Mediendateien in Medienobjekten hochgeladen haben, können Sie sie in Formaten codieren, die mit einer Vielzahl von Browsern und Geräten wiedergegeben werden können. 

Um mit Media Services v3 codieren zu können, müssen Sie **Transformationen** und **Aufträge** erstellen.

![Transformationen](./media/encoding/transforms-jobs.png)

- [Transformationen und Aufträge](transforms-jobs-concept.md)
- [Codierung mit Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Media Analytics

Um Ihre Video- und Audiodateien zu analysieren, müssen Sie auch **Transformationen** und **Aufträge** erstellen.

- [Analysieren von Video- und Audiodateien](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Verpackung, Bereitstellung, Schutz

Sobald Ihre Inhalte codiert sind, können Sie die Vorteile der **dynamischen Paketerstellung** nutzen. Bei Media Services ist ein **Streamingendpunkt**/Ursprung der dynamische Paketerstellungsdienst, mit dem Medieninhalte für Clientplayer bereitgestellt werden. Um Videos im Ausgabeobjekt für die Wiedergabe durch Clients verfügbar zu machen, müssen Sie einen **Streaminglocator** erstellen und dann die Streaming-URLs erstellen. 

Beim Erstellen des **Streaminglocators** müssen Sie zusätzlich zum Namen des Objekts eine **Streamingrichtlinie** angeben. Mithilfe von **Streamingrichtlinien** können Sie Streamingprotokolle und Verschlüsselungsoptionen (falls zutreffend) für Ihre **Streaminglocators** definieren.

Die dynamische Paketerstellung wird unabhängig davon verwendet, ob Sie für Ihre Inhalte das Live- oder On-Demand-Streaming nutzen. Das nachstehende Diagramm zeigt das On-Demand-Streaming mit dem Workflow zur dynamischen Paketerstellung.

![Dynamische Paketerstellung](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder/und einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients.

Wenn Sie Verschlüsselungsoptionen für Ihren Datenstrom festlegen, erstellen Sie die **Inhaltsschlüsselrichtlinie** und verknüpfen sie mit Ihrem **Streaminglocator**. Mit der **Richtlinie für den Inhaltsschlüssel** können Sie konfigurieren, wie der Inhaltsschlüssel an Endclients verteilt wird.

Die folgende Abbildung veranschaulicht den Media Services-Workflow zum Schutz von Inhalten: 

![Inhalt schützen](./media/content-protection/content-protection.svg)

&#42; Die dynamische Verschlüsselung unterstützt AES-128 mit unverschlüsseltem Schlüssel, CBCS und CENC. 

Sie können **dynamische Manifeste** von Media Services verwenden, um nur eine bestimmte Wiedergabe oder Subclips Ihres Videos zu streamen. Im folgenden Beispiel wurde ein Encoder zum Codieren eines Zwischenassets in sieben ISO-MP4s-Videowiedergaben (von 180p bis 1080p) verwendet. Das codierte Medienobjekt kann dynamisch in eines der folgenden Streamingprotokolle gepackt werden: HLS, MPEG-DASH und Smooth.  Oben in der Abbildung ist das HLS-Manifest für das Medienobjekt ohne Filter dargestellt (es enthält alle sieben Wiedergaben).  Links unten ist das HLS-Manifest abgebildet, auf das der Filter "ott" angewendet wurde. Der Filter „ott“ gibt an, dass alle Bitraten unter 1 MBit/s entfernt werden. Dies führte dazu, dass zwei Qualitätsstufen in der Antwort entfernt wurden. Rechts unten ist das HLS-Manifest abgebildet, auf das der Filter „mobile“ angewandt wurde. Der Filter "mobile" gibt an, dass Wiedergaben mit einer größeren Auflösung als 720p entfernt werden. Dies führte dazu, dass zwei Wiedergaben mit 1080p gelöscht wurden.

![Filtern der Wiedergabe](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamische Paketerstellung](dynamic-packaging-overview.md)
- [Streamingendpunkte](streaming-endpoint-concept.md)
- [Streaminglocators](streaming-locators-concept.md)
- [Streamingrichtlinien](streaming-policy-concept.md)
- [Richtlinien für Inhaltsschlüssel](content-key-policy-concept.md)
- [Inhaltsschutz](content-protection-overview.md)
- [Dynamische Manifeste](filters-dynamic-manifest-overview.md)
- [Filter](filters-concept.md)

## <a name="live-streaming"></a>Livestreaming

Mit Azure Media Services können Sie Ihren Kunden Liveereignisse in der Azure Cloud anbieten. **Liveereignisse** sorgen für das Erfassen und Verarbeiten von Livevideofeeds. Wenn Sie ein **Liveereignis** erstellen, wird ein Eingangsendpunkt erstellt, mit dem Sie ein Livesignal von einem Remoteencoder senden können. Wenn der Stream an das **Liveereignis** übertragen wird, können Sie das Streamingereignis starten, indem Sie ein **Medienobjekt**, eine **Liveausgabe** und einen **Streaminglocator** erstellen. Durch die **Liveausgabe** wird der Datenstrom in das **Medienobjekt** archiviert und über den **Streamingendpunkt** für die Zuschauer verfügbar gemacht. Für ein **Liveereignis** ist einer von zwei Typen möglich: **Pass-Through** und **Livecodierung**.

Die folgende Abbildung veranschaulicht den Workflow vom Typ „Pass-Through“:

![Pass-Through](./media/live-streaming/pass-through.svg)

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Liveereignisse und Liveausgaben](live-events-outputs-concept.md)

## <a name="monitoring"></a>Überwachung

### <a name="event-grid"></a>Event Grid

Zum Anzeigen des Auftragsfortschritts sollten Sie **Event Grid** verwenden. Media Services gibt auch die Live-Ereignistypen aus. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. 

- [Behandeln von Event Grid-Ereignissen](reacting-to-media-services-events.md)
- [Schemas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Überwachen von Metriken und Diagnoseprotokollen, die Ihnen zu verstehen helfen, wie sich Ihre Anwendungen mit Azure Monitor verhalten.

- [Metriken und Diagnoseprotokolle](media-services-metrics-diagnostic-logs.md)
- [Schemas für Diagnoseprotokolle](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Beteiligte Kunden

Mit dem Azure Media Player können Sie von Media Services gestreamte Medieninhalte auf einer Vielzahl von Browsern und Geräten wiedergeben. Der Azure Media Player nutzt Industriestandards wie HTML5, MSE (Media Source Extensions) und EME (Encrypted Media Extensions) für ein funktionsreiches adaptives Streaming. 

- [Übersicht über Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Codieren von Remotedateien und Streamen von Videos: REST](stream-files-tutorial-with-rest.md)
* [Codieren von hochgeladenen Dateien und Streamen von Videos: .NET](stream-files-tutorial-with-api.md)
* [Livestreaming: .NET](stream-live-tutorial-with-api.md)
* [Analysieren Ihres Videos: .NET](analyze-videos-tutorial-with-api.md)
* [Dynamische AES-128-Verschlüsselung: .NET](protect-with-aes128.md)
* [Dynamische Verschlüsselung mit Multi-DRM: .NET](protect-with-drm.md) 
