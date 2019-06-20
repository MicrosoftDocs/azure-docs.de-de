---
title: Media Services – Anmerkungen zu dieser Version | Microsoft Docs
description: Versionsanmerkungen zu Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c519cf88f40928bbd556b0accfa30d9f8c5c0f11
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991916"
---
# <a name="azure-media-services-release-notes"></a>Versionsanmerkungen zu Azure Media Services

Diese Versionsanmerkungen zu Azure Media Services bieten eine Übersicht über Änderungen im Vergleich zu früheren Versionen und bekannte Probleme.

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Wir wünschen uns Feedback von unseren Kunden, damit wir Probleme beheben können, die negative Auswirkungen auf Ihre Arbeit haben. Wenn Sie ein Problem melden oder Fragen stellen möchten, verfassen Sie einen Beitrag im [MSDN-Forum für Azure Media Services]. 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Aktuell bekannte Probleme
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Allgemeine Probleme von Media Services

| Problem | BESCHREIBUNG |
| --- | --- |
| Mehrere gängige HTTP-Header werden in der REST-API nicht bereitgestellt. |Wenn Sie Media Services-Anwendungen mithilfe der REST-API entwickeln, werden Sie bemerken, dass einige gängige HTTP-Headerfelder (einschließlich CLIENT-REQUEST-ID, REQUEST-ID und RETURN-CLIENT-REQUEST-ID) nicht unterstützt werden. Diese Header werden in einem späteren Update hinzugefügt. |
| Prozentcodierung ist nicht zulässig. |Media Services verwendet den Wert der IAssetFile.Name-Eigenschaft beim Erstellen von URLs für den Streaminginhalt (z.B. `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Aus diesem Grund ist die Prozentcodierung nicht zulässig. Der Wert der Eigenschaft „Name“ darf keines der folgenden [für die Prozentcodierung reservierten Zeichen](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) enthalten: !*'();:@&=+$,/?%#[]". Darüber hinaus wird für die Dateinamenerweiterung nur ein Punkt (.) unterstützt. |
| Die ListBlobs-Methode, die Teil des Azure Storage-SDK Version 3.x ist, schlägt fehl. |Media Services generiert SAS-URLs auf Grundlage der Version [12.02.2012](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) . Falls Sie das Storage SDK zur Auflistung von Blobs in einem Blobcontainer nutzen möchten, verwenden Sie die [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)-Methode, die Teil des Storage SDK Version 2.x ist. |
| Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die übermäßig viele Anforderungen an den Dienst senden. Der Dienst kann den HTTP-Statuscode 503, „Dienst nicht verfügbar“, zurückgeben. |Weitere Informationen finden Sie in der Beschreibung des HTTP-Statuscodes 503 unter [Media Services-Fehlercodes](media-services-encoding-error-codes.md). |
| Beim Abfragen von Entitäten werden maximal 1.000 Entitäten gleichzeitig zurückgegeben, weil die öffentliche REST-Version 2 Abfrageergebnisse auf 1.000 Ergebnisse begrenzt. |Verwenden Sie „Skip“ und „Take“ (.NET) bzw. „top“ (REST), wie in [diesem .NET-Beispiel](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) und [diesem REST-API-Beispiel](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) beschrieben. |
| Bei einigen Clients kann im Smooth Streaming-Manifest ein Problem mit einem Wiederholungstag auftreten. |Weitere Informationen finden Sie in [diesem Abschnitt](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-Objekte können nicht serialisiert werden und funktionieren daher nicht mit Azure Cache for Redis. |Wenn Sie versuchen, das SDK-Objekt „AssetCollection“ zu serialisieren, um es Azure Cache for Redis hinzuzufügen, wird eine Ausnahme ausgelöst. |

## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>REST-API-Versionsverlauf
Informationen zum Versionsverlauf der Media Services-REST-API finden Sie unter [Azure Media Services – REST-API-Referenz].

## <a name="march-2019"></a>März 2019

Das Azure Media Services-Feature „Media Hyperlapse-Vorschau“ wurde eingestellt.

## <a name="december-2018"></a>Dezember 2018

Das Azure Media Services-Feature „Media Hyperlapse-Vorschau“ wird in Kürze eingestellt. Ab dem 19. Dezember 2018 erfolgen in Media Services keine Änderungen oder Verbesserungen von Media Hyperlapse mehr. Am 29. März 2019 wird das Feature eingestellt und ist dann nicht mehr verfügbar.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>CMAF-Unterstützung

CMAF- und „cbcs“-Verschlüsselungsunterstützung für Apple HLS- (iOS 11 und höher) und MPEG-DASH-Player, die CMAF unterstützen.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT-Miniaturbild-Sprites

Sie können jetzt mit Media Services Web VTT-Miniaturbild-Sprites mit unseren APIs der Version 2 zu erstellen. Weitere Informationen finden Sie unter [Generieren eines Miniaturbild-Sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Juli 2018

Beim letzten Service Release gab es geringfügige Änderungen der Formatierung von Fehlermeldungen, die vom Dienst zurückgegeben werden, wenn bei einem Auftrag ein Fehler auftritt, bezüglich des Umbruchs in zwei oder mehr Zeilen.

## <a name="may-2018"></a>Mai 2018 

Ab dem 12. Mai 2018 unterstützen Livekanäle nicht mehr das Erfassungsprotokoll zum RTP/MPEG-2-Transportdatenstrom. Migrieren Sie von Erfassungsprotokollen für RTP/MPEG-2 zu Erfassungsprotokollen für RTMP oder fragmentiertem MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Release von Oktober 2017
> [!IMPORTANT] 
> Für Media Services werden Azure Access Control Service-Authentifizierungsschlüssel in Zukunft nicht mehr unterstützt. Ab 22. Juni 2018 können Sie sich beim Media Services-Back-End nicht mehr über den Code durch Verwendung von Access Control Service-Schlüsseln authentifizieren. Sie müssen Ihren Code für die Verwendung von Azure Active Directory (Azure AD) gemäß den Informationen unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md) aktualisieren. Achten Sie im Azure-Portal auf Warnhinweise zu dieser Änderung.

### <a name="updates-for-october-2017"></a>Updates für Oktober 2017
#### <a name="sdks"></a>SDKs
* Das .NET SDK wurde aktualisiert und unterstützt jetzt die Azure AD-Authentifizierung. Die Unterstützung für die Access Control Service-Authentifizierung wurde aus dem aktuellen .NET SDK auf Nuget.org entfernt, um die Migration zu Azure AD zu beschleunigen. 
* Das Java SDK wurde aktualisiert und unterstützt jetzt die Azure AD-Authentifizierung. Dem Java SDK wurde die Unterstützung für die Azure AD-Authentifizierung hinzugefügt. Weitere Informationen zum Verwenden des Java SDK mit Media Services finden Sie unter [Erste Schritte mit dem Java-Client-SDK für Azure Media Services](media-services-java-how-to-use.md).

#### <a name="file-based-encoding"></a>Dateibasierte Codierung
* Sie können jetzt den Premium-Encoder verwenden, um Ihre Inhalte mit dem HEVC-Videocodec H.265 (High-Efficiency Video Coding) zu codieren. Die Auswahl von H.265 gegenüber anderen Codecs wie H.264 hat keine Auswirkungen auf den Preis. Informationen zu HEVC-Patentlizenzen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).
* Bei Quellvideos, die mit dem H.265-Videocodec (HEVC) codiert sind, z.B. mit iOS11 oder GoPro Hero 6 aufgezeichnete Videoaufnahmen, können Sie jetzt entweder den Premium-Encoder oder den Standard-Encoder zum Codieren verwenden. Informationen zu Patentlizenzen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).
* Bei Inhalten, die mehrsprachige Audiospuren enthalten, müssen die Werte für die Sprachen gemäß den entsprechenden Dateiformatspezifikationen (z.B. ISO MP4) ordnungsgemäß bezeichnet sein. Dann können Sie den Standard-Encoder verwenden, um die Inhalte für das Streaming zu codieren. Der resultierende Streaminglocator listet die verfügbaren Audiosprachen auf.
* Der Standard-Encoder unterstützt jetzt zwei neue reine Audiosystemvoreinstellungen: „AAC-Audio“ und „AAC gute Audioqualität“. Beide Einstellungen produzieren eine Stereoausgabe mit erweiterter Audiocodierung (Advanced Audio Coding, AAC) mit Bitraten von 128 KBit/s bzw. 192 KBit/s.
* Der Premium-Encoder unterstützt jetzt QuickTime-/MOV-Dateiformate als Eingabe. Beim Videocodec muss es sich um einen der [in diesem GitHub-Artikel aufgeführten Apple ProRes-Typen](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats) handeln. Das Audiosystem muss entweder AAC oder Pulse Code Modulation (PCM) sein. Der Premium-Encoder unterstützt z.B. DVC-/DVCPro-Videos in QuickTime-/MOV-Dateien nicht als Eingabe. Der Standard-Encoder unterstützt diese Videocodecs.
* In den Encodern wurden folgende Fehlerbehebungen vorgenommen:

    * Sie können Aufträge jetzt unter Verwendung eines Eingabemedienobjekts senden. Nach Abschluss dieser Aufträge können Sie das Medienobjekt bearbeiten (Sie können beispielsweise Dateien im Medienobjekt hinzufügen, löschen oder umbenennen) und zusätzliche Aufträge senden.
    * Die Qualität der vom Standard-Encoder erzeugten JPEG-Miniaturansichten wurde verbessert.
    * Der Standard-Encoder bietet eine bessere Verarbeitung von Eingabemetadaten und Erzeugung von Miniaturansichten in Videos mit sehr kurzer Laufzeit.
    * Verbesserungen des im Standard-Encoder verwendeten H.264-Decoders eliminieren bestimmte seltene Artefakte. 

#### <a name="media-analytics"></a>Media Analytics
Allgemeine Verfügbarkeit von Azure Media Redactor: Dieser Medienprozessor ermöglicht die Anonymisierung von Inhalten, indem die Gesichter von bestimmten Personen unscharf gemacht werden – ideal für den Einsatz in den Bereichen öffentliche Sicherheit und Nachrichtenmedien. 

Eine Übersicht über diesen neuen Prozessor finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/azure-media-redactor/). Informationen zu Dokumentation und Einstellungen finden Sie unter [Bearbeiten von Gesichtern mit Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Release von Juni 2017

Media Services unterstützt jetzt die [Azure AD-basierte Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Derzeit unterstützt Media Services das Azure Access Control Service-Authentifizierungsmodell. Die Access Control Service-Authentifizierung ist ab 1. Juni 2018 veraltet. Es wird empfohlen, möglichst bald zum Azure AD-Authentifizierungsmodell zu migrieren.

## <a name="march-2017-release"></a>Release von März 2017

Sie können jetzt den Standard-Encoder zum [automatischen Generieren einer Reihe von Bitraten](media-services-autogen-bitrate-ladder-with-mes.md) verwenden, indem Sie beim Erstellen einer Codierungsaufgabe die Voreinstellung „Adaptives Streaming“ angeben. Verwenden Sie die Voreinstellung „Adaptives Streaming“, um ein Video für das Streamen mit Media Services zu codieren. Um eine Codierungsvoreinstellung für Ihr bestimmtes Szenario anzupassen, können Sie mit [diesen Voreinstellungen](media-services-mes-presets-overview.md) beginnen.

Sie können jetzt Media Encoder Standard Workflow oder Media Encoder Premium Workflow verwenden, um [eine Codierungsaufgabe zu erstellen, die fMP4-Blöcke generiert](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Release von Februar 2017

Ab 1. April 2017 werden alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, sowie alle zugehörigen Aufgabendatensätze automatisch gelöscht. Die Löschung erfolgt auch dann, wenn die Gesamtanzahl von Datensätzen unter dem Kontingentlimit liegt. Um die Auftrags- und Aufgabeninformationen zu archivieren, können Sie den unter [Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK](media-services-dotnet-manage-entities.md) beschriebenen Code verwenden.

## <a name="january-2017-release"></a>Release von Januar 2017

In Media Services stellt ein Streamingendpunkt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt an eine Clientplayeranwendung oder ein Content Delivery Network (CDN) übermitteln kann. Media Services bietet auch eine nahtlose Integration in Azure Content Delivery Network. Der ausgehende Stream eines StreamingEndpoint-Diensts kann ein Livestream, ein Video on Demand oder ein progressiver Download Ihres Medienobjekts in Ihrem Media Services-Konto sein. Jedes Media Services-Konto enthält einen standardmäßigen Streamingendpunkt. Im Konto können zusätzliche Streamingendpunkte erstellt werden. 

Es gibt zwei Versionen von Streamingendpunkten: 1.0 und 2.0. Ab 10. Januar 2017 enthalten alle neu erstellten Media Services-Konten einen standardmäßigen Streamingendpunkt der Version 2.0. Zusätzliche Streamingendpunkte, die Sie diesem Konto hinzufügen, weisen ebenfalls Version 2.0 auf. Diese Änderung wirkt sich nicht auf vorhandene Konten aus. Vorhandene Streamingendpunkte weisen Version 1.0 auf und können auf Version 2.0 aktualisiert werden. Diese Änderung zieht Veränderungen bei Verhalten, Abrechnung und Features nach sich. Weitere Informationen finden Sie unter [Übersicht über Streamingendpunkte](media-services-streaming-endpoints-overview.md).

Ab Version 2.15 fügt Media Services der Streamingendpunktentität folgende Eigenschaften hinzu:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Weitere Informationen zu diesen Eigenschaften finden Sie unter [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Release von Dezember 2016

 Sie können Media Services jetzt verwenden, um auf Telemetrie- und Metrikdaten für die zugehörigen Dienste zuzugreifen. Mit der aktuellen Version von Media Services können Sie Telemetriedaten für Liveentitäten für Kanäle, Streamingendpunkte und Archive sammeln. Weitere Informationen finden Sie unter [Media Services-Telemetrie](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Release von Juli 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Updates der durch Codierungsaufgaben generierten Manifestdatei (*.ism)
Wenn eine Codierungsaufgabe an Media Encoder Standard oder Media Encoder Premium gesendet wird, erstellt die Aufgabe eine [Streamingmanifestdatei](media-services-deliver-content-overview.md) (*.ism) im Ausgabemedienobjekt. Im letzten Dienstrelease wurde die Syntax dieser Streamingmanifestdatei aktualisiert.

> [!NOTE]
> Die Syntax der Streamingmanifestdatei (.ism) ist für die interne Verwendung reserviert. Dies soll in zukünftigen Releases geändert werden. Ändern oder bearbeiten Sie den Inhalt dieser Datei nicht.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Im Ausgabemedienobjekt wird eine neue Clientmanifestdatei (*.ismc) generiert, wenn eine Codierungsaufgabe mindestens eine MP4-Datei ausgibt
Seit dem letzten Dienstrelease enthält das Ausgabemedienobjekt nach Abschluss einer Codierungsaufgabe, die mindestens eine MP4-Datei generiert, darüber hinaus eine Streamingclient-Manifestdatei (*.ismc). Die ISMC-Datei trägt zur Verbesserung des dynamischen Streamings bei. 

> [!NOTE]
> Die Syntax der Clientmanifestdatei (.ismc) ist für die interne Verwendung reserviert. Dies soll in zukünftigen Releases geändert werden. Ändern oder bearbeiten Sie den Inhalt dieser Datei nicht.
> 
> 

Weitere Informationen finden Sie in [diesem Blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Bekannte Probleme
Bei einigen Clients kann im Smooth Streaming-Manifest ein Problem mit einem Wiederholungstag auftreten. Weitere Informationen finden Sie in [diesem Abschnitt](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Release von April 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services bietet Media Analytics für leistungsstarke Funktionen zur Gewinnung von Erkenntnissen aus Videos. Weitere Informationen finden Sie unter [Media Analytics auf der Media Services-Plattform](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Vorschau)
Mit Media Services können Sie jetzt Ihre HLS-Inhalte (HTTP Live Streaming) mit Apple FairPlay dynamisch verschlüsseln. Sie können auch den Lizenzbereitstellungsdienst von Media Services verwenden, um FairPlay-Lizenzen an Clients zu übermitteln. Weitere Informationen finden Sie unter „Verwenden von Azure Media Services zum Streamen von durch Apple FairPlay geschützten HLS-Inhalten“.

## <a id="feb_changes16"></a>Release von Februar 2016
Die neueste Version des Media Services SDK für .NET (3.5.3) enthält eine Fehlerbehebung für Google Widevine. AssetDeliveryPolicy konnte nicht für mehrere mit Widevine verschlüsselte Medienobjekte wiederverwendet werden. Im Rahmen dieser Fehlerbehebung wurde dem SDK folgende Eigenschaft hinzugefügt: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Release von Januar 2016
Reservierte Einheiten für die Codierung wurden umbenannt, um Verwechslungen mit Encoder-Namen zu vermeiden.

Die reservierten Einheiten für die Codierung in den Tarifen „Basic“, „Standard“ und „Premium“ wurden in die reservierten Einheiten „S1“, „S2“ bzw. „S3“ umbenannt. Kunden, die reservierte Einheiten für die Codierung im Tarif „Basic“ verwenden, sehen heute „S1“ als Bezeichnung im Azure-Portal (und auf der Abrechnung). Kunden mit den Tarifen „Standard“ bzw. „Premium“ sehen die Bezeichnungen „S2“ bzw. „S3“. 

## <a id="dec_changes_15"></a>Release von Dezember 2015

### <a name="media-encoder-deprecation-announcement"></a>Ankündigung zur Einstellung von Media Encoder

 Media Encoder wird ungefähr 12 Monate nach der Veröffentlichung von Media Encoder Standard eingestellt.

### <a name="azure-sdk-for-php"></a>Azure SDK für PHP
Das Azure SDK-Team hat eine neue Version des [Azure SDK für PHP](https://github.com/Azure/azure-sdk-for-php)-Pakets veröffentlicht, das Updates und neue Features für Media Services enthält. Insbesondere unterstützt das Media Services SDK für PHP jetzt die neuesten Features für den [Inhaltsschutz](media-services-content-protection-overview.md). Hierbei handelt es sich um die dynamische Verschlüsselung mit AES und DRM (PlayReady und Widevine) mit und ohne Tokeneinschränkungen. Unterstützt wird auch die Skalierung von [Codierungseinheiten](media-services-dotnet-encoding-units.md).

Weitere Informationen finden Sie unter

* Die folgenden [Codebeispiele](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) erleichtern Ihnen den schnellen Einstieg:
  * **vodworkflow_aes.php**: Diese PHP-Datei veranschaulicht die Verwendung der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts. Sie basiert auf dem .NET-Beispiel, das unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselbereitstellungsdiensts](media-services-protect-with-aes128.md) erläutert wird.
  * **vodworkflow_aes.php**: Diese PHP-Datei veranschaulicht die Verwendung der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts. Sie basiert auf dem .NET-Beispiel, das unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md) erläutert wird.
  * **scale_encoding_units.php**: Diese PHP-Datei veranschaulicht das Skalieren von reservierten Einheiten für die Codierung.

## <a id="nov_changes_15"></a>Release von November 2015
 Media Services bietet jetzt den Widevine-Dienst zur Lizenzübermittlung in der Cloud an. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Weitere Informationen finden Sie auch in [diesem Tutorial](media-services-protect-with-playready-widevine.md) und im [GitHub-Repository](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevine-Lizenzübermittlungsdienste über Media Services befinden sich in der Vorschauphase. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Release von Oktober 2015
Media Services ist jetzt in den folgenden Datencentern live: Brasilien, Süden, Indien, Westen, Indien, Süden und Indien, Mitte. Über das Azure-Portal können Sie jetzt [Media Service-Konten erstellen](media-services-portal-create-account.md) und verschiedene auf der [Dokumentationswebseite für Media Services](https://azure.microsoft.com/documentation/services/media-services/) beschriebene Aufgaben ausführen. Live Encoding ist in diesen Rechenzentren nicht aktiviert. Darüber hinaus sind nicht alle Arten von für die Codierung reservierten Einheiten in diesen Rechenzentren verfügbar.

* Brasilien, Süden:                                          Reservierte Einheiten für die Codierung stehen nur im Standard- und Basic-Tarif zur Verfügung.
* Indien, Westen, Indien, Süden und Indien, Mitte:             Reservierte Einheiten für die Codierung stehen nur im Basic-Tarif zur Verfügung.

## <a id="september_changes_15"></a>Release von September 2015
Media Services bietet jetzt die Möglichkeit zum Schutz von Video on Demand-Streams und Livestreams mit der modularen DRM-Technologie Widevine. Folgende Partner für Bereitstellungsdienste können Sie bei der Übermittlung von Widevine-Lizenzen unterstützen:
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Sie können das [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (ab Version 3.5.1) oder die REST-API verwenden, um AssetDeliveryConfiguration für die Verwendung von Widevine zu konfigurieren. 
* Media Services bietet jetzt Unterstützung für Apple ProRes-Videos. Sie können jetzt die QuickTime-Quellvideodateien hochladen, die Apple ProRes oder andere Codecs verwenden. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Sie können jetzt mit Media Encoder Standard Subclipping und Livearchivextraktion ausführen. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Die folgenden Filteraktualisierungen wurden vorgenommen: 
  
  * Sie können jetzt das Apple-HLS-Format mit einem Nur-Audio-Filter verwenden. Mit diesem Update können Sie eine reine Audiospur durch Angabe von (audio-only= false) in der URL entfernen.
  * Beim Definieren von Filtern für Ihre Medienobjekte können jetzt mehrere Filter (bis zu drei) in einer einzelnen URL kombinieren.
    
    Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services unterstützt jetzt I-Frames in HLS, Version 4. Die I-Frame-Unterstützung optimiert Vorlauf- und Rücklaufvorgänge. Standardmäßig enthalten alle Ausgaben von HLS Version 4 die I-Frame-Wiedergabeliste (EXT-X-I-FRAME-STREAM-INF).
Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Release von August 2015
* Das Media Services-SDK für Java Version 0.8.0 und neue Beispiele sind jetzt verfügbar. Weitere Informationen finden Sie unter
    
* Der Azure Media Player wurde mit Unterstützung für mehrere Audiostreams aktualisiert. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Release von Juli 2015
* Die allgemeine Verfügbarkeit von Media Encoder Standard wurde angekündigt. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard verwendet die in [diesem Abschnitt](https://go.microsoft.com/fwlink/?LinkId=618336) beschriebenen Voreinstellungen. Beim Verwenden einer Voreinstellung für 4K-Codierungen erhalten Sie reservierte Einheiten vom Typ „Premium“. Weitere Informationen finden Sie unter [Skalieren der Codierung](media-services-scale-media-processing-overview.md).
* Mit Media Services und dem Media Player wurden Liveuntertitel in Echtzeit verwendet. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Updates für das Media Services .NET SDK
Das Media Services .NET SDK liegt jetzt in der Version 3.4.0.0 vor. Die folgenden Updates wurden vorgenommen: 

* Die Unterstützung für das Livearchiv wurde implementiert. Sie können ein Medienobjekt, das ein Livearchiv enthält, nicht herunterladen.
* Die Unterstützung für dynamische Filter wurde implementiert.
* Es wurden Funktionen implementiert, mit denen Benutzer einen Speichercontainer beibehalten können, wenn ein Medienobjekt gelöscht wird.
* Es wurden Fehlerbehebungen im Zusammenhang mit Wiederholungsrichtlinien in Kanälen vorgenommen.
* Media Encoder Premium Workflow wurde aktiviert.

## <a id="june_changes_15"></a>Release von Juni 2015
### <a name="media-services-net-sdk-updates"></a>Updates für das Media Services .NET SDK
Das Media Services .NET SDK liegt jetzt in der Version 3.3.0.0 vor. Die folgenden Updates wurden vorgenommen: 

* Die Unterstützung für die Ermittlungsspezifikation von OpenID Connect wurde hinzugefügt.
* Die Unterstützung für die Verarbeitung des Schlüsselrollovers seitens des Identitätsanbieters wurde hinzugefügt.

Wenn Sie einen Identitätsanbieter verwenden, der ein OpenID Connect-Ermittlungsdokument verfügbar macht (wie z.B. Azure AD, Google und Salesforce), können Sie Media Services anweisen, Signaturschlüssel zur Überprüfung des JSON-Webtokens (JWT) aus der OpenID Connect-Ermittlungsspezifikation abzurufen. 

Weitere Informationen finden Sie unter [Using Json Web Keys from OpenID Connect discovery spec to work with JWT token authentication in Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/) (Verwenden von JSON-Webschlüsseln aus der OpenID Connect-Ermittlungsspezifikation zur Verwendung mit der JWT-Tokenauthentifizierung in Azure Media Services).

## <a id="may_changes_15"></a>Release von Mai 2015
Folgende neue Funktionen wurden angekündigt:

* [Eine Vorschau der Livecodierung mit Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamisches Manifest](media-services-dynamic-manifest-overview.md)

## <a id="april_changes_15"></a>Release von April 2015
### <a name="general-media-services-updates"></a>Allgemeine Media Services-Updates
* Der [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) wurde angekündigt.
* Beginnend mit Media Services REST 2.10 werden Kanäle, die zur Erfassung eines RTMP (Real-Time Messaging Protocol) konfiguriert sind, mit primären und sekundären Erfassungs-URLs erstellt. Weitere Informationen finden Sie unter [Kanalerfassungskonfigurationen](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer wurde aktualisiert.
* Die Unterstützung für Spanisch wurde hinzugefügt.
* Es wurde eine neue Konfiguration für das XML-Format hinzugefügt.

Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Updates für das Media Services .NET SDK
Das Media Services .NET SDK liegt jetzt in der Version 3.2.0.0 vor. Die folgenden Updates wurden vorgenommen:

* Wichtige Änderung: TokenRestrictionTemplate.Issuer und TokenRestrictionTemplate.Audience sind jetzt Zeichenfolgentypen.
* Die Erstellung von benutzerdefinierten Wiederholungsrichtlinien wurde aktualisiert.
* Im Zusammenhang mit dem Hochladen/Herunterladen von Dateien wurden Fehlerbehebungen vorgenommen.
* Die MediaServicesCredentials-Klasse akzeptiert jetzt primäre und sekundäre Endpunkte für die Zugriffssteuerung zum Authentifizieren.

## <a id="march_changes_15"></a>Release von März 2015
### <a name="general-media-services-updates"></a>Allgemeine Media Services-Updates
* Media Services bietet jetzt die Integration in Content Delivery Network. Zur Unterstützung der Integration wurde die Eigenschaft „CdnEnabled“ zu „StreamingEndpoint“ hinzugefügt. „CdnEnabled“ kann mit REST-APIs ab Version 2.9 verwendet werden. Weitere Informationen finden Sie unter [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). „CdnEnabled“ kann mit dem .NET SDK ab Version 3.1.0.2 verwendet werden. Weitere Informationen finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Media Encoder Premium Workflow wurde angekündigt. Weitere Informationen finden Sie unter [Introducing Premium Encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/) (Einführung in die Premium-Codierung in Azure Media Services).

## <a id="february_changes_15"></a>Release von Februar 2015
### <a name="general-media-services-updates"></a>Allgemeine Media Services-Updates
Die Media Services-REST-API liegt jetzt in der Version 2.9 vor. Ab mit dieser Version können Sie die Content Delivery Network-Integration mit Streamingendpunkten aktivieren. Weitere Informationen finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Release von Januar 2015
### <a name="general-media-services-updates"></a>Allgemeine Media Services-Updates
Die allgemeine Verfügbarkeit des Inhaltschutzes mit dynamischer Verschlüsselung wurde angekündigt. Weitere Informationen finden Sie unter [Azure Media Services enhances streaming security with General Availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/) (Azure Media Services erhöht die Sicherheit beim Streaming mit der allgemeinen Verfügbarkeit der DRM-Technologie).

### <a name="media-services-net-sdk-updates"></a>Updates für das Media Services .NET SDK
Das Media Services .NET SDK liegt jetzt in der Version 3.1.0.1 vor.

Durch diese Version wird der Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate-Standardkonstruktor als veraltet eingestuft. Der neue Konstruktor akzeptiert TokenType als Argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Release von Dezember 2014
### <a name="general-media-services-updates"></a>Allgemeine Media Services-Updates
* Dem Media Indexer wurden einige Updates und neue Funktionen hinzugefügt. Weitere Informationen finden Sie unter [Azure Media Indexer Version 1.1.6.7 Release Notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/) (Versionsanmerkungen zu Azure Media Indexer Version 1.1.6.7).
* Es wurde eine neue REST-API hinzugefügt, mit der Sie reservierte Einheiten für die Codierung aktualisieren können. Weitere Informationen finden Sie unter [EncodingReservedUnitType](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Die CORS-Unterstützung für den Schlüsselübermittlungsdienst wurde hinzugefügt.
* Für die Abfrage von Autorisierungsrichtlinienoptionen wurden Leistungsverbesserungen vorgenommen.
* Im Rechenzentrum „China“ gilt die [Schlüsselübermittlungs-URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) jetzt pro Kunde (genau wie in anderen Rechenzentren).
* Die automatische HLS-Zieldauer wurde hinzugefügt. Bei Livestreaming wird HLS immer dynamisch verpackt. Standardmäßig berechnet Media Services das Verhältnis für die HLS-Segmentpaketerstellung (FragmentsPerSegment) basierend auf dem Keyframe-Intervall (KeyFrameInterval). Diese Methode wird auch als Gruppe von Bildern (Group Of Pictures, GOP) bezeichnet, die vom Liveencoder empfangen werden. Weitere Informationen finden Sie unter [Übersicht über Livestreaming mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Updates für das Media Services .NET SDK
Das [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) liegt jetzt in der Version 3.1.0.0 vor. Die folgenden Updates wurden vorgenommen:

* Die .NET SDK-Abhängigkeit wurde auf das .NET 4.5-Framework aktualisiert.
* Es wurde eine neue API hinzugefügt, mit der Sie reservierte Einheiten für die Codierung aktualisieren können. Weitere Informationen finden Sie unter [Skalieren der Codierung mit .NET SDK](media-services-dotnet-encoding-units.md).
* Die JWT-Unterstützung für die Tokenauthentifizierung wurde hinzugefügt. Weitere Informationen finden Sie unter [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (JWT-Tokenauthentifizierung in Azure Media Services und dynamische Verschlüsselung).
* Relative Offsets für BeginDate und ExpirationDate wurden in der PlayReady-Lizenzvorlage hinzugefügt.

## <a id="november_changes_14"></a>Release von November 2014
* Sie können Media Services jetzt zum Erfassen von Smooth Streaming-Live-Inhalten (fMP4) über eine SSL-Verbindung verwenden. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. Media Services unterstützt derzeit SSL mit benutzerdefinierten Domänen nicht. Weitere Informationen zu Livestreaming finden Sie unter [Übersicht über Livestreaming mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Sie können RTMP-Livestreams derzeit nicht über eine SSL-Verbindung erfassen.
* Sie können nur dann über SSL streamen, wenn der Streamingendpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streamingendpunkten basieren, die nach dem 10. September 2014 erstellt wurden, enthält die URL „streaming.mediaservices.windows.net“ (das neue Format). Streaming-URLs, die „origin.mediaservices.windows.net“ (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, [erstellen Sie einen neuen Streamingendpunkt](media-services-portal-manage-streaming-endpoints.md). Verwenden Sie auf dem neuen Streamingendpunkt basierende URLs, um Ihre Inhalte über SSL zu streamen.

### <a id="oct_sdk"></a>Media Services .NET SDK
Das Media Services SDK für .NET-Erweiterungen liegt jetzt in der Version 2.0.0.3 vor.

Das Media Services SDK für .NET liegt jetzt in der Version 3.0.0.8 vor. Die folgenden Updates wurden vorgenommen:

* In Klassen für Wiederholungsrichtlinien wurde Refactoring implementiert.
* HTTP-Anforderungsheadern wurde eine Benutzer-Agent-Zeichenfolge hinzugefügt.
* Ein NuGet-Build-Wiederherstellungsschritt wurde hinzugefügt.
* Szenariotests zur Verwendung von X.509-Zertifikaten aus dem Repository wurden korrigiert.
* Für Updates von Kanälen und Streamingendpunkten wurden Überprüfungseinstellungen hinzugefügt.

### <a name="new-github-repository-to-host-media-services-samples"></a>Neues GitHub-Repository zum Hosten von Media Services-Beispielen
Die Beispiele befinden sich im [GitHub-Repository für Media Services-Beispiele](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Release von September 2014
Die Media Services-REST-Metadaten liegen jetzt in der Version 2.7 vor. Weitere Informationen zu den neuesten REST-Updates finden Sie unter [Media Services Operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) (Media Services-Vorgänge – REST-API-Referenz).

Das Media Services SDK für .NET liegt jetzt in der Version 3.0.0.7 vor.

### <a id="sept_14_breaking_changes"></a>Änderungen mit Auswirkung auf die Lauffähigkeit
* „Ursprung“ wurde in [StreamingEndpoint] umbenannt.
* Das Standardverhalten bei Verwendung des Azure-Portals zum Codieren und Veröffentlichen von MP4-Dateien wurde geändert.

### <a id="sept_14_GA_changes"></a>Neue Funktionen/Szenarien, die zum Release für die allgemeine Verfügbarkeit gehören
* Der Media Indexer-Medienprozessor wurde eingeführt. Weitere Informationen finden Sie unter [Indizieren von Mediendateien mit Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Mit der [StreamingEndpoint]-Entität können Sie jetzt benutzerdefinierte Domänennamen (Hostnamen) hinzufügen.
  
    Damit ein benutzerdefinierter Domänenname als Name des Media Services-Streamingendpunkts verwendet werden kann, fügen Sie Ihrem Streamingendpunkt benutzerdefinierte Hostnamen hinzu. Verwenden Sie die REST-APIs oder das .NET SDK von Media Services, um benutzerdefinierte Hostnamen hinzuzufügen.
  
    Es gelten die folgenden Bedingungen:
  
  * Sie müssen Eigentümer des benutzerdefinierten Domänennamens sein.
  * Das Eigentum am Domänennamen muss von Media Services überprüft werden. Um die Domäne zu überprüfen, erstellen Sie einen CNAME-Eintrag, der die übergeordnete Domäne „MediaServicesAccountId“ zum Überprüfen von „DNS mediaservices-dns-zone“ zuordnet.
  * Sie müssen einen anderen CNAME-Eintrag erstellen, der den benutzerdefinierten Hostnamen (z.B. „sports.contoso.com“) dem Hostnamen des Media Services-Streamingendpunkts zuordnet (z.B. „amstest.streaming.mediaservices.windows.net“).

    Weitere Informationen finden Sie unter der CustomHostNames-Eigenschaft im Artikel [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

### <a id="sept_14_preview_changes"></a>Neue Funktionen/Szenarien, die Teil der öffentlichen Vorschauversion sind
* Livestreaming (Vorschau). Weitere Informationen finden Sie unter [Übersicht über Livestreaming mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Schlüsselübermittlungsdienst. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselbereitstellungsdiensts](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamische AES-Verschlüsselung. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselbereitstellungsdiensts](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* PlayReady-Lizenzübermittlungsdienst. 
* Dynamische PlayReady-Verschlüsselung. 
* Media Services PlayReady-Lizenzvorlage. Weitere Informationen finden Sie unter [Media Services PlayReady-Lizenzvorlage – Übersicht].
* Streamen von im Speicher verschlüsselten Medienobjekten. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Release von August 2014
Wenn Sie ein Medienobjekt codieren, wird bei Abschluss des Codierungsauftrags ein Ausgabemedienobjekt erstellt. Bis zu diesem Release erstellte der Media Services-Encoder Metadaten zu Ausgabemedienobjekten. Ab diese, Release erstellt der Encoder zudem Metadaten zu Eingabemedienobjekten. Weitere Informationen finden Sie unter [Eingeben von Metadaten] und [Ausgeben von Metadaten].

## <a id="july_changes_14"></a>Release von Juli 2014
Die folgenden Fehlerkorrekturen wurden für Azure Media Services Packager und Encryptor vorgenommen:

* Wenn ein Live-Archivmedienobjekt an HLS übertragen wird, wird nur Audio wiedergegeben: Dieses Problem wurde behoben. Jetzt können Audio und Video wiedergegeben werden.
* Wenn ein Medienobjekt in HLS und AES-128-Bit-Umschlagverschlüsselung gepackt ist, werden die gepackten Streams nicht auf Android-Geräten wiedergegeben: Dieses Problem wurde behoben, und die gepackten Streams können auf Android-Geräten, die HLS unterstützen, wiedergegeben werden.

## <a id="may_changes_14"></a>Release von Mai 2014
### <a id="may_14_changes"></a>Allgemeine Media Services-Updates
Sie können jetzt die [dynamische Paketerstellung] zum Streamen von HLS Version 3 verwenden. Fügen Sie zum Streamen von HLS Version 3 das folgende Format zum Pfad für den Ursprungslocator hinzu: *.ism/manifest(format=m3u8-aapl-v3). Weitere Informationen finden Sie in [diesem Forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Die dynamische Paketerstellung unterstützt jetzt auch die Bereitstellung von mit PlayReady verschlüsseltem HLS (Version 3 und Version 4) basierend auf statisch mit PlayReady verschlüsseltem Smooth Streaming. Informationen zum Verschlüsseln von Smooth Streaming mit PlayReady finden Sie unter [Dynamische Paketerstellung](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-Updates
Das Media Services .NET SDK liegt jetzt in der Version 3.0.0.5 vor. Die folgenden Updates wurden vorgenommen:

* Die Geschwindigkeit und die Stabilität beim Hoch- und Herunterladen von Medienobjekten wurden verbessert.
* Bei der Wiederholungslogik und der Behandlung vorübergehender Ausnahmen wurden Verbesserungen vorgenommen: 
  
  * Die Erkennung vorübergehender Fehler und die Wiederholungslogik wurden für Ausnahmen verbessert, die durch Abfragen, Speichern von Änderungen, Hochladen oder Herunterladen von Dateien verursacht werden. 
  * Wenn Sie Webausnahmen erhalten (z.B. während einer Access Control Service-Tokenanforderung), führen schwerwiegende Fehler jetzt schneller zu einem Abbruch.

Weitere Informationen finden Sie unter [Wiederholungslogik im Media Services SDK für .NET].

## <a id="jan_feb_changes_14"></a>Releases von Januar/Februar 2014
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 und 3.0.0.3
Folgende Änderungen wurden in 3.0.0.1 und 3.0.0.2 vorgenommen:

* Probleme hinsichtlich der Verwendung von LINQ-Abfragen mit OrderBy-Anweisungen wurden behoben.
* Testlösungen in [GitHub] wurden in einheitenbasierte Tests und szenarienbasierte Tests unterteilt.

Weitere Informationen zu den Änderungen finden Sie unter: [Media Services .NET SDK 3.0.0.1 and 3.0.0.2 Releases](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html) (Media Services .NET SDK-Versionen 3.0.0.1 und 3.0.0.2).

Die folgenden Änderungen wurden in Version 3.0.0.3 vorgenommen:

* Die Azure-Speicherabhängigkeiten wurden für die Verwendung von Version 3.0.3.0 aktualisiert.
* Behoben wurde auch ein Problem mit der Abwärtskompatibilität für Versionen 3.0. *.* .

## <a id="december_changes_13"></a>Release von Dezember 2013
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Die Versionen 3.0.x.x sind nicht abwärtskompatibel zu den Versionen 2.4.x.x.
> 
> 

Die neueste Version des Media Services SDK ist 3.0.0.0. Sie können das neueste Paket aus NuGet herunterladen oder die Bits unter [GitHub]abrufen.

Ab Media Services SDK Version 3.0.0.0 können Sie die Token des [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) wiederverwenden. Weitere Informationen finden Sie im Abschnitt „Wiederverwenden von Token des Access Control Service“ unter [Herstellen einer Verbindung mit Media Services mit dem Media Services SDK für .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-Erweiterungen 2.0.0.0
 Die Media Services SDK-Erweiterungen für .NET sind eine Reihe von Erweiterungsmethoden und Hilfsfunktionen, die Ihren Code vereinfachen und eine einfachere Entwicklung mit Media Services ermöglichen. Unter [Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev) (Media Services .NET SDK-Erweiterungen) können Sie die neuesten Erweiterungen abrufen.

## <a id="november_changes_13"></a>Release von November 2013
### <a name="nov_13_donnet_changes"></a>Änderungen im Media Services .NET SDK
Ab dieser Version behandelt das Media Services SDK für .NET vorübergehende Fehler, die bei Aufrufen der Media Services-REST-API-Schicht auftreten können.

## <a id="august_changes_13"></a>Release von August 2013
### <a name="aug_13_powershell_changes"></a>In Azure SDK-Tools enthaltene Media Services-PowerShell-Cmdlets
Die folgenden Media Services-PowerShell-Cmdlets sind jetzt in [Azure SDK-Tools](https://github.com/Azure/azure-sdk-tools) enthalten:

* Get-AzureMediaServices 

    Beispiel: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Beispiel: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Beispiel: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Beispiel: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Release von Juni 2013
### <a name="june_13_general_changes"></a>Änderungen an Media Services
Die in diesem Abschnitt aufgeführten Änderungen sind Updates, die in den Media Services-Releases von Juni 2013 enthalten sind:

* Möglichkeit, mehrere Speicherkonten mit einem Media Service-Konto zu verknüpfen. 
    * StorageAccount
    * Asset.StorageAccountName und Asset.StorageAccount
* Möglichkeit zum Aktualisieren von Job.Priority. 
* Benachrichtigungsbezogene Entitäten und Eigenschaften: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Auftrag
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Änderungen im Media Services .NET SDK
Die folgenden Änderungen sind in den Media Services SDK-Releases von Juni 2013 enthalten. Das neueste Media Services SDK ist auf GitHub verfügbar.

* Ab Version 2.3.0.0 unterstützt das Media Services SDK die Verknüpfung mehrerer Speicherkonten mit einem Media Services-Konto. Die folgenden APIs unterstützen diese Funktion:
  
    * IStorageAccount-Typ
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts-Eigenschaft
    * StorageAccount-Eigenschaft
    * StorageAccountName-Eigenschaft
  
      Weitere Informationen finden Sie unter [Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Benachrichtigungsbezogene APIs. Ab Version 2.2.0.0 können Sie sich Azure-Warteschlangenspeicher-Benachrichtigungen anhören. Weitere Informationen finden Sie unter [Verarbeiten von Media Services-Auftragsbenachrichtigungen](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions-Eigenschaft
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint-Typ
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription-Typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection-Typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType-Typ
* Abhängigkeit vom Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Abhängigkeit von OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Release von Dezember 2012
### <a name="dec_12_dotnet_changes"></a>Änderungen im Media Services .NET SDK
* IntelliSense: Aufnahme fehlender IntelliSense-Dokumentation für viele Typen.
* Microsoft.Practices.TransientFaultHandling.Core: Es wurde ein Problem behoben, bei dem das SDK immer noch eine Abhängigkeit zu einer alten Version dieser Assembly aufwies. Das SDK verweist jetzt auf Version 5.1.1209.1 dieser Assembly.

Problembehebung im SDK von November 2012:

* IAsset.Locators.Count: Diese Anzahl wird nun richtig in neuen IAsset-Schnittstellen angegeben, nachdem alle Locators gelöscht wurden.
* IAssetFile.ContentFileSize: Dieser Wert wird jetzt nach dem Hochladen durch „IAssetFile.Upload(filepath)“ richtig festgelegt.
* IAssetFile.ContentFileSize: Diese Eigenschaft kann jetzt beim Erstellen einer Medienobjektdatei festgelegt werden. Sie war zuvor schreibgeschützt.
* IAssetFile.Upload(filepath): Es wurde ein Fehler behoben, bei dem diese synchrone Uploadmethode beim Hochladen mehrerer Dateien in das Medienobjekt den folgenden Fehler ausgab. Der Fehler lautete "Die Anforderung konnte nicht authentifiziert werden. Stellen Sie sicher, dass der Wert des Autorisierungsheaders, einschließlich der Signatur, korrekt ist."
* IAssetFile.UploadAsync: Es wurde ein Fehler behoben, der die Anzahl gleichzeitig hochgeladener Dateien auf fünf beschränkte.
* IAssetFile.UploadProgressChanged: Dieses Ereignis wird nun vom SDK bereitgestellt.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Diese Methodenüberladung wird nun bereitgestellt.
* IAssetFile.DownloadAsync: Es wurde ein Fehler behoben, der die Anzahl gleichzeitig heruntergeladener Dateien auf fünf beschränkte.
* IAssetFile.Delete(): Es wurde ein Fehler behoben, bei dem das Aufrufen der Löschfunktion eine Ausnahme auslösen konnte, wenn keine Datei für IAssetFile hochgeladen wurde.
* Aufträge: Es wurde ein Fehler behoben, bei dem durch die Verkettung einer Aufgabe zum Konvertieren von MP4 zu Smooth Streams und einer PlayReady-Schutzaufgabe unter Verwendung einer Auftragsvorlage überhaupt keine Aufgabe erstellt wurde.
* EncryptionUtils.GetCertificateFromStore(): Diese Methode löst keine Nullverweisausnahme mehr aus, weil bei der Suche nach dem Zertifikat aufgrund von Problemen mit der Zertifikatkonfiguration ein Fehler auftritt.

## <a id="november_changes_12"></a>Release von November 2012
Die in diesem Abschnitt aufgeführten Änderungen sind Updates, die im SDK von November 2012 (Version 2.0.0.0) enthalten waren. Aufgrund dieser Änderungen muss möglicherweise Code, der für die SDK-Vorschauversion von Juni 2012 geschrieben wurde, geändert oder neu geschrieben werden.

* Objekte
  
    * IAsset.Create(assetName) ist die *einzige* Funktion für die Medienobjekterstellung. IAsset.Create lädt im Rahmen des Methodenaufrufs keine Dateien mehr hoch. Verwenden Sie IAssetFile zum Hochladen.
    * Die IAsset.Publish-Methode und der AssetState.Publish-Enumerationswert wurden aus dem Services SDK entfernt. Von diesem Wert abhängiger Code muss neu geschrieben werden.
* FileInfo
  
    * Diese Klasse wurde entfernt und durch IAssetFile ersetzt.
  
* IAssetFiles
  
    * IAssetFile ersetzt FileInfo und weist ein anderes Verhalten auf. Um das IAssetFiles-Objekt verwenden zu können, instanziieren Sie es, und laden Sie dann eine Datei über das Media Services SDK oder das Storage SDK hoch. Die folgenden IAssetFile.Upload-Überladungen können verwendet werden:
  
        * IAssetFile.Upload(filePath): Diese synchrone Methode sperrt den Thread und wird nur beim Hochladen einer einzelnen Datei empfohlen.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Diese asynchrone Methode ist der bevorzugte Uploadmechanismus. 
    
            Bekannter Fehler: Wenn Sie das Abbruchtoken verwenden, wird der Upload abgebrochen. Die Aufgaben können mehrere Abbruchzustände aufweisen. Sie müssen Ausnahmen korrekt erfassen und behandeln.
* Locators
  
    * Die ursprungsspezifischen Versionen wurden entfernt. Der SAS-spezifische Locator „context.Locators.CreateSasLocator (asset, accessPolicy)“ wird als veraltet markiert oder durch die allgemeine Verfügbarkeit entfernt. Informationen zum aktualisierten Verhalten finden Sie im Abschnitt „Locator“ unter „Neue Funktionalität“.

## <a id="june_changes_12"></a>Vorschauversion Juni 2012
Die folgende Funktion war im November-Release des SDK neu:

* Löschen von Entitäten
  
    * IAsset-, IAssetFile-, ILocator-, IAccessPolicy- und IContentKey-Objekte werden nun auf Objektebene gelöscht (d.h. „IObject.Delete()“), anstatt in der Sammlung gelöscht werden zu müssen (d.h. „cloudMediaContext.ObjCollection.Delete(objInstance)“).
* Locators
  
    * Locators müssen jetzt mithilfe der CreateLocator-Methode erstellt werden. Sie müssen die LocatorType.SAS- oder LocatorType.OnDemandOrigin-Enumerationswerte als Argument für den spezifischen Locatortyp verwenden, der erstellt werden soll.
    * Den Locators wurden neue Eigenschaften hinzugefügt, mit denen Sie nutzbare URIs für Ihre Inhalte einfacher abrufen können. Dieses neue Design der Locators bietet mehr Flexibilität für eine zukünftige Erweiterbarkeit durch Drittanbieter und verbessert die Benutzerfreundlichkeit für Medienclientanwendungen.
* Unterstützung der asynchronen Methode
  
    * Für alle Methoden wurde asynchrone Unterstützung hinzugefügt.

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[MSDN-Forum für Azure Media Services]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services – REST-API-Referenz]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Eingeben von Metadaten]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Ausgeben von Metadaten]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Media Services PlayReady-Lizenzvorlage – Übersicht]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamische Paketerstellung]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Wiederholungslogik im Media Services SDK für .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

