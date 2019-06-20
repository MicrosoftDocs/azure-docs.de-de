---
title: Filter und dynamische Azure Media Services-Manifeste | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie Filter erstellen, mit denen Ihre Kunden bestimmte Abschnitte eines Streams streamen können. Media Services erstellt dynamische Manifeste, um dieses selektive Streaming zu erreichen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/11/2019
ms.author: juliako
ms.openlocfilehash: ab07b87d724f2006b6b5c0e4f472140f92230dea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080401"
---
# <a name="pre-filtering-manifests-with-dynamic-packager"></a>Vorfiltern von Manifesten mit dem dynamischen Packager

Bei der Bereitstellung von Inhalten mit Adaptive Bitrate Streaming auf Geräten müssen Sie häufig mehrere Versionen eines Manifests veröffentlichen, um auf bestimmte Gerätefunktionen oder eine verfügbare Netzwerkbandbreite abzuzielen. Der [dynamische Packager](dynamic-packaging-overview.md) gestattet es Ihnen, Filter anzugeben, die bestimmte Codecs, Auflösungen, Bitraten und Audiospurkombinationen unmittelbar ausfiltern können und so die Notwendigkeit beseitigen, mehrere Kopien erstellen zu müssen. Sie müssen lediglich eine neue URL mit einem bestimmten Satz von Filtern veröffentlichen, die für Ihre Zielgeräte (iOS, Android, SmartTV oder Browser) und die Netzwerkgegebenheiten (Szenarien mit hoher Bandbreite, mobiler Datenübertragung oder niedriger Bandbreite) konfiguriert sind. In diesem Fall können Clients das Streamen Ihrer Inhalte durch die Abfragezeichenfolge manipulieren (durch Angeben verfügbarer [Medienobjekt- oder Kontofilter](filters-concept.md)) und Filter verwenden, um bestimmte Abschnitte eines Datenstroms zu filtern.

Einige Lieferszenarien erfordern, dass Sie sicherstellen, dass ein auf bestimmte Spuren nicht zugreifen kann. Beispielsweise, wenn Sie ein Manifest, das HD-Titel enthält, nicht in einem bestimmten Abonnententarif veröffentlichen möchten. Oder wenn Sie bestimmte Titel mit Adaptive Bitrate (ABR) entfernen möchten, um die Bereitstellungskosten auf einem bestimmten Gerät zu reduzieren, das von den zusätzlichen Spuren nicht profitieren würde. In diesem Fall könnten Sie beim Erstellen einem [Streaminglocator](streaming-locators-concept.md) eine Liste zuvor erstellter Filter zuordnen. In diesem Fall können Clients nicht manipulieren, wie der Inhalt gestreamt wird, weil dies im **Streaminglocator** definiert ist.

Sie können die Filterung über bestimmte [Filter im Streaminglocator](filters-concept.md#associating-filters-with-streaming-locator) mit zusätzlichen, gerätespezifischen Filtern kombinieren, die Ihr Client in der URL angibt. Dies kann nützlich sein, um zusätzliche Spuren einzuschränken, wie Metadaten- oder Ereignisdatenströme, Audiosprachen oder beschreibende Audiospuren. 

Diese Fähigkeit, verschiedene Filter in Ihrem Datenstrom angeben zu können, bietet eine leistungsstarke Lösung zur Manipulation **dynamischer Manifeste** für gezielte mehrfache Anwendungsfallszenarien auf Ihren Zielgeräten. In diesem Thema werden Konzepte im Zusammenhang mit **dynamischen Manifesten** erläutert. Außerdem sind Beispiele für Szenarien enthalten, in denen Sie dieses Feature möglicherweise verwenden möchten.

> [!NOTE]
> Dynamische Manifeste nehmen keine Änderungen am Medienobjekt und dem Standardmanifest für dieses Medienobjekt vor. 
> 

## <a name="manifests-overview"></a>Übersicht über Manifeste

Media Services unterstützt die Protokolle HLS, MPEG DASH und Smooth Streaming. Als Teil der [dynamischen Paketerstellung](dynamic-packaging-overview.md) werden die Streamingclientmanifeste (HLS Master Playlist, DASH Media Presentation Description (MPD) und Smooth Streaming) basierend auf dem Formatselektor in der URL dynamisch generiert. Siehe die Übermittlungsprotokolle in [diesem Abschnitt](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Abrufen und Untersuchen von Manifestdateien

Sie geben eine Liste von Eigenschaftsbedingungen für die Filterung nach Spuren an, anhand derer die Spuren Ihres Streams (Live oder Video on Demand) in das dynamisch erstellte Manifest aufgenommen werden sollen. Um die Eigenschaften der Spuren abzurufen und zu untersuchen, müssen Sie zunächst das Smooth Streaming-Manifest laden.

Im Tutorial [Hochladen, Codieren und Streamen von Dateien mit .NET](stream-files-tutorial-with-api.md) erfahren Sie, wie Sie die Streaming-URLs mit .NET erstellen (siehe Abschnitt [Erstellen von URLs](stream-files-tutorial-with-api.md#get-streaming-urls)). Wenn Sie die App ausführen, zeigt einer der URLs auf das Smooth Streaming-Manifest: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Kopieren Sie die URL, und fügen Sie sie in die Adressleiste eines Browsers ein. Die Datei wird heruntergeladen. Sie können die Datei in einem Text-Editor Ihrer Wahl öffnen.

Das REST-Beispiel finden Sie unter [Hochladen, Codieren und Streamen von Dateien mit REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Überwachen der Bitrate eines Videostreams

Sie können mithilfe der [Azure Media Player-Demoseite](https://aka.ms/amp) die Bitrate eines Videostreams überwachen. Die Demoseite zeigt auf der Registerkarte **Diagnose** Diagnoseinformationen an:

![Azure Media Player-Diagnose][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Beispiele: URLs mit Filtern in der Abfragezeichenfolge

Auf Streamingprotokolle mit adaptiver Bitrate können Filter angewandt werden: HLS, MPEG-DASH und Smooth Streaming. Die folgende Tabelle zeigt einige Beispiele für URLs mit Filtern:

|Protocol|Beispiel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtern der Wiedergabe

Sie haben die Möglichkeit, ein Medienobjekt in mehreren Codierungsprofilen (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) und mit Bitraten für unterschiedliche Qualität zu codieren. Die Profile und Bitraten des Medienobjekts werden jedoch nicht auf allen Clientgeräten unterstützt. Auf älteren Android-Geräten wird beispielsweise nur H.264 Baseline+AACL unterstützt. Wenn höhere Bitraten an ein Gerät gesendet werden, auf dem daraus kein Nutzen gezogen werden kann, werden Bandbreite und Rechenressourcen des Geräts vergeudet. Diese Geräte müssen alle angegebenen Informationen decodieren, um sie dann für die Anzeige herunterzuskalieren.

Mit dynamischen Manifesten können Sie beispielsweise Geräteprofile für Mobiltelefone, Konsolen oder HD/SD erstellen und die Spuren und die Qualität einfügen, die den einzelnen Profilen zugeordnet sein sollen.

![Beispiel für das Filtern der Wiedergabe][renditions2]

Im folgenden Beispiel wurde ein Encoder zum Codieren eines Zwischenassets in sieben ISO-MP4s-Videowiedergaben (von 180p bis 1080p) verwendet. Das codierte Medienobjekt kann [dynamisch](dynamic-packaging-overview.md) in eines der folgenden Streamingprotokolle gepackt werden: HLS, MPEG-DASH und Smooth.  Oben in der Abbildung ist das HLS-Manifest für das Medienobjekt ohne Filter dargestellt (es enthält alle sieben Wiedergaben).  Links unten ist das HLS-Manifest abgebildet, auf das der Filter "ott" angewendet wurde. Der Filter „ott“ gibt an, dass alle Bitraten unter 1 MBit/s entfernt werden. Dies führte dazu, dass zwei Qualitätsstufen in der Antwort entfernt wurden. Rechts unten ist das HLS-Manifest abgebildet, auf das der Filter „mobile“ angewandt wurde. Der Filter "mobile" gibt an, dass Wiedergaben mit einer größeren Auflösung als 720p entfernt werden. Dies führte dazu, dass zwei Wiedergaben mit 1080p gelöscht wurden.

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

## <a name="combining-multiple-filters-filter-composition"></a>Kombinieren mehrerer Filter (Filterkomposition)

Sie können auch mehrere Filter in einer einzelnen URL kombinieren. 

Das folgende Szenario zeigt, warum es sinnvoll sein kann, Filter zu kombinieren:

1. Sie müssen Ihre Videoqualitäten für mobile Geräte wie Android oder iPAD filtern (um die Videoqualitäten einzuschränken). Zum Entfernen der unerwünschten Qualitätsstufen würden Sie einen Kontofilter erstellen, der für die Geräteprofile geeignet ist. Kontofilter können für alle Medienobjekte desselben Media Services-Konto ohne weitere Zuordnung verwendet werden. 
2. Sie möchten außerdem die Start- und Endzeit Zeit eines Medienobjekts kürzen. Zu diesem Zweck erstellen Sie einen Medienobjektfilter und legen die Start-/Endzeit fest. 
3. Sie möchten die beiden Filter kombinieren (ohne Kombination müssen Sie den Qualitätsfilter zum Kürzungsfilter hinzufügen, was die Verwendung des Filters erschweren würde).

Um Filter zu kombinieren, müssen Sie die Filternamen, durch Semikolons getrennt, zur Manifest-/Wiedergabelisten-URL hinzufügen. Angenommen, Sie verfügen über einen Filter mit dem Namen *MyMobileDevice* zum Filtern der Qualitäten und über einen weiteren Filter mit dem Namen *MyStartTime* zum Festlegen einer bestimmten Startzeit. Sie können diese Filter folgendermaßen kombinieren:

Sie können bis zu drei Filter kombinieren. 

Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) Blog.

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

- Die Werte für **forceEndTimestamp**, **presentationWindowDuration** und **liveBackoffDuration** dürfen für einen VoD-Filter nicht festgelegt werden. Sie werden nur in Szenarien mit Livefiltern eingesetzt. 
- Dynamische Manifeste werden in GOP-Grenzen (Keyframes) ausgeführt, daher weisen Kürzungen GOP-Genauigkeit auf. 
- Sie können die gleichen Filternamen für Konto- und Medienobjektfilter verwenden. Medienobjektfilter haben Vorrang vor Kontofiltern und überschreiben diese.
- Wenn Sie einen Filter aktualisieren, kann es bis zu 2 Minuten dauern, bis die Regeln am Streamingendpunkt aktualisiert wurden. Wenn der Inhalt mit einigen Filtern verarbeitet (und in Proxys und CDN-Caches zwischengespeichert) wurde, können durch Aktualisieren dieser Filter Player-Fehler auftreten. Es wird empfohlen, den Cache nach dem Aktualisieren des Filters zu leeren. Wenn dies nicht möglich ist, empfiehlt sich die Verwendung eines anderen Filters.
- Kunden müssen das Manifest manuell herunterladen und den exakten Wert von „startTimestamp“ und Zeitskala analysieren.
    
    - Um die Eigenschaften der Spuren in einem Medienobjekt zu bestimmen, müssen Sie die [Manifestdatei abrufen und untersuchen](#get-and-examine-manifest-files).
    - Dies ist die Formel zum Festlegen der Zeitstempeleigenschaften für einen Medienobjektfilter: <br/>startTimestamp = &lt;Startzeit im Manifest&gt; +  &lt;erwartete Filterstartzeit in Sekunden&gt; × Zeitskala

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten Informationen zum programmgesteuerten Erstellen von Filtern.  

- [Erstellen von Filtern mit REST-APIs](filters-dynamic-manifest-rest-howto.md)
- [Erstellen von Filtern mit .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Erstellen von Filtern mit der CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
