---
ms.openlocfilehash: 8bcef40dad9c67e9e2c6d6c4a051045999487027
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530903"
---
Wenn Sie den Mediengraphen ausführen, durchlaufen die Ergebnisse vom Knoten des Bewegungserkennungsprozessors den Knoten der IoT Hub-Senke und werden an den IoT-Hub gesendet. Die im **Ausgabefenster** von Visual Studio Code angezeigten Meldungen enthalten jeweils die Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Live Video Analytics-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Für die obige Ausgabe gilt Folgendes: 

* Bei der Meldung handelt es sich um ein Diagnoseereignis (`MediaSessionEstablished`). Hierdurch wird angegeben, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* In `applicationProperties` wird von `subject` auf den Knoten in der Graphtopologie verwiesen, von dem die Meldung generiert wurde. In diesem Fall stammt die Meldung vom RTSP-Quellknoten.
* In `applicationProperties` wird mit `eventType` angegeben, dass es sich um ein Diagnoseereignis handelt.
* `eventTime` gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt `body` enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="motiondetection-event"></a>Ereignis „MotionDetection“

Wenn Bewegung erkannt wird, sendet das Modul „Live Video Analytics in IoT Edge“ ein Rückschlussereignis. `type` ist auf `motion` festgelegt, um anzugeben, dass es sich um ein Ergebnis des Bewegungserkennungsprozessors handelt. Der Wert `eventTime` gibt (in UTC-Zeit) an, wann die Bewegung aufgetreten ist. 

Hier ist ein Beispiel für diese Meldung angegeben:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  } 
}  
```

In diesem Beispiel: 

* Der Wert `eventTime` ist die Uhrzeit, zu der das Ereignis eingetreten ist.
* Der Wert `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `timestamp` und `inferences`.
* In den Daten von `inferences` ist angegeben, dass `type` auf `motion` festgelegt ist. Hier sind auch zusätzliche Daten zum Ereignis `motion` zu finden.
* Der Abschnitt `box` enthält die Koordinaten für einen Begrenzungsrahmen um das sich bewegende Objekt. Die Werte werden anhand der Breite und Höhe des Videos (in Pixel) normalisiert. In diesem Beispiel beträgt die Breite 1920 und die Höhe 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
