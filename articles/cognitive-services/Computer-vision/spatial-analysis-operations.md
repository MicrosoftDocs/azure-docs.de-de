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
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: af220106c415165a0dbe7cda64a31a6068f53164
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981781"
---
# <a name="spatial-analysis-operations"></a>Vorgänge der räumlichen Analyse

Die räumliche Analyse ermöglicht die Analyse von in Echtzeit gestreamten Videodaten von Kamerageräten. Für jedes von Ihnen konfigurierte Kameragerät wird über die Vorgänge der räumlichen Analyse ein Ausgabestream mit JSON-Nachrichten generiert, die an Ihre Instanz von Azure IoT Hub gesendet werden. 

Für den Container für räumliche Analyse werden die folgenden Vorgänge implementiert:

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. Die Zone muss vollständig von einer einzelnen Kamera abgedeckt werden, damit PersonCount eine exakte Gesamtzahl aufzeichnen kann. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Hiermit wird ein _personZoneEnterExitEvent_-Ereignis ausgegeben, wenn eine Person die Zone betritt oder verlässt. Außerdem werden direktionale Informationen wie die nummerierte Seite der überschrittenen Zone bereitstellt. Hiermit wird ein _personZoneDwellTimeEvent_-Ereignis ausgegeben, wenn eine Person die Zone verlässt. Außerdem wird neben direktionalen Informationen auch die Zeitspanne in Millisekunden angegeben, die die Person in der Zone verbracht hat. |
| cognitiveservices.vision.spatialanalysis-persondistance | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Alle obigen Vorgänge sind auch in der `.debug`-Version verfügbar. Sie ermöglichen die Visualisierung der Videoframes während der Verarbeitung. Sie müssen `xhost +` auf dem Hostcomputer ausführen, um die Visualisierung von Videoframes und Ereignissen zu ermöglichen.

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Hiermit wird ein _personZoneEnterExitEvent_-Ereignis ausgegeben, wenn eine Person die Zone betritt oder verlässt. Außerdem werden direktionale Informationen wie die nummerierte Seite der überschrittenen Zone bereitstellt. Hiermit wird ein _personZoneDwellTimeEvent_-Ereignis ausgegeben, wenn eine Person die Zone verlässt. Außerdem wird neben direktionalen Informationen auch die Zeitspanne in Millisekunden angegeben, die die Person in der Zone verbracht hat. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Die räumliche Analyse kann auch mit [Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) als Video-KI-Modul ausgeführt werden. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. <br> Gibt ein _personCountEvent_-Anfangsereignis und anschließend _personCountEvent_-Ereignisse aus, wenn sich die Anzahl ändert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet. <br>Gibt ein _personLineEvent_-Ereignis aus, wenn die Person die Linie überschreitet, und gibt Informationen zur Richtung an. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Hiermit wird ein _personZoneEnterExitEvent_-Ereignis ausgegeben, wenn eine Person die Zone betritt oder verlässt. Außerdem werden direktionale Informationen wie die nummerierte Seite der überschrittenen Zone bereitstellt. Hiermit wird ein _personZoneDwellTimeEvent_-Ereignis ausgegeben, wenn eine Person die Zone verlässt. Außerdem wird neben direktionalen Informationen auch die Zeitspanne in Millisekunden angegeben, die die Person in der Zone verbracht hat.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen. <br> Gibt regelmäßig ein _personDistanceEvent_-Ereignis mit dem Ort der einzelnen Verstöße gegen die Abstandsregel aus. |

Live Video Analytics-Vorgänge sind auch in der `.debug`-Version verfügbar (z. B. cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug). Sie ermöglicht das Visualisieren der Videoframes während der Verarbeitung. Sie müssen `xhost +` auf dem Hostcomputer ausführen, um die Visualisierung der Videoframes und Ereignisse zu ermöglichen.

> [!IMPORTANT]
> Mit den KI-Modellen für maschinelles Sehen können Menschen in Videomaterial erkannt und lokalisiert werden. Die Ausgabe erfolgt dann über einen Begrenzungsrahmen, der den menschlichen Körper umgibt. Bei den KI-Modellen wird nicht versucht, die Identitäten oder demografischen Daten von Personen zu ermitteln.

Hier sind die Parameter angegeben, die von diesen Vorgängen der räumlichen Analyse benötigt werden.

| Vorgangsparametern| Beschreibung|
|---------|---------|
| Vorgangs-ID | Der Vorgangsbezeichner aus der obigen Tabelle.|
| enabled | Boolesch: „true“ oder „false“|
| VIDEO_URL| Dies ist die RTSP-URL für das Kameragerät (Beispiel: `rtsp://username:password@url`). Bei der räumlichen Analyse wird der Datenstrom mit H.264-Codierung entweder über RTSP, HTTP oder mp4 unterstützt. Video_URL kann mithilfe der AES-Verschlüsselung als verschleierter base64-Zeichenfolgenwert bereitgestellt werden. Wenn die Video-URL verschleiert ist, müssen `KEY_ENV` und `IV_ENV` als Umgebungsvariablen bereitgestellt werden. Das Beispielhilfsprogramm zum Generieren von Schlüsseln und Verschlüsselungen finden Sie [hier](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Ein Anzeigename für das Kameragerät oder den Videostream. Dies wird zusammen mit der JSON-Ausgabe des Ereignisses zurückgegeben.|
| VIDEO_IS_LIVE| „true“ für Kamerageräte, „false“ für Videoaufzeichnungen|
| VIDEO_DECODE_GPU_INDEX| Gibt an, welche GPU zum Decodieren des Videoframes verwendet werden soll. Standardmäßig ist „0“ angegeben. Diese Angabe sollte dem `gpu_index` in einer anderen Knotenkonfiguration entsprechen, z. B. `VICA_NODE_CONFIG`, `DETECTOR_NODE_CONFIG`.|
| INPUT_VIDEO_WIDTH | Dies ist die Framebreite des eingegangenen Videos oder Streams (z. B. 1920). Hierbei handelt es sich um ein optionales Feld. Wenn dieses angegeben ist, wird der Frame auf diese Dimension skaliert, aber das Seitenverhältnis wird weiterhin beibehalten.|
| DETECTOR_NODE_CONFIG | JSON-Code, mit dem angegeben wird, auf welcher GPU der Erkennungsknoten ausgeführt werden soll. Hierfür sollte das folgende Format verwendet werden: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | JSON-Konfiguration für Zone und Linie wie unten beschrieben.|
| ENABLE_FACE_MASK_CLASSIFIER | Mit `True` wird das Erkennen von Personen mit Gesichtsmasken im Videostream aktiviert, mit `False` wird diese Option deaktiviert. Diese Einstellung ist standardmäßig deaktiviert. Die Gesichtsmaskenerkennung erfordert, dass der width-Parameter für das Eingangsvideo 1920 ist (`"INPUT_VIDEO_WIDTH": 1920`). Das Gesichtsmaskenattribut wird nicht zurückgegeben, wenn erkannte Personen nicht in die Kamera sehen oder zu weit davon entfernt sind. Weitere Informationen finden Sie im [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md). |

Dies ist ein Beispiel für die DETECTOR_NODE_CONFIG-Parameter für alle Vorgänge der räumlichen Analyse.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sampling_num": 80,
"calibration_quality_check_sampling_times": 5,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000,
"recalibration_score": 75
}
```

| Name | type| BESCHREIBUNG|
|---------|---------|---------|
| `gpu_index` | Zeichenfolge| Der GPU-Index, auf dem die Ausführung dieses Vorgangs basiert.|
| `do_calibration` | Zeichenfolge | Gibt an, dass die Kalibrierung aktiviert ist. `do_calibration` muss auf „true“ festgelegt sein, damit **cognitiveservices.vision.spatialanalysis-persondistance** richtig funktioniert. Die Zeichenfolge „do_calibration“ ist standardmäßig auf TRUE festgelegt. |
| `enable_recalibration` | bool | Hiermit wird angegeben, ob die automatische Neukalibrierung aktiviert ist. Der Standardwert ist `true`.|
| `calibration_quality_check_frequency_seconds` | INT | Dies ist die minimale Anzahl von Sekunden zwischen den einzelnen Qualitätsprüfungen, um zu bestimmen, ob eine Neukalibrierung erforderlich ist. Der Standardwert ist `86400` (24 Stunden). Nur verwendet, wenn `enable_recalibration=True`.|
| `calibration_quality_check_sampling_num` | INT | Dies ist die Anzahl der zufällig ausgewählten Stichproben gespeicherter Daten, die bei der Qualitätsprüfung pro Fehlermessung verwendet werden sollen. Der Standardwert ist `80`. Nur verwendet, wenn `enable_recalibration=True`.|
| `calibration_quality_check_sampling_times` | INT | Dies ist die Anzahl der für verschiedene zufällig ausgewählte Datenstichproben durchgeführten Fehlermessungen pro Qualitätsprüfung. Der Standardwert ist `5`. Nur verwendet, wenn `enable_recalibration=True`.|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | Dies ist die minimale Anzahl von Sekunden zwischen dem Erfassen neuer Datenstichproben für die Neukalibrierung und der Qualitätsprüfung. Der Standardwert ist `300` (fünf Minuten). Nur verwendet, wenn `enable_recalibration=True`.|
| `calibration_quality_check_one_round_sample_collect_num` | INT | Dies ist die minimale Anzahl der pro Stichprobenerfassungsrunde zu erfassenden neuen Datenstichproben. Der Standardwert ist `10`. Nur verwendet, wenn `enable_recalibration=True`.|
| `calibration_quality_check_queue_max_size` | INT | Dies ist die maximale Anzahl der zu speichernden Datenstichproben, wenn das Kameramodell kalibriert wird. Der Standardwert ist `1000`. Nur verwendet, wenn `enable_recalibration=True`.|
| `recalibration_score` | INT | Dies ist der Schwellenwert für die maximale Qualität, bei der eine Neukalibrierung erfolgt. Der Standardwert ist `75`. Nur verwendet, wenn `enable_recalibration=True`. Die Qualität der Kalibrierung wird basierend auf einer umgekehrten Beziehung mit den Fehlern bei Neuprojektionen von Bildzielen berechnet. Bei erkannten Zielen in 2D-Bildframes werden die Ziele in den 3D-Raum projiziert und mithilfe vorhandener Kamerakalibrierungsparameter wieder zurück in den 2D-Bildframe projiziert. Fehler bei der Neuprojektion werden anhand der durchschnittlichen Abstände zwischen den erkannten Zielen und den neu projizierten Zielen gemessen.|
| `enable_breakpad`| bool | Hiermit kann angegeben werden, ob Sie Breakpad aktivieren möchten, womit Speicherabbilder für die Debugverwendung generiert werden. Die Standardeinstellung ist `false`. Wenn Sie diese Einstellung auf `true` festlegen, müssen Sie im `HostConfig`-Teil des `createOptions`-Containers auch `"CapAdd": ["SYS_PTRACE"]` hinzufügen. Das Speicherabbild wird standardmäßig in die AppCenter-App [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) hochgeladen. Wenn Sie möchten, dass die Speicherabbilder in Ihre eigene AppCenter-App hochgeladen werden, können Sie die Umgebungsvariable `RTPT_APPCENTER_APP_SECRET` mit dem App-Geheimnis Ihrer App außer Kraft setzen.


### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-personcount

 Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
    }]
}
```

| Name | type| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für die Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt und gezählt werden. Die Punkte des Polygons basieren auf normalisierten Koordinaten (0 - 1), wobei die obere Linke Ecke die Werte (0,0, 0,0) und die untere rechte Ecke die Werte (1,0, 1,0) aufweist.   
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcount** sollte dies `count` lauten.|
| `trigger` | Zeichenfolge| Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind `event` zum Senden von Ereignissen, wenn sich die Anzahl ändert, oder `interval` zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.
| `output_frequency` | INT | Die Rate, mit der Ereignisse ausgegeben werden. Bei `output_frequency` = X wird jedes x-te Ereignis ausgegeben. Beispiel: `output_frequency` = 2 bedeutet, dass jedes zweite Ereignis ausgegeben wird. `output_frequency` gilt sowohl für `event` als auch für `interval`. |
| `focus` | Zeichenfolge| Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann `footprint` (Fußabdruck der Person), `bottom_center` (untere Mitte des Begrenzungsrahmens der Person) oder `center` (Mitte des Begrenzungsrahmens der Person) sein.|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Linienkonfiguration für cognitiveservices.vision.spatialanalysis-personcrossingline

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Linie konfiguriert wird. Sie können für diesen Vorgang mehrere Linien für die Überschreitung konfigurieren.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Name | type| BESCHREIBUNG|
|---------|---------|---------|
| `lines` | list| Die Liste mit den Linien.|
| `name` | Zeichenfolge| Der Anzeigename für diese Linie.|
| `line` | list| Die Definition der Linie. Dies ist eine direktionale Linie zum Festlegen von „Betreten“ bzw. „Verlassen“.|
| `start` | Wertpaar| x/y-Koordinaten für den Startpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. |
| `end` | Wertpaar| x/y-Koordinaten für den Endpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. |
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. Der Standardwert ist 16. Dies ist der empfohlene Wert, um eine maximale Genauigkeit zu erzielen. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcrossingline** sollte dies `linecrossing` lauten.|
|`trigger`|Zeichenfolge|Der Typ des Triggers für das Senden eines Ereignisses.<br>Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Linie überschreitet.|
| `focus` | Zeichenfolge| Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann `footprint` (Fußabdruck der Person), `bottom_center` (untere Mitte des Begrenzungsrahmens der Person) oder `center` (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Name | type| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt oder gezählt werden. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. 
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. Der Standardwert ist 48, wenn der Typ „zonecrossing“ und 16, wenn die Zeit DwellTime ist. Dies sind die empfohlenen Werte, um eine maximale Genauigkeit zu erzielen.  |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** sollte dies `zonecrossing` oder `zonedwelltime` lauten.|
| `trigger`|Zeichenfolge|Der Typ des Triggers für das Senden eines Ereignisses.<br>Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Zone betritt oder verlässt.|
| `focus` | Zeichenfolge| Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann `footprint` (Fußabdruck der Person), `bottom_center` (untere Mitte des Begrenzungsrahmens der Person) oder `center` (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zonenkonfiguration für cognitiveservices.vision.spatialanalysis-persondistance

Dies ist ein Beispiel für eine JSON-Eingabe für den SPACEANALYTICS_CONFIG-Parameter, mit dem eine Zone für **cognitiveservices.vision.spatialanalysis-persondistance** konfiguriert wird. Sie können mehrere Zonen für diesen Vorgang konfigurieren.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
    "type": "persondistance",
    "config":{
        "trigger": "event",
        "output_frequency":1,
        "minimum_distance_threshold":6.0,
        "maximum_distance_threshold":35.0,
           "threshold": 16.00,
           "focus": "footprint"
            }
    }]
   }]
}
```

| Name | type| BESCHREIBUNG|
|---------|---------|---------|
| `zones` | list| Die Liste mit den Zonen. |
| `name` | Zeichenfolge| Der Anzeigename für diese Zone.|
| `polygon` | list| Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen gezählt werden und der Abstand zwischen den Personen gemessen wird. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. 
| `threshold` | float| Ereignisse werden ausgegeben, wenn der Vertrauenswert der KI-Modelle größer oder gleich diesem Wert ist. |
| `type` | Zeichenfolge| Für **cognitiveservices.vision.spatialanalysis-persondistance** sollte dies `people_distance` lauten.|
| `trigger` | Zeichenfolge| Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind `event` zum Senden von Ereignissen, wenn sich die Anzahl ändert, oder `interval` zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.
| `output_frequency` | INT | Die Rate, mit der Ereignisse ausgegeben werden. Bei `output_frequency` = X wird jedes x-te Ereignis ausgegeben. Beispiel: `output_frequency` = 2 bedeutet, dass jedes zweite Ereignis ausgegeben wird. `output_frequency` gilt sowohl für `event` als auch für `interval`.|
| `minimum_distance_threshold` | float| Ein Abstand in Fuß, bei dem das Ereignis „TooClose“ ausgelöst wird, wenn Personen weniger als den angegebenen Abstand voneinander aufweisen.|
| `maximum_distance_threshold` | float| Ein Abstand in Fuß, bei dem das Ereignis „TooFar“ ausgelöst wird, wenn Personen mehr als den angegebenen Abstand voneinander aufweisen.|
| `focus` | Zeichenfolge| Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann `footprint` (Fußabdruck der Person), `bottom_center` (untere Mitte des Begrenzungsrahmens der Person) oder `center` (Mitte des Begrenzungsrahmens der Person) sein.|

Weitere Informationen zu Zonen- und Linienkonfigurationen finden Sie im Artikel zur [Kameraplatzierung](spatial-analysis-camera-placement.md).

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
            "status": "Calibrated",
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
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
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
            "metadata":{
            "attributes": {
                "face_noMask": 0.99
            }
            }
    }
    ],
    "schemaVersion": "1.0"
}
```

| Ereignisfeldname | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `zone` | Zeichenfolge | „Name“ des Polygonfelds, das für die durchquerte Zone steht|
| `trigger` | Zeichenfolge| Der Triggertyp lautet je nach Wert von `trigger` in SPACEANALYTICS_CONFIG entweder „event“ (Ereignis) oder „interval“ (Intervall).|

| Name des Erkennungsfelds | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|
| `face_Mask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person eine Gesichtsmaske trägt. |
| `face_noMask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person **keine** Gesichtsmaske trägt. |

| Name des Felds „SourceInfo“ | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|
| `cameraCallibrationInfo` | collection | Sammlung mit Werten|
| `status` | Zeichenfolge | Dies ist der Status der Kalibrierung im Format `state[;progress description]`. Der Status kann `Calibrating`, `Recalibrating` (bei aktivierter Neukalibrierung) oder `Calibrated` sein. Der Teil für die Beschreibung des Fortschritts ist nur gültig, wenn der Status `Calibrating` und der Status `Recalibrating` vorliegt, da hiermit der Fortschritt des aktuellen Kalibrierungsprozesses angezeigt wird.|
| `cameraHeight` | float | Die Höhe der Kamera über dem Boden in Fuß. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `focalLength` | float | Die Brennweite der Kamera in Pixel. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `tiltUpAngle` | float | Der Neigungswinkel der Kamera gegenüber der Vertikalen. Dieser Wert wird von der automatischen Kalibrierung abgeleitet.|

| Name des Felds „SourceInfo“ | Typ| BESCHREIBUNG|
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
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Ereignisfeldname | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Ereignistyp|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `status` | Zeichenfolge| Richtung der Linienüberschreitung, entweder „CrossLeft“ oder „CrossRight“|
| `zone` | Zeichenfolge | Feld „Name“ der überschrittenen Linie|

| Name des Erkennungsfelds | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|
| `face_Mask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person eine Gesichtsmaske trägt. |
| `face_noMask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person **keine** Gesichtsmaske trägt. |

| Name des Felds „SourceInfo“ | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|


> [!IMPORTANT]
> Das KI-Modell erkennt eine Person unabhängig davon, ob ihr Gesicht zur Kamera oder in die andere Richtung weist. Das KI-Modell führt keine Gesichtserkennung durch und gibt keine biometrischen Informationen aus. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-Format für cognitiveservices.vision.spatialanalysis-personcrossingpolygon: KI Insights

JSON-Beispiel für Erkennungen, die von diesem Vorgang mit dem `zonecrossing`-Typ SPACEANALYTICS_CONFIG ausgegeben werden:

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
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
        "metadata": {
        "attributes": {
        "face_Mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

JSON-Beispiel für Erkennungen, die von diesem Vorgang mit dem `zonedwelltime`-Typ SPACEANALYTICS_CONFIG ausgegeben werden:

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
        "durationMs": 7132.0
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

| Ereignisfeldname | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Ereignis-ID|
| `type` | Zeichenfolge| Der Ereignistyp. Der Wert kann entweder _personZoneDwellTimeEvent_ oder _personZoneEnterExitEvent_ sein.|
| `detectionsId` | array| Array mit der Größe 1 des eindeutigen Bezeichners der Personenerkennung, für die dieses Ereignis ausgelöst wurde|
| `properties` | collection| Sammlung mit Werten|
| `trackinId` | Zeichenfolge| Eindeutiger Bezeichner der erkannten Person|
| `status` | Zeichenfolge| Richtung von Polygondurchquerungen, entweder „Enter“ oder „Exit“|
| `side` | INT| Dies ist die Nummer der Polygonseite, die von der Person überschritten wurde. Jede Seite ist eine nummerierte Kante zwischen den beiden Eckpunkten des Polygons, das Ihre Zone darstellt. Die Kante zwischen den ersten beiden Eckpunkten des Polygons stellt die erste Seite dar.|
| `durationMs` | float | Dies ist die Zeit in Millisekunden, die die Person in der Zone verbracht hat. Dieses Feld wird bereitgestellt, wenn der Ereignistyp _personZoneDwellTimeEvent_ ist.|
| `zone` | Zeichenfolge | „Name“ des Polygonfelds, das für die durchquerte Zone steht|

| Name des Erkennungsfelds | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|
| `face_Mask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person eine Gesichtsmaske trägt. |
| `face_noMask` | float | Der Attributkonfidenzwert mit dem Bereich (0-1) gibt an, dass die erkannte Person **keine** Gesichtsmaske trägt. |

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
            "status": "Calibrated",
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

| Ereignisfeldname | Typ| BESCHREIBUNG|
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

| Name des Erkennungsfelds | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Erkennungs-ID|
| `type` | Zeichenfolge| Erkennungstyp|
| `region` | collection| Sammlung mit Werten|
| `type` | Zeichenfolge| Typ der Region|
| `points` | collection| Oberer linker und unterer rechter Punkt, wenn der Regionstyp RECTANGLE (RECHTECK) lautet. |
| `confidence` | float| Algorithmusvertrauen|
| `centerGroundPoint` | 2 Gleitkommawerte| `x`/`y`-Werte mit den Koordinaten der abgeleiteten Position der Person auf dem Boden in Fuß. `x` und `y` sind Koordinaten auf der Bodenebene, vorausgesetzt, der Boden ist eben. Die Position der Kamera ist der Ausgangspunkt. |

Beim Berechnen von `centerGroundPoint` ist `x` der Abstand zwischen der Kamera und der Person entlang einer Linie, die senkrecht zur Kamerabildebene liegt. `y` ist der Abstand zwischen der Kamera und der Person entlang einer Linie, die parallel zur Kamerabildebene liegt. 

![Beispiel für den unteren Mittelpunkt](./media/spatial-analysis/x-y-chart.png) 

In diesem Beispiel hat `centerGroundPoint` den Wert `{x: 4, y: 5}`. Dies bedeutet, dass der Raum in der Vogelperspektive von einer Kamera überwacht wird und sich eine Person an einem Punkt im Raum befindet, der vier Fuß vor und fünf Fuß rechts von der Kamera liegt.


| Name des Felds „SourceInfo“ | Typ| BESCHREIBUNG|
|---------|---------|---------|
| `id` | Zeichenfolge| Camera ID (Kamera-ID)|
| `timestamp` | date| UTC-Datum, an dem die JSON-Nutzlast ausgegeben wurde|
| `width` | INT | Videoframebreite|
| `height` | INT | Videoframehöhe|
| `frameId` | INT | Framebezeichner|
| `cameraCallibrationInfo` | collection | Sammlung mit Werten|
| `status` | Zeichenfolge | Dies ist der Status der Kalibrierung im Format `state[;progress description]`. Der Status kann `Calibrating`, `Recalibrating` (bei aktivierter Neukalibrierung) oder `Calibrated` sein. Der Teil für die Beschreibung des Fortschritts ist nur gültig, wenn der Status `Calibrating` und der Status `Recalibrating` vorliegt, da hiermit der Fortschritt des aktuellen Kalibrierungsprozesses angezeigt wird.|
| `cameraHeight` | float | Die Höhe der Kamera über dem Boden in Fuß. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `focalLength` | float | Die Brennweite der Kamera in Pixel. Dieser Wert wird von der automatischen Kalibrierung abgeleitet. |
| `tiltUpAngle` | float | Der Neigungswinkel der Kamera gegenüber der Vertikalen. Dieser Wert wird von der automatischen Kalibrierung abgeleitet.|


## <a name="use-the-output-generated-by-the-container"></a>Verwenden der vom Container generierten Ausgabe

Es kann sein, dass Sie die Erkennung über die räumliche Analyse oder zugehörige Ereignisse in Ihre Anwendung integrieren möchten. Hier sind einige Punkte angegeben, die Sie beachten sollten: 

* Verwenden Sie das Azure Event Hub-SDK für Ihre gewählte Programmiersprache, um eine Verbindung mit dem Azure IoT Hub-Endpunkt herstellen und die Ereignisse empfangen zu können. Weitere Informationen finden Sie unter [Lesen von Nachrichten, die von Geräten an die Cloud gesendet werden, vom integrierten Endpunkt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). 
* Richten Sie das **Nachrichtenrouting** auf Ihrer Azure IoT Hub-Instanz ein, um Ereignisse an andere Endpunkte zu senden oder die Ereignisse in Ihrem Datenspeicher zu speichern. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../../iot-hub/iot-hub-devguide-messages-d2c.md). 
* Richten Sie einen Azure Stream Analytics-Auftrag ein, um die Ereignisse bei ihrem Eintreffen in Echtzeit zu verarbeiten und Visualisierungen zu erstellen. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Bereitstellung von Vorgängen zur räumlichen Analyse im großen Stil (mehrere Kameras)

Um die beste Leistung und Auslastung der GPUs zu erzielen, können Sie beliebige Vorgänge zur räumlichen Analyse mit mehreren Kameras mithilfe von Graphinstanzen bereitstellen. Im Folgenden finden Sie ein Beispiel für die Ausführung des `cognitiveservices.vision.spatialanalysis-personcrossingline`-Vorgangs für 15 Kameras.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
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
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Name | type| Beschreibung|
|---------|---------|---------|
| `batch_size` | INT | Wenn alle Kameras die gleiche Auflösung haben, legen Sie `batch_size` auf die Anzahl der Kameras fest, die in diesem Vorgang verwendet werden. Andernfalls legen Sie `batch_size` auf 1 fest oder belassen es beim Standardwert (1), was bedeutet, dass kein Batch unterstützt wird. |

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Webanwendung für die Erfassung der Personenanzahl](spatial-analysis-web-app.md)
* [Protokollierung und Problembehandlung](spatial-analysis-logging.md)
* [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md)
* [Leitfaden zur Platzierung von Zonen und Linien](spatial-analysis-zone-line-placement.md)
