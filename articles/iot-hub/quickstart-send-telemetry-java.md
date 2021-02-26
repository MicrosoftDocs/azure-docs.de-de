---
title: 'Schnellstart: Senden von Telemetriedaten an Azure IoT Hub mit Java'
description: In dieser Schnellstartanleitung führen Sie zwei Java-Beispielanwendung aus, um simulierte Telemetriedaten an eine IoT Hub-Instanz zu senden und zur Verarbeitung in der Cloud aus der IoT Hub-Instanz zu lesen.
author: wesmc7777
manager: lizross
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 01/27/2021
ms.openlocfilehash: c0f1272bf195c6d5ef2dfe88cc6541f731fa51c8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928557"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Schnellstart: Senden von Telemetriedaten an einen Azure IoT-Hub und Lesen der Daten mit einer Java-Anwendung

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

In dieser Schnellstartanleitung senden Sie Telemetriedaten an Azure IoT Hub und lesen die Daten mit einer Java-Anwendung. IoT Hub ist ein Azure-Dienst, mit dem Sie umfangreiche Telemetriedaten von Ihren Geräten in der Cloud erfassen können, um sie zu speichern oder zu verarbeiten. In dieser Schnellstartanleitung werden zwei vorgefertigte Java-Anwendungen verwendet: eine zum Senden der Telemetriedaten und eine andere zum Lesen der Telemetriedaten aus dem Hub. Vor dem Ausführen dieser beiden Anwendungen erstellen Sie eine IoT Hub-Instanz und registrieren ein Gerät bei dieser Instanz.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* Java SE Development Kit 8. Wählen Sie unter [Langfristiger Java-Support für Azure und Azure Stack](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) unter **Langfristiger Support** die Option **Java 8** aus.

    Mit dem folgenden Befehl können Sie die aktuelle Java-Version auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi)

    Mit dem folgenden Befehl können Sie die aktuelle Maven-Version auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    mvn --version
    ```

* Führen Sie den Vorgang zum Herunterladen oder Klonen des Repositorys „azure-iot-samples-java“ durch, indem Sie auf der [Seite mit dem Repository „azure-iot-samples-java“](https://github.com/Azure-Samples/azure-iot-samples-java) die Schaltfläche **Code** verwenden. 

    In diesem Artikel werden die Beispiele [simulated-device](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/simulated-device) und [read-d2c-messages](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) aus dem Repository verwendet.

* Der Port 8883 muss in Ihrer Firewall geöffnet sein. Für das Beispielgerät in dieser Schnellstartanleitung wird das MQTT-Protokoll verwendet, das über den Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyJavaDevice**: Der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyJavaDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Führen Sie den folgenden Befehl in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

3. Darüber hinaus benötigen Sie den mit _Event Hubs kompatiblen Endpunkt_, den mit _Event Hubs kompatiblen Pfad_ und den _Dienstprimärschlüssel_ Ihres IoT-Hubs, um der Back-End-Anwendung das Herstellen einer Verbindung mit Ihrem IoT-Hub und das Abrufen der Nachrichten zu ermöglichen. Die folgenden Befehle rufen diese Werte für Ihre IoT Hub-Instanz ab:

     **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Notieren Sie sich diese drei Werte. Sie benötigen sie später in der Schnellstartanleitung.

## <a name="send-simulated-telemetry"></a>Senden simulierter Telemetriedaten

Die Anwendung zur Simulation eines Geräts stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her und sendet simulierte Telemetriedaten für Temperatur und Luftfeuchtigkeit.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des Java-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device**.

2. Öffnen Sie die Datei **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `connString` durch die Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.java**.

    ```java
    public class SimulatedDevice {
      // The device connection string to authenticate the device with your IoT hub.
      // Using the Azure CLI:
      // az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table

      //private static String connString = "{Your device connection string here}";    
      private static String connString = "HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}";    
     ```

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die simulierte Geräteanwendung zu erstellen:

    ```cmd/sh
    mvn clean package
    ```

4. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die simulierte Geräteanwendung auszuführen:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausgabe der Telemetrie, die vom Gerät an den IoT-Hub gesendet wurde](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

Die Back-End-Anwendung stellt eine Verbindung mit dem dienstseitigen Endpunkt **Events** in Ihrer IoT Hub-Instanz her. Die Anwendung empfängt die vom simulierten Gerät gesendeten Gerät-zu-Cloud-Nachrichten. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt, um Gerät-zu-Cloud-Nachrichten zu empfangen und zu verarbeiten.

1. Navigieren Sie in einem anderen lokalen Terminalfenster zum Stammordner des Java-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\read-d2c-messages**.

2. Öffnen Sie die Datei **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** in einem Text-Editor Ihrer Wahl. Aktualisieren Sie die folgenden Variablen, und speichern Sie Ihre Änderungen an der Datei.

    | Variable | Wert |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Ersetzen Sie den Wert der Variablen durch den mit Event Hubs kompatiblen Endpunkt, den Sie sich zuvor notiert haben. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Ersetzen Sie den Wert der Variablen durch den mit Event Hubs kompatiblen Pfad, den Sie sich zuvor notiert haben. |
    | `IOT_HUB_SAS_KEY`                | Ersetzen Sie den Wert der Variablen durch den Dienstprimärschlüssel, den Sie sich zuvor notiert haben. |

    ```java
    public class ReadDeviceToCloudMessages {
    
      private static final String EH_COMPATIBLE_CONNECTION_STRING_FORMAT = "Endpoint=%s/;EntityPath=%s;"
          + "SharedAccessKeyName=%s;SharedAccessKey=%s";
    
      // az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_ENDPOINT = "{your Event Hubs compatible endpoint}";
    
      // az iot hub show --query properties.eventHubEndpoints.events.path --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_PATH = "{your Event Hubs compatible name}";
    
      // az iot hub policy show --name service --query primaryKey --hub-name {your IoT Hub name}
      private static final String IOT_HUB_SAS_KEY = "{your service primary key}";    
    ```


3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Back-End-Anwendung zu erstellen:

    ```cmd/sh
    mvn clean package
    ```

4. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die Back-End-Anwendung auszuführen:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Back-End-Anwendung vom simulierten Gerät an den Hub gesendete Telemetriedaten empfängt:

    ![Ausgabe, wenn die Back-End-Anwendung die an den IoT-Hub gesendeten Telemetriedaten empfängt](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, mit einer Java-Anwendung simulierte Telemetriedaten an den Hub gesendet und mit einer einfachen Back-End-Anwendung die Telemetriedaten aus dem Hub gelesen.

Um zu erfahren, wie Sie das simulierte Gerät über eine Back-End-Anwendung steuern, fahren Sie mit der nächsten Schnellstartanleitung fort.

> [!div class="nextstepaction"]
> [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts](quickstart-control-device-java.md)
