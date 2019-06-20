---
title: Veröffentlichen von Azure Media Services-Inhalten mit REST
description: Erfahren Sie, wie Sie einen Locator erstellen, der zum Generieren einer Streaming-URL verwendet wird. Der Code verwendet die REST-API.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 974f0af461ecdc7de820191950b010035d02a601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598306"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Veröffentlichen von Azure Media Services-Inhalten mit REST 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Sie können einen MP4-Satz mit adaptiver Bitrate streamen, indem Sie einen OnDemand-Streaminglocator und eine Streaming-URL erstellen. Im Artikel [Codieren eines Medienobjekts](media-services-rest-encode-asset.md) wird die Codierung in einen MP4-Satz mit adaptiver Bitrate erläutert. Wenn Ihr Inhalt verschlüsselt ist, konfigurieren Sie vor dem Erstellen eines Locators die Bereitstellungsrichtlinie für Medienobjekte (wie in [diesem Artikel](media-services-rest-configure-asset-delivery-policy.md) beschrieben). 

Sie können auch einen OnDemand-Streaminglocator zum Erstellen von URLs verwenden, die auf MP4-Dateien verweisen, die progressiv heruntergeladen werden können.  

In diesem Artikel wird erläutert, wie Sie einen OnDemand-Streaminglocator erstellen, um Ihr Medienobjekt zu veröffentlichen und Smooth-, MPEG DASH- sowie HLS-Streaming-URLs zu erstellen. Außerdem wird veranschaulicht, wie Sie URLs für progressive Downloads erstellen.

Im [folgenden](#types) Abschnitt finden Sie die Enumerationstypen, deren Werte in REST-Aufrufen verwendet werden.   

> [!NOTE]
> Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Nachfolgende Aufrufe müssen an den neuen URI gesendet werden.

## <a name="create-an-ondemand-streaming-locator"></a>Erstellen eines OnDemand-Streaminglocators
Verfahren Sie zum Erstellen des OnDemand-Streaminglocators und Abrufen von URLs wie folgt:

1. Wenn der Inhalt verschlüsselt ist, definieren Sie eine Zugriffsrichtlinie.
2. Erstellen Sie einen OnDemand-Streaminglocator.
3. Wenn Sie ein Streaming planen, rufen Sie die Streaming-Manifestdatei (ISM) im Medienobjekt ab. 
   
   Wenn Sie einen progressiven Download ausführen möchten, rufen Sie die Namen der MP4-Dateien im Medienobjekt ab. 
4. Erstellen Sie URLs für die Manifestdatei oder MP4-Dateien. 
5. Sie können keinen Streaminglocator mit einer Zugriffsrichtlinie erstellen, die Schreib- oder Leseberechtigungen umfasst.

### <a name="create-an-access-policy"></a>Erstellen einer Zugriffsrichtlinie

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z. B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), verwenden Sie dieselbe Richtlinien-ID. Weitere Informationen dazu finden Sie in [diesem Artikel](media-services-dotnet-manage-entities.md#limit-access-policies).

Anforderung:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Antwort:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Erstellen eines OnDemand-Streaminglocators
Erstellen Sie den Locator für das angegebene Medienobjekt und die zugehörige Richtlinie.

Anforderung:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Antwort:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Erstellen von Streaming-URLs
Verwenden Sie den nach der Locator-Erstellung zurückgegebenen **Path** -Wert, um die Smooth-, HLS- und MPEG DASH-URLs zu erstellen. 

Smooth Streaming: **Pfad** + Manifestdateiname + „/manifest“

Beispiel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Pfad** + Manifestdateiname + „/manifest(format=m3u8-aapl)“

Beispiel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Pfad** + Manifestdateiname + „/manifest(format=mpd-time-csf)“

Beispiel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Erstellen von URLs für progressive Downloads
Verwenden Sie den nach der Locator-Erstellung zurückgegebenen **Path** -Wert, um die URL für progressive Downloads zu generieren.   

URL: **Pfad** + Name der MP4-Medienobjektdatei

Beispiel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Enumerationstypen
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
[Übersicht über die Media Services Operations-REST-API](media-services-rest-how-to-use.md)

[Konfigurieren der Übermittlungsrichtlinie für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md)

