---
title: 'Tutorial: Bereitstellen einer Custom Vision-Klassifizierung auf einem Gerät – Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Custom Vision und IoT Edge ein als Container ausgeführtes Modell für maschinelles Sehen erstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a0530739428e18d01209f94345ae53dfb743d80b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239685"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Tutorial: Durchführen von Bildklassifizierungen im Edge-Bereich mit dem Custom Vision Service

Azure IoT Edge kann Workloads aus der Cloud an den Edge-Bereich verlagern und so die Effizienz Ihrer IoT-Lösung steigern. Diese Funktion eignet sich besonders für Dienste, die große Datenmengen verarbeiten – also beispielsweise für Modelle für maschinelles Sehen. Mit dem [Custom Vision Service](../cognitive-services/custom-vision-service/home.md) können Sie benutzerdefinierte Bildklassifizierungen erstellen und auf Geräten als Container bereitstellen. Die Kombination dieser beiden Dienste ermöglicht es Ihnen, Erkenntnisse aus Bildern oder Videostreams zu gewinnen, ohne zuvor sämtliche Daten an einen externen Ort zu übertragen. Custom Vision bietet eine Klassifizierung, die ein Bild mit einem trainierten Modell vergleicht, um Erkenntnisse zu generieren. 

So kann mit Custom Vision auf einem IoT Edge-Gerät beispielsweise ermittelt werden, ob das Verkehrsaufkommen auf einer Autobahn höher ist als normal oder ob in einem Parkhaus noch Parkplätze in einer Reihe frei sind. Diese Erkenntnisse können an einen anderen Dienst weitergegeben werden, um Aktionen auszuführen. 

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> * Erstellen einer Bildklassifizierung mit Custom Vision
> * Entwickeln eines IoT Edge-Moduls, das den Custom Vision-Webserver auf Ihrem Gerät abfragt
> * Senden der Ergebnisse der Bildklassifizierung an IoT Hub

<center>

![Architekturdiagramm des Tutorials: Staging und Bereitstellung der Klassifizierung](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben: 

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Um ein IoT Edge-Modul mit dem Custom Vision-Dienst zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer: 

* [Python](https://www.python.org/downloads/)
* [Git-Client](https://git-scm.com/downloads)
* [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-Erweiterung für Visual Studio Code

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
   | NAME | Geben Sie einen Namen für Ihr Projekt an (beispielsweise **EdgeTreeClassifier**). |
   | BESCHREIBUNG | Eine optionale Projektbeschreibung. |
   | Ressourcengruppe | Übernehmen Sie den Standardwert **Eingeschränkte Testversion**. |
   | Projekttypen | **Klassifizierung** |
   | Klassifizierungstypen | **Multiclass (single tag per image)** (Multiklasse (einzelnes Tag pro Bild)) | 
   | Domänen | **General (compact)** (Allgemein (kompakt)) |

5. Wählen Sie **Projekt erstellen** aus.

### <a name="upload-images-and-train-your-classifier"></a>Hochladen von Bildern und Trainieren Ihrer Klassifizierung

Für die Erstellung einer Bildklassifizierung benötigen Sie eine Gruppe von Trainingsbildern sowie Testbilder. 

1. Klonen Sie Beispielbilder aus dem Repository [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) auf Ihrem lokalen Entwicklungscomputer, oder laden Sie sie herunter. 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Kehren Sie zu Ihrem Custom Vision-Projekt zurück, und wählen Sie **Bilder hinzufügen** aus. 

3. Navigieren Sie in dem lokal geklonten Git-Repository zum ersten Bildordner: **Cognitive-CustomVision-Windows/Samples/Images/Hemlock**. Wählen Sie in dem Ordner alle zehn Bilder und anschließend **Öffnen** aus. 

4. Fügen Sie dieser Gruppe von Bildern das Tag **hemlock** hinzu, und drücken Sie die**EINGABETASTE**, um das Tag anzuwenden. 

5. Wählen Sie **10 Dateien hochladen** aus. 

   ![Hochladen von Dateien mit dem Tag „hemlock“ in Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Klicken Sie nach dem erfolgreichen Hochladen der Bilder auf **Fertig**.

7. Wählen Sie erneut **Bilder hinzufügen** aus.

8. Navigieren Sie zum zweiten Bildordner: **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry**. Wählen Sie in dem Ordner alle zehn Bilder und anschließend **Öffnen** aus. 

9. Fügen Sie dieser Gruppe von Bildern das Tag **japanese cherry** hinzu, und drücken Sie die**EINGABETASTE**, um das Tag anzuwenden. 

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

1. Wählen Sie in Visual Studio Code **Ansicht** > **Terminal** aus, um das in Visual Studio Code integrierte Terminal zu öffnen.

2. Geben Sie im integrierten Terminal den folgenden Befehl ein, um **Cookiecutter** zu installieren (oder zu aktualisieren). Cookiecutter wird zum Erstellen der IoT Edge-Python-Modulvorlage in VS Code verwendet:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Vergewissern Sie sich, dass sich das Verzeichnis, in dem Cookiecutter installiert wird, im `Path` Ihrer Umgebung befindet, damit das Programm über eine Eingabeaufforderung aufgerufen werden kann.

3. Klicken Sie auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen. 

4. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Geben Sie in der Befehlspalette die folgenden Informationen an, um die Projektmappe zu erstellen: 

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein (beispielsweise **CustomVisionSolution**), oder übernehmen Sie den Standardnamen. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **classifier**.<br><br>Wichtig: Der Name des Moduls muss in Kleinbuchstaben angegeben werden. Bei Modulverweisen von IoT Edge wird die Groß-/Kleinschreibung berücksichtigt, und in dieser Projektmappe wird eine Bibliothek verwendet, die alle Anforderungen in Kleinbuchstaben formatiert. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Ihr Containerimage wird aus dem vorherigen Schritt übernommen. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. Die endgültige Zeichenfolge sieht wie folgt aus: \<Registrierungsname\>.azurecr.io/classifier. |
 
   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-deploy-custom-vision/repository.png)

Das Visual Studio Code-Fenster lädt den Arbeitsbereich für Ihre IoT Edge-Projektmappe.

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

1. Öffnen Sie im VS Code-Explorer die ENV-Datei.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“. 

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus. 

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten wir den Standardwert **amd64** bei. 

### <a name="add-your-image-classifier"></a>Hinzufügen Ihrer Bildklassifizierung

Die Python-Modulvorlage in Visual Studio Code enthält Beispielcode, den Sie zum Testen von IoT Edge ausführen können. Dieser Code wird allerdings in diesem Szenario nicht verwendet. Gehen Sie stattdessen wie in diesem Abschnitt beschrieben vor, um den Beispielcode durch den Bildklassifizierungscontainer zu ersetzen, den Sie zuvor exportiert haben. 

1. Navigieren Sie in Ihrem Datei-Explorer zu dem Custom Vision-Paket, das Sie heruntergeladen und extrahiert haben. Kopieren Sie den gesamten Inhalt aus dem extrahierten Paket. Dieser sollte zwei Ordner (**app** und **azureml**) sowie zwei Dateien (**Dockerfile** und **README**) umfassen. 

2. Navigieren Sie in Ihrem Datei-Explorer zu dem Verzeichnis, das Sie Visual Studio Code gegenüber als Zielverzeichnis für die Erstellung Ihrer IoT Edge-Projektmappe angegeben haben. 

3. Öffnen Sie den Ordner des Klassifizierungsmoduls. Sofern Sie im vorherigen Abschnitt die vorgeschlagenen Namen verwendet haben, sieht die Ordnerstruktur wie folgt aus: **CustomVisionSolution/modules/classifier**. 

4. Fügen Sie die Dateien in den Ordner **classifier** ein. 

5. Wechseln Sie wieder zum Visual Studio Code-Fenster. Im Arbeitsbereich Ihrer Projektmappe sollten nun im Modulordner die Bildklassifizierungsdateien angezeigt werden. 

   ![Arbeitsbereich der Projektmappe mit Bildklassifizierungsdateien](./media/tutorial-deploy-custom-vision/workspace.png)

6. Öffnen Sie im Ordner „classifier“ die Datei **module.json**. 

7. Aktualisieren Sie den Parameter **platforms**, sodass er auf das neue Dockerfile verweist, das Sie hinzugefügt haben. Entfernen Sie außerdem die ARM32-Architektur und die AMD64.debug-Optionen. Diese werden derzeit für das Custom Vision-Modul nicht unterstützt. 

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
   | Select deployment template file (Bereitstellungsvorlagendatei auswählen) | Wählen Sie die Datei „deployment.template.json“ aus dem Ordner „CustomVisionSolution“ aus. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie Ihr Modul **cameraCapture**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert für Ihre Azure-Containerregistrierung. Die endgültige Zeichenfolge sieht wie folgt aus: **\<Registrierungsname\>.azurecr.io/cameracapture**. |

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

    import iothub_client
    # pylint: disable=E0611
    from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError
    # pylint: disable=E0401

    # messageTimeout - the maximum time in milliseconds until a message times out.
    # The timeout period starts at IoTHubModuleClient.send_event_async.
    MESSAGE_TIMEOUT = 10000

    # Choose HTTP, AMQP or MQTT as transport protocol.  
    PROTOCOL = IoTHubTransportProvider.MQTT

    # global counters
    SEND_CALLBACKS = 0

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = IoTHubMessage(bytearray(strMessage, 'utf8'))
        hubManager.send_event_to_output("output1", message, 0)

    # Callback received when the message that we send to IoT Hub is processed.
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        SEND_CALLBACKS += 1
        print ( "Confirmation received for message with result = %s" % result )
        print ( "   Total calls confirmed: %d \n" % SEND_CALLBACKS )

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
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    class HubManager(object):
        def __init__(self, protocol, message_timeout):
            self.client_protocol = protocol
            self.client = IoTHubModuleClient()
            self.client.create_from_environment(protocol)
            # set the time until a message times out
            self.client.set_option("messageTimeout", message_timeout)
            
        # Sends a message to an output queue, to be routed by IoT Edge hub. 
        def send_event_to_output(self, outputQueueName, event, send_context):
            self.client.send_event_async(
                outputQueueName, event, send_confirmation_callback, send_context)

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global hubManager 
                hubManager = HubManager(PROTOCOL, MESSAGE_TIMEOUT)
            except IoTHubError as iothub_error:
                print ( "Unexpected error %s from IoTHub" % iothub_error )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
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

3. Öffnen Sie in Visual Studio Code die Datei **Dockerfile.amd64** für das cameraCapture-Modul. (ARM32 wird vom Custom Vision-Modul derzeit nicht unterstützt.) 

4. Fügen Sie nach der Zeile, die das Arbeitsverzeichnis (`WORKDIR /app`) festlegt, die folgende Codezeile hinzu: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Speichern Sie das Dockerfile. 

### <a name="prepare-a-deployment-manifest"></a>Vorbereiten eines Bereitstellungsmanifests

Sie haben in diesem Tutorial ein Custom Vision-Modell trainiert, um Bilder von Bäumen zu klassifizieren, und dieses Modell als IoT Edge-Modul verpackt. Anschließend haben Sie ein zweites Modul erstellt, das den Bildklassifizierungsserver abfragen und die Ergebnisse an IoT Hub zurückgeben kann. Als Nächstes können Sie das Bereitstellungsmanifest erstellen, das gegenüber einem IoT Edge-Gerät angibt, wie diese beiden Module gestartet und gemeinsam ausgeführt werden. 

Die IoT Edge-Erweiterung für Visual Studio Code stellt in jeder IoT Edge-Projektmappe eine Vorlage bereit, die Sie beim Erstellen eines Bereitstellungsmanifests unterstützt. 

1. Öffnen Sie die Datei **deployment.template.json** aus dem Projektmappenordner. 

2. Navigieren Sie zum Abschnitt **modules**. Er sollte drei Module enthalten: die beiden, die Sie erstellt haben (also „classifier“ und „cameraCapture“), und ein drittes (tempSensor), das standardmäßig vorhanden ist. 

3. Löschen Sie das Modul **tempSensor** zusammen mit allen dazugehörigen Parametern. Dieses Modul dient zur Bereitstellung von Beispieldaten für Testszenarien und wird in dieser Bereitstellung nicht benötigt. 

4. Sollten Sie das Bildklassifizierungsmodul nicht **classifier** genannt haben, überprüfen Sie nun den Namen, und vergewissern Sie sich, dass er vollständig in Kleinbuchstaben angegeben ist. Das Modul „cameraCapture“ ruft das Modul „classifier“ unter Verwendung einer Anforderungsbibliothek auf, die alle Anforderungen in Kleinbuchstaben formatiert, und von IoT Edge wird die Groß-/Kleinschreibung berücksichtigt. 

5. Aktualisieren Sie den Parameter **createOptions** für das Modul „cameraCapture“ mit dem folgenden JSON-Code. Diese Informationen erstellen Umgebungsvariablen im Modulcontainer, die im Prozess „main.py“ abgerufen werden. Durch Einbeziehung dieser Informationen in das Bereitstellungsmanifest können Sie das Image oder den Endpunkt ändern, ohne das Modulimage neu zu erstellen. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Falls Sie Ihr Custom Vision-Modul nicht *classifier* genannt haben, aktualisieren Sie den Wert des Bildverarbeitungsendpunkts entsprechend. 

5. Aktualisieren Sie am Ende der Datei den Parameter **routes** für das Modul „$edgeHub“. Die Vorhersageergebnisse sollen von „cameraCapture“ an IoT Hub weitergeleitet werden. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Falls Sie Ihr zweites Modul nicht *cameraCapture* genannt haben, aktualisieren Sie den Routenwert entsprechend. 

7. Speichern Sie die Datei **deployment.template.json**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Erstellen und Bereitstellen Ihrer IoT Edge-Projektmappe

Nachdem Sie die beiden Module erstellt und die Bereitstellungsmanifestvorlage konfiguriert haben, können Sie die Containerimages erstellen und an Ihre Containerregistrierung pushen. 

Sobald sich die Images in Ihrer Registrierung befinden, können Sie die Projektmappe auf einem IoT Edge-Gerät bereitstellen. Sie können über IoT Hub Module auf einem Gerät festlegen, können jedoch auch über Visual Studio Code auf IoT Hub und Geräte zugreifen. In diesem Abschnitt richten Sie den Zugriff auf Ihre IoT Hub-Instanz ein und stellen dann mithilfe von VS Code Ihre Projektmappe auf dem IoT Edge-Gerät bereit.

Erstellen Sie zunächst Ihre Projektmappe, und pushen Sie sie an Ihre Containerregistrierung. 

1. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und wählen Sie anschließend **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen) aus. Sie können den Status dieses Vorgangs im integrierten Terminal in VS Code nachverfolgen. 
2. Beachten Sie, dass Ihrer Projektmappe der neue Ordner **config** hinzugefügt wurde. Erweitern Sie diesen Ordner, und öffnen Sie die darin enthaltene Datei **deployment.json**.
3. Überprüfen Sie die Informationen in der Datei „deployment.json“. Die Datei „deployment.json“ wird auf der Grundlage der von Ihnen konfigurierten Bereitstellungsvorlagendatei und der Informationen aus der Projektmappe (einschließlich der ENV-Datei und der Dateien vom Typ „module.json“) automatisch erstellt bzw. aktualisiert. 

Wählen Sie als Nächstes Ihr Gerät aus, und stellen Sie Ihre Projektmappe bereit.

1. Erweitern Sie im VS Code-Explorer den Abschnitt **Azure IoT Hub-Geräte**. 
2. Klicken Sie mit der rechten Maustaste auf das Gerät, das als Ziel für die Bereitstellung festgelegt werden soll, und klicken Sie auf **Create deployment for single device** (Bereitstellung für einzelnes Gerät erstellen). 
3. Navigieren Sie im Datei-Explorer zum Ordner **config** der Projektmappe, und klicken Sie auf **deployment.json**. Klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). 

War die Bereitstellung erfolgreich, wird in VS Code eine Bestätigungsnachricht ausgegeben. Erweitern Sie im VS Code-Explorer die Details des IoT Edge-Geräts, das Sie für diese Bereitstellung verwendet haben. Zeigen Sie mit dem Cursor auf den Header **Azure IoT Hub-Geräte**, um die Aktualisierungsschaltfläche zu aktivieren, falls die Module nicht sofort angezeigt werden. Es kann etwas dauern, bis die Module gestartet wurden und mit IoT Hub kommunizieren. 

Sie können auch direkt auf dem Gerät überprüfen, ob alle Module ausgeführt werden. Führen Sie auf dem IoT Edge-Gerät den folgenden Befehl aus, um den Status der Module anzuzeigen. Es kann etwas dauern, bis die Module gestartet wurden.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Anzeigen der Klassifizierungsergebnisse

Die Ergebnisse Ihrer Module können auf zwei Arten angezeigt werden: auf dem Gerät selbst, wenn die Nachrichten generiert und gesendet werden, oder über Visual Studio Code, wenn die Nachrichten bei IoT Hub eingehen. 

Zeigen Sie auf Ihrem Gerät die Protokolle des Moduls „cameraCapture“ an, um die gesendeten Nachrichten und die Empfangsbestätigung von IoT Hub zu sehen. 

   ```bash
   iotedge logs cameraCapture
   ```

Klicken Sie in Visual Studio Code mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus. 

Die Ergebnisse aus dem Custom Vision-Modul werden in Form von Nachrichten vom Modul „cameraCapture“ gesendet und enthalten die Angabe, mit welcher Wahrscheinlichkeit es sich bei dem Bild um ein Bild einer Hemlocktanne oder eines Kirschbaums handelt. Da auf dem Bild eine Hemlocktanne zu sehen ist, sollte die Wahrscheinlichkeit bei 1.0 liegen. 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Custom Vision-Modell trainiert und als Modul auf einem IoT Edge-Gerät bereitgestellt. Anschließend haben Sie ein Modul erstellt, das den Bildklassifizierungsdienst abfragen und die Ergebnisse an IoT Hub zurückgeben kann. 

Eine ausführlichere Version dieses Szenarios mit einem Livekamerafeed finden Sie im GitHub-Projekt [Custom Vision + Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (Custom Vision und Azure IoT Edge auf einem Raspberry Pi 3). 

Sie können mit den nächsten Tutorials fortfahren, um andere Möglichkeiten kennenzulernen, wie Azure IoT Edge Ihnen beim Umwandeln von Daten in geschäftliche Erkenntnisse auf Edge-Ebene helfen kann.

> [!div class="nextstepaction"]
> [Speichern von Daten im Edge-Bereich mit SQL Server-Datenbanken](tutorial-store-data-sql-server.md)
