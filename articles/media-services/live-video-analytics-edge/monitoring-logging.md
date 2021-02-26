---
title: 'Überwachung und Protokollierung: Azure'
description: Dieser Artikel bietet eine Übersicht der Überwachung und Protokollierung in Live Video Analytics in IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: a77ca6cf9dc66d1efda5741266f1a2eecc2599c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507816"
---
# <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

In diesem Artikel erfahren Sie, wie Sie Ereignisse vom Modul Live Video Analytics in IoT Edge für die Remoteüberwachung empfangen können. 

Darüber hinaus erfahren Sie, wie Sie die Protokolle steuern können, die vom Modul generiert werden.

## <a name="taxonomy-of-events"></a>Taxonomie von Ereignissen

Live Video Analytics in IoT Edge gibt Ereignisse oder Telemetriedaten entsprechend der folgenden Taxonomie aus:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagramm, das die Taxonomie von Ereignissen zeigt.":::

* Betrieb: Ereignisse, die von den Aktionen eines Benutzers oder während der Ausführung eines [Mediendiagramms](media-graph-concept.md) generiert werden
   
   * Volumen: Ein geringes Volumen wird erwartet (mehrmals pro Minute oder sogar seltener)
   * Beispiele:

      - Aufzeichnung gestartet (wie im folgenden Beispiel gezeigt)
      - Aufzeichnung beendet
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnose: Ereignisse, die bei der Diagnose von Leistungsproblemen helfen

   * Volumen: kann hoch sein (mehrmals pro Minute)
   * Beispiele:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Informationen (wie im folgenden Beispiel gezeigt) 
      - Lücken im eingehenden Videofeed

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: Als Teil der Videoanalyse erstellte Ereignisse.

   * Volumen: kann hoch sein (mehrmals pro Minute oder mehr)
   * Beispiele:
      
      - Bewegung erkannt (wie im folgenden Beispiel gezeigt) 
      - Rückschlussergebnis

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Die vom Modul ausgegebenen Ereignisse werden an den [IoT Edge-Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub) gesendet. Sie können von dort aus an andere Ziele weitergeleitet werden. 

### <a name="timestamps-in-analytic-events"></a>Zeitstempel in Analyseereignissen

Wie bereits gezeigt, sind Ereignisse, die im Rahmen der Videoanalyse generiert werden, mit einem Zeitstempel versehen. Wenn die [Livevideo-Aufzeichnung](video-recording-concept.md) im Rahmen der Graphtopologie erfolgt, können Sie anhand dieser Zeitstempel ermitteln, wo das jeweilige Ereignis im aufgezeichneten Video zu finden ist. Im Folgenden finden Sie die Richtlinien zum Zuordnen des Zeitstempels in einem Analyseereignis zur Zeitachse des Videos, das in einem [Azure Media Services-Asset](terminology.md#asset) aufgezeichnet wurde.

Extrahieren Sie zuerst den Wert `eventTime`. Verwenden Sie diesen Wert in einem [Zeitbereichsfilter](playback-recordings-how-to.md#time-range-filters), um einen passenden Teil der Aufzeichnung abzurufen. Angenommen, Sie möchten ein Video abrufen, das 30 Sekunden vor der `eventTime` beginnt und 30 Sekunden danach endet. Im vorherigen Beispiel, in dem `eventTime` „2020-05-12T23:33:09.381Z“ ist, würde eine Anforderung für ein HLS-Manifest für die 30 Sekunden vor und nach `eventTime` wie diese Anforderung aussehen:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Die vorangehende URL würde eine [Hauptwiedergabeliste](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) zurückgeben, die URLs für Medienwiedergabelisten enthält. Die Medienwiedergabeliste würde Einträge enthalten, die diesem vergleichbar sind:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Im obigen Eintrag wird gemeldet, dass ein Videofragment verfügbar ist, das bei einem `timestamp`-Wert von `143039375031270` beginnt. Der `timestamp`-Wert im Analyseereignis verwendet dieselbe Zeitskala wie die Medienwiedergabeliste. Er kann verwendet werden, um das relevante Videofragment zu identifizieren und den richtigen Frame zu suchen.

Weitere Informationen finden Sie in diesen [Artikeln zur framegenauen Suche](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) in HLS.

## <a name="controlling-events"></a>Steuern von Ereignissen

Sie können mit den folgenden Eigenschaften von Modulzwillingen die betriebsbezogenen und Diagnoseereignisse steuern, die von Live Video Analytics im IoT Edge-Modul veröffentlicht werden. Diese Eigenschaften sind im [JSON-Schema für Modulzwillinge](module-twin-configuration-schema.md) dokumentiert.

- `diagnosticsEventsOutputName`: Um Diagnoseereignisse vom Modul abzurufen, schließen Sie diese Eigenschaft ein, und stellen Sie einen beliebigen Wert für sie bereit. Lassen Sie die Eigenschaft aus oder leer, um das Modul an der Veröffentlichung von Diagnoseereignissen zu hindern.
   
- `operationalEventsOutputName`: Um Betriebsereignisse vom Modul abzurufen, schließen Sie diese Eigenschaft ein, und stellen Sie einen beliebigen Wert für sie bereit. Lassen Sie die Eigenschaft aus oder leer, um das Modul an der Veröffentlichung von Betriebsereignissen zu hindern.
   
Analyseereignisse werden von Knoten wie dem Bewegungserkennungsprozessor oder dem HTTP-Erweiterungsprozessor generiert. Die IoT-Hub-Senke wird verwendet, um sie an den IoT Edge-Hub zu senden. 

Sie können das [Routing aller vorangehenden Ereignisse](../../iot-edge/module-composition.md#declare-routes) über die Eigenschaft `desired` des `$edgeHub`-Modulzwillings im Bereitstellungsmanifest steuern:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Im vorangehenden JSON-Code ist `lvaEdge` der Name der Live Video Analytics-Instanz im IoT Edge-Modul. Die Routingregel folgt dem Schema, das in [Deklarieren von Routen](../../iot-edge/module-composition.md#declare-routes) definiert ist.

> [!NOTE]
> Um sicherzustellen, dass die Analyseereignisse den IoT Edge-Hub erreichen, muss ein IoT-Hub-Senkknoten vorhanden sein, der einem Verarbeitungsknoten für die Bewegungserkennung und/oder einem Verarbeitungsknoten der HTTP-Erweiterung im Datenstrom nachgelagert ist.

## <a name="event-schema"></a>Ereignisschema

Ereignisse haben auf dem Edge-Gerät ihren Ursprung und können auf dem Edge-Gerät oder in der Cloud verbraucht werden. Ereignisse, die von Live Video Analytics in IoT Edge generiert werden, entsprechen dem von Azure IoT Hub eingerichteten [Streaming-Messagingmuster](../../iot-hub/iot-hub-devguide-messages-construct.md). Das Muster besteht aus Systemeigenschaften, Anwendungseigenschaften und einem Text.

### <a name="summary"></a>Zusammenfassung

Wenn es über IoT Hub beobachtet wird, verfügt jedes Ereignis über eine Reihe allgemeiner Eigenschaften:

|Eigenschaft   |Eigenschaftstyp| Datentyp   |Beschreibung|
|---|---|---|---|
|`message-id`   |System |guid|  Eindeutige Ereignis-ID.|
|`topic`|   applicationProperty |Zeichenfolge|    Azure Resource Manager-Pfad für das Azure Media Services-Konto.|
|`subject`| applicationProperty |Zeichenfolge|    Unterpfad der Entität, die das Ereignis ausgibt.|
|`eventTime`|   applicationProperty|    Zeichenfolge| Die Uhrzeit, zu der das Ereignis generiert wurde.|
|`eventType`|   applicationProperty |Zeichenfolge|    Ereignistypbezeichner. (Informationen hierzu finden Sie im nächsten Abschnitt.)|
|`body`|body    |Objekt (object)|    Bestimmte Ereignisdaten.|
|`dataVersion`  |applicationProperty|   Zeichenfolge  |{Major}.{Minor}|

### <a name="properties"></a>Eigenschaften

#### <a name="message-id"></a>message-id

Ein global eindeutiger Bezeichner (GUID, Globally Unique Identifier) für das Ereignis.

#### <a name="topic"></a>topic

Stellt das dem Graph zugeordnete Azure Media Services-Konto dar.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Die Entität, die das Ereignis ausgibt:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Die `subject`-Eigenschaft ermöglicht das Zuordnen generischer Ereignisse zum Generierungsmodul. Beispielsweise wäre im Fall eines ungültigen RTSP-Benutzernamens oder -Kennworts das generierte Ereignis `Microsoft.Media.Graph.Diagnostics.ProtocolError` auf dem `/graphInstances/myGraph/sources/myRtspSource`-Knoten.

#### <a name="event-types"></a>Ereignistypen

Ereignistypen werden einem Namespace gemäß diesem Schema zugewiesen:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Ereignisklassen

|Klassenname|Beschreibung|
|---|---|
|Analytics  |Als Teil der Inhaltsanalyse erstellte Ereignisse.|
|Diagnose    |Ereignisse, die bei der Diagnose von Problemen und Leistungsproblemen helfen.|
|Bei Betrieb    |Im Rahmen des Ressourcenbetriebs generierte Ereignisse.|

Die Ereignistypen sind für jede Ereignisklasse spezifisch.

Beispiele:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Ereigniszeit

Die Ereigniszeit wird in einer ISO 8601-Zeichenfolge formatiert. Sie gibt den Zeitpunkt an, zu dem das Ereignis eingetreten ist.

### <a name="azure-monitor-collection-via-telegraf"></a>Azure Monitor-Sammlung mithilfe von Telegraf

Diese Metriken werden vom Modul „Live Video Analytics in IoT Edge“ gemeldet:  

|Metrikname|type|Bezeichnung|BESCHREIBUNG|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Maßstab|iothub, edge_device, module_name, graph_topology|Gesamtanzahl der aktiven Graphen pro Topologie|
|lva_received_bytes_total|Zähler|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Gesamtanzahl der von einem Knoten empfangenen Bytes. Wird nur für RTSP-Quellen unterstützt.|
|lva_data_dropped_total|Zähler|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Zähler für alle gelöschten Daten (Ereignisse, Medien usw.).|

> [!NOTE]
> Ein [Prometheus-Endpunkt](https://prometheus.io/docs/practices/naming/) wird an Port 9600 des Containers verfügbar gemacht. Wenn Sie dem Modul „Live Video Analytics in IoT Edge“ den Namen „lvaEdge“ gegeben haben, kann durch Senden einer GET-Anforderung an http://lvaEdge:9600/metrics auf Metriken zugegriffen werden.   

Führen Sie die folgenden Schritte aus, um die Erfassung von Metriken aus dem Modul „Live Video Analytics in IoT Edge“ zu aktivieren:

1. Erstellen Sie auf dem Entwicklungscomputer einen Ordner, und navigieren Sie zu diesem Ordner.

1. Erstellen Sie in dem Ordner eine `telegraf.toml`-Datei, die folgende Konfigurationen enthält:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Ersetzen Sie unbedingt die Variablen in der TOML-Datei. Die Variablen werden mit geschweiften Klammern (`{}`) gekennzeichnet.

1. Erstellen Sie in demselben Ordner eine Dockerfile, die folgende Befehle enthält:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Erstellen Sie mit Docker CLI-Befehlen die Docker-Datei, und veröffentlichen Sie das Image in Ihrer Azure Container Registry-Instanz.
    
   Weitere Informationen zur Verwendung der Docker CLI zum Pushen in eine Containerregistrierung finden Sie unter [Pushübertragung des ersten Images an eine private Containerregistrierung mit der Docker CLI](../../container-registry/container-registry-get-started-docker-cli.md). Weitere Informationen zu Azure Container Registry finden Sie in der [Dokumentation](../../container-registry/index.yml).


1. Fügen Sie nach Abschluss des Pushens in Azure Container Registry den folgenden Knoten ihrer Bereitstellungsmanifestdatei hinzu:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Ersetzen Sie unbedingt die Variablen in der Manifestdatei. Die Variablen werden mit geschweiften Klammern (`{}`) gekennzeichnet.


   Azure Monitor kann [über den Dienstprinzipal authentifiziert werden](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Das Azure Monitor-Telegraf-Plug-In stellt [verschiedene Methoden der Authentifizierung](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication) bereit. 

  1. Um die Dienstprinzipalauthentifizierung zu verwenden, legen Sie diese Umgebungsvariablen fest:  
     `AZURE_TENANT_ID`: Gibt den Mandanten für die Authentifizierung an.  
     `AZURE_CLIENT_ID`: Gibt die zu verwendende App-Client-ID an.  
     `AZURE_CLIENT_SECRET`: Gibt das zu verwendende App-Geheimnis an.  
     
     >[!TIP]
     > Sie können dem Dienstprinzipal die Rolle **Herausgeber von Überwachungsmetriken** zuweisen. Führen Sie die Schritte in **[Erstellen eines Dienstprinzipals](https://docs.microsoft.com/azure/azure-arc/data/upload-metrics-and-logs-to-azure-monitor?pivots=client-operating-system-macos-and-linux#create-service-principal)** aus, um den Dienstprinzipal zu erstellen und die Rolle zuzuweisen.

1. Sobald die Module bereitgestellt sind, werden die Metriken in Azure Monitor unter einem einzigen Namespace angezeigt. Metriknamen entsprechen den von Prometheus ausgegebenen. 

   Wechseln Sie in diesem Fall im Azure-Portal zum IoT-Hub, und wählen Sie im linken Bereich **Metriken** aus. Die Metriken sollten dort angezeigt werden.

Wenn Sie Prometheus zusammen mit [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-analytics-tutorial) verwenden, können Sie Metriken generieren und [überwachen](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), z. B. das verwendete CPUPercent, MemoryUsedPercent usw. Mithilfe der Kusto-Abfragesprache können Sie Abfragen wie die folgenden schreiben und den von den IoT Edge-Modulen verwendeten CPU-Prozentwert ermitteln.
```kusto
let cpu_metrics = promMetrics_CL
| where Name_s == "edgeAgent_used_cpu_percent"
| extend dimensions = parse_json(Tags_s)
| extend module_name = tostring(dimensions.module_name)
| where module_name in ("lvaEdge","yolov3","tinyyolov3")
| summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
cpu_metrics
| summarize cpu_percent = sum(cpu_percent) by TimeGenerated
| extend module_name = "Total"
| union cpu_metrics
```

[ ![Diagramm, das die Metriken mithilfe der Kusto-Abfrage anzeigt.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Protokollierung

Wie bei anderen IoT Edge-Modulen, können Sie außerdem [die Containerprotokolle auf dem Edge-Gerät untersuchen](../../iot-edge/troubleshoot.md#check-container-logs-for-issues). Sie können die in den Protokollen erfassten Informationen durch die [folgenden Modulzwillingseigenschaften](module-twin-configuration-schema.md) konfigurieren:

* `logLevel`

   * Zulässige Werte sind `Verbose`, `Information`, `Warning`, `Error` und `None`.
   * Der Standardwert ist `Information`. Die Protokolle enthalten Fehler, Warnungen und Informationen.
   * Wenn Sie den Wert auf `Warning` festlegen, enthalten die Protokolle Fehler- und Warnmeldungen.
   * Wenn Sie den Wert auf `Error` festlegen, enthalten die Protokolle nur Fehlermeldungen.
   * Wenn Sie den Wert auf `None` festlegen, werden keine Protokolle generiert. (Diese Konfiguration wird nicht empfohlen.)
   * Verwenden Sie `Verbose` nur, wenn Sie Protokolle für den Azure-Support freigeben müssen, um ein Problem zu diagnostizieren.

* `logCategories`

   * Eine durch Trennzeichen getrennte Liste einer oder mehrerer dieser Werte: `Application`, `Events`, `MediaPipeline`.
   * Standardwert: `Application, Events`.
   * `Application`: Allgemeine Informationen aus dem Modul wie Nachrichten zum Modulstart, Umgebungsfehler und Aufrufen direkter Methoden.
   * `Events`: Alle Ereignisse, die weiter oben in diesem Artikel beschrieben sind.
   * `MediaPipeline`: Detailprotokolle, die möglicherweise Einblicke bei der Problembehandlung geben können, etwa bei Problemen beim Herstellen einer Verbindung mit einer RTSP-fähigen Kamera.
   
### <a name="generating-debug-logs"></a>Generieren von Debugprotokollen

In bestimmten Fällen müssen Sie möglicherweise ausführlichere Protokolle als die oben beschriebenen generieren, um den Azure-Support beim Beheben eines Problems zu unterstützen. So generieren Sie diese Protokolle:

1. [Verknüpfen Sie den Modulspeicher mit dem Gerätespeicher](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) über `createOptions`. Wenn Sie eine [Vorlage für ein Bereitstellungsmanifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) aus den Schnellstartanleitungen untersuchen, sehen Sie diesen Code:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Mit diesem Code schreibt das Edge-Modul Protokolle in den Gerätespeicherpfad `/var/local/mediaservices/`. 

 1. Fügen Sie dem Modul die folgende `desired`-Eigenschaft hinzu:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Das Modul schreibt nun Debugprotokolle in einem binären Format in den Gerätespeicherpfad `/var/local/mediaservices/debuglogs/`. Sie können diese Protokolle für den Azure-Support freigeben.

## <a name="faq"></a>Häufig gestellte Fragen

Wenn Sie Fragen haben, finden Sie weitere Informationen in den [häufig gestellten Fragen zu Überwachung und Metriken](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Nächste Schritte

[Fortlaufende Videoaufzeichnung](continuous-video-recording-tutorial.md)