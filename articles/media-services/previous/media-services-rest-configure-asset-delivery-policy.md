---
title: Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit der Media Services-REST-API | Microsoft Docs
description: In diesem Thema wird die Konfiguration verschiedener Übermittlungsrichtlinien für Medienobjekte mit der Media Services-REST-API erläutert.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60817398"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurieren von Übermittlungsrichtlinien für Medienobjekte
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Wenn Sie dynamisch verschlüsselte Medienobjekte übermitteln möchten, ist einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services das Konfigurieren von Übermittlungsrichtlinien. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung).

In diesem Thema wird erläutert, warum und wie Übermittlungsrichtlinien für Medienobjekte erstellt und konfiguriert werden.

> [!NOTE]
> Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 
>
> Darüber hinaus muss Ihr Medienobjekt einen MP4-Satz bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten, damit die dynamische Paketerstellung und die dynamische Verschlüsselung verwendet werden können.

Sie können verschiedene Richtlinien auf dasselbe Medienobjekt anwenden. Sie könnten z. B. eine PlayReady-Verschlüsselung auf Smooth Streaming und AES-Umschlagverschlüsselung auf MPEG-DASH und HLS anwenden. Alle Protokolle, die nicht in einer Übermittlungsrichtlinie definiert sind (wenn Sie z. B. eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt), werden vom Streaming ausgeschlossen. Die einzige Ausnahme besteht darin, wenn Sie überhaupt keine Übermittlungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

Wenn Sie ein speicherverschlüsseltes Medienobjekt übermitteln möchten, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie gestreamt. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES (Advanced Encryption Standard)-Umschlagverschlüsselung verschlüsseln möchten, legen Sie den Richtlinientyp auf **DynamicEnvelopeEncryption**fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf **NoDynamicEncryption**fest. In den folgenden Beispielen wird die Konfiguration dieser Richtlinientypen veranschaulicht.

Je nachdem, wie Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren, können Sie die folgenden Streamingprotokolle dynamisch packen, dynamisch verschlüsseln und streamen: Smooth Streaming-, HLS-, MPEG DASH-Streams.

Die folgende Liste enthält die Formate, die Sie zum Streamen von Smooth, HLS und DASH verwenden.

Smooth Streaming:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest

HLS:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl)

MPEG DASH

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=mpd-time-csf)


Anweisungen zum Veröffentlichen eines Medienobjekts und Erstellen einer Streaming-URL finden Sie unter [Erstellen einer Streaming-URL](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Überlegungen
* Sie können eine mit einem Asset verknüpfte AssetDeliveryPolicy nicht löschen, solange ein OnDemand-Locator (Streaminglocator) für dieses Asset vorhanden ist. Es wird empfohlen, die Richtlinie aus dem Asset zu entfernen, bevor Sie die Richtlinie löschen.
* Ein Streaminglocator kann nicht auf einem speicherverschlüsselten Asset erstellt werden, wenn keine Übermittlungsrichtlinie für das Asset festgelegt ist.  Wenn das Asset nicht speicherverschlüsselt ist, lässt Sie das System einen Locator erstellen und das Asset in Klartext ohne Übermittlungsrichtlinie streamen.
* Sie haben die Möglichkeit, mehrere Übermittlungsrichtlinien für ein einzelnes Asset zu verwenden, aber Sie dürfen nur eine Möglichkeit zum Verarbeiten eines bestimmten AssetDeliveryProtocol angeben.  Wenn Sie also versuchen, zwei Übermittlungsrichtlinien zu verknüpfen, die das AssetDeliveryProtocol.SmoothStreaming-Protokoll angeben, führt dies zu einem Fehler, da das System nicht weiß, welche Richtlinie angewendet werden soll, wenn ein Client eine Smooth Streaming-Anforderung sendet.
* Wenn Sie über ein Asset mit einem vorhandenen Streaminglocator verfügen, können Sie keine neue Richtlinie mit dem Asset verknüpfen, eine vorhandene Verknüpfung vom Asset aufheben oder eine Übermittlungsrichtlinie aktualisieren, die dem Asset zugeordnet ist.  Sie müssen zuerst den Streaminglocator entfernen, die Richtlinien anpassen und dann den Streaminglocator neu erstellen.  Sie können die gleiche Locator-ID verwenden, wenn Sie den Streaminglocator neu erstellen. Allerdings sollten Sie sicherstellen, dass dadurch keine Probleme für Clients auftreten, da Inhalte nach Ursprung oder einem nachgelagerten CDN zwischengespeichert werden können.

> [!NOTE]
> 
> Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Löschen einer Übermittlungsrichtlinie für Medienobjekte
### <a id="create_asset_delivery_policy"></a>Erstellen einer Übermittlungsrichtlinie für Medienobjekte
Die folgende HTTP-Anforderung erstellt eine Übermittlungsrichtlinie für Medienobjekte. Diese gibt an, dass keine dynamische Verschlüsselung angewendet und der Stream mit einem der folgenden Protokolle übermittelt wird:  MPEG DASH-, HLS- und Smooth Streaming-Protokolle. 

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben   

Anforderung:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Antwort:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>Verknüpfen des Medienobjekts mit einer Übermittlungsrichtlinie für Medienobjekte
Die folgende HTTP-Anforderung verknüpft das angegebene Medienobjekt mit der Übermittlungsrichtlinie für Medienobjekte.

Anforderung:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Antwort:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption-Übermittlungsrichtlinie für Medienobjekte
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Erstellen eines Inhaltsschlüssels des EnvelopeEncryption-Typs und Verknüpfen des Schlüssels mit dem Medienobjekt
Wenn Sie die DynamicEnvelopeEncryption-Übermittlungsrichtlinie angeben, müssen Sie sicherstellen, dass Ihr Medienobjekt mit einem Inhaltsschlüssel des EnvelopeEncryption-Typs verknüpft wird. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-rest-create-contentkey.md).

### <a id="get_delivery_url"></a>Abrufen der Übermittlungs-URL
Rufen Sie die Übermittlungs-URL für die angegebene Übermittlungsmethode des Inhaltsschlüssels auf, den Sie im vorherigen Schritt erstellt haben. Ein Client verwendet die zurückgegebene URL, um einen AES-Schlüssel oder eine PlayReady-Lizenz zur Wiedergabe des geschützten Inhalts anzufordern.

Geben Sie den Typ der abzurufenden URL im Text der HTTP-Anforderung an. Wenn Sie Ihre Inhalte mit PlayReady schützen, fordern Sie eine Media Services PlayReady-Lizenzerwerbs-URL an, indem Sie für keyDeliveryType den Wert 1 angeben: {"keyDeliveryType":1}. Wenn Sie Ihre Inhalte mit der Umschlagverschlüsselung schützen, fordern Sie eine Schlüsselerwerbs-URL an, indem Sie für keyDeliveryType den Wert 2 angeben: {"keyDeliveryType":2}.

Anforderung:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Antwort:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Erstellen einer Übermittlungsrichtlinie für Medienobjekte
Durch die folgende HTTP-Anforderung wird die **AssetDeliveryPolicy** erstellt. Die Richtlinie ist so konfiguriert, dass sie die dynamische Umschlagsverschlüsselung (**DynamicEnvelopeEncryption**) auf das **HLS**-Protokoll anwendet (andere Protokolle in diesem Beispiel sind vom Streaming ausgeschlossen). 

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben   

Anforderung:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Antwort:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Verknüpfen des Medienobjekts mit einer Übermittlungsrichtlinie für Medienobjekte
Weitere Informationen finden Sie unter [Verknüpfen des Medienobjekts mit einer Übermittlungsrichtlinie für Medienobjekte](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption-Übermittlungsrichtlinie für Medienobjekte
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Erstellen eines Inhaltsschlüssels des CommonEncryption-Typs und Verknüpfen des Schlüssels mit dem Medienobjekt
Wenn Sie die DynamicCommonEncryption-Übermittlungsrichtlinie angeben, müssen Sie sicherstellen, dass Ihr Medienobjekt mit einem Inhaltsschlüssel des CommonEncryption-Typs verknüpft wird. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a>Abrufen der Übermittlungs-URL
Rufen Sie die Übermittlungs-URL für die PlayReady-Übermittlungsmethode des Inhaltsschlüssels auf, den Sie im vorherigen Schritt erstellt haben. Ein Client verwendet die zurückgegebene URL, um eine PlayReady-Lizenz zur Wiedergabe des geschützten Inhalts anzufordern. Weitere Informationen finden Sie unter [Abrufen der Übermittlungs-URL](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Erstellen einer Übermittlungsrichtlinie für Medienobjekte
Durch die folgende HTTP-Anforderung wird die **AssetDeliveryPolicy** erstellt. Die Richtlinie ist so konfiguriert, dass sie die dynamische allgemeine Verschlüsselung (**DynamicCommonEncryption**) auf das **Smooth Streaming**-Protokoll anwendet (andere Protokolle in diesem Beispiel sind vom Streaming ausgeschlossen). 

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben   

Anforderung:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Wenn Sie Ihre Inhalte mithilfe von Widevine DRM schützen möchten, aktualisieren Sie die AssetDeliveryConfiguration-Werte so, dass WidevineLicenseAcquisitionUrl (hat den Wert 7) verwendet wird, und geben Sie die URL eines Lizenzübermittlungsdiensts an. Sie können sich von folgenden AMS-Partnern bei der Übermittlung von Widevine-Lizenzen unterstützen lassen: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Beispiel: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Wenn Sie mit Widevine verschlüsseln, können Sie nur über DASH übermitteln. Geben Sie DASH (2) im Übermittlungsprotokoll für Medienobjekte an.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Verknüpfen des Medienobjekts mit einer Übermittlungsrichtlinie für Medienobjekte
Weitere Informationen finden Sie unter [Verknüpfen des Medienobjekts mit einer Übermittlungsrichtlinie für Medienobjekte](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Beim Definieren von AssetDeliveryPolicy verwendete Typen

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Die folgende Enumeration beschreibt Werte, die Sie für das Protokoll zur Übermittlung von Medienobjekten festlegen können.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Die folgende Enumeration beschreibt Werte, die Sie für den Richtlinientyp zur Übermittlung von Medienobjekten festlegen können.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Die folgende Enumeration beschreibt Werte, die Sie verwenden können, um die Übermittlungsmethode des symmetrischen Schlüssels an den Client zu konfigurieren.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Die folgende Enumeration beschreibt Werte, die Sie festlegen können, um die Schlüssel zum Abrufen einer spezifischen Konfiguration für eine Richtlinie zur Übermittlung von Medienobjekten zu konfigurieren.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

