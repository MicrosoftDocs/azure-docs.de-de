---
title: Live Video Analytics-Upgrade auf IoT Edge von 1.0 auf 2.0
description: In diesem Artikel werden die Unterschiede und die unterschiedlichen Aspekte beschrieben, die bei der Aktualisierung von Live Video Analytics (LVA) auf dem Azure IoT Edge-Modul zu beachten sind.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955648"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Live Video Analytics-Upgrade auf IoT Edge von 1.0 auf 2.0

In diesem Artikel werden die Unterschiede und die unterschiedlichen Aspekte beschrieben, die bei der Aktualisierung von Live Video Analytics (LVA) auf dem Azure IoT Edge-Modul zu beachten sind.

## <a name="change-list"></a>Liste der Änderungen

> [!div class="mx-tdCol4BreakAll"]
> |Titel|Live Video Analytics 1.0|Live Video Analytics 2.0|Beschreibung|
> |-------------|----------|---------|---------|
> |Containerimage|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Von Microsoft veröffentlichte Docker-Images für Live Video Analytics auf Azure IoT Edge|
> |**MediaGraph-Knoten** |    |   |   |
> |Quellen|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-Quelle </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub-Nachrichtenquelle |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-Quelle </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub-Nachrichtenquelle | MediaGraph-Knoten, die als Quellen für Medienerfassung und Nachrichten fungieren.|
> |Prozessoren|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Verarbeitungsknoten für die Bewegungserkennung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Verarbeitungsknoten für Bildfrequenzfilter </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Verarbeitungsknoten für die HTTP-Erweiterung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC-Erweiterungsprozessor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signalgateprozessor |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Verarbeitungsknoten für die Bewegungserkennung </br>:::image type="icon" source="./././media/upgrading-lva/remove.png"::: **Verarbeitungsknoten für Bildfrequenzfilter**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Verarbeitungsknoten für die HTTP-Erweiterung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC-Erweiterungsprozessor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signalgateprozessor | MediaGraph-Knoten, die Ihnen ermöglichen, die Medien vor dem Senden an KI-Rückschlussserver zu formatieren.|
> |Senken|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Medienobjektsenke </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dateisenke </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub-Nachrichtensenke|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Medienobjektsenke </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dateisenke </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub-Nachrichtensenke| MediaGraph-Knoten, die Ihnen ermöglichen, die verarbeiteten Medien zu speichern.|
> |**Unterstützte MediaGraph-Typen** |    |   |   |
> |Topologien|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse und fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externe Analyse und fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei Bewegung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei KI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei externem Ereignis </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse gefolgt von KI-Rückschluss |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse und fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externe Analyse und fortlaufende Videoaufzeichnung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei Bewegung </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei KI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ereignisbasierte Aufzeichnung bei externem Ereignis </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegungsanalyse gefolgt von KI-Rückschluss </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **KI-Komposition** </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **Audio- und Videoaufzeichnung** </br>  | MediaGraph-Topologien, mit denen Sie die Blaupause für einen Graph mit Parametern als Platzhalter für Werte definieren können.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Aktualisieren von Live Video Analytics auf dem IoT Edge-Modul von 1.0 auf 2.0
Stellen Sie beim Aktualisieren von Live Video Analytics auf dem IoT Edge-Modul sicher, dass Sie die folgenden Informationen aktualisieren.
### <a name="container-image"></a>Containerimage
Suchen Sie in Ihrer Bereitstellungsvorlage unter dem Knoten `modules` nach ihrer Live Video Analytics-Instanz auf dem IoT Edge-Modul, und aktualisieren Sie das `image`-Feld wie folgt:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Wenn Sie den Namen der Live Video Analytics-Instanz auf dem IoT Edge-Modul nicht geändert haben, suchen Sie unter dem Modulknoten nach `lvaEdge`.

### <a name="topology-file-changes"></a>Änderungen der Topologiedatei
Stellen Sie in den Topologiedateien sicher, dass **`apiVersion`** auf 2.0 festgelegt ist.

### <a name="mediagraph-node-changes"></a>Änderungen des MediaGraph-Knotens


* Die Audiodaten aus der Kameraquelle können nun zusammen mit den Videodaten downstream weitergegeben werden. Sie können **reine Audiodaten** oder **reine Videodaten** oder **Audio- und Videodaten** mit **`outputSelectors`** an einen beliebigen MediaGraph-Knoten weitergeben. Wenn Sie z. B. nur Videodaten aus der RTSP-Quelle auswählen und downstream übergeben möchten, fügen Sie dem RTSP-Quellknoten Folgendes hinzu:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** ist eine optionale Eigenschaft. Wenn sie nicht verwendet wird, gibt MediaGraph die Audiodaten (sofern aktiviert) und die Videodaten von der RTSP-Kamera downstream weiter. 

* Beachten Sie in den Prozessoren `MediaGraphHttpExtension` und `MediaGraphGrpcExtension` die folgenden Änderungen:  
    #### <a name="image-properties"></a>Image-Eigenschaften
    * `MediaGraphImageFormatEncoded` wird nicht mehr unterstützt. 
      * Verwenden Sie stattdessen **`MediaGraphImageFormatBmp`** oder **`MediaGraphImageFormatJpeg`** oder **`MediaGraphImageFormatPng`** . Beispiel:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Wenn Sie RAW-Bilder verwenden möchten, verwenden Sie **`MediaGraphImageFormatRaw`** . Um diese zu verwenden, aktualisieren Sie den Bildknoten wie folgt:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Folgende pixelFormat-Werte sind möglich: `yuv420p`,`rgb565be`, `rgb565le`, `rgb555be`, `rgb555le`, `rgb24`, `bgr24`, `argb`, `rgba`, `abgr`, `bgra`.  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensionConfiguration für den gRPC-Erweiterungsprozessor  
    * In einem `MediaGraphGrpcExtension`-Prozessor ist eine neue Eigenschaft namens **`extensionConfiguration`** verfügbar, eine optionale Zeichenfolge, die als Teil des gRPC-Vertrags verwendet werden kann. Dieses Feld kann verwendet werden, um beliebige Daten an den Rückschlussserver weiterzugeben, und Sie können definieren, wie der Rückschlussserver diese Daten verwendet.  
    Ein Anwendungsfall dieser Eigenschaft liegt vor, wenn Sie mehrere KI-Modelle in einen einzelnen Rückschlussserver gepackt haben. Mit dieser Eigenschaft müssen Sie nicht für jedes KI-Modell einen Knoten verfügbar machen. Stattdessen können Sie als Erweiterungsanbieter für eine Diagramminstanz definieren, wie die verschiedenen KI-Modelle mithilfe der **`extensionConfiguration`** -Eigenschaft ausgewählt werden, und während der Ausführung gibt LVA diese Zeichenfolge an den Rückschlussserver weiter, der mit ihr das gewünschte KI-Modell aufrufen kann.  

    #### <a name="ai-composition"></a>KI-Komposition
    * Live Video Analytics 2.0 unterstützt jetzt die Verwendung mehrerer MediaGraph-Erweiterungsprozessoren innerhalb einer Topologie. Sie können die Medienframes von der RTSP-Kamera sequenziell, parallel oder in einer Kombination aus beidem an verschiedene KI-Modelle weitergeben. Hier sehen Sie eine Beispieltopologie mit zwei sequenziell verwendeten KI-Modellen.

### <a name="disk-space-management-with-sink-nodes"></a>Speicherplatzverwaltung mit Senkeknoten
* In Ihrem **Dateisenke**-Knoten können Sie jetzt angeben, wieviel Speicherplatz auf dem Datenträger die Live Video Analytics-Instanz auf dem IoT Edge-Modul zum Speichern der verarbeiteten Bilder verwenden kann. Fügen Sie zu diesem Zweck dem Dateisenke-Knoten das Feld **`maximumSizeMiB`** hinzu. Ein Beispiel für einen Dateisenke-Knoten sieht wie folgt aus:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* In Ihrem **Medienobjektsenke**-Knoten können Sie angeben, wieviel Speicherplatz auf dem Datenträger die Live Video Analytics-Instanz auf dem IoT Edge-Modul zum Speichern der verarbeiteten Bilder verwenden kann. Fügen Sie zu diesem Zweck dem Medienobjektsenke-Knoten das Feld **`localMediaCacheMaximumSizeMiB`** hinzu. Ein Beispiel für einen Medienobjektsenke-Knoten sieht wie folgt aus:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  Der **Dateisenke**-Pfad ist in den Basisverzeichnispfad und das Dateinamenmuster aufgeteilt, während der **Medienobjektsenke**-Pfad den Basisverzeichnispfad enthält.  

### <a name="frame-rate-management"></a>Bildfrequenzverwaltung
* **`MediaGraphFrameRateFilterProcessor`** ist in **Live Video Analytics 2.0 auf dem IoT Edge-Modul** veraltet.
    * Um Stichproben der eingehenden Videodaten zur Verarbeitung zu entnehmen, fügen Sie die **`samplingOptions`** -Eigenschaft den MediaGraph-Erweiterungsprozessoren (`MediaGraphHttpExtension` oder `MediaGraphGrpcExtension`) hinzu.  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Modulmetriken im Prometheus-Format mit Telegraf
Mit diesem Release kann Telegraf zum Senden von Metriken an Azure Monitor verwendet werden. Von dort aus können die Metriken an Log Analytics oder einen Event Hub weitergeleitet werden.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taxonomie von Ereignissen":::

Sie können ein Telegraf-Image mit einer benutzerdefinierten Konfiguration problemlos mithilfe von Docker entwickeln. Weitere Informationen finden Sie auf der Seite [Überwachung und Protokollierung](monitoring-logging.md#azure-monitor-collection-via-telegraf).

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erste Schritte: Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)