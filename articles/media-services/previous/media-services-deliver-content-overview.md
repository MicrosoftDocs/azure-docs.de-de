---
title: Übermitteln von Inhalten an Kunden | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht darüber, was zur Bereitstellung von Inhalten mit Azure Media Services erforderlich ist.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5db2cb983c0c3cd0e2194f7686964d9ec3828d6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61232270"
---
# <a name="deliver-content-to-customers"></a>Übermitteln von Inhalten an Kunden
Bei der Übermittlung Ihrer Streaming- oder Video-on-Demand-Inhalte an Kunden möchten Sie qualitativ hochwertige Videos unter verschiedenen Netzwerkbedingungen an unterschiedliche Geräte senden.

So erreichen Sie dieses Ziel:

* Codieren Sie den Datenstrom in einen Videodatenstrom mit mehreren Bitraten (adaptive Bitrate). So berücksichtigen Sie verschiedene Qualitäts- und Netzwerkbedingungen.
* Verpacken Sie den Datenstrom mit der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md) von Microsoft Azure Media Services neu in verschiedene Protokolle. So berücksichtigen Sie das Streaming auf verschiedene Geräte. Media Services unterstützt die folgenden Technologien mit Adaptive Bitrate Streaming: <br/>
    * **HTTP Live Streaming** (HLS): Fügen Sie den Pfad „(format=m3u8-aapl)“ dem Abschnitt „/Manifest“ der URL hinzu, um den Ursprungsserver des Streamings anzuweisen, HLS-Inhalte für die Nutzung auf nativen **Apple iOS**-Geräten zurückzugeben (Details finden Sie unter [Locators](#locators) und [URLs](#URLs)).
    * **MPEG-DASH**: Fügen Sie den Pfad „(format=mpd-time-csf)“ dem Abschnitt „/Manifest“ der URL hinzu, um den Ursprungsserver des Streamings anzuweisen, MPEG-DASH-Inhalte zurückzugeben (Details finden Sie unter [Locators](#locators) und [URLs](#URLs)).
    * **Smooth Streaming**.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 

Dieser Artikel bietet eine Übersicht der wichtigen Konzepte zur Inhaltsübermittlung.

Bekannte Probleme werden unter [Bekannte Probleme](media-services-deliver-content-overview.md#known-issues)behandelt.

## <a name="dynamic-packaging"></a>Dynamische Paketerstellung
Mit der dynamischen Paketerstellung von Media Services können Sie Ihre mit adaptiver Bitrate MP4- oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG-DASH, HLS, Smooth Streaming), übermitteln, ohne dass Sie diese Streamingformate erneut packen müssen. Wir empfehlen Ihnen, Ihre Inhalte mit dynamischer Paketerstellung zu übermitteln.

Um die dynamische Paketerstellung nutzen zu können, müssen Sie Ihre Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate codieren.

Mit der dynamischen Paketerstellung speichern Sie die Dateien in einem einzigen Speicherformat und zahlen auch nur für dieses eine Format. Media Services reagiert gemäß Ihren Anforderungen.

Dynamische Paketerstellung steht für Standard- und Premium-Streamingendpunkte zur Verfügung. 

Weitere Informationen finden Sie unter [Dynamische Paketerstellung](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filter und dynamische Manifeste
Mit Media Services können Sie Filter für Ihre Medienobjekte definieren. Diese Filter sind serverseitige Regeln, mit denen Ihre Kunden verschiedene Aktionen ausführen können, z.B. Wiedergabe bestimmter Videoabschnitte. Sie können zudem eine Teilmenge von Audio- und Videowiedergaben (anstelle von allen mit dem Medienobjekt verknüpften Wiedergaben) angeben, die für das Gerät eines Kunden geeignet sind. Diese Filterung erfolgt über *dynamische Manifeste*, die auf Anfrage des Kunden zum Streamen von Videos basierend auf einem oder mehreren angegebenen Filtern erstellt werden.

Weitere Informationen finden Sie unter [Filter und dynamische Manifeste](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Locators
Um Ihren Benutzern eine URL für das Streaming bzw. Herunterladen des Inhalts angeben zu können, müssen Sie zunächst das Medienobjekt veröffentlichen, indem Sie einen Locator erstellen. Ein Locator bieten einen Einstiegspunkt für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien. Media Services unterstützt zwei Arten von Locatorobjekten:

* OnDemandOrigin-Locator. Diese werden zum Streamen von Medien (z.B. MPEG-DASH, HLS oder Smooth Streaming) und zum progressiven Download von Dateien verwendet.
* Shared Access Signature-Locators (SAS). Diese werden zum Herunterladen von Mediendateien auf den lokalen Computer verwendet werden.

Anhand einer *Zugriffsrichtlinie* werden die Berechtigungen eines Clients (z.B. Lesen, Schreiben und Auflisten) und die Dauer definiert, für die der Client auf eine bestimmte Ressource zugreifen kann. Beachten Sie, dass die Berechtigung zum Auflisten (AccessPermissions.List) beim Erstellen eines OnDemandOrigin-Locators nicht verwendet werden sollte.

Locator verfügen über ein Ablaufdatum. Im Azure-Portal wird ein Ablaufdatum von 100 Jahren in der Zukunft für Locator festgelegt.

> [!NOTE]
> Für Locator, die Sie vor März 2015 im Azure-Portal erstellt haben, beträgt die Ablauffrist zwei Jahre.
> 
> 

Verwenden Sie zum Aktualisieren eines Ablaufdatums für einen Locator die [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator)- oder [.NET](https://go.microsoft.com/fwlink/?LinkID=533259)-APIs. Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL.

Locator sind nicht für die Verwaltung der Zugriffssteuerung pro Benutzer konzipiert. Mit den Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM) können Sie einzelnen Benutzern verschiedene Zugriffsrechte erteilen. Weitere Informationen finden Sie unter [Sichern von Medien](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Beim Erstellen eines Locators tritt möglicherweise eine Verzögerung von 30 Sekunden auf, die durch die erforderlichen Speicher- und Weitergabeprozesse in Azure Storage verursacht wird.

## <a name="adaptive-streaming"></a>Adaptives Streaming
Bei adaptiven Bitratentechnologien können Videoplayeranwendungen die Netzwerkbedingungen ermitteln und eine Auswahl aus mehreren Bitraten treffen. Wenn die Netzwerkleistung absinkt, kann der Client eine niedrigere Bitrate auswählen, sodass die Wiedergabe des Videos mit einer geringeren Videoqualität fortgesetzt werden kann. Verbessert sich die Netzwerkleistung, kann der Client auf eine höhere Bitrate umschalten und eine verbesserte Videoqualität anbieten. Azure Media Services unterstützt die folgenden Technologien mit adaptiver Bitrate: HTTP Live Streaming (HLS), Smooth Streaming und MPEG-DASH.

Um Benutzern Streaming-URLs bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt mit den Inhalten, die Sie streamen möchten. Um diese Inhalte streamen zu können, müssen Sie diesen Pfad jedoch ändern. Zum Erstellen einer vollständigen URL für die Streaming-Manifestdatei müssen Sie den Pfadwert des Locators mit dem Dateinamen des Manifests (dateiname.ism) verketten. Fügen Sie dem Locatorpfad anschließend **/Manifest** und (ggf.) ein geeignetes Format hinzu.

> [!NOTE]
> Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen. Beachten Sie, dass SSL mit benutzerdefinierten Domänen derzeit nicht von AMS unterstützt wird.  
> 

Sie können nur über SSL streamen, wenn der Streamingendpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streamingendpunkten basieren, die nach dem 10. September 2014 erstellt wurden, enthält die URL „streaming.mediaservices.windows.net“. Streaming-URLs, die "origin.mediaservices.windows.net" (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, erstellen Sie einen neuen Streamingendpunkt. Verwenden Sie die URLs, die auf dem neuen Streamingendpunkt basieren, um Ihre Inhalte über SSL zu streamen.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Streaming-URL-Formate

### <a name="mpeg-dash-format"></a>MPEG-DASH-Format
{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-Format
{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-Format
{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming-Format (HLS) mit „audio-only“-Filter
Standardmäßig sind reine Tonspuren im HLS-Manifest enthalten. Dies ist für die Apple Store-Zertifizierung für Mobilfunknetze erforderlich. Wenn ein Client in diesem Fall nicht über genügend Bandbreite verfügt oder über eine 2G-Verbindung verbunden ist, wird zur reinen Audiowiedergabe gewechselt. Auf diese Weise kann das Streamen von Inhalten ohne Pufferung fortgesetzt werden, jedoch wird kein Bild angezeigt. In einigen Szenarios könnte es vorteilhafter sein, wenn der Player puffert, anstatt nur die Tonspur abzuspielen. Wenn Sie die reine Tonspur entfernen möchten, fügen Sie der URL **audio-only= false** hinzu.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Weitere Informationen finden Sie unter [Azure Media Services – Dynamic Manifest Composition support and HLS output additional features](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)(Azure Media Services – Unterstützung des Erstellens des dynamischen Manifests und zusätzliche HLS-Ausgabefunktionen).

### <a name="smooth-streaming-format"></a>Smooth Streaming-Format
{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest

Beispiel:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0-Manifest (Legacymanifest)
Das Smooth Streaming-Manifestformat enthält standardmäßig das Wiederholungstag (r-Tag). Einige Player unterstützen das r-Tag jedoch nicht. Clients mit diesen Playern können ein Format verwenden, das das r-Tag deaktiviert:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progressiver Download
Mit progressivem Download können Sie die Medienwiedergabe starten, bevor die gesamte Datei heruntergeladen ist. Dateien mit der Endung ISM* (ISMV, ISMA, ISMT, ISMC) können Sie nicht progressiv herunterladen.

Verwenden Sie den "OnDemandOrigin"-Typ des Locator zum progressiven Herunterladen von Inhalten. Das folgende Beispiel zeigt die URL, die auf dem OnDemandOrigin-Typ des Locators basiert:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Sie müssen alle speicherverschlüsselten Medienobjekte entschlüsseln, die Sie aus dem ursprünglichen Dienst für den progressiven Download streamen möchten.

## <a name="download"></a>Download
Um Ihre Inhalte auf ein Clientgerät herunterzuladen, müssen Sie einen SAS-Locator erstellen. Der SAS-Locator ermöglicht den Zugriff auf den Azure Storage-Container, in dem sich die Datei befindet. Zum Erstellen der Download-URL müssen Sie den Dateinamen zwischen dem Host und der SAS-Signatur einbetten.

Das folgende Beispiel zeigt die URL, die auf dem SAS-Locator basiert:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Es gelten die folgenden Bedingungen:

* Sie müssen alle speicherverschlüsselten Medienobjekte entschlüsseln, die Sie aus dem ursprünglichen Dienst für den progressiven Download streamen möchten.
* Ein Download, der nicht innerhalb von 12 Stunden abgeschlossen wird, schlägt fehl.

## <a name="streaming-endpoints"></a>Streamingendpunkte

Ein Streamingendpunkt stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Clientwiedergabeanwendung oder einem Content Delivery Network (CDN) bereitstellen kann. Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Es gibt zwei Arten von Streamingendpunkten: **Standard** und **Premium**. Weitere Informationen finden Sie unter [Übersicht über Streamingendpunkte](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 

## <a name="known-issues"></a>Bekannte Probleme
### <a name="changes-to-smooth-streaming-manifest-version"></a>Änderungen an der Smooth Streaming-Manifestversion
Vor der Dienstversion vom Juli 2016 galt Folgendes: Wenn von Media Encoder Standard, Media Encoder Premium Workflow oder dem älteren Azure Media Encoder erstellte Medienobjekte mithilfe der dynamischen Paketerstellung gestreamt wurden, entsprach das zurückgegebene Smooth Streaming-Manifest der Version 2.0. In Version 2.0 wurden für die Dauer der Fragmente nicht die sog. Wiederholungstags („r“-Tags) verwendet. Beispiel:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Ab der Dienstversion vom Juli 2016 entspricht das generierte Smooth Streaming-Manifest der Version 2.2, wo für die Dauer von Fragmenten Wiederholungstags verwendet werden. Beispiel:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Einige ältere Smooth Streaming-Clients unterstützen möglicherweise nicht die Wiederholungstags, sodass das Manifest nicht geladen werden kann. Um dieses Problem zu beheben, können Sie den älteren Manifestformatparameter **(format=fmp4-v20)** verwenden oder Ihren Client auf die neueste Version aktualisieren, die Wiederholungstags unterstützt. Weitere Informationen finden Sie unter [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwandte Themen
[Aktualisieren von Media Services nach dem Austausch der Speicherschlüssel](media-services-roll-storage-access-keys.md)

