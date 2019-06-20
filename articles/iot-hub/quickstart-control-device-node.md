---
title: 'Schnellstart: Steuern eines Geräts über Azure IoT Hub (Node.js) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei Node.js-Beispielanwendungen aus. Eine dieser Anwendungen ist eine Back-End-Anwendung, die mit Ihrem Hub verbundene Geräte remote steuern kann. Die andere Anwendung simuliert ein Gerät, das mit Ihrem Hub verbunden ist und remote gesteuert werden kann.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/27/2019
ms.openlocfilehash: 15b96d44b1ff78a83a4364579b7984d20e3f4d85
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597715"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-nodejs"></a>Schnellstart: Steuern eines mit einem IoT-Hub verbundenen Geräts (Node.js)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie große Mengen von Telemetriedaten von Ihren IoT-Geräten in der Cloud erfassen und Ihre Geräte über die Cloud verwalten können. In dieser Schnellstartanleitung verwenden Sie eine *direkte Methode*, um ein simuliertes Gerät zu steuern, das mit Ihrer IoT Hub-Instanz verbunden ist. Sie können direkte Methoden verwenden, um das Verhalten eines mit Ihrer IoT Hub-Instanz verbundenen Geräts zu ändern.

In dieser Schnellstartanleitung werden zwei vorab geschriebene Node.js-Anwendungen verwendet:

* Eine Anwendung zur Simulation eines Geräts, die auf direkte Methoden reagiert, die von einer Back-End-Anwendung aufgerufen werden. Um die Aufrufe der direkten Methode zu empfangen, stellt diese Anwendung eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her.

* Eine Back-End-Anwendung, die die direkten Methoden auf dem simulierten Gerät aufruft. Um eine direkte Methode auf einem Gerät aufzurufen, stellt diese Anwendung eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in Node.js geschrieben. Sie benötigen mindestens Node.js v10.x.x auf Ihrem Entwicklungscomputer.

Sie können Node.js für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Laden Sie das Node.js-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv (falls Sie dies nicht bereits getan haben).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-node.md) absolviert haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-node.md) absolviert haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyNodeDevice**: Der Name des Geräts, das Sie registrieren. Verwenden Sie **MyNodeDevice** wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyNodeDevice \
      --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

3. Darüber hinaus benötigen Sie eine _Dienstverbindungszeichenfolge_, damit die Back-End-Anwendung eine Verbindung mit Ihrer IoT Hub-Instanz herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName --output table
    ```

    Notieren Sie sich die Dienstverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt. Die Dienstverbindungszeichenfolge unterscheidet sich von der Geräteverbindungszeichenfolge.

## <a name="listen-for-direct-method-calls"></a>Lauschen auf direkte Methodenaufrufe

Die simulierte Geräteanwendung stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her, sendet simulierte Telemetriedaten und lauscht auf Aufrufe direkter Methoden aus Ihrem Hub. In dieser Schnellstartanleitung weist der Aufruf einer direkten Methode aus dem Hub das Gerät an, das Intervall zu ändern, in dem es Telemetriedaten sendet. Das simulierte Gerät sendet eine Bestätigung an Ihren Hub, nachdem es die direkte Methode ausgeführt hat.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des Node.js-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device-2**.

2. Öffnen Sie die Datei **SimulatedDevice.js** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `connectionString` durch die Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.js**.

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Anwendung zur Simulation eines Geräts auszuführen:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](./media/quickstart-control-device-node/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Aufrufen der direkten Methode

Die Back-End-Anwendung stellt eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her. Die Anwendung sendet über Ihre IoT Hub-Instanz Aufrufe direkter Methoden an ein Gerät und lauscht auf Bestätigungen. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt.

1. Navigieren Sie in einem weiteren lokalen Terminalfenster zum Stammordner des Node.js-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\back-end-application**.

2. Öffnen Sie die Datei **BackEndApplication.js** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `connectionString` durch die Dienstverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann Ihre Änderungen an der Datei **BackEndApplication.js**.

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Back-End-Anwendung auszuführen:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    Der folgende Screenshot zeigt die Ausgabe, nachdem die Anwendung einen Aufruf einer direkten Methode an das Gerät gerichtet und eine Bestätigung empfangen hat:

    ![Ausführen der Back-End-Anwendung](./media/quickstart-control-device-node/BackEndApplication.png)

    Nachdem Sie die Back-End-Anwendung ausgeführt haben, sehen Sie eine Nachricht im Konsolenfenster, in dem das simulierte Gerät ausgeführt wird, und die Häufigkeit, mit der das Gerät Nachrichten sendet, ändert sich:

    ![Änderung im simulierten Client](./media/quickstart-control-device-node/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie in einer Back-End-Anwendung eine direkte Methode auf einem Gerät aufgerufen und in einer simulierten Geräteanwendung auf den Aufruf einer direkten Methode geantwortet.

Um zu erfahren, wie Sie Gerät-zu-Cloud-Nachrichten an verschiedene Ziele in der Cloud weiterleiten, fahren Sie mit dem nächsten Tutorial fort.

> [!div class="nextstepaction"]
> [Tutorial: Weiterleiten von Telemetriedaten zur Verarbeitung an verschiedene Endpunkte](tutorial-routing.md)