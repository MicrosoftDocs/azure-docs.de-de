---
ms.openlocfilehash: 373b7b8dbf2ccc516edbbb3b87e284d88dfaf0c2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508488"
---
Wenn Sie den Mediengraph ausführen, werden die Ergebnisse aus dem Knoten des HTTP-Erweiterungsprozessors über den Knoten der IoT Hub-Senke an den IoT-Hub übergeben. Die im **Ausgabefenster** angezeigten Meldungen enthalten jeweils die Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Live Video Analytics-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp ist `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
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

Der Knoten des HTTP-Erweiterungsprozessors empfängt Rückschlussergebnisse vom yolov3-Modul. Anschließend gibt er die Ergebnisse über den Knoten der IoT Hub-Senke als Rückschlussereignisse aus. 

In diesen Ereignissen ist der Typ auf `entity` festgelegt, um anzuzeigen, dass es sich um eine Entität wie etwa ein Auto oder einen LKW handelt. Der Wert von `eventTime` ist die UTC-Uhrzeit, zu der das Objekt erkannt wurde. 

Im folgenden Beispiel wurden zwei Autos auf dem gleichen Video-Einzelbild erkannt, mit unterschiedlichen Konfidenzwerten.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  }
}
```

Beachten Sie in den Meldungen die folgenden Details:

* Der Wert `eventTime` ist die Uhrzeit, zu der das Ereignis eingetreten ist.
* Der Abschnitt `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `inferences`.
* Im Abschnitt `inferences` ist angegeben, dass `type` auf `entity` festgelegt ist. Dieser Abschnitt enthält zusätzliche Daten zur Entität.
