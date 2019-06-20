---
title: Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK | Microsoft-Dokumentation
description: In diesem Thema wird die Konfiguration verschiedener Übermittlungsrichtlinien für Medienobjekte mit Azure Media Services .NET SDK erläutert.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465608"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Übersicht
Wenn Sie verschlüsselte Medienobjekte übermitteln möchten, ist einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services das Konfigurieren von Übermittlungsrichtlinien. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung).

In diesem Artikel wird erläutert, warum und wie Übermittlungsrichtlinien für Medienobjekte erstellt und konfiguriert werden.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Zustand **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 
>
>Darüber hinaus muss Ihr Medienobjekt einen MP4-Satz bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten, damit die dynamische Paketerstellung und die dynamische Verschlüsselung verwendet werden können.

Sie können verschiedene Richtlinien auf dasselbe Medienobjekt anwenden. Sie könnten z. B. eine PlayReady-Verschlüsselung auf Smooth Streaming und AES-Umschlagverschlüsselung auf MPEG-DASH und HLS anwenden. Alle Protokolle, die nicht in einer Übermittlungsrichtlinie definiert sind (wenn Sie z. B. eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt), werden vom Streaming ausgeschlossen. Die einzige Ausnahme ist, wenn Sie überhaupt keine Bereitstellungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

Wenn Sie ein speicherverschlüsseltes Medienobjekt übermitteln möchten, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie gestreamt. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES (Advanced Encryption Standard)-Umschlagverschlüsselung verschlüsseln möchten, legen Sie den Richtlinientyp auf **DynamicEnvelopeEncryption**fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf **NoDynamicEncryption**fest. In den folgenden Beispielen wird die Konfiguration dieser Richtlinientypen veranschaulicht.

Je nachdem, wie Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren, können Sie die folgenden Streamingprotokolle dynamisch packen, verschlüsseln und streamen: Smooth Streaming, HLS und MPEG DASH.

Die folgende Liste enthält die Formate, die Sie zum Streamen von Smooth, HLS und DASH verwenden.

Smooth Streaming:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest

HLS:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl)

MPEG DASH

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=mpd-time-csf)

## <a name="considerations"></a>Überlegungen
* Vor dem Löschen von „AssetDeliveryPolicy“ müssen Sie alle Streaminglocators löschen, die dem Asset zugeordnet sind. Später können Sie bei Bedarf neue Streaminglocators mit einem neuen AssetDeliveryPolicy-Element erstellen.
* Ein Streaminglocator kann nicht auf einem speicherverschlüsselten Asset erstellt werden, wenn keine Übermittlungsrichtlinie für das Asset festgelegt ist.  Wenn das Asset nicht speicherverschlüsselt ist, lässt Sie das System einen Locator erstellen und das Asset in Klartext ohne Übermittlungsrichtlinie streamen.
* Sie haben die Möglichkeit, mehrere Übermittlungsrichtlinien für ein einzelnes Asset zu verwenden, aber Sie dürfen nur eine Möglichkeit zum Verarbeiten eines bestimmten AssetDeliveryProtocol angeben.  Wenn Sie also versuchen, zwei Übermittlungsrichtlinien zu verknüpfen, die das AssetDeliveryProtocol.SmoothStreaming-Protokoll angeben, führt dies zu einem Fehler, da das System nicht weiß, welche Richtlinie angewendet werden soll, wenn ein Client eine Smooth Streaming-Anforderung sendet.
* Wenn Sie über ein Asset mit einem vorhandenen Streaminglocator verfügen, können Sie keine neue Richtlinie mit dem Asset verknüpfen (Sie können entweder eine vorhandene Richtlinienverknüpfung des Assets aufheben oder eine Übermittlungsrichtlinie aktualisieren, die dem Asset zugeordnet ist).  Sie müssen zuerst den Streaminglocator entfernen, die Richtlinien anpassen und dann den Streaminglocator neu erstellen.  Sie können die gleiche Locator-ID verwenden, wenn Sie den Streaminglocator neu erstellen. Allerdings sollten Sie sicherstellen, dass dadurch keine Probleme für Clients auftreten, da Inhalte nach Ursprung oder einem nachgelagerten CDN zwischengespeichert werden können.

## <a name="clear-asset-delivery-policy"></a>Löschen einer Übermittlungsrichtlinie für Medienobjekte

Mit der folgenden **ConfigureClearAssetDeliveryPolicy**-Methode wird angegeben, keine dynamische Verschlüsselung anzuwenden und den Stream unter Verwendung eines der folgenden Protokolle zu übermitteln:  MPEG DASH-, HLS- und Smooth Streaming-Protokolle. Möglicherweise möchten diese Richtlinie auf Ihre im Speicher verschlüsselten Medienobjekte anwenden.

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption-Übermittlungsrichtlinie für Medienobjekte

Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Diese Richtlinie ist für die Anwendung der dynamischen allgemeinen Verschlüsselung (**DynamicCommonEncryption**) auf ein Smooth Streaming-Protokoll konfiguriert (andere Protokolle werden vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **CommonEncryption**-Typs). Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md#common_contentkey).

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services ermöglicht es Ihnen ebenfalls, Widevine-Verschlüsselung hinzuzufügen. Im folgenden Beispiel wird gezeigt, wie sowohl PlayReady als auch Widevine zur Übermittlungsrichtlinie für Medienobjekte (AssetDeliveryPolicy) hinzugefügt wird.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Wenn Sie mit Widevine verschlüsseln, können Sie nur über DASH übermitteln. Geben Sie DASH im Übermittlungsprotokoll für Medienobjekte an.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption-Übermittlungsrichtlinie für Medienobjekte
Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Diese Richtlinie ist für die Anwendung der dynamischen Umschlagverschlüsselung (**DynamicEnvelopeEncryption**) auf die Protokolle Smooth Streaming, HLS und DASH konfiguriert (wenn Sie einige Protokolle nicht angeben, werden sie vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **EnvelopeEncryption**-Typs). Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md#envelope_contentkey).

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>Beim Definieren von AssetDeliveryPolicy verwendete Typen

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Die folgende Enumeration beschreibt Werte, die Sie für das Protokoll zur Übermittlung von Medienobjekten festlegen können.

```csharp
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
```
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

Die folgende Enumeration beschreibt Werte, die Sie für den Richtlinientyp zur Übermittlung von Medienobjekten festlegen können.  
```csharp
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
```
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

Die folgende Enumeration beschreibt Werte, die Sie verwenden können, um die Übermittlungsmethode des symmetrischen Schlüssels an den Client zu konfigurieren.
  ```csharp  
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
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Die folgende Enumeration beschreibt Werte, die Sie festlegen können, um die Schlüssel zum Abrufen einer spezifischen Konfiguration für eine Richtlinie zur Übermittlung von Medienobjekten zu konfigurieren.
```csharp
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
```
## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

