---
title: Erstellen eines Livestreams mit OBS Studio
description: Erfahren Sie, wie mit dem Portal und OBS Studio ein Azure Media Services-Livestream erstellt wird.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956071"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Erstellen eines Azure Media Services-Livestreams mit OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Schnellstart wird beschrieben, wie Sie mithilfe des Azure-Portals und Open Broadcasting Studio (OBS) einen Azure Media Services-Livestream erstellen Hierbei wird vorausgesetzt, dass Sie über ein Azure-Abonnement verfügen und ein Media Services-Konto erstellt haben.

In dieser Schnellstartanleitung wird Folgendes behandelt:

- Einrichten eines lokalen Encoders mit OBS
- Einrichten eines Livestreams
- Einrichten von Livestreamausgaben
- Ausführen eines Standard-Streamingendpunkts
- Anzeigen des Livestreams und der On-Demand-Ausgabe in Azure Media Player

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Microsoft Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Einrichten eines lokalen Encoders mithilfe von OBS

1. Laden Sie OBS für Ihr Betriebssystem von der [Open Broadcaster Software-Website](https://obsproject.com/) herunter, und installieren Sie es.
1. Starten Sie die Anwendung, und lassen Sie sie geöffnet.

## <a name="run-the-default-streaming-endpoint"></a>Ausführen des Standard-Streamingendpunkts

1. Wählen Sie in der Media Services-Liste die Option **Streamingendpunkte** aus.

   ![Menüelement „Streamingendpunkte“](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Wählen Sie den Standard-Streamingendpunkt aus, wenn sein Status „Beendet“ lautet. Mit diesem Schritt gelangen Sie auf die Seite des Endpunkts.
1. Wählen Sie **Starten** aus.

   ![Schaltfläche „Starten“ für den Streamingendpunkt](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Einrichten eines Azure Media Services-Livestreams

1. Greifen Sie im Portal auf das Azure Media Services-Konto zu, und wählen Sie anschließend in der Liste **Media Services** die Option **Livestreaming** aus.

   ![Link „Livestreaming“](media/live-events-obs-quickstart/select-live-streaming.png)
1. Wählen Sie **Liveereignis hinzufügen** aus, um ein neues Livestreamingereignis zu erstellen.

   ![Symbol „Liveereignis hinzufügen“](media/live-events-obs-quickstart/add-live-event.png)
1. Geben Sie im Feld **Name des Liveereignisses** einen Namen für Ihr neues Ereignis ein (z. B. *TestLiveEvent*).

   ![Feld „Name des Liveereignisses“](media/live-events-obs-quickstart/live-event-name.png)
1. Geben Sie im Feld **Beschreibung** eine optionale Beschreibung des Ereignisses ein.
1. Aktivieren Sie die Option **Passthrough – keine Cloudcodierung**.

   ![Option „Cloudcodierung“](media/live-events-obs-quickstart/cloud-encoding.png)
1. Wählen Sie die Option **RTMP** aus.
1. Vergewissern Sie sich, dass für **Liveereignis starten** die Option **Nein** ausgewählt ist. So vermeiden Sie, dass für das Liveereignis Kosten anfallen, bevor es bereit ist. (Die Abrechnung beginnt, nachdem das Liveereignis gestartet wurde.)

   ![Option „Liveereignis starten“](media/live-events-obs-quickstart/start-live-event-no.png)
1. Wählen Sie die Schaltfläche **Bewerten + erstellen** aus, um die Einstellungen zu überprüfen.
1. Wählen Sie die Schaltfläche **Erstellen** aus, um das Liveereignis zu erstellen. Nun wird wieder die Liste mit den Liveereignissen angezeigt.
1. Wählen Sie den Link zum Liveereignis aus, das Sie soeben erstellt haben. Beachten Sie, dass Ihr Ereignis beendet ist.
1. Lassen Sie diese Seite in Ihrem Browser geöffnet. Wir werden später darauf zurückkommen.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Einrichten eines Livestreams mit OBS Studio

OBS wird mit einer Standardszene aber ohne ausgewählte Eingaben gestartet.

   ![OBS-Standardbildschirm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Videoquelle hinzufügen

1. Klicken Sie im Bereich **Quellen** auf das Symbol **hinzufügen**, um ein neues Quellgerät auszuwählen. Das Menü **Quellen** wird geöffnet.

1. Wählen Sie im Quellgerätmenü die Option **Videoaufnahmegerät** aus. Das Menü **Quelle erstellen/auswählen** wird geöffnet.

   ![OBS-Quellenmenü mit ausgewähltem Videogerät](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Wählen Sie das Optionsfeld **Vorhandene hinzufügen** aus, und klicken Sie dann auf **OK**. Das Menü mit **Eigenschaften für Videogeräte** wird geöffnet.

   ![OBS-Menü für neue Videoquelle mit „Vorhandene hinzufügen“ ausgewählt](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Wählen Sie in der Dropdownliste **Geräte** die Videoeingabe aus, die Sie für die Übertragung verwenden möchten. Lassen Sie die restlichen Einstellungen bis auf Weiteres unverändert, und klicken Sie auf **OK**. Die Eingabequelle wird dem Bereich **Quellen** hinzugefügt, und die Videoeingabeansicht wird im Bereich **Vorschau** angezeigt.

   ![OBS-Kameraeinstellungen](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Audioquelle hinzufügen

1. Klicken Sie im Bereich **Quellen** auf das Symbol **hinzufügen**, um ein neues Quellgerät auszuwählen. Das Menü „Quellgerät“ wird geöffnet.

1. Wählen Sie im Quellgerätmenü die Option **Audioaufnahmegerät** aus. Das Menü **Quelle erstellen/auswählen** wird geöffnet.

   ![OBS-Quellenmenü mit ausgewähltem Audiogerät](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Wählen Sie das Optionsfeld **Vorhandene hinzufügen** aus, und klicken Sie dann auf **OK**. Das Menü mit **Eigenschaften für die Erfassung von Audioeingaben** wird geöffnet.

   ![OBS-Audioquelle mit „Vorhandene hinzufügen“ ausgewählt ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Wählen Sie in der Dropdownliste **Geräte** das Audioaufnahmegerät aus, das Sie für die Übertragung verwenden möchten. Lassen Sie die restlichen Einstellungen bis auf Weiteres unverändert, und klicken Sie auf „OK“. Das Audioaufnahmegerät wird dem Audiomixerbereich hinzugefügt.

   ![Dropdownliste für die Auswahl des OBS-Audiogeräts](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Einrichten von Streaming in OBS

In den nächsten Schritten kehren Sie zu Azure Media Services in Ihrem Browser zurück, um die Eingabe-URL zu kopieren, damit Sie sie in die Ausgabeeinstellungen eingeben können:

1. Wählen Sie im Portal auf der Seite „Azure Media Services“ die Option **Starten** aus, um das Livestreamereignis zu starten. (Die Abrechnung beginnt.)

   ![Symbol „Starten“](media/live-events-obs-quickstart/start.png)
1. Stellen Sie den **RTMP**-Umschalter auf **RTMPS** ein.
1. Kopieren Sie die URL aus dem Feld **Eingabe-URL** in die Zwischenablage.

   ![Eingabe-URL](media/live-events-obs-quickstart/input-url.png)

1. Wechseln Sie zur OBS-Anwendung.

1. Klicken Sie im Bereich **Steuerelemente** auf die Schaltfläche **Einstellungen**. Die Einstellungsoptionen werden geöffnet.

   ![OBS-Steuerelementbereich mit aktivierten Einstellungen](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Wählen Sie im Menü **Einstellungen** die Option **Stream** aus.

1. Wählen Sie in der Dropdownliste **Dienst** die Option „Alle anzeigen“ aus, und wählen Sie dann **Benutzerdefiniert…** aus.

1. Fügen Sie im Feld **Server** die RTMPS-URL aus der Zwischenablage ein.

1. Geben Sie einen Wert in das Feld **Streamschlüssel** ein.  Der Wert spielt keine Rolle, muss aber vorhanden sein.

    ![OBS-Streameinstellungen](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Wählen Sie im Menü **Einstellungen** die Option **Ausgabe** aus.

1. Geben Sie *2* in das Feld **Keyframe-Intervall** ein. Dadurch wird die Fragmentlänge auf 2 Sekunden festgelegt. Verwenden Sie für eine Liveübertragung mit kürzerer Wartezeit 1 Sekunde als Wert.

1. OPTIONAL: Legen Sie die Voreinstellung für die **CPU-Auslastung** auf *veryfast* fest, wenn Sie einen Computer mit wenig Verarbeitungsleistung verwenden. Optional können Sie die Kbit/s auf einen niedrigeren Wert festlegen, wenn ungünstige Netzwerkbedingungen vorliegen.

   ![OBS-Ausgabeeinstellungen](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Behalten Sie die restlichen Einstellungen unverändert bei, und klicken Sie auf **OK**.

### <a name="start-streaming"></a>Streaming starten

1. Klicken Sie im Bereich **Steuerelemente** auf **Streaming starten**.

    ![OBS-Schaltfläche „Streaming starten“](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Wechseln Sie in Ihrem Browser zum Liveereignis-Bildschirm von Azure Media Services, und klicken Sie auf den Link **Player neu laden**. Der Stream sollte jetzt im Vorschauplayer angezeigt werden.

## <a name="set-up-outputs"></a>Einrichten von Ausgaben

In diesem Teil werden Ihre Ausgaben und eine Möglichkeit zum Aufzeichnen Ihres Livestreams eingerichtet.  

> [!NOTE]
> Zum Streamen dieser Ausgabe muss der Streamingendpunkt aktiv sein. Weitere Informationen finden Sie unten im Abschnitt [Ausführen des Standard-Streamingendpunkts](#run-the-default-streaming-endpoint).

1. Wählen Sie unter der Videoansicht **Ausgaben** den Link **Ausgaben erstellen** aus.
1. Der Name der Ausgabe kann im Feld **Name** auf Wunsch in einen benutzerfreundlicheren Wert geändert werden, damit die Ausgabe später leichter zu finden ist.

   ![Feld „Name“ der Ausgabe](media/live-events-wirecast-quickstart/output-name.png)
1. Lassen Sie alle anderen Felder vorerst unverändert.
1. Wählen Sie **Weiter** aus, um einen Streaminglocator hinzuzufügen.
1. Ändern Sie den Namen des Locators ggf. in einen benutzerfreundlicheren Namen.

   ![Feld „Name“ für Locator](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lassen Sie alle anderen Angaben unverändert.
1. Klicken Sie auf **Erstellen**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Wiedergeben der Ausgabeübertragung mit Azure Media Player

1. Kopieren Sie die Streaming-URL unter dem Videoplayer für die **Ausgabe**.
1. Öffnen Sie in einem Webbrowser die [Demoversion für Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Fügen Sie die Streaming-URL in das Feld **URL** von Azure Media Player ein.
1. Wählen Sie die Schaltfläche **Player aktualisieren** aus.
1. Wählen Sie für das Video das Symbol **Wiedergeben** aus, um Ihren Livestream anzuzeigen.

## <a name="stop-the-broadcast"></a>Beenden der Übertragung

Beenden Sie die Übertragung, wenn Sie genügend Inhalt gestreamt haben.

1. Wählen Sie im Portal die Option **Beenden** aus.

1. Wählen Sie in OBS im Bereich **Steuerelemente** die Schaltfläche **Streaming beenden** aus. Hiermit wird die Übertragung aus OBS beendet.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Wiedergeben der On-Demand-Ausgabe mit Azure Media Player

Die von Ihnen erstellte Ausgabe steht nun für On-Demand-Streaming zur Verfügung, solange Ihr Streamingendpunkt aktiv ist.

1. Navigieren Sie zur Media Services-Liste, und wählen Sie **Medienobjekte** aus.
1. Suchen Sie nach der von Ihnen erstellten Ereignisausgabe, und wählen Sie den Link zum Medienobjekt aus. Die Seite für die Medienobjektausgabe wird geöffnet.
1. Kopieren Sie die Streaming-URL unter dem Videoplayer für das Medienobjekt.
1. Wechseln Sie zurück zu Azure Media Player im Browser, und fügen Sie die Streaming-URL in das Feld „URL“ ein.
1. Wählen Sie **Player aktualisieren**.
1. Wählen Sie im Video das Symbol **Wiedergeben** aus, um das On-Demand-Medienobjekt anzuzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

> [!IMPORTANT]
> Beenden Sie die Dienste. Nach Abschluss der Schritte in dieser Schnellstartanleitung sollten Sie das Liveereignis und den Streamingendpunkt unbedingt beenden, da Ihnen ansonsten die weitere Ausführungszeit in Rechnung gestellt wird. Informationen zum Beenden des Liveereignisses finden Sie unter [Beenden der Übertragung](#stop-the-broadcast) (Schritt 2 und 3).

Beenden Sie den Streamingendpunkt wie folgt:

1. Wählen Sie in der Media Services-Liste die Option **Streamingendpunkte** aus.
2. Wählen Sie den Standard-Streamingendpunkt aus, den Sie weiter oben gestartet haben. Durch diesen Schritt wird die Seite des Endpunkts geöffnet.
3. Wählen Sie **Stop** (Beenden) aus.

> [!TIP]
> Wenn Sie die Medienobjekte dieses Ereignisses nicht mehr benötigen, sollten Sie sie löschen, damit keine Kosten für die Speicherung mehr anfallen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Liveereignisse und Liveausgaben in Media Services](./live-events-outputs-concept.md)
