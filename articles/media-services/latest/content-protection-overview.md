---
title: Inhaltsschutz mit der dynamischen Verschlüsselung von Media Services (Azure) | Microsoft-Dokumentation
description: In diesem Artikel finden Sie eine Übersicht über den Inhaltsschutz mit dynamischer Verschlüsselung. Zudem werden Streamingprotokolle und Verschlüsselungsverfahren erläutert.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 99aea38ec877074075eaec8cf9ab8da077901acf
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393107"
---
# <a name="content-protection-with-dynamic-encryption"></a>Inhaltsschutz mit dynamischer Verschlüsselung

Mit Azure Media Services können Sie Ihre Medien ab dem Zeitpunkt, an dem sie Ihren Computer verlassen, während des gesamten Prozesses der Speicherung, Verarbeitung und Übermittlung sichern. Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. 

Die folgende Abbildung veranschaulicht den Media Services-Workflow zum Schutz von Inhalten: 

![Inhalt schützen](./media/content-protection/content-protection.svg)

&#42; *Die dynamische Verschlüsselung unterstützt AES-128 mit unverschlüsseltem Schlüssel, CBCS und CENC. Details finden Sie [hier](#streaming-protocols-and-encryption-types) in der Unterstützungsmatrix.*

Dieser Artikel erläutert die relevanten Konzepte und Begriffe für den Inhaltsschutz mit Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hauptkomponenten eines Inhaltsschutzsystems

Für einen erfolgreichen Entwurf Ihres Inhaltsschutzsystems oder Ihrer Inhaltsschutzanwendung müssen Sie mit dem vollständigen Umfang und Aufwand vertraut sein. Die folgende Aufstellung bietet eine Übersicht über drei Komponenten, die Sie implementieren müssen. 

1. Azure Media Services-Code
  
   Das [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)-Beispiel zeigt Ihnen die Implementierung eines Multi-DRM-Systems mit Media Services v3 über .NET. Zudem zeigt es die Verwendung des Media Services-Diensts zur Lizenz-/Schlüsselbereitstellung. Sie können jedes Medienobjekt mit mehreren Verschlüsselungstypen (AES-128, PlayReady, Widevine, FairPlay) verschlüsseln. Informationen zu sinnvollen Kombinationen finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](#streaming-protocols-and-encryption-types).
  
   Das Beispiel veranschaulicht die folgenden Schritte:

   1. Erstellen und konfigurieren Sie [Richtlinien für Inhaltsschlüssel](content-key-policy-concept.md). Sie erstellen eine **Richtlinie für den Inhaltsschlüssel**, um zu konfigurieren, wie der Inhaltsschlüssel (der sicheren Zugriff auf Ihre Medienobjekte bereitstellt) an Endclients übermittelt wird.    

      * Definieren Sie die Autorisierung zur Lizenzbereitstellung, die die Logik der Autorisierungsprüfung basierend auf Ansprüchen in JWT angibt.
      * Konfigurieren Sie die Lizenzen [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md) und/oder [FairPlay](fairplay-license-overview.md). Mit den Vorlagen können Sie die Rechte und Berechtigungen für die einzelnen verwendeten DRMs konfigurieren.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Erstellen Sie einen [Streaminglocator](streaming-locators-concept.md), der so konfiguriert ist, dass er das verschlüsselte Objekt streamen kann. 
  
      Der **Streaminglocator** muss einer [Streamingrichtlinie](streaming-policy-concept.md) zugeordnet sein. In diesem Beispiel wird „StreamingLocator.StreamingPolicyName“ auf die Richtlinie „Predefined_MultiDrmCencStreaming“ festgelegt. Die PlayReady- und Widevine-Verschlüsselungen werden angewendet, und der Schlüssel wird basierend auf den konfigurierten DRM-Lizenzen an den Wiedergabeclient übermittelt. Wenn Sie den Stream auch mit CBCS (FairPlay) verschlüsseln möchten, verwenden Sie „Predefined_MultiDrmStreaming“.
      
      Der Streaminglocator wird außerdem der definierten **Richtlinie für den Inhaltsschlüssel** zugeordnet.
    
   3. Erstellen Sie einen Testtoken.

      Die **GetTokenAsync**-Methode zeigt, wie Sie einen Testtoken erstellen.
   4. Erstellen Sie die Streaming-URL.

      Die **GetDASHStreamingUrlAsync**-Methode zeigt, wie Sie die Streaming-URL erstellen. In diesem Fall streamt die URL den **DASH**-Inhalt.

2. Player mit AES- oder DRM-Client. Eine Videoplayer-App basierend auf einem Player-SDK (nativ oder browserbasiert) muss die folgenden Anforderungen erfüllen:
   * Das Player-SDK unterstützt die erforderlichen DRM-Clients.
   * Das Player-SDK unterstützt die erforderlichen Streamingprotokolle: Smooth, DASH und/oder HLS.
   * Das Player-SDK muss die Übergabe eines JWT-Token in die Lizenzerwerbsanforderung verarbeiten können.
  
     Sie können mithilfe der [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) einen Player erstellen. Verwenden Sie die [ProtectionInfo-API von Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/), um anzugeben, welche DRM-Technologie auf unterschiedlichen DRM-Plattformen verwendet werden soll.

     Zum Testen von mit AES oder CENC (Widevine und/oder PlayReady) verschlüsselten Inhalten können Sie [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) verwenden. Stellen Sie sicher, dass Sie auf „Erweiterte Optionen“ klicken und Ihre Verschlüsselungsoptionen angeben.

     Wenn Sie mit FairPlay verschlüsselte Inhalte testen möchten, verwenden Sie [diesen Testplayer](https://aka.ms/amtest). Der Player unterstützt Widevine-, PlayReady- und FairPlay-DRMs sowie die AES-128-Verschlüsselung mit unverschlüsselten Schlüsseln. 
    
     Sie müssen den richtigen Browser zum Testen der verschiedenen DRMs auswählen: Chrome/Opera/Firefox für Widevine, Microsoft Edge/IE11 für PlayReady, Safari unter macOS für FairPlay.

3. Sicherheitstokendienst (STS), der JSON Web Token (JWT) als Zugriffstoken für den Zugriff auf die Back-End-Ressource ausgibt. Sie können die AMS-Lizenzbereitstellungsdienste als Back-End-Ressource verwenden. Ein Sicherheitstokendienst muss Folgendes definieren:

   * Aussteller und Zielgruppe (oder Gültigkeitsbereich)
   * Ansprüche, die von Geschäftsanforderungen für den Inhaltsschutz abhängen
   * Symmetrische oder asymmetrische Überprüfung für die Signaturüberprüfung
   * Unterstützung des Schlüsselrollovers (sofern erforderlich)

     Sie können [dieses STS-Tool](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) verwenden, um den Sicherheitstokendienst zu testen. Das Tool unterstützt alle drei Typen von Verifizierungsschlüsseln: symmetrisch, asymmetrisch oder Azure AD mit Schlüsselrollover. 

> [!NOTE]
> Es wird dringend empfohlen, jede Komponente umfassend einzurichten und vollständig zu testen (siehe Beschreibung weiter oben), bevor Sie mit der nächsten Komponente fortfahren. Verwenden Sie zum Testen Ihres Inhaltsschutzsystems die in der Aufstellung oben angegebenen Tools.  

## <a name="streaming-protocols-and-encryption-types"></a>Streamingprotokolle und Verschlüsselungstypen

Mit Media Services können Sie Ihre zu übermittelnden Inhalte dynamisch mit unverschlüsselten AES-Schlüsseln oder per DRM-Verschlüsselung über Microsoft PlayReady, Google Widevine oder FairPlay verschlüsseln. Zurzeit können Sie die Streamingformate HTTP Live Streaming (HLS), MPEG DASH und Smooth Streaming verschlüsseln. Jedes Protokoll unterstützt die folgenden Verschlüsselungsmethoden:

### <a name="hls"></a>HLS

Das HLS-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas.

|Containerformat|Verschlüsselungsschema|URL-Beispiel|
|---|---|---|
|Alle|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS, CMAF und FairPlay (einschließlich HEVC und H.265) werden auf folgenden Geräten unterstützt:

* iOS 11 oder höher 
* iPhone 8 oder höher
* MacOS High Sierra mit Intel-CPU der 7. Generation

### <a name="mpeg-dash"></a>MPEG-DASH

Das MPEG-DASH-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas.

|Containerformat|Verschlüsselungsschema|URL-Beispiele
|---|---|---|
|Alle|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Das Smooth Streaming-Protokoll unterstützt folgende Containerformate und Verschlüsselungsschemas.

|Protocol|Containerformat|Verschlüsselungsschema|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browser

Folgende DRM-Clients werden von gängigen Browsern unterstützt:

|"Browser"|Verschlüsselung|
|---|---|
|Chrome|Widevine|
|Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Unverschlüsselter AES-128-Schlüssel im Vergleich zu DRM

Kunden fragen sich oft, ob sie die AES-Verschlüsselung oder ein DRM-System verwenden sollten. Der Hauptunterschied zwischen den beiden Systemen besteht darin, dass bei der AES-Verschlüsselung der Inhaltsschlüssel während der Übertragung ohne zusätzliche Verschlüsselung (als Klartext) an den Client übertragen wird. So kann der Schlüssel zur Entschlüsselung des Inhalts für den Clientplayer zugänglich gemacht und in einer Netzwerkablaufverfolgung auf dem Client als unverschlüsselter Text angezeigt werden. Die Verschlüsselung mit einem unverschlüsselten AES-128-Schlüssel eignet sich für Anwendungsfälle, in denen der Betrachter vertrauenswürdig ist (z.B. bei Unternehmensvideos, die innerhalb eines Unternehmens verteilt und von Mitarbeitern angesehen werden).

DRM-Systeme wie PlayReady, Widevine und FairPlay bieten eine zusätzliche Verschlüsselungsebene für den Schlüssel, der zum Entschlüsseln des Inhalts verwendet wird (im Vergleich zum unverschlüsselten AES-128-Schlüssel). Der Inhaltsschlüssel wird zusätzlich zur Verschlüsselung auf Übertragungsebene durch TLS in einen durch die DRM-Runtime geschützten Schlüssel verschlüsselt. Zusätzlich erfolgt die Entschlüsselung in einer sicheren Umgebung auf Betriebssystemebene, wo ein Angriff durch einen böswilligen Benutzer schwieriger auszuführen ist. DRM wird für Anwendungsfälle empfohlen, in denen der Betrachter möglicherweise nicht vertrauenswürdig ist und Sie ein Höchstmaß an Sicherheit benötigen.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Dynamische Verschlüsselung und des Schlüsselübermittlungsdienst

In Media Services v3 ist dem Streaminglocator ein Inhaltsschlüssel zugeordnet (siehe [dieses Beispiel](protect-with-aes128.md)). Bei Verwendung des Media Services-Schlüsselübermittlungsdiensts kann der Inhaltsschlüssel durch Azure Media Services generiert werden. Sie sollten den Inhaltsschlüssel selbst generieren, wenn Sie Ihren eigenen Schlüsselbereitstellungsdienst verwenden oder wenn Sie ein Hochverfügbarkeitsszenario verwenden, für das Sie denselben Inhaltsschlüssel in zwei Rechenzentren benötigen.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um den Inhalt dynamisch mit einem unverschlüsselten AES-Schlüssel oder per DRM-Verschlüsselung zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Media Services-Schlüsselübermittlungsdienst oder dem von Ihnen angegebenen Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Richtlinie für Inhaltsschlüssel aus, die Sie für den Schlüssel angegeben haben.

Media Services bietet einen Schlüsselübermittlungsdienst zum Übermitteln von DRM-Lizenzen (PlayReady, Widevine und FairPlay) und unverschlüsselten AES-Schlüsseln an autorisierte Clients. Zum Konfigurieren von Autorisierungs- und Authentifizierungsrichtlinien für Ihre Lizenzen und Schlüssel können Sie die REST-API oder eine Media Services-Clientbibliothek verwenden.

### <a name="custom-key-and-license-acquisition-url"></a>URL für benutzerdefinierte Schlüssel und den Lizenzerwerb

Verwenden Sie folgende Vorlagen, wenn Sie einen anderen Schlüssel und Lizenzbereitstellungsdienst (nicht Media Services) festlegen möchten. Die beiden ersetzbaren Felder in den Vorlagen sind vorhanden, damit Sie Ihre Streamingrichtlinie für mehrere Ressourcen freigeben können, anstatt für jede Ressource eine Streamingrichtlinie zu erstellen. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate: Vorlage für die URL des benutzerdefinierten Diensts, der Schlüssel für die Player der Endbenutzer bereitstellt. Diese ist nicht erforderlich, wenn Sie Azure Media Services für das Ausstellen von Schlüsseln verwenden. Die Vorlage unterstützt ersetzbare Token, die der Dienst zur Laufzeit mit dem Wert für die Anforderung aktualisiert.  Die derzeit unterstützten Werte für Token sind {AlternativeMediaId} (wird durch den Wert von StreamingLocatorId.AlternativeMediaId ersetzt) und {ContentKeyId} (wird durch den Wert des Bezeichners des angeforderten Schlüssels ersetzt).
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate: Vorlage für die URL des benutzerdefinierten Diensts, der Lizenzen für die Player der Endbenutzer bereitstellt. Diese ist nicht erforderlich, wenn Sie Azure Media Services für das Ausstellen von Lizenzen verwenden. Die Vorlage unterstützt ersetzbare Token, die der Dienst zur Laufzeit mit dem Wert für die Anforderung aktualisiert. Die derzeit unterstützten Werte für Token sind {AlternativeMediaId} (wird durch den Wert von StreamingLocatorId.AlternativeMediaId ersetzt) und {ContentKeyId} (wird durch den Wert des Bezeichners des angeforderten Schlüssels ersetzt). 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate: Identische Funktionsweise wie die oben behandelte Vorlage, gilt jedoch für Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate: Identische Funktionsweise wie die oben behandelte Vorlage, gilt jedoch für FairPlay.  

Beispiel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` enthält den Wert des angeforderten Schlüssels, und `AlternativeMediaId` kann verwendet werden, wenn Sie die Anforderung einer Ihrer Entitäten zuordnen möchten. `AlternativeMediaId` kann beispielsweise verwendet werden, um nach Berechtigungen zu suchen.

Unter [Streaming Policies - Create (Streamingrichtlinien: Erstellung)](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) finden Sie REST-Beispiele, in denen URLs für benutzerdefinierte Schlüssel und den Lizenzerwerb verwendet werden.

## <a name="control-content-access"></a>Steuern des Zugriffs auf Inhalte

Sie können steuern, wer Zugriff auf Ihre Inhalte hat, indem Sie die Richtlinie für Inhaltsschlüssel konfigurieren. Media Services unterstützt mehrere Möglichkeiten zur Autorisierung von Benutzern, die Schlüssel anfordern. Sie müssen die Richtlinie für Inhaltsschlüssel konfigurieren. Der Client (Player) muss die Richtlinie erfüllen, bevor der Schlüssel an den Client übermittelt werden kann. Die Richtlinie für Inhaltsschlüssel kann eine **offene** oder eine **Tokeneinschränkung** enthalten. 

Bei einer Richtlinie mit Tokeneinschränkung wird der Inhaltsschlüssel nur an einen Client gesendet, der in der Schlüssel-/Lizenzanforderung ein gültiges JWT (JSON Web Token) oder SWT (Simple Web Token) präsentiert. Dieses Token muss von einem Sicherheitstokendienst ausgestellt werden. Sie können Azure Active Directory als Sicherheitstokendienst verwenden oder einen benutzerdefinierten Sicherheitstokendienst bereitstellen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausstellungsansprüche konfiguriert sein, die Sie in der Konfiguration der Tokeneinschränkung angegeben haben. Der Schlüsselübermittlungsdienst von Media Services gibt den angeforderten Schlüssel bzw. die Lizenz an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Schlüssel bzw. die Lizenz konfigurierten Ansprüchen übereinstimmen.

Bei der Konfiguration der Richtlinie mit Tokeneinschränkung müssen die Parameter für den primären Verifizierungsschlüssel (primary verification key), den Aussteller (issuer) und die Zielgruppe (audience) angegeben werden. Der primäre Verifizierungsschlüssel enthält den Schlüssel, mit dem das Token signiert wurde. Der Aussteller ist der Sicherheitstokendienst, der das Token ausstellt. „Audience“ (manchmal auch „Scope“) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen.

Benutzer verwenden häufig einen benutzerdefinierten Sicherheitstokendienst, um benutzerdefinierte Ansprüche in das Token einzufügen. Dadurch kann zwischen verschiedenen ContentKeyPolicyOptions mit unterschiedlichen DRM-Lizenzparametern (Abonnementlizenz oder Mietlizenz) ausgewählt oder ein Anspruch eingefügt werden, der den Bezeichner des Inhaltsschlüssels darstellt, auf den das Token den Zugriff ermöglicht.
 
## <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

Zum Schutz Ihrer im Ruhezustand befindlichen Ressourcen sollten die Ressourcen durch die speicherseitige Verschlüsselung verschlüsselt werden. Die folgende Tabelle zeigt, wie die speicherseitige Verschlüsselung in Media Services v3 funktioniert:

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v3|
|---|---|---|
|Media Services-Speicherverschlüsselung| AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet|Nicht unterstützt<sup>(1)</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet|Unterstützt|
|[Clientseitige Storage-Verschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet|Nicht unterstützt|

<sup>1</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Ressourcen mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Ressourcen funktioniert, jedoch nicht die Erstellung neuer zulässt.

## <a name="troubleshoot"></a>Problembehandlung

Wenn der Fehler `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` angezeigt wird, überprüfen Sie, ob Sie die richtige Streamingrichtlinie angegeben haben.

Bei Fehlern, die auf `_NOT_SPECIFIED_IN_URL` enden, sollten Sie sicherstellen, dass Sie das Verschlüsselungsformat in der URL angeben. Beispiel: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Weitere Informationen finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Schutz durch AES-Verschlüsselung](protect-with-aes128.md)
* [Schutz mit DRM](protect-with-drm.md)
* [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)
* [Häufig gestellte Fragen](frequently-asked-questions.md)

