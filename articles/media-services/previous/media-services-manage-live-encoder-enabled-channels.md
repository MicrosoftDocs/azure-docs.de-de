---
title: Livestreaming mit Azure Media Services zum Erstellen von Multi-Bitrate-Datenströmen | Microsoft Docs
description: 'In diesem Thema wird beschrieben, wie ein Kanal eingerichtet wird, von dem ein Single-Bitrate-Livedatenstrom aus einem lokalen Encoder empfangen und eine Livecodierung zu einem Datenstrom mit adaptiver Bitrate mit Media Services ausgeführt wird. Der Datenstrom kann über einen oder mehrere Streamingendpunkte an Wiedergabe-Clientanwendungen übermittelt werden. Hierzu dienen die folgenden adaptiven Streamingprotokolle: HLS, Smooth Stream, MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: c168182f0b34329ed3e72e90ce86456dfbe210ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217192"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Livestreaming mit Azure Media Services zum Erstellen von Multi-Bitrate-Datenströmen

> [!NOTE]
> Ab dem 12. Mai 2018 unterstützen Livekanäle nicht mehr das Erfassungsprotokoll zum RTP/MPEG-2-Transportdatenstrom. Migrieren Sie von Erfassungsprotokollen für RTP/MPEG-2 zu Erfassungsprotokollen für RTMP oder fragmentiertem MP4 (Smooth Streaming).

## <a name="overview"></a>Übersicht
In Azure Media Services (AMS) stellt ein **Kanal** eine Pipeline für die Verarbeitung von Livestreaminginhalten dar. Es gibt zwei Arten, auf die Live-Eingabedatenströme von **Kanälen** empfangen werden können:

* Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an den Kanal, der zum Ausführen der Live-Codierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Kanal wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Multi-Bitrate-Videodatenstrom (adaptiv) ausgeführt. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.
* Von einem lokalen Liveencoder wird Multi-Bitrate-basiertes **RTMP** oder **Smooth Streaming** (fragmentiertes MP4) an den Kanal gesendet, der nicht für die Livecodierung mit AMS aktiviert ist. Die erfassten Streams durchlaufen **Kanäle** ohne weitere Verarbeitung. Diese Methode wird als **Pass-Through-Methode** bezeichnet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco und Elemental. Die folgenden Liveencoder geben RTMP aus: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek und Tricaster.  Ein Liveencoder kann auch einen Single-Bitrate-Datenstrom an einen Kanal senden, der nicht für die Livecodierung konfiguriert ist. Dies wird jedoch nicht empfohlen. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.

  > [!NOTE]
  > Die Verwendung der Pass-Through-Methode ist die wirtschaftlichste Form des Livestreamings.
  > 
  > 

Mit Media Services-Version 2.10 können Sie beim Erstellen eines Kanals angeben, wie der Eingabedatenstrom vom Kanal empfangen werden soll und ob vom Kanal eine Livecodierung des Datenstroms ausgeführt werden soll. Sie haben zwei Möglichkeiten:

* **None** (Keine): Geben Sie diesen Wert an, wenn ein lokaler Liveencoder verwendet werden soll, von dem ein Multi-Bitrate-Datenstrom (Pass-Through-Datenstrom) ausgegeben wird. In diesem Fall wird der Eingabedatenstrom ohne Codierung an die Ausgabe geleitet. Bis zur Version 2.10 ist dies das Standardkanalverhalten.  Ausführlichere Informationen zum Arbeiten mit Kanälen dieses Typs finden Sie unter [Livestreaming mit lokalen Encodern, die Multi-Bitrate-Datenströme erstellen](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** : Wählen Sie diesen Wert, wenn Sie Media Services verwenden möchten, um einen Single-Bitrate-Livedatenstrom in einen Multi-Bitrate-Datenstrom zu codieren. Beachten Sie, dass das Live Encoding sich auf die Abrechnung auswirkt und dass die Beibehaltung des Status „Wird ausgeführt“ für einen Live Encoding-Kanal mit Gebühren verbunden ist.  Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden.

> [!NOTE]
> In diesem Thema werden die Attribute der Kanäle erläutert, welche zum Ausführen der Livecodierung aktiviert sind (Codierungstyp**Standard** ). Informationen zum Arbeiten mit Kanälen, die nicht zum Ausführen der Livecodierung aktiviert sind, finden Sie unter [Livestreaming mit lokalen Encodern, die Multi-Bitrate-Datenströme erstellen](media-services-live-streaming-with-onprem-encoders.md).
> 
> Lesen Sie unbedingt den Abschnitt [Überlegungen](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Hinweise zur Gebührenberechnung
Für einen Live Encoding-Kanal beginnt die Berechnung von Gebühren, sobald der Status über die API in „Wird ausgeführt“ geändert wird.   Sie können den Status auch im Azure-Portal oder im Azure Media Services-Explorer anzeigen (https://aka.ms/amse).

In der folgenden Tabelle ist dargestellt, wie Kanalzustände den Abrechnungszuständen in der API und im Azure-Portal zugeordnet werden. Die Zustände zwischen der API und der Benutzeroberfläche des Portals unterscheiden sich leicht. Sobald ein Kanal über die API in den Zustand „Wird ausgeführt“ oder im Azure-Portal in den Zustand „Bereit“ oder „Streaming“ versetzt wird, wird die Abrechnung aktiv.
Um die weitere Gebührenberechnung für den Kanal zu beenden, müssen Sie den Kanal über die API oder im Azure-Portal beenden.
Sie sind für das Beenden Ihrer Kanäle verantwortlich, wenn Sie die Nutzung des Live Encoding-Kanals abgeschlossen haben.  Wenn Sie einen Codierkanal nicht beenden, werden weiter Gebühren berechnet.

### <a id="states"></a>Kanalstatus und ihre Zuordnung zum Abrechnungsmodus
Dies ist der aktuelle Status des Kanals. Mögliche Werte sind:

* **Stopped**(Beendet): Dies ist der anfängliche Status des Kanals nach seiner Erstellung (es sei denn, im Portal wurde das automatische Starten gewählt). Dies ist der anfängliche Status des Kanals nach seiner Erstellung (es sei denn, im Portal wurde das automatische Starten gewählt). In diesem Status werden keine Gebühren berechnet. Ein Streaming ist jedoch nicht zulässig.
* **Wird gestartet**. Der Kanal wird gestartet. In diesem Status werden keine Gebühren berechnet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, wird der Kanal in den Status „Stopped“ (Angehalten) geschaltet.
* **Wird ausgeführt**. Vom Kanal können Livestreams verarbeitet werden. Die Nutzung wird jetzt berechnet. Sie müssen den Kanal beenden, um zu verhindern, dass weiter Gebühren berechnet werden. 
* **Wird beendet**. Der Kanal wird beendet. In diesem Übergangszustand erfolgt keine Berechnung. In diesem Status sind weder Updates noch Streaming zulässig.
* **Wird gelöscht**. Der Kanal wird gelöscht. In diesem Übergangszustand erfolgt keine Berechnung. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus mit den Abrechnungsmodi aufgeführt. 

| Kanalstatus | Portal-UI-Indikatoren | Werden Gebühren berechnet? |
| --- | --- | --- |
| Wird gestartet |Wird gestartet |Nein (Übergangsstatus) |
| Wird ausgeführt |Bereit (keine ausgeführten Programme)<br/>oder<br/>Streaming (mindestens ein ausgeführtes Programm) |JA |
| Wird beendet |Wird beendet |Nein (Übergangsstatus) |
| Beendet |Beendet |Nein |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisches Abschalten nicht verwendeter Kanäle
Seit dem 25. Januar 2016 stellt Media Services ein Update bereit, das einen Kanal (mit aktiviertem Live Encoding) automatisch beendet, nachdem er für einen längeren Zeitraum nicht verwendet wurde. Dies gilt für Kanäle ohne aktive Programme, die seit längerer Zeit keine Eingabe erhalten haben.

Der Schwellenwert für einen Zeitraum ohne Verwendung beträgt nominell 12 Stunden, Änderung allerdings vorbehalten.

## <a name="live-encoding-workflow"></a>Live Encoding-Workflow
Im folgenden Diagramm ist ein Livedatenstrom-Workflow dargestellt, bei dem ein Single-Bitrate-Datenstrom empfangen (in einem der folgenden Protokolle: RTMP oder Smooth Streaming) und dann in einen Multi-Bitrate-Datenstrom codiert wird. 

![Liveworkflow][live-overview]

## <a id="scenario"></a>Allgemeines Livestreamingszenario
Im Folgenden werden grundlegende Schritte zum Erstellen allgemeiner Livestreaminganwendungen erläutert.

> [!NOTE]
> Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen. Das Live Encoding wirkt sich auf die Abrechnung aus, und die Beibehaltung des Status „Wird ausgeführt“ für einen Live Encoding-Kanal ist mit stundenweisen Gebühren verbunden.  Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden. 

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein **Single**-Bitrate-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP oder Smooth Streaming. 

    Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.
2. Erstellen Sie einen Kanal, und starten Sie ihn. 
3. Rufen Sie die Erfassungs-URL des Kanals ab. 

    Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
4. Rufen Sie die Vorschau-URL des Kanals ab. 

    Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.
5. Erstellen Sie ein Programm. 

    Im Azure-Portal wird beim Erstellen eines Programms auch ein Medienobjekt erstellt. 

    Wenn Sie hingegen .NET SDK oder REST verwenden, müssen Sie beim Erstellen eines Programms ein Medienobjekt erstellen und angeben, dass es verwendet werden soll. 
6. Veröffentlichen Sie das mit dem Programm verknüpfte Medienobjekt.   

    >[!NOTE]
    >Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, muss sich im Status **Wird ausgeführt** befinden. 

7. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
8. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
9. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
10. Löschen Sie das Programm (und optional das Medienobjekt).   

> [!NOTE]
> Es ist sehr wichtig, das Beenden eines Live Encoding-Kanals nicht zu vergessen. Beachten Sie, dass das Live Encoding stundenweise abgerechnet wird und dass die Beibehaltung des Status „Wird ausgeführt“ für einen Live Encoding-Kanal mit Gebühren verbunden ist.  Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden. 
> 
> 

## <a id="channel"></a>Eingabe-/Erfassungskonfigurationen des Kanals
### <a id="Ingest_Protocols"></a>Erfassungsstreamingprotokoll
Wenn der **Encodertyp** die Einstellung **Standard** aufweist, gibt es folgende gültige Optionen:

* Single-Bitrate **RTMP**
* Single-Bitrate **Fragmentiertes MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>Single-Bitrate-RTMP
Überlegungen:

* Der eingehende Datenstrom darf kein Multi-Bitrate-Video enthalten.
* Der Videodatenstrom muss eine durchschnittliche Bitrate unter 15-Mbit/s aufweisen.
* Der Audiodatenstrom muss eine durchschnittliche Bitrate unter 1-Mbit/s aufweisen.
* Folgende Codecs werden unterstützt:
* MPEG-4 AVC/H.264-Video
* Baseline, Main, High Profile (8-Bit 4:2:0)
* High 10 Profile (10-Bit 4:2:0)
* High 422 Profile (10-Bit 4:2:2)
* MPEG-2 AAC-LC-Audio
* Mono, Stereo, Surround (5.1, 7.1)
* 44,1 kHz-Samplingrate
* ADTS Paketerstellung im MPEG-2-Format
* Zu den empfohlenen Encodern gehören:
* Telestream Wirecast
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmentiertes Single-Bitrate-MP4 (Smooth Streaming)
Typisches Anwendungsbeispiel:

Verwenden Sie lokale Liveencoder von Herstellern wie Elemental Technologies, Ericsson, Ateme oder Envivio, um den Eingabedatenstrom über das offene Internet an ein Azure-Rechenzentrum in der Nähe zu senden.

Überlegungen:

Wie bei [Single-Bitrate-RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Weitere Überlegungen
* Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.
* Die maximale Auflösung für den eingehenden Videodatenstrom beträgt 1920 x 1080, und höchstens 60 Felder pro Sekunde (verschachtelt) bzw. 30 Bilder pro Sekunde (progressiv).

### <a name="ingest-urls-endpoints"></a>Erfassungs-URLs (Endpunkte)
Mit einem Kanal ist ein Eingabeendpunkt (Erfassungs-URL) verfügbar, den Sie im Liveencoder angeben, damit vom Encoder Datenströme an Ihre Kanäle übertragen werden können.

Wenn Sie einen Kanal erstellen, können Sie die Erfassungs-URLs abrufen. Der Kanal muss dazu nicht den Status **Running** (Wird ausgeführt) aufweisen. Wenn Sie beginnen möchten, Daten an den Kanal zu senden, ist der Status **Running** (Wird ausgeführt) hingegen erforderlich. Wenn die Datenerfassung durch den Kanal begonnen hat, können Sie über die Vorschau-URL eine Vorschau Ihres Datenstroms ansehen.

Sie können optional einen Livedatenstrom mit fragmentiertem MP4 (Smooth Streaming) über eine SSL-Verbindung erfassen. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. AMS unterstützt derzeit SSL mit benutzerdefinierten Domänen nicht.  

### <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen
Sie können die IP-Adressen definieren, die zum Veröffentlichen von Videos in diesem Kanal zugelassen sind. Zulässige IP-Adressen können als einzelne IP-Adresse (Beispiel: 10.0.0.1), als IP-Adressbereiche mit einer IP-Adresse und einer CIDR-Subnetzmaske (Beispiel: 10.0.0.1/22) oder als IP-Adressbereiche mit einer IP-Adresse und einer Subnetzmaske in Punkt-Dezimalschreibweise (Beispiel: 10.0.0.1(255.255.252.0)) angegeben werden.

Wenn keine IP-Adressen angegeben werden und keine Regeldefinition vorhanden ist, so ist keine IP-Adresse zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen 0.0.0.0/0 fest.

## <a name="channel-preview"></a>Kanalvorschau
### <a name="preview-urls"></a>Vorschau-URLs
Mit den Kanälen ist ein Vorschauendpunkt (Vorschau-URL) verfügbar, mit dem Sie eine Vorschau anzeigen und Ihren Datenstrom vor der weiteren Verarbeitung und Übermittlung überprüfen können.

Wenn Sie einen Kanal erstellen, können Sie die Vorschau-URL abrufen. Der Kanal muss zum Abrufen der URL nicht den Status **Running** (Wird ausgeführt) aufweisen.

Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

> [!NOTE]
> Zurzeit kann die Vorschau des Datenstroms unabhängig vom angegebenen Eingabetyp nur in fragmentiertem MP4-Format (Smooth Streaming) geliefert werden.  Sie können einen Player verwenden, der im Azure-Portal gehostet wird, um Ihren Datenstrom anzuzeigen.
> 
> 

### <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen
Sie können die IP-Adressen definieren, die zum Herstellen einer Verbindung mit dem Vorschauendpunkt zugelassen sind. Wenn keine IP-Adressen angegeben werden, wird jede IP-Adresse zugelassen. Zulässige IP-Adressen können als einzelne IP-Adresse (Beispiel: 10.0.0.1), als IP-Adressbereiche mit einer IP-Adresse und einer CIDR-Subnetzmaske (Beispiel: 10.0.0.1/22) oder als IP-Adressbereiche mit einer IP-Adresse und einer Subnetzmaske in Punkt-Dezimalschreibweise (Beispiel: 10.0.0.1(255.255.252.0)) angegeben werden.

## <a name="live-encoding-settings"></a>Einstellungen für die Livecodierung
In diesem Abschnitt wird beschrieben, wie die Einstellungen für den Liveencoder innerhalb des Kanals angepasst werden können, wenn der **Codierungstyp** des Kanals die Einstellung **Standard** aufweist.

> [!NOTE]
> Ihr Beitragsfeed kann nur eine Audiospur enthalten – das Erfassen mehrerer Audiospuren wird derzeit nicht unterstützt. Beim Ausführen der Livecodierung mit [lokalen Liveencodern](media-services-live-streaming-with-onprem-encoders.md) können Sie einen Beitragsfeed im Smooth Streaming-Protokoll mit mehreren Audiospuren senden.
> 
> 

### <a name="ad-marker-source"></a>Quelle für AD-Marker
Sie können die Quelle für AD-Markersignale angeben. Der Standardwert lautet **Api**, der Liveencoder im Kanal soll also eine asynchrone **AD-Marker-API** überwachen.

### <a name="cea-708-closed-captions"></a>CEA 708-Untertitel
Dies ist ein optionales Kennzeichen für den Liveencoder, CEA 708-Untertiteldaten, die in das eingehende Video eingebettet sind, zu ignorieren. Wenn das Kennzeichen die Einstellung „False“ (Standard) aufweist, werden CEA 708-Daten erkannt und in die Ausgabe-Videodatenströme eingefügt.

#### <a name="index"></a>Index
Es wird empfohlen, als Eingabe einen Single-Program-Transportdatenstrom (SPTS) zu verwenden. Wenn der Eingabedatenstrom mehrere Programme enthält, wird die Programmkartentabelle (PMT) in der Eingabe vom Liveencoder analysiert. Eingaben, welche die Datenstrom-Typnamen „MPEG-2 AAC ADTS“, „AC-3 System-A“, „AC-3 System-B“, „MPEG-2 Private PES“, „MPEG-1 Audio“ oder „MPEG-2 Audio“ aufweisen, werden identifiziert und in der Reihenfolge angeordnet, die in der PMT angegeben ist. Der nullbasierte Index wird zum Abrufen des n-ten Eintrags in dieser Anordnung verwendet.

#### <a name="language"></a>Sprache
Dies ist die Sprachen-ID des Audiodatenstroms entsprechend ISO 639-2, z. B. „DEU“. Wenn keine ID vorhanden ist, gilt die Standardeinstellung „UND“ (undefiniert).

### <a id="preset"></a>Systemvoreinstellung
Hier ist die Voreinstellung angegeben, die vom Liveencoder in diesem Kanal verwendet werden soll. Derzeit lautet der einzig zulässige Wert **Default720p** (Standard).

Beachten Sie, dass Sie sich an amslived@microsoft.com wenden müssen, wenn Sie benutzerdefinierte Voreinstellungen verwenden möchten.

Mit **Default720p** wird das Video in die folgenden 6 Ebenen codiert.

#### <a name="output-video-stream"></a>Ausgabe-Videodatenstrom

| BitRate | Breite | Höhe | Max. Bilder/s | Profil | Name des Ausgabedatenstroms |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hoch |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hoch |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hoch |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hoch |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hoch |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hoch |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Ausgabe-Audiodatenstrom

Audio wird mit einer Samplingrate von 48 kHz in Stereo-AAC-LC mit 128 KBit/s codiert.

## <a name="signaling-advertisements"></a>Signalisieren von Werbespots
Wenn Livecodierung bei Ihrem Kanal aktiviert ist, verfügen Sie über eine videoverarbeitende Komponente in der Pipeline, die Sie bearbeiten können. Sie können durch den Kanal Slates und/oder Werbespots in den ausgehenden Datenstrom mit adaptiver Bitrate einfügen lassen. Bei Slates handelt es sich um Standbilder, mit denen Sie den Live-Eingabefeed in bestimmten Situationen (z. B. bei Werbepausen) verdecken können. Bei Werbesignalen handelt es sich um zeitlich synchronisierte Signale, die Sie in den ausgehenden Datenstrom einbinden, damit vom Videoplayer bestimmte Aktionen ausgeführt werden, z. B. Umschalten zu einem Werbespot um eine bestimmte Zeit. In diesem [Blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) finden Sie einen Überblick über den SCTE-35-Signalmechanismus, der zu diesem Zweck verwendet wird. Im Folgenden wird ein typisches Szenario beschrieben, das Sie in Ihr Liveereignis implementieren könnten.

1. Zeigen Sie Ihren Zuschauern ein PRE-EVENT-Bild an, bevor das Programmereignis beginnt.
2. Zeigen Sie Ihren Zuschauern ein POST-EVENT-Bild an, wenn das Programmereignis endet.
3. Zeigen Sie Ihren Zuschauern ein ERROR-EVENT-Bild an, wenn während des Programmereignisses ein Fehler (z. B. Stromausfall) auftritt.
4. Zeigen Sie Ihren Zuschauern ein AD-BREAK-Bild an, um den Liveereignisfeed während Werbepausen auszublenden.

Beim Signalisieren von Werbespots können Sie die folgenden Eigenschaften festlegen: 

### <a name="duration"></a>Duration
Dies ist die Dauer der Werbepause in Sekunden. Es muss sich um einen positiven Wert ungleich null handeln, damit die Werbepause gestartet werden kann. Wird eine Werbepause ausgeführt, und die Dauer für die CueId der aktuellen Werbepause lautet null, so wird die Werbepause abgebrochen.

### <a name="cueid"></a>CueId
Dies ist der eindeutige Bezeichner einer Werbepause, der von nachgeschalteten Anwendungen verwendet wird, um entsprechende Aktionen auszuführen. Es muss sich um eine positive ganze Zahl handeln. Sie können für diesen Wert eine zufällige positive ganze Zahl festlegen oder zum Nachverfolgen der CueIds ein vorgeschaltetes System verwenden. Achten Sie darauf, alle IDs in positive ganze Zahlen zu normalisieren, bevor Sie diese über die API senden.

### <a name="show-slate"></a>Show slate (Slate anzeigen)
Optional. Hierdurch wird dem Liveencoder signalisiert, bei Werbepausen zum [Standard-Slatebild](media-services-manage-live-encoder-enabled-channels.md#default_slate) zu wechseln und den eingehenden Videodatenstrom auszublenden. Im Slatezustand wird auch die Audioausgabe stummgeschaltet. Die Standardeinstellung lautet **false**. 

Das zu verwendende Bild wird zum Zeitpunkt der Kanalerstellung über die Eigenschaft „Default slate asset Id“ angegeben. Für das Slatebild wird Stretching durchgeführt, um es an die Anzeigebildgröße anzupassen. 

## <a name="insert-slate--images"></a>Einfügen von Slatebildern
Dem Liveencoder im Kanal kann signalisiert werden, zu einem Slatebild zu wechseln. Auch das Beenden eines aktuellen Slates kann signalisiert werden. 

Der Liveencoder kann so konfiguriert werden, dass in bestimmten Situationen, z. B. bei Werbepausen, zu einem Slatebild gewechselt und das eingehende Videosignal ausgeblendet wird. Wird kein solches Slate konfiguriert, so wird das eingehende Videosignal während dieser Werbepause nicht maskiert.

### <a name="duration"></a>Duration (Dauer)
Dies ist die Dauer des Slates in Sekunden. Es muss sich um einen positiven Wert ungleich null handeln, damit das Slate gestartet werden kann. Wird ein Slate ausgeführt und eine Dauer von null angegeben, so wird das aktuelle Slate beendet.

### <a name="insert-slate-on-ad-marker"></a>Insert slate on ad marker (Slate bei AD-Marker einfügen)
Wenn für diese Einstellung der Wert „true“ festgelegt ist, wird der Liveencoder zum Einfügen eines Slatebilds während Werbepausen konfiguriert. Der Standardwert lautet „true“. 

### <a id="default_slate"></a>Default slate Asset Id (ID des Slate-Standardmedienobjekts)

Optional. Hier wird die ID des Media Services-Medienobjekts angegeben, welches das Slatebild enthält. Der Standardwert lautet null. 


> [!NOTE] 
> Vor dem Erstellen des Kanals muss das Slate-Bild mit den folgenden Einschränkungen als dediziertes Medienobjekt hochgeladen werden (in diesem Medienobjekt dürfen sich keine anderen Dateien befinden). Dieses Bild wird nur verwendet, wenn der Liveencoder aufgrund einer Werbepause ein Slate einfügt oder das Einfügen eines Slates explizit veranlasst wurde. Es ist derzeit nicht möglich, ein benutzerdefiniertes Bild zu verwenden, wenn das Eingangssignal des Liveencoders verloren geht. Für dieses Feature können Sie [hier](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel) abstimmen.


* Auflösung von höchstens 1920 x 1080
* Größe von maximal 3 MB
* Der Dateinamen muss die Erweiterung *JPG aufweisen.
* Das Bild muss als einzige AssetFile in ein Medienobjekt hochgeladen werden. Diese AssetFile sollte als primäre Datei gekennzeichnet werden. Bei dem Medienobjekt ist keine Speicherverschlüsselung möglich.

Wird keine **default slate Asset Id** (ID des Slate-Standardmedienobjekts) angegeben, und die Option **insert slate on ad marker** (Slate bei AD-Marker einfügen) weist die Einstellung **true** auf, so wird ein Standardbild von Azure Media Services verwendet, um den eingehenden Videodatenstrom zu verdecken. Im Slatezustand wird auch die Audioausgabe stummgeschaltet. 

## <a name="channels-programs"></a>Kanalprogramme
Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livestream steuern können. Kanäle verwalten Programme. Die Beziehung zwischen Kanal und Programm ähnelt der bei herkömmlichen Medien: Ein Kanal weist einen konstanten Datenstrom von Inhalten auf, und ein Programm ist auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden. Durch die Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedem Programm ist ein Medienobjekt zugeordnet, von welchem die gestreamten Inhalte gespeichert werden. Ein Medienobjekt ist einem Blockblobcontainer im Azure Storage-Konto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als Blobs in diesem Container gespeichert. Zum Veröffentlichen des Programms, damit Ihre Kunden den Datenstrom sehen können, müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Von einem Kanal können bis zu drei Programme gleichzeitig ausgeführt werden, sodass Sie von einem eingehenden Datenstrom mehrere Archive erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm wie im Abschnitt „Programmieren von Livestreaminganwendungen“ beschrieben.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden. 

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Medienobjekt. Medienobjekte können nicht gelöscht werden, wenn sie von Programmen verwendet werden. Zuerst muss das betreffende Programm gelöscht werden. 

Auch nach dem Beenden und Löschen des Programms können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Abrufen einer Miniaturansicht des Livefeeds
Wenn Livecodierung aktiviert ist, können Sie jetzt eine Vorschau des Livefeeds abrufen, sobald er den Kanal erreicht. Dies kann ein wertvolles Tool sein, um zu überprüfen, ob Ihre Livefeed den Kanal tatsächlich erreicht. 

## <a id="states"></a>Kanalstatus und ihre Zuordnung zum Abrechnungsmodus
Dies ist der aktuelle Status des Kanals. Mögliche Werte sind:

* **Stopped**(Beendet): Dies ist der anfängliche Status des Kanals nach seiner Erstellung (es sei denn, im Portal wurde das automatische Starten gewählt). In diesem Status können die Eigenschaften des Kanals aktualisiert werden. Ein Streaming ist jedoch nicht zulässig.
* **Wird gestartet**. Der Kanal wird gestartet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, wird der Kanal in den Status „Stopped“ (Angehalten) geschaltet.
* **Wird ausgeführt**. Vom Kanal können Livestreams verarbeitet werden.
* **Wird beendet**. Der Kanal wird beendet. In diesem Status sind weder Updates noch Streaming zulässig.
* **Wird gelöscht**. Der Kanal wird gelöscht. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus mit den Abrechnungsmodi aufgeführt. 

| Kanalstatus | Portal-UI-Indikatoren | In Rechnung gestellt? |
| --- | --- | --- |
| Wird gestartet |Wird gestartet |Nein (Übergangsstatus) |
| Wird ausgeführt |Bereit (keine ausgeführten Programme)<br/>oder<br/>Streaming (mindestens ein ausgeführtes Programm) |JA |
| Wird beendet |Wird beendet |Nein (Übergangsstatus) |
| Beendet |Beendet |Nein |

> [!NOTE]
> Derzeit beträgt die durchschnittliche Dauer bis zum Start des Kanals ca. 2 Minuten, aber in Einzelfällen kann dies auch mehr als 20 Minuten dauern. Das Zurücksetzen des Kanals kann bis zu 5 Minuten dauern.
> 
> 

## <a id="Considerations"></a>Überlegungen
* Tritt für einen Kanal des Codierungstyps **Standard** ein Verlust des Eingabequellen-/Beitragsfeeds auf, wird der Verlust kompensiert, indem der Quellvideo-/-audiodatenstrom durch eine Fehlertafel (Fehlerslate) und Stille ersetzt wird. Der Kanal gibt solange eine Tafel aus, bis der Eingabe-/Beitragsfeed fortgesetzt wird. Es empfiehlt sich, einen Livekanal nicht länger als 2 Stunden in diesem Zustand zu belassen. Nach diesem Punkt ist weder das Verhalten des Kanals nach erneuter Verbindung der Eingabe noch sein Verhalten als Reaktion auf einen Rücksetzungsbefehl vorhersagbar. Sie müssen den Kanal beenden, ihn löschen und dann einen neuen erstellen.
* Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.
* Rufen Sie bei jeder Neukonfiguration des Liveencoders die **Reset** -Methode für den Kanal auf. Bevor Sie den Kanal zurückzusetzen, müssen Sie das Programm beenden. Wenn Sie den Kanal zurückgesetzt haben, starten Sie das Programm neu.
* Ein Kanal kann nur beendet werden, wenn er den Status „Wird ausgeführt“ aufweist und alle Programme im Kanal beendet wurden.
* In der Standardeinstellung können Sie Ihrem Media Services-Konto nicht mehr als 5 Livekanäle hinzufügen. Hierbei handelt es sich um eine weiche Kontingentgrenze bei allen neuen Konten. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).
* Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.
* Es werden nur Kanäle in Rechnung gestellt, die den Status **Running** (Wird ausgeführt) aufweisen. Weitere Informationen finden Sie in [diesem Abschnitt](media-services-manage-live-encoder-enabled-channels.md#states) .
* Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen.
* Vergewissern Sie sich, dass sich der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, im Zustand **Wird ausgeführt** befindet.
* Die Codierungsvoreinstellung verwendet das Konzept der „max. Bildfrequenz“ von 30 BpS. Wenn die Eingabe 60 BpS/59.94i beträgt, werden die Eingabeframes per Deinterlacing auf 30/29.97 BpS gesenkt. Wenn die Eingabe 50 BpS/50i beträgt, werden die Eingabeframes per Deinterlacing auf 25 BpS gesenkt. Wenn die Eingabe 25 BpS beträgt, bleibt die Ausgabe bei 25 BpS.
* Vergessen Sie nicht, IHRE KANÄLE ZU BEENDEN, wenn Sie fertig sind. Wenn Sie dies nicht tun, werden weiter Gebühren berechnet.

## <a name="known-issues"></a>Bekannte Probleme
* Die durchschnittliche Dauer bis zum Start des Kanals wurde auf ca. 2 Minuten verkürzt, aber in Einzelfällen kann dies immer noch mehr als 20 Minuten dauern.
* Die Slate-Bilder sollten den [hier](media-services-manage-live-encoder-enabled-channels.md#default_slate)beschriebenen Einschränkungen entsprechen. Wenn Sie versuchen, einen Kanal mit einem Slate-Standardbild zu erstellen, das größer als 1920 x 1080 ist, tritt bei der Anforderung ein Fehler auf.
* Wir weisen noch einmal darauf hin: Vergessen Sie nicht, IHRE KANÄLE ZU BEENDEN, wenn Sie mit dem Streaming fertig sind. Wenn Sie dies nicht tun, werden weiter Gebühren berechnet.

## <a name="next-step"></a>Nächster Schritt
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwandte Themen
[Bereitstellen von Livestreamingereignissen mit Azure Media Services](media-services-overview.md)

[Erstellen von Kanälen, von denen eine Livecodierung eines Single-Bitrate-Datenstroms in einen Datenstrom mit adaptiver Bitrate ausgeführt wird, über das Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Erstellen von Kanälen, von denen eine Livecodierung eines Single-Bitrate-Datenstroms in einen Datenstrom mit adaptiver Bitrate ausgeführt wird, mit dem .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Verwalten von Kanälen mithilfe der REST-API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Media Services-Konzepte](media-services-concepts.md)

[Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

