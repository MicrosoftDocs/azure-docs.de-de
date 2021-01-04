---
title: Vorgänge der räumlichen Analyse
titleSuffix: Azure Cognitive Services
description: Enthält eine Beschreibung der Vorgänge der räumlichen Analyse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014542"
---
# <a name="spatial-analysis-operations"></a>Vorgänge der räumlichen Analyse

Die räumliche Analyse ermöglicht die Analyse von in Echtzeit gestreamten Videodaten von Kamerageräten. Für jedes von Ihnen konfigurierte Kameragerät wird über die Vorgänge der räumlichen Analyse ein Ausgabestream mit JSON-Nachrichten generiert, die an Ihre Instanz von Azure IoT Hub gesendet werden. 

Für den Container für räumliche Analyse werden die folgenden Vorgänge implementiert:

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br> Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Zone durchquert, und gibt Informationen zur Richtung an. |
| cognitiveservices.vision.spatialanalysis-persondistance | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Alle obigen Vorgänge sind auch in der `.debug`-Version verfügbar. Sie ermöglichen die Visualisierung der Videoframes während der Verarbeitung. Sie müssen `xhost +` auf dem Hostcomputer ausführen, um die Visualisierung von Videoframes und Ereignissen zu ermöglichen.

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br> Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Zone durchquert, und gibt Informationen zur Richtung an. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Die räumliche Analyse kann auch mit [Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) als Video-KI-Modul ausgeführt werden. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br> Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Zone durchquert, und gibt Informationen zur Richtung an. |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Live Video Analytics-Vorgänge sind auch in der `.debug`-Version verfügbar (z. B. cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug). Sie ermöglicht das Visualisieren der Videoframes während der Verarbeitung. Sie müssen `xhost +` auf dem Hostcomputer ausführen, um die Visualisierung der Videoframes und Ereignisse zu ermöglichen.

> [!IMPORTANT]
> Mit den KI-Modellen für maschinelles Sehen können Menschen in Videomaterial erkannt und lokalisiert werden. Die Ausgabe erfolgt dann über einen Begrenzungsrahmen, der den menschlichen Körper umgibt. Bei den KI-Modellen wird nicht versucht, Gesichter oder die Identitäten oder demografischen Daten von Personen zu erkennen.

Hier sind die Parameter angegeben, die von diesen Vorgängen der räumlichen Analyse benötigt werden.

| Vorgangsparametern| Beschreibung|
|---------|---------|
| Vorgangs-ID | Der Vorgangsbezeichner aus der obigen Tabelle.|
| enabled | Boolesch: „true“ oder „false“|
| VIDEO_URL| Die RTSP-URL für das Kameragerät (Beispiel: `rtsp://username:password@url`). Bei der räumlichen Analyse wird der Datenstrom mit H.264-Codierung entweder für RTSP, http oder mp4 unterstützt. |
| VIDEO_SOURCE_ID | Ein Anzeigename für das Kameragerät oder den Videostream. Dies wird zusammen mit der JSON-Ausgabe des Ereignisses zurückgegeben.|
| VIDEO_IS_LIVE| „true“ für Kamerageräte, „false“ für Videoaufzeichnungen|
| VIDEO_DECODE_GPU_INDEX| Gibt an, welche GPU zum Decodieren des Videoframes verwendet werden soll. Standardmäßig ist „0“ angegeben. Diese Angabe sollte dem `gpu_index` in einer anderen Knotenkonfiguration entsprechen, z. B. `VICA_NODE_CONFIG`, `DETECTOR_NODE_CONFIG`.|
| DETECTOR_NODE_CONFIG | JSON-Code, mit dem angegeben wird, auf welcher GPU der Erkennungsknoten ausgeführt werden soll. Hierfür sollte das folgende Format verwendet werden: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | JSON-Konfiguration für Zone und Linie wie unten beschrieben.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-personcount

 Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Name | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für die Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt und gezählt werden. Die Punkte des Polygons basieren auf normalisierten Koordinaten (0 - 1), wobei die obere Linke Ecke die Werte (0,0, 0,0) und die untere rechte Ecke die Werte (1,0, 1,0) aufweist.   
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcount** sollte dies `count` lauten.|
| `trigger` | Zeichenfolge| Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind `event` zum Senden von Ereignissen, wenn sich die Anzahl ändert, oder `interval` zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.
| `interval` | Zeichenfolge| Ein Zeitraum in Sekunden, in dem die Personenzahl aggregiert wird, bevor ein Ereignis ausgelöst wird. Der Vorgang setzt die Analyse der Szene mit konstanter Geschwindigkeit fort und gibt die häufigste Anzahl für dieses Intervall zurück. Das Aggregationsintervall gilt sowohl für `event` als auch für `interval`.|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Linienkonfiguration für cognitiveservices.vision.spatialanalysis-personcrossingline

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Linie konfiguriert wird. Sie können für diesen Vorgang mehrere Linien für die Überschreitung konfigurieren.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Name | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `lines` | list| Die Liste mit den Linien.|
| `name` | Zeichenfolge| Der Anzeigename für diese Linie.|
| `line` | list| Die Definition der Linie. Dies ist eine direktionale Linie zum Festlegen von „Betreten“ bzw. „Verlassen“.|
| `start` | Wertpaar| x/y-Koordinaten für den Startpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. |
| `end` | Wertpaar| x/y-Koordinaten für den Endpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. |
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcrossingline** sollte dies `linecrossing` lauten.|
|`trigger`|Zeichenfolge|Der Typ des Triggers für das Senden eines Ereignisses.<br>Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Linie überschreitet.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Name | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt oder gezählt werden. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. 
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** sollte dies `enter` oder `exit` lauten.|
| `trigger`|Zeichenfolge|Der Typ des Triggers für das Senden eines Ereignisses.<br>Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Zone betritt oder verlässt.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-persondistance

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone für **cognitiveservices.vision.spatialanalysis-persondistance** konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Name | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen gezählt werden und der Abstand zwischen den Personen gemessen wird. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. 
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-persondistance** sollte dies `people_distance` lauten.|
| `trigger` | Zeichenfolge| Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind `event` zum Senden von Ereignissen, wenn sich die Anzahl ändert, oder `interval` zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.
| `interval` | Zeichenfolge | Ein Zeitraum in Sekunden, in dem die Verstöße aggregiert werden, bevor ein Ereignis ausgelöst wird. Das Aggregationsintervall gilt sowohl für `event` als auch für `interval`.|
| `output_frequency` | INT | Die Rate, mit der Ereignisse ausgegeben werden. Bei `output_frequency` = X wird jedes x-te Ereignis ausgegeben. Beispiel: `output_frequency` = 2 bedeutet, dass jedes zweite Ereignis ausgegeben wird. Die Ausgabehäufigkeit (output_frequency) gilt für `event` und für `interval`.|
| `minimum_distance_threshold` | float| Ein Abstand in Fuß, bei dem das Ereignis „TooClose“ ausgelöst wird, wenn Personen weniger als den angegebenen Abstand voneinander aufweisen.|
| `maximum_distance_threshold` | float| Ein Abstand in Fuß, bei dem das Ereignis „TooFar“ ausgelöst wird, wenn Personen mehr als den angegebenen Abstand voneinander aufweisen.|

Dies ist ein Beispiel für eine JSON-Eingabe für den DETECTOR_NODE_CONFIG-Parameter, mit dem eine Zone für **cognitiveservices.vision.spatialanalysis-persondistance** konfiguriert wird.

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Name | Typ| Beschreibung|
|---------|---------|---------|
| `gpu_index` | Zeichenfolge| Der GPU-Index, auf dem die Ausführung dieses Vorgangs basiert.|
| `do_calibration` | Zeichenfolge | Gibt an, dass die Kalibrierung aktiviert ist. `do_calibration` muss auf „true“ festgelegt sein, damit **cognitiveservices.vision.spatialanalysis-persondistance** richtig funktioniert.|

Weitere Informationen zu Zonen- und Linienkonfigurationen finden Sie im Artikel mit den Informationen zur [Kameraplatzierung](spatial-analysis-camera-placement.md).

## <a name="spatial-analysis-operation-output"></a>Ausgabe von Vorgängen der räumlichen Analyse

Die Ereignisse aus den einzelnen Vorgängen werden für Azure IoT Hub im JSON-Format ausgegeben.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-Format für cognitiveservices.vision.spatialanalysis-personcount: KI Insights

JSON-Beispielcode für ein Ereignis, das von diesem Vorgang ausgegeben wird.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Ereignisfeldname | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `status` | Zeichenfolge| „Enter“ (Betreten) oder „Exit“ (Verlassen)|
| `side` | INT| Zahl der Polygonseite, die von der Person überschritten wurde|
| `zone` | Zeichenfolge | „Name“ des Polygonfelds, das für die durchquerte Zone steht|
| `trigger` | Zeichenfolge| Der Triggertyp lautet je nach Wert von `trigger` in SPACEANALYTICS_CONFIG entweder „event“ (Ereignis) oder „interval“ (Intervall).|

| Name des Erkennungsfelds | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|

| Name des Felds „SourceInfo“ | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|
| `cameraCallibrationInfo` | collection | Sammlung mit Werten|
| `status` | Zeichenfolge | Gibt an, ob die Kamerakalibrierung in Bezug auf die Bodenfläche abgeschlossen ist (Complete).|
| `cameraHeight` | float | Die Höhe der Kamera über dem Boden in Fuß. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `focalLength` | float | Die Brennweite der Kamera in Pixel. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `tiltUpAngle` | float | Der Neigungswinkel der Kamera gegenüber der Vertikalen. Dieser Wert wird von der automatischen Kalibrierung abgeleitet.|

| Name des Felds „SourceInfo“ | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-Format für cognitiveservices.vision.spatialanalysis-personcrossingline: KI Insights

JSON-Beispiel für Erkennungen, die von diesem Vorgang ausgegeben werden.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Ereignisfeldname | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `status` | Zeichenfolge| Richtung der Linienüberschreitung, entweder „CrossLeft“ oder „CrossRight“|
| `zone` | Zeichenfolge | Feld „Name“ der überschrittenen Linie|

| Name des Erkennungsfelds | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|

| Name des Felds „SourceInfo“ | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|


> [!IMPORTANT]
> Das KI-Modell erkennt eine Person unabhängig davon, ob ihr Gesicht zur Kamera oder in die andere Richtung weist. Das KI-Modell führt keine Gesichtserkennung bzw. anderweitige Erkennung durch und gibt keine biometrischen Informationen aus. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-Format für cognitiveservices.vision.spatialanalysis-personcrossingpolygon: KI Insights

JSON-Beispiel für Erkennungen, die von diesem Vorgang ausgegeben werden.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Ereignisfeldname | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `status` | Zeichenfolge| Richtung von Polygondurchquerungen, entweder „Enter“ oder „Exit“|
| `zone` | Zeichenfolge | „Name“ des Polygonfelds, das für die durchquerte Zone steht|

| Name des Erkennungsfelds | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-Format für cognitiveservices.vision.spatialanalysis-persondistance: KI Insights

JSON-Beispiel für Erkennungen, die von diesem Vorgang ausgegeben werden.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Ereignisfeldname | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `personCount` | INT| Anzahl von erkannten Personen bei Ausgabe des Ereignisses|
| `averageDistance` | float| Durchschnittlicher Abstand zwischen allen erkannten Personen in Fuß|
| `minimumDistanceThreshold` | float| Der Abstand in Fuß, bei dem das Ereignis „TooClose“ ausgelöst wird, wenn Personen weniger als den angegebenen Abstand voneinander aufweisen.|
| `maximumDistanceThreshold` | float| Der Abstand in Fuß, bei dem das Ereignis „TooFar“ ausgelöst wird, wenn Personen mehr als den angegebenen Abstand voneinander aufweisen.|
| `eventName` | Zeichenfolge| Der Ereignisname lautet `TooClose` bei einem Verstoß gegen `minimumDistanceThreshold`, `TooFar` bei einem Verstoß gegen `maximumDistanceThreshold` oder `unknown`, falls die automatische Kalibrierung nicht abgeschlossen wurde.|
| `distanceViolationPersonCount` | INT| Anzahl von erkannten Personen, die gegen `minimumDistanceThreshold` oder `maximumDistanceThreshold` verstoßen haben|
| `zone` | Zeichenfolge | Das Feld „name“ des Polygons, das für die Zone steht, in der der Abstand zwischen Personen überwacht wird.|
| `trigger` | Zeichenfolge| Der Triggertyp lautet je nach Wert von `trigger` in SPACEANALYTICS_CONFIG entweder „event“ (Ereignis) oder „interval“ (Intervall).|

| Name des Erkennungsfelds | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|
| `centerGroundPoint` | 2 Gleitkommawerte| `x`/`y`-Werte mit den Koordinaten der abgeleiteten Position der Person auf dem Boden in Fuß. `x` ist der Abstand von der Kamera auf der senkrechten Linie von der Kamerabildebene bis zum Boden in Fuß. `y` ist der Abstand von der Kamera parallel zur Bildebene bis zum Boden in Fuß.|

| Name des Felds „SourceInfo“ | Typ| Beschreibung|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|
| `cameraCallibrationInfo` | collection | Sammlung mit Werten|
| `status` | Zeichenfolge | Gibt an, ob die Kamerakalibrierung in Bezug auf die Bodenfläche abgeschlossen ist (Complete).|
| `cameraHeight` | float | Die Höhe der Kamera über dem Boden in Fuß. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `focalLength` | float | Die Brennweite der Kamera in Pixel. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `tiltUpAngle` | float | Der Neigungswinkel der Kamera gegenüber der Vertikalen. Dieser Wert wird von der automatischen Kalibrierung abgeleitet.|


## <a name="use-the-output-generated-by-the-container"></a>Verwenden der vom Container generierten Ausgabe

Es kann sein, dass Sie die Erkennung über die räumliche Analyse oder zugehörige Ereignisse in Ihre Anwendung integrieren möchten. Hier sind einige Punkte angegeben, die Sie beachten sollten: 

* Verwenden Sie das Azure Event Hub-SDK für Ihre gewählte Programmiersprache, um eine Verbindung mit dem Azure IoT Hub-Endpunkt herstellen und die Ereignisse empfangen zu können. Weitere Informationen finden Sie unter [Lesen von Nachrichten, die von Geräten an die Cloud gesendet werden, vom integrierten Endpunkt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). 
* Richten Sie das **Nachrichtenrouting** auf Ihrer Azure IoT Hub-Instanz ein, um Ereignisse an andere Endpunkte zu senden oder die Ereignisse in Ihrem Datenspeicher zu speichern. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../../iot-hub/iot-hub-devguide-messages-d2c.md). 
* Richten Sie einen Azure Stream Analytics-Auftrag ein, um die Ereignisse bei ihrem Eintreffen in Echtzeit zu verarbeiten und Visualisierungen zu erstellen. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Bereitstellung von Vorgängen zur räumlichen Analyse im großen Stil (mehrere Kameras)

Um die beste Leistung und Auslastung der GPUs zu erzielen, können Sie beliebige Vorgänge zur räumlichen Analyse mit mehreren Kameras mithilfe von Graphinstanzen bereitstellen. Im Folgenden finden Sie ein Beispiel für die Ausführung des Vorgangs `cognitiveservices.vision.spatialanalysis-personcount` für fünf Kameras.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Name | Typ| Beschreibung|
|---------|---------|---------|
| `batch_size` | INT | Gibt die Anzahl der Kameras an, die beim Vorgang verwendet werden. |

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Webanwendung für die Erfassung der Personenanzahl](spatial-analysis-web-app.md)
* [Protokollierung und Problembehandlung](spatial-analysis-logging.md)
* [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md)
* [Leitfaden zur Platzierung von Zonen und Linien](spatial-analysis-zone-line-placement.md)