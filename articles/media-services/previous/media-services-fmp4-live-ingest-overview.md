---
title: Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services | Microsoft-Dokumentation
description: Diese Spezifikation beschreibt das Protokoll und Format für die fragmentierte MP4-basierte Livestreamingerfassung für Azure Media Services. Mit Azure Media Services können Sie unter Verwendung von Azure als Cloudplattform in Echtzeit Liveereignisse streamen und Inhalte übertragen. In diesem Dokument werden auch optimale Verfahren zur Erstellung hoch redundanter und stabiler Mechanismen der Echtzeiterfassung beschrieben.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: b3357436d068396c5c3c4fae10ed6857759c5aed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61221321"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services 

Diese Spezifikation beschreibt das Protokoll und Format für die fragmentierte MP4-basierte Livestreamingerfassung für Azure Media Services. Media Services bietet einen Livestreamingdienst, mit dem Kunden Liveereignisse und Sendungsinhalte mit Azure als Cloudplattform in Echtzeit streamen können. In diesem Dokument werden auch optimale Verfahren zur Erstellung hoch redundanter und stabiler Mechanismen der Echtzeiterfassung beschrieben.

## <a name="1-conformance-notation"></a>1. Konformität der Schlüsselbegriffe
Die Schlüsselwörter „MUSS“, „DARF NICHT“, „ERFORDERLICH“, „SOLL“, „SOLL NICHT“, „SOLLTE“, „SOLLTE NICHT“, „EMPFOHLEN“, „KANN“ und „OPTIONAL“ in diesem Dokument sind wie in RFC 2119 zu verstehen.

## <a name="2-service-diagram"></a>2. Schematische Darstellung des Diensts
In der folgenden schematischen Darstellung ist die allgemeine Architektur des Livestreamingdiensts in Media Services dargelegt:

1. Ein Liveencoder überträgt mithilfe von Push Livefeeds in Kanäle, die über das Azure Media Services-SDK erstellt und bereitgestellt werden.
1. Kanäle, Programme und Streamingendpunkte in Media Services verarbeiten alle Livestreamingfunktionen einschließlich Erfassung, Formatierung, Cloud-DVR, Sicherheit, Skalierbarkeit und Redundanz.
1. Optional können Kunden auswählen, eine Azure Content Delivery Network-Ebene zwischen dem Streamingendpunkt und den Clientendpunkten bereitzustellen.
1. Clientendpunkte streamen vom Streamingendpunkt über HTTP Adaptive Streaming-Protokolle. Beispiele hierfür sind Microsoft Smooth Streaming, Dynamic Adaptive Streaming über HTTP (DASH oder MPEG-DASH) und Apple HTTP Live Streaming (HLS).

![Erfassungsdatenfluss][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstromformat – ISO 14496-12, fragmentiertes MP4
Das in diesem Dokument beschriebene Übertragungsformat zur Erfassung des Livestreamings basiert auf [ISO-14496-12]. Eine ausführliche Erläuterung des fragmentierten MP4-Formats und der Erweiterungen für Video-on-Demand-Dateien und die Livestreamingerfassung finden Sie unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definitionen für Liveerfassungsformat
Es folgt eine Liste der speziellen Formatdefinitionen, die für die Echtzeiterfassung in Azure Media Services gelten:

1. Die Felder **ftyp**, **Live Server Manifest Box** und **moov** MÜSSEN immer zusammen mit Anforderungen gesendet werden (HTTP POST). Diese Felder MÜSSEN am Anfang des Streams und immer dann gesendet werden, wenn der Encoder erneut eine Verbindung herstellen muss, um die Streamerfassung fortzusetzen. Weitere Informationen finden Sie in Abschnitt 6 in [1].
1. Im Abschnitt 3.3.2 in [1] wird ein optionales Feld mit dem Namen **StreamManifestBox** für die Echtzeiterfassung definiert. Aufgrund der Routinglogik des Azure-Lastenausgleichs ist dieses Kontrollkästchen veraltet. Das Feld SOLLTE bei der Erfassung in Media Services NICHT vorhanden sein. Wenn dieses Feld vorhanden ist, wird es in Media Services ohne Meldung ignoriert.
1. Das in Abschnitt 3.2.3.2 in [1] definierte Feld **TrackFragmentExtendedHeaderBox** MUSS für jedes Fragment vorhanden sein.
1. Version 2 von **TrackFragmentExtendedHeaderBox** SOLLTE verwendet werden, um Mediensegmente mit identischen URLs in mehreren Rechenzentren zu generieren. Das Fragmentindexfeld ist ERFORDERLICH für das rechenzentrenübergreifende Failover indexbasierter Streamingformate wie z.B. Apple HLS und indexbasiertes MPEG-DASH. Zum Aktivieren des rechenzentrenübergreifenden Failovers MUSS der Fragmentindex zwischen mehreren Encodern synchronisiert und für jedes folgende Medienfragment jeweils um 1 erhöht werden, auch bei Neustarts und Fehlern der Encoder.
1. Im Abschnitt 3.3.6 in [1] wird das Feld mit der Bezeichnung **MovieFragmentRandomAccessBox** (**mfra**) definiert, das am Ende der Echtzeiterfassung gesendet werden KANN, um das Ende des Streams (End-of-Stream, EOS) für den Kanal anzugeben. Aufgrund der Erfassungslogik von Media Services ist die Verwendung von EOS (End-of-Stream) veraltet, und das Feld **mfra** für die Echtzeiterfassung SOLLTE NICHT gesendet werden. Wenn es dennoch gesendet wird, wird es in Media Services ohne Meldung ignoriert. Um den Status des Erfassungspunkts zurückzusetzen, empfehlen die Verwendung der [Kanalzurücksetzung](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Wir empfehlen auch die Verwendung des [Programmstopps](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) zum Beenden einer Präsentation und eines Streams.
1. Die Dauer der MP4-Fragmente SOLLTE konstant sein, um die Größe der Clientmanifeste zu reduzieren. Eine konstante MP4-Fragmentdauer verbessert auch die Clientdownloadheuristiken durch Verwendung von Wiederholungstags. Die Dauer KANN schwanken, um nicht ganzzahlige Frameraten auszugleichen.
1. Die Dauer des MP4-Fragments SOLLTE zwischen ca. 2 und 6 Sekunden liegen.
1. Zeitstempel und Indizes des MP4-Fragments (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` and `fragment_index`) SOLLTEN in aufsteigender Reihenfolge eingehen. Obwohl Fragmente in Media Services dupliziert werden können, sind die Möglichkeiten begrenzt, Fragmente entsprechend der Medienzeitachse neu anzuordnen.

## <a name="4-protocol-format--http"></a>4. Protokollformat – HTTP
Bei der auf fragmentiertem ISO-MP4-basierenden Echtzeiterfassung für Media Services wird eine HTTP POST-Standardanforderung mit langer Laufzeit verwendet, um codierte Mediendaten im fragmentierten MP4-Format an den Dienst zu übertragen. Jede HTTP POST-Anforderung sendet einen vollständigen Bitstrom in fragmentiertem MP4 („Stream“), der mit den Headerfeldern (**ftyp**, **Live Server Manifest Box** und **moov**) beginnt und mit einer Sequenz von Fragmenten (Felder **moof** und **mdat**) fortgesetzt wird. Informationen zur URL-Syntax für die HTTP POST-Anforderung finden Sie in Abschnitt 9.2 in [1]. Beispiel für die POST-URL: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requirements (Anforderungen)
Es folgen die detaillierten Anforderungen:

1. Der Encoder SOLLTE die Übertragung starten, indem eine HTTP POST-Anforderung mit leerem "Hauptteil" (Inhaltslänge 0) mit der gleichen Erfassungs-URL gesendet wird. Damit kann der Encoder schnell erkennen, ob der Echtzeiterfassungs-Endpunkt gültig ist, und ob Authentifizierung oder andere Bedingungen erforderlich sind. Über das HTTP-Protokoll kann der Server erst eine HTTP-Antwort zurücksenden, wenn die gesamte Anforderung, einschließlich des POST-Texts, empfangen wurde. Aufgrund der langen Laufzeit von Liveereignissen kann der Encoder ohne diesen Schritt Fehler möglicherweise erst erkennen, nachdem alle Daten gesendet wurden.
1. Der Encoder MUSS alle wegen (1) ausgegebenen Fehler oder Authentifizierungsanforderungen behandeln. Wenn (1) mit dem Antwortcode "200" erfolgreich ausgeführt wird, wird der Vorgang fortgesetzt.
1. Der Encoder MUSS eine neue HTTP POST-Anforderung mit dem Stream aus fragmentiertem MP4 beginnen. Die Nutzlast MUSS mit den Headerfeldern, gefolgt von Fragmenten, beginnen. Beachten Sie, dass die Felder **ftyp**, **Live Server Manifest Box** und **moov** (in dieser Reihenfolge) mit jeder Anforderung gesendet werden MÜSSEN, selbst wenn der Encoder erneut eine Verbindung herstellen muss, weil die vorhergehende Anforderung vor dem Ende des Streams beendet wurde. 
1. Der Encoder MUSS für das Hochladen die segmentierte Transfercodierung verwenden, da es nicht möglich ist, die gesamte Inhaltslänge des Liveereignisses vorherzusagen.
1. Nach dem Ende des Ereignisses und dem Senden des letzten Fragments MUSS der Encoder die Nachrichtensequenz für die segmentierte Transfercodierung beenden (bei den meisten HTTP-Clientstapeln erfolgt dies automatisch). Der Encoder MUSS warten, bis der Dienst den letzten Antwortcode zurückgibt, und dann die Verbindung beenden. 
1. Der Encoder DARF das in Abschnitt 9.2 in [1] beschriebene `Events()`-Nomen NICHT für die Echtzeiterfassung in Media Services verwenden.
1. Wenn die HTTP POST-Anforderung endet oder ein Timeout aufgrund eines TCP-Fehlers vor dem Ende des Streams auftritt, MUSS der Encoder eine neue POST-Anforderung mithilfe einer neuen Verbindung ausgeben und die vorausgehenden Anforderungen erfüllen. Der Encoder muss darüber hinaus die vorherigen beiden MP4-Fragmente für jede Spur im Stream neu senden und fortfahren, ohne eine Diskontinuität in der Medienzeitachse auszulösen. Durch das erneute Senden der letzten beiden MP4-Fragmente für jede Spur wird sichergestellt, dass keine Daten verloren gehen. Mit anderen Worten: Wenn ein Stream eine Audio- und eine Videospur enthält und bei der aktuellen POST-Anforderung ein Fehler auftritt, muss der Encoder eine neue Verbindung herstellen und die letzten zwei zuvor erfolgreich gesendeten Fragmente für die Audiospur sowie die letzten zwei zuvor erfolgreich gesendeten Fragmente für die Videospur erneut senden, um sicherzustellen, dass keine Daten verloren gehen. Der Encoder MUSS einen vorausschauenden Puffer der Medienfragmente beibehalten, den er beim Wiederherstellen der Verbindung erneut sendet.

## <a name="5-timescale"></a>5. Timescale
Unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) wird die Verwendung der Zeitskala für **SmoothStreamingMedia** (Abschnitt 2.2.2.1), **StreamElement** (Abschnitt 2.2.2.3), **StreamFragmentElement** (Abschnitt 2.2.2.6) und **LiveSMIL** (Abschnitt 2.2.7.3.1) beschrieben. Wenn der Zeitskalawert nicht vorhanden ist, wird der Standardwert 10.000.000 (10 MHz) verwendet. Obwohl die Spezifikation des Smooth Streaming-Formats die Verwendung anderer Zeitskalawerte nicht blockiert, verwenden die meisten Encoderimplementierungen diesen Standardwert (10 MHz), um Smooth Streaming-Erfassungsdaten zu generieren. Wegen der Funktion [Azure Media Services Dynamic Packaging](media-services-dynamic-packaging-overview.md) wird empfohlen, 90 kHz als Zeitskala für Videostreams sowie 44,1 kHz oder 48,1 kHz für Audiostreams zu verwenden. Wenn für verschiedene Streams unterschiedliche Zeitskalawerte verwendet werden, MUSS die Zeitskala der Streamebene gesendet werden. Weitere Informationen finden Sie unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definition von „Stream“
Ein „Stream ist die Basiseinheit für Vorgänge in der Echtzeiterfassung beim Erstellen von Livepräsentationen sowie beim Verarbeiten von Streamingfailovers und Redundanzszenarios. Ein „Stream“ ist definiert als ein eindeutiger fragmentierter MP4-Bitstrom, der eine oder mehrere Spuren enthalten kann. Eine vollständige Livepräsentation kann je nach Konfiguration der Liveencoder einen oder mehrere Streams enthalten. In den folgenden Beispielen werden verschiedene Optionen zum Erstellen einer vollständigen Livepräsentation unter Verwendung eines oder mehrerer Streams veranschaulicht.

**Beispiel:** 

Der Kunde möchte eine Livestreamingpräsentation erstellen, die die folgenden Audio- und Videobitraten enthält:

Video – 3.000 KBit/s, 1.500 KBit/s, 750 KBit/s

Audio – 128 KBit/s

### <a name="option-1-all-tracks-in-one-stream"></a>Option 1: Alle Spuren in einem Stream
Mit dieser Option generiert ein einzelner Encoder alle Audio-/Videospuren und bündelt diese dann in einem fragmentierten MP4-Bitstrom. Der fragmentierte MP4-Bitstrom wird dann über eine einzelne HTTP POST-Verbindung gesendet. In diesem Beispiel gibt es nur einen Stream für die Livepräsentation.

![Streams – eine Spur][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Option 2: Jede Spur in einem separaten Stream
Bei dieser Option fügt der Encoder jeweils nur eine Spur in jeden fragmentierten MP4-Bitstrom ein und sendet alle Streams über separate HTTP-Verbindungen. Dies kann mit einem Encoder oder mehreren Encodern erfolgen. Aus der Perspektive der Echtzeiterfassung besteht diese Livepräsentation aus vier Streams.

![Streams – separate Spuren][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Option 3: Bündeln der Audiospur mit der Videospur mit der niedrigsten Bitrate in einem Stream
Bei dieser Option wird die Audiospur mit der Videospur mit der niedrigsten Bitrate in einem fragmentierten MP4-Bitstrom gebündelt, die anderen zwei Videospuren werden als separate Streams gesendet. 

![Streams – Audio- und Videospuren][image4]

### <a name="summary"></a>Zusammenfassung
Dies ist keine vollständige Liste aller möglichen Erfassungsoptionen für dieses Beispiel. Tatsächlich werden bei der Echtzeiterfassung alle möglichen Gruppierungen der Spuren in Streams unterstützt. Basierend auf der technischen Komplexität, der Kapazität des Encoders sowie auf Überlegungen zu Redundanz und Failover können Kunden und Anbieter von Encodern ihre eigenen Implementierungen auswählen. In den meisten Fällen gibt es jedoch nur eine einzige Audiospur für die gesamte Livepräsentation. Daher ist es wichtig, die Integrität des Erfassungsstreams sicherzustellen, der die Audiospur enthält. Diese Überlegung führt häufig dazu, dass die Audiospur einen eigenen Stream erhält (wie in Option 2), oder dass sie mit der Videospur mit der niedrigsten Bitrate gebündelt wird (wie in Option 3). Auch im Hinblick auf eine bessere Redundanz und Fehlertoleranz wird das Senden der gleichen Audiospur in zwei verschiedenen Streams (Option 2 mit redundanten Audiospuren) oder die Bündelung der Audiospur in mindestens zwei Videospuren mit der niedrigsten Bitrate (Option 3 mit gebündeltem Audiostream in mindestens zwei Videostreams) für die Echtzeiterfassung in Media Services dringend empfohlen.

## <a name="7-service-failover"></a>7. Dienstfailover
Angesichts der Eigenschaften des Live-Streamings ist eine gute Failoverunterstützung von großer Bedeutung für die gewährleistete Verfügbarkeit des Diensts. Media Services ist so konzipiert, dass verschiedene Arten von Fehlern behoben werden können, darunter Netzwerkfehler, Serverfehler und Speicherprobleme. Bei Verwendung in Verbindung mit der geeigneten Failoverlogik des Liveencoders erhalten Kunden einen hoch zuverlässigen Livestreamingdienst in der Cloud.

In diesem Abschnitt werden Szenarios für Dienstfailover erörtert. In diesem Fall erfolgt der Fehler innerhalb des Diensts und tritt als Netzwerkfehler zutage. Es folgen einige Empfehlungen für die Encoder-Implementierung zur Behebung des Dienstfailovers:

1. Verwenden Sie für die Herstellung der TCP-Verbindung ein Timeout von 10 Sekunden. Wenn ein Versuch zum Herstellen der Verbindung länger als 10 Sekunden dauert, wird der Vorgang abgebrochen und wiederholt. 
1. Verwenden Sie ein kurzes Timeout für das Senden der HTTP-Anforderungsnachrichtenabschnitte. Wenn die Dauer des MP4-Zielfragments N Sekunden ist, verwenden Sie für das Senden ein Timeout zwischen N und 2 N Sekunden. Verwenden Sie z.B. ein Timeout von 6 bis 12 Sekunden, wenn die Dauer des MP4-Fragments 6 Sekunden beträgt. Wenn eine Zeitüberschreitung auftritt, setzen Sie die Verbindung zurück, öffnen Sie eine neue Verbindung, und setzen Sie die Erfassung des Streams über die neue Verbindung fort. 
1. Definieren Sie für jede Spur einen kontinuierlichen Puffer, der jeweils die letzten zwei Fragmente enthält, die erfolgreich und vollständig an den Dienst gesendet wurden.  Wenn die HTTP POST-Anforderung für einen Stream vor dem Ende des Streams beendet wird oder das Zeitlimit überschreitet, öffnen Sie eine neue Verbindung, und starten Sie eine neue HTTP POST-Anforderung. Senden Sie die Header des Streams sowie die letzten zwei Fragmente für jede Spur erneut, und setzen Sie den Stream ohne Fehlstellen in der Medienzeitachse fort. Dies verringert die Wahrscheinlichkeit eines Datenverlusts.
1. Es wird empfohlen, dass der Encoder die Anzahl der Wiederholungsversuche zum Herstellen einer Verbindung oder zum Fortsetzen des Streamings nach dem Auftreten eines TCP-Fehlers NICHT begrenzt.
1. Nach einem TCP-Fehler gilt Folgendes:
  
    a. Die aktuelle Verbindung MUSS getrennt werden, und eine neue Verbindung für eine neue HTTP POST-Anforderung MUSS hergestellt werden.

    b. Die neue HTTP POST-URL MUSS mit der ursprünglichen POST-URL identisch sein.
  
    c. Die neue HTTP POST-Anforderung MUSS Streamheader (Felder **ftyp**, **Live Server Manifest Box** und **moov**) enthalten, die mit den Streamheadern in der ursprünglichen POST-Anforderung identisch sind.
  
    d. Die letzten zwei Fragmente für jede Spur müssen erneut gesendet werden, und das Streaming muss ohne Fehlstellen in der Medienzeitachse fortgesetzt werden. Die Zeitstempel der MP4-Fragmente müssen kontinuierlich erhöht werden, auch bei mehreren HTTP POST-Anforderungen.
1. Der Encoder SOLLTE die HTTP POST-Anforderung beenden, wenn Daten nicht mit einer Rate gesendet werden, die der Dauer des MP4-Fragments entspricht.  Eine HTTP POST-Anforderung, die keine Daten sendet, kann verhindern, dass Media Services im Fall einer Aktualisierung des Diensts die Verbindung mit dem Encoder schnell trennt. Aus diesem Grund SOLLTEN die HTTP POST-Anforderungen für platzsparende Spuren (Signal für Werbung) kurzlebig sein und beendet werden, sobald das Fragment gesendet wurde.

## <a name="8-encoder-failover"></a>8. Encoderfailover
Das Encoder-Failover ist das zweite Failoverszenario, das für die Übermittlung des End-to-End-Live-Streamings betrachtet werden muss. In diesem Szenario tritt der Fehler beim Encoder auf. 

![Encoderfailover][image5]

Die folgenden Erwartungen gelten im Hinblick auf den Echtzeiterfassungs-Endpunkt, wenn ein Encoderfailover auftritt:

1. Eine neue Encoder-Instanz SOLLTE erstellt werden, um das Streaming fortzusetzen, wie in der Abbildung dargestellt (Stream für Video mit einer Größe von 3.000 K mit einer gestrichelten Linie dargestellt).
1. Der neue Encoder MUSS die gleiche URL für HTTP POST-Anforderungen verwenden wie die Instanz, bei der der Fehler aufgetreten ist.
1. Die POST-Anforderung des neuen Encoders MUSS die gleichen Fragmented MP4-Headerfelder enthalten wie die Instanz, bei der der Fehler aufgetreten ist.
1. Der neue Encoder MUSS ordnungsgemäß mit allen anderen für die gleiche Livepräsentation ausgeführten Encodern synchronisiert werden, sodass synchronisierte Audio- und Videobeispiele mit abgeglichenen Fragmentgrenzen generiert werden.
1. Der neue Stream MUSS dem vorherigen Stream semantisch entsprechen und auf Header- und Fragmentebene austauschbar sein.
1. Der neue Encoder SOLLTE versuchen, den Datenverlust zu minimieren. Die Werte für `fragment_absolute_time` und `fragment_index` von Medienfragmenten SOLLTEN ab dem Punkt steigen, an dem der Encoder zuletzt beendet wurde. Die Werte für `fragment_absolute_time` und `fragment_index` SOLLTEN kontinuierlich steigen, können jedoch bei Bedarf eine Fehlstelle aufweisen. Media Services ignoriert Fragmente, die bereits empfangen und verarbeitet wurden. Daher ist es besser, im Zweifelsfall Fragmente erneut zu senden, als Fehlstellen in die Medienzeitachse einzuführen. 

## <a name="9-encoder-redundancy"></a>9. Encoderredundanz
Für bestimmte kritische Liveereignisse, die eine noch höhere Verfügbarkeit und Quality of Experience erfordern, sollten Sie redundante Aktiv-Aktiv-Encoder verwenden, um ein nahtloses Failover ohne Datenverlust zu erreichen.

![Encoderredundanz][image6]

Wie in dieser Abbildung dargestellt, gibt es zwei Gruppen von Encodern, die mithilfe von Push zwei Kopien jedes Streams gleichzeitig in den Livestreamingdienst übertragen. Dieses Setup wird unterstützt, da Media Services doppelte Fragmente basierend auf der Stream-ID und dem Zeitstempel des Fragments herausfiltern kann. Im resultierenden Livestream und Archiv sind die bestmöglichen Streams aus den beiden Quellen in einer einzelnen Kopie aller Streams zusammengefasst. In einem hypothetischen Extremfall ist der resultierende Livestream aus dem Dienst kontinuierlich ohne Datenverlust, solange zu einem bestimmten Zeitpunkt jeweils ein Encoder für jeden Stream ausgeführt wird (dabei muss es sich nicht um den gleichen Encoder handeln). 

Die Anforderungen für dieses Szenario sind nahezu identisch mit den Anforderungen für das Szenario des „Encoderfailovers“, mit der Ausnahme, dass die zweite Gruppe von Encodern zur gleichen Zeit wie die primären Encoder ausgeführt wird.

## <a name="10-service-redundancy"></a>10. Dienstredundanz
Für eine hoch redundante globale Verteilung müssen Sie manchmal eine regionsübergreifende Sicherung durchführen, um regionalen Ausfällen entgegenzuwirken. Durch Erweiterung der Topologie unter „Encoderredundanz“ können Kunden eine redundante Dienstbereitstellung in einer anderen Region festlegen, die mit der zweiten Gruppe von Encodern verbunden ist. Kunden können zudem mit einem Content Delivery Network-Anbieter zusammenarbeiten, um einen Global Traffic Manager vor den beiden Dienstbereitstellungen bereitzustellen und so den Clientdatenverkehr nahtlos weiterzuleiten. Die Anforderungen für die Encoder sind mit denen bei der „Encoderredundanz“ identisch. Die einzige Ausnahme ist, dass die zweite Gruppe von Encodern auf einen anderen Echtzeiterfassungs-Endpunkt verwiesen werden muss. Im folgenden Diagramm wird dieses Setup veranschaulicht:

![Dienstredundanz][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Spezielle Typen von Erfassungsformaten
In diesem Abschnitt werden spezielle Formattypen für die Echtzeiterfassung erörtert, die für spezifische Szenarios entwickelt wurden.

### <a name="sparse-track"></a>Platzsparende Spur
Bei der Übermittlung einer Livestreamingpräsentation mit einer Rich Client-Benutzeroberfläche ist es häufig erforderlich, zeitsynchronisierte Ereignisse oder Signale in-band mit den wichtigsten Mediendaten zu übertragen. Ein Beispiel hierfür ist das dynamische Einfügen von Livewerbung. Diese Art der Ereignissignalisierung unterscheidet sich aufgrund ihres selteneren Vorkommens vom regulären Audio- und Videostreaming. Mit anderen Worten: Die Signalisierungsdaten werden normalerweise nicht kontinuierlich gesendet, und das jeweilige Intervall kann nur schwer vorhergesagt werden. Das Konzept von platzsparenden Spuren wurde entwickelt, um In-Band-Signalisierungsdaten zu erfassen und zu übertragen.

Die folgenden Schritte sind eine empfohlene Implementierung für die Erfassung von platzsparenden Spuren:

1. Erstellen Sie einen separaten fragmentierten MP4-Bitstrom, der nur platzsparende Spuren ohne Audio- und Videospuren enthält.
1. Verwenden Sie im Feld **Live Server Manifest Box** entsprechend der Definition im Abschnitt 6 in [1] den Parameter *parentTrackName*, um den Namen der übergeordneten Spur anzugeben. Weitere Informationen finden Sie in Abschnitt 4.2.1.2.1.2 in [1].
1. Im Feld **Live Server Manifest Box** MUSS **manifestOutput** auf **true** gesetzt werden.
1. Aufgrund des seltenen Eintretens des Signalisierungsereignisses wird Folgendes empfohlen:
   
    a. Zu Beginn des Liveereignisses sendet der Encoder die ursprünglichen Headerfelder an den Dienst, sodass der Dienst die platzsparende Spur im Clientmanifest registrieren kann.
   
    b. Der Encoder SOLLTE die HTTP POST-Anforderung beenden, wenn keine Daten gesendet werden. Eine HTTP POST-Anforderung mit langer Laufzeit, die keine Daten sendet, kann verhindern, dass Media Services im Fall einer Aktualisierung des Diensts oder Serverneustarts die Verbindung mit dem Encoder schnell trennt. In diesen Fällen ist der Medienserver vorübergehend in einem Empfangsvorgang auf dem Socket blockiert.
   
    c. In dem Zeitraum, in dem keine Signalisierungsdaten vorhanden sind, SOLLTE der Encoder die HTTP POST-Anforderung schließen. Während die POST-Anforderung aktiv ist, SOLLTE der Encoder Daten senden.

    d. Beim Senden von platzsparenden Fragmenten kann der Encoder einen expliziten Inhaltslängenheader festlegen, sofern dieser vorhanden ist.

    e. Beim Senden von platzsparenden Fragmenten über eine neue Verbindung SOLLTE der Encoder das Senden mit den Headerfeldern starten, gefolgt von den neuen Fragmenten. Dies gilt für den Fall, dass inzwischen ein Failover aufgetreten ist und die neue Verbindung mit einem neuen Server hergestellt wird, auf dem die platzsparende Spur noch nicht bekannt ist.

    f. Das Fragment der platzsparenden Spur wird für den Client zur Verfügung gestellt, wenn das Fragment der entsprechenden übergeordneten Spur mit dem gleichen oder einem größeren Zeitstempelwert für den Client zur Verfügung gestellt wird. Wenn für das platzsparende Fragment beispielsweise der Zeitstempel t=1.000 festgelegt ist, wird erwartet, dass der Client das platzsparende Fragment mit t=1.000 herunterladen kann, nachdem er für das Fragment „Video“ (angenommen, der Name der übergeordneten Spur lautet „Video“) den Zeitstempel t=1.000 oder höher erkannt hat. Beachten Sie, dass das tatsächliche Signal sehr wohl auch für eine andere Position in der Zeitachse der Präsentation für seinen eigentlichen Zweck verwendet werden kann. In diesem Beispiel ist es möglich, dass das platzsparende Fragment mit t=1.000 eine XML-Nutzlast aufweist, die für das Einfügen einer Werbung in einer Position definiert ist, die zeitlich einige Sekunden später folgt.

    g. Die Nutzlast der Fragmente der platzsparenden Spur kann je nach Szenario in verschiedenen Formaten (z.B. XML, Text oder binär) vorliegen.

### <a name="redundant-audio-track"></a>Redundante Audiospur
In einem typischen HTTP Adaptive Streaming-Szenario (z.B. Smooth Streaming oder DASH) befindet sich häufig nur eine Audiospur in der gesamten Präsentation. Im Gegensatz zu Videospuren, die über mehrere Qualitätsstufen verfügen, aus denen sich der Client bei Fehlerzuständen eine Stufe auswählen kann, kann die Audiospur eine einzelne Fehlerquelle sein, wenn die Erfassung des Streams, der die Audiospur enthält, fehlerhaft ist. 

Um dieses Problem zu beheben, unterstützt Media Services die Echtzeiterfassung redundanter Audiospuren. Dahinter steht die Idee, dass die gleiche Audiospur mehrmals in anderen Streams gesendet werden kann. Der Dienst registriert zwar die Audiospur nur einmal im Clientmanifest, kann jedoch redundante Audiospuren als Sicherungskopien zum Abrufen von Audiofragmenten verwenden, wenn bei der primären Audiospur Probleme auftreten. Zur Erfassung redundanter Audiospuren gelten folgende Bedingungen für den Encoder:

1. Der Encoder muss die gleiche Audiospur in mehreren fragmentierten MP4-Bitströmen erstellen. Die redundanten Audiospuren MÜSSEN semantisch den gleichen Fragmentzeitstempeln entsprechen und auf Header- und Fragmentebene austauschbar sein.
1. Der Encoder muss sicherstellen, dass der Eintrag „audio“ im „Live Server Manifest“ (siehe Abschnitt 6 in [1]) für alle redundanten Audiospuren identisch ist.

Die folgende Implementierung wird für redundante Audiospuren empfohlen:

1. Senden Sie jede eindeutige Audiospur allein in einem separaten Stream. Senden Sie auch einen redundanten Stream für jeden dieser Audiospurstreams, wobei sich der zweite Stream vom ersten Stream nur durch den Bezeichner in der HTTP POST-URL unterscheidet: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
1. Verwenden Sie separate Streams, um die Videos mit den beiden niedrigsten Bitraten zu senden. Jeder dieser Streams SOLLTE auch eine Kopie jeder eindeutigen Audiospur enthalten. Wenn beispielsweise mehrere Sprachen unterstützt werden, SOLLTEN diese Streams Audiospuren für jede Sprache enthalten.
1. Verwenden Sie separate Serverinstanzen (Encoder) zum Codieren und Senden der in (1) und (2) genannten redundanten Streams. 

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
