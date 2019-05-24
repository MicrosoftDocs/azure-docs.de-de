---
title: Filter und dynamische Manifeste | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie Filter erstellen, mit denen Ihre Kunden bestimmte Abschnitte eines Streams streamen können. Media Services erstellt dynamische Manifeste, um dieses selektive Streaming zu archivieren.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 68eeb40e905d089601208d9fc181042c7b434843
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956802"
---
# <a name="filters-and-dynamic-manifests"></a>Filter und dynamische Manifeste

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 2](media-services-dynamic-manifest-overview.md)
> * [Version 3](../latest/filters-dynamic-manifest-overview.md)

Ab Version 2.17 können Sie mit Media Services Filter für Ihre Medienobjekte definieren. Diese Filter sind serverseitige Regeln, mit denen Ihre Kunden verschiedene Aktionen ausführen können, z.B. Wiedergabe bestimmter Videoabschnitte (anstelle des gesamten Videos). Sie können zudem nur eine Teilmenge von Audio- und Videowiedergaben (anstelle von allen mit dem Medienobjekt verknüpften Wiedergaben) angeben, die für das Gerät eines Kunden geeignet sind. Diese Filterung der Medienobjekte erfolgt über **dynamische Manifeste**, die auf Anfrage des Kunden zum Streamen von Videos basierend auf bestimmten Filtern erstellt werden.

In diesem Thema werden allgemeine Szenarien behandelt, bei denen die Verwendung von Filtern für Ihre Kunden von Nutzen sein kann. Außerdem werden Links zu Themen vorgestellt, in denen die programmgesteuerte Erstellung von Filtern erläutert wird.

## <a name="overview"></a>Übersicht
Bei der Übermittlung Ihrer Inhalte an Kunden (Streaming von Liveveranstaltungen oder Video-on-Demand) besteht Ihr Ziel darin, qualitativ hochwertige Videos für unterschiedliche Geräte unter verschiedenen Netzwerkbedingungen zu übermitteln. Gehen Sie wie folgt vor, um dieses Ziel zu erreichen:

* Codieren Sie den Stream in einen Videostream mit mehreren Bitraten ([mit adaptiver Bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (dies betrifft die Qualität und die Netzwerkbedingungen), und 
* verwenden Sie [Media Services Dynamic Packaging](media-services-dynamic-packaging-overview.md) , um den Stream dynamisch erneut in verschiedene Protokolle zu packen (dies betrifft das Streaming auf verschiedenen Geräten). Media Services unterstützt die folgenden Technologien mit Adaptive Bitrate Streaming: HTTP Live Streaming (HLS), Smooth Streaming und MPEG DASH. 

### <a name="manifest-files"></a>Manifestdateien
Wenn Sie ein Medienobjekt für das Streaming mit adaptiver Bitrate codieren, wird eine **Manifestdatei** (Wiedergabelistendatei) erstellt (die Datei ist textbasiert oder XML-basiert). Die **Manifestdatei** enthält Streamingmetadaten wie z. B. Typ (Audio, Video oder Text), Spurenname, Start-und Endzeit, Bitrate (Qualität), Sprachen, Präsentationsfenster (variables Fenster mit fester Dauer) und Videocodec (FourCC). Sie weist den Player zudem zum Abrufen des nächsten Fragments an, indem Informationen zu den nächsten für die Wiedergabe verfügbaren Videofragmenten und den zugehörigen Speicherorten angezeigt werden. Fragmente (oder Segmente) sind die eigentlichen "Blöcke" von Videoinhalten.

Hier ein Beispiel für eine Manifestdatei: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dynamische Manifeste
Es gibt bestimmte [Szenarios](media-services-dynamic-manifest-overview.md#scenarios) , für die Ihre Kunden eine größere Flexibilität benötigen, als dies in den Einstellungen der Standardmanifestdatei des Medienobjekts beschrieben ist. Beispiel: 

* Gerätespezifisch: Übermitteln nur der angegebenen Wiedergaben und/oder Sprachen, die von dem Gerät unterstützt werden, das für die Wiedergabe der Inhalte verwendet wird („Filtern der Wiedergabe“). 
* Reduzieren des Manifests, um einen Subclip eines Liveereignisses anzuzeigen ("Filtern von Subclips").
* Kürzen des Starts eines Videos ("Kürzen eines Videos").
* Anpassen des Präsentationsfensters (DVR), um eine begrenzte Größe des DVR-Fensters im Player anzugeben („Anpassen des Präsentationsfensters“).

Um diese Flexibilität zu erreichen, umfasst Media Services **dynamische Manifeste** , die auf vordefinierten [Filtern](media-services-dynamic-manifest-overview.md#filters)basieren.  Nachdem Sie die Filter definiert haben, können Ihre Kunden sie zum Streamen einer spezifischen Wiedergabe oder bestimmter Subclips Ihres Videos verwenden. Dazu geben sie die Filter in der Streaming-URL an. Die Filter können auf die mit der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md) unterstützten Streamingprotokolle mit adaptiver Bitrate angewandt werden: HLS, MPEG-DASH und Smooth Streaming. Beispiel: 

MPEG-DASH-URL mit Filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming-URL mit Filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Weitere Informationen zum Übermitteln Ihrer Inhalte und zum Erstellen von Streaming-URLs finden Sie unter [Übersicht zum Übermitteln von Inhalten](media-services-deliver-content-overview.md).

> [!NOTE]
> Beachten Sie, dass dynamische Manifeste nicht das Medienobjekt und das Standardmanifest für dieses Medienobjekt ändern. Ihre Kunden können auswählen, ob sie einen Stream mit oder ohne Filter anfordern. 
> 
> 

### <a id="filters"></a>Filter
Es gibt zwei Typen von Filtern für Medienobjekte: 

* Globale Filter (können auf jedes Medienobjekt im Azure Media Services-Konto angewendet werden und haben die Lebensdauer des Kontos) und 
* Lokale Filter (können nur auf ein Medienobjekt angewendet werden, dem der Filter bei der Erstellung zugeordnet wurde, und haben die Lebensdauer des Medienobjekts) 

Globale und lokale Filter verfügen über genau die gleichen Eigenschaften. Der Hauptunterschied zwischen den beiden Filtern liegt in ihrer jeweils besseren Tauglichkeit für verschiedene Szenarios. Globale Filter eignen sich in der Regel für Geräteprofile (Filtern der Wiedergabe), lokale Filter hingegen können zum Kürzen eines bestimmten Medienobjekts eingesetzt werden.

## <a id="scenarios"></a>Allgemeine Szenarios
Wie bereits zuvor erwähnt, besteht Ihr Ziel bei der Übermittlung Ihrer Inhalte für Kunden (Streaming von Liveereignissen oder Video-on-Demand) darin, qualitativ hochwertige Videos für unterschiedliche Geräte unter verschiedenen Netzwerkbedingungen zu übermitteln. Darüber hinaus haben Sie möglicherweise andere Anforderungen, beispielsweise die Filterung Ihrer Medienobjekte oder die Verwendung **dynamischer Manifeste**. In den folgenden Abschnitten erhalten Sie einen kurzen Überblick über verschiedene Filterungsszenarios.

* Angeben einer spezifischen Teilmenge von Audio- und Videowiedergaben (anstatt aller mit dem Medienobjekt verknüpften Wiedergaben), die für bestimmte Geräte geeignet sind. 
* Wiedergeben von nur einem Abschnitt eines Videos (anstelle des gesamten Videos).
* Anpassen des DVR-Präsentationsfensters.

## <a name="rendition-filtering"></a>Filtern der Wiedergabe
Sie haben die Möglichkeit, ein Medienobjekt in mehreren Codierungsprofilen (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) und mit Bitraten für unterschiedliche Qualität zu codieren. Die Profile und Bitraten des Medienobjekts werden jedoch nicht auf allen Clientgeräten unterstützt. Auf älteren Android-Geräten wird beispielsweise nur H.264 Baseline+AACL unterstützt. Wenn höhere Bitraten an ein Gerät gesendet werden, auf dem daraus kein Nutzen gezogen werden kann, werden Bandbreite und Rechenressourcen des Geräts verschwendet. Diese Geräte müssen alle angegebenen Informationen decodieren, um sie dann für die Anzeige herunterzuskalieren.

Mit dynamischen Manifesten können Sie Geräteprofile erstellen, z. B. für Mobiltelefone, Konsolen oder HD/SD, und die Spuren und die Qualität einfügen, die den einzelnen Profilen zugeordnet sein sollen.

![Beispiel für das Filtern der Wiedergabe][renditions2]

Im folgenden Beispiel wurde ein Encoder zum Codieren eines Zwischenassets in sieben ISO-MP4s-Videowiedergaben (von 180p bis 1080p) verwendet. Das codierte Medienobjekt kann dynamisch in eines der folgenden Streamingprotokolle gepackt werden: HLS, Smooth Streaming und MPEG-DASH.  Oben in der Abbildung ist das HLS-Manifest für das Medienobjekt ohne Filter dargestellt (es enthält alle sieben Wiedergaben).  Links unten ist das HLS-Manifest abgebildet, auf das der Filter "ott" angewendet wurde. Der Filter "ott" gibt an, dass alle Bitraten unter 1 MBit/s entfernt werden. Dies führte dazu, dass zwei Qualitätsstufen in der Antwort entfernt wurden. Rechts unten ist das HLS-Manifest abgebildet, auf das der Filter „mobile“ angewandt wurde. Der Filter "mobile" gibt an, dass Wiedergaben mit einer größeren Auflösung als 720p entfernt werden. Dies führte dazu, dass zwei Wiedergaben mit 1080p gelöscht wurden.

![Filtern der Wiedergabe][renditions1]

## <a name="removing-language-tracks"></a>Entfernen von Sprachspuren
Ihre Medienobjekte können mehrere Audiosprachen enthalten, z. B. Englisch, Spanisch, Französisch usw. In der Regel verwaltet das Player-SDK die Auswahl der Standardaudiospur und verfügbare Audiospuren entsprechend der Benutzerauswahl. Es ist schwierig, solche Player-SDKs zu entwickeln. Sie erfordern verschiedene Implementierungen in gerätespezifischen Player-Frameworks. Außerdem sind die Player-APIs auf manchen Plattformen eingeschränkt und umfassen keine Funktion zur Audioauswahl, sodass Benutzer die Standardaudiospur nicht auswählen oder ändern können. Mit Filtern für Medienobjekte können Sie das Verhalten steuern, indem Sie Filter erstellen, die nur die gewünschten Audiosprachen enthalten.

![Filtern der Sprachspuren][language_filter]

## <a name="trimming-start-of-an-asset"></a>Kürzen des Starts eines Medienobjekts
Bei den meisten Live-Streaming-Ereignissen führen Operatoren vor dem eigentlichen Ereignis einige Tests durch. Sie fügen z.B. die folgende Meldung vor dem Start des Ereignisses ein: „Programm beginnt sofort“. Wenn das Programm archiviert wird, werden auch die Test- und Meldungsdaten archiviert und in die Präsentation einbezogen. Diese Informationen sollten jedoch nicht auf den Clients angezeigt werden. Mit dynamischen Manifesten können Sie einen Filter für die Startzeit erstellen und die unerwünschten Daten aus dem Manifest entfernen.

![Kürzen des Starts][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Erstellen von Subclips (Ansichten) aus einem Livearchiv
Viele Liveereignisse werden lange ausgeführt. Daher können Livearchive mehrere Ereignisse enthalten. Nach dem Ende des Liveereignisses möchten Sendeanstalten das Livearchiv möglicherweise in logische Sequenzen mit Programmstart und Programmende unterteilen. Anschließend sollen diese virtuellen Programme separat ohne Nachbearbeitung des Livearchivs veröffentlicht und keine separaten Medienobjekte erstellt werden (bei denen kein Nutzen aus den vorhandenen in den CDNs zwischengespeicherten Fragmenten gezogen wird). Beispiele für solche virtuellen Programme sind Halbzeiten eines Fußball- oder Basketballspiels, die Innings beim Baseball oder einzelne Wettbewerbe beliebiger Sportprogramme.

Mit dynamischen Manifesten können Sie Filter mit Start- und Endzeiten sowie virtuelle Ansichten für Ihr Livearchiv erstellen. 

![Filter für Subclip][subclip_filter]

Gefiltertes Medienobjekt:

![Skilaufen][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Anpassen des Präsentationsfensters (DVR)
Zum gegenwärtigen Zeitpunkt umfasst Azure Media Services Umlaufarchive, bei denen eine Dauer zwischen 5 Minuten und 25 Stunden konfiguriert werden kann. Durch Filter über Manifeste kann ein DVR-Gleitfenster über dem Archiv erstellt werden, ohne dass Medien gelöscht werden. Es gibt viele Szenarios, bei denen Sendeanstalten ein in der Größe begrenztes DVR-Fenster bereitstellen möchten, das sich am Rand des Livevideos bewegt, und gleichzeitig ein größeres Archivierungsfenster beibehalten möchten. Ein bestimmter Sender möchte eventuell die Daten verwenden, die sich außerhalb des DVR-Fensters befinden, um Clips hervorzuheben oder unterschiedliche DVR-Fenster für verschiedene Geräte bereitstellen. Auf den meisten Mobilgeräten werden beispielsweise keine großen DVR-Fenster verarbeitet. (Sie können ein 2-Minuten-Fenster für Mobilgeräte und ein 1-Stunden-Fenster für Desktopclients einrichten.)

![DVR-Fenster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Anpassen von LiveBackoff (Liveposition)
Mit der Filterung über Manifeste können mehrere Sekunden vom Rand eines Liveprogramms entfernt werden. Die Filterung gestattet es Sendeanstalten, die Präsentation am Vorschauveröffentlichungspunkt anzusehen und Einfügepunkte für Werbespots zu erstellen, bevor die Zuschauer den Stream empfangen (um 30 Sekunden zurückgehalten). Sendeanstalten können diese Werbespots dann an ihre Clientframeworks senden, sodass diese sie rechtzeitig empfangen und die Informationen vor der Ausstrahlung verarbeiten können.

Neben der Unterstützung für Werbespots kann mithilfe der LiveBackoff-Einstellung die Betrachterposition angepasst werden, sodass Clients anstelle der HTTP-Fehler 404 oder 412 weiterhin Fragmente empfangen, wenn sie den Rand des Livevideos erreichen.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinieren mehrerer Regeln in einem Filter
Sie können mehrere Filterregeln in ein und demselben Filter kombinieren. Als Beispiel können Sie eine „Bereichsregel“ zum Entfernen von Meldungen aus einem Livearchiv definieren und zudem verfügbare Bitraten filtern. Wenn mehrere Filterregeln angewendet werden, ist das Endergebnis die Schnittmenge aller Regeln.

![mehrere Regeln][multiple-rules]

## <a name="create-filters-programmatically"></a>Programmgesteuertes Erstellen von Filtern
Im folgenden Artikel werden Media Services-Entitäten beschrieben, die im Zusammenhang mit Filtern stehen. In diesem Artikel wird auch erörtert, wie Filter programmgesteuert erstellt werden.  

[Erstellen von Filtern mit REST-APIs](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinieren mehrerer Filter (Filterkomposition)
Sie können auch mehrere Filter in einer einzelnen URL kombinieren. 

Das folgende Szenario zeigt, warum es sinnvoll sein kann, Filter zu kombinieren:

1. Sie müssen Ihre Videoqualitäten für mobile Geräte wie Android oder iPAD filtern (um die Videoqualitäten einzuschränken). Zum Entfernen der unerwünschten Qualitäten würden Sie einen globalen Filter erstellen, der für die Geräteprofile geeignet ist. Wie bereits zuvor in diesem Artikel erwähnt, können globale Filter für alle Medienobjekte desselben Mediendienstkontos ohne weitere Zuordnung verwendet werden. 
2. Sie möchten außerdem die Start- und Endzeit Zeit eines Medienobjekts kürzen. Zu diesem Zweck erstellen Sie einen lokalen Filter und legen die Start- bzw. Endzeit fest. 
3. Sie möchten die beiden Filter kombinieren (ohne Kombination müssen Sie den Qualitätsfilter zum Kürzungsfilter hinzufügen, was die Verwendung des Filters erschweren würde).

Um Filter zu kombinieren, müssen Sie die Filternamen, durch Semikolons getrennt, zur Manifest-/Wiedergabelisten-URL hinzufügen. Angenommen, Sie verfügen über einen Filter mit dem Namen *MyMobileDevice* zum Filtern der Qualitäten und über einen weiteren Filter mit dem Namen *MyStartTime* zum Festlegen einer bestimmten Startzeit. Sie können diese Filter folgendermaßen kombinieren:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Sie können bis zu drei Filter kombinieren. 

Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) Blog.

## <a name="know-issues-and-limitations"></a>Bekannte Probleme und Einschränkungen
* Dynamische Manifeste werden in GOP-Grenzen (Keyframes) ausgeführt, daher weisen Kürzungen GOP-Genauigkeit auf. 
* Sie können die gleichen Filternamen für lokale und globale Filter verwenden. Lokale Filter haben Vorrang vor globalen Filtern und überschreiben diese.
* Wenn Sie einen Filter aktualisieren, kann es bis zu 2 Minuten dauern, bis die Regeln am Streamingendpunkt aktualisiert wurden. Wenn der Inhalt mit einigen Filtern verarbeitet (und in Proxys und CDN-Caches zwischengespeichert) wurde, können durch Aktualisieren dieser Filter Player-Fehler auftreten. Es wird empfohlen, den Cache nach dem Aktualisieren des Filters zu leeren. Wenn dies nicht möglich ist, empfiehlt sich die Verwendung eines anderen Filters.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Bereitstellen von Inhalten für Kunden – Übersicht](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
