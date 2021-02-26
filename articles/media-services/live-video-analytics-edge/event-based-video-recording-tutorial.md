---
title: 'Tutorial: Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud: Azure'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Live Video Analytics in Azure IoT Edge verwenden, um eine ereignisbasierte Videoaufzeichnung in der Cloud durchzuführen und sie aus der Cloud wiederzugeben.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: fda727a1abb488910d52b756455d2b6843e0e8b1
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550251"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud

In diesem Tutorial lernen Sie, wie Sie Azure Live Video Analytics in Azure IoT Edge verwenden, um selektiv Teile einer Live-Videoquelle in Azure Media Services in der Cloud aufzuzeichnen. Dieser Anwendungsfall wird in diesem Tutorial als [ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md) (Event Based Video Recording, EVR) bezeichnet. Um Teile eines Livevideos aufzuzeichnen, verwenden Sie ein KI-Modell zur Objekterkennung, um im Videosignal nach Objekten zu suchen und Videoclips nur aufzuzeichnen, wenn ein bestimmter Objekttyp erkannt wird. Außerdem erfahren Sie mehr über die Wiedergabe der aufgezeichneten Videoclips mithilfe von Media Services. Diese Funktionalität ist für eine Vielzahl von Szenarien nützlich, bei denen die Notwendigkeit besteht, ein Archiv interessierender Videoclips zu unterhalten. 

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten der relevanten Ressourcen
> * Untersuchen des Codes für die EVR
> * Ausführen des Beispielcodes
> * Untersuchen der Ergebnisse und Betrachten des Videos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Übersicht zu Live Video Analytics in IoT Edge](overview.md)
* [Terminologie zu Live Video Analytics in IoT Edge](terminology.md)
* [Mediengraph: Konzepte](media-graph-concept.md) 
* [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)
* [Tutorial: Entwickeln eines IoT Edge-Moduls](../../iot-edge/tutorial-develop-for-linux.md)
* [Vorgehensweise beim Bearbeiten von „deployment.*.template.json“](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Abschnitt zum [Deklarieren von Routen im IoT Edge-Bereitstellungsmanifest](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Voraussetzungen

Dies sind die Voraussetzungen für dieses Tutorial:

* [Visual Studio Code](https://code.visualstudio.com/) mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)- und [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)-Erweiterungen auf dem Entwicklungscomputer.

    > [!TIP]
    > Sie werden unter Umständen aufgefordert, Docker zu installieren. Ignorieren Sie diese Aufforderung.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) auf Ihrem Entwicklungscomputer.
* Abschluss des [Setupskripts für Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) und [Einrichten der Umgebung](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?pivots=programming-language-csharp#set-up-your-development-environment).

Nach dem Abschluss dieser Schritte sind in Ihrem Azure Abonnement relevante Azure-Ressourcen bereitgestellt:

* Azure IoT Hub
* Azure-Speicherkonto
* Azure Media Services-Konto
* Virtueller Linux-Computer in Azure mit installierter [IoT Edge-Runtime](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Falls für erstellte Azure-Ressourcen Probleme auftreten, helfen Ihnen die Informationen zum Beheben von häufigen Problemen im **[Leitfaden für die Problembehandlung](troubleshoot-how-to.md#common-error-resolutions)** weiter.

## <a name="concepts"></a>Konzepte

Ereignisbasierte Videoaufzeichnung bezeichnet den Vorgang des Aufzeichnen von Videos mit Auslösung durch ein Ereignis. Dieses Ereignis kann in folgender Weise generiert werden:
- Verarbeitung des Videosignals selbst, beispielsweise beim Erkennen eines bewegten Objekts im Video.
- Aus einer unabhängigen Quelle, z. B. dem Öffnen einer Tür. 

Alternativ können Sie die Aufzeichnung nur dann auslösen, wenn ein Rückschlüsse ziehender Dienst erkennt, dass ein bestimmtes Ereignis eingetreten ist. In diesem Tutorial verwenden Sie ein Video von Fahrzeugen, die sich auf einer Schnellstraße bewegen, und zeichnen immer dann einen Videoclip auf, wenn ein Lastwagen erkannt wird.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Mediendiagramm":::

Das Diagramm ist die bildliche Darstellung eines [Mediengraphs](media-graph-concept.md) und weiterer Module, mit denen das gewünschte Szenario realisiert wird. Vier IoT Edge-Module sind beteiligt:

* Live Video Analytics in einem IoT Edge-Modul.
* Ein Edge-Modul, das ein KI-Modell hinter einem HTTP-Endpunkt ausführt. Dieses KI-Modul verwendet das [YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-Modell, das viele Objekttypen erkennen kann.
* Ein benutzerdefiniertes Modul zum Zählen und Filtern von Objekten, das im Diagramm als Objektzähler bezeichnet wird. Sie erstellen einen Objektzähler und stellen ihn in diesem Tutorial bereit.
* Ein [RTSP-Simulatormodul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) zum Simulieren einer RTSP-Kamera.
    
Wie das Diagramm zeigt, verwenden Sie einen [RTSP-Quellknoten](media-graph-concept.md#rtsp-source) im Mediengraph zum Erfassen des simulierten Live-Videos von Verkehr auf einer Schnellstraße und senden dieses Video an zwei Pfade:

* Der erste Pfad führt zu einem HTTP-Erweiterungsknoten. Der Knoten erfasst die Videoframes in einem von Ihnen mithilfe des Felds `samplingOptions` festgelegten Wert und leitet die Frames dann als Bilder an das KI-Modul YOLOv3 weiter, das eine Objekterkennung darstellt. Der Knoten empfängt die Ergebnisse, nämlich die vom Modell erkannten Objekte (Fahrzeuge im Verkehrsfluss). Der HTTP-Erweiterungsknoten veröffentlicht anschließend die Ergebnisse über den Knoten der IoT Hub-Nachrichtensenke auf dem IoT Edge Hub.
* Das Objektzählermodul ist so eingerichtet, dass es Nachrichten vom IoT Edge Hub empfängt – welche die Ergebnisse der Objekterkennung (Fahrzeuge im Verkehrsfluss) beinhalten. Das Modul überprüft diese Nachrichten und sucht dabei nach Objekten eines bestimmten Typs, der mithilfe einer Einstellung konfiguriert wurde. Wenn ein solches Objekt gefunden wird, sendet dieses Modul eine Nachricht an den IoT Edge Hub. Diese „Objekt gefunden“-Meldungen werden dann an den IoT Hub-Quellknoten des Mediengraphen weitergeleitet. Beim Empfang einer solchen Nachricht löst der IoT Hub-Quellknoten im Mediengraph den [Signalgateprozessor](media-graph-concept.md#signal-gate-processor)-Knoten aus. Der Signalgate-Prozessorknoten wird dann für eine konfigurierte Zeitspanne geöffnet. Das Videosignal fließt für diese Zeitspanne durch das Gate zum Knoten der Medienobjektsenke. Dieser Anteil des Livestreams wird dann über den Knoten der [Medienobjektsenke](media-graph-concept.md#asset-sink) in einem [Medienobjekt](terminology.md#asset) in Ihrem Azure Media Services-Konto aufgezeichnet.

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Vergewissern Sie sich zunächst, dass Sie den dritten Punkt der [Voraussetzungen](#prerequisites) ausgeführt haben. Wählen Sie nach dem Abschluss des Setupskripts für die Ressourcen die geschweiften Klammern aus, um die Ordnerstruktur anzuzeigen. Wie Sie sehen, wurden im Verzeichnis „~/clouddrive/lva-sample“ einige Dateien erstellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="App-Einstellungen":::

Für dieses Tutorial sind die folgenden Dateien relevant:

* **~/clouddrive/lva-sample/edge-deployment/.env**: Enthält Eigenschaften, die Visual Studio Code zum Bereitstellen von Modulen auf einem Edge-Gerät verwendet.
* **~/clouddrive/lva-sample/appsetting.json**: Von Visual Studio Code zum Ausführen des Beispielcodes verwendet.

Sie benötigen die Dateien, um diese Schritte auszuführen.

1. Klonen Sie das Repository über den GitHub-Link https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Starten Sie Visual Studio Code, und öffnen Sie den Ordner, in den Sie das Repository heruntergeladen haben.
1. Navigieren Sie in Visual Studio Code zum Ordner „src/cloud-to-device-console-app“, und erstellen Sie eine Datei namens **appsettings.json**. Diese Datei enthält die Einstellungen, die zum Ausführen des Programms erforderlich sind.
1. Kopieren Sie den Inhalt der Datei „~/clouddrive/lva-sample/appsettings.json“. Der Text sollte wie folgt aussehen:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Die IoT Hub-Verbindungszeichenfolge ermöglicht die Verwendung von Visual Studio Code, um Befehle über Azure IoT Hub an die Edge-Module zu senden.
    
1. Navigieren Sie als Nächstes zum Ordner „src/edge“, und erstellen Sie eine Datei vom Typ **.env**.
1. Kopieren Sie den Inhalt der Datei „~/clouddrive/lva-sample/edge-deployment/.env“. Der Text sollte wie folgt aussehen:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Untersuchen der Vorlagendatei 

Im vorherigen Schritt haben Sie Visual Studio Code gestartet und den Ordner geöffnet, der den Beispielcode enthält.

Navigieren Sie in Visual Studio Code zu „src/edge“. Hier befinden sich die erstellte ENV-Datei sowie einige Bereitstellungsvorlagendateien. Durch diese Vorlage wird definiert, welche Edge-Module auf dem Edge-Gerät (dem virtuellen Azure-Linux-Computer) bereitgestellt werden. Die ENV-Datei enthält Werte für die Variablen, die in diesen Vorlagen verwendet werden, z. B. die Anmeldeinformationen für Media Services.

Öffnen Sie „src/edge/deployment.objectCounter.template.json“. Im Abschnitt **Module** gibt es vier Einträge, die den oben im Abschnitt „Konzepte“ aufgelisteten Elementen entsprechen:

* **lvaEdge**: Dies ist das Modul für Live Video Analytics in IoT Edge.
* **yolov3**: Dies ist das KI-Modul, das mithilfe des YOLO v3-Modells erstellt wurde.
* **rtspsim**: Dies ist der RTSP-Simulator.
* **objectCounter**: Dies ist das Modul, das in den Ergebnissen von yolov3 nach spezifischen Objekten sucht.

Informationen zum objectCounter-Modul finden Sie in der Zeichenfolge (${MODULES.objectCounter}), die für den Wert „image“ verwendet wird. Dies basiert auf dem [Tutorial](../../iot-edge/tutorial-develop-for-linux.md) zum Entwickeln eines IoT Edge-Moduls. Visual Studio Code erkennt automatisch, dass der Code für das objectCounter-Modul sich unter „src/edge/modules/objectCounter“ befindet. 

Lesen Sie [diesen Abschnitt](../../iot-edge/module-composition.md#declare-routes) zum Deklarieren von Routen im IoT Edge-Bereitstellungsmanifest. Überprüfen Sie dann die Routen in der JSON-Vorlagendatei. Beachten Sie Folgendes:

* LVAToObjectCounter wird verwendet, um bestimmte Ereignisse an einen bestimmten Endpunkt im objectCounter-Modul zu senden.
* ObjectCounterToLVA wird verwendet, um ein Auslöserereignis an einen bestimmten Endpunkt (bei dem es sich um den IoT Hub-Quellknoten handeln sollte ) im lvaEdge-Modul zu senden.
* objectCounterToIoTHub wird als Debugtool verwendet, damit Sie beim Ausführen dieses Tutorials die Ausgabe von objectCounter sehen können.

> [!NOTE]
> Überprüfen Sie die gewünschten Eigenschaften für das objectCounter-Modul, die dafür eingerichtet sind, nach Objekten zu suchen, die mit einem Konfidenzgrad von mindestens 50 % als „LKW“ gekennzeichnet sind.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests 

Im Bereitstellungsmanifest werden die Module, die auf einem Edge-Gerät bereitgestellt werden, sowie die Konfigurationseinstellungen für diese Module definiert. Führen Sie die folgenden Schritte aus, um ein Manifest auf der Grundlage der Vorlagendatei zu generieren, und stellen Sie es dann auf dem Edge-Gerät bereit.

Führen Sie in Visual Studio Code [diese Anweisungen](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) aus, um sich bei Docker anzumelden. Wählen Sie dann **Erstellen und Übertragen der IoT Edge-Projektmappe per Push** aus. Verwenden Sie für diesen Schritt „src/edge/deployment.objectCounter.template.json“.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Erstellen und Übertragen der IoT Edge-Projektmappe per Push":::

Durch diese Aktion wird das objectCounter-Modul zur Objektzählung erstellt und das Image per Push an Ihre Azure Container Registry (ACR) überstellt.

* Überprüfen Sie, ob die Umgebungsvariablen CONTAINER_REGISTRY_USERNAME_myacr und CONTAINER_REGISTRY_PASSWORD_myacr in der ENV-Datei definiert sind.

Mit diesem Schritt wird das IoT Edge-Bereitstellungsmanifest unter „src/edge/config/deployment.objectCounter.amd64.json“ erstellt. Klicken Sie mit der rechten Maustaste auf diese Datei, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Bereitstellung für einzelnes Gerät erstellen":::

Wenn dies Ihr erstes Tutorial mit Live Video Analytics in IoT Edge ist, werden Sie von Visual Studio Code aufgefordert, die IoT Hub-Verbindungszeichenfolge einzugeben. Sie können sie aus der Datei „appsettings.json“ kopieren.

> [!NOTE]
> Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Als Nächstes bittet Visual Studio Code Sie, ein IoT Hub-Gerät auszuwählen. Wählen Sie Ihr IoT Edge-Gerät aus; dies sollte „lva-sample-device“ sein.

In dieser Phase hat die Bereitstellung von Edge-Modulen auf Ihrem IoT Edge-Gerät begonnen.
Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich in Visual Studio Code. Sie sollten vier bereitgestellte Module mit den Namen „lvaEdge“, „rtspsim“, „yolov3“ und „objectCounter“ sehen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Vier bereitgestellte Module":::

## <a name="prepare-for-monitoring-events"></a>Vorbereiten der Überwachung von Ereignissen

Führen Sie die folgenden Schritte aus, um die Ereignisse vom Objektzählermodul und von Live Video Analytics im IoT Edge-Modul zu sehen:

1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, und suchen Sie links unten nach **Azure IoT Hub**.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf die Datei „lva-sample-device“, und wählen Sie die Option **Überwachung des integrierten Ereignisendpunkts starten** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Überwachung des integrierten Ereignisendpunkts starten":::

    > [!NOTE]
    > Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
## <a name="run-the-program"></a>Ausführen des Programms

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. <!--In Visual Studio Code, go-->Navigieren Sie zu „src/cloud-to-device-console-app/operations.json“.
1. Bearbeiten Sie unter dem Knoten **GraphTopologySet** Folgendes:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. Bearbeiten Sie anschließend unter den Knoten **GraphInstanceSet** und **GraphTopologyDelete**:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Starten Sie eine Debugsitzung, indem Sie F5 drücken. Daraufhin werden im **Terminalfenster** einige Nachrichten ausgegeben.
1. Die Datei „operations.json“ beginnt mit Aufrufen für „GraphTopologyList“ und „GraphInstanceList“. Falls Sie nach Abschluss vorheriger Schnellstartanleitungen oder Tutorials eine Ressourcenbereinigung durchgeführt haben, gibt diese Aktion leere Listen zurück, und die Ausführung wird angehalten, bis Sie die **EINGABETASTE** drücken, wie hier zu sehen:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
1. Nachdem Sie im **Terminalfenster** die **EINGABETASTE** gedrückt haben, werden die nächsten Aufrufe direkter Methoden durchgeführt:
   * Ein Aufruf von „GraphTopologySet“ unter Verwendung des vorherigen Werts für „topologyUrl“
   * Ein Aufruf von „GraphInstanceSet“ unter Verwendung des folgenden Textkörpers:
     
        ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
            "description": "Sample graph description",
            "parameters": [
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
    
   * Ein Aufruf von „GraphInstanceActivate“ zum Starten der Graphinstanz und des Videoflows
   * Ein zweiter Aufruf von „GraphInstanceList“, um zu zeigen, dass sich die Graphinstanz im Zustand „Wird ausgeführt“ befindet
     
1. Die Ausgabe im **Terminalfenster** wird angehalten, und Sie werden zum **Drücken der EINGABETASTE** aufgefordert, um den Vorgang fortzusetzen. Drücken Sie noch nicht die **EINGABETASTE**. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wenn Sie nun zum **Ausgabefenster** in Visual Studio Code wechseln, werden die Nachrichten angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an IoT Hub gesendet werden.

   Diese Nachrichten werden im folgenden Abschnitt beschrieben.
1. Die Graphinstanz wird weiterhin ausgeführt und zeichnet das Video auf. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Überprüfen Sie die Nachrichten, wie im folgenden Abschnitt erläutert. Um die Instanz zu beenden, wechseln Sie anschließend zurück zum **Terminalfenster**, und drücken Sie die **EINGABETASTE**. Die nächsten Aufrufe erfolgen, um die Ressourcen zu bereinigen und verwenden dazu:

   * einen Aufruf von „GraphInstanceDeactivate“ zum Deaktivieren der Graphinstanz
   * einen Aufruf von „GraphInstanceDelete“ zum Löschen der Instanz
   * einen Aufruf von „GraphTopologyDelete“ zum Löschen der Topologie
   * einen abschließenden Aufruf von „GraphTopologyList“, um zu zeigen, dass die Liste jetzt leer ist.

## <a name="interpret-the-results"></a>Interpretieren der Ergebnisse 

Wenn Sie den Mediengraph ausführen, werden vom Modul „Live Video Analytics in IoT Edge“ bestimmte Diagnose- und Betriebsereignisse an die IoT Edge Hub-Instanz gesendet. Diese Ereignisse sind die Meldungen, die im **Ausgabefenster** von Visual Studio Code angezeigt werden. Sie enthalten einen Abschnitt „body“ und einen Abschnitt „applicationProperties“. Informationen zu diesen Abschnitten finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden die Anwendungseigenschaften und der Inhalt von „body“ durch das Live Video Analytics-Modul definiert.

## <a name="diagnostics-events"></a>Diagnoseereignisse

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis 

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im RTSP-Simulatorcontainer ausgeführt wird. Bei erfolgreich hergestellter Verbindung wird dieses Ereignis ausgegeben. Der Ereignistyp lautet **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Die Meldung ist ein Diagnoseereignis (MediaSessionEstablished). Hierdurch wird angegeben, dass vom RTSP-Quellknoten (Antragsteller) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten in der Graphtopologie, über den die Nachricht generiert wurde. In diesem Fall stammt die Nachricht vom RTSP-Quellknoten.
* Der Abschnitt „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
* Der Abschnitt „eventTime“ gibt den Zeitpunkt des Ereignisses an. Dies ist der Zeitpunkt, zu dem das Verkehrsvideo (MKV-Datei) als Livestream beim Modul einzutreffen begann.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis, in diesem Fall die [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Details.


## <a name="operational-events"></a>Betriebsereignisse

Nachdem der Mediengraph eine Zeit lang ausgeführt wird, trifft früher oder später ein Ereignis vom objectCounter-Modul ein. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

Der Abschnitt „applicationProperties“ enthält die Ereigniszeit. Dies ist der Zeitpunkt, zu dem das objectCounter-Modul beobachtet hat, dass die Ergebnisse des yolov3-Moduls interessierende Objekte (LKW) enthalten.

Sie werden möglicherweise das Auftreten weiterer dieser Ereignisse feststellen, wenn weitere LKW im Video erkannt werden.

### <a name="recordingstarted-event"></a>RecordingStarted-Ereignis

Fast unmittelbar nach dem Senden des Ereignisses durch den Objektzähler wird ein Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingStarted** angezeigt:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name des Azure Media Services-Medienobjekts, in dem das Video aufgezeichnet wird. Notieren Sie sich diesen Wert.

### <a name="recordingavailable-event"></a>RecordingAvailable-Ereignis

Wenn vom Knoten der Medienobjektsenke ein Video in das Medienobjekt hochgeladen wurde, wird ein Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingAvailable** ausgegeben:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass genügend Daten in das Medienobjekt geschrieben wurden, um die Wiedergabe des Videos in Playern/Clients zu starten. Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name des Azure Media Services-Medienobjekts, in dem das Video aufgezeichnet wird.

### <a name="recordingstopped-event"></a>Ereignis „RecordingStopped“

Wenn Sie die Aktivierungseinstellungen (maximumActivationTime) für den Knoten des Signalgateprozessors in der [Topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json) überprüfen, sehen Sie, dass das Gate so eingerichtet ist, dass es nach 30 Sekunden nach dem Senden des Videos geschlossen wird. Ungefähr 30 Sekunden nach dem RecordingStarted-Ereignis sollte ein Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingStopped** angezeigt werden. Dieses Ereignis gibt an, dass der Knoten der Medienobjektsenke das Aufzeichnen von Videodaten im Medienobjekt beendet hat.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass die Aufzeichnung beendet wurde. Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name des Azure Media Services-Medienobjekts, in dem das Video aufgezeichnet wird.

## <a name="media-services-asset"></a>Media Services-Medienobjekt  

Sie können das Media Services-Medienobjekt, das vom Graph erstellt wurde, untersuchen, indem Sie sich beim Azure-Portal anmelden und das Video anzeigen.

1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie Ihr Media Services-Konto unter den Ressourcen in Ihrem Abonnement. Öffnen Sie den Kontobereich.
1. Wählen Sie in der **Media Services**-Liste **Medienobjekte** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Fortlaufende Videoaufzeichnung":::
1. Sie finden ein Medienobjekt mit dem Namen „sampleAssetFromEVR-LVAEdge-{DateTime}“ aufgeführt. Dies ist der Name, der in der Eigenschaft „outputLocation“ des RecordingStarted-Ereignisses angegeben ist. Das assetNamePattern in der Topologie bestimmt, wie dieser Name generiert wurde.
1. Wählen Sie das Medienobjekt aus.
1. Klicken Sie auf der Detailseite des Medienobjekts unter dem Textfeld **Streaming-URL** auf **Neu erstellen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Neues Medienobjekt":::
1. Übernehmen Sie im daraufhin angezeigten Assistenten die Standardoptionen, und wählen Sie **Hinzufügen** aus. Weitere Informationen finden Sie unter [Videowiedergabe](video-playback-concept.md).

    > [!TIP]
    > Vergewissern Sie sich, [dass Ihr Streamingendpunkt aktiv ist](../latest/streaming-endpoint-concept.md).
1. Der Player sollte das Video laden. Wählen Sie **Wiedergabe** aus, um es anzuzeigen.

> [!NOTE]
> Da es sich bei der Quelle des Videos um einen Container zum Simulieren eines Kamerafeeds handelt, beziehen sich die Zeitstempel im Video auf den Zeitpunkt, zu dem Sie die Graphinstanz aktiviert bzw. deaktiviert haben. Wenn Sie die Wiedergabesteuerelemente verwenden, die in das Tutorial [Wiedergeben von mehrtägigen Aufzeichnungen](playback-multi-day-recordings-tutorial.md) integriert sind, können Sie die Zeitstempel im Video auf dem Bildschirm anzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die anderen Tutorials ausprobieren möchten, behalten Sie die erstellten Ressourcen. Wechseln Sie andernfalls zum Azure-Portal, navigieren Sie zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial durchgeführt haben, und löschen Sie die Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras mit RTSP-Unterstützung finden Sie auf der [Seite für ONVIF-konforme Produkte](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die den Profilen G, S oder T entsprechen.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät (anstelle eines virtuellen Azure-Linux-Computers). Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md). Befolgen Sie anschließend die Anweisungen in der Schnellstartanleitung [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md), um das Gerät für Azure IoT Hub zu registrieren.