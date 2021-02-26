---
ms.openlocfilehash: 7b9ed5dda925793eff4f85408c5eb494f5ac0925
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569588"
---
### <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

Als Teil der Voraussetzungen haben Sie den Beispielcode in einen Ordner heruntergeladen. Führen Sie die unten angegebenen Schritte aus, um die Beispieldateien zu überprüfen und zu bearbeiten.

1. Navigieren Sie in Visual Studio Code zu *src/edge*. Ihre *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.

    Die Bereitstellungsvorlage verweist auf das Bereitstellungsmanifest für das Edge-Gerät. Es enthält einige Platzhalterwerte. Die *ENV*-Datei enthält die Werte für diese Variablen.

1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin finden Sie Ihre Datei *appsettings.json* und einige andere Dateien:

    * operations.json: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    * main.py: Der Beispielprogrammcode. Mit diesem Code wird Folgendes durchgeführt:

        * Laden der App-Einstellungen
        * Aufrufen direkter Methoden, die vom Modul „Live Video Analytics in IoT Edge“ verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen direkten Methoden aufrufen.
        * Anhalten der Ausführung, sodass Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
        * Aufrufen direkter Methoden zur Bereinigung der Ressourcen  
1. Bearbeiten Sie die Datei *operations.json*:
    * Ändern Sie den Link zur Graphtopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * Bearbeiten Sie unter `GraphInstanceSet` den Namen der Graphtopologie, damit er mit dem Wert im vorherigen Link übereinstimmt:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Ändern Sie unter `GraphTopologyDelete` den Namen:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests

1. Klicken Sie mit der rechten Maustaste auf die Datei *src/edge/deployment.yolov3.template.json*, und wählen Sie dann **IoT Edge-Bereitstellungsmanifest generieren** aus.

    ![Generate IoT Edge Deployment Manifest (IoT Edge-Bereitstellungsmanifest generieren)](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Die Manifestdatei *deployment.yolov3.amd64.json* wird im Ordner *src/edge/config* erstellt.

1. Überspringen Sie diesen Schritt, wenn Sie die Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](../../../detect-motion-emit-events-quickstart.md) abgeschlossen haben. 

    Wählen Sie andernfalls in der Nähe des Bereichs **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** und dann **Festlegen der IoT Hub-Verbindungszeichenfolge** aus. Sie können die Zeichenfolge aus der Datei *appsettings.json* kopieren. Um sicherzustellen, dass Sie den richtigen IoT Hub in Visual Studio Code konfiguriert haben, verwenden Sie alternativ den Befehl [IoT Hub auswählen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Festlegen der IoT Hub-Verbindungszeichenfolge](../../../media/quickstarts/set-iotconnection-string.png)

    > [!NOTE]
    > Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/ deployment.yolov3.amd64.json*, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus. 

    ![Create Deployment for Single Device (Bereitstellung für einzelnes Gerät erstellen)](../../../media/quickstarts/create-deployment-single-device.png)

1. Wenn Sie aufgefordert werden, ein IoT Hub-Gerät auszuwählen, wählen Sie **lva-sample-device** aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

    * Live Video Analytics-Modul mit dem Namen **lvaEdge**
    * Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert
        > [!NOTE]
        > In den obigen Schritten wird vorausgesetzt, dass Sie den mit dem Setupskript erstellten virtuellen Computer verwenden. Gehen Sie wie folgt vor, falls Sie stattdessen Ihr eigenes Edgegerät verwenden: Navigieren Sie zum Gerät, und führen Sie die folgenden Befehle mit **Administratorrechten** aus, um die für diese Schnellstartanleitung verwendete Beispielvideodatei zu pullen und zu speichern:  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * Das Modul **yolov3**, bei dem es sich um das YOLOv3-Objekterkennungsmodell handelt, das maschinelles Sehen auf die Bilder anwendet und mehrere Klassen von Objekttypen zurückgibt.
 
      ![Module, die im Edge-Gerät bereitgestellt sind](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Klicken Sie mit der rechten Maustaste auf das Live Video Analytics-Gerät, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus. Sie müssen diesen Schritt ausführen, um die IoT Hub-Ereignisse im **Ausgabefenster** von Visual Studio Code zu überwachen. 

   ![Starten der Überwachung](../../../media/quickstarts/start-monitoring-iothub-events.png) 

> [!NOTE]
> Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
### <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von *operations.json* beginnt mit Aufrufen der direkten Methoden `GraphTopologyList` und `GraphInstanceList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.

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

    Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

     * Ein Aufruf von `GraphTopologySet`, der die vorherige `topologyUrl` verwendet
     * Ein Aufruf von `GraphInstanceSet`, der den folgenden Textkörper verwendet:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
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

     * Ein Aufruf von `GraphInstanceActivate`, der die Graphinstanz und den Videodatenfluss startet
     * Zweiter Aufruf von `GraphInstanceList` mit der Anzeige, dass sich die Graphinstanz im ausgeführten Zustand befindet
1. Die Ausgabe im **Terminalfenster** wird mit der Aufforderung `Press Enter to continue` angehalten. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Meldungen angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
1. Der Mediengraph wird weiter ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden des Mediengraphs zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE. 

    Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
      * Mit dem Aufruf von `GraphInstanceDeactivate` wird die Graphinstanz deaktiviert.
      * Mit dem Aufruf von `GraphInstanceDelete` wird die Instanz gelöscht.
      * Mit dem Aufruf von `GraphTopologyDelete` wird die Topologie gelöscht.
      * Ein abschließender Aufruf von `GraphTopologyList` zeigt, dass die Liste leer ist.
