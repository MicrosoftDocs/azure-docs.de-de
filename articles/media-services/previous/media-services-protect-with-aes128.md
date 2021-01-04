---
title: Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts | Microsoft-Dokumentation
description: In diesem Thema werden das dynamische Verschlüsseln mit AES-128 und das Verwenden des Schlüsselübermittlungsdiensts behandelt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a0e2f785bb3c097b7d2918198457a3f7baf6a39c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96023447"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](../latest/index.yml) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Mithilfe von Media Services können Sie HTTP Live Streaming (HLS) und Smooth Streaming, die mit dem AES verschlüsselt sind, mithilfe von 128-Bit-Verschlüsselungsschlüsseln bereitstellen. Media Services stellt außerdem den Schlüsselübermittlungsdienst bereit, der Verschlüsselungsschlüssel an autorisierte Benutzer übermittelt. Wenn ein Medienobjekt von Media Services verschlüsselt werden soll, ordnen Sie dem Medienobjekt einen Verschlüsselungsschlüssel zu und konfigurieren außerdem Autorisierungsrichtlinien für den Schlüssel. Wenn ein Datenstrom von einem Player angefordert wird, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt dynamisch mit AES zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu ermitteln, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

Media Services unterstützt mehrere Möglichkeiten zur Authentifizierung von Benutzern, die Schlüssel anfordern. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen oder Token. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Sicherheitstokendienst (Security Token Service, STS) ausgestellt wurde. Media Services unterstützt Token im Format [Simple Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) und [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). Weitere Informationen finden Sie unter [Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel](media-services-protect-with-aes128.md#configure_key_auth_policy).

Damit dynamische Verschlüsselung genutzt werden kann, müssen Sie über ein Medienobjekt verfügen, das eine Sammlung aus MP4-Dateien mit mehreren Bitraten oder Smooth Streaming-Quelldateien mit mehreren Bitraten enthält. Außerdem müssen Sie die Übermittlungsrichtlinie für das Medienobjekt konfigurieren (weiter unten in diesem Artikel beschrieben). Basierend auf dem angegebenen Format in der Streaming-URL stellt der On-Demand-Streaming-Server dann sicher, dass der Datenstrom im ausgewählten Protokoll übermittelt wird. Aus diesem Grund werden die Dateien nur in einem Speicherformat gespeichert, und Sie zahlen auch nur für ein Format. Media Services reagiert auf die Anforderungen eines Clients jeweils mit der richtigen Antwort.

Dieser Artikel richtet sich an Entwickler, die an Anwendungen arbeiten, die geschützte Medieninhalte übermitteln. In dem Artikel wird gezeigt, wie Sie den Schlüsselübermittlungsdienst mit Autorisierungsrichtlinien konfigurieren, damit nur autorisierte Clients die Verschlüsselungsschlüssel empfangen können. Außerdem wird gezeigt, wie dynamische Verschlüsselung verwendet wird.

Informationen zur Verschlüsselung von Inhalten mit dem Advanced Encryption Standard (AES) für die Übermittlung an Safari unter macOS finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Eine Übersicht über den Schutz von Medieninhalten mit der AES-Verschlüsselung finden Sie in [diesem Video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Workflow für dynamische AES-128-Verschlüsselung und Schlüsselübermittlungsdienst

Führen Sie die folgenden allgemeinen Schritte beim Verschlüsseln Ihrer Medienobjekte mit AES unter Verwendung des Media Services-Schlüsselübermittlungsdiensts sowie der dynamischen Verschlüsselung aus:

1. [Erstellen eines Medienobjekts und Hochladen von Dateien in das Medienobjekt](media-services-protect-with-aes128.md#create_asset)

2. [Codieren eines Medienobjekts, das die Sammlung von MP4-Dateien mit adaptiver Bitrate enthält](media-services-protect-with-aes128.md#encode_asset)

3. [Erstellen eines Inhaltsschlüssels und Zuordnen des Schlüssels zum codierten Medienobjekt](media-services-protect-with-aes128.md#create_contentkey) In Media Services enthält der Inhaltsschlüssel den Verschlüsselungsschlüssel des Medienobjekts.

4. [Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel](media-services-protect-with-aes128.md#configure_key_auth_policy) Sie müssen die Autorisierungsrichtlinie für den Inhaltsschlüssel konfigurieren. Der Client muss die Richtlinie erfüllen, bevor der Inhaltsschlüssel an den Client übermittelt wird.

5. [Konfigurieren der Übermittlungsrichtlinie für ein Medienobjekt](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Die Konfiguration der Auslieferungsrichtlinie umfasst die URL für den Schlüsselerwerb und einen Initialisierungsvektor (IV). (AES-128 erfordert denselben IV für die Ver- und Entschlüsselung.) Die Konfiguration umfasst Folgendes: das Bereitstellungsprotokoll (z. B. MPEG DASH, HLS, Smooth Streaming oder alle), die Art der dynamischen Verschlüsselung (z. B. Envelope oder keine dynamische Verschlüsselung).

    Sie können für jedes Protokoll für das gleiche Medienobjekt jeweils eine andere Richtlinie anwenden. Beispielsweise können Sie die PlayReady-Verschlüsselung auf Smooth/DASH und einen AES Envelope auf HLS anwenden. Alle Protokolle, die nicht in einer Bereitstellungsrichtlinie definiert sind, werden für das Streaming gesperrt. (Ein Beispiel ist, wenn Sie eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt.) Die einzige Ausnahme ist, wenn Sie überhaupt keine Bereitstellungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

6. [Erstellen eines OnDemand-Locators](media-services-protect-with-aes128.md#create_locator), um eine Streaming-URL zu erhalten.

Außerdem wird in diesem Artikel gezeigt, [wie der Client einen Schlüssel vom Schlüsselbereitstellungsdienst anfordern kann](media-services-protect-with-aes128.md#client_request).

Ein vollständiges [.NET-Beispiel](media-services-protect-with-aes128.md#example) finden Sie am Ende des Artikels.

In der folgenden Abbildung ist der oben beschriebene Workflow dargestellt. Hier wird das Token zur Authentifizierung verwendet.

![Schützen mit AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Im weiteren Verlauf dieses Artikels finden Sie Beschreibungen, Codebeispiele und Links zu Themen, die Informationen dazu enthalten, wie die oben beschriebenen Aufgaben ausgeführt werden.

## <a name="current-limitations"></a>Aktuelle Einschränkungen
Wenn Sie die Übermittlungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie alle vorhandenen Locators löschen und einen neuen Locator erstellen.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Erstellen eines Medienobjekts und Hochladen von Dateien in das Medienobjekt
Damit Sie Ihre Videos verwalten, codieren und streamen können, müssen Sie Ihre Inhalte zuerst in Media Services hochladen. Nachdem dies geschehen ist, sind Ihre Inhalte sicher in der Cloud zur weiteren Verarbeitung und für das weitere Streaming gespeichert. 

Weitere Informationen finden Sie unter [Hochladen von Dateien in ein Media Services-Konto](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Codieren des Medienobjekts, das die Sammlung von MP4-Dateien mit adaptiver Bitrate enthält
Bei der dynamischen Verschlüsselung erstellen Sie ein Medienobjekt, das eine Sammlung aus MP4-Dateien mit mehreren Bitraten oder Smooth Streaming-Quelldateien mit mehreren Bitraten enthält. Anschließend wird durch den bedarfsgesteuerten Streamingserver auf Basis des in der Manifest- oder Fragmentanforderung angegebenen Formats sichergestellt, dass Sie den Datenstrom im ausgewählten Protokoll erhalten. So werden die Dateien nur in einem Speicherformat gespeichert, und Sie zahlen auch nur für ein Format. Media Services reagiert auf die Anforderungen eines Clients jeweils mit der richtigen Antwort. Weitere Informationen finden Sie in der Übersicht unter [Dynamische Paketerstellung](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Beim Erstellen Ihres Media Services-Kontos wird dem Konto ein Standard-Streamingendpunkt mit dem Status „Beendet“ hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status „Wird ausgeführt“ aufweisen. 
>
>Darüber hinaus muss Ihr Medienobjekt einen MP4-Satz bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten, damit die dynamische Paketerstellung und die dynamische Verschlüsselung verwendet werden können.

Informationen zum Codieren finden Sie unter [Codieren eines Medienobjekts mit Media Encoder Standard mit .NET](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Erstellen eines Inhaltsschlüssels und Zuordnen des Schlüssels zum codierten Medienobjekt
In Media Services enthält der Inhaltsschlüssel den Schlüssel, mit dem ein Medienobjekt verschlüsselt werden soll.

Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel
Media Services unterstützt mehrere Möglichkeiten zur Authentifizierung von Benutzern, die Schlüssel anfordern. Sie müssen die Autorisierungsrichtlinie für den Inhaltsschlüssel konfigurieren. Der Client (Player) muss die Richtlinie erfüllen, bevor der Schlüssel an den Client übermittelt werden kann. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen, Tokeneinschränkung oder IP-Einschränkung.

Weitere Informationen finden Sie unter [Konfigurieren von Autorisierungsrichtlinien für Inhaltsschlüssel](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurieren einer Bereitstellungsrichtlinie für Medienobjekte
Konfigurieren Sie die Übermittlungsrichtlinie für Medienobjekte. Die Konfiguration der Bereitstellungsrichtlinie für Medienobjekte umfasst Folgendes:

* URL für den Schlüsselerwerb. 
* Initialisierungsvektor (IV) für die Umschlagverschlüsselung. AES-128 erfordert denselben IV für die Ver- und Entschlüsselung. 
* Das Übermittlungsprotokoll für Medienobjekte (z. B. „MPEG-DASH“, „HLS“, „Smooth Streaming“ oder „Alle“).
* Typ der dynamischen Verschlüsselung (z. B. AES-Umschlag) oder keine dynamische Verschlüsselung. 

Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Erstellen eines OnDemand-Streaminglocators, um eine Streaming-URL abzurufen
Sie müssen die Streaming-URL für Smooth Streaming, DASH oder HLS für Ihre Benutzer bereitstellen.

> [!NOTE]
> Wenn Sie die Übermittlungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie alle vorhandenen Locators löschen und einen neuen Locator erstellen.
> 
> 

Anweisungen zum Veröffentlichen eines Medienobjekts und Erstellen einer Streaming-URL finden Sie unter [Erstellen einer Streaming-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Abrufen eines Testtokens
Rufen Sie ein Testtoken ab, das auf der Tokeneinschränkung basiert, die für die Schlüsselautorisierungsrichtlinie verwendet wurde.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Sie können den [Azure Media Services Player](https://aka.ms/azuremediaplayer) verwenden, um Ihren Datenstrom zu testen.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Wie kann der Client einen Schlüssel vom Schlüsselübermittlungsdienst anfordern?
Im vorherigen Schritt haben Sie die URL erstellt, die auf eine Manifestdatei verweist. Der Client muss die notwendigen Informationen aus den Streamingmanifestdateien extrahieren, um die Anforderung an den Schlüsselübermittlungsdienst zu stellen.

### <a name="manifest-files"></a>Manifestdateien
Der Client muss den URL-Wert aus der Manifestdatei extrahieren. Der URL-Wert enthält außerdem die Inhaltsschlüssel-ID („kid“). Dann versucht der Client, den Verschlüsselungsschlüssel vom Schlüsselübermittlungsdienst abzurufen. Der Client muss auch den IV-Wert extrahieren und damit den Datenstrom entschlüsseln. Der folgende Codeausschnitt zeigt das `<Protection>`-Element des Smooth Streaming-Manifests:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

Im Fall von HLS wird das Stammmanifest in Segmentdateien aufgeteilt. 

Beispiel für das Stammmanifest: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Es enthält eine Liste der Segmentdateinamen.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Wenn Sie eine der Segmentdateien in einem Text-Editor öffnen (z. B. „http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)“), enthält sie „#EXT-X-KEY“, was darauf hinweist, dass die Datei verschlüsselt ist.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Wenn Sie beabsichtigen, HLS mit AES-Verschlüsselung in Safari abzuspielen, lesen Sie [diesen Blogbeitrag](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Anfordern des Schlüssels vom Schlüsselübermittlungsdienst

Im folgenden Code wird gezeigt, wie Sie eine Anforderung an den Media Services-Schlüsselübermittlungsdienst senden, indem Sie einen aus dem Manifest extrahierten Schlüsselübermittlungs-URI und ein Token senden. (Das Abrufen einfacher Webtoken aus einem sicheren Tokendienst wird in diesem Thema nicht behandelt.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Schützen Ihrer Inhalte mit AES-128 mit .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben.

2. Fügen Sie den „appSettings“ gemäß der Definition in Ihrer Datei „app.config“ die folgenden Elemente hinzu:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Beispiel

Überschreiben Sie den Code in Ihrer Datei "Program.cs" mit dem in diesem Abschnitt gezeigten Code.
 
>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene Media Services-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Verwenden Sie dieselbe Richtlinien-ID, falls Sie immer die gleichen Tage und Zugriffsberechtigungen verwenden. Ein Beispiel hierfür ist die Verwendung von Richtlinien für Locators, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload). Weitere Informationen finden Sie in „Einschränken von Zugriffsrichtlinien“ unter [Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Stellen Sie sicher, dass die Variablen so aktualisiert werden, dass sie auf die Ordner zeigen, in denen sich Ihre Eingabedateien befinden.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
