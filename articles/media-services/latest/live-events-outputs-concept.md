---
title: Livestreamingkonzepte in Azure Media Services – Liveereignisse und Liveausgaben | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über Livestreamingkonzepte in Azure Media Services v3.
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
ms.date: 06/04/2019
ms.author: juliako
ms.openlocfilehash: f7f6aa000101f4c0de69d4d0922624b4020af8fb
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688672"
---
# <a name="live-events-and-live-outputs"></a>Liveereignisse und Liveausgaben

Mit Azure Media Services können Sie Ihren Kunden Liveereignisse in der Azure Cloud anbieten. Um Ihre Livestreamingereignisse in Media Services v3 zu konfigurieren, müssen Sie die in diesem Artikel besprochenen Konzepte verstehen.

> [!TIP]
> Für Kunden, die von Media Services v2-APIs migrieren, ersetzt die **Liveereignis**-Entität **Channel** in v2, und **Liveausgabe** ersetzt**Programm**.


## <a name="live-events"></a>Liveereignisse

[Liveereignisse](https://docs.microsoft.com/rest/api/media/liveevents) sorgen für das Erfassen und Verarbeiten von Livevideofeeds. Wenn Sie ein Liveereignis erstellen, wird ein Eingangsendpunkt erstellt, mit dem Sie ein Livesignal von einem Remoteencoder senden können. Der Remoteliveencoder sendet den Datenfeed an diesen Eingangsendpunkt, entweder über [RTMP](https://www.adobe.com/devnet/rtmp.html) oder über das [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx)-Protokoll (fragmentiertes MP4). Für das Smooth Streaming-Erfassungsprotokoll werden die URL-Schemas `http://` und `https://` unterstützt. Für das RTMP-Erfassungsprotokoll werden die URL-Schemas `rtmp://` und `rtmps://` unterstützt. 

## <a name="live-event-types"></a>Liveereignistypen

Für ein [Liveereignis](https://docs.microsoft.com/rest/api/media/liveevents) ist einer von zwei Typen möglich: Pass-Through und Livecodierung. 

### <a name="pass-through"></a>Pass-Through

![Pass-Through](./media/live-streaming/pass-through.svg)

Wenn Sie das **Liveereignis** vom Typ „Pass-Through“ verwenden, stützen Sie sich auf Ihren lokalen Liveencoder, um einen Videostream mit mehreren Bitraten zu erzeugen und als Beitragsfeed an das Liveereignis zu senden (über RTMP oder das Protokoll für fragmentiertes MP4). Das Liveereignis leitet dann die eingehenden Videostreams ohne weitere Bearbeitung weiter. Ein LiveEvent vom Typ „Pass-Through“ ist für Liveereignisse mit langer Laufzeit oder für ein lineares 24x365-Livestreaming optimiert. Geben Sie beim Erstellen dieses Liveereignistyps „None“ (LiveEventEncodingType.None) an.

Sie können den Beitragsfeed mit einer Auflösung von bis zu 4K und einer Bildrate von 60 Bildern/Sekunde senden, entweder mit H.264/AVC- oder H.265/HEVC-Videocodecs und AAC-Audiocodec (AAC-LC, HE-AACv1 oder HE-AACv2).  Weitere Informationen finden Sie im Artikel [Vergleich von Liveereignistypen](live-event-types-comparison.md).

> [!NOTE]
> Die Verwendung der Pass-Through-Methode ist die wirtschaftlichste Form des Livestreamings, wenn mehrere Ereignisse über einen längeren Zeitraum gestreamt werden und Sie bereits in lokale Encoder investiert haben. Preisdetails finden Sie [hier](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Ein .NET-Codebeispiel finden Sie in [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Livecodierung](./media/live-streaming/live-encoding.svg)

Wenn Sie die Livecodierung mit Media Services verwenden, konfigurieren Sie Ihren lokalen Liveencoder so, dass er ein Video mit einer einzelnen Bitrate als Beitragsfeed an das Liveereignis sendet (über RTMP oder das Protokoll für fragmentiertes MP4). Das Liveereignis codiert diesen eingehenden Stream mit einer einzelnen Bitrate in einen [Videostream mit mehreren Bitraten](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) und stellt ihn über Protokolle wie MPEG-DASH, HLS und Smooth Streaming für Wiedergabegeräte zur Verfügung. Geben Sie beim Erstellen dieses Liveereignistyps den Codierungstyp **Standard** (LiveEventEncodingType.Standard) an.

Sie können den Beitragsfeed mit einer Auflösung von bis zu 1080p und einer Bildrate von 30 Bildern/Sekunde senden, mit den H.264/AVC- oder H.265/HEVC-Videocodecs und dem AAC-Audiocodec (AAC-LC, HE-AACv1 oder HE-AACv2). Weitere Informationen finden Sie im Artikel [Vergleich von Liveereignistypen](live-event-types-comparison.md).

Bei Verwendung der Livecodierung (Einstellung **Standard** für Liveereignis) wird mit der Codierungsvoreinstellung definiert, wie der eingehende Datenstrom in mehrere Bitraten oder Ebenen codiert wird. Weitere Informationen finden Sie auf der Seite mit den [Systemvoreinstellungen](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Derzeit lautet der einzige zulässige Voreinstellungswert für den Standardtyp von Liveereignissen *Default720p*. Wenden Sie sich an amshelp@microsoft.com, falls Sie eine benutzerdefinierte Voreinstellung für die Livecodierung verwenden müssen. Geben Sie hierbei die gewünschte Tabelle mit den Angaben zur Auflösung und zu den Bitraten an. Vergewissern Sie sich, dass nur eine Ebene mit 720p und maximal sechs Ebenen vorhanden sind.

## <a name="live-event-creation-options"></a>Erstellungsoptionen für Liveereignisse

Beim Erstellen eines Liveereignisses können Sie die folgenden Optionen angeben:

* Streamingprotokoll für das Liveereignis (momentan unterstützte Protokolle: RTMP und Smooth Streaming).<br/>Die Protokolloption kann nicht geändert werden, während das Liveereignis oder die zugehörigen Liveausgaben aktiv sind. Sollten Sie verschiedene Protokolle benötigen, können Sie für jedes Streamingprotokoll ein separates Liveereignis erstellen.  
* IP-Einschränkungen für Erfassung und Vorschau. Sie können die IP-Adressen definieren, die ein Video für dieses Liveereignis erfassen dürfen. Zulässige IP-Adressen können als einzelne IP-Adresse (Beispiel: 10.0.0.1), als IP-Adressbereiche mit einer IP-Adresse und einer CIDR-Subnetzmaske (Beispiel: 10.0.0.1/22) oder als IP-Adressbereiche mit einer IP-Adresse und einer Subnetzmaske in Punkt-Dezimalschreibweise (Beispiel: 10.0.0.1(255.255.252.0)) angegeben werden.<br/>Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen 0.0.0.0/0 fest.<br/>Die IP-Adressen müssen in einem der folgenden Formate vorliegen: IPv4-Adresse mit vier Ziffern, CIDR-Adressbereich.
* Bei der Ereigniserstellung können Sie angeben, dass das Ereignis automatisch gestartet werden soll. <br/>Wenn für den automatischen Start „true“ festgelegt ist, wird das Liveereignis nach der Erstellung gestartet. Die Abrechnung beginnt, sobald das Liveereignis startet. Sie müssen für die Liveereignisressource explizit „Beenden“ auswählen, damit keine Gebühren mehr anfallen. Starten Sie alternativ das Ereignis, wenn Sie zum Starten des Streamings bereit sind. 

    Weitere Informationen finden Sie im Abschnitt [Zustände und Abrechnung von Liveereignissen](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Erfassungs-URLs für Liveereignisse

Wenn das Liveereignis erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem lokalen Liveencoder bereitstellen. Diese URLs werden vom Liveencoder zur Eingabe eines Livestreams verwendet. Weitere Informationen finden Sie unter [Empfohlene lokale Liveencoder](recommended-on-premises-live-encoders.md). 

Sie können Nicht-Vanity-URLs oder Vanity-URLs verwenden. 

> [!NOTE] 
> Damit eine Erfassungs-URL vorhersagbar ist, legen Sie den Vanitymodus fest.

* Nicht-Vanity-URL

    Die Nicht-Vanity-URL ist der Standardmodus in AMS v3. Sie erhalten das Liveereignis potenziell schnell, aber die Erfassungs-URL ist erst bekannt, wenn das Liveereignis gestartet wurde. Die URL ändert sich, wenn Sie das Liveereignis starten/beenden. <br/>Nicht-Vanity-URLs sind in Szenarien nützlich, in denen ein Endbenutzer das Streaming mithilfe einer App durchführen möchte, die App auf ein Liveereignis wartet und eine dynamische Erfassungs-URL kein Problem darstellt.
* Vanity-URL

    Der Vanitymodus wird von großen Medienanstalten bevorzugt, die Hardware-Rundfunkencoder verwenden und diese nicht beim Starten des Liveereignisses neu konfigurieren möchten. Dabei wird eine vorhersagbare Erfassungs-URL gewünscht, die sich nicht im Lauf der Zeit ändert.
    
    Zum Festlegen dieses Modus setzen Sie `vanityUrl` zum Zeitpunkt der Erstellung auf `true` (der Standardwert ist `false`). Sie müssen auch Ihr eigenes Zugriffstoken übergeben (um ein zufälliges Token in der URL zu vermeiden). Legen Sie `LiveEventInput.accessToken` zum Zeitpunkt der Erstellung auf einen eindeutigen Bezeichner fest. Sobald der Modus festgelegt ist, kann er nicht aktualisiert werden.

### <a name="live-ingest-url-naming-rules"></a>Benennungsregeln für Liveerfassungs-URLs

Die nachstehende *zufällige* Zeichenfolge ist eine 128-Bit-Hexadezimalzahl (sie umfasst 32 Zeichen von 0 bis 9 und a bis f).<br/>
Das nachstehende *Zugriffstoken* wird zum Angeben einer festen URL benötigt. Es handelt sich ebenfalls um eine 128-Bit-Hexadezimalzahl.

#### <a name="non-vanity-url"></a>Nicht-Vanity-URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>Vanity-URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>Vorschau-URL für Liveereignisse

Sobald das **Liveereignis** Daten aus dem Beitragsfeed empfängt, können Sie über den zugehörigen Vorschauendpunkt eine Vorschau anzeigen und den Empfang des Livestreams bestätigen, bevor Sie mit der Veröffentlichung fortfahren. Nachdem Sie sich von der Qualität des Vorschaustreams überzeugt haben, können Sie das Liveereignis verwenden, um den Livestream (vorab erstellte) **Streamingendpunkte** für die Übertragung verfügbar zu machen. Hierzu erstellen Sie eine neue [Liveausgabe](https://docs.microsoft.com/rest/api/media/liveoutputs) für das **Liveereignis**. 

> [!IMPORTANT]
> Vergewissern Sie sich, dass das Video an die Vorschau-URL übertragen wird, bevor Sie fortfahren.

## <a name="live-event-long-running-operations"></a>Zeitintensive Vorgänge für Liveereignisse

Weitere Informationen finden Sie unter [Zeitintensive Vorgänge](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Liveausgaben

Wenn der Stream an das Liveereignis übertragen wird, können Sie das Streamingereignis starten, indem Sie ein [Medienobjekt](https://docs.microsoft.com/rest/api/media/assets), eine [Liveausgabe](https://docs.microsoft.com/rest/api/media/liveoutputs) und einen [Streaminglocator](https://docs.microsoft.com/rest/api/media/streaminglocators) erstellen. Durch die Liveausgabe wird der Datenstrom archiviert und über den [Streamingendpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints) für die Zuschauer verfügbar gemacht.  

> [!NOTE]
> Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. Wenn Sie die Liveausgabe löschen, werden das zugrunde liegende Medienobjekt und sein Inhalt nicht gelöscht. 

Die Beziehung zwischen einem **Liveereignis** und der zugehörigen **Liveausgabe** ist ähnlich wie bei einer traditionellen Fernsehsendung, bei der ein Kanal (**Liveereignis**) einen konstanten Videodatenstrom darstellt und eine Aufzeichnung (**Liveausgabe**) auf ein bestimmtes Zeitsegment (z.B. Abendnachrichten von 18:30 bis 19:00 Uhr) ausgerichtet ist. Sie können Fernsehsendungen mit einem digitalen Videorecorder (DVR) aufnehmen – die entsprechende Funktion in Liveereignissen wird über die **ArchiveWindowLength**-Eigenschaft verwaltet. Es handelt sich um eine ISO-8601-Zeitspanne (z.B. PTHH:MM:SS), die die Kapazität des Digitalrecorders angibt. Diese kann von mindestens 3 Minuten auf bis zu maximal 25 Stunden eingestellt werden.

Das **Liveausgabe**-Objekt verhält sich wie ein Tonbandgerät, das den Livestream erfasst und in einem Medienobjekt in Ihrem Media Services-Konto aufzeichnet. Der aufgezeichnete Inhalt wird in dem Ihrem Konto zugeordneten Azure Storage-Konto dauerhaft in dem Container gespeichert, der über die Medienobjektressource definiert wurde. Die **Liveausgabe** ermöglicht Ihnen auch, einige der Eigenschaften des ausgehenden Livestreams zu steuern, etwa welcher Anteil des Streams in der Archivaufzeichnung verwahrt wird (beispielsweise die Kapazität des Cloud-DVR) und ob die Zuschauer mit der Wiedergabe des Livestreams beginnen können. Das Archiv für den Datenträger ist ein zirkuläres „Archivfenster“, das nur die Menge an Inhalten umfasst, die in der **archiveWindowLength**-Eigenschaft der **Liveausgabe** angegeben ist. Inhalt außerhalb dieses Fensters wird automatisch aus dem Speichercontainer entfernt und ist nicht wiederherstellbar. Sie können mehrere **Liveausgaben** (bis zu drei) mit unterschiedlichen Archivlängen und Einstellungen für ein **Liveereignis** erstellen.  

Wenn Sie das **Medienobjekt** der **Liveausgabe** mit einem **Streaminglocator** veröffentlicht haben, ist das **Liveereignis** (bis zur DVR-Fensterlänge) weiterhin bis zum Ablauf oder der Löschung des Streaminglocators sichtbar, je nachdem, was zuerst eintritt.

Weitere Informationen finden Sie unter [Verwenden eines Cloud-DVR](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)
