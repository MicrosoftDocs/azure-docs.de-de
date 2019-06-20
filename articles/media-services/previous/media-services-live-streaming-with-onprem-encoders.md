---
title: Livestreaming mit lokalen Encodern, die Datenströme mit Mehrfachbitrate erstellen – Azure | Microsoft-Dokumentation
description: 'In diesem Thema wird das Einrichten eines Kanals beschrieben, der einen Livestream mit Mehrfachbitrate von einem lokalen Encoder empfängt. Der Datenstrom kann über einen oder mehrere Streamingendpunkte an Wiedergabe-Clientanwendungen übermittelt werden. Hierzu dienen die folgenden adaptiven Streamingprotokolle: HLS, Smooth Streaming, DASH.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: da20e4601b75bcb22546d21f6ad218ac9ba2728b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463792"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Arbeiten mit Kanälen, die Livestreams mit Mehrfachbitrate von lokalen Encodern empfangen

> [!NOTE]
> Ab dem 12. Mai 2018 unterstützen Livekanäle nicht mehr das Erfassungsprotokoll zum RTP/MPEG-2-Transportdatenstrom. Migrieren Sie von Erfassungsprotokollen für RTP/MPEG-2 zu Erfassungsprotokollen für RTMP oder fragmentiertem MP4 (Smooth Streaming).

## <a name="overview"></a>Übersicht
In Azure Media Services stellt ein *Kanal* eine Pipeline zum Verarbeiten von Livestreaminginhalten dar. Es gibt zwei Arten, auf die Live-Eingabedatenströme von Kanälen empfangen werden können:

* Von einem lokalen Liveencoder wird ein RTMP- oder Smooth Streaming-Datenstrom (Fragmentiertes MP4) mit Mehrfachbitrate an den Kanal gesendet, der nicht für die Livecodierung mit Media Services aktiviert ist. Die erfassten Datenströme durchlaufen Kanäle ohne weitere Verarbeitung. Diese Methode wird als *Pass-Through-Methode* bezeichnet. Ein Liveencoder kann auch einen Single-Bitrate-Datenstrom an einen Kanal senden, der nicht für Live Encoding konfiguriert ist. Dies ist aber nicht zu empfehlen. Der Datenstrom wird für Kunden, die dies anfordern, von Media Services bereitgestellt.

  > [!NOTE]
  > Die Verwendung der Pass-Through-Methode ist die wirtschaftlichste Form des Livestreamings.


* Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an den Kanal, der zum Ausführen der Livecodierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Kanal wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Videodatenstrom (adaptiv) mit Mehrfachbitrate durchgeführt. Der Datenstrom wird für Kunden, die dies anfordern, von Media Services bereitgestellt.

Ab Version Media Services 2.10 können Sie beim Erstellen eines Kanals angeben, wie der Kanal den Eingabedatenstrom empfangen soll. Sie können auch angeben, ob der Kanal den Live Encoding-Prozess für Ihren Datenstrom durchführen soll. Sie haben zwei Möglichkeiten:

* **Pass-Through**: Geben Sie diesen Wert an, wenn ein lokaler Liveencoder verwendet werden soll, der einen Mehrfachbitraten-Datenstrom (Pass-Through-Datenstrom) ausgibt. In diesem Fall wird der Eingabedatenstrom ohne Codierung an die Ausgabe geleitet. Dies ist das Verhalten eines Kanals für ältere Versionen als Version 2.10. Dieser Artikel enthält Details zum Arbeiten mit Kanälen dieses Typs.
* **Live Encoding**: Wählen Sie diesen Wert, wenn Sie Media Services verwenden möchten, um einen Single-Bitrate-Livedatenstrom in einen Mehrfachbitrate-Datenstrom zu codieren. Beim Belassen eines Liveencodingkanals im Status **Wird ausgeführt** fallen Gebühren an. Wir empfehlen Ihnen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden. Der Datenstrom wird für Kunden, die dies anfordern, von Media Services bereitgestellt.

> [!NOTE]
> In diesem Artikel werden die Attribute der Kanäle erläutert, die nicht zum Ausführen der Livecodierung aktiviert sind. Informationen zum Arbeiten mit Kanälen, die zum Ausführen der Livecodierung aktiviert sind, finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).
>
>Informationen zu empfohlenen lokalen Encodern finden Sie unter [Empfohlene lokale Encoder](media-services-recommended-encoders.md).

Im folgenden Diagramm ist ein Livestreaming-Workflow dargestellt, der über einen lokalen Liveencoder RTMP- oder Fragmentiertes MP4-Datenstrom (Smooth Streaming) mit Mehrfachbitrate ausgibt.

![Liveworkflow][live-overview]

## <a id="scenario"></a>Allgemeines Livestreamingszenario
Die folgenden Schritte beschreiben die Aufgaben zum Erstellen von häufig verwendeten Livestreaminganwendungen.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, der einen RTMP- oder Fragmentiertes MP4-Datenstrom (Smooth Streaming) mit Mehrfachbitrate ausgibt. Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](https://go.microsoft.com/fwlink/?LinkId=532824).

    Sie können diesen Schritt auch nach der Erstellung Ihres Kanals ausführen.
2. Erstellen Sie einen Kanal, und starten Sie ihn.

3. Rufen Sie die Erfassungs-URL des Kanals ab.

    Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
4. Rufen Sie die Vorschau-URL des Kanals ab.

    Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.
5. Erstellen Sie ein Programm.

    Im Azure-Portal wird beim Erstellen eines Programms auch ein Medienobjekt erstellt.

    Wenn Sie hingegen das .NET SDK oder REST verwenden, müssen Sie beim Erstellen eines Programms ein Medienobjekt erstellen und angeben, dass es verwendet werden soll.
6. Veröffentlichen Sie das mit dem Programm verknüpfte Medienobjekt.   

    >[!NOTE]
    >Beim Erstellen Ihres Azure Media Services-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, muss sich im Status **Wird ausgeführt** befinden.

7. Starten Sie das Programm, wenn Sie zum Starten von Streaming und Archivierung bereit sind.

8. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.

9. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

10. Löschen Sie das Programm (und optional das Medienobjekt).     

## <a id="channel"></a>Beschreibung von Kanälen und zugehörigen Komponenten
### <a id="channel_input"></a>Kanaleingangskonfigurationen (Erfassung)
#### <a id="ingest_protocols"></a>Erfassungsstreamingprotokoll
Media Services unterstützt die Erfassung von Livefeeds, indem „Fragmentiertes MP4“ und „RTMP“ (jeweils mit Mehrfachbitrate) als Streamingprotokolle verwendet werden. Wenn das Erfassungs-Streamingprotokoll RTMP aktiviert ist, werden für den Kanal zwei Eingangsendpunkte für die Erfassung erstellt:

* **Primäre URL**: Gibt die vollqualifizierte URL des primären RTMP-Erfassungsendpunkts für den Kanal an.
* **Sekundäre URL** (optional): Gibt die vollqualifizierte URL des sekundären RTMP-Erfassungsendpunkts für den Kanal an.

Verwenden Sie die sekundäre URL, um die Beständigkeit und Fehlertoleranz des Erfassungsdatenstroms (sowie das Failover und die Fehlertoleranz des Encoders) zu verbessern. Dies gilt insbesondere für die folgenden Szenarien:

- Einzelner Encoder, der einen doppelten Push zu primären und sekundären URLs ausführt:

    Der Hauptzweck ist hierbei, mehr Resilienz gegenüber Netzwerkfluktuationen und Jitter bereitzustellen. Einige RTMP-Encoder können Trennungen der Netzwerkverbindung nicht gut verarbeiten. Bei einer Trennung der Netzwerkverbindung beendet ein Encoder ggf. die Codierung und sendet die gepufferten Daten dann nicht, wenn die Verbindung wiederhergestellt wird. Dies führt zu Diskontinuitäten und Datenverlust. Trennungen der Netzwerkverbindung können aufgrund einer instabilen Netzwerkverbindung oder Wartungen auf Azure-Seite auftreten. Durch primäre/sekundäre URLs werden Netzwerkprobleme reduziert, und es kann ein kontrollierter Aktualisierungsprozess bereitgestellt werden. Bei jeder geplanten Trennung der Netzwerkverbindung verwaltet Media Services die primären und sekundären Trennungen und sorgt für eine Verzögerung zwischen diesen beiden Trennvorgängen. So haben die Encoder genügend Zeit zum Fortsetzen des Sendens von Daten und zum Wiederherstellen der Verbindung. Die Reihenfolge der Trennungen kann zufällig sein. Es gibt aber immer eine Verzögerung zwischen primären/sekundären bzw. sekundären/primären URLs. In diesem Szenario ist der Encoder immer noch die einzige Fehlerquelle.

- Mehrere Encoder, bei denen jeder Encoder einen Push zu einem dedizierten Endpunkt durchführt:

    Dieses Szenario bietet Redundanz für Encoder und Erfassung. In diesem Szenario überträgt encoder1 Daten an die primäre URL und encoder2 an die sekundäre URL. Bei einem Ausfall eines Encoders kann der andere Encoder das Senden der Daten fortsetzen. Die Datenredundanz kann erhalten werden, da Media Services primäre und sekundäre URLs nicht zur gleichen Zeit trennt. Bei diesem Szenario wird davon ausgegangen, dass die Encoder zeitlich synchronisiert sind und exakt die gleichen Daten bereitstellen.  

- Mehrere Encoder, die einen doppelten Push zu primären und sekundären URLs durchführen:

    Bei diesem Szenario senden beide Encoder Daten an primäre und sekundäre URLs. Dies bietet die höchste Zuverlässigkeit und Fehlertoleranz sowie Datenredundanz. Bei diesem Szenario kann sowohl ein Encoderausfall als auch eine Trennung der Verbindung toleriert werden. Dies gilt auch, wenn einer der Encoder nicht mehr funktioniert. Hierbei wird davon ausgegangen, dass die Encoder zeitlich synchronisiert sind und exakt die gleichen Daten bereitstellen.  

Informationen zu RTMP-Liveencodern finden Sie unter [Azure Media Services RTMP Support and Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824)(in englischer Sprache).

#### <a name="ingest-urls-endpoints"></a>Erfassungs-URLs (Endpunkte)
Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) dar, den Sie im Liveencoder angeben, damit dieser Datenströme an die Kanäle übertragen kann.   

Sie können die Erfassungs-URLs beim Erstellen des Kanals erhalten. Der Kanal muss hierfür nicht den Status **Running** (Wird ausgeführt) aufweisen. Wenn Sie damit beginnen möchten, Daten an den Kanal zu senden, muss der Kanal den Status **Running** (Wird ausgeführt) aufweisen. Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie Ihren Datenstrom über die Vorschau-URL anzeigen.

Sie können Livestreams vom Typ „Fragmentiertes MP4“ (Smooth Streaming) über eine SSL-Verbindung erfassen. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. Derzeit kann RTMP nicht über SSL erfasst werden.

#### <a id="keyframe_interval"></a>Keyframe-Intervall
Wenn Sie einen lokalen Liveencoder zum Erzeugen eines Datenstroms mit Mehrfachbitrate nutzen, gibt das Keyframe-Intervall die GOP-Dauer (Group of Pictures) wie von diesem externen Encoder verwendet an. Nachdem der Kanal diesen eingehenden Datenstrom empfangen hat, können Sie Ihren Livedatenstrom in einem der folgenden Formate für Wiedergabe-Clientanwendungen bereitstellen: Smooth Streaming, Dynamisches adaptives Streaming über HTTP (DASH) und HTTP Live Streaming (HLS). Beim Livestreaming wird HLS immer dynamisch verpackt. Standardmäßig berechnet Media Services das HLS-Segment-Paketerstellungsverhältnis (Fragmente pro Segment) basierend auf dem Keyframe-Intervall, das vom Liveencoder empfangen wird.

Die folgende Tabelle zeigt, wie die Segmentdauer berechnet wird:

| Keyframe-Intervall | Paketerstellungsverhältnis für HLS-Segmente (FragmentsPerSegment) | Beispiel |
| --- | --- | --- |
| Kleiner oder gleich 3 Sekunden |3:1 |Wenn „KeyFrameInterval“ (bzw. GOP) den Wert „2 Sekunden“ aufweist, beträgt das HLS-Segment-Paketerstellungsverhältnis standardmäßig 3:1. Es wird ein 6-Sekunden-HLS-Segment erstellt. |
| 3 bis 5 Sekunden |2:1 |Wenn „KeyFrameInterval“ (bzw. GOP) den Wert „4 Sekunden“ aufweist, beträgt das HLS-Segment-Paketerstellungsverhältnis standardmäßig 2:1. Es wird ein 8-Sekunden-HLS-Segment erstellt. |
| Größer als 5 Sekunden |1:1 |Wenn „KeyFrameInterval“ (bzw. GOP) den Wert „6 Sekunden“ aufweist, beträgt das HLS-Segment-Paketerstellungsverhältnis standardmäßig 1:1. Es wird ein 6-Sekunden-HLS-Segment erstellt. |

Sie können das Verhältnis der Fragmente pro Segment ändern, indem Sie den Ausgang des Kanals konfigurieren und FragmentsPerSegment für ChannelOutputHls festlegen.

Sie können den Keyframe-Intervallwert auch durch Festlegen der KeyFrameInterval-Eigenschaft für ChannelInput ändern. Wenn Sie das KeyFrameInterval explizit festlegen, wird FragmentsPerSegment für das Paketerstellungsverhältnis für HLS-Segmente anhand der oben beschriebenen Regeln berechnet.  

Wenn Sie sowohl KeyFrameInterval als auch FragmentsPerSegment explizit festlegen, verwendet Media Services die von Ihnen festgelegten Werte.

#### <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen
Sie können die IP-Adressen definieren, die zum Veröffentlichen von Videos in diesem Kanal zugelassen sind. Eine zulässige IP-Adresse kann wie folgt angegeben werden:

* Einzelne IP-Adresse (z.B. 10.0.0.1)
* IP-Adressbereich, für den eine IP-Adresse und eine CIDR-Subnetzmaske (z.B. 10.0.0.1/22) verwendet werden
* IP-Adressbereich, für den eine IP-Adresse und Subnetzmaske in punktierter Dezimalschreibweise (z.B. 10.0.0.1(255.255.252.0)) verwendet werden

Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen 0.0.0.0/0 fest.

### <a name="channel-preview"></a>Kanalvorschau
#### <a name="preview-urls"></a>Vorschau-URLs
Mit den Kanälen ist ein Vorschauendpunkt (Vorschau-URL) verfügbar, mit dem Sie eine Vorschau anzeigen und Ihren Datenstrom vor der weiteren Verarbeitung und Übermittlung überprüfen können.

Wenn Sie einen Kanal erstellen, können Sie die Vorschau-URL abrufen. Der Kanal muss zum Abrufen der URL nicht den Status **Running** (Wird ausgeführt) aufweisen. Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

Zurzeit kann die Vorschau des Datenstroms unabhängig vom angegebenen Eingabetyp nur im Format „Fragmentiertes MP4“ (Smooth Streaming) bereitgestellt werden. Sie können den Player unter [Smooth Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) verwenden, um das Smooth Streaming zu testen. Außerdem können Sie einen Player verwenden, der im Azure-Portal gehostet wird, um Ihren Datenstrom anzuzeigen.

#### <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen
Sie können die IP-Adressen definieren, die zum Herstellen einer Verbindung mit dem Vorschauendpunkt zugelassen sind. Wenn keine IP-Adressen angegeben werden, ist jede IP-Adresse zulässig. Eine zulässige IP-Adresse kann wie folgt angegeben werden:

* Einzelne IP-Adresse (z.B. 10.0.0.1)
* IP-Adressbereich, für den eine IP-Adresse und eine CIDR-Subnetzmaske (z.B. 10.0.0.1/22) verwendet werden
* IP-Adressbereich, für den eine IP-Adresse und Subnetzmaske in punktierter Dezimalschreibweise (z.B. 10.0.0.1(255.255.252.0)) verwendet werden

### <a name="channel-output"></a>Kanalausgabe
Informationen zur Kanalausgabe finden Sie im Abschnitt [Keyframe-Intervall](#keyframe_interval).

### <a name="channel-managed-programs"></a>Vom Kanal verwaltete Programme
Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livestream steuern können. Die Programme werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden. Durch die Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedem Programm ist ein Medienobjekt zugeordnet, mit dem die gestreamten Inhalte gespeichert werden. Ein Medienobjekt ist einem Blockblobcontainer im Azure-Speicherkonto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als Blobs in diesem Container gespeichert. Zum Veröffentlichen des Programms, damit Ihre Kunden den Datenstrom sehen können, müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Sie können diesen Locator verwenden, um eine Streaming-URL zu erstellen, die Sie für Ihre Clients bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Sie können verschiedene Teile eines Ereignisses je nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, sechs Stunden eines Programms zu archivieren, aber nur die letzten zehn Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von sechs Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von zehn Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen Sie stattdessen für jedes Ereignis ein neues Programm. Starten Sie das Programm, wenn Sie zum Starten des Streaming- und Archivierungsvorgangs bereit sind. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Medienobjekt. Ein Medienobjekt kann nicht gelöscht werden, wenn es von einem Programm verwendet wird. Zuerst muss das Programm gelöscht werden.

Auch nach dem Beenden und Löschen des Programms ist es für Benutzer möglich, Ihre archivierten Inhalte als Video on Demand zu streamen, bis Sie das Medienobjekt löschen. Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

## <a id="states"></a>Kanalstatus und Abrechnung
Mögliche Werte für den aktuellen Status eines Kanals sind:

* **Beendet**: Dies ist der anfängliche Status eines Kanals nach der Erstellung. In diesem Status können die Eigenschaften des Kanals aktualisiert werden. Ein Streaming ist aber nicht zulässig.
* **Wird gestartet**: Der Kanal wird gestartet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, erhält der Kanal wieder den Status **Beendet**.
* **Running**: Der Kanal kann Livestreams verarbeiten.
* **Wird beendet**: Der Kanal wird beendet. In diesem Status sind weder Updates noch Streaming zulässig.
* **Wird gelöscht**: Der Kanal wird gelöscht. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus zu den Abrechnungsmodi aufgeführt.

| Kanalstatus | Portal-UI-Indikatoren | In Rechnung gestellt? |
| --- | --- | --- |
| **Wird gestartet** |**Wird gestartet** |Nein (Übergangsstatus) |
| **Wird ausgeführt** |**Bereit** (keine ausgeführten Programme)<p><p>oder<p>**Streaming** (mindestens ein ausgeführtes Programm) |Ja |
| **Wird beendet** |**Wird beendet** |Nein (Übergangsstatus) |
| **Beendet** |**Beendet** |Nein |

## <a id="cc_and_ads"></a>Untertitel und Werbeeinblendungen
Die folgende Tabelle enthält die unterstützten Standards für Untertitel und Werbeeinblendungen.

| Standard | Notizen |
| --- | --- |
| CEA-708 und EIA-608 (708/608) |CEA-708 und EIA 608 sind Untertitelstandards für die USA und Kanada.<p><p>Derzeit werden Untertitel nur unterstützt, wenn sie im codierten Eingabedatenstrom übertragen werden. Sie müssen einen Live-Media Encoder verwenden, mit dem Untertitel nach dem Standard 608 oder 708 in den codierten Datenstrom eingefügt werden können, der an Media Services gesendet wird. Media Services sendet die Inhalte mit eingefügten Untertiteln an Ihre Zuschauer. |
| TTML in ISMT (Smooth Streaming-Texttracks) |Die dynamische Paketerstellung von Media Services ermöglicht Ihren Clients das Streamen von Inhalten in einem der folgenden Formate: DASH, HLS oder Smooth Streaming. Wenn Sie Fragmentiertes MP4 (Smooth Streaming) mit Untertiteln in ISMT (Smooth Streaming-Texttracks) erfassen, kann der Datenstrom aber nur an Smooth Streaming-Clients gesendet werden. |
| SCTE-35 |SCTE-35 ist ein digitales Signalisierungssystem zum Einfügen von Werbeeinblendungen. Downstream-Empfänger verwenden das Signal zum Einfügen von Werbung in den Stream für die vorgesehene Zeit. SCTE-35 muss als Spur mit geringer Dichte in den Eingabestream gesendet werden.<p><p>Derzeit wird als Eingabestreamformat mit Werbesignalen nur Fragmentiertes MP4 (Smooth Streaming) unterstützt. Das einzige unterstützte Ausgabeformat ist ebenfalls Smooth Streaming. |

## <a id="considerations"></a>Überlegungen
Wenn Sie mit einem lokalen Liveencoder einen Datenstrom mit Mehrfachbitrate an einen Kanal senden, gelten folgende Einschränkungen:

* Stellen Sie sicher, dass Sie ausreichend ungenutzte Internetbandbreite zum Senden an die Erfassungspunkte haben.
* Für die Verwendung einer sekundären Erfassungs-URL ist zusätzliche Bandbreite erforderlich.
* Der eingehende Datenstrom mit Mehrfachbitrate kann maximal zehn Ebenen (Schichten) der Videoqualität und maximal fünf Audiospuren aufweisen.
* Die höchste durchschnittliche Bitrate für die Videoqualitätsebenen sollte weniger als 10 MBit/s betragen.
* Die zusammengezählten durchschnittlichen Bitraten für alle Video- und Audiostreams sollten unter 25 Mbit/s liegen.
* Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.
* Sie können eine Single-Bitrate-Erfassung für Ihren Kanal durchführen. Aber da der Datenstrom vom Kanal nicht verarbeitet wird, erhalten die Clientanwendungen ebenfalls einen Single-Bitrate-Datenstrom. (Dies ist nicht zu empfehlen.)

Hier sind weitere Aspekte im Zusammenhang mit der Arbeit mit Kanälen und den zugehörigen Komponenten aufgeführt:

* Rufen Sie bei jeder Neukonfiguration des Liveencoders die **Reset** -Methode für den Kanal auf. Bevor Sie den Kanal zurückzusetzen, müssen Sie das Programm beenden. Wenn Sie den Kanal zurückgesetzt haben, starten Sie das Programm neu.

  > [!NOTE]
  > Wenn Sie das Programm neu starten, müssen Sie es einem neuen Medienobjekt zuweisen und einen neuen Locator erstellen. 
  
* Ein Kanal kann nur beendet werden, wenn er den Status **Wird ausgeführt** aufweist und alle Programme im Kanal beendet wurden.
* In der Standardeinstellung können Sie Ihrem Media Services-Konto nur bis zu fünf Kanäle hinzufügen. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).
* Es werden nur dann Gebühren berechnet, wenn sich der Kanal im Status **Wird ausgeführt** befindet. Weitere Informationen finden Sie im Abschnitt [Kanalstatus und Abrechnung](media-services-live-streaming-with-onprem-encoders.md#states).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwandte Themen
[Empfohlene lokale Encoder](media-services-recommended-encoders.md)

[Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Azure Media Services – Übersicht und häufige Szenarios](media-services-overview.md)

[Media Services-Konzepte](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
