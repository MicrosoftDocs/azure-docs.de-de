---
title: Schützen von HLS-Inhalten mit Apple FairPlay im Offlinemodus – Azure | Microsoft-Dokumentation
description: Dieses Thema bietet einen Überblick und veranschaulicht, wie Sie Azure Media Services verwenden, um Ihre HLS-Inhalte (HTTP Live Streaming) dynamisch mit Apple FairPlay im Offlinemodus verschlüsseln.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: bc939011f87f03ef1de7e728fc52fc0c9887dd31
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935406"
---
# <a name="offline-fairplay-streaming-for-ios"></a>FairPlay-Streaming im Offlinemodus für iOS 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 3](../latest/offline-fairplay-for-ios.md)
> * [Version 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

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

## <a name="preliminary-steps"></a>Vorbereitende Schritte
Bevor Sie Offline-DRM für FairPlay auf einem iOS 10+-Gerät implementieren:

* Machen Sie sich mit dem Schutz von Onlineinhalten für FairPlay vertraut. Weitere Informationen finden Sie in den folgenden Artikeln und Beispielen:

    - [Allgemeine Verfügbarkeit von Apple FairPlay Streaming für Azure Media Services](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Schützen von HLS-Inhalten mit Apple FairPlay oder Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Beispiel für FPS Streaming im Onlinemodus](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Rufen Sie das FPS SDK über das Apple Developer Network ab. Das FPS SDK enthält zwei Komponenten:

    - FPS Server SDK, das KSM (Key Security Module), Clientbeispiele, eine Spezifikation und eine Reihe von Testvektoren enthält.
    - Das FPS Deployment Pack, das die D-Funktionsspezifikation sowie Anweisungen zur Generierung des FPS-Zertifikats, des kundenspezifischen privaten Schlüssels und des geheimen Anwendungsschlüssels enthält. Apple gibt das FPS Deployment Pack nur an lizenzierte Inhaltsanbieter aus.

## <a name="configuration-in-media-services"></a>Konfiguration in Media Services
Für die Konfiguration des FPS-Offlinemodus über das [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) verwenden Sie das Media Services .NET SDK in der Version 4.0.0.4 oder höher, die die erforderliche API zum Konfigurieren des FPS-Offlinemodus bereitstellt.
Außerdem benötigen Sie den funktionierenden Code, um Content Protection im Onlinemodus für FPS zu konfigurieren. Nachdem Sie über den Code für die Konfiguration von Content Protection im Onlinemodus für FPS verfügen, müssen Sie nur noch die folgenden beiden Änderungen vornehmen.

## <a name="code-change-in-the-fairplay-configuration"></a>Codeänderung in der FairPlay-Konfiguration
Die erste Änderung ist die Definition eines booleschen Wert für „Offlinemodus aktiviert“ namens „objDRMSettings.EnableOfflineMode“, der „true“ ist, wenn er das DRM-Offlineszenario aktiviert. In Abhängigkeit von diesem Indikator nehmen Sie die folgende Änderung an der FairPlay-Konfiguration vor:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Codeänderung für die Konfiguration der Übermittlungsrichtlinie für Medienobjekte
Die zweite Änderung besteht darin, den dritten Schlüssel zu „Dictionary<AssetDeliveryPolicyConfigurationKey, string>“ hinzuzufügen.
Fügen Sie „AssetDeliveryPolicyConfigurationKey“ wie hier veranschaulicht hinzu:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Nach diesem Schritt enthält die Zeichenfolge „<Dictionary_AssetDeliveryPolicyConfigurationKey>“ in der FPS-Übermittlungrichtlinie für Medienobjekte die folgenden drei Einträge:

* „AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl“ oder „AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl“, je nach Faktoren wie dem FPS KSM-/Schlüsselserver und der Frage, ob dieselbe Übermittlungsrichtlinie für Medienobjekte über mehrere Objekte hinweg wiederverwendet werden soll.
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Jetzt ist Ihr Media Services-Konto für die Übermittlung von FairPlay-Lizenzen im Offlinemodus konfiguriert.

## <a name="sample-ios-player"></a>Beispiel für iOS-Player
Die Unterstützung des FPS-Offlinemodus ist nur unter iOS 10 und höher verfügbar. Das FPS Server SDK (Version 3.0 oder höher) enthält das Dokument und das Beispiel für den FPS-Offlinemodus. Insbesondere enthält das FPS Server SDK (Version 3.0 oder höher) die folgenden zwei Elemente, die sich auf den Offlinemodus beziehen:

* Dokument: „Offlinewiedergabe mit FairPlay Streaming und HTTP Live Streaming“. Apple, 14. September 2016 Im FPS Server SDK, Version 4.0, wird dieses Dokument in das FPS-Hauptdokument eingebunden.
* Beispielcode: HLSCatalog-Beispiel für den FPS-Offlinemodus in „\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\“. In der HLSCatalog-Beispiel-App werden die folgenden Codedateien für die Implementierung von Features des Offlinemodus verwendet:

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

![Datenströme von FairPlay iOS-Apps im Offlinemodus](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

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
- **Wofür steht der letzte Parameter in der folgenden API für den FPS-Offlinemodus?** 
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Die Dokumentation zu dieser API finden Sie unter [Methode „FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration“](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Der Parameter repräsentiert die Dauer der Offlinemiete mit der Einheit „Stunde“.
- **Wie sieht die Struktur der heruntergeladenen bzw. Offlinedateien auf iOS-Geräten aus?** Die heruntergeladene Dateistruktur auf einem iOS-Gerät sieht aus wie auf dem folgenden Screenshot. Der Ordner `_keys` speichert heruntergeladene FPS-Lizenzen, wobei eine Speicherdatei für jeden Lizenzdiensthost verwendet wird. Der Ordner `.movpkg` speichert Audio- und Videoinhalte. Der erste Ordner mit einem Namen, der mit einem Bindestrich endet, auf den eine Zahl folgt, enthält Videoinhalte. Der numerische Wert ist die Spitzenbandbreite (PeakBandwidth) der Videowiedergabe. Der zweite Ordner mit einem Namen, der mit einem Bindestrich endet, auf den „0“ folgt, enthält Audioinhalte. Der dritte Ordner namens „Data“ enthält die Hauptwiedergabeliste des FPS-Inhalts. Schließlich enthält „boot.xml“ eine vollständige Beschreibung des Inhalts des Ordners `.movpkg`. 

![Dateistruktur der iOS-Beispielanwendung für FairPlay im Offlinemodus](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>Zusammenfassung
Dieses Dokument enthält die folgenden Schritte und Informationen, mit denen Sie den FPS-Offlinemodus implementieren können:

* Die Content Protection-Konfiguration für Media Services über die Media Services .NET API konfiguriert die dynamische FairPlay-Verschlüsselung und FairPlay-Lizenzübermittlung in Media Services.
* Ein iOS-Player, der auf dem Beispiel aus dem FPS Server SDK basiert, richtet einen iOS-Player ein, der FPS-Inhalte entweder im Onlinestreamingmodus oder im Offlinemodus wiedergeben kann.
* Beispielvideos für FPS werden zum Testen von Offlinemodus und Onlinestreaming verwendet.
* Eine häufig gestellte Fragen beantwortet Fragen zum FPS-Offlinemodus.
