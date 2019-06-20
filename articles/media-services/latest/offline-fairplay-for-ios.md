---
title: Schützen von HLS-Inhalten mit Apple FairPlay im Offlinemodus – Azure | Microsoft-Dokumentation
description: Dieses Thema bietet einen Überblick und veranschaulicht, wie Sie Azure Media Services verwenden, um Ihre HLS-Inhalte (HTTP Live Streaming) dynamisch mit Apple FairPlay im Offlinemodus verschlüsseln.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 58315e639a54b67bdef6751004b7a768e4f92224
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937515"
---
# <a name="offline-fairplay-streaming-for-ios"></a>FairPlay-Streaming im Offlinemodus für iOS 

 Azure Media Services bietet eine Reihe von ausgereiften [Content Protection-Diensten](https://azure.microsoft.com/services/media-services/content-protection/), die Folgendes abdecken:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128-Verschlüsselung

Die DRM/AES-Verschlüsselung (Digital Rights Management/Advanced Encryption Standard) des Inhalts erfolgt auf Anforderung für verschiedene Streamingprotokolle dynamisch. Bereitstellungsdienste für DRM-Lizenzen/AES-Entschlüsselungsschlüssel werden auch von Media Services bereitgestellt.

Neben dem Schutz von Inhalten für das Onlinestreaming über verschiedene Streamingprotokolle ist auch der Offlinemodus für geschützte Inhalte ein häufig gefordertes Feature. Für die folgenden Szenarien ist die Unterstützung des Offlinemodus erforderlich:

* Wiedergabe, wenn keine Internetverbindung verfügbar ist, z. B. auf Reisen.
* Einige Inhaltsanbieter verbieten möglicherweise die Bereitstellung von DRM-Lizenzen außerhalb bestimmter Landesgrenzen/Regionen. Wenn Benutzer Inhalte auf Reisen außerhalb des Landes oder der Region ansehen möchten, ist ein Offlinedownload erforderlich.
* In einigen Ländern/Regionen ist die Verfügbarkeit und/oder Bandbreite des Internets noch begrenzt. Benutzer können sich für den vorherigen Download entscheiden, um Inhalte in ausreichender Auflösung für ein zufriedenstellendes Anzeigeerlebnis ansehen zu können. In diesem Fall liegt das Problem typischerweise nicht in der Verfügbarkeit des Netzwerks, sondern in der begrenzten Netzwerkbandbreite. OTT-/OVP-Anbieter (Over-the-Top/Online Video Platform) fordern die Unterstützung eines Offlinemodus.

Dieser Artikel befasst sich mit der Unterstützung von FairPlay Streaming (FPS) im Offlinemodus für Zielgeräte mit iOS 10 oder höher. Dieses Feature wird für andere Apple-Plattformen wie watchOS, tvOS oder Safari unter macOS nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Offline-DRM für FairPlay auf einem iOS 10+-Gerät implementieren:

* Informieren Sie sich zum Schutz von Onlineinhalten für FairPlay: 

    - [Anforderungen und Konfiguration der Apple FairPlay-Lizenz](fairplay-license-overview.md)
    - [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](protect-with-drm.md)
    - Ein .NET-Beispiel, dass die Konfiguration von FPS-Streaming im Onlinemodus enthält: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Rufen Sie das FPS SDK über das Apple Developer Network ab. Das FPS SDK enthält zwei Komponenten:

    - FPS Server SDK, das KSM (Key Security Module), Clientbeispiele, eine Spezifikation und eine Reihe von Testvektoren enthält.
    - Das FPS Deployment Pack, das die D-Funktionsspezifikation sowie Anweisungen zur Generierung des FPS-Zertifikats, des kundenspezifischen privaten Schlüssels und des geheimen Anwendungsschlüssels enthält. Apple gibt das FPS Deployment Pack nur an lizenzierte Inhaltsanbieter aus.
* Klonen https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Sie müssen den Code in [Verschlüsseln mit DRM mit .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) ändern, um FairPlay-Konfigurationen hinzufügen zu können.  

## <a name="configure-content-protection-in-azure-media-services"></a>Content Protection-Konfiguration in Azure Media Services

Gehen Sie in der Methode [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) folgendermaßen vor:

Heben Sie die Auskommentierung des Codes auf, der die FairPlay-Richtlinienoption konfiguriert:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Heben Sie außerdem die Auskommentierung des Codes auf, der „CBCS ContentKeyPolicyOption“ zur Liste der „ContentKeyPolicyOptions“ hinzufügt.

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Aktivieren des Offlinemodus

Um den Offlinemodus zu aktivieren, erstellen Sie eine benutzerdefinierte StreamingPolicy, und verwenden Sie deren Namen beim Erstellen eines StreamingLocator in [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Jetzt ist Ihr Media Services-Konto für die Übermittlung von FairPlay-Lizenzen im Offlinemodus konfiguriert.

## <a name="sample-ios-player"></a>Beispiel für iOS-Player

Die Unterstützung des FPS-Offlinemodus ist nur unter iOS 10 und höher verfügbar. Das FPS Server SDK (Version 3.0 oder höher) enthält das Dokument und das Beispiel für den FPS-Offlinemodus. Insbesondere enthält das FPS Server SDK (Version 3.0 oder höher) die folgenden zwei Elemente, die sich auf den Offlinemodus beziehen:

* Dokument: „Offlinewiedergabe mit FairPlay Streaming und HTTP Live Streaming“. Apple, 14. September 2016 Im FPS Server SDK, Version 4.0, wird dieses Dokument in das FPS-Hauptdokument eingebunden.
* Beispielcode: HLSCatalog-Beispiel (Teil des FPS Server SDK von Apple) für den FPS-Offlinemodus in „\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\“. In der HLSCatalog-Beispiel-App werden die folgenden Codedateien für die Implementierung von Features des Offlinemodus verwendet:

    - Die Codedatei „AssetPersistenceManager.swift“: „AssetPersistenceManager“ ist die Hauptklasse in diesem Beispiel, die Folgendes veranschaulicht:

        - Verwalten der Downloads von HLS-Datenströmen, z. B. die APIs zum Starten und Abbrechen von Downloads und zum Löschen vorhandener Medienobjekte von Geräten.
        - Überwachen des Downloadstatus.
    - Die Codedateien „AssetListTableViewController.swift“ und „AssetListTableViewCell.swift“: „AssetListTableViewController“ ist die Hauptschnittstelle in diesem Beispiel. Sie enthält eine Liste der Medienobjekte, mit denen das Beispiel einen Download wiedergeben, herunterladen, löschen oder abbrechen kann. 

Diese Schritte veranschaulichen die Einrichtung eines aktiven iOS-Players. Nehmen Sie unter der Annahme, dass Sie mit dem HLSCatalog-Beispiel im FPS Server SDK (Version 4.0.1) beginnen, die folgenden Codeänderungen vor:

Implementieren Sie in „HLSCatalog\Shared\Managers\ContentKeyDelegate.swift“ die Methode `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` mithilfe des folgenden Codes. Verwenden Sie für „drmUr“ eine Variable, die der HLS-URL zugeordnet ist.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Implementieren Sie in „HLSCatalog\Shared\Managers\ContentKeyDelegate.swift“ die Methode `requestApplicationCertificate()`. Diese Implementierung hängt davon ab, ob Sie das Zertifikat (nur öffentlicher Schlüssel) mit dem Gerät einbetten oder das Zertifikat im Web hosten. Die folgende Implementierung verwendet das gehostete Anwendungszertifikat, das in den Testbeispielen verwendet wird. Verwenden Sie für „certUrl“ eine Variable, die die URL des Anwendungszertifikats enthält.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Für den abschließenden integrierten Test werden sowohl die Video-URL als auch die URL des Anwendungszertifikats im Abschnitt „Integrierter Test“ zur Verfügung gestellt.

Fügen Sie in „HLSCatalog\Shared\Resources\Streams.plist“ die Testvideo-URL ein. Verwenden Sie für die ID des Inhaltsschlüssels die FairPlay-URL für den Lizenzerwerb mit dem skd-Protokoll als eindeutigen Wert.

![Datenströme von FairPlay iOS-Apps im Offlinemodus](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Verwenden Sie Ihre eigene Testvideo-URL, FairPlay-Lizenzerwerbs-URL und Anwendungszertifikat-URL, wenn Sie diese eingerichtet haben. Oder Sie fahren mit dem nächsten Abschnitt fort, der Testbeispiele enthält.

## <a name="integrated-test"></a>Integrierter Test

Drei Testbeispiele in Media Services decken die folgenden drei Szenarien ab:

* FPS-geschützt, mit Video, Audio und alternativen Audiotitel
* FPS-geschützt, mit Video und Audio, aber ohne alternativen Audiotitel
* FPS-geschützt, nur mit Video und ohne Audio

Diese Beispiele sind auf dieser [Demoseite](https://aka.ms/poc#22) zu finden, wobei das entsprechende Anwendungszertifikat in einer Azure-Web-App gehostet wird.
Mit der Version 3 oder 4 des Beispiels des FPS Server SDK wird nur Audio wiedergegeben, wenn eine Hauptwiedergabeliste alternative Audiotitel enthält. Aus diesem Grund müssen Sie den alternativen Audiotitel entfernen. Mit anderen Worten, die zuvor aufgelisteten zweiten und dritten Beispiele arbeiten im Online- und Offlinemodus. Das zuerst aufgeführte Beispiel gibt nur im Offlinemodus Audio wieder, während das Onlinestreaming ordnungsgemäß funktioniert.

## <a name="faq"></a>Häufig gestellte Fragen

Die folgenden häufig gestellten Fragen helfen bei der Problembehandlung:

- **Warum wird im Offlinemodus nur Audio, aber kein Video wiedergegeben?** Dieses Verhalten scheint durch den Entwurf der Beispielanwendung bedingt zu sein. Wenn ein alternativer Audiotitel vorhanden ist (was bei HLS der Fall ist), werden sowohl iOS 10 als auch iOS 11 im Offlinemodus standardmäßig den alternativen Audiotitel verwenden. Um dieses Verhalten für den FPS-Offlinemodus zu kompensieren, entfernen Sie den alternativen Audiotitel aus dem Datenstrom. Um dies für Media Services zu erreichen, fügen Sie den dynamischen Manifestfilter „audio-only=false“ hinzu. Eine HLS-URL endet somit mit „.ism/manifest(format=m3u8-aapl,audio-only=false)“. 
- **Warum wird im Offlinemodus nach dem Hinzufügen von „audio-only=false“ weiterhin nur Audio ohne Video wiedergegeben?** Je nach Cacheschlüsseldesign des Content Delivery Network (CDN) wird der Inhalt möglicherweise zwischengespeichert. Löschen Sie den Cacheinhalt.
- **Wird der FPS-Offlinemodus zusätzlich zu iOS 10 auch unter iOS 11 unterstützt?** Ja. Der FPS-Offlinemodus wird für iOS 10 und iOS 11 unterstützt.
- **Warum kann ich das Dokument zur Offlinewiedergabe mit FairPlay Streaming und HTTP Live Streaming nicht im FPS Server SDK finden?** Seit FPS Server SDK Version 4 wurde dieses Dokument in den FairPlay Streaming-Programmierleitfaden integriert.
- **Wie sieht die Struktur der heruntergeladenen bzw. Offlinedateien auf iOS-Geräten aus?** Die heruntergeladene Dateistruktur auf einem iOS-Gerät sieht aus wie auf dem folgenden Screenshot. Der Ordner `_keys` speichert heruntergeladene FPS-Lizenzen, wobei eine Speicherdatei für jeden Lizenzdiensthost verwendet wird. Der Ordner `.movpkg` speichert Audio- und Videoinhalte. Der erste Ordner mit einem Namen, der mit einem Bindestrich endet, auf den eine Zahl folgt, enthält Videoinhalte. Der numerische Wert ist die Spitzenbandbreite (PeakBandwidth) der Videowiedergabe. Der zweite Ordner mit einem Namen, der mit einem Bindestrich endet, auf den „0“ folgt, enthält Audioinhalte. Der dritte Ordner namens „Data“ enthält die Hauptwiedergabeliste des FPS-Inhalts. Schließlich enthält „boot.xml“ eine vollständige Beschreibung des Inhalts des Ordners `.movpkg`. 

![Dateistruktur der iOS-Beispielanwendung für FairPlay im Offlinemodus](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Beispieldatei für „boot.xml“:

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

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Schützen mit AES-128](protect-with-aes128.md).
