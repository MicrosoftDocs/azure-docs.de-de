---
title: Konfigurieren des Haivision KB-Encoders zum Senden eines Single-Bitrate-Livedatenstroms an Azure | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie den Haivision KB-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert ist.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;dbgeorge
ms.openlocfilehash: 058a1f964eb14d89628c92cbadd80511b7a27bae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61230496"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Verwenden des Haivision KB-Liveencoders zum Senden eines Single-Bitrate-Livedatenstroms  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

In diesem Thema wird beschrieben, wie Sie den [Haivision KB-Liveencoder](https://www.haivision.com/products/kb-series/) zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert ist. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Verwenden Sie unter Mac OS oder Linux das Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel.md) zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen
*   Zugriff auf einen Haivision KB-Encoder, auf dem mindestens Softwareversion 5.01 ausgeführt wird.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter „Kanaleinstellungen“ **Standard** für die Option „Live Encoding“ aus, und legen Sie für das Eingabeprotokoll **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen. Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.
3. Klicken Sie auf **Kanal erstellen**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Das Starten des Kanals kann bis zu 20 Minuten dauern.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurieren des Haivision KB-Encoders
In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

Video:
-   Codec: H.264
-   Profil: Hoch (Ebene 4.0)
-   Bitrate: 5.000 KBit/s
-   Keyframe: 2 Sekunden (60 Frames)
-   Bildfrequenz: 30

Audio:
-   Codec: AAC (LC)
-   Bitrate: 192 KBit/s
-   Abtastrate: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurationsschritte
1.  Melden Sie sich bei der Haivision KB-Benutzeroberfläche an.
2.  Klicken Sie auf die **Menüschaltfläche** im Kanalkontrollcenter, und wählen Sie **Add Channel** (Kanal hinzufügen) aus.  
    ![Screenshot 14.8.2017 um 9:15:09 Uhr](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Geben Sie den **Kanalnamen** im Feld „Name“ ein, und klicken Sie auf „Weiter“.  
    ![Screenshot 14.8.2017 um 9:19:07 Uhr](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wählen Sie im Dropdownmenü **Input Source** (Eingabequelle) die Option **Channel Input Source** (Kanaleingabequelle) aus, und klicken Sie auf „Weiter“.
    ![Screenshot 14.8.2017 um 9:20:44 Uhr](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Wählen Sie im Dropdownmenü **Encoder Template** (Encoder-Vorlage) die Option **H264-720-AAC-192** aus, und klicken Sie auf „Weiter“.
    ![Screenshot 14.8.2017 um 9:23:15 Uhr](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Wählen Sie im Dropdownmenü **Select New Output** (Neue Ausgabe auswählen) die Option **RTMP** aus, und klicken Sie auf „Weiter“.  
    ![Screenshot 14.8.2017 um 9:27:51 Uhr](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Füllen Sie im Fenster **Channel Output** (Kanalausgabe) die Azure-Datenstrominformationen aus. Fügen Sie den **RTMP**-Link von der ersten Kanaleinrichtung im Bereich **Server** ein. Geben Sie im Bereich **Output Name** (Ausgabename) den Namen des Kanals ein. Benennen Sie im Bereich „Stream Name Template“ (Vorlage für Datenstromnamen) den Datenstrom mithilfe der Vorlage „RTMPStreamName_%video_bitrate%“.
    ![Screenshot 14.8.2017 um 9:33:17 Uhr](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicken Sie auf „Weiter“ und anschließend auf „Fertig“.
9.  Klicken Sie auf die **Wiedergabeschaltfläche**, um den Encoder zu starten.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testen der Wiedergabe
Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Zeigen Sie mit dem Mauszeiger im Menü auf „Playback the Preview“ (Vorschau wiedergeben), und wählen Sie „with Azure Media Player“ (mit Azure Media Player) aus.

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Artikel zur Problembehandlung.

## <a name="create-a-program"></a>Erstellen eines Programms
1.  Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte „Live“ mit der rechten Maustaste in den Programmbereich, und wählen Sie „Create New Program“ (Neues Programm erstellen) aus.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Benennen Sie die Anwendung, und passen Sie ggf. die Archivfensterlänge an (Standardwert: vier Stunden). Sie können außerdem einen Speicherort angeben oder die Standardeinstellung beibehalten.
2.  Aktivieren Sie das Kontrollkästchen „Start the Program now“ (Programm jetzt starten).
3.  Klicken Sie auf „Create Program“ (Programm erstellen).
4.  Wenn die Anwendung ausgeführt wird, bestätigen Sie die Wiedergabe, indem Sie mit der rechten Maustaste auf das Programm klicken, zu „Playback the program(s)“ (Programme wiedergeben) navigieren und dann „with Azure Media Player“ (mit Azure Media Player) auswählen.
5.  Nach der Bestätigung klicken Sie mit der rechten Maustaste erneut auf das Programm, und wählen Sie „Copy the Output URL to Clipboard“ (Ausgabe-URL in die Zwischenablage kopieren) aus, bzw. rufen Sie diese Informationen über das Menü mit der Option „Program information and settings“ (Programminformationen und -einstellungen) ab.

Der Datenstrom kann jetzt in einen Player eingebettet oder an eine Zielgruppe für die Livewiedergabe verteilt werden.

> [!NOTE]
> Die Programmerstellung erfordert weniger Zeit als die Kanalerstellung.
