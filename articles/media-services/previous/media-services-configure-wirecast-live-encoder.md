---
title: Konfigurieren des Telestream Wirecast-Encoders zum Senden eines Single-Bitrate-Livedatenstroms | Microsoft-Dokumentation
description: 'In diesem Thema wird beschrieben, wie Sie den Wirecast-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: ecab0ee67dd1d5cee3fd9927c00c9f9d827b2f47
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910048"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Verwenden des Wirecast-Encoders zum Senden eines Single-Bitrate-Livedatenstroms

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

In diesem Artikel wird beschrieben, wie Sie den [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) -Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Verwenden Sie unter Mac OS oder Linux das Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel.md) zu erstellen.

> [!NOTE]
> Encoder müssen TLS 1.2 unterstützen, wenn RTMPS-Protokolle verwendet werden. Verwenden Sie aufgrund der TLS 1.2-Anforderung die Wirecast-Version 13.0.2 oder höher.

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
1. Navigieren Sie im AMSE-Tool zur Registerkarte **Live** , und klicken Sie mit der rechten Maustaste in den Kanalbereich. Wählen Sie im Menü die Option **Kanal erstellen** aus.

    ![Screenshot mit Auswahl von „Kanal erstellen“ in einem Menü.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter „Kanaleinstellungen“ **Standard** für die Option „Live Encoding“ aus, und legen Sie für das Eingabeprotokoll **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen.

    Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.

3. Klicken Sie auf **Kanal erstellen**.

   ![Screenshot des Dialogfelds zum Erstellen eines Livekanals.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Das Starten des Kanals kann bis zu 20 Minuten dauern.
>
>

Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurieren des Telestream Wirecast-Encoders
In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

**Video** :

* Codec: H.264
* Profil: Hoch (Ebene 4.0)
* Bitrate: 5.000 KBit/s
* Keyframe: 2 Sekunden (60 Sekunden)
* Bildfrequenz: 30

**Audio** :

* Codec: AAC (LC)
* Bitrate: 192 KBit/s
* Abtastrate: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurationsschritte
1. Öffnen Sie die Telestream Wirecast-Anwendung auf dem verwendeten Computer, und konfigurieren Sie sie für das RTMP-Streaming.
2. Konfigurieren Sie die Ausgabe, indem Sie zur Registerkarte **Ausgabe** navigieren und **Ausgabeeinstellungen...** auswählen.

    Stellen Sie sicher, dass das **Ausgabeziel** auf **RTMP-Server** festgelegt ist.
3. Klicken Sie auf **OK**.
4. Legen Sie auf der Einstellungsseite das Feld **Ziel** auf **Azure Media Services** fest.

    Das Codierungsprofil ist auf **Azure H.264 720p 16:9 (1280x720)** voreingestellt. Zum Anpassen dieser Einstellungen wählen Sie das Zahnradsymbol rechts neben der Dropdownliste aus und wählen dann **Neue Voreinstellung** aus.

    ![Screenshot des Dialogfelds „Vorlage wählen“ mit Auswahl von BlobTrigger.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurieren Sie die Encodervoreinstellungen.

    Benennen Sie die Voreinstellung, und überprüfen Sie, ob die folgenden empfohlenen Einstellungen vorliegen:

    **Video**

   * Encoder: MainConcept h. 264
   * Frames pro Sekunde: 30
   * Durchschnittliche Bitrate: 5.000 KBit/s (kann je nach Netzwerkeinschränkungen angepasst werden)
   * Profil: Main
   * Keyframe alle: 60 Bilder

     **Audio**

   * Zielbitrate: 192 KBit/s
   * Abtastrate: 44,100 kHz

     ![Screenshot der Encodervoreinstellung für AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Klicken Sie auf **Speichern**.

    Das neu erstellte Profil kann jetzt im Feld "Codierung" ausgewählt werden.

    Stellen Sie sicher, dass das neue Profil ausgewählt ist.
7. Rufen Sie die Eingabe-URL des Kanals ab, um diese dem Wirecast- **RTMP-Endpunkt** zuzuweisen.

    Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.

    Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren** , und wählen Sie dann **Primäre Eingabe-URL** aus.  

    ![Screenshot der Option „Eingabe-URL in Zwischenablage kopieren“ für primäre Eingabe-URL.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Fügen Sie diese Informationen im Wirecast-Fenster **Output Settings** (Ausgabeoptionen) im Ausgabebereich in das Feld **Adresse** ein, und weisen Sie einen Datenstromnamen zu.

    ![Screenshot der Ausgabeeinstellungen.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Klicken Sie auf **OK**.
2. Vergewissern Sie sich auf dem **Wirecast** -Hauptbildschirm, dass die Eingabequellen für Video und Audio bereit sind, und klicken Sie dann in der linken oberen Ecke auf **Streamen**.

    ![Screenshot der Schaltfläche „Wirecast Stream“.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Bevor Sie auf **Streamen** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist.
> Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne ein Eingabe in einem betriebsbereiten Zustand verbleibt.
>
>

## <a name="test-playback"></a>Testen der Wiedergabe

Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Playback the Preview** (Vorschau wiedergeben), und wählen Sie **with Azure Media Player** (mit Azure Media Player) aus.  

![Screenshot von „Vorschau wiedergeben“ mit ausgewählter Option „Azure Media Player“.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Artikel zur [Problembehandlung](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Erstellen eines Programms
1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Create New Program** (Neues Programm erstellen) aus.  

    ![Screenshot der ausgewählten Option „Programm erstellen“.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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
