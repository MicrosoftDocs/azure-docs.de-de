---
title: Konfigurieren von FMLE zum Senden eines Single-Bitrate-Livedatenstroms | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie den Flash Media Live Encoder (FMLE) zum Senden eines Single-Bitrate-Datenstroms an AMS-Kanäle konfigurieren, die für Livecodierung aktiviert sind.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 01bb628a6520488dcebf49a1e868213b955abc31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465936"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Verwenden des FMLE zum Senden eines Single-Bitrate-Livedatenstroms 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

In diesem Artikel wird beschrieben, wie Sie den [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) zum Senden eines Single-Bitrate-Datenstroms an AMS-Kanäle konfigurieren, die für Livecodierung aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Verwenden Sie unter Mac OS oder Linux das Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel.md) zu erstellen.

In diesem Tutorial wird AAC verwendet. AAC wird jedoch vom FMLE nicht standardmäßig unterstützt. Sie müssen ggf. ein Plug-In für AAC-Codierung erwerben, z.B. von MainConcept: [AAC-Plug-In](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Voraussetzungen
* [Erstellen eines Azure Media Services-Kontos](media-services-portal-create-account.md)
* Stellen Sie sicher, dass ein Streamingendpunkt vorhanden ist, der ausgeführt wird. Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-portal-manage-streaming-endpoints.md)
* Installieren Sie die neueste Version des [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) -Tools.
* Starten Sie das Tool, und stellen Sie eine Verbindung mit Ihrem AMS-Konto her.

## <a name="tips"></a>Tipps
* Verwenden Sie nach Möglichkeit eine Kabelverbindung zum Internet.
* Als Faustregel zum Bestimmen der erforderlichen Bandbreite verdoppeln Sie die Streamingbitraten. Dies ist zwar keine zwingende Voraussetzung, aber hilfreich, um die Auswirkungen einer Überlastung des Netzwerks zu verringern.
* Bei der Verwendung softwarebasierter Encoder schließen Sie alle nicht benötigten Programme.

## <a name="create-a-channel"></a>Erstellen eines Kanals
1. Navigieren Sie im AMSE-Tool zur Registerkarte **Live**, und klicken Sie mit der rechten Maustaste in den Kanalbereich. Wählen Sie im Menü die Option **Kanal erstellen** aus.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter „Kanaleinstellungen“ **Standard** für die Option „Live Encoding“ aus, und legen Sie für das Eingabeprotokoll **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen.

    Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.

3. Klicken Sie auf **Kanal erstellen**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Das Starten des Kanals kann bis zu 20 Minuten dauern.
>
>

Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Beachten Sie, dass die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurieren des FMLE
In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

**Video**:

* Codec: H.264
* Profil: Hoch (Ebene 4.0)
* Bitrate: 5.000 KBit/s
* Keyframe: 2 Sekunden (60 Sekunden)
* Bildfrequenz: 30

**Audio**:

* Codec: AAC (LC)
* Bitrate: 192 KBit/s
* Abtastrate: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurationsschritte
1. Navigieren Sie auf dem zu verwendenden Computer zur FMLE-Benutzeroberfläche (Flash Media Live Encoder).

    Die Benutzeroberfläche besteht aus einer Hauptseite mit Einstellungen. Beachten Sie die folgenden empfohlenen Einstellungen, wenn Sie mit Streaming per FMLE beginnen.

   * Format: H.264-Bildfrequenz: 30,00
   * Eingangsgröße: 1.280 × 720
   * Bitrate: 5.000 KBit/s (kann an Netzwerkbeschränkungen angepasst werden)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Wenn Sie Datenquellen mit Zeilensprung verwenden, aktivieren Sie die Option "Deinterlace".
2. Wählen Sie das Schraubenschlüsselsymbol neben "Format". Anschließend werden die folgenden zusätzlichen Einstellungen angezeigt:

   * Profil: Main
   * Ebene: 4,0
   * Keyframefrequenz: 2 Sekunden

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Legen Sie die folgenden wichtigen Audioeinstellungen fest:

   * Format: AAC
   * Abtastrate: 44.100 Hz
   * Bitrate: 192 KBit/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Rufen Sie die Eingabe-URL des Kanals ab, um diese dem FMLE- **RTMP-Endpunkt**zuzuweisen.

    Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.

    Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren**, und wählen Sie dann **Primäre Eingabe-URL** aus.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Fügen Sie diese Informationen in das Feld **FMS-URL** des Ausgabebereichs ein, und weisen Sie einen Datenstromnamen zu.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Wiederholen Sie für zusätzliche Redundanz diese Schritte für die sekundäre Eingabe-URL.
6. Wählen Sie **Verbinden**aus.

> [!IMPORTANT]
> Bevor Sie auf **Verbinden** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist.
> Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne ein Eingabe in einem betriebsbereiten Zustand verbleibt.
>
>

## <a name="test-playback"></a>Testen der Wiedergabe

Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Playback the Preview** (Vorschau wiedergeben), und wählen Sie **with Azure Media Player** (mit Azure Media Player) aus.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Artikel zur [Problembehandlung](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Erstellen eines Programms
1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Create New Program** (Neues Programm erstellen) aus.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Benennen Sie die Anwendung, und passen Sie ggf. die **Archivfensterlänge** an (Standardwert: vier Stunden). Sie können außerdem einen Speicherort angeben oder die Standardeinstellung beibehalten.  
3. Aktivieren Sie das Kontrollkästchen **Programm jetzt starten** .
4. Klicken Sie auf **Programm erstellen**.  

    >[!NOTE]
    >Die Programmerstellung erfordert weniger Zeit als die Kanalerstellung.
        
5. Sobald die Anwendung ausgeführt wird, bestätigen Sie die Wiedergabe, indem Sie mit der rechten Maustaste auf das Programm klicken, zu **Playback the program(s)** (Programm(e) wiedergeben) navigieren und dann **with Azure Media Player** (mit Azure Media Player) auswählen.  
6. Nach der Bestätigung klicken Sie mit der rechten Maustaste erneut auf das Programm, und wählen Sie **Copy the Output URL to Clipboard** (Ausgabe-URL in die Zwischenablage kopieren) aus (bzw. rufen Sie diese Informationen über das Menü mit der Option **Program information and settings** (Programminformationen und -einstellungen) ab).

Der Datenstrom kann jetzt in einen Player eingebettet oder an eine Zielgruppe für die Livewiedergabe verteilt werden.  

## <a name="troubleshooting"></a>Problembehandlung
Eine Anleitung finden Sie im Artikel zur [Problembehandlung](media-services-troubleshooting-live-streaming.md).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
