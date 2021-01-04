---
title: Analysieren von Livevideos mithilfe der KI-Erweiterung für OpenVINO™ Model Server von Intel
description: In diesem Tutorial wird ein von Intel bereitgestellter KI-Modellserver verwendet, um den Livevideofeed einer (simulierten) IP-Kamera zu analysieren.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 82906111e64bd278d4371d1c3497fefc4510bbbd
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401207"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Tutorial: Analysieren von Livevideos mithilfe der KI-Erweiterung für OpenVINO™ Model Server von Intel 

In diesem Tutorial erfahren Sie, wie Sie die KI-Erweiterung für OpenVINO™ Model Server von Intel verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Es wird gezeigt, wie Sie über diesen Rückschlussserver auf Modelle für die Objekterkennung (Person, Fahrzeug oder Fahrrad) sowie auf ein Modell für die Klassifizierung von Fahrzeugen zugreifen. Eine Teilmenge der Frames aus dem Livevideofeed wird an diesen Rückschlussserver gesendet, und die Ergebnisse werden an den IoT Edge-Hub übermittelt.

In diesem Tutorial werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet. Es basiert auf Beispielcode, der in C# geschrieben ist, und baut auf der Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) auf.

> [!NOTE]
> Für dieses Tutorial muss ein x86-64-Computer als Edgegerät verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto das ein aktives Abonnement beinhaltet. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Sollten Sie die Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) nicht durchlaufen haben, führen Sie die Schritte zum [Einrichten der Azure-Ressourcen](detect-motion-emit-events-quickstart.md#set-up-azure-resources) aus.

> [!TIP]
> Bei der Installation von Azure IoT Tools werden Sie unter Umständen aufgefordert, Docker zu installieren. Sie können diese Aufforderung ignorieren.

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen wird ein kurzes Parkplatzvideo auf den virtuellen Linux-Computer in Azure kopiert, den Sie als IoT Edge-Gerät verwenden. In dieser Schnellstartanleitung wird die Videodatei verwendet, um einen Livestream zu simulieren.

Öffnen Sie eine Anwendung wie etwa den [VLC Media Player](https://www.videolan.org/vlc/). Drücken Sie STRG+N, und fügen Sie dann einen Link zum [Video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) ein, um die Wiedergabe zu starten. Das Video zeigt Fahrzeuge auf einem Parkplatz. Die meisten davon sind geparkt. Ein einzelnes Fahrzeug bewegt sich.

In dieser Schnellstartanleitung werden Live Video Analytics in IoT Edge und die KI-Erweiterung für OpenVINO™ Model Server von Intel verwendet, um Objekte wie Fahrzeuge zu erkennen oder diese zu klassifizieren. Die resultierenden Rückschlussereignisse werden im IoT Edge-Hub veröffentlicht.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Übersicht":::

In diesem Diagramm ist der Fluss der Signale in diesem Schnellstart dargestellt. Ein [Edge-Modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](media-graph-concept.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](media-graph-concept.md#http-extension-processor). 

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Dabei werden die eingehenden Videoframes, die durch das Feld `samplingOptions` festgelegt wurden, abgetastet und darüber hinaus in den angegebenen Bildtyp umgewandelt. Anschließend leitet er das Bild per REST an ein anderes Edge-Modul weiter, das KI-Modelle hinter einem HTTP-Endpunkt ausführt. In diesem Beispiel wird als Edge-Modul die KI-Erweiterung für OpenVINO™ Model Server von Intel verwendet. Der Knoten des HTTP-Erweiterungsprozessors erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Senke](media-graph-concept.md#iot-hub-message-sink). Der Knoten sendet diese Ereignisse dann an den [IoT Edge Hub-](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

In diesem Lernprogramm lernen Sie Folgendes:

1. Erstellen, Bereitstellen und Ändern des Mediengraphen
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Informationen zur KI-Erweiterung für OpenVINO™ Model Server von Intel

Die Intel®-Distribution des [OpenVINO™-Toolkits](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual Inference and Neural network Optimization) ist ein kostenloses Softwarekit, das Entwickler und Data Scientists dabei unterstützt, Workloads für maschinelles Sehen zu beschleunigen, Deep Learning-Rückschlüsse und -Bereitstellungen zu optimieren und eine mühelose, heterogene Ausführung auf Intel®-Plattformen zwischen Edge und Cloud zu ermöglichen. Es umfasst das Intel® Deep Learning Deployment Toolkit mit Modelloptimierung und Rückschluss-Engine sowie das Repository [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) mit über 40 optimierten vortrainierten Modellen.

Zur Erstellung komplexer Hochleistungslösungen für die Livevideoanalyse empfiehlt es sich, Live Video Analytics im IoT Edge-Modul mit einer leistungsfähigen Rückschluss-Engine zu kombinieren, die das Skalierungspotential am Edge nutzen kann. In diesem Tutorial werden Rückschlussanforderungen an die [KI-Erweiterung für OpenVINO™ Model Server von Intel](https://aka.ms/lva-intel-ovms) gesendet – ein Edge-Modul, das für die Zusammenarbeit mit Live Video Analytics in IoT Edge konzipiert wurde. Dieses Rückschlussservermodul enthält OpenVINO™ Model Server (OVMS) – einen auf dem OpenVINO™-Toolkit basierenden Rückschlussserver, der speziell für Workloads für maschinelles Sehen optimiert und für Intel®-Architekturen entwickelt wurde. OVMS wurde mit einer Erweiterung für den unkomplizierten Austausch von Videoframes und Rückschlussergebnissen zwischen dem Rückschlussserver und Live Video Analytics im IoT Edge-Modul versehen. Damit können Sie ein beliebiges, vom OpenVINO™-Toolkit unterstütztes Modell ausführen. (Zum Anpassen des Rückschlussservermoduls ändern Sie den [Code](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper).) Darüber hinaus steht eine umfassende Palette an Beschleunigungsmechanismen zur Auswahl, die über Intel®-Hardware bereitgestellt werden. Hierzu zählen CPUs (Atom, Core, Xeon), FPGAs und VPUs.

Das erste Release dieses Rückschlussservers bietet Zugriff auf folgende [Modelle](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Fahrzeugerkennung (Rückschluss-URL: http://{Modulname}:4000/vehicleDetection)
- Personen-/Fahrzeug-/Fahrraderkennung (Rückschluss-URL: http://{Modulname}:4000/personVehicleBikeDetection)
- Fahrzeugklassifizierung (Rückschluss-URL: http://{Modulname}:4000/vehicleClassification)
- Gesichtserkennung (Rückschluss-URL: http://{Modulname}:4000/faceDetection)

> [!NOTE]
> Wenn Sie das Edge-Modul der KI-Erweiterung für OpenVINO™ Model Server von Intel und die darin enthaltene Software herunterladen und verwenden, stimmen Sie den Geschäftsbedingungen der [Lizenzvereinbarung](https://www.intel.com/content/www/us/en/legal/terms-of-use.html) zu.
> Intel setzt sich für die Achtung der Menschenrechte ein und ist darauf bedacht, eine Mitschuld an Menschenrechtsverletzungen zu vermeiden. Weitere Informationen finden Sie in den [globalen Menschenrechtsprinzipien von Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkte und Software von Intel dürfen nur in Anwendungen verwendet werden, die kein international anerkanntes Menschenrecht verletzen oder zu einer solchen Verletzung beitragen.

## <a name="create-and-deploy-the-media-graph"></a>Erstellen und Bereitstellen des Mediengraphen

### <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

Als Teil der Voraussetzungen haben Sie den Beispielcode in einen Ordner heruntergeladen. Führen Sie die unten angegebenen Schritte aus, um die Beispieldateien zu überprüfen und zu bearbeiten.

1. Navigieren Sie in Visual Studio Code zu *src/edge*. Ihre *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.

    Die Bereitstellungsvorlage verweist auf das Bereitstellungsmanifest für das Edge-Gerät. Es enthält einige Platzhalterwerte. Die *ENV*-Datei enthält die Werte für diese Variablen.

1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin finden Sie Ihre Datei *appsettings.json* und einige andere Dateien:

    * ***c2d-console-app.csproj** _: Die Projektdatei für Visual Studio Code.
    _ ***operations.json** _: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    _ ***Program.cs** _: Das Beispiel für den Programmcode. Mit diesem Code wird Folgendes durchgeführt:

        _ Laden der App-Einstellungen
        * Aufrufen direkter Methoden, die vom Modul „Live Video Analytics in IoT Edge“ verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.
        * Anhalten der Ausführung, sodass Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
        * Aufrufen direkter Methoden zur Bereinigung der Ressourcen


1. Bearbeiten Sie die Datei *operations.json*:
    * Ändern Sie den Link zur Graphtopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json"`

    * Bearbeiten Sie unter `GraphInstanceSet` den Namen der Graphtopologie, damit er mit dem Wert im vorherigen Link übereinstimmt:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Ändern Sie unter `GraphTopologyDelete` den Namen:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests

1. Klicken Sie mit der rechten Maustaste auf die Datei *src/edge/deployment.openvino.template.json*, und wählen Sie **IoT Edge-Bereitstellungsmanifest generieren** aus.

    ![Generate IoT Edge Deployment Manifest (IoT Edge-Bereitstellungsmanifest generieren)](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Die Manifestdatei *deployment.yolov3.amd64.json* wird im Ordner *src/edge/config* erstellt.

1. Überspringen Sie diesen Schritt, wenn Sie die Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) abgeschlossen haben. 

    Wählen Sie andernfalls in der Nähe des Bereichs **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** und dann **Festlegen der IoT Hub-Verbindungszeichenfolge** aus. Sie können die Zeichenfolge aus der Datei *appsettings.json* kopieren. Um sicherzustellen, dass Sie den richtigen IoT Hub in Visual Studio Code konfiguriert haben, verwenden Sie alternativ den Befehl [IoT Hub auswählen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Festlegen der IoT Hub-Verbindungszeichenfolge](./media/quickstarts/set-iotconnection-string.png)

1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/deployment.openvino.amd64.json*, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus. 

    ![Create Deployment for Single Device (Bereitstellung für einzelnes Gerät erstellen)](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Wenn Sie aufgefordert werden, ein IoT Hub-Gerät auszuwählen, wählen Sie **lva-sample-device** aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

    * Live Video Analytics-Modul mit dem Namen **lvaEdge**
    * Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert
    * Modul **openvino**, (das KI-Erweiterungsmodul für OpenVINO™ Model Server von Intel) 

### <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

Klicken Sie mit der rechten Maustaste auf das Live Video Analytics-Gerät, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus. Sie müssen diesen Schritt ausführen, um die IoT Hub-Ereignisse im **Ausgabefenster** von Visual Studio Code zu überwachen. 

![Starten der Überwachung](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Ausführen des Beispielprogramms für die Fahrzeugerkennung
Wenn Sie die [Graphtopologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `inferencingUrl` auf `http://openvino:4000/vehicleDetection` festgelegt wurde. Das bedeutet, dass der Rückschlussserver nach der Erkennung von Fahrzeugen im Livevideo Ergebnisse zurückgibt (sofern Fahrzeuge erkannt werden).

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von *operations.json* beginnt mit Aufrufen der direkten Methoden `GraphTopologyList` und `GraphInstanceList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.

    Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

     * Ein Aufruf von `GraphTopologySet`, der die vorherige `topologyUrl` verwendet
     * Ein Aufruf von `GraphInstanceSet`, der den folgenden Textkörper verwendet:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. Die Ausgabe im **Terminalfenster** wird mit der Aufforderung `Press Enter to continue` angehalten. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Meldungen angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
1. Der Mediengraph wird weiter ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden des Mediengraphs zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE. 

    Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
      * Mit dem Aufruf von `GraphInstanceDeactivate` wird die Graphinstanz deaktiviert.
      * Mit dem Aufruf von `GraphInstanceDelete` wird die Instanz gelöscht.
      * Mit dem Aufruf von `GraphTopologyDelete` wird die Topologie gelöscht.
      * Ein abschließender Aufruf von `GraphTopologyList` zeigt, dass die Liste leer ist.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie den Mediengraph ausführen, werden die Ergebnisse aus dem Knoten des HTTP-Erweiterungsprozessors über den Knoten der IoT Hub-Senke an den IoT-Hub übergeben. Die im **Ausgabefenster** angezeigten Meldungen enthalten jeweils die Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Live Video Analytics-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp ist `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

Beachten Sie in dieser Meldung diese Details:

* Bei der Meldung handelt es sich um ein Diagnoseereignis. `MediaSessionEstablished` gibt an, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* In `applicationProperties` wird mit `subject` angegeben, dass die Meldung über den Knoten der RTSP-Quelle im Mediengraph generiert wurde.
* In `applicationProperties` wird mit `eventType` angegeben, dass es sich um ein Diagnoseereignis handelt.
* Die `eventTime` gibt den Zeitpunkt des Ereignisses an.
* Der `body` enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der Knoten des HTTP-Erweiterungsprozessors empfängt Rückschlussergebnisse vom KI-Erweiterungsmodul für OpenVINO™ Model Server. Anschließend gibt er die Ergebnisse über den Knoten der IoT Hub-Senke als Rückschlussereignisse aus. 

In diesen Ereignissen ist der Typ auf `entity` festgelegt, um anzuzeigen, dass es sich um eine Entität wie etwa ein Auto oder einen LKW handelt. Der Wert von `eventTime` ist die UTC-Uhrzeit, zu der das Objekt erkannt wurde. 

Im folgenden Beispiel wurden zwei Fahrzeuge mit einem Zuverlässigkeitswert über 0,9 erkannt.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

Beachten Sie in den Meldungen die folgenden Details:

* In `applicationProperties` wird von `subject` auf den Knoten in der Graphtopologie verwiesen, von dem die Meldung generiert wurde. 
* In `applicationProperties` wird unter `eventType` darauf hingewiesen, dass es sich hierbei um ein Analyseereignis handelt.
* Der Wert `eventTime` ist die Uhrzeit, zu der das Ereignis eingetreten ist.
* Der Abschnitt `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `inferences`.
* Im Abschnitt `inferences` ist angegeben, dass `type` auf `entity` festgelegt ist. Dieser Abschnitt enthält zusätzliche Daten zur Entität.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Ausführen des Beispielprogramms für die Erkennung von Personen, Fahrzeugen oder Fahrrädern
Wenn Sie ein anderes Modell verwenden möchten, müssen sowohl die Graphtopologie als auch die Datei `operations.json` geändert werden.

Kopieren Sie die [Graphtopologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) in eine lokale Datei (beispielsweise `C:\TEMP\topology.json`). Öffnen Sie diese Kopie, und ändern Sie den Wert `inferencingUrl` in `http://openvino:4000/personVehicleBikeDetection`.

Navigieren Sie als Nächstes in Visual Studio Code zum Ordner *src/cloud-to-device-console-app*, und öffnen Sie die Datei `operations.json`. Bearbeiten Sie die Zeile mit `topologyUrl` wie folgt:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Die Rückschlussergebnisse ähneln hinsichtlich des Schemas den Ergebnissen des Fahrzeugerkennungsmodells. Lediglich `subtype` ist auf `personVehicleBikeDetection` festgelegt.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Ausführen des Beispielprogramms für die Fahrzeugklassifizierung
Öffnen Sie in Visual Studio Code die lokale Kopie von `topology.json` aus dem vorherigen Schritt, und ändern Sie den Wert von `inferencingUrl` in `http://openvino:4000/vehicleClassification`. Wenn Sie das vorherige Beispiel für die Erkennung von Personen, Fahrzeugen oder Fahrrädern ausgeführt haben, muss die Datei `operations.json` nicht erneut geändert werden.

Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Hier sehen Sie ein Beispiel für ein Klassifizierungsergebnis:

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Ausführen des Beispielprogramms für die Gesichtserkennung
Öffnen Sie in Visual Studio Code die lokale Kopie von `topology.json` aus dem vorherigen Schritt, und ändern Sie den Wert von `inferencingUrl` in `http://openvino:4000/faceDetection`. Wenn Sie das vorherige Beispiel für die Erkennung von Personen, Fahrzeugen oder Fahrrädern ausgeführt haben, muss die Datei `operations.json` nicht erneut geändert werden.

Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Im Anschluss sehen Sie ein Beispiel für ein Erkennungsergebnis. (Hinweis: Das oben verwendete Parkplatzvideo enthält keine erkennbaren Gesichter. Verwenden Sie daher ein anderes Video, um dieses Modell zu testen.)

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle eines virtuellen Azure-Linux-Computers. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md) befolgen. Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md) ausführen.