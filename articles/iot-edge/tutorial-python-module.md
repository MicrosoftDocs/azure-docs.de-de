---
title: 'Tutorial: Benutzerdefiniertes Python-Modul  mit Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit Python-Code erstellen und dieses Modul auf einem Edge-Gerät bereitstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 402b61bb0845532d601e9f5dcaaf55eacce685d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959372"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Tutorial: Entwickeln und Bereitstellen eines Python-IoT Edge-Moduls für Linux-Geräte

Verwenden Sie Visual Studio Code zum Entwickeln von Python-Code, und stellen Sie ihn auf einem Linux-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird.

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein IoT Edge-Modul erstellen und bereitstellen, das Sensordaten auf dem in der Schnellstartanleitung eingerichteten IoT Edge-Gerät filtert. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen eines IoT Edge-Python-Moduls mit Visual Studio Code
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio Code und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird gezeigt, wie Sie ein Modul in **Python** mit **Visual Studio Code** entwickeln und auf einem **Linux-Gerät** bereitstellen. IoT Edge unterstützt keine Python-Module für Windows-Geräte.

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von Python-Modulen für Linux:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Verwenden von VS Code für Python-Module unter Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Verwenden von VS Code für Python-Module unter Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
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

Erstellen Sie eine Python-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Klicken Sie in Visual Studio Code auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen.

2. Geben Sie in der Befehlspalette den Befehl **Azure: Sign in** ein, und führen Sie ihn aus, und befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. Falls Sie bereits angemeldet sind, können Sie diesen Schritt überspringen.

3. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Folgen Sie den Anweisungen, und geben Sie die folgenden Informationen an, um Ihre Projektmappe zu erstellen:

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Python-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **PythonModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Das endgültige Imagerepository sieht wie folgt aus: \<registry name\>.azurecr.io/pythonmodule. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihr Containerrepository und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie im VS Code-Explorer die Datei vom Typ **.env**.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie die ENV-Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit Python-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“.

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus.

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Jede Vorlage enthält Beispielcode, der simulierte Sensordaten aus dem Modul **SimulatedTemperatureSensor** an den IoT-Hub weiterleitet. Fügen Sie in diesem Abschnitt den Code hinzu, der **pythonModule** erweitert, um die Nachrichten vor dem Senden zu analysieren.

1. Öffnen Sie im VS Code-Explorer **Module** > **PythonModule** > **main.py**.

2. Importieren Sie oben in der Datei **main.py** die Bibliothek **json**:

    ```python
    import json
    ```

3. Fügen Sie die Variablen **TEMPERATURE_THRESHOLD** und **TWIN_CALLBACKS** unter den globalen Leistungsindikatoren hinzu. Der Temperaturschwellenwert legt den Wert fest, den die gemessene Computertemperatur übersteigen muss, damit die Daten an die IoT Hub-Instanz gesendet werden.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Ersetzen Sie die Funktion **input1_listener** durch den folgenden Code:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Aktualisieren Sie die **Listener**, um auch auf Zwillingsaktualisierungen zu lauschen.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Speichern Sie die Datei „main.py“.

7. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe.

8. Fügen Sie dem Bereitstellungsmanifest den Modulzwilling **PythonModule** hinzu. Fügen Sie am Ende des Abschnitts **moduleContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-python-module/module-twin.png)

9. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und dem „PythonModule“ Code hinzugefügt, der Nachrichten herausfiltert, bei denen die gemeldete Computertemperatur innerhalb der zulässigen Grenzwerte liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**.

2. Melden Sie sich bei Docker an. Geben Sie dazu den nachstehenden Befehl im Terminal ein. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

3. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und wählen Sie **Build and Push IoT Edge Solution** (IoT Edge-Projektmappe erstellen und übertragen) aus.

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie den Visual Studio Code-Explorer und die Azure IoT Tools-Erweiterung, um das Modulprojekt auf Ihrem IoT Edge-Gerät bereitzustellen. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.amd64.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Visual Studio Code-Explorer im Abschnitt **Azure IoT Hub** den Bereich **Geräte**, um Ihre IoT-Geräteliste anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

3. Wählen Sie im Konfigurationsordner **(config)** die Datei **deployment.amd64.json** aus, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). Verwenden Sie nicht die Datei „deployment.template.json“.

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **PythonModule**-Modul zusammen mit dem **SimulatedTemperatureSensor**-Modul sowie mit **$edgeAgent** und **$edgeHub** ausgeführt werden.

    Es dauert ggf. einige Minuten, bis die Module gestartet werden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten.

## <a name="view-the-generated-data"></a>Anzeigen der generierten Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet.

Der Status Ihres IoT Edge-Geräts wird im Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte) des Visual Studio Code-Explorers angezeigt. Erweitern Sie die Details Ihres Geräts, um eine Liste mit den bereitgestellten und ausgeführten Modulen anzuzeigen.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

2. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Es kann eine Weile dauern, bis die Nachrichten eintreffen. Das IoT Edge-Gerät muss die neue Bereitstellung erhalten und alle Module starten. Danach wird bei den am Code von „PythonModule“ vorgenommenen Änderungen gewartet, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu.

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

In diesem Tutorial haben Sie ein IoT Edge-Modul erstellt, das Code zum Filtern von Rohdaten enthält, die von Ihrem IoT Edge-Gerät generiert wurden.

Sie können die nächsten Tutorials durcharbeiten, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)