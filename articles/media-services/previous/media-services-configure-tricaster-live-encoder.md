---
title: Konfigurieren des NewTek TriCaster-Encoders zum Senden eines Single-Bitrate-Livedatenstroms | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie den Tricaster-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 6e09ce83296fccfbcb4a04913d55961e0da4de79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720802"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Verwenden des NewTek TriCaster-Encoders zum Senden eines Single-Bitrate-Livedatenstroms  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

In diesem Artikel wird beschrieben, wie Sie den [NewTek TriCaster](https://newtek.com/products/tricaster-40.html)-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Verwenden Sie unter Mac OS oder Linux das Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel.md) zu erstellen.

> [!NOTE]
> Bei der Verwendung von TriCaster zum Senden eines Beitragsfeeds an AMS-Kanäle, die für Live Encoding aktiviert sind, können Video- oder Audiostörungen im Liveereignis auftreten, wenn Sie bestimmte Funktionen von TriCaster nutzen, z. B. schnelle Schnitte zwischen Feeds oder Wechseln zwischen Slates. Das AMS-Team arbeitet an einer Lösung zur Behebung dieser Probleme. Derzeit wird jedoch davon abgeraten, diese Features zu verwenden.
>
>

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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter „Kanaleinstellungen“ **Standard** für die Option „Live Encoding“ aus, und legen Sie für das Eingabeprotokoll **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen.

    Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.

3. Klicken Sie auf **Kanal erstellen**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Das Starten des Kanals kann bis zu 20 Minuten dauern.
>
>

Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurieren des NewTek TriCaster-Encoders

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

1. Erstellen Sie ein neues **NewTek TriCaster** -Projekt abhängig von der verwendeten Videoeingabequelle.
2. Suchen Sie in diesem Projekt die Schaltfläche **Streamen** , und klicken Sie auf das Zahnradsymbol daneben, um das Konfigurationsmenü für den Datenstrom zu öffnen.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Klicken Sie nach dem Öffnen des Menüs unter der Überschrift "Verbindung" auf **Neu** . Wenn Sie zur Eingabe des Verbindungstyps aufgefordert werden, wählen Sie **Adobe Flash**aus.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicken Sie auf **OK**.
5. Jetzt können Sie ein FMLE-Profil importieren, indem Sie auf den Dropdownpfeil unter **Streamingprofil** klicken und zu **Durchsuchen** navigieren.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigieren Sie zum Speicherort des konfigurierten FMLE-Profils.
7. Wählen Sie es aus, und klicken Sie auf **OK**.

    Nachdem das Profil hochgeladen wurde, fahren Sie mit dem nächsten Schritt fort.
8. Rufen Sie die Eingabe-URL des Kanals ab, um diese dem Tricaster- **RTMP-Endpunkt**zuzuweisen.

    Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.

    Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren**, und wählen Sie dann **Primäre Eingabe-URL** aus.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Fügen Sie diese Informationen im Tricaster-Projekt unter **Flash Server** in das Feld **Speicherort** ein. Weisen Sie außerdem im Feld **Datenstrom-ID** einen Datenstromnamen zu.

    Wenn dem FMLE-Profil Datenstrominformationen hinzugefügt wurden, können Sie diese hier auch importieren. Klicken Sie dazu auf **Einstellungen importieren**, navigieren Sie zum gespeicherten FMLE-Profil, und klicken Sie auf **OK**. Die relevanten Felder von Flash Server sollte mit den Informationen aus FMLE ausgefüllt werden.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Klicken Sie abschließend am unteren Rand des Bildschirms auf **OK** . Wenn Video- und Audioeingaben in den Tricaster bereit sind, beginnen Sie das Streaming an AMS, indem Sie auf die Schaltfläche **Streamen** klicken.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Bevor Sie auf **Streamen** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist.
> Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne ein Eingabe in einem betriebsbereiten Zustand verbleibt.
>
>

## <a name="test-playback"></a>Testen der Wiedergabe

Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Playback the Preview** (Vorschau wiedergeben), und wählen Sie **with Azure Media Player** (mit Azure Media Player) aus.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Artikel zur [Problembehandlung](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Erstellen eines Programms

1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Create New Program** (Neues Programm erstellen) aus.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Nächster Schritt

Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
