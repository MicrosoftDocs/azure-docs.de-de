---
title: 'Schnellstart: Steuern eines Geräts über Azure IoT Hub (Python) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei Python-Beispielanwendungen aus. Eine dieser Anwendungen ist eine Back-End-Anwendung, die mit Ihrem Hub verbundene Geräte remote steuern kann. Die andere Anwendung simuliert ein Gerät, das mit Ihrem Hub verbunden ist und remote gesteuert werden kann.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: 5db51e78a6770a642728cc058f425baf7c7e095b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832159"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In dieser Schnellstartanleitung verwenden Sie eine direkte Methode, um ein simuliertes Gerät zu steuern, das mit Azure IoT Hub verbunden ist. IoT Hub ist ein Azure-Dienst, mit dem Sie Ihre IoT-Geräte über die Cloud verwalten und große Mengen von Gerätetelemetriedaten zum Speichern oder Verarbeiten in der Cloud erfassen können. Sie können direkte Methoden verwenden, um das Verhalten eines mit Ihrer IoT Hub-Instanz verbundenen Geräts zu ändern. In dieser Schnellstartanleitung werden zwei Python-Anwendungen verwendet: eine simulierte Geräteanwendung, die auf direkte Methoden reagiert, die von einer Back-End-Anwendung aufgerufen werden, und eine Back-End-Anwendung, die die direkten Methoden auf dem simulierten Gerät aufruft.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7 oder höher](https://www.python.org/downloads/). Informationen zu anderen unterstützten Python-Versionen finden Sie im Artikel zu den [Azure IoT-Gerätefeatures](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Ein Python-Beispielprojekt](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip)

* Der Port 8883 muss in Ihrer Firewall geöffnet sein. Für das Beispielgerät in dieser Schnellstartanleitung wird das MQTT-Protokoll verwendet, das über den Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-python.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-python.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    **MyPythonDevice:** Der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyPythonDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

3. Darüber hinaus benötigen Sie eine _Dienstverbindungszeichenfolge_, damit die Back-End-Anwendung eine Verbindung mit Ihrer IoT Hub-Instanz herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Notieren Sie sich die Dienstverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt. Die Dienstverbindungszeichenfolge unterscheidet sich von der Geräteverbindungszeichenfolge, die Sie sich im vorherigen Schritt notiert haben.

## <a name="listen-for-direct-method-calls"></a>Lauschen auf Aufrufe direkter Methoden

Die simulierte Geräteanwendung stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her, sendet simulierte Telemetriedaten und lauscht auf Aufrufe direkter Methoden aus Ihrem Hub. In dieser Schnellstartanleitung weist der Aufruf einer direkten Methode aus dem Hub das Gerät an, das Intervall zu ändern, in dem es Telemetriedaten sendet. Das simulierte Gerät sendet eine Bestätigung an Ihren Hub, nachdem es die direkte Methode ausgeführt hat.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des Python-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device-2**.

1. Öffnen Sie die Datei **SimulatedDevice.py** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `CONNECTION_STRING` durch die Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.py**.

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die simulierte Geräteanwendung auszuführen:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Aufrufen der direkten Methode

Die Back-End-Anwendung stellt eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her. Die Anwendung sendet über Ihren IoT-Hub Aufrufe direkter Methoden an ein Gerät und lauscht auf Bestätigungen. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt.

1. Navigieren Sie in einem anderen lokalen Terminalfenster zum Stammordner des Python-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\back-end-application**.

1. Öffnen Sie die Datei **BackEndApplication.py** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `CONNECTION_STRING` durch die Dienstverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann Ihre Änderungen an der Datei **BackEndApplication.py**.

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die Back-End-Anwendung auszuführen:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Der folgende Screenshot zeigt die Ausgabe, nachdem die Anwendung einen Aufruf einer direkten Methode an das Gerät gerichtet und eine Bestätigung empfangen hat:

    ![Ausführen der Back-End-Anwendung](./media/quickstart-control-device-python/backend-application.png)

    Nachdem Sie die Back-End-Anwendung ausgeführt haben, sehen Sie eine Nachricht im Konsolenfenster, in dem das simulierte Gerät ausgeführt wird, und die Häufigkeit, mit der das Gerät Nachrichten sendet, ändert sich:

    ![Änderung im simulierten Client](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie in einer Back-End-Anwendung eine direkte Methode auf einem Gerät aufgerufen und in einer simulierten Geräteanwendung auf den Aufruf einer direkten Methode geantwortet.

Um zu erfahren, wie Sie Gerät-zu-Cloud-Nachrichten an verschiedene Ziele in der Cloud weiterleiten, fahren Sie mit dem nächsten Tutorial fort.

> [!div class="nextstepaction"]
> [Tutorial: Weiterleiten von Telemetriedaten zur Verarbeitung an verschiedene Endpunkte](tutorial-routing.md)
