---
title: Konfigurieren des Offlinestreamings mit PlayReady
description: In diesem Thema erfahren Sie, wie Sie Ihr Azure Media Services v3-Konto für das Offlinestreaming von mit PlayReady für Windows 10 geschützten Inhalten konfigurieren.
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
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: ea3d895970c6a18286d719471a693f774efaa387
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955228"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offlinestreaming mit PlayReady für Windows 10 mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services unterstützt Download und Offlinewiedergabe mit DRM-Schutz. In diesem Artikel wird die Offline-Unterstützung von Azure Media Services für PlayReady-Clients unter Windows 10 behandelt. In den folgenden Artikeln erfahren Sie mehr über die Unterstützung des Offlinemodus für Geräte mit iOS/FairPlay und Android/Widevine:

- [Offlinestreaming mit FairPlay für iOS](offline-fairplay-for-ios.md)
- [Offlinestreaming mit Widevine für Android](offline-widevine-for-android.md)

> [!NOTE]
> Offline-DRM wird nur für eine einzelne Anforderung für eine Lizenz in Rechnung gestellt, wenn Sie den Inhalt herunterladen. Fehler werden nicht in Rechnung gestellt.

## <a name="overview"></a>Übersicht

Dieser Abschnitt enthält Hintergrundinformationen zu den möglichen Gründen für eine Wiedergabe im Offlinemodus:

* In einigen Ländern/Regionen ist die Verfügbarkeit und/oder Bandbreite des Internets noch begrenzt.  Benutzer können sich für den vorherigen Download entscheiden, um Inhalte in ausreichender Auflösung für ein zufriedenstellendes Anzeigeerlebnis ansehen zu können. In diesem Fall geht es häufig nicht um die Verfügbarkeit des Netzwerks, sondern um die begrenzte Bandbreite des Netzwerks. OTT/OVP-Anbieter fordern eine Unterstützung des Offlinemodus.
* Wie auf der Aktionärshauptversammlung von Netflix im 3. Quartal 2016 bekannt gegeben, sei das Herunterladen von Inhalten laut Reed Hastings, CEO von Netflix, ein oft nachgefragtes Feature, für das das Unternehmen offen sei.
* Einige Inhaltsanbieter verbieten möglicherweise die Bereitstellung von DRM-Lizenzen außerhalb einer Landesgrenze/Region. Wenn ein Benutzer ins Ausland reist und trotzdem Inhalte ansehen möchte, ist ein Download für die Offlinenutzung erforderlich.
 
Die Herausforderung bei der Implementierung des Offlinemodus ist folgende:

* MP4 wird von vielen Playern und Encodertools unterstützt, bietet aber keine Verbindung zwischen MP4-Container und DRM.
* Auf lange Sicht ist CFF mit CENC die beste Wahl. Allerdings ist das Ökosystem zur Unterstützung von Tools und Playern derzeit noch nicht vorhanden. Wir brauchen aber heute eine Lösung.
 
Unser Vorschlag: Das Smooth Streaming-Dateiformat ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) mit H264/AAC bietet eine Bindung mit PlayReady (AES-128-CTR). Die jeweilige Smooth Streaming-Datei im ISMV-Format (vorausgesetzt, das Audio wird im Video gemuxt) ist selbst eine fMP4-Datei und kann für die Wiedergabe verwendet werden. Wenn ein Smooth Streaming-Inhalt mittels PlayReady-verschlüsselt wird, wird jede ISMV-Datei zu einer mit PlayReady-geschützten, fragmentierten MP4-Datei. Wir können eine ISMV-Datei mit der gewünschten Bitrate auswählen und sie zum Download in MP4 umbenennen.

Es gibt zwei Möglichkeiten, die mit PlayReady geschützte MP4-Datei für den progressiven Download bereitzustellen:

* Sie können diese MP4-Datei in dasselbe Container-/Mediendienst-Medienobjekt einfügen und den Streamingendpunkt von Azure Media Services für den progressiven Download nutzen.
* Sie können den SAS-Locator für den progressiven Download direkt aus Azure Storage verwenden und dabei Azure Media Services umgehen.
 
Es gibt zwei Arten von PlayReady-Lizenzübermittlung:

* PlayReady-Lizenzübermittlung in Azure Media Services
* An beliebiger Stelle gehosteter PlayReady-Lizenzserver

Nachstehend finden Sie zwei Sätze von Testmedienobjekten, von denen das erste die PlayReady-Lizenzübermittlung in AMS und das zweite meinen PlayReady-Lizenzserver verwendet, der auf einer Azure-VM gehostet wird:

Medienobjekt 1:

* URL für progressiven Download: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA-URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

Medienobjekt 2:

* URL für progressiven Download: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA-URL (lokal): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Zum Testen der Wiedergabe haben wir eine universelle Windows-Anwendung unter Windows 10 verwendet. Unter [Windows 10 Universal Sample](https://github.com/Microsoft/Windows-universal-samples) auf GitHub finden Sie ein einfaches Beispiel für einen Player namens [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Alles, was wir tun müssen, ist, den Code hinzuzufügen, damit wir das heruntergeladene Video auswählen und es anstelle der adaptiven Streamingquelle als Quelle verwenden können. Die Änderungen befinden sich im Ereignishandler für das Schaltflächenklickereignis.

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Wiedergabe im Offlinemodus der mit PlayReady geschützten fMP4-Datei](./media/offline-playready-for-windows/offline-playready1.jpg)

Da das Video von PlayReady geschützt wird, kann der Screenshot das Video nicht enthalten.

Zusammengefasst haben wir den Offlinemodus in Azure Media Services erreicht:

* Inhaltstranscodierung und PlayReady-Verschlüsselung können in Azure Media Services oder anderen Tools erfolgen.
* Für den progressiven Download können Inhalte in Azure Media Services oder Azure Storage gehostet werden.
* Die PlayReady-Lizenzübermittlung kann aus Azure Media Services oder einem anderen Dienst erfolgen.
* Die vorbereiteten Smooth Streaming-Inhalte können weiterhin für Onlinestreaming über DASH oder Smooth Streaming mit PlayReady als DRM verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

[Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)
