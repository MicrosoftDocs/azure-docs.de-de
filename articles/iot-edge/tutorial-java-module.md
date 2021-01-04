---
title: 'Tutorial: Benutzerdefiniertes Java-Modul  mit Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit Java-Code erstellen und es auf einem Edge-Gerät bereitstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: 51b7f6e814a9fad286a934466daeb1ffced225c1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968059"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Tutorial: Entwickeln eines Java-IoT Edge-Moduls für Linux-Geräte

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen. Sie verwenden das simulierte Io  Edge-Gerät, das Sie in der Schnellstartanleitung zum Bereitstellen von Azure IoT Edge auf einem simulierten Gerät unter [Linux](quickstart-linux.md) erstellt haben. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen eines IoT Edge-Java-Moduls mithilfe von Visual Studio Code auf der Grundlage des Azure IoT Edge-Maven-Vorlagenpakets und des Azure IoT-Java-Geräte-SDK.
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio Code und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird gezeigt, wie Sie ein Modul in **Java** mit **Visual Studio Code** entwickeln und auf einem **Linux-Gerät** bereitstellen. IoT Edge unterstützt keine Java-Module für Windows-Geräte.

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von Java-Modulen:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Verwenden von VS Code für Java-Module unter Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Verwenden von VS Code für Java-Module unter Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Um ein IoT Edge-Modul in Java zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer: 

* [Java-Erweiterungspaket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) für Visual Studio Code
* [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) und [Festlegen der `JAVA_HOME`-Umgebungsvariable](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) für den Verweis auf Ihre JDK-Installation
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >Beim Java- und Maven-Installationsprozess werden Ihrem System Umgebungsvariablen hinzugefügt. Starten Sie nach Abschluss der Installation geöffnete Instanzen des Visual Studio Code-Terminals, von Powershell oder Eingabeaufforderungen neu. Mit diesem Schritt wird sichergestellt, dass die Java- und Maven-Befehle in Zukunft von diesen Hilfsprogrammen erkannt werden.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

Mit den folgenden Schritten wird ein IoT Edge-Modulprojekt erstellt, das auf dem Azure IoT Edge-Maven-Vorlagenpaket und dem Azure IoT Java-Geräte-SDK basiert. Sie erstellen das Projekt, indem Sie Visual Studio Code und die Azure IoT-Tools verwenden.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Erstellen Sie eine Java-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Klicken Sie in Visual Studio Code auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen.

2. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Java-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **JavaModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Das endgültige Imagerepository sieht so aus: \<registry name\>.azurecr.io/javamodule. |
   | Provide value for groupId (Wert für groupId eingeben) | Geben Sie einen Wert für die Gruppen-ID ein, oder übernehmen Sie den Standardwert **com.edgemodule**. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-java-module/repository.png)

Wenn Sie zum ersten Mal ein Java-Modul erstellen, dauert das Herunterladen der Maven-Pakete unter Umständen mehrere Minuten. Wenn die Lösung bereit ist, wird im VS Code-Fenster der Arbeitsbereich für Ihre IoT Edge-Arbeitsmappe geladen. Der Arbeitsbereich für die Projektmappe enthält fünf übergeordnete Komponenten:

* Der Ordner **modules** enthält den Java-Code für Ihr Modul sowie die Docker-Dateien für die Erstellung Ihres Moduls als Containerimage.
* In der Datei **\.env** werden Ihre Anmeldeinformationen für die Containerregistrierung gespeichert.
* Die Datei **deployment.template.json** enthält die Informationen, die die IoT Edge-Runtime zum Bereitstellen von Modulen auf einem Gerät verwendet.
* Die Datei **deployment.debug.template.json** enthält die Debugversion der Module.
* In diesem Tutorial wird nicht der Ordner **\.vscode** oder die Datei **\.gitignore** bearbeitet.

Wenn Sie beim Erstellen Ihrer Lösung keine Containerregistrierung angegeben, aber den Standardwert „localhost:5000“ übernommen haben, besitzen Sie keine Datei vom Typ „\.env“.

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie im VS Code-Explorer die ENV-Datei.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit Java-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“.

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus.

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

1. Öffnen Sie im VS Code-Explorer **Module** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Fügen Sie am Anfang der Datei den folgenden Code ein, um neue referenzierte Klassen zu importieren.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Fügen Sie die folgende Definition in der **App**-Klasse hinzu. Mit dieser Variablen wird ein Temperaturschwellenwert festgelegt. Die gemessene Computertemperatur wird erst an IoT Hub gemeldet, wenn sie diesen Wert übersteigt.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Ersetzen Sie die execute-Methode von **MessageCallbackMqtt** durch den folgenden Code. Diese Methode wird aufgerufen, wenn das Modul eine MQTT-Nachricht vom IoT Edge-Hub empfängt. Sie filtert Nachrichten heraus, die Temperaturen unter dem über den Modulzwilling festgelegten Temperaturschwellenwert melden.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Fügen Sie die folgenden zwei statischen internen Klassen zur **App**-Klasse hinzu. Mit diesen Klassen wird die Variable „tempThreshold“ aktualisiert, wenn sich die gewünschte Eigenschaft des Modulzwillings ändert. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Fügen Sie in der **main**-Methode nach **client.open()** die folgenden Zeilen ein, um die Modulzwillingsupdates zu abonnieren:

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Speichern Sie die Datei „App.java“.

8. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe.

9. Fügen Sie den Modulzwilling **JavaModule** zum Bereitstellungsmanifest hinzu. Fügen Sie am Ende des Abschnitts **moduleContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-java-module/module-twin.png)

10. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und dem **JavaModule**-Element Code hinzugefügt, mit dem Nachrichten herausgefiltert werden, deren gemeldete Computertemperatur unter dem zulässigen Grenzwert liegt. Erstellen Sie nun die Projektmappe als Containerimage, und übertragen Sie sie per Pushvorgang an die Containerregistrierung.

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

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **JavaModule**-Modul zusammen mit dem **SimulatedTemperatureSensor**-Modul sowie mit **$edgeAgent** und **$edgeHub** ausgeführt werden.  

    Es dauert ggf. einige Minuten, bis die Module gestartet werden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten.

## <a name="view-the-generated-data"></a>Anzeigen der generierten Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

2. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Es kann eine Weile dauern, bis die Nachrichten eintreffen. Das IoT Edge-Gerät muss die neue Bereitstellung erhalten und alle Module starten. Danach wird bei den am Code von „JavaModule“ vorgenommenen Änderungen gewartet, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu.

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „JavaModule“ im Bereitstellungsmanifest verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Erweitern Sie in Visual Studio Code die Details unter Ihrem IoT Edge-Gerät, um die derzeit ausgeführten Module anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf **JavaModule**, und wählen Sie **Modulzwilling bearbeiten** aus.

3. Suchen Sie **TemperatureThreshold** in den gewünschten Eigenschaften. Ändern Sie den Wert auf eine neue Temperatur, die 5 bis 10 Grad höher als die zuletzt gemeldete Temperatur ist.

4. Speichern Sie die Modulzwillingsdatei.

5. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle im Bearbeitungsbereich für Modulzwillinge, und wählen Sie **Modulzwilling aktualisieren** aus.

6. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Jetzt sollten die Nachrichten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert wurden.

In den nächsten Tutorials erfahren Sie, wie Azure IoT Edge Sie bei der Bereitstellung von Azure-Clouddiensten unterstützt, um Daten im Edgebereich zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)