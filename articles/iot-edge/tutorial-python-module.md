---
title: Erstellen eines benutzerdefinierten Python-Moduls – Azure IoT Edge | Microsoft-Dokumentation
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit Python-Code erstellen und dieses Modul auf einem Edge-Gerät bereitstellen.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 6ec429bc7c1598e947b798c30e7992e3d357d00c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303816"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Tutorial: Entwickeln und Bereitstellen eines Python-IoT Edge-Moduls für Linux-Geräte

Verwenden Sie Visual Studio Code zum Entwickeln von C-Code, und stellen Sie ihn auf einem Linux-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird. 

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein IoT Edge-Modul erstellen und bereitstellen, das Sensordaten auf dem in der Schnellstartanleitung eingerichteten IoT Edge-Gerät filtert. In diesem Tutorial lernen Sie Folgendes:    

> [!div class="checklist"]
> * Erstellen eines IoT Edge-Python-Moduls mit Visual Studio Code
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio Code und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten


Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Projektmappenbereich

In diesem Tutorial wird gezeigt, wie Sie ein Modul in **Python** mit **Visual Studio Code** entwickeln und auf einem **Linux-Gerät** bereitstellen. IoT Edge unterstützt keine Python-Module für Windows-Geräte. 

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von Python-Modulen für Linux: 

| Python | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Verwenden von VS Code für Python-Module unter Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Verwenden von VS Code für Python-Module unter Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben: 

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Um ein IoT Edge-Modul in Python zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer: 

* [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python) für Visual Studio Code 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) zum Installieren von Python-Paketen (normalerweise in der Python-Installation enthalten).

>[!Note]
>Stellen Sie sicher, dass sich der Ordner `bin` im Pfad für Ihre Plattform befindet. In der Regel ist dies `~/.local/` für UNIX und macOS bzw. `%APPDATA%\Python` unter Windows.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts
Die folgenden Schritte dienen zum Erstellen eines IoT Edge-Python-Moduls unter Verwendung von Visual Studio Code und der Azure IoT-Tools.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Verwenden Sie das Python-Paket **cookiecutter**, um eine Python-Lösungsvorlage zu erstellen, auf der Sie aufbauen können. 

1. Wählen Sie in Visual Studio Code **Ansicht** > **Terminal** aus, um das in Visual Studio Code integrierte Terminal zu öffnen.

2. Geben Sie im Terminal den folgenden Befehl ein, um **cookiecutter** zu installieren (oder zu aktualisieren). „cookiecutter“ wird zum Erstellen der IoT Edge-Lösungsvorlage verwendet.

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Vergewissern Sie sich, dass sich das Verzeichnis, in dem Cookiecutter installiert wird, im PATH-Element Ihrer Umgebung befindet, damit das Programm über eine Eingabeaufforderung aufgerufen werden kann. Das Verzeichnis ist Teil der Ausgabe des Installationsskripts, beispielsweise `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Starten Sie Visual Studio Code neu, um die Änderungen für PATH zu übernehmen. 

3. Klicken Sie auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen. 

4. Geben Sie in der Befehlspalette den Befehl **Azure: Sign in** ein, und führen Sie ihn aus, und befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. Falls Sie bereits angemeldet sind, können Sie diesen Schritt überspringen.

5. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Folgen Sie den Anweisungen, und geben Sie die folgenden Informationen an, um Ihre Projektmappe zu erstellen:

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **PythonModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Das endgültige Imagerepository sieht wie folgt aus: \<Registrierungsname\>.azurecr.io/pythonmodule. |
 
   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihr Containerrepository und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. 

1. Öffnen Sie im VS Code-Explorer die Datei vom Typ **.env**. 
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben. 
3. Speichern Sie die ENV-Datei. 

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit C#-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“. 

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus. 

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei. 

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Jede Vorlage enthält Beispielcode, der simulierte Sensordaten aus dem Modul **tempSensor** an die IoT Hub-Instanz weiterleitet. Fügen Sie in diesem Abschnitt den Code hinzu, der **pythonModule** erweitert, um die Nachrichten vor dem Senden zu analysieren. 

1. Öffnen Sie im VS Code-Explorer **Module** > **PythonModule** > **main.py**.

2. Importieren Sie oben in der Datei **main.py** die Bibliothek **json**:

    ```python
    import json
    ```

3. Fügen Sie die Variablen **TEMPERATURE_THRESHOLD** und **TWIN_CALLBACKS** unter den globalen Leistungsindikatoren hinzu. Der Temperaturschwellenwert legt den Wert fest, den die gemessene Computertemperatur übersteigen muss, damit die Daten an die IoT Hub-Instanz gesendet werden.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Ersetzen Sie die Funktion **receive_message_callback** durch folgenden Code:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Fügen Sie eine neue Funktion namens **module_twin_callback** hinzu. Diese Funktion wird aufgerufen, wenn die gewünschten Eigenschaften aktualisiert werden.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Fügen Sie der Methode **__init__** in der Klasse **HubManager** eine neue Methode hinzu, um die soeben hinzugefügte Funktion **module_twin_callback** zu initialisieren:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Speichern Sie die Datei „main.py“.

8. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe. 

9. Fügen Sie dem Bereitstellungsmanifest den Modulzwilling **PythonModule** hinzu. Fügen Sie am Ende des Abschnitts **moduleContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-python-module/module-twin.png)

10. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und dem „PythonModule“ Code hinzugefügt, der Nachrichten herausfiltert, bei denen die gemeldete Computertemperatur innerhalb der zulässigen Grenzwerte liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**.

1. Melden Sie sich bei Docker an. Geben Sie dazu den nachstehenden Befehl im Terminal ein. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

2. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und klicken Sie anschließend auf **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen).

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.


## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie den Visual Studio Code-Explorer und die Azure IoT Tools-Erweiterung, um das Modulprojekt auf Ihrem IoT Edge-Gerät bereitzustellen. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Visual Studio Code-Explorer den Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte), um Ihre Liste mit IoT-Geräten anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

3. Wählen Sie im Konfigurationsordner (**config**) die Datei **deployment.json** aus, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). Verwenden Sie nicht die Datei „deployment.template.json“.

4. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **PythonModule**-Modul zusammen mit dem **TempSensor**-Modul sowie **$edgeAgent** und **$edgeHub** ausgeführt werden. 

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet.

Der Status Ihres IoT Edge-Geräts wird im Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte) des Visual Studio Code-Explorers angezeigt. Erweitern Sie die Details Ihres Geräts, um eine Liste mit den bereitgestellten und ausgeführten Modulen anzuzeigen.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

2. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Möglicherweise dauert es eine Weile, bis die Nachrichten eintreffen, weil das IoT Edge-Gerät erst seine neue Bereitstellung empfangen und alle Module starten muss. Danach wird bei den am Code von „PythonModule“ vorgenommenen Änderungen gewartet, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu. 

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „PythonModule“ im Bereitstellungsmanifest verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Erweitern Sie in Visual Studio Code die Details unter Ihrem IoT Edge-Gerät, um die derzeit ausgeführten Module anzuzeigen. 

2. Klicken Sie mit der rechten Maustaste auf **PythonModule**, und wählen Sie **Modulzwilling bearbeiten** aus. 

3. Suchen Sie **TemperatureThreshold** in den gewünschten Eigenschaften. Ändern Sie den Wert auf eine neue Temperatur, die 5 bis 10 Grad höher als die zuletzt gemeldete Temperatur ist. 

4. Speichern Sie die Modulzwillingsdatei.

5. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle im Bearbeitungsbereich für Modulzwillinge, und wählen Sie **Modulzwilling aktualisieren** aus. 

6. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Jetzt sollten die Nachrichten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul erstellt, das Code zum Filtern von Rohdaten enthält, die von Ihrem IoT Edge-Gerät generiert wurden. Wenn Sie jetzt Ihre eigenen Module entwickeln möchten, können Sie mehr zum [Entwickeln Ihrer eigenen IoT Edge-Module](module-development.md) oder zum [Entwickeln von Modulen mit Visual Studio Code](how-to-vs-code-develop-module.md) erfahren. Sie können die nächsten Tutorials durcharbeiten, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)