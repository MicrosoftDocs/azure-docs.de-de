---
title: Analysieren von Livevideo mit Live Video Analytics in IoT Edge and Azure Custom Vision
description: Erfahren Sie, wie Sie mit Azure Custom Vision ein Containermodell erstellen können, mit dem ein Spielzeug-LKW erkannt wird. Stellen Sie mithilfe der KI-Erweiterbarkeitsfunktion von Azure Live Video Analytics in Azure IoT Edge das Modell auf Edge-Ebene bereit, um in einem Livevideostream Spielzeug-LKW zu erkennen.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 0b5d011ac7832436edf1f5c898b1fe1e239d0aea
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055364"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analysieren von Livevideo mit Live Video Analytics in IoT Edge and Azure Custom Vision

In diesem Tutorial erfahren Sie, wie Sie mit [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) ein Containermodell erstellen, mit dem ein Spielzeug-LKW erkannt wird, und mithilfe der [KI-Erweiterbarkeitsfunktion](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) von Azure Live Video Analytics in Azure IoT Edge das Modell auf Edge-Ebene bereitstellen, um in einem Livevideostream Spielzeug-LKW zu erkennen.

Wir zeigen Ihnen, wie Sie die Leistungsfähigkeit von Custom Vision nutzen, um ein Modell für maschinelles Sehen zu erstellen und zu trainieren, indem Sie einige Bilder heraufladen und mit Bezeichnungen versehen. Sie benötigen keinerlei Wissen über Data Science, maschinelles Lernen oder KI. Außerdem lernen Sie die Funktionen von Live Video Analytics kennen, mit denen Sie auf einfache Weise ein benutzerdefiniertes Modell als Container auf dem Edgecomputer bereitstellen und einen simulierten Livevideofeed analysieren können.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Das Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Einrichten der relevanten Ressourcen
> * Erstellen eines Custom Vision-Modells in der Cloud, um Spielzeug-LKW zu erkennen und auf Edge-Ebene bereitzustellen.
> * Erstellen und Bereitstellen eines Mediengraphen mit einer HTTP-Erweiterung für das Custom Vision-Modell
> * Ausführen des Beispielcodes
> * Überprüfen und Interpretieren der Ergebnisse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie die folgenden Artikel, bevor Sie beginnen:

* [Übersicht zu Live Video Analytics in IoT Edge](overview.md)
* [Übersicht über Azure Custom Vision](../../cognitive-services/custom-vision-service/overview.md) (in englischer Sprache)
* [Terminologie zu Live Video Analytics in IoT Edge](terminology.md)
* [Mediengraph: Konzepte](media-graph-concept.md)
* [Live Video Analytics ohne Videoaufzeichnung](analyze-live-video-concept.md)
* [Ausführen von Live Video Analytics mit eigenem Modell](use-your-model-quickstart.md)
* [Tutorial: Entwickeln eines IoT Edge-Moduls](../../iot-edge/tutorial-develop-for-linux.md)
* [Vorgehensweise beim Bearbeiten von „deployment.*.template.json“](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> Von diesem Custom Vision-Modul werden nur **Intel x86- und amd64-Architekturen** unterstützt. Überprüfen Sie die Architektur Ihres Edgegeräts an, bevor Sie fortfahren.

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

In diesem Tutorial wird mit einer [Spielzeugauto-Rückschlussvideodatei](https://lvamedia.blob.core.windows.net/public/t2.mkv) ein Livestream simuliert. Sie können das Video z. B. in der Anwendung [VLC Media Player](https://www.videolan.org/vlc/) überprüfen. Wählen Sie **STRG+N** aus, und fügen Sie dann einen Link zum [Spielzeugauto-Rückschlussvideo](https://lvamedia.blob.core.windows.net/public/t2.mkv) ein, um die Wiedergabe zu starten. Beachten Sie während der Wiedergabe des Videos, dass am 36-Sekunden-Marker ein Spielzeug-LKW im Video erscheint. Das benutzerdefinierte Modell wurde zum Erkennen dieses speziellen Spielzeug-LKW trainiert. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

In diesem Tutorial verwenden Sie Live Video Analytics in IoT Edge, um Spielzeug-LKW zu erkennen und entsprechende Rückschlussereignisse auf dem IoT Edge Hub zu veröffentlichen.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagramm, das eine Übersicht zu Custom Vision zeigt.":::

In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](media-graph-concept.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](media-graph-concept.md#http-extension-processor).

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys.  Dabei werden die eingehenden Videoframes, die durch das Feld `samplingOptions` festgelegt wurden, abgetastet und darüber hinaus in den angegebenen Bildtyp umgewandelt. Anschließend leitet der das Bild über REST an ein anderes Edge-Modul weiter, das ein KI-Modell hinter einem HTTP-Endpunkt ausführt. In diesem Beispiel handelt es sich bei dem Edge-Modul um das mithilfe von Custom Vision erstellte Spielzeug-LKW-Erkennungsmodell. Der Prozessorknoten der HTTP-Erweiterung erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse auf dem Knoten der [Azure IoT-Hub-Senke](media-graph-concept.md#iot-hub-message-sink). Anschließend sendet der Knoten diese Ereignisse an den [IoT Edge-Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Erstellen und Bereitstellen eines Custom Vision-Spielzeugerkennungsmodells 

Wie der Name besagt, können Sie mit Custom Vision eine eigene benutzerdefinierte Objekterkennung oder -klassifizierung in der Cloud erstellen. Es bietet eine einfache, benutzerfreundliche und intuitive Oberfläche zum Erstellen von Custom Vision-Modellen, die über Container in der Cloud oder auf Edge-Ebene bereitgestellt werden können.

Zum Erstellen einer Erkennung für Spielzeug-LKW befolgen Sie die Schritte in [Schnellstart: Erstellen einer Objekterkennung mit der Custom Vision-Website](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Von diesem Custom Vision-Modul werden nur **Intel x86- und amd64-Architekturen** unterstützt. Überprüfen Sie die Architektur Ihres Edgegeräts an, bevor Sie fortfahren.

Weitere Hinweise:
 
* Verwenden Sie für dieses Tutorial nicht die Beispielbilder, die im [Abschnitt „Voraussetzungen“](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) des Schnellstartartikels angegeben werden. Stattdessen haben wir einen bestimmten Satz Bilder verwendet, um ein Custom Vision Modell für die Spielzeugerkennung zu erstellen. Verwenden Sie [diese Bilder](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip), wenn Sie aufgefordert werden, [Ihre Trainingsbilder](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) in der Schnellstartanleitung auszuwählen.
* Stellen Sie im Schnellstartartikel im Abschnitt zum Kennzeichnen von Bildern sicher, dass Sie den Spielzeug-LKW im Bild mit dem Tag „delivery truck“ versehen.

Nachdem Sie den Vorgang abgeschlossen haben, können Sie das Modell in einen Docker-Container exportieren, indem Sie die Schaltfläche **Exportieren** auf der Registerkarte **Performance** (Leistung) verwenden. Achten Sie darauf, Linux als Typ der Containerplattform auszuwählen. Auf dieser Plattform wird der Container ausgeführt. Der Computer, auf dem Sie den Container herunterladen, kann ein Computer mit Windows oder Linux sein. Die folgenden Anweisungen basieren auf der Containerdatei, die auf einen Windows-Computer heruntergeladen wurde.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Bildschirm mit ausgewähltem Dockerfile.":::
 
1. Sie sollten auf den lokalen Computer eine ZIP-Datei mit dem Namen `<projectname>.DockerFile.Linux.zip` heruntergeladen haben. 
1. Überprüfen Sie, ob Docker installiert ist. Installieren Sie andernfalls [Docker](https://docs.docker.com/get-docker/) für Ihren Windows-Desktop.
1. Entpacken Sie die heruntergeladene Datei an einem Speicherort Ihrer Wahl. Verwenden Sie die Befehlszeile, um zum entzippten Ordnerverzeichnis zu wechseln.
    
    Führen Sie die folgenden Befehle aus:
    
    1. `docker build -t cvtruck` 
    
        Mit diesem Befehl werden viele Pakete heruntergeladen, wird das Docker-Image erstellt und mit der Bezeichnung `cvtruck:latest` versehen.
    
        > [!NOTE]
        > Im Erfolgsfall sollten die folgenden Meldungen angezeigt werden: `Successfully built <docker image id>` und `Successfully tagged cvtruck:latest`. Falls bei den Buildbefehlen ein Fehler auftritt, versuchen Sie es noch einmal. Manchmal werden Abhängigkeitspakete im ersten Durchgang nicht heruntergeladen.
    1. `docker  image ls`

        Mit diesem Befehl wird überprüft, ob sich das neue Bild in der lokalen Registrierung befindet.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Mit diesem Befehl wird der verfügbar gemachte Docker-Port (80) im Port des lokalen Computers (80) veröffentlicht.
    1. `docker container ls`
    
        Mit diesem Befehl werden die Portzuordnungen überprüft, und es wird überprüft, ob der Docker-Container auf Ihrem Computer erfolgreich ausgeführt wird. Die Ausgabe sollte ungefähr wie folgt lauten:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Dieser Befehl testet den Container auf dem lokalen Computer. Wenn das Bild den gleichen Lieferwagen zeigt, mit dem wir das Modell trainiert haben, sollte die Ausgabe etwas wie im folgenden Beispiel sein. Es deutet darauf hin, dass der Lieferwagen mit einer Wahrscheinlichkeit von 90,12 % erkannt wurde.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Untersuchen der Beispieldateien

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generieren und Bereitstellen des Bereitstellungsmanifests

1. Navigieren Sie in Visual Studio Code zu „src/cloud-to-device-console-app/operations.json“.

1. Vergewissern Sie sich, dass unter `GraphTopologySet` Folgendes zutrifft:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. Stellen Sie unter `GraphInstanceSet` Folgendes sicher:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Fügen Sie Folgendes oben zum Parameterarray hinzu: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Ändern Sie den Wert von Parameter `rtspUrl` in `"rtsp://rtspsim:554/media/t2.mkv"`.
1. Stellen Sie unter `GraphTopologyDelete` sicher, dass `"name": "InferencingWithHttpExtension"` ist.
1. Klicken Sie mit der rechten Maustaste auf die Datei „src/edge/deployment.customvision.template.json“, und wählen Sie dann **IoT Edge-Bereitstellungsmanifest generieren** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Screenshot, der das Generieren des IoT Edge-Bereitstellungsmanifests zeigt.":::
  
    Durch diese Aktion sollte im Ordner „src/edge/config“ die Manifestdatei „deployment.customvision.amd64.json“ erstellt werden.
1. Öffnen Sie die Datei „src/edge/ deployment.customvision.template.json“, und suchen Sie den JSON-Block `registryCredentials`. Dieser Block enthält die Adresse Ihrer Azure-Containerregistrierung sowie Ihren Benutzernamen und Ihr Kennwort.
1. Pushen Sie mit den folgenden Schritten an der Befehlszeile den lokalen Custom Vision-Container in Ihre Azure Container Registry-Instanz:

    1. Melden Sie sich mit dem folgenden Befehl bei der Registrierung an:
    
        `docker login <address>`
    
        Geben Sie den Benutzernamen und das Kennwort ein, wenn Sie zur Authentifizierung aufgefordert werden.
        
        > [!NOTE]
        > Das Kennwort ist in der Befehlszeile nicht sichtbar.
    1. Kennzeichnen Sie Ihr Image mithilfe des folgenden Befehls: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Pushen Sie Ihr Image, indem Sie diesen Befehl ausführen: <br/>`docker push <address>/cvtruck`.

        Bei Erfolg sollte `Pushed` in der Befehlszeile zusammen mit der SHA für das Image angezeigt werden.
    1. Sie können stattdessen auch die Azure Container Registry-Instanz im Azure-Portal überprüfen. Hier sehen Sie den Namen des Repositorys zusammen mit dem Tag.
1. Legen Sie die IoT Hub-Verbindungszeichenfolge fest, indem Sie im Bereich **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** auswählen. Sie können die Zeichenfolge aus der Datei „appsettings.json“ kopieren. (Hier finden Sie eine weitere empfohlene Vorgehensweise, um unter Verwendung des [Befehls zum Auswählen von IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub) sicherzustellen, dass in Visual Studio Code der richtige IoT-Hub konfiguriert ist.)

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Screenshot, der das Festlegen der IoT-Hub-Verbindungszeichenfolge zeigt.":::
1. Klicken Sie dann mit der rechten Maustaste auf „src/edge/config/deployment.customvision.amd64.json“, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Screenshot, der das Erstellen der Bereitstellung für ein Einzelgerät zeigt.":::
1. Sie werden anschließend aufgefordert, ein IoT-Hub-Gerät auszuwählen. Wählen Sie in der Dropdownliste **lva-sample-device** aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden den Azure IoT-Hub im unteren linken Bereich. Die folgenden Module sollten im Edge-Gerät als bereitgestellt angezeigt werden:

    * Das Live Video Analytics in IoT Edge-Modul mit dem Namen `lvaEdge`.
    * Ein Modul mit dem Namen `rtspsim`, das einen RTSP-Server simuliert, der als Quelle eines Livevideofeeds fungiert.
    * Ein Modul mit dem Namen `cv`. Dies ist das Custom Vision-Erkennungsmodell für Spielzeug-LKW, das Custom Vision auf die Bilder anwendet und mehrere Tagtypen zurückgibt. (Unser Modell wurde nur mit einem Tag trainiert – „delivery truck“).

## <a name="prepare-for-monitoring-events"></a>Vorbereiten der Überwachung von Ereignissen

Klicken Sie mit der rechten Maustaste auf das Live Video Analytics-Gerät, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus. Sie müssen diesen Schritt ausführen, um die IoT Hub-Ereignisse im **Ausgabefenster** von Visual Studio Code zu überwachen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Screenshot, der „Überwachung des integrierten Ereignisendpunkts starten“ zeigt.":::

## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

Wenn Sie die Graphtopologie für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `inferencingUrl` auf `http://cv:80/image` festgelegt wurde. Diese Einstellung bedeutet, dass der Rückschlussserver nach dem Erkennen von Spielzeug-LKW im Livevideo Ergebnisse zurückgibt, falls welche vorhanden sind.

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder wählen Sie **STRG+UMSCHALT+X** aus), und suchen Sie nach „Azure IoT-Hub“.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Screenshot von „Erweiterungseinstellungen“.":::
1. Suchen Sie **Show Verbose Message** (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Screenshot von „Ausführliche Meldung anzeigen“.":::
1. Drücken Sie die **F5**-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von operations.json beginnt mit Aufrufen der direkten Methoden `GraphTopologyList` und `GraphInstanceList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die **EINGABETASTE**, um den Vorgang fortzusetzen.
    
   Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:
    
   * Ein Aufruf von `GraphTopologySet`, der die vorherige `topologyUrl` verwendet
   * Ein Aufruf von `GraphInstanceSet`, der den folgenden Textkörper verwendet:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
   ```
    
   * Ein Aufruf von `GraphInstanceActivate`, der die Graphinstanz und den Videodatenfluss startet
   * Zweiter Aufruf von `GraphInstanceList` mit der Anzeige, dass sich die Graphinstanz im ausgeführten Zustand befindet
    
1. Die Ausgabe im **Terminalfenster** wird an einer Aufforderung **Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen** angehalten. Warten Sie noch mit dem Drücken der **EINGABETASTE**. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Meldungen angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieses Tutorials sind diese Meldungen beschrieben.
1. Der Mediengraph wird weiter ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden des Mediengraphs zurück zum **Terminalfenster**, und drücken Sie die **EINGABETASTE**.
Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
    
   * Mit dem Aufruf von `GraphInstanceDeactivate` wird die Graphinstanz deaktiviert.
   * Mit dem Aufruf von `GraphInstanceDelete` wird die Instanz gelöscht.
   * Mit dem Aufruf von `GraphTopologyDelete` wird die Topologie gelöscht.
   * Ein abschließender Aufruf von `GraphTopologyList` zeigt, dass die Liste leer ist.
    
## <a name="interpret-the-results"></a>Interpretieren der Ergebnisse

Wenn Sie den Mediengraph ausführen, werden die Ergebnisse aus dem Knoten des HTTP-Erweiterungsprozessors über den Knoten der IoT Hub-Senke an den IoT-Hub übergeben. Die Meldungen, die im **Ausgabefenster** angezeigt werden, enthalten einen Hauptteil und einen Abschnitt `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Live Video Analytics-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp lautet **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Beachten Sie in dieser Meldung diese Details:

* Bei der Meldung handelt es sich um ein Diagnoseereignis. `MediaSessionEstablished` zeigt an, dass der RTSP-Quellknoten (das Subjekt), mit dem RTSP-Simulator verbunden wurde und begonnen hat, einen simulierten Livefeed zu empfangen.
* In `applicationProperties` wird mit `subject` angegeben, dass die Meldung über den Knoten der RTSP-Quelle im Mediengraphen generiert wurde.
* In `applicationProperties` gibt der Ereignistyp an, dass es sich bei diesem Ereignis um ein Diagnoseereignis handelt.
* Der Ereigniszeitpunkt gibt die Uhrzeit an, zu der das Ereignis aufgetreten ist.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der Knoten des HTTP-Erweiterungsprozessors empfängt Rückschlussergebnisse vom Custom Vision-Container und gibt sie über den Knoten der IoT Hub-Senke als Rückschlussereignisse aus.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Beachten Sie die folgenden Informationen in den vorangehenden Meldungen:

* Das Subjekt in `applicationProperties` verweist auf den Knoten im MediaGraph, von dem die Meldung generiert wurde. In diesem Fall stammt die Meldung vom Prozessor der HTTP-Erweiterung.
* Der „eventType“ in `applicationProperties` gibt an, dass es sich um ein Analyserückschlussereignis handelt.
* Der Ereigniszeitpunkt gibt die Uhrzeit an, zu der das Ereignis aufgetreten ist.
* Der Textkörper enthält Daten zu dem Analyseereignis. In diesem Fall ist das Ereignis ein Rückschlussereignis. Daher enthält der Textkörper ein Array von Rückschlüssen, die als „predictions“ (Vorhersagen) bezeichnet werden.
* Der Abschnitt „predictions“ enthält eine Liste der Vorhersagen, wo sich ein Lieferwagen (Tag = „delivery truck“) im Frame befindet. Sie werden sich erinnern – „delivery truck“ ist der benutzerdefinierte Tag, den Sie Ihrem benutzerdefinierten trainierten Modell für den Spielzeug-LKW zur Verfügung gestellt haben. Das Modell führt Rückschlüsse auf den Spielzeug-LKW im Eingabevideo durch und identifiziert ihn mit verschiedenen Konfidenzwerten für die Wahrscheinlichkeit.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die anderen Tutorials und Schnellstartanleitungen durcharbeiten möchten, behalten Sie die von Ihnen erstellten Ressourcen. Wechseln Sie andernfalls zum Azure-Portal, navigieren Sie zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial durchgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle eines virtuellen Azure-Linux-Computers. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md) befolgen.

Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md) ausführen.
