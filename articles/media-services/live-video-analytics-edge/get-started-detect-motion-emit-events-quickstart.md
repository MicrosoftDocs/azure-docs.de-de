---
title: 'Erste Schritte mit Live Video Analytics in IoT Edge: Azure'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie in die Nutzung von Live Video Analytics in IoT Edge einsteigen. Es wird beschrieben, wie Sie in einem Livevideostream Bewegung erkennen.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d9f2637166c3807e36f4304e9394bf2ecf91d88c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573805"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Schnellstart: Erste Schritte: Live Video Analytics in IoT Edge

In dieser Schnellstartanleitung werden die ersten Schritte mit Live Video Analytics in IoT Edge erläutert. Hierfür wird eine Azure-VM als IoT Edge-Gerät verwendet. Außerdem wird ein simulierter Livevideostream verwendet. 

Nach dem Ausführen der Setupschritte können Sie einen simulierten Livevideostream über einen Mediengraphen ausführen, der Bewegungen im Stream erkennt und meldet. Dieser Mediengraph ist im folgenden Diagramm grafisch dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Livevideoanalysen, die auf Bewegungserkennung basieren":::

Sie können sich das folgende Video mit ausführlichen Schritten für den Einstieg in Live Video Analytics in IoT Edge ansehen:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

  > [!NOTE]
  > Sie benötigen ein Azure-Abonnement mit Berechtigungen zum Erstellen von Dienstprinzipalen. (Die **Rolle „Besitzer“** stellt diese Berechtigungen bereit.) Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt.  

* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Vergewissern Sie sich, dass für das Netzwerk, mit dem Ihr Entwicklungscomputer verbunden ist, AMQP (Advanced Message Queueing Protocol) über Port 5671 für ausgehenden Datenverkehr zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.

> [!TIP]
> Bei der Installation der Azure IoT Tools-Erweiterung werden Sie unter Umständen aufgefordert, Docker zu installieren. Sie können diese Aufforderung ignorieren.

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

Für dieses Tutorial werden die folgenden Azure-Ressourcen benötigt:

* IoT Hub
* Speicherkonto
* Azure Media Services-Konto
* Ein virtueller Linux-Computer in Azure mit installierter [IoT Edge-Runtime](../../iot-edge/how-to-install-iot-edge.md)

Für diese Schnellstartanleitung wird die Verwendung des [Setupskripts für Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) zum Bereitstellen der erforderlichen Ressourcen in Ihrem Azure-Abonnement empfohlen. Gehen Sie dazu folgendermaßen vor:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und wählen Sie das Cloud Shell-Symbol aus.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Wenn Sie Cloud Shell zum ersten Mal verwenden, werden Sie aufgefordert, ein Abonnement auszuwählen, um ein Speicherkonto und eine Microsoft Azure Files-Freigabe zu erstellen. Wählen Sie **Speicher erstellen** aus, um ein Speicherkonto für die Cloud Shell-Sitzungsinformationen zu erstellen. Dieses Speicherkonto ist von dem Konto getrennt, das vom Skript für die Verwendung mit Ihrem Azure Media Services-Konto erstellt wird.
1. Wählen Sie auf der linken Seite des Cloud Shell-Fensters im Dropdownmenü **Bash** als Option für die Umgebung aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Umgebungsauswahl":::
1. Führen Sie den folgenden Befehl aus.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Nach der erfolgreichen Ausführung des Skripts sollten alle erforderlichen Ressourcen Ihres Abonnements angezeigt werden. Vom Skript werden insgesamt zwölf Ressourcen eingerichtet:
    1. **Streamingendpunkt**: Dient als Hilfe beim Wiedergeben des aufgezeichneten AMS-Objekts.
    1. **Virtueller Computer**: Ein virtueller Computer, der als Ihr Edgegerät fungiert.
    1. **Datenträger**: Ein Speicherdatenträger, der für die Speicherung von Medien und Artefakten an den virtuellen Computer angefügt ist.
    1. **Netzwerksicherheitsgruppe**: Wird verwendet, um Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem virtuellen Azure-Netzwerk zu filtern.
    1. **Netzwerkschnittstelle**: Ermöglicht einem virtuellen Azure-Computer die Kommunikation mit dem Internet, Azure und anderen Ressourcen.
    1. **Bastion-Verbindung**: Ermöglicht Ihnen das Herstellen einer Verbindung mit Ihrem virtuellen Computer über Ihren Browser und das Azure-Portal.
    1. **Öffentliche IP-Adresse**: Ermöglicht es Azure-Ressourcen, mit dem Internet und öffentlichen Azure-Diensten zu kommunizieren.
    1. **Virtuelles Netzwerk**: Ermöglicht es vielen Arten von Azure-Ressourcen (z. B. Ihrem virtuellen Computer), sicher untereinander sowie mit dem Internet und mit lokalen Netzwerken zu kommunizieren. Informieren Sie sich eingehender über [virtuelle Netzwerke](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
    1. **IoT Hub**: Dient als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung, IoT Edge-Modulen und den verwalteten Geräten.
    1. **Media Services-Konto**: Dient als Hilfe beim Verwalten und Streamen von Medieninhalten in Azure.
    1. **Speicherkonto**: Sie müssen über ein primäres Speicherkonto verfügen. Darüber hinaus können Sie beliebig viele sekundäre Speicherkonten an Ihr Media Services-Konto anfügen. Weitere Informationen finden Sie unter [Azure Storage-Konten mit Azure Media Services-Konten](https://docs.microsoft.com/azure/media-services/latest/storage-account-concept).
    1. **Containerregistrierung**: Dient zum Speichern und Verwalten Ihrer privaten Docker-Containerimages und der zugehörigen Artefakte.

In der Ausgabe des Skripts ist in einer Tabelle mit den Ressourcen der Name des IoT-Hubs angegeben. Suchen Sie nach dem Ressourcentyp **`Microsoft.Devices/IotHubs`** , und notieren Sie sich den Namen. Sie benötigen diesen Namen im nächsten Schritt.  

> [!NOTE]
> Darüber hinaus generiert das Skript auch einige Konfigurationsdateien im Verzeichnis ***~/clouddrive/lva-sample/*** . Sie benötigen diese Dateien später in dieser Schnellstartanleitung noch.

> [!TIP]
> Falls für erstellte Azure-Ressourcen Probleme auftreten, helfen Ihnen die Informationen zum Beheben von häufigen Problemen im **[Leitfaden für die Problembehandlung](troubleshoot-how-to.md#common-error-resolutions)** weiter.

## <a name="deploy-modules-on-your-edge-device"></a>Bereitstellen von Modulen auf dem Edgegerät

Führen Sie den folgenden Befehl über Cloud Shell aus.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Mit diesem Befehl werden die folgenden Module für das Edgegerät bereitgestellt (in diesem Fall die Linux-VM).

* Live Video Analytics in IoT Edge (Modulname: `lvaEdge`)
* RTSP-Simulator (Real-Time Streaming Protocol) (Modulname: `rtspsim`)

Das RTSP-Simulatormodul simuliert einen Livevideostream. Hierfür wird eine Videodatei verwendet, die beim Ausführen des [Setupskripts für Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) auf Ihr Edgegerät kopiert wurde. 

Nun werden die Module bereitgestellt, aber es sind keine Mediengraphen aktiv.

## <a name="configure-the-azure-iot-tools-extension"></a>Konfigurieren der Azure IoT Tools-Erweiterung

Befolgen Sie die unten angegebene Anleitung, um mit der Azure IoT Tools-Erweiterung eine Verbindung mit Ihrem IoT-Hub herzustellen.

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Wählen Sie **Ansicht** > **Explorer** aus. Oder drücken Sie STRG+UMSCHALT+E.
1. Wählen Sie unten links auf der Registerkarte **Explorer** die Option **Azure IoT Hub** aus.
1. Wählen Sie das Symbol **Weitere Optionen** aus, um das Kontextmenü anzuzeigen. Wählen Sie anschließend **IoT Hub-Verbindungszeichenfolge festlegen** aus.
1. Geben Sie Ihre IoT Hub-Verbindungszeichenfolge ein, wenn das entsprechende Eingabefeld angezeigt wird. In Cloud Shell finden Sie die Verbindungszeichenfolge in *~/clouddrive/lva-sample/appsettings.json*.

> [!NOTE]
> Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Wenn die Verbindung erfolgreich hergestellt wird, wird die Liste mit den Edgegeräten angezeigt. Es sollte mindestens ein Gerät mit dem Namen **lva-sample-device** angezeigt werden. Sie können nun über das Kontextmenü Ihre IoT Edge-Geräte verwalten und mit Azure IoT Hub interagieren. Erweitern Sie unter **lva-sample-device** den Knoten **Module**, um die auf dem Edgegerät bereitgestellten Module anzuzeigen.

![Knoten „lva-sample-device“](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Wenn Sie [Live Video Analytics in IoT Edge manuell auf einem IoT Edge-Gerät (z. B. einem ARM64-Gerät) bereitgestellt haben](deploy-iot-edge-device.md), wird das Modul unter Azure IoT Hub unter diesem Gerät angezeigt. Sie können dieses Modul auswählen. Führen Sie dann die folgenden restlichen Schritte aus.

## <a name="use-direct-method-calls"></a>Verwenden von Aufrufen direkter Methoden

Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie direkte Methoden aufrufen. Weitere Informationen finden Sie unter [Direkte Methoden für Live Video Analytics in IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Aufrufen von „GraphTopologyList“

Gehen Sie wie folgt vor, um alle [Graphtopologien](media-graph-concept.md#media-graph-topologies-and-instances) des Moduls aufzulisten:

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf das Modul **lvaEdge**, und wählen Sie **Invoke Module Direct Method** (Direkte Methode des Moduls aufrufen) aus.
1. Geben Sie im angezeigten Feld die Zeichenfolge *GraphTopologyList* ein.
1. Kopieren Sie die folgende JSON-Nutzlast, und fügen Sie sie in das Feld ein. Drücken Sie anschließend die EINGABETASTE.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Dies ist die erwartete Antwort, da keine Graphtopologien erstellt wurden.
    

### <a name="invoke-graphtopologyset"></a>Aufrufen von „GraphTopologySet“

Wie bereits zuvor können Sie nun `GraphTopologySet` aufrufen, um eine [Graphtopologie](media-graph-concept.md#media-graph-topologies-and-instances) festzulegen. Verwenden Sie den folgenden JSON-Code als Nutzlast.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Mit dieser JSON-Nutzlast wird eine Graphtopologie erstellt, von der drei Parameter definiert werden. Zwei dieser Parameter weisen Standardwerte auf. Die Topologie enthält einen Quellknoten (RTSP-Quelle), einen Prozessorknoten (Bewegungserkennungsprozessor) und einen Senkenknoten (IoT Hub-Senke).

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Als Statuscode wird „201“ zurückgegeben. Dieser Status gibt an, dass eine neue Topologie erstellt wurde. 

Versuchen Sie nun, diese nächsten Schritte auszuführen:

1. Rufen Sie `GraphTopologySet` erneut auf. Als Statuscode wird „200“ zurückgegeben. Dieser Code gibt an, dass eine vorhandene Topologie erfolgreich aktualisiert wurde.
1. Rufen Sie `GraphTopologySet` erneut auf, aber ändern Sie nun die Beschreibungszeichenfolge. Als Statuscode wird „200“ zurückgegeben, und die Beschreibung wird auf den neuen Stand aktualisiert.
1. Rufen Sie `GraphTopologyList` wie im vorherigen Abschnitt beschrieben auf. Die Topologie `MotionDetection` wird jetzt in der zurückgegebenen Nutzlast angezeigt.

### <a name="invoke-graphtopologyget"></a>Aufrufen von „GraphTopologyGet“

Rufen Sie `GraphTopologyGet` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Beachten Sie in der Antwortnutzlast die folgenden Details:

* Der Statuscode lautet „200“ (erfolgreiche Ausführung).
* Die Nutzlast enthält die beiden Zeitstempel `created` und `lastModified`.

### <a name="invoke-graphinstanceset"></a>Aufrufen von „GraphInstanceSet“

Erstellen Sie eine Graphinstanz, die auf die vorherige Graphtopologie verweist. Mit Graphinstanzen können Sie Livevideostreams von vielen Kameras analysieren, indem Sie die gleiche Graphtopologie verwenden. Weitere Informationen finden Sie unter [Topologien und Instanzen von Mediengraphen](media-graph-concept.md#media-graph-topologies-and-instances).

Rufen Sie die direkte Methode `GraphInstanceSet` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Beachten Sie, dass für diese Nutzlast Folgendes gilt:

* Gibt den Topologienamen (`MotionDetection`) an, für den die Instanz erstellt werden muss.
* Enthält einen Parameterwert für `rtspUrl`, für den in der Nutzlast der Graphtopologie kein Standardwert vorhanden war. Dieser Wert ist ein Link zum folgenden Beispielvideo:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Beachten Sie in der Antwortnutzlast Folgendes:

* Der Statuscode lautet „201“. Hierdurch wird angegeben, dass eine neue Instanz erstellt wurde.
* Der Status lautet `Inactive`. Hierdurch wird angegeben, dass die Graphinstanz erstellt, aber nicht aktiviert wurde. Weitere Informationen finden Sie unter [Zustände von Mediengraphen](media-graph-concept.md).

Versuchen Sie nun, diese nächsten Schritte auszuführen:

1. Rufen Sie `GraphInstanceSet` erneut auf, indem Sie dieselbe Nutzlast verwenden. Beachten Sie, dass „200“ als Statuscode zurückgegeben wird.
1. Rufen Sie `GraphInstanceSet` erneut auf, aber verwenden Sie nun eine andere Beschreibung. Achten Sie auf die aktualisierte Beschreibung in der Antwortnutzlast, mit der angegeben wird, dass die Graphinstanz erfolgreich aktualisiert wurde.
1. Rufen Sie `GraphInstanceSet` auf, aber ändern Sie den Namen in `Sample-Graph-2`. Beachten Sie in der Antwortnutzlast die neu erstellte Graphinstanz (Statuscode „201“).

### <a name="invoke-graphinstanceactivate"></a>Aufrufen von „GraphInstanceActivate“

Aktivieren Sie nun die Graphinstanz, um den Fluss von Livevideos über das Modul zu starten. Rufen Sie die direkte Methode `GraphInstanceActivate` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Graphinstanz erfolgreich aktiviert wurde.

### <a name="invoke-graphinstanceget"></a>Aufrufen von „GraphInstanceGet“

Rufen Sie nun die direkte Methode `GraphInstanceGet` auf, indem Sie die folgende Nutzlast verwenden.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Beachten Sie in der Antwortnutzlast die folgenden Details:

* Der Statuscode lautet „200“ (erfolgreiche Ausführung).
* Der Status lautet `Active`. Hierdurch wird angegeben, dass die Graphinstanz nun aktiv ist.

## <a name="observe-results"></a>Überprüfen der Ergebnisse

In der zuvor erstellten und aktivierten Graphinstanz wird der Knoten des Bewegungserkennungsprozessors verwendet, um Bewegungen im eingehenden Livevideostream zu erkennen. Anschließend werden Ereignisse an den Knoten der IoT Hub-Senke gesendet. Diese Ereignisse werden an den IoT Edge-Hub weitergeleitet. 

Führen Sie die folgenden Schritte aus, um die Ergebnisse anzuzeigen.

1. Öffnen Sie in Visual Studio Code den Bereich **Explorer**. Suchen Sie unten links nach der Option **Azure IoT Hub**.
2. Erweitern Sie den Knoten **Geräte**.
3. Klicken Sie mit der rechten Maustaste auf **lva-sample-device**, und wählen Sie anschließend **Überwachung des integrierten Ereignisendpunkts starten** aus.

    ![Starten der Überwachung von IoT Hub-Ereignissen](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
Im **Ausgabefenster** wird die folgende Meldung angezeigt:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

Beachten Sie die folgenden Details:

* Die Meldung enthält die beiden Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).
* In `applicationProperties` wird von `subject` auf den Knoten des `MediaGraph`-Elements verwiesen, von dem die Meldung generiert wurde. In diesem Fall stammt die Meldung vom Bewegungserkennungsprozessor.
* In `applicationProperties` wird unter `eventType` darauf hingewiesen, dass es sich hierbei um ein Analyseereignis handelt.
* Der Wert `eventTime` ist die Uhrzeit, zu der das Ereignis eingetreten ist.
* Der Abschnitt `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `timestamp` und `inferences`.
* Im Abschnitt `inferences` ist angegeben, dass `type` auf `motion` festgelegt ist. Hier werden zusätzliche Daten zum Ereignis `motion` angegeben.

Nachdem der Mediengraph einige Zeit ausgeführt wurde, wird im **Ausgabefenster** die folgende Meldung angezeigt.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Beachten Sie in dieser Meldung die folgenden Details:

* In `applicationProperties` wird mit `subject` angegeben, dass die Meldung über den Knoten der RTSP-Quelle im Mediengraphen generiert wurde.
* In `applicationProperties` wird mit `eventType` angegeben, dass es sich um ein Diagnoseereignis handelt.
* `body` enthält Daten zum Diagnoseereignis. In diesem Fall enthält die Meldung den Text, weil für das Ereignis `MediaSessionEstablished` festgelegt ist.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Aufrufen zusätzlicher direkter Methoden zur Bereinigung

Rufen Sie direkte Methoden auf, um die Graphinstanz zuerst zu deaktivieren und dann zu löschen.

### <a name="invoke-graphinstancedeactivate"></a>Aufrufen von „GraphInstanceDeactivate“

Rufen Sie die direkte Methode `GraphInstanceDeactivate` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Graphinstanz erfolgreich deaktiviert wurde.

Versuchen Sie anschließend, `GraphInstanceGet` aufzurufen (wie oben in diesem Artikel beschrieben). Sehen Sie sich den Wert von `state` an.

### <a name="invoke-graphinstancedelete"></a>Aufrufen von „GraphInstanceDelete“

Rufen Sie die direkte Methode `GraphInstanceDelete` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Graphinstanz erfolgreich gelöscht wurde.

### <a name="invoke-graphtopologydelete"></a>Aufrufen von „GraphTopologyDelete“

Rufen Sie die direkte Methode `GraphTopologyDelete` auf, indem Sie die folgende Nutzlast verwenden.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Graphtopologie erfolgreich gelöscht wurde.

Versuchen Sie nun, diese nächsten Schritte auszuführen:

1. Rufen Sie `GraphTopologyList` auf. Sie sehen, dass das Modul keine Graphtopologien enthält.
1. Rufen Sie `GraphInstanceList` auf, indem Sie dieselbe Nutzlast wie für `GraphTopologyList` verwenden. Sie sehen, dass keine Graphinstanzen aufgelistet werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie die in dieser Schnellstartanleitung erstellten Ressourcen löschen.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Aufzeichnen von Videos mit Live Video Analytics in IoT Edge](continuous-video-recording-tutorial.md).
* Informieren Sie sich ausführlicher über [Diagnosemeldungen](monitoring-logging.md).