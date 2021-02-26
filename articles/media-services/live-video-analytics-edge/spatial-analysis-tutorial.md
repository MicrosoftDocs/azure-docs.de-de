---
title: Analysieren von Livevideos mit maschinellem Sehen für die räumliche Analyse – Azure
description: In diesem Tutorial wird gezeigt, wie Sie Live Video Analytics zusammen mit dem KI-Feature Maschinelles Sehen für die räumliche Analyse aus Azure Cognitive Services verwenden, um einen Livevideofeed von einer (simulierten) IP-Kamera zu analysieren.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: f8a828d27b69fedefe7ccbc2ad7290bf300afc16
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526788"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analysieren von Livevideos mit maschinellem Sehen für die räumliche Analyse (Vorschau)

In diesem Tutorial wird gezeigt, wie Sie Live Video Analytics zusammen mit dem [KI-Dienst Maschinelles Sehen für die räumliche Analyse aus Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) verwenden, um einen Livevideofeed von einer (simulierten) IP-Kamera zu analysieren. Es wird gezeigt, wie Sie mit diesem Rückschlussserver das Streamingvideo analysieren können, um räumliche Beziehungen zwischen Personen und Bewegung im physischen Raum zu ermitteln.  Eine Teilmenge der Frames im Videofeed wird an den Rückschlussserver gesendet, und die Ergebnisse werden an IoT Edge Hub gesendet. Wenn bestimmte Bedingungen erfüllt sind, werden Videoclips aufgezeichnet und als Azure Media Services-Medienobjekte gespeichert.

In diesem Lernprogramm führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Einrichten von Ressourcen
> * Untersuchen Sie den Code.
> * Ausführen des Beispielcodes
> * Überwachen von Ereignissen
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Sie benötigen ein Azure-Abonnement mit Berechtigungen zum Erstellen von Dienstprinzipalen. (Die **Rolle „Besitzer“** stellt diese Berechtigungen bereit.) Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt. 
## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Übersicht zu Live Video Analytics in IoT Edge](overview.md)
* [Terminologie zu Live Video Analytics in IoT Edge](terminology.md)
* [Mediengraph: Konzepte](media-graph-concept.md)
* [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)
* [Tutorial: Entwickeln eines IoT Edge-Moduls](../../iot-edge/tutorial-develop-for-linux.md)
* [Bereitstellen von Live Video Analytics in Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein, um das Modul „spatial-analysis“ mit dem Live Video Analytics-Modul zu verbinden.

* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
    * Vergewissern Sie sich, dass für das Netzwerk, mit dem der Entwicklungscomputer verbunden ist, Advanced Message Queueing Protocol über Port 5671 zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) mit GPU-Beschleunigung.  
    Es wird empfohlen, Azure Stack Edge mit GPU-Beschleunigung zu verwenden. Der Container wird jedoch auch auf jedem anderen Gerät mit einer [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) ausgeführt. 
* [Azure Cognitive Service-Container Maschinelles Sehen](https://azure.microsoft.com/services/cognitive-services/computer-vision/) für räumliche Analysen.  
    Um diesen Container verwenden zu können, benötigen Sie eine Ressource für maschinelles Sehen, um den zugeordneten **API-Schlüssel** und einen **Endpunkt-URI** abzurufen. Der API-Schlüssel ist im Azure-Portal auf der Übersichtsseite und Schlüsselseite für Maschinelles Sehen verfügbar. Der Schlüssel und der Endpunkt sind erforderlich, um den Container zu starten.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Übersicht über die räumliche Analyse":::
 
In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](media-graph-concept.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den `MediaGraphCognitiveServicesVisionExtension`-Prozessorknoten.

Der Knoten „MediaGraphCognitiveServicesVisionExtension“ fungiert als Proxy. Er wandelt die Video-Einzelbilder in den angegebenen Bildtyp um. Anschließend leitet er das Bild über **gemeinsam genutzten Speicher** an ein anderes Edge-Modul weiter, das KI-Vorgänge hinter einem gRPC-Endpunkt ausführt. In diesem Beispiel ist das Edge-Modul das Modul „spatial-analysis“. Der Verarbeitungsknoten „MediaGraphCognitiveServicesVisionExtension“ erfüllt zwei Aufgaben:

* Er sammelt die Ergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Senke](media-graph-concept.md#iot-hub-message-sink). Der Knoten sendet diese Ereignisse dann an den [IoT Edge Hub-](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Er erfasst außerdem mit einem [Signalgateprozessor](media-graph-concept.md#signal-gate-processor) einen Videoclip von 30 Sekunden aus der RTSP-Quelle und speichert ihn als Media Services-Medienobjekt.

## <a name="create-the-computer-vision-resource"></a>Erstellen der Ressource für maschinelles Sehen

Sie müssen im [Azure-Portal](../../iot-edge/how-to-deploy-modules-portal.md) oder über die Azure CLI eine Azure-Ressource vom Typ Maschinelles Sehen erstellen. Sie können die Ressource erstellen, nachdem Ihre Anforderung des Zugriffs auf den Container genehmigt und Ihre Azure-Abonnement-ID registriert wurde. Wechseln Sie zu https://aka.ms/csgate, um Ihren Anwendungsfall und Ihre Azure-Abonnement-ID zu übermitteln.  Sie müssen die Azure-Ressource mit dem Azure-Abonnement erstellen, das im Zugriffsanforderungsformular angegeben wurde.

### <a name="gathering-required-parameters"></a>Ermitteln erforderlicher Parameter

Es gibt drei primäre Parameter, die für alle Cognitive Services-Container, einschließlich des Containers „spatial-analysis“, benötigt werden. Die Lizenzbedingungen müssen mit dem Wert „accept“ vorhanden sein. Außerdem werden sowohl der Endpunkt-URI als auch der API-Schlüssel benötigt.

### <a name="keys-and-endpoint-uri"></a>Schlüssel und Endpunkt-URI

Ein Schlüssel wird zum Starten des Containers „spatial-analysis“ verwendet. Er ist im Azure-Portal auf der Seite `Keys and Endpoint` der entsprechenden Cognitive Services-Ressource verfügbar. Navigieren Sie zu dieser Seite, und suchen Sie die Schlüssel und den Endpunkt-URI.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Endpunkt-URI":::

## <a name="set-up-azure-stack-edge"></a>Einrichten von Azure Stack Edge

Führen Sie [diese Schritte](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) aus, um Azure Stack Edge einzurichten. Führen Sie dann die folgenden Schritte aus, um das Modul Live Video Analytics und das Modul „spatial analysis“ bereitzustellen.

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

1. Klonen Sie das Repository unter diesem Speicherort: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Öffnen Sie in Visual Studio Code den Ordner, in den das Repository heruntergeladen wurde.
1. Navigieren Sie in Visual Studio Code zum Ordner src/cloud-to-device-console-app. Erstellen Sie darin eine Datei, und geben Sie ihr den Namen appsettings.json. Diese Datei enthält die Einstellungen, die zum Ausführen des Programms erforderlich sind.
1. Führen Sie die folgenden Schritte aus, um den „IotHubConnectionString“ aus Azure Stack Edge abzurufen:

    * Wechseln Sie zu IoT Hub im Azure-Portal, und klicken Sie im linken Navigationsbereich auf `Shared access policies`.
    * Klicken Sie auf `iothubowner`, um die Schlüssel für gemeinsamen Zugriff abzurufen.
    * Kopieren Sie den `Connection String – primary key`, und fügen Sie ihn in das Eingabefeld in Visual Studio Code ein.
    
        Die Verbindungszeichenfolge lautet: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Kopieren Sie den folgenden Inhalt in die Datei. Ersetzen Sie die Variablen.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Navigieren Sie zum Ordner src/edge, und erstellen Sie eine Datei mit dem Namen .env.
1. Kopieren Sie den Inhalt der Datei /clouddrive/lva-sample/edge-deployment/.env. Der Text sollte wie der folgende Code aussehen.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Einrichten der Bereitstellungsvorlage  

Die Bereitstellungsdatei finden Sie unter /src/edge/deployment.spatialAnalysis.template.json. Die Vorlage enthält das Modul „lvaedge“, das Modul „rtspsim“ und das Modul „spatial-analysis“.

Bei der Bereitstellungsvorlagendatei sind einige Aspekte zu beachten:

1. Legen Sie die Portbindung fest.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` sollte in den „createOptions“ der Module „lvaEdge“ und „spatial-analysis“ identisch und auf „host“ festgelegt sein.
1. Damit der RTSP-Simulator ausgeführt wird, müssen Sie Volumebindungen eingerichtet haben. Weitere Informationen finden Sie unter [Einrichten von Docker-Volumebereitstellungen](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Stellen Sie eine Verbindung mit der SMB-Freigabe her](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share), und kopieren Sie die [Beispielvideodatei „bulldozer“](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) in die lokale Freigabe.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Das Modul „rtspsim“ muss folgende Konfiguration aufweisen:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generieren und Bereitstellen des Bereitstellungsmanifests

Mit dem Bereitstellungsmanifest wird definiert, welche Module auf einem Edgegerät bereitgestellt werden. Darüber hinaus werden auch die Konfigurationseinstellungen für diese Module definiert.

Führen Sie die folgenden Schritte aus, um das Manifest auf der Grundlage der Vorlagendatei zu generieren und dann auf dem Edgegerät bereitzustellen.

1. Öffnen Sie Visual Studio Code.
1. Wählen Sie neben dem Bereich AZURE IOT HUB das Symbol Weitere Aktionen aus, um die IoT Hub-Verbindungszeichenfolge festzulegen. Sie können die Zeichenfolge aus der Datei `src/cloud-to-device-console-app/appsettings.json` kopieren.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Räumliche Analyse: Verbindungszeichenfolge":::
1. Klicken Sie mit der rechten Maustaste auf `src/edge/deployment.spatialAnalysis.template.json`, und wählen Sie „IoT Edge-Bereitstellungsmanifest generieren“ aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Räumliche Analyse: Datei „deployment.amd64.json“":::
    
    Mit dieser Aktion sollte eine Manifestdatei mit dem Namen deployment.amd64.json im Ordner src/edge/config erstellt werden.
1. Klicken Sie mit der rechten Maustaste auf `src/edge/config/deployment.spatialAnalysis.amd64.json`, wählen Sie „Create Deployment for Single Device“ (Bereitstellung für einzelnes Gerät erstellen) und dann den Namen des Edgegeräts aus.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Räumliche Analyse: JSON-Datei für Bereitstellungsvorlage":::   
1. Wählen Sie bei Anzeige der Aufforderung zum Auswählen eines IoT Hub-Geräts im Dropdownmenü den Azure Stack Edge-Namen aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:
    
    * Live Video Analytics in IoT Edge (Modulname „lvaEdge“)
    * RTSP-Simulator (Real-Time Streaming Protocol) (Modulname „rtspsim“)
    * Räumliche Analyse (Modulname „spatialAnalysis“)
    
Wenn die Bereitstellung erfolgreich durchgeführt wurde, wird eine Meldung wie die folgende ausgegeben:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Anschließend befinden sich in „Geräte/Module“ die Module `lvaEdge`, `rtspsim`, `spatialAnalysis` und `rtspsim`, und ihr Status sollte „Wird ausgeführt“ lauten.

## <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Öffnen Sie den Explorer-Bereich, und suchen Sie links unten nach „Azure IoT Hub“.
1. Erweitern Sie den Knoten „Geräte“.
1. Klicken Sie in Azure Stack Edge mit der rechten Maustaste, und wählen Sie „Überwachung des integrierten Ereignisendpunkts starten“ aus.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Räumliche Analyse: Starten der Überwachung":::
     
## <a name="run-the-program"></a>Ausführen des Programms

Die Datei „program.cs“ ruft die direkten Methoden in „src/cloud-to-device-console-app/operations.json“ auf. Wir müssen „operations.json“ einrichten und eine Topologie für die Verwendung von Mediengraphen bereitstellen.  

In „operations.json“:

* Legen Sie die Topologie wie folgt fest:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Erstellen Sie eine Graphinstanz wie diese, und legen Sie hier die Parameter in der Topologie fest:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```

>[!Note]
Sehen Sie sich an, wie MediaGraphRealTimeComputerVisionExtension verwendet wird, um eine Verbindung mit dem Modul „spatial-analysis“ herzustellen. Legen Sie ${grpcUrl} auf **tcp://spatialAnalysis:<PORTNUMMER>** fest, z. B. „tcp://spatialAnalysis:50051“.

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Führen Sie eine Debugsitzung aus, und befolgen Sie die **Terminalanweisungen**, um die Topologie festzulegen, die Graphinstanz festzulegen, die Graphinstanz zu aktivieren und schließlich die Ressourcen zu löschen.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn ein Mediengraph instanziiert wird, sollte das Ereignis „MediaSessionEstablished“angezeigt werden, in diesem Fall ein [MediaSessionEstablished-Beispielereignis](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Das Modul „spatial-analysis“ sendet auch KI Insights-Ereignisse an Live Video Analytics und dann an IoT Hub. Sie werden auch in der **Ausgabe** angezeigt. „ENTITY“ bezeichnet Erkennungsobjekte, und „EVENT“ bezeichnet Ereignisse der räumlichen Analyse. Diese Ausgabe wird an Live Video Analytics übermittelt.

Beispielausgabe für „personZoneEvent“ (von dem Vorgang „cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics“):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie verschiedene vom Modul `spatialAnalysis` bereitgestellte Vorgänge aus, z. B. **personCount** und **personDistance**, indem Sie das Flag „enabled“ im Graphknoten der Bereitstellungsmanifestdatei umschalten.
>[!Tip]
> Verwenden Sie eine [Beispielvideodatei](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv), die mehrere Personen zeigt.

> [!NOTE]
> Sie können jeweils nur einen Vorgang ausführen. Stellen Sie daher sicher, dass nur ein Flag auf **true** festgelegt ist und dass die anderen auf **false** festgelegt sind.