---
title: Widevine-Offlinestreaming für Android
description: In diesem Thema erfahren Sie, wie Sie Ihr Azure Media Services v3-Konto für das Offlinestreaming von durch Widevine geschützten Inhalten konfigurieren.
services: media-services
keywords: DASH, DRM, Widevine-Offlinemodus, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c7adab9c14075e12cf5656f9e798e78ff26ff0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955206"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Widevine-Offlinestreaming für Android mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Neben dem Schutz von Inhalten für Onlinestreaming bieten Abonnement- und Verleihdienste für Medieninhalte herunterladbare Inhalte, die genutzt werden können, wenn keine Internetverbindung besteht. Dadurch können Sie beispielsweise Inhalte auf Ihr Smartphone oder Tablet herunterladen und im Offlinemodus ansehen, wenn Sie mit dem Flugzeug unterwegs sind und keine Netzwerkverbindung verfügbar ist. Weitere Szenarien, in denen Sie möglicherweise Inhalte herunterladen möchten:

- Einige Inhaltsanbieter verbieten möglicherweise die Bereitstellung von DRM-Lizenzen außerhalb einer Landesgrenze/Region. Wenn ein Benutzer Inhalte ansehen möchte, während er ins Ausland reist, ist ein Offline-Download erforderlich.
- In einigen Ländern/Regionen ist die Verfügbarkeit und/oder Bandbreite des Internets begrenzt. Benutzer haben die Möglichkeit, Inhalte herunterzuladen, um sie in zufriedenstellender Auflösung ansehen zu können.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

In diesem Artikel erfahren Sie, wie Sie auf Android-Geräten die Wiedergabe im Offlinemodus für durch Widevine geschützte DASH-Inhalte implementieren. Dank Offline-DRM können Sie Abonnement-, Verleih- und Kaufmodelle für Ihre Inhalte anbieten, sodass die Kunden Ihrer Dienste Inhalte problemlos mitnehmen können, wenn sie über keine Internetverbindung verfügen.

Wir stellen drei Optionen für die Erstellung der Android-Player-Apps vor:

> [!div class="checklist"]
> * Erstellen eines Players unter Verwendung der Java-API des ExoPlayer SDK
> * Erstellen eines Players unter Verwendung der Xamarin-Bindung des ExoPlayer SDK
> * Erstellen eines Players unter Verwendung von EME (Encrypted Media Extension; Erweiterung für verschlüsselte Medien) und MSE (Media Source Extension; Medienquellenerweiterung) im Chrome-Browser für Mobilgeräte (ab v62)

Außerdem beantworten wir in diesem Artikel einige häufig gestellte Fragen im Zusammenhang mit dem Offlinestreaming von durch Widevine geschützten Inhalten.

> [!NOTE]
> Offline-DRM wird nur für eine einzelne Anforderung für eine Lizenz in Rechnung gestellt, wenn Sie den Inhalt herunterladen. Fehler werden nicht in Rechnung gestellt.

## <a name="prerequisites"></a>Voraussetzungen 

Vor der Implementierung von Offline-DRM für Widevine auf Android-Geräten sollten Sie zunächst folgende Schritte durchführen:

- Machen Sie sich mit den Konzepten vertraut, die zum Schutz von Onlineinhalten mit Widevine DRM eingeführt werden. Dies wird in den folgenden Dokumenten/Beispielen ausführlich behandelt:
    - [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)
    - [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](protect-with-drm.md)
- Klonen https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Sie müssen den Code in [Verschlüsseln mit DRM mit .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) ändern, um Widevine-Konfigurationen hinzufügen zu können.  
- Machen Sie sich mit dem Google ExoPlayer SDK für Android vertraut. Hierbei handelt es sich um ein Open-Source-Videoplayer-SDK, das die Offlinewiedergabe von durch Widevine DRM geschützten Inhalten unterstützt. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer-Entwicklerhandbuch](https://google.github.io/ExoPlayer/guide.html)
    - [ExoPlayer-Entwicklerblog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Content Protection-Konfiguration in Azure Media Services

Gehen Sie in der Methode [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) folgendermaßen vor:

1. Geben Sie an, wie die Bereitstellung des symmetrischen Schlüssels im Lizenzübermittlungsdienst autorisiert wird: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Konfigurieren der Widevine-Lizenzvorlage:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Erstellen von ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Aktivieren des Offlinemodus

Um den **Offlinemodus** für Widevine-Lizenzen zu aktivieren, müssen Sie die [Widevine-Lizenzvorlage](widevine-license-template-overview.md) konfigurieren. Legen Sie im Objekt **policy_overrides** die Eigenschaft **can_persist** auf **true** fest (Standardwert ist „false“), wie in [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563) gezeigt. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurieren des Android-Players für die Offlinewiedergabe

Eine native Player-App für Android-Geräte lässt sich am einfachsten mithilfe des [Google ExoPlayer SDK](https://github.com/google/ExoPlayer) (einem Open-Source-Videoplayer-SDK) entwickeln. ExoPlayer unterstützt Features, die bislang von der nativen MediaPlayer-API von Android nicht unterstützt werden. Hierzu zählen unter anderem das MPEG-DASH- und das Microsoft Smooth Streaming-Übermittlungsprotokoll.

Ab Version 2.6 enthält ExoPlayer zahlreiche Klassen, die die Offlinewiedergabe von durch Widevine DRM geschützten Inhalten unterstützen. Besonders die OfflineLicenseHelper-Klasse stellt hilfreiche Funktionen bereit, die das Herunterladen, Verlängern und Veröffentlichen von Offlinelizenzen mithilfe von „DefaultDrmSessionManager“ erleichtern. Die Klassen im SDK-Ordner „library/core/src/main/java/com/google/android/exoplayer2/offline/“ unterstützen das Herunterladen von Offlinevideoinhalten.

Folgende Klassen vereinfachen die Verwendung des Offlinemodus im ExoPlayer SDK für Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Entwickler sollten während der Anwendungsentwicklung das [ExoPlayer-Entwicklerhandbuch](https://google.github.io/ExoPlayer/guide.html) und den entsprechenden [Entwicklerblog](https://medium.com/google-exoplayer) heranziehen. Von Google steht derzeit keine umfassend dokumentierte Referenzimplementierung und kein umfassend dokumentierter Beispielcode für die ExoPlayer-App mit Widevine-Offlineunterstützung zur Verfügung. Die Informationen sind daher auf das Entwicklerhandbuch und den Blog beschränkt. 

### <a name="working-with-older-android-devices"></a>Verwenden älterer Android-Geräte

Für einige ältere Android-Geräte müssen Sie Werte für die folgenden (in der [Widevine-Lizenzvorlage](widevine-license-template-overview.md) definierten) Eigenschaften von **policy_overrides** festlegen: **rental_duration_seconds**, **playback_duration_seconds** und **license_duration_seconds**. Alternativ können Sie die Werte auf Null (unbegrenzte Dauer) festlegen.  

Die Werte müssen festgelegt werden, um einen Ganzzahlüberlauffehler zu vermeiden. Weitere Erläuterungen zu diesem Problem finden Sie unter https://github.com/google/ExoPlayer/issues/3150 und https://github.com/google/ExoPlayer/issues/3112. <br/>Wenn Sie die Werte nicht explizit festlegen, werden für **PlaybackDurationRemaining** und **LicenseDurationRemaining** sehr große Werte zugewiesen (beispielsweise „9223372036854775807“ – der größtmögliche positive Wert für eine 64-Bit-Ganzzahl). Dadurch scheint die Widevine-Lizenz abgelaufen zu sein, und es findet keine Entschlüsselung statt. 

Dieses Problem tritt ab Android 5.0 Lollipop nicht mehr auf, da es sich bei Android 5.0 um die erste Android-Version mit uneingeschränkter Unterstützung von ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) und 64-Bit-Plattformen handelt. Android 4.4 KitKat und ältere Versionen waren dagegen ursprünglich für die Unterstützung von ARMv7 und 32-Bit-Plattformen konzipiert.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Erstellen einer Android-Wiedergabe-App mithilfe von Xamarin

Xamarin-Bindungen für ExoPlayer finden Sie unter den folgenden Links:

- [Xamarin bindings library for the Google ExoPlayer library](https://github.com/martijn00/ExoPlayerXamarin) (Bibliothek mit Xamarin-Bindungen für die Google ExoPlayer-Bibliothek)
- [Xamarin-Bindungen für ExoPlayer (NuGet)](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Weitere Informationen finden Sie in folgendem Thema: [Xamarin-Bindung](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-Apps für Android

Ab [Chrome für Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates) werden permanente Lizenzen in EME unterstützt. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) wird nun auch in Chrome für Android unterstützt. Dadurch können Sie Offlinewiedergabeanwendungen in Chrome erstellen, wenn Ihre Endbenutzer mindestens über diese Chrome-Version verfügen. 

Darüber hinaus hat Google ein PWA-Beispiel (Progressive Web App) erstellt und als Open-Source-Code bereitgestellt: 

- [Quellcode](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Von Google gehostete Version](https://biograf-155113.appspot.com/ttt/episode-2/) (funktioniert nur auf Android-Geräten mit Chrome ab Version 62)

Wenn Sie Ihren mobilen Chrome-Browser auf einem Android-Smartphone mindestens auf die Version 62 upgraden und die oben erwähnte gehostete Beispiel-App testen, sehen Sie, dass sowohl das Onlinestreaming als auch die Offlinewiedergabe funktioniert.

Die oben erwähnte Open-Source-PWA-App wurde in Node.js erstellt. Wenn Sie eine eigene Version auf einem Ubuntu-Server hosten möchten, beachten Sie die folgenden allgemeinen Probleme, die die Wiedergabe verhindern können:

1. CORS-Problem: Das Beispielvideo in der Beispiel-App wird in https://storage.googleapis.com/biograf-video-files/videos/ gehostet. Google hat CORS für alle Testbeispiele eingerichtet, die im Google Cloud Storage-Bucket gehostet werden. Sie werden mit CORS-Headern bereitgestellt, die explizit den CORS-Eintrag angeben: `https://biograf-155113.appspot.com` (die Domäne, in der Google das Beispiel hostet). Dadurch wird der Zugriff durch andere Sites verhindert. Wenn Sie es versuchen, wird der folgende HTTP-Fehler ausgegeben: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Zertifikatsproblem: Ab der Chrome-Version 58 wird von EME für Widevine HTTPS benötigt. Daher muss die Beispiel-App mit einem X.509-Zertifikat über HTTPS gehostet werden. Ein herkömmliches Testzertifikat funktioniert aufgrund der folgenden Anforderungen nicht: Sie müssen ein Zertifikat abrufen, dass die folgenden Mindestanforderungen erfüllt:
    - Für Chrome und Firefox muss das Zertifikat die Einstellung für den alternativen Antragstellernamen (Subject Alternative Name, SAN) enthalten.
    - Das Zertifikat muss über eine vertrauenswürdige Zertifizierungsstelle verfügen. Ein selbstsigniertes Entwicklungszertifikat kann nicht verwendet werden.
    - Der allgemeine Name (Common Name, CN) des Zertifikats muss dem DNS-Namen des Webservers oder Gateways entsprechen.

## <a name="faqs"></a>Häufig gestellte Fragen

Weitere Informationen finden unter [Häufig gestellte Fragen zu Widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Zusätzliche Hinweise

Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie erfahren, wie Sie auf Android-Geräten die Wiedergabe im Offlinemodus für durch Widevine geschützte DASH-Inhalte implementieren.  Außerdem wurden einige häufig gestellte Fragen im Zusammenhang mit dem Offlinestreaming von durch Widevine geschützten Inhalten beantwortet.
