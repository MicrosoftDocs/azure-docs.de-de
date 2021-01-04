---
title: 'Tutorial: Bereitstellen einer Custom Vision-Klassifizierung auf einem Gerät mithilfe von Azure IoT Edge'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Custom Vision und IoT Edge ein als Container ausgeführtes Modell für maschinelles Sehen erstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 564f19a1be5b3ce8a5267a07bd4f1f6de80fecf1
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621279"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Tutorial: Durchführen von Bildklassifizierungen im Edge-Bereich mit dem Custom Vision Service

Azure IoT Edge kann Workloads aus der Cloud an den Edge-Bereich verlagern und so die Effizienz Ihrer IoT-Lösung steigern. Diese Funktion eignet sich besonders für Dienste, die große Datenmengen verarbeiten – also beispielsweise für Modelle für maschinelles Sehen. Mit dem [Custom Vision Service](../cognitive-services/custom-vision-service/overview.md) können Sie benutzerdefinierte Bildklassifizierungen erstellen und auf Geräten als Container bereitstellen. Die Kombination dieser beiden Dienste ermöglicht es Ihnen, Erkenntnisse aus Bildern oder Videostreams zu gewinnen, ohne zuvor sämtliche Daten an einen externen Ort zu übertragen. Custom Vision bietet eine Klassifizierung, die ein Bild mit einem trainierten Modell vergleicht, um Erkenntnisse zu generieren.

So kann mit Custom Vision auf einem IoT Edge-Gerät beispielsweise ermittelt werden, ob das Verkehrsaufkommen auf einer Autobahn höher ist als normal oder ob in einem Parkhaus noch Parkplätze in einer Reihe frei sind. Diese Erkenntnisse können an einen anderen Dienst weitergegeben werden, um Aktionen auszuführen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer Bildklassifizierung mit Custom Vision
> * Entwickeln eines IoT Edge-Moduls, das den Custom Vision-Webserver auf Ihrem Gerät abfragt
> * Senden der Ergebnisse der Bildklassifizierung an IoT Hub

<center>

![Architekturdiagramm des Tutorials: Staging und Bereitstellung der Klassifizierung](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

>[!TIP]
>Bei diesem Tutorial handelt es sich um eine vereinfachte Version des Beispielprojekts [Custom Vision + Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (Custom Vision und Azure IoT Edge auf einem Raspberry Pi 3). Das Tutorial wurde für die Ausführung auf einem virtuellen Cloudcomputer konzipiert und verwendet statische Bilder zum Trainieren und Testen der Bildklassifizierung. Dies ist hilfreich für Benutzer, die ihre ersten Erfahrungen mit Custom Vision in IoT Edge sammeln. Das Beispielprojekt verwendet physische Hardware und richtet einen Livekamerafeed zum Trainieren und Testen der Bildklassifizierung ein. Dies ist hilfreich für Benutzer, die sich an einem detaillierteren, praxisnahen Szenario versuchen möchten.

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorherige Tutorial absolviert haben, um Ihre Umgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Um ein IoT Edge-Modul mit dem Custom Vision-Dienst zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer:

* [Python](https://www.python.org/downloads/)
* [Git-Client](https://git-scm.com/downloads)
* [Python-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Erstellen einer Bildklassifizierung mit Custom Vision

Um eine Bildklassifizierung zu erstellen, müssen Sie ein Custom Vision-Projekt erstellen und Trainingsbilder bereitstellen. Weitere Informationen zu den Schritten in diesem Abschnitt finden Sie unter [Erstellen einer Klassifizierung mit Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Nachdem Sie Ihre Bildklassifizierung erstellt und trainiert haben, können Sie sie als Docker-Container exportieren und auf einem IoT Edge-Gerät bereitstellen.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

1. Navigieren Sie im Webbrowser zur [Custom Vision-Webseite](https://customvision.ai/).

2. Wählen Sie **Anmelden** aus, und melden Sie sich mit dem Konto an, mit dem Sie auch auf Azure-Ressourcen zugreifen.

3. Wählen Sie **Neues Projekt** aus.

4. Erstellen Sie Ihr Projekt mit den folgenden Werten:

   | Feld | Wert |
   | ----- | ----- |
   | Name | Geben Sie einen Namen für Ihr Projekt an (beispielsweise **EdgeTreeClassifier**). |
   | BESCHREIBUNG | Eine optionale Projektbeschreibung. |
   | Resource | Wählen Sie eine Ihrer Azure-Ressourcengruppen aus, die eine Custom Vision-Dienstressource enthält, oder **erstellen Sie eine neue**, wenn Sie noch keine hinzugefügt haben. |
   | Projekttypen | **Klassifizierung** |
   | Klassifizierungstypen | **Multiclass (single tag per image)** (Multiklasse (einzelnes Tag pro Bild)) |
   | Domänen | **General (compact)** (Allgemein (kompakt)) |
   | Exportieren von Funktionen | **Basisplattformen (Tensorflow, CoreML, ONNX...)** |

5. Wählen Sie **Projekt erstellen** aus.

### <a name="upload-images-and-train-your-classifier"></a>Hochladen von Bildern und Trainieren Ihrer Klassifizierung

Für die Erstellung einer Bildklassifizierung benötigen Sie eine Gruppe von Trainingsbildern sowie Testbilder.

1. Klonen Sie Beispielbilder aus dem Repository [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) auf Ihrem lokalen Entwicklungscomputer, oder laden Sie sie herunter.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Kehren Sie zu Ihrem Custom Vision-Projekt zurück, und wählen Sie **Bilder hinzufügen** aus.

3. Navigieren Sie in dem lokal geklonten Git-Repository zum ersten Bildordner: **Cognitive-CustomVision-Windows/Samples/Images/Hemlock**. Wählen Sie in dem Ordner alle zehn Bilder und anschließend **Öffnen** aus.

4. Fügen Sie dieser Gruppe von Bildern das Tag **hemlock** hinzu, und drücken Sie die **EINGABETASTE**, um das Tag anzuwenden.

5. Wählen Sie **10 Dateien hochladen** aus.

   ![Hochladen von Dateien mit dem Tag „hemlock“ in Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Klicken Sie nach dem erfolgreichen Hochladen der Bilder auf **Fertig**.

7. Wählen Sie erneut **Bilder hinzufügen** aus.

8. Navigieren Sie zum zweiten Bildordner: **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry**. Wählen Sie in dem Ordner alle zehn Bilder und anschließend **Öffnen** aus.

9. Fügen Sie dieser Gruppe von Bildern das Tag **japanese cherry** hinzu, und drücken Sie die **EINGABETASTE**, um das Tag anzuwenden.

10. Wählen Sie **10 Dateien hochladen** aus. Klicken Sie nach dem erfolgreichen Hochladen der Bilder auf **Fertig**.

11. Wenn beide Gruppen von Bildern mit Tags versehen und hochgeladen wurden, wählen Sie **Trainieren** aus, um die Klassifizierung zu trainieren.

### <a name="export-your-classifier"></a>Exportieren Ihrer Klassifizierung

1. Wählen Sie nach dem Trainieren Ihrer Klassifizierung auf der Leistungsseite der Klassifizierung die Option **Exportieren** aus.

   ![Exportieren Ihrer trainierten Bildklassifizierung](./media/tutorial-deploy-custom-vision/export.png)

2. Wählen Sie für die Plattform die Option **DockerFile** aus. 

3. Wählen Sie für die Version die Option **Linux** aus.  

4. Wählen Sie **Exportieren** aus. 

   ![Exportieren als DockerFile mit Linux-Containern](./media/tutorial-deploy-custom-vision/export-2.png)

5. Wählen Sie nach Abschluss des Exportvorgangs **Herunterladen** aus, und speichern Sie das ZIP-Paket lokal auf Ihrem Computer. Extrahieren Sie alle Dateien aus dem Paket. Diese Dateien werden verwendet, um ein IoT Edge-Modul zu erstellen, das den Bildklassifizierungsserver enthält. 

Sie haben Ihr Custom Vision-Projekt nun erstellt und trainiert. Die exportierten Dateien werden im nächsten Abschnitt verwendet. Die Arbeit an der Custom Vision-Webseite ist jedoch abgeschlossen. 

## <a name="create-an-iot-edge-solution"></a>Erstellen einer IoT Edge-Projektmappe

Die Dateien für eine Containerversion Ihrer Bildklassifizierung befinden sich nun auf Ihrem Entwicklungscomputer. In diesem Abschnitt konfigurieren Sie den Bildklassifizierungscontainer für die Ausführung als IoT Edge-Modul. Darüber hinaus erstellen Sie ein zweites Modul, das zusammen mit der Bildklassifizierung bereitgestellt wird. Das zweite Modul richtet Anforderungen an die Klassifizierung und sendet die Ergebnisse als Nachrichten an IoT Hub. 

### <a name="create-a-new-solution"></a>Erstellen einer neuen Lösung

Eine Projektmappe ist eine logische Methode, um mehrerer Module für eine einzelne IoT Edge-Bereitstellung zu entwickeln und zu strukturieren. Sie enthält Code für mindestens ein Modul sowie das Bereitstellungsmanifest, das die Konfiguration der Module auf einem IoT Edge-Gerät deklariert. 

1. Klicken Sie in Visual Studio Code auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen. 

1. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Geben Sie in der Befehlspalette die folgenden Informationen an, um die Projektmappe zu erstellen: 

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein (beispielsweise **CustomVisionSolution**), oder übernehmen Sie den Standardnamen. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **classifier**.<br><br>Wichtig: Der Name des Moduls muss in Kleinbuchstaben angegeben werden. Bei Modulverweisen von IoT Edge wird die Groß-/Kleinschreibung berücksichtigt, und in dieser Projektmappe wird eine Bibliothek verwendet, die alle Anforderungen in Kleinbuchstaben formatiert. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Ihr Containerimage wird aus dem vorherigen Schritt übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln.<br><br>Die endgültige Zeichenfolge sieht wie folgt aus: **\<registry name\>.azurecr.io/classifier**. |
 
   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-deploy-custom-vision/repository.png)

Das Visual Studio Code-Fenster lädt den Arbeitsbereich für Ihre IoT Edge-Projektmappe.

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie im VS Code-Explorer die ENV-Datei.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. In diesem Tutorial wird die Standardoption „Linux AMD64“ verwendet. 

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus. 

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei. 

### <a name="add-your-image-classifier"></a>Hinzufügen Ihrer Bildklassifizierung

Die Python-Modulvorlage in Visual Studio Code enthält Beispielcode, den Sie zum Testen von IoT Edge ausführen können. Dieser Code wird allerdings in diesem Szenario nicht verwendet. Gehen Sie stattdessen wie in diesem Abschnitt beschrieben vor, um den Beispielcode durch den Bildklassifizierungscontainer zu ersetzen, den Sie zuvor exportiert haben. 

1. Navigieren Sie in Ihrem Datei-Explorer zu dem Custom Vision-Paket, das Sie heruntergeladen und extrahiert haben. Kopieren Sie den gesamten Inhalt aus dem extrahierten Paket. Dieser sollte zwei Ordner (**app** und **azureml**) sowie zwei Dateien (**Dockerfile** und **README**) umfassen. 

2. Navigieren Sie in Ihrem Datei-Explorer zu dem Verzeichnis, das Sie Visual Studio Code gegenüber als Zielverzeichnis für die Erstellung Ihrer IoT Edge-Projektmappe angegeben haben. 

3. Öffnen Sie den Ordner des Klassifizierungsmoduls. Sofern Sie im vorherigen Abschnitt die vorgeschlagenen Namen verwendet haben, sieht die Ordnerstruktur wie folgt aus: **CustomVisionSolution/modules/classifier**. 

4. Fügen Sie die Dateien in den Ordner **classifier** ein. 

5. Wechseln Sie wieder zum Visual Studio Code-Fenster. Im Arbeitsbereich Ihrer Projektmappe sollten nun im Modulordner die Bildklassifizierungsdateien angezeigt werden. 

   ![Arbeitsbereich der Projektmappe mit Bildklassifizierungsdateien](./media/tutorial-deploy-custom-vision/workspace.png)

6. Öffnen Sie im Ordner „classifier“ die Datei **module.json**. 

7. Aktualisieren Sie den Parameter **platform** so, dass er auf das hinzugefügte neue Dockerfile verweist, und entfernen Sie alle Optionen bis auf „AMD64“, da in diesem Tutorial nur diese Architektur verwendet wird. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Speichern Sie die Änderungen. 

### <a name="create-a-simulated-camera-module"></a>Erstellen eines simulierten Kameramoduls

Bei einer echten Custom Vision-Bereitstellung wäre eine Kamera vorhanden, die Livebilder oder Videostreams bereitstellt. In diesem Szenario simulieren Sie die Kamera, indem Sie ein Modul erstellen, das ein Testbild an die Bildklassifizierung sendet. 

#### <a name="add-and-configure-a-new-module"></a>Hinzufügen und Konfigurieren eines neuen Moduls

In diesem Abschnitt fügen Sie der gleichen Custom Vision-Projektmappe (CustomVisionSolution) ein neues Modul hinzu und stellen Code bereit, um die simulierte Kamera zu erstellen. 

1. Führen Sie im gleichen Visual Studio Code-Fenster über die Befehlspalette den Befehl **Azure IoT Edge: Add IoT Edge Module** (Azure IoT Edge: IoT Edge-Modul hinzufügen) aus. Geben Sie in der Befehlspalette die folgenden Informationen für Ihr neues Modul an: 

   | Prompt | Wert | 
   | ------ | ----- |
   | Select deployment template file (Bereitstellungsvorlagendatei auswählen) | Wählen Sie die Datei **deployment.template.json** aus dem Ordner „CustomVisionSolution“ aus. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie Ihr Modul **cameraCapture**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ersetzen Sie **localhost:5000** durch den Wert von **Anmeldeserver** für Ihre Azure-Containerregistrierung.<br><br>Die endgültige Zeichenfolge sieht wie folgt aus: **\<registryname\>.azurecr.io/cameracapture**. |

   Das VS Code-Fenster lädt Ihr neues Modul in den Arbeitsbereich der Projektmappe und aktualisiert die Datei „deployment.template.json“. Nun sollten zwei Modulordner angezeigt werden: „classifier“ und „cameraCapture“. 

2. Öffnen Sie die Datei **main.py** im Ordner **modules** / **cameraCapture**. 

3. Ersetzen Sie die gesamte Datei durch den folgenden Code. Dieser Beispielcode sendet POST-Anforderungen an den im Klassifizierungsmodul ausgeführten Bildverarbeitungsdienst. Für diesen Modulcontainer stellen wir ein Beispielbild bereit, das in den Anforderungen verwendet werden kann. Anschließend wird die Antwort als IoT Hub-Nachricht verpackt und an eine Ausgabewarteschlange gesendet.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Speichern Sie die Datei **main.py**. 

5. Öffnen Sie die Datei **requirements.txt**. 

6. Fügen Sie eine neue Zeile für eine Bibliothek hinzu, die in den Container aufgenommen werden soll.

   ```Text
   requests
   ```

7. Speichern Sie die Datei **requirements.txt** .


#### <a name="add-a-test-image-to-the-container"></a>Hinzufügen eines Testbilds zu dem Container

Wir verwenden in diesem Szenario keinen Bildfeed von einer echten Kamera, sondern ein einzelnes Testbild. Ein Testbild ist in dem GitHub-Repository enthalten, das Sie weiter oben in diesem Tutorial für die Trainingsbilder heruntergeladen haben. 

1. Navigieren Sie zu dem Testbild. Es befindet sich unter **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Kopieren Sie **test_image.jpg**. 

3. Navigieren Sie zum Verzeichnis Ihrer IoT Edge-Projektmappe, und fügen Sie das Testbild in den Ordner **modules** / **cameraCapture** ein. Das Bild muss sich im gleichen Ordner befinden wie die Datei „main.py“, die Sie im vorherigen Abschnitt bearbeitet haben. 

4. Öffnen Sie in Visual Studio Code die Datei **Dockerfile.amd64** für das cameraCapture-Modul.

5. Fügen Sie nach der Zeile, die das Arbeitsverzeichnis (`WORKDIR /app`) festlegt, die folgende Codezeile hinzu:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Speichern Sie das Dockerfile.

### <a name="prepare-a-deployment-manifest"></a>Vorbereiten eines Bereitstellungsmanifests

Sie haben in diesem Tutorial ein Custom Vision-Modell trainiert, um Bilder von Bäumen zu klassifizieren, und dieses Modell als IoT Edge-Modul verpackt. Anschließend haben Sie ein zweites Modul erstellt, das den Bildklassifizierungsserver abfragen und die Ergebnisse an IoT Hub zurückgeben kann. Als Nächstes können Sie das Bereitstellungsmanifest erstellen, das gegenüber einem IoT Edge-Gerät angibt, wie diese beiden Module gestartet und gemeinsam ausgeführt werden. 

Die IoT Edge-Erweiterung für Visual Studio Code stellt in jeder IoT Edge-Projektmappe eine Vorlage bereit, die Sie beim Erstellen eines Bereitstellungsmanifests unterstützt. 

1. Öffnen Sie die Datei **deployment.template.json** aus dem Projektmappenordner. 

2. Navigieren Sie zum Abschnitt **modules**. Er sollte drei Module enthalten: die beiden, die Sie erstellt haben (also „classifier“ und „cameraCapture“), und ein drittes (SimulatedTemperatureSensor), das standardmäßig vorhanden ist. 

3. Löschen Sie das Modul **SimulatedTemperatureSensor** zusammen mit allen dazugehörigen Parametern. Dieses Modul dient zur Bereitstellung von Beispieldaten für Testszenarien und wird in dieser Bereitstellung nicht benötigt. 

4. Sollten Sie das Bildklassifizierungsmodul nicht **classifier** genannt haben, überprüfen Sie nun den Namen, und vergewissern Sie sich, dass er vollständig in Kleinbuchstaben angegeben ist. Das Modul „cameraCapture“ ruft das Modul „classifier“ unter Verwendung einer Anforderungsbibliothek auf, die alle Anforderungen in Kleinbuchstaben formatiert, und von IoT Edge wird die Groß-/Kleinschreibung berücksichtigt. 

5. Aktualisieren Sie den Parameter **createOptions** für das Modul „cameraCapture“ mit dem folgenden JSON-Code. Diese Informationen erstellen Umgebungsvariablen im Modulcontainer, die im Prozess „main.py“ abgerufen werden. Durch Einbeziehung dieser Informationen in das Bereitstellungsmanifest können Sie das Image oder den Endpunkt ändern, ohne das Modulimage neu zu erstellen. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Falls Sie Ihr Custom Vision-Modul nicht *classifier* genannt haben, aktualisieren Sie den Wert des Bildverarbeitungsendpunkts entsprechend. 

6. Aktualisieren Sie am Ende der Datei den Parameter **routes** für das Modul „$edgeHub“. Die Vorhersageergebnisse sollen von „cameraCapture“ an IoT Hub weitergeleitet werden.

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Falls Sie Ihr zweites Modul nicht *cameraCapture* genannt haben, aktualisieren Sie den Routenwert entsprechend. 

7. Speichern Sie die Datei **deployment.template.json**.

## <a name="build-and-push-your-iot-edge-solution"></a>Erstellen und Pushen Ihrer IoT Edge-Projektmappe

Nachdem Sie die beiden Module erstellt und die Bereitstellungsmanifestvorlage konfiguriert haben, können Sie die Containerimages erstellen und an Ihre Containerregistrierung pushen.

Sobald sich die Images in Ihrer Registrierung befinden, können Sie die Projektmappe auf einem IoT Edge-Gerät bereitstellen. Sie können über IoT Hub Module auf einem Gerät festlegen, können jedoch auch über Visual Studio Code auf IoT Hub und Geräte zugreifen. In diesem Abschnitt richten Sie den Zugriff auf Ihre IoT Hub-Instanz ein und stellen dann mithilfe von VS Code Ihre Projektmappe auf dem IoT Edge-Gerät bereit.

Erstellen Sie zunächst Ihre Projektmappe, und pushen Sie sie an Ihre Containerregistrierung.

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**.

2. Melden Sie sich bei Docker an. Geben Sie dazu den nachstehenden Befehl im Terminal ein. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

3. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und klicken Sie anschließend auf **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen).

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie den Visual Studio Code-Explorer und die Azure IoT Tools-Erweiterung, um das Modulprojekt auf Ihrem IoT Edge-Gerät bereitzustellen. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.amd64.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Visual Studio Code-Explorer im Abschnitt **Azure IoT Hub** den Bereich **Geräte**, um Ihre IoT-Geräteliste anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

3. Wählen Sie im Konfigurationsordner **(config)** die Datei **deployment.amd64.json** aus, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). Verwenden Sie nicht die Datei „deployment.template.json“.

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollten die neuen Module **classifier** und **cameraCapture** als ausgeführt angezeigt werden – zusammen mit **$edgeAgent** und **$edgeHub**.  

Sie können auch direkt auf dem Gerät überprüfen, ob alle Module ausgeführt werden. Führen Sie auf dem IoT Edge-Gerät den folgenden Befehl aus, um den Status der Module anzuzeigen.

   ```bash
   iotedge list
   ```

Es dauert ggf. einige Minuten, bis die Module gestartet werden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten.

## <a name="view-classification-results"></a>Anzeigen der Klassifizierungsergebnisse

Die Ergebnisse Ihrer Module können auf zwei Arten angezeigt werden: auf dem Gerät selbst, wenn die Nachrichten generiert und gesendet werden, oder über Visual Studio Code, wenn die Nachrichten bei IoT Hub eingehen. 

Zeigen Sie auf Ihrem Gerät die Protokolle des Moduls „cameraCapture“ an, um die gesendeten Nachrichten und die Empfangsbestätigung von IoT Hub zu sehen. 

   ```bash
   iotedge logs cameraCapture
   ```

Klicken Sie in Visual Studio Code mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus. 

> [!NOTE]
> Die Ausgabe des Moduls „cameraCapture“ enthält unter Umständen zunächst einige Verbindungsfehler. Dies ist auf die Verzögerung zwischen Modulen zurückzuführen, die bereitgestellt und gestartet werden.
>
> Vom Modul „cameraCapture“ wird automatisch weiter versucht, eine Verbindung herzustellen, bis der Vorgang erfolgreich war. Danach werden die erwarteten Bildklassifizierungsnachrichten angezeigt, die weiter unten beschrieben sind.

Die Ergebnisse aus dem Custom Vision-Modul werden in Form von Nachrichten vom Modul „cameraCapture“ gesendet und enthalten die Angabe, mit welcher Wahrscheinlichkeit es sich bei dem Bild um ein Bild einer Hemlocktanne oder eines Kirschbaums handelt. Da auf dem Bild eine Hemlocktanne zu sehen ist, sollte die Wahrscheinlichkeit bei 1.0 liegen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Custom Vision-Modell trainiert und als Modul auf einem IoT Edge-Gerät bereitgestellt. Anschließend haben Sie ein Modul erstellt, das den Bildklassifizierungsdienst abfragen und die Ergebnisse an IoT Hub zurückgeben kann.

Sie können mit den nächsten Tutorials fortfahren, um andere Möglichkeiten kennenzulernen, wie Azure IoT Edge Ihnen beim Umwandeln von Daten in geschäftliche Erkenntnisse auf Edge-Ebene helfen kann.

> [!div class="nextstepaction"]
> [Speichern von Daten im Edge-Bereich mit SQL Server-Datenbanken](tutorial-store-data-sql-server.md)