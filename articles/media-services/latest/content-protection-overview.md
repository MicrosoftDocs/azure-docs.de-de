---
title: Schützen Sie Ihren Inhalt mit der dynamischen Verschlüsselung von Media Services v3
titleSuffix: Azure Media Services
description: Erfahren Sie mehr über den Schutz von Inhalten mit dynamischer Verschlüsselung, Streamingprotokollen und Verschlüsselungstypen in Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5d6530cf7b8d8611ff23a3517112cb0aa7442d6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971181"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Schützen Sie Ihren Inhalt mit der dynamischen Verschlüsselung von Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Verwenden Sie Azure Media Services, um Ihre Medien ab dem Zeitpunkt, an dem sie Ihren Computer verlassen, während des gesamten Prozesses der Speicherung, Verarbeitung und Übermittlung sichern. Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. Inhalt, der mit einem unverschlüsselten AES-Schlüssel verschlüsselt und per HTTPS gesendet wird, bleibt verschlüsselt, bis er den Client erreicht.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

In Media Services v3 ist dem Streaminglocator ein Inhaltsschlüssel zugeordnet (siehe [dieses Beispiel](protect-with-aes128.md)). Bei Verwendung des Media Services-Schlüsselübermittlungsdiensts kann der Inhaltsschlüssel durch Azure Media Services generiert werden. Sie sollten den Inhaltsschlüssel selbst generieren, wenn Sie Ihren eigenen Schlüsselbereitstellungsdienst verwenden oder ein Hochverfügbarkeitsszenario, sodass Sie denselben Inhaltsschlüssel in zwei Rechenzentren benötigen.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um den Inhalt dynamisch mit einem unverschlüsselten AES-Schlüssel oder per DRM-Verschlüsselung zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Media Services-Schlüsselübermittlungsdienst oder dem von Ihnen angegebenen Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Richtlinie für Inhaltsschlüssel aus, die Sie für den Schlüssel angegeben haben.

Zum Konfigurieren von Autorisierungs- und Authentifizierungsrichtlinien für Ihre Lizenzen und Schlüssel können Sie die REST-API oder eine Media Services-Clientbibliothek verwenden.

Die folgende Abbildung veranschaulicht den Workflow für Media Services-Inhaltsschutz:

![Workflow für Media Services-Inhaltsschutz](./media/content-protection/content-protection.svg)
  
&#42; *Die dynamische Verschlüsselung unterstützt AES-128 mit unverschlüsseltem Schlüssel, CBCS und CENC. Details finden Sie in der [Unterstützungsmatrix](#streaming-protocols-and-encryption-types).*

In diesem Artikel werden Konzepte und Begriffe erläutert, die Ihnen helfen, den Inhaltsschutz mit Media Services zu verstehen.

## <a name="main-components-of-a-content-protection-system"></a>Hauptkomponenten eines Inhaltsschutzsystems

Für ein erfolgreiches Abschließen Ihres Inhaltsschutzsystems muss Ihnen der Umfang der Maßnahmen genau bekannt sein. Die folgenden Abschnitte geben eine Übersicht über drei Komponenten, die Sie implementieren müssen.

> [!NOTE]
> Es wird dringend empfohlen, dass Sie sich auf jede Komponente in den folgenden Abschnitten konzentrieren und vollständige Tests durchführen, bevor Sie mit der nächsten Komponente fortfahren. Verwenden Sie zum Testen Ihres Inhaltsschutzsystems die in den Abschnitten angegebenen Tools.

### <a name="media-services-code"></a>Media Services-Code
  
Das [DRM-Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) zeigt Ihnen die Implementierung eines Multi-DRM-Systems mit Media Services v3 über .NET. Zudem zeigt es die Verwendung des Media Services-Diensts zur Lizenz-/Schlüsselbereitstellung.
  
Sie können jedes Medienobjekt mit mehreren Verschlüsselungstypen (AES-128, PlayReady, Widevine, FairPlay) verschlüsseln. Informationen zu sinnvollen Kombinationen finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](#streaming-protocols-and-encryption-types).

Das Beispiel veranschaulicht die folgenden Schritte:

1. Erstellen und konfigurieren Sie eine [Richtlinie für Inhaltsschlüssel](content-key-policy-concept.md).

   Sie erstellen eine Richtlinie für Inhaltsschlüssel, um zu konfigurieren, wie der Inhaltsschlüssel (der sicheren Zugriff auf Ihre Medienobjekte bereitstellt) an Endclients übermittelt wird:  

   * Definieren Sie die Autorisierung zur Lizenzbereitstellung. Geben Sie die Logik der Autorisierungsprüfung basierend auf Ansprüchen in JSON Web Token (JWT) an.
   * Konfigurieren Sie die Lizenzen [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md) und/oder [FairPlay](fairplay-license-overview.md). Mit den Vorlagen können Sie die Rechte und Berechtigungen für die einzelnen DRMs konfigurieren.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Erstellen Sie einen [Streaminglocator](streaming-locators-concept.md), der so konfiguriert ist, dass er das verschlüsselte Objekt streamen kann.
  
   Der Streaminglocator muss einer [Streamingrichtlinie](streaming-policy-concept.md) zugeordnet sein. In diesem Beispiel wird `StreamingLocator.StreamingPolicyName` auf die Richtlinie „Predefined_MultiDrmCencStreaming“ festgelegt.

   Die PlayReady- und Widevine-Verschlüsselungen werden angewendet, und der Schlüssel wird basierend auf den konfigurierten DRM-Lizenzen an den Wiedergabeclient übermittelt. Wenn Sie den Stream auch mit CBCS (FairPlay) verschlüsseln möchten, verwenden Sie die Richtlinie „Predefined_MultiDrmStreaming“.

   Der Streaminglocator wird außerdem der von Ihnen definierten Richtlinie für Inhaltsschlüssel zugeordnet.

3. Erstellen Sie einen Testtoken.

   Die `GetTokenAsync`-Methode zeigt, wie Sie ein Testtoken erstellen.
4. Erstellen Sie die Streaming-URL.

   Die `GetDASHStreamingUrlAsync`-Methode zeigt, wie Sie die Streaming-URL erstellen. In diesem Fall streamt die URL den DASH-Inhalt.

### <a name="player-with-an-aes-or-drm-client"></a>Player mit einem AES- oder DRM-Client

Eine Videoplayer-App basierend auf einem Player-SDK (nativ oder browserbasiert) muss die folgenden Anforderungen erfüllen:

* Das Player-SDK unterstützt die erforderlichen DRM-Clients.
* Das Player-SDK unterstützt die erforderlichen Streamingprotokolle: Smooth, DASH und/oder HTTP Live Streaming (HLS).
* Das Player-SDK kann die Übergabe eines JWT-Tokens in einer Lizenzerwerbsanforderung verarbeiten.

Sie können mithilfe der [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) einen Player erstellen. Verwenden Sie die [ProtectionInfo-API von Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/), um anzugeben, welche DRM-Technologie auf unterschiedlichen DRM-Plattformen verwendet werden soll.

Zum Testen von mit AES oder CENC (Widevine und/oder PlayReady) verschlüsselten Inhalten können Sie [Azure Media Player](https://aka.ms/azuremediaplayer) verwenden. Stellen Sie sicher, dass Sie **Erweiterte Optionen** auswählen und Ihre Verschlüsselungsoptionen prüfen.

Wenn Sie mit FairPlay verschlüsselte Inhalte testen möchten, verwenden Sie [diesen Testplayer](https://aka.ms/amtest). Der Player unterstützt Widevine-, PlayReady- und FairPlay-DRMs sowie die AES-128-Verschlüsselung mit unverschlüsselten Schlüsseln.

Wählen Sie den richtigen Browser zum Testen der verschiedenen DRMs aus:

* Chrome, Opera oder Firefox für Widevine.
* Microsoft Edge oder Internet Explorer 11 für PlayReady.
* Safari unter macOS für FairPlay.

### <a name="security-token-service"></a>Sicherheitstokendienst

Ein Sicherheitstokendienst (Security Token Service, STS) gibt JWT als Zugriffstoken für den Back-End-Ressourcenzugriff aus. Sie können den Azure Media Services-Diensts zur Lizenz-/Schlüsselbereitstellung als Back-End-Ressource verwenden. Ein Sicherheitstokendienst muss Folgendes definieren:

* Aussteller und Zielgruppe (oder Gültigkeitsbereich).
* Ansprüche, die von Geschäftsanforderungen für den Inhaltsschutz abhängen.
* Symmetrische oder asymmetrische Überprüfung für die Signaturüberprüfung.
* Unterstützung des Schlüsselrollovers (sofern erforderlich).

Mit [diesem STS-Tool](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) können Sie den Sicherheitstokendienst testen. Es unterstützt alle drei Typen von Überprüfungsschlüsseln: symmetrisch, asymmetrisch oder Azure Active Directory (Azure AD) mit Schlüsselrollover.

## <a name="streaming-protocols-and-encryption-types"></a>Streamingprotokolle und Verschlüsselungstypen

Mit Media Services können Sie Ihre zu übermittelnden Inhalte dynamisch mit unverschlüsselten AES-Schlüsseln oder per DRM-Verschlüsselung über Microsoft PlayReady, Google Widevine oder FairPlay verschlüsseln. Zurzeit können Sie die Formate HLS, MPEG DASH und Smooth Streaming verschlüsseln. Jedes Protokoll unterstützt die folgenden Verschlüsselungsmethoden.

### <a name="hls"></a>HLS

Das HLS-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas:

|Containerformat|Verschlüsselungsschema|URL-Beispiel|
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (einschließlich HEVC/H.265) wird auf folgenden Geräten unterstützt:

* iOS 11 oder höher.
* iPhone 8 oder höher.
* macOS High Sierra mit Intel-CPU der 7. Generation

### <a name="mpeg-dash"></a>MPEG-DASH

Das MPEG-DASH-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas:

|Containerformat|Verschlüsselungsschema|URL-Beispiele
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Das Smooth Streaming-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas.

|Protocol|Containerformat|Verschlüsselungsschema|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1.1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1-Unterstützung wird als abwärtskompatible Lösung für ein Smart TV-Gerät (Samsung, LG) bereitgestellt, das die frühe „Silverlight“-Version von Common Encryption implementiert hat. Sie sollten das PIFF-Format nur dann verwenden, wenn es zur Unterstützung älterer, von 2009 bis 2015 ausgelieferter Samsung- oder LG-Smart TV-Geräte verwendet werden muss, die die PIFF 1.1-Version der PlayReady-Verschlüsselung unterstützten. 

### <a name="browsers"></a>Browser

Folgende DRM-Clients werden von gängigen Browsern unterstützt:

|Browser|Verschlüsselung|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Steuern des Inhaltszugriffs

Sie können steuern, wer Zugriff auf Ihre Inhalte hat, indem Sie die Richtlinie für Inhaltsschlüssel konfigurieren. Media Services unterstützt mehrere Möglichkeiten zur Autorisierung von Benutzern, die Schlüssel anfordern. Der Client (Player) muss die Richtlinie erfüllen, bevor der Schlüssel an den Client übermittelt werden kann. Die Richtlinie für Inhaltsschlüssel kann eine *offene* oder eine *Tokeneinschränkung* enthalten.

Eine Richtlinie für Inhaltsschlüssel mit offener Einschränkung kann verwendet werden, wenn Sie eine Lizenz für Personen ohne Autorisierung ausgeben möchten. Beispielsweise wenn Ihr Umsatz AD-basiert und nicht abonnementbasiert ist.  

Bei einer Richtlinie für Inhaltsschlüssel mit Tokeneinschränkung wird der Inhaltsschlüssel nur an einen Client gesendet, der in der Lizenz-/Schlüsselanforderung ein gültiges JWT-Token oder ein einfaches Webtoken (Simple Web Token, SWT) präsentiert. Dieses Token muss von einem Sicherheitstokendienst (STS) ausgestellt werden.

Sie können Azure AD als STS verwenden oder einen [benutzerdefinierten STS](#using-a-custom-sts) bereitstellen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausstellungsansprüche konfiguriert sein, die Sie in der Konfiguration der Tokeneinschränkung angegeben haben. Der Media Services-Dienst zur Lizenz-/Schlüsselbereitstellung gibt die angeforderte Lizenz bzw. den angeforderten Schlüssel an den Client zurück, wenn die beiden folgenden Bedingungen erfüllt sind:

* Das Token ist gültig.
* Die Ansprüche im Token entsprechen denen, die für die Lizenz oder den Schlüssel konfiguriert sind.

Bei der Konfiguration der Richtlinie mit Tokeneinschränkung müssen die Parameter für den primären Verifizierungsschlüssel (primary verification key), den Aussteller (issuer) und die Zielgruppe (audience) angegeben werden. Der primäre Verifizierungsschlüssel enthält den Schlüssel, mit dem das Token signiert wurde. Der Aussteller ist der Sicherheitstokendienst, der das Token ausstellt. „Audience“ (manchmal auch „Scope“) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Media Services-Dienst zur Lizenz-/Schlüsselbereitstellung überprüft, ob diese Werte im Token mit den Werten in der Vorlage übereinstimmen.

### <a name="token-replay-prevention"></a>Verhindern der Tokenwiedergabe

Mit dem Feature zum *Verhindern der Tokenwiedergabe* können Media Services-Kunden einen Grenzwert festlegen, der bestimmt, wie oft ein Token zum Anfordern eines Schlüssels oder einer Lizenz verwendet werden kann. Der Kunde kann einen Anspruch vom Typ `urn:microsoft:azure:mediaservices:maxuses` im Token hinzufügen, wobei der Wert die Häufigkeit ist, mit der das Token für den Erwerb einer Lizenz oder eines Schlüssels verwendet werden kann. Bei allen nachfolgenden Anforderungen mit demselben Token für die Schlüsselbereitstellung wird eine nicht autorisierte Antwort zurückgegeben. Informationen zum Hinzufügen des Anspruchs finden Sie im [DRM-Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Überlegungen

* Kunden müssen die Kontrolle über die Tokengenerierung haben. Der Anspruch muss in das Token selbst eingefügt werden.
* Bei Verwendung dieser Funktion werden Anforderungen mit Token, deren Ablaufzeit mehr als eine Stunde vor dem Zeitpunkt liegt, zu dem die Anforderung empfangen wird, werden mit der Antwort „nicht autorisiert“ abgelehnt.
* Token werden durch ihre Signatur eindeutig identifiziert. Jede Änderung an der Nutzlast (z. B. eine Aktualisierung der Ablaufzeit oder des Anspruchs) ändert die Signatur des Tokens, und es zählt als ein neues Token, das bei der Schlüsselbereitstellung zuvor noch nicht aufgetreten ist.
* Die Wiedergabe schlägt fehl, wenn das Token den vom Kunden festgelegten `maxuses`-Wert überschritten hat.
* Diese Funktion kann für alle vorhandenen geschützten Inhalte verwendet werden (nur das ausgegebene Token muss geändert werden).
* Diese Funktion kann sowohl mit JWT als auch mit SWT verwendet werden.

## <a name="using-a-custom-sts"></a>Verwenden eines benutzerdefinierten Sicherheitstokendienst

Möglicherweise möchte ein Kunde einen benutzerdefinierten Sicherheitstokendienst zum Bereitstellen von Token verwenden. Hierfür kann es folgende Gründe geben:

* Der vom Kunden verwendete Identitätsanbieter (Identity Provider, IDP) unterstützt den Sicherheitstokendienst nicht. In diesem Fall kann ein benutzerdefinierter Sicherheitstokendienst eine mögliche Option sein.
* Der Kunde benötigt möglicherweise eine flexiblere oder strengere Kontrolle bei der Integration des Sicherheitstokendiensts in das Abrechnungssystem des Kunden für Abonnenten.

   Beispielsweise kann ein [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services)-Dienstanbieter mehrere Abonnentenpakete (z. B. „Premium“, „Standard“ und „Sport“) anbieten. Der Programmanbieter möchte die Ansprüche in einem Token mit dem Paket eines Abonnenten abgleichen, sodass nur die Inhalte in einem bestimmten Paket zur Verfügung gestellt werden. In diesem Fall bietet ein benutzerdefinierter STS die erforderliche Flexibilität und Kontrolle.

* Einfügen benutzerdefinierter Ansprüche in das Token, um zwischen verschiedenen ContentKeyPolicyOptions mit unterschiedlichen DRM-Lizenzparametern (Abonnementlizenz oder Mietlizenz) auszuwählen.
* Einfügen eines Anspruchs, der den Bezeichner des Inhaltsschlüssels darstellt, auf den das Token den Zugriff ermöglicht.

Wenn Sie einen benutzerdefinierten Sicherheitstokendienst verwenden, müssen Sie zwei Änderungen vornehmen:

* Wenn Sie den Lizenzbereitstellungsdienst für ein Medienobjekt konfigurieren, müssen Sie anstelle des aktuellen Schlüssels aus Azure AD Sicherheitsschlüssel angeben, der vom benutzerdefinierten Sicherheitstokendienst zur Überprüfung verwendet wird.
* Wenn ein JWT generiert wird, wird anstelle des privaten Schlüssels des aktuellen X.509-Zertifikats in Azure AD ein Sicherheitsschlüssel angegeben.

Es gibt zwei Arten von Sicherheitsschlüsseln:

* Symmetrischer Schlüssel: Um ein JWT zu generieren und zu überprüfen, wird ein und derselbe Schlüssel verwendet.
* Asymmetrischer Schlüssel: Ein Paar aus öffentlichem und privatem Schlüssel in einem X.509-Zertifikat wird mit einem privaten Schlüssel verwendet, um ein JWT zu verschlüsseln und zu generieren. Das gleiche Paar wird mit dem öffentlichen Schlüssel verwendet, um das Token zu überprüfen.

Bei Verwendung von .NET Framework/C# als Entwicklungsplattform muss das X.509-Zertifikat für einen asymmetrischen Sicherheitsschlüssel eine Schlüssellänge von mindestens 2048 aufweisen. Diese Schlüssellänge ist eine Voraussetzung für die System.IdentityModel.Tokens.X509AsymmetricSecurityKey-Klasse in .NET Framework. Andernfalls wird die folgende Ausnahme ausgelöst: IDX10630: „System.IdentityModel.Tokens.X509AsymmetricSecurityKey“ für die Signierung darf nicht kleiner als 2.048 Bits sein.

## <a name="custom-key-and-license-acquisition-url"></a>URL für benutzerdefinierte Schlüssel und den Lizenzerwerb

Verwenden Sie folgende Vorlagen, wenn Sie einen anderen Lizenz-/Schlüsselbereitstellungsdienst (nicht Media Services) festlegen möchten. Die beiden ersetzbaren Felder in den Vorlagen sind vorhanden, damit Sie Ihre Streamingrichtlinie für mehrere Ressourcen freigeben können, anstatt für jede Ressource eine Streamingrichtlinie zu erstellen. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Vorlage für die URL des benutzerdefinierten Diensts, der Schlüssel für die Player der Endbenutzer bereitstellt. Diese ist nicht erforderlich, wenn Sie Azure Media Services für das Ausstellen von Schlüsseln verwenden. 

   Die Vorlage unterstützt ersetzbare Token, die der Dienst zur Laufzeit mit dem Wert für die Anforderung aktualisiert.  Folgende Tokenwerte werden derzeit unterstützt:
   * `{AlternativeMediaId}`, der durch den Wert von „StreamingLocatorId.AlternativeMediaId“ ersetzt wird.
   * `{ContentKeyId}`, der durch den Wert des Bezeichners des angeforderten Schlüssels ersetzt wird.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Vorlage für die URL des benutzerdefinierten Diensts, der Lizenzen für die Player der Endbenutzer bereitstellt. Diese ist nicht erforderlich, wenn Sie Azure Media Services für das Ausstellen von Lizenzen verwenden.

   Die Vorlage unterstützt ersetzbare Token, die der Dienst zur Laufzeit mit dem Wert für die Anforderung aktualisiert. Folgende Tokenwerte werden derzeit unterstützt:  
   * `{AlternativeMediaId}`, der durch den Wert von „StreamingLocatorId.AlternativeMediaId“ ersetzt wird.
   * `{ContentKeyId}`, der durch den Wert des Bezeichners des angeforderten Schlüssels ersetzt wird. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Entspricht der vorherigen Vorlage, nur für Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Entspricht der vorherigen Vorlage, nur für FairPlay.  

Beispiel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` enthält den Wert des angeforderten Schlüssels. Sie können `AlternativeMediaId` verwenden, wenn Sie die Anforderung einer Ihrer Entitäten zuordnen möchten. `AlternativeMediaId` kann beispielsweise verwendet werden, um nach Berechtigungen zu suchen.

Unter [Streaming Policies – Create](/rest/api/media/streamingpolicies/create) (Streamingrichtlinien: Erstellung) finden Sie REST-Beispiele, in denen URLs für den benutzerdefinierten Lizenz-/Schlüsselerwerb verwendet werden.

> [!NOTE]
> Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="troubleshoot"></a>Problembehandlung

Wenn der Fehler `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` angezeigt wird, überprüfen Sie, ob Sie die richtige Streamingrichtlinie angegeben haben.

Bei Fehlern, die auf `_NOT_SPECIFIED_IN_URL` enden, sollten Sie sicherstellen, dass Sie das Verschlüsselungsformat in der URL angeben. z. B. `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Weitere Informationen finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Schutz durch AES-Verschlüsselung](protect-with-aes128.md)
* [Schutz mit DRM](protect-with-drm.md)
* [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)
* [Speicherseitige Verschlüsselung](storage-account-concept.md#storage-side-encryption)
* [Häufig gestellte Fragen](frequently-asked-questions.md)
* [JSON Web Token Handler](/dotnet/framework/security/json-web-token-handler)
