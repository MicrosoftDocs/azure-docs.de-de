---
title: 'Fortlaufende Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud: Azure'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Live Video Analytics in Azure IoT Edge für die fortlaufende Videoaufzeichnung in der Cloud verwenden und einen beliebigen Teil dieses Videos mit Azure Media Services streamen.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 8659bd2e029da13870b50dd6535e959bc90c81a7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551059"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Fortlaufende Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud

In diesem Tutorial erfahren Sie, wie Sie Azure Live Video Analytics in Azure IoT Edge für die [fortlaufende Videoaufzeichnung](continuous-video-recording-concept.md) (CVR) in der Cloud verwenden und einen beliebigen Teil dieses Videos mit Azure Media Services streamen. Diese Funktionalität ist unter anderem in Sicherheits- und Complianceszenarien hilfreich, in denen Kameraaufnahmen über mehrere Tage oder Wochen archiviert werden müssen. 

In diesem Lernprogramm führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Einrichten der relevanten Ressourcen
> * Untersuchen des Codes für die fortlaufende Videoaufzeichnung
> * Ausführen des Beispielcodes
> * Untersuchen der Ergebnisse und Betrachten des Videos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Übersicht zu Live Video Analytics in IoT Edge](overview.md)
* [Terminologie zu Live Video Analytics in IoT Edge](terminology.md)
* [Mediengraph](media-graph-concept.md) 
* [Fortlaufende Videoaufzeichnung](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Voraussetzungen

Dies sind die Voraussetzungen für dieses Tutorial:

* [Visual Studio Code](https://code.visualstudio.com/) mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)- und [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)-Erweiterungen auf dem Entwicklungscomputer.

    > [!TIP]
    > Sie werden unter Umständen aufgefordert, Docker zu installieren. Ignorieren Sie diese Aufforderung.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) auf Ihrem Entwicklungscomputer.
* Ausführung des [Setupskripts für Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

Nach dem Abschluss dieser Schritte sind in Ihrem Azure Abonnement relevante Azure-Ressourcen bereitgestellt:

* Azure IoT Hub
* Azure-Speicherkonto
* Azure Media Services-Konto
* Virtueller Linux-Computer in Azure mit installierter [IoT Edge-Runtime](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Falls für erstellte Azure-Ressourcen Probleme auftreten, helfen Ihnen die Informationen zum Beheben von häufigen Problemen im **[Leitfaden für die Problembehandlung](troubleshoot-how-to.md#common-error-resolutions)** weiter.

## <a name="concepts"></a>Konzepte

Wie im Artikel zum [Mediengraph-Konzept](media-graph-concept.md) erläutert, können Sie mithilfe eines Mediengraphs Folgendes definieren:

- Den Ort, an dem Medien erfasst werden sollen
- Wie sie verarbeitet werden sollen
- Wohin die Ergebnisse übermittelt werden sollen. 
 
 Für eine fortlaufende Videoaufzeichnung muss das Video mit einer RTSP-fähigen Kamera fortlaufend in einem [Azure Media Services-Medienobjekt](terminology.md#asset) aufgezeichnet werden. Dieses Diagramm ist eine grafische Darstellung dieses Mediengraphs.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Mediendiagramm":::

In diesem Tutorial wird ein mithilfe des [Live555-Medienservers](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) erstelltes Edge-Modul verwendet, um eine RTSP-Kamera zu simulieren. Innerhalb des Mediengraphs wird ein Knoten vom Typ [RTSP-Quelle](media-graph-concept.md#rtsp-source) verwendet, um den Livefeed abzurufen, und das Video wird an einen Knoten vom Typ [ Medienobjektsenke](media-graph-concept.md#asset-sink) gesendet, um es in einem Medienobjekt aufzuzeichnen. In diesem Tutorial wird ein [Beispielvideo zur Schnellstraßenkreuzung](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) verwendet.
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Vergewissern Sie sich zunächst, dass Sie den dritten Punkt der [Voraussetzungen](#prerequisites) ausgeführt haben. Wählen Sie nach dem Abschluss des Setupskripts für die Ressourcen die geschweiften Klammern aus, um die Ordnerstruktur anzuzeigen. Wie Sie sehen, wurden im Verzeichnis „~/clouddrive/lva-sample“ einige Dateien erstellt.

![App-Einstellungen](./media/quickstarts/clouddrive.png)

Für dieses Tutorial sind die folgenden Dateien relevant:

* **~/clouddrive/lva-sample/edge-deployment/.env**: Enthält Eigenschaften, die Visual Studio Code zum Bereitstellen von Modulen auf einem Edge-Gerät verwendet.
* **~/clouddrive/lva-sample/appsettings.json**: Von Visual Studio Code zum Ausführen des Beispielcodes verwendet.

Sie benötigen die Dateien, um diese Schritte auszuführen:

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

## <a name="examine-the-sample-files"></a>Untersuchen der Beispieldateien

Öffnen Sie in Visual Studio Code die Datei „src/edge/deployment.template.json“. Durch diese Vorlage wird definiert, welche Edge-Module auf dem Edge-Gerät (dem virtuellen Azure-Linux-Computer) bereitgestellt werden. Der Abschnitt **modules** enthält zwei Einträge mit folgenden Namen:

* **lvaEdge**: Dies ist das Modul für Live Video Analytics in IoT Edge.
* **rtspsim**: Dies ist der RTSP-Simulator.

Navigieren Sie als Nächstes zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

* **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
* **operations.json**: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die ausgeführt werden.
* **Program.cs**: Das Beispiel für den Programmcode, das Folgendes ausführt:
    * Laden der App-Einstellungen
    * Aufrufen direkter Methoden, die vom Modul „Live Video Analytics in IoT Edge“ verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.
    * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
    * Aufrufen direkter Methoden zur Bereinigung der Ressourcen

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests 

Im Bereitstellungsmanifest werden die Module, die auf einem Edge-Gerät bereitgestellt werden, sowie die Konfigurationseinstellungen für diese Module definiert. Führen Sie die folgenden Schritte aus, um ein Manifest auf der Grundlage der Vorlagendatei zu generieren, und stellen Sie es dann auf dem Edge-Gerät bereit.

1. Starten Sie Visual Studio Code.
1. Legen Sie die IoT Hub-Verbindungszeichenfolge fest, indem Sie im Bereich **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** auswählen. Kopieren Sie die Zeichenfolge aus der Datei „src/cloud-to-device-console-app/appsettings.json“. 

    ![Festlegen der IoT Hub-Verbindungszeichenfolge](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Klicken Sie mit der rechten Maustaste auf „src/edge/deployment.template.json“, und wählen Sie dann **IoT Edge-Bereitstellungsmanifest generieren** aus. Von Visual Studio Code werden die Werte aus der ENV-Datei verwendet, um die in der Bereitstellungsvorlagendatei gefundenen Variablen zu ersetzen. Diese Aktion erstellt eine Manifestdatei im Ordner „src/edge/config“ mit dem Namen **deployment.amd64.json**.

   ![Generieren des IoT Edge-Bereitstellungsmanifest](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Klicken Sie mit der rechten Maustaste auf die Datei „src/edge/config/deployment.amd64.json“, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

   ![Create Deployment for Single Device (Bereitstellung für einzelnes Gerät erstellen)](./media/quickstarts/create-deployment-single-device.png)
1. Sie werden dann aufgefordert, ein **IoT Hub-Gerät auszuwählen**. Wählen Sie in der Dropdownliste das Gerät „lva-sample-device“ aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich. Die folgenden Module sollten im Edge-Gerät als bereitgestellt angezeigt werden:
    * Live Video Analytics in IoT Edge (Modulname **lvaEdge**)
    * RTSP-Simulator (Modulname **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Vorbereiten der Überwachung der Module 

Wenn Sie das Modul „Live Video Analytics in IoT Edge“ verwenden, um den Livevideostream aufzuzeichnen, werden Ereignisse an die IoT Hub-Instanz gesendet. Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, und suchen Sie links unten nach **Azure IoT Hub**.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf die Datei „lva-sample-device“, und wählen Sie die Option **Überwachung des integrierten Ereignisendpunkts starten** aus.

    ![Überwachung des integrierten Ereignisendpunkts starten](./media/quickstarts/start-monitoring-iothub-events.png)

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
1. Navigieren Sie zu „src/cloud-to-device-console-app/operations.json“.
1. Bearbeiten Sie unter dem Knoten **GraphTopologySet** Folgendes:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Vergewissern Sie sich anschließend unter den Knoten **GraphInstanceSet** und **GraphTopologyDelete**, dass der Wert für **topologyName** dem Wert der Eigenschaft **name** in der vorherigen Graphtopologie entspricht:

    `"topologyName" : "CVRToAMSAsset"`  
1. Öffnen Sie die [Topologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) in einem Browser, und sehen Sie sich „assetNamePattern“ an. Es empfiehlt sich gegebenenfalls, in der Datei „operations.json“ den Namen der Graphinstanz (standardmäßig „Sample-Graph-1“) zu ändern, um sicherzustellen, dass Sie über ein Medienobjekt mit einem eindeutigen Namen verfügen.

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Starten Sie eine Debugsitzung, indem Sie F5 drücken. Daraufhin werden im **Terminalfenster** einige Nachrichten ausgegeben.
1. Die Datei „operations.json“ beginnt mit Aufrufen für „GraphTopologyList“ und „GraphInstanceList“. Falls Sie nach Abschluss vorheriger Schnellstartanleitungen oder Tutorials eine Ressourcenbereinigung durchgeführt haben, gibt diese Aktion leere Listen zurück, und die Ausführung wird angehalten, bis Sie die **EINGABETASTE** drücken, wie hier zu sehen:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
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
         "topologyName": "CVRToAMSAsset",
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
   * Aufruf von „GraphInstanceActivate“ zum Starten der Graphinstanz und des Videoflows
   * Ein zweiter Aufruf von „GraphInstanceList“, um zu zeigen, dass sich die Graphinstanz im Zustand „Wird ausgeführt“ befindet 
1. Die Ausgabe im **Terminalfenster** wird angehalten, und Sie werden zum **Drücken der EINGABETASTE** aufgefordert, um den Vorgang fortzusetzen. Drücken Sie noch nicht die **EINGABETASTE**. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wenn Sie nun zum **Ausgabefenster** in Visual Studio Code wechseln, werden die Nachrichten angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an IoT Hub gesendet werden.

   Diese Nachrichten werden im folgenden Abschnitt beschrieben.
1. Die Graphinstanz wird weiterhin ausgeführt und zeichnet das Video auf. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Kehren Sie zum Beenden der Aufzeichnung zum **Terminalfenster** zurück, und drücken Sie die **EINGABETASTE**. Die nächsten Aufrufe erfolgen, um die Ressourcen zu bereinigen und verwenden dazu:

   * einen Aufruf von „GraphInstanceDeactivate“ zum Deaktivieren der Graphinstanz
   * einen Aufruf von „GraphInstanceDelete“ zum Löschen der Instanz
   * einen Aufruf von „GraphTopologyDelete“ zum Löschen der Topologie
   * einen abschließenden Aufruf von „GraphTopologyList“, um zu zeigen, dass die Liste jetzt leer ist.

## <a name="interpret-the-results"></a>Interpretieren der Ergebnisse 

Wenn Sie den Mediengraph ausführen, werden vom Modul „Live Video Analytics in IoT Edge“ bestimmte Diagnose- und Betriebsereignisse an die IoT Edge Hub-Instanz gesendet. Diese Ereignisse sind die Meldungen, die im **Ausgabefenster** von Visual Studio Code angezeigt werden. Sie enthalten einen Abschnitt „body“ und einen Abschnitt „applicationProperties“. Informationen zu diesen Abschnitten finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden die Anwendungseigenschaften und der Inhalt von „body“ durch das Live Video Analytics-Modul definiert.

## <a name="diagnostics-events"></a>Diagnoseereignisse 

### <a name="mediasession-established-event"></a>Ereignis „MediaSessionEstablished“

Wenn die Graphinstanz aktiviert wird, wird vom RTSP-Quellknoten versucht, eine Verbindung mit dem RTSP-Server herzustellen, der im Modul „rtspsim“ ausgeführt wird. Bei erfolgreich hergestellter Verbindung wird dieses Ereignis ausgegeben:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Die Meldung ist ein Diagnoseereignis (MediaSessionEstablished). Hierdurch wird angegeben, dass vom RTSP-Quellknoten (Antragsteller) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten in der Graphtopologie, über den die Nachricht generiert wurde. In diesem Fall stammt die Nachricht vom RTSP-Quellknoten.
* Der Abschnitt „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
* Der Abschnitt „eventTime“ gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis, in diesem Fall die [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Details.

## <a name="operational-events"></a>Betriebsereignisse 

### <a name="recordingstarted-event"></a>RecordingStarted-Ereignis

Wenn der Knoten der Medienobjektsenke mit der Videoaufzeichnung beginnt, wird ein Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingStarted** ausgegeben:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat.

Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name des Azure Media Services-Medienobjekts, in dem das Video aufgezeichnet wird. Notieren Sie sich diesen Wert.

### <a name="recordingavailable-event"></a>RecordingAvailable-Ereignis

Wie der Name bereits vermuten lässt, wird das Ereignis „RecordingStarted“ gesendet, wenn die Aufzeichnung begonnen hat. Unter Umständen wurden aber noch keine Videodaten in das Medienobjekt hochgeladen. Wenn vom Knoten der Medienobjektsenke Videodaten in das Medienobjekt hochgeladen wurden, wird ein Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingAvailable** ausgegeben:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass genügend Daten in das Medienobjekt geschrieben wurden, um die Wiedergabe des Videos in Playern/Clients zu starten.

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat.

Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name des Azure Media Services-Medienobjekts, in dem das Video aufgezeichnet wird.

### <a name="recordingstopped-event"></a>Ereignis „RecordingStopped“

Wenn Sie die Graphinstanz deaktivieren, beendet der Knoten der Medienobjektsenke das Aufzeichnen von Videodaten im Medienobjekt. Er gibt dieses Ereignis vom Typ **Microsoft.Media.Graph.Operational.RecordingStopped** aus:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass die Aufzeichnung beendet wurde.

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten der Medienobjektsenke im Graph, der diese Nachricht generiert hat.

Der Textabschnitt enthält Informationen über den Ausgabeort, der in diesem Fall der Name des Azure Media Services-Medienobjekts ist, in dem das Videosignal aufgezeichnet wird.

## <a name="media-services-asset"></a>Media Services-Medienobjekt  

Sie können das durch den Mediengraph erstellte Media Services-Medienobjekt untersuchen, indem Sie sich beim Azure-Portal anmelden und sich das Video ansehen.

1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie Ihr Media Services-Konto unter den Ressourcen in Ihrem Abonnement, und öffnen Sie den Kontobereich.
1. Wählen Sie in der **Media Services**-Liste **Medienobjekte** aus.

    ![Media Services-Medienobjekte](./media/continuous-video-recording-tutorial/assets.png)
1. Sie finden ein Medienobjekt mit dem Namen „sampleAsset-CVRToAMSAsset-Sample-Graph-1“ aufgeführt. Dies ist das Benennungsmuster, das in ihrer Graphtopologiedatei gewählt wurde.
1. Wählen Sie das Medienobjekt aus.
1. Klicken Sie auf der Detailseite des Medienobjekts unter dem Textfeld **Streaming-URL** auf **Neu erstellen**.

    ![Neues Medienobjekt](./media/continuous-video-recording-tutorial/new-asset.png)

1. Übernehmen Sie im daraufhin angezeigten Assistenten die Standardoptionen, und wählen Sie **Hinzufügen** aus. Weitere Informationen finden Sie unter [Videowiedergabe](video-playback-concept.md).

    > [!TIP]
    > Vergewissern Sie sich, [dass Ihr Streamingendpunkt aktiv ist](../latest/streaming-endpoint-concept.md).
1. Der Player sollte das Video laden. Wählen Sie **Wiedergabe** aus, um es anzuzeigen.

> [!NOTE]
> Da es sich bei der Quelle des Videos um einen Container zum Simulieren eines Kamerafeeds handelt, beziehen sich die Zeitstempel im Video auf den Zeitpunkt, zu dem Sie die Graphinstanz aktiviert bzw. deaktiviert haben. Im Tutorial [Wiedergeben von mehrtägigen Aufzeichnungen](playback-multi-day-recordings-tutorial.md) erfahren Sie, wie Sie eine mehrtägige Aufzeichnung durchsuchen und sich Teile dieses Archivs ansehen. Darüber hinaus können Sie in diesem Tutorial die Zeitstempel im Video auf dem Bildschirm anzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die anderen Tutorials ausprobieren möchten, behalten Sie die erstellten Ressourcen. Wechseln Sie andernfalls zum Azure-Portal, navigieren Sie zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial durchgeführt haben, und löschen Sie die Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras mit RTSP-Unterstützung finden Sie auf der [Seite für ONVIF-konforme Produkte](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die den Profilen G, S oder T entsprechen.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät (anstelle eines virtuellen Azure-Linux-Computers). Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md). Befolgen Sie anschließend die Anweisungen in der Schnellstartanleitung [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md), um das Gerät für Azure IoT Hub zu registrieren.