---
title: Häufig gestellte Fragen zu Azure Media Services v3 | Microsoft-Dokumentation
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 50a0fe0fa5dece41ac9e343d5a8939e8d9dc634e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426886"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Häufig gestellte Fragen zu Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Media Services v3.

## <a name="general"></a>Allgemein

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Welche Einschränkungen bestehen im Azure-Portal im Zusammenhang mit Media Services v3?

Sie können das [Azure-Portal](https://portal.azure.com/) für Folgendes nutzen: Verwalten von v3-Liveereignissen, Anzeigen von v3-Objekten und -Aufträgen, Abrufen von Informationen über das Zugreifen auf APIs sowie Verschlüsseln von Inhalten. <br/>Verwenden Sie für alle anderen Verwaltungsaufgaben (etwa für die Verwaltung von Transformationen und Aufträgen oder die Analyse von v3-Inhalten) die [REST-API](/rest/api/media/accountfilters), die [CLI](/cli/azure/ams) oder eins der unterstützten [SDKs](media-services-apis-overview.md#sdks).

Wenn Ihr Video zuvor mithilfe der Media Services v3-API in das Media Services-Konto hochgeladen wurde oder der Inhalt basierend auf einer Liveausgabe generiert wurde, sind die Schaltflächen **Codieren** , **Analysieren** und **Verschlüsseln** im Azure-Portal nicht verfügbar. Verwenden Sie für diese Aufgaben die Media Services v3-APIs.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Welche Azure-Rollen können Aktionen mit den Ressourcen von Azure Media Services durchführen? 

Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung (RBAC) in Azure für Media Services-Konten](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Wie funktioniert das Streaming auf Apple iOS-Geräten?

Stellen Sie sicher, dass Sie am Ende Ihres Pfads (nach dem Bereich **/manifest** der URL) **(format=m3u8-aapl)** verwenden, um den Ursprungsserver des Streamings anzuweisen, HLS-Inhalte (HTTP Live Streaming) für die Nutzung auf nativen Apple iOS-Geräten zurückzugeben. Ausführliche Informationen finden Sie unter [Bereitstellung von Inhalten](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Wie lassen sich reservierte Einheiten für Medien konfigurieren?

Für die Audio- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, sollten Sie Ihr Konto mit zehn S3-MRUs (Media Reserved Units, reservierte Einheiten für Medien) bereitstellen. Wenn Sie mehr als zehn S3-MRUs benötigen, erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket.

Ausführliche Informationen dazu finden Sie unter [Skalieren der Medienverarbeitung](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Was ist die empfohlene Methode zum Verarbeiten von Videos?

Verwenden Sie [Transformationen](/rest/api/media/transforms), um allgemeine Aufgaben zur Codierung oder Analyse von Videos zu konfigurieren. Jede Transformation beschreibt eine Vorgehensweise oder einen Workflow von Aufgaben für die Verarbeitung Ihrer Video- oder Audiodateien. Ein [Auftrag](/rest/api/media/jobs) ist die eigentliche Anforderung an Media Services, die erstellte Transformation auf ein Eingabevideo oder auf einen Audioinhalt anzuwenden. Nach dem Erstellen der Transformation können Sie mithilfe von Media Services-APIs oder veröffentlichten SDKs Aufträge übermitteln. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Ich habe ein Video hochgeladen, codiert und veröffentlicht. Warum wird das Video nicht abgespielt, wenn ich versuche, es zu streamen?

Einer der häufigsten Gründe ist, dass der Streamingendpunkt, von dem Sie die Wiedergabe ausführen möchten, nicht den Status „Wird ausgeführt“ aufweist.

### <a name="how-does-pagination-work"></a>Wie funktioniert die Paginierung?

Bei Verwendung der Paginierung sollten Sie immer den Link „Weiter“ verwenden, um die Sammlung zu durchlaufen, und nicht auf eine bestimmte Seitengröße zurückgreifen. Weitere Informationen und Beispiele finden Sie unter [Filterung, Sortierung, Paginierung](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Welche Features sind noch nicht in Azure Media Services v3 verfügbar?

Details finden Sie unter [Featurelücken in Bezug auf v2-APIs](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Wie wird ein Media Services-Konto zwischen Abonnements verschoben?  

Informationen dazu finden Sie unter [Verschieben eines Media Services-Kontos zwischen Abonnements](media-services-account-concept.md).

## <a name="live-streaming"></a>Livestreaming 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Wie beende ich den Livestream, nachdem die Übertragung abgeschlossen wurde?

Sie können dies clientseitig oder serverseitig ausführen.

#### <a name="client-side"></a>Clientseitig

Die Webanwendung sollte die Benutzer beim Schließen des Browsers fragen, ob sie den Broadcast beenden möchten. Dies ist ein Browserereignis, das von Ihrer Webanwendung behandelt werden kann.

#### <a name="server-side"></a>Serverseitig

Sie können Liveereignisse überwachen, indem Sie Event Grid-Ereignisse abonnieren. Weitere Informationen finden Sie unter [Event Grid-Ereignisschema](media-services-event-schemas.md#live-event-types).

Sie haben folgende Möglichkeiten:

* [Abonnieren](reacting-to-media-services-events.md) Sie die Ereignisse für [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) auf Streamebene, und überwachen Sie, ob für einen bestimmten Zeitraum neue Verbindungen eingehen, um Ihr Liveereignis zu beenden und zu löschen.
* [Abonnieren](reacting-to-media-services-events.md) Sie die [Taktereignisse](media-services-event-schemas.md#liveeventingestheartbeat) auf Spurebene. Wenn die eingehende Bitrate bei allen Spuren auf 0 fällt oder wenn der letzte Zeitstempel nicht mehr ansteigt, können Sie das Liveereignis sicher beenden. Die Taktereignisse gehen alle 20 Sekunden für jede Spur ein, sodass die Informationsmenge etwas höher ausfallen könnte.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Wie füge ich Unterbrechungen/Videos und Bildtafeln während eines Livestreams ein?

Die Livecodierung von Media Services v3 unterstützt aktuell das Einfügen von Video oder Bildtafeln in Livedatenströme noch nicht. 

Sie können einen [lokalen Liveencoder](recommended-on-premises-live-encoders.md) verwenden, um das Quellvideo zu wechseln. Viele Apps bieten die Möglichkeit zum Wechseln der Quelle, darunter Telestream Wirecast, Switcher Studio (unter iOS) und OBS Studio (kostenlose App).

## <a name="content-protection"></a>Inhaltsschutz

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Sollte ich eine Verschlüsselung mit unverschlüsseltem AES-128-Schlüssel oder ein DRM-System verwenden?

Kunden fragen sich oft, ob sie die AES-Verschlüsselung oder ein DRM-System verwenden sollten. Der Hauptunterschied zwischen den beiden Systemen besteht darin, dass bei der AES-Verschlüsselung der Inhaltsschlüssel über TLS an den Client übertragen wird, sodass er bei der Übertragung verschlüsselt ist, aber keine zusätzliche Verschlüsselung aufweist (Klartext). So kann der Schlüssel zur Entschlüsselung des Inhalts für den Clientplayer zugänglich gemacht und in einer Netzwerkablaufverfolgung auf dem Client als unverschlüsselter Text angezeigt werden. Die Verschlüsselung mit einem unverschlüsselten AES-128-Schlüssel eignet sich für Anwendungsfälle, in denen der Betrachter vertrauenswürdig ist (z.B. bei Unternehmensvideos, die innerhalb eines Unternehmens verteilt und von Mitarbeitern angesehen werden).

DRM-Systeme wie PlayReady, Widevine und FairPlay bieten eine zusätzliche Verschlüsselungsebene für den Schlüssel, der zum Entschlüsseln des Inhalts verwendet wird (im Vergleich zu einem unverschlüsselten AES-128-Schlüssel). Der Inhaltsschlüssel wird zusätzlich zur Verschlüsselung auf Übertragungsebene durch TLS mit einem durch die DRM-Runtime geschützten Schlüssel verschlüsselt. Zusätzlich erfolgt die Entschlüsselung in einer sicheren Umgebung auf Betriebssystemebene, wo ein Angriff durch einen böswilligen Benutzer schwieriger auszuführen ist. DRM wird für Anwendungsfälle empfohlen, in denen der Betrachter möglicherweise nicht vertrauenswürdig ist und Sie ein Höchstmaß an Sicherheit benötigen.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Wie zeige ich ein Video nur für Benutzer mit bestimmten Berechtigungen an, ohne Azure AD zu verwenden?

Sie müssen keinen bestimmten Tokenanbieter wie Azure Active Directory (Azure AD) verwenden. Sie können einen eigenen [JWT](https://jwt.io/)-Anbieter (einen sogenannten Sicherheitstokendienst oder STS) erstellen und dabei eine Verschlüsselung mit asymmetrischem Schlüssel verwenden. Sie können in Ihrem benutzerdefinierten Sicherheitstokendienst Ansprüche basierend auf Ihrer Geschäftslogik hinzufügen.

Stellen Sie sicher, dass der Aussteller, die Zielgruppe und die Ansprüche genau dem Inhalt des JWT und dem Wert `ContentKeyPolicyRestriction` in `ContentKeyPolicy` entsprechen.

Weitere Informationen finden Sie unter [Inhaltsschutz mit der dynamischen Verschlüsselung von Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Wie und wo kann ich JWT-Token abrufen, um damit dann eine Lizenz oder einen Schlüssel anzufordern?

Für die Produktion benötigen Sie einen Sicherheitstokendienst (Webdienst), der bei einer HTTPS-Anforderung ein JWT-Token ausgibt. Zum Testen können Sie den in der `GetTokenAsync`-Methode angegebenen Code verwenden, der in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) definiert ist.

Der Player fordert nach der Authentifizierung eines Benutzers beim STS ein solches Token an und weist dieses als Wert des Tokens zu. Sie können die [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) verwenden.

Ein Beispiel für die Ausführung des STS mit einem symmetrischen oder einem asymmetrischen Schlüssel finden Sie im [JWT-Tool](https://aka.ms/jwt). Ein Beispiel für einen Player, der auf Azure Media Player basiert und ein solches JWT-Token verwendet, finden Sie im [Azure-Medientesttool](https://aka.ms/amtest). (Erweitern Sie den Link **player_settings** , um die Tokeneingabe anzuzeigen.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Wie autorisiere ich Anforderungen zum Streamen von Videos mit AES-Verschlüsselung?

Der richtige Ansatz ist die Nutzung von eines Sicherheitstokendiensts. Fügen Sie im STS je nach Benutzerprofil unterschiedliche Ansprüche hinzu (z. B. „Premium-Benutzer“, „Basic-Benutzer“, „Benutzer der kostenlosen Testversion“). Bei unterschiedlichen Ansprüchen in einem JWT kann der Benutzer unterschiedliche Inhalte sehen. Für andere Inhalte oder Medienobjekte weist `ContentKeyPolicyRestriction` den entsprechenden Wert für `RequiredClaims` auf.

Verwenden Sie die Azure Media Services-APIs für die Konfiguration von Lizenzen/Schlüsselbereitstellungen und die Verschlüsselung Ihrer Objekte (siehe [dieses Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Weitere Informationen finden Sie unter

- [Übersicht über den Inhaltsschutz](content-protection-overview.md)
- [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Sollte ich HTTP oder HTTPS verwenden?
Die ASP.NET MVC-Playeranwendung muss Folgendes unterstützen:

* Die Benutzerauthentifizierung in Azure AD, die über HTTPS erfolgen muss.
* Den JWT-Austausch zwischen Client und Azure AD, der über HTTPS erfolgen muss.
* Den DRM-Lizenzerwerb durch den Client, der über HTTPS erfolgen muss, wenn Lizenzen von Media Services bereitgestellt werden. Bei der PlayReady-Produktsuite ist HTTPS für die Lizenzbereitstellung nicht zwingend vorgegeben. Wenn Ihr PlayReady-Lizenzserver sich außerhalb von Media Services befindet, können Sie HTTP oder HTTPS verwenden.

Die ASP.NET-Playeranwendung verwendet HTTPS als Best Practice, daher befindet der Media Player sich auf einer HTTPS-Seite. Für das Streaming wird jedoch HTTP bevorzugt, daher müssen Sie diese Probleme mit gemischten Inhalten berücksichtigen:

* Der Browser lässt keine gemischten Inhalte zu. Plug-Ins wie Silverlight und OSMF für Smooth Streaming und DASH lassen diese jedoch zu. Gemischte Inhalte sind ein Sicherheitsproblem, da sie die Möglichkeit eröffnen, schadhaften JavaScript-Code einzuschleusen und dadurch möglicherweise Kundendaten zu gefährden. Browser blockieren diese Funktion standardmäßig. Die einzige Möglichkeit, dieses Problem zu begehen, besteht darin, auf Server- bzw. Ursprungsseite alle Domänen zuzulassen (egal ob HTTPS oder HTTP). Dies ist aber wahrscheinlich auch nicht sehr sinnvoll.
* Vermeiden Sie gemischte Inhalte. Sowohl die Playeranwendung als auch Media Player sollten HTTP oder HTTPS verwenden. Wenn Sie gemischte Inhalte wiedergeben, erfordert die Smooth Streaming-Technologie von Silverlight das Bestätigen einer Warnung zu gemischten Inhalten. Die Smooth Streaming-Technologie von Flash verarbeitet gemischte Inhalte ohne entsprechende Warnung.
* Wenn Ihr Streamingendpunkt vor August 2014 erstellt wurde, wird HTTPS nicht unterstützt. Erstellen und verwenden Sie in diesem Fall einen neuen Streamingendpunkt für HTTPS.

### <a name="what-about-live-streaming"></a>Was gilt für Livestreaming?

Sie können genau den gleichen Entwurf und die gleiche Implementierung zum Schutz von Livestreams in Media Services verwenden, indem Sie das einem Programm zugeordnete Medienobjekt als VOD-Medienobjekt behandeln. Um Liveinhalte mit mehreren DRM-Systemen zu schützen, wenden Sie dieselben Einrichtungs- und Verarbeitungsschritte wie bei einem VOD-Medienobjekt an, bevor Sie das Medienobjekt der Liveausgabe zuordnen.

### <a name="what-about-license-servers-outside-media-services"></a>Was gilt für Lizenzserver außerhalb von Media Services?

In vielen Fällen bestehen bei Kunden Investitionen in eine Lizenzserverfarm, die sich in ihrem eigenen Rechenzentrum befindet oder von DRM-Dienstanbietern gehostet wird. Mit dem Inhaltsschutz von Media Services können Sie im Hybridmodus arbeiten. Inhalte können in Media Services gehostet und dynamisch geschützt werden, während DRM-Lizenzen von Servern außerhalb von Media Services bereitgestellt werden. Berücksichtigen Sie in diesem Fall die folgenden Änderungen:

* Der Sicherheitstokendienst (Security Token Service, STS) muss Token ausstellen, die zulässig sind und von der Lizenzserverfarm überprüft werden können. Beispielsweise erfordern die von Axinom bereitgestellten Widevine-Lizenzserver ein spezifisches JWT, das eine Berechtigungsnachricht enthält. Sie benötigen einen Sicherheitstokendienst, um ein solches JWT auszustellen. 
* Sie müssen den Lizenzbereitstellungsdienst nicht mehr in Media Services konfigurieren. Sie müssen die Lizenzerwerbs-URLs (für PlayReady, Widevine und FairPlay) bei der Konfiguration von `ContentKeyPolicy` angeben.

> [!NOTE]
> Widevine ist ein von Google bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google unterliegt.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 und v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kann ich v3-Ressourcen im Azure-Portal verwalten?

Derzeit können Sie das [Azure-Portal](https://portal.azure.com/) für Folgendes verwenden:

* Verwalten von [Liveereignissen](live-events-outputs-concept.md) in Media Services v3 
* Anzeigen (nicht Verwalten) von [Medienobjekten](assets-concept.md) der Version 3 
* [Abrufen von Informationen zum Zugriff auf APIs](./access-api-howto.md) 

Verwenden Sie für alle anderen Verwaltungsaufgaben (z. B. [Transformationen und Aufträge](transforms-jobs-concept.md) und [Inhaltsschutz](content-protection-overview.md)) die [REST-API](/rest/api/media/), die [Azure-Befehlszeilenschnittstelle](/cli/azure/ams) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Gibt es in v3 ein AssetFile-Konzept?

Das `AssetFile`-Konzept wurde aus der Media Services-API entfernt, um die Abhängigkeit von Media Services vom Storage SDK zu beseitigen. Nun sind die Informationen des Storage SDK in Azure Storage enthalten und nicht mehr in Media Services. 

Weitere Informationen finden Sie unter [Migration zu Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Wo ist die clientseitige Speicherverschlüsselung geblieben?

Es wird jetzt wird empfohlen, die serverseitige Speicherverschlüsselung (die standardmäßig aktiviert ist) zu verwenden. Weitere Informationen finden Sie unter [Azure-Speicherdienstverschlüsselung für ruhende Daten](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Offlinestreaming

### <a name="fairplay-streaming-for-ios"></a>FairPlay-Streaming für iOS

Die folgenden häufig gestellten Fragen helfen bei der Problembehandlung für Offlinestreaming für iOS mit FairPlay.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Warum wird im Offlinemodus nur Audio, aber kein Video wiedergegeben?

Dieses Verhalten scheint durch den Entwurf der Beispielanwendung bedingt zu sein. Wenn ein alternativer Audiotitel vorhanden ist (was bei HLS der Fall ist), werden sowohl iOS 10 als auch iOS 11 im Offlinemodus standardmäßig den alternativen Audiotitel verwenden. Um dieses Verhalten für den FPS-Offlinemodus zu kompensieren, entfernen Sie den alternativen Audiotitel aus dem Datenstrom. Um dies für Media Services zu erreichen, fügen Sie den dynamischen Manifestfilter **audio-only=false** hinzu. Eine HLS-URL endet somit mit **.ism/manifest(format=m3u8-aapl,audio-only=false)** . 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Warum wird im Offlinemodus nach dem Hinzufügen von „audio-only=false“ weiterhin nur Audio ohne Video wiedergegeben?

Je nach Cacheschlüsseldesign für das Content Delivery Network wird der Inhalt möglicherweise zwischengespeichert. Löschen Sie den Cacheinhalt.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Wird der FPS-Offlinemodus zusätzlich zu iOS 10 auch unter iOS 11 unterstützt?

Ja. Der FPS-Offlinemodus wird für iOS 10 und iOS 11 unterstützt.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Warum kann ich das Dokument zur Offlinewiedergabe mit FairPlay Streaming und HTTP Live Streaming nicht im FPS Server SDK finden?

Seit FPS Server SDK Version 4 wurde dieses Dokument in den FairPlay Streaming-Programmierleitfaden integriert.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Wie sieht die Struktur der heruntergeladenen bzw. Offlinedateien auf iOS-Geräten aus?

Die heruntergeladene Dateistruktur auf einem iOS-Gerät sieht aus wie auf dem folgenden Screenshot. Der Ordner `_keys` speichert heruntergeladene FPS-Lizenzen, wobei eine Speicherdatei für jeden Lizenzdiensthost verwendet wird. Der Ordner `.movpkg` speichert Audio- und Videoinhalte. 

Der erste Ordner mit einem Namen, der mit einem Bindestrich gefolgt von einer Zahl endet, weist Videoinhalte auf. Der numerische Wert ist die Spitzenbandbreite der Videowiedergabe. Der zweite Ordner mit einem Namen, der mit einem Bindestrich endet, auf den „0“ folgt, enthält Audioinhalte. Der dritte Ordner namens `Data` enthält die Masterwiedergabeliste des FPS-Inhalts. Schließlich enthält „boot.xml“ eine vollständige Beschreibung des Inhalts des Ordners `.movpkg`. 

![Offlinedateistruktur der iOS-Beispielanwendung für FairPlay](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Beispiel für die Datei „boot.xml“:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine-Streaming für Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Wie kann ich für einige Clients/Benutzer persistente (offlinefähige) Lizenzen und für andere nicht persistente (nicht offlinefähige) Lizenzen übermitteln? Muss ich den Inhalt duplizieren und separate symmetrische Schlüssel verwenden?

Da Media Services v3 ein Medienobjekt mit mehreren `StreamingLocator`-Instanzen zulässt, können folgende Elemente zur gleichen Zeit vorhanden sein:

* Eine `ContentKeyPolicy`-Instanz mit `license_type = "persistent"` und `ContentKeyPolicyRestriction` mit Anspruch auf `"persistent"` und der zugehörige `StreamingLocator`.
* Eine weitere `ContentKeyPolicy`-Instanz mit `license_type="nonpersistent"` und `ContentKeyPolicyRestriction` mit Anspruch auf `"nonpersistent` und der zugehörige `StreamingLocator`.
* Zwei `StreamingLocator`-Instanzen mit unterschiedlichen `ContentKey`-Werten.

Abhängig von der Geschäftslogik des benutzerdefinierten STS werden unterschiedliche Ansprüche im JWT-Token ausgegeben. Mit dem Token kann nur die dazugehörige Lizenz abgerufen und nur die entsprechende URL wiedergegeben werden.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Wie sieht die Zuordnung zwischen den Sicherheitsstufen von "Widevine" und "Media Services DRM" aus?

In der Widevine DRM-Architekturübersicht von Google sind drei verschiedene Sicherheitsstufen definiert. In der [Azure Media Services-Dokumentation zur Widevine-Lizenzvorlage](widevine-license-template-overview.md) werden jedoch fünf Sicherheitsstufen (Clientstabilitätsanforderungen für die Wiedergabe) beschrieben. In diesem Abschnitt wird die Zuordnung der Sicherheitsstufen erläutert.

Beide Gruppen von Sicherheitsstufen werden durch Google Widevine definiert. Der Unterschied besteht in der Nutzungsebene: Architektur oder API. Die fünf Sicherheitsstufen werden in der Widevine-API verwendet. Das `content_key_specs`-Objekt, das `security_level` enthält, wird deserialisiert und durch den Widevine-Lizenzdienst von Azure Media Services an den globalen Widevine-Übermittlungsdienst übergeben. Die folgende Tabelle zeigt die Zuordnung zwischen den beiden Gruppen von Sicherheitsstufen.

| **In der Widevine-Architektur definierte Sicherheitsstufen** |**In der Widevine-API verwendete Sicherheitsstufen**|
|---|---| 
| **Sicherheitsstufe 1** : Die gesamte Inhaltsverarbeitung, Kryptografie und Kontrolle findet innerhalb der vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE) statt. In einigen Implementierungsmodellen findet die Sicherheitsverarbeitung unter Umständen in unterschiedlichen Chips statt.|**security_level=5** : Kryptografie, Decodierung und Verarbeitung von Medien (komprimiert und unkomprimiert) müssen innerhalb einer hardwaregestützten TEE stattfinden.<br/><br/>**security_level=4** : Kryptografie und Decodierung müssen innerhalb einer hardwaregestützten TEE durchgeführt werden.|
**Sicherheitsstufe 2** : Die Kryptografie (aber nicht die Videoverarbeitung) wird innerhalb der TEE durchgeführt. Entschlüsselte Puffer werden an die Anwendungsdomäne zurückgegeben und über separate Videohardware oder -software verarbeitet. Auf der zweiten Stufe werden Kryptografieinformationen allerdings weiterhin nur innerhalb der TEE verarbeitet.| **security_level=3** : Die zentralen Vorgänge für Daten und Verschlüsselung müssen innerhalb einer hardwaregestützten TEE ausgeführt werden. |
| **Sicherheitsstufe 3** : Es gibt keine TEE auf dem Gerät. Zum Schutz der kryptografischen Informationen und entschlüsselten Inhalte können geeignete Maßnahmen unter dem Hostbetriebssystem ergriffen werden. Eine Implementierung der dritten Stufe kann auch ein hardwarebasiertes Kryptografiemodul enthalten. Dadurch wird aber nur die Leistung verbessert, nicht die Sicherheit. | **security_level=2** : Erfordert Softwareverschlüsselung und einen verborgenen Decoder.<br/><br/>**security_level=1** : Erfordert softwarebasierte White-Box-Verschlüsselung.|

#### <a name="why-does-content-download-take-so-long"></a>Warum dauert das Herunterladen des Inhalts so lange?

Die Downloadgeschwindigkeit lässt sich auf zwei Arten verbessern:

* Aktivieren Sie ein Content Delivery Network, sodass Endbenutzer den Inhalt wahrscheinlich nicht über den Ursprung/Streamingendpunkt des Inhalts herunterladen, sondern eher über das CDN. Wenn ein Benutzer auf einen Streamingendpunkt zugreift, wird jedes HLS-Segment oder DASH-Fragment dynamisch gepackt und verschlüsselt. Dies führt bei den einzelnen Segmenten oder Fragmenten zwar nur zu einer Latenz im Millisekundenbereich, bei einem Video mit einer Dauer von einer Stunde kann die kumulierte Latenz jedoch zu einer längeren Downloadzeit führen.
* Geben Sie Benutzern die Möglichkeit, anstelle des gesamten Inhalts nur bestimmte Videoqualitätsstufen und Audiospuren herunterzuladen. Es macht im Offlinemodus keinen Sinn, alle Qualitätsstufen herunterzuladen. Es gibt zwei Möglichkeiten, dies zu erreichen:

  * Durch den Client gesteuert: Die herunterzuladende Videoqualitätsstufe und die Audiospuren werden automatisch durch die Player-App oder vom Benutzer ausgewählt.
  * Durch den Dienst gesteuert: In Azure Media Services kann mithilfe des Features für ein dynamisches Manifest ein (globaler) Filter erstellt werden, der die HLS-Wiedergabeliste oder DASH MPD auf eine einzelne Videoqualitätsstufe und bestimmte Audiospuren beschränkt. Dieser Filter ist dann in der Download-URL enthalten, die den Benutzern angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

[Media Services v3 – Übersicht](media-services-overview.md)