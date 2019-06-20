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
ms.custom: mvc
ms.date: 03/26/2019
ms.openlocfilehash: 74146242ef4084e5990027117689bb1371c3f272
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832186"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie große Mengen von Telemetriedaten von Ihren IoT-Geräten in der Cloud erfassen und Ihre Geräte über die Cloud verwalten können. In dieser Schnellstartanleitung verwenden Sie eine *direkte Methode*, um ein simuliertes Gerät zu steuern, das mit Ihrer IoT Hub-Instanz verbunden ist. Sie können direkte Methoden verwenden, um das Verhalten eines mit Ihrer IoT Hub-Instanz verbundenen Geräts zu ändern.

In dieser Schnellstartanleitung werden zwei vorab geschriebene Python-Anwendungen verwendet:

* Eine simulierte Geräteanwendung, das auf direkte Methoden reagiert, die von einer Back-End-Anwendung aufgerufen werden. Um die Aufrufe der direkten Methode zu empfangen, stellt diese Anwendung eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her.

* Eine Back-End-Anwendung, die die direkten Methoden auf dem simulierten Gerät aufruft. Um eine direkte Methode auf einem Gerät aufzurufen, stellt diese Anwendung eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in Python geschrieben. Derzeit unterstützen die Microsoft Azure IoT SDKs für Python nur bestimmte Python-Versionen für jede Plattform. Weitere Informationen finden Sie in der [Infodatei zum Python SDK](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

In dieser Schnellstartanleitung wird davon ausgegangen, dass Sie einen Windows-Entwicklungscomputer verwenden. Auf Windows-Systemen wird ausschließlich [Python 3.6.x](https://www.python.org/downloads/release/python-368/) unterstützt. Das von Ihnen gewählte Python-Installationsprogramm sollte auf der Architektur des Systems basieren, mit dem Sie arbeiten. Verwenden Sie eine CPU-Systemarchitektur mit 32 Bit, laden Sie das x86-Installationsprogramm herunter. Für die 64-Bit-Architektur müssen Sie das x86-64-Programm herunterladen. Stellen Sie darüber hinaus sicher, dass [Microsoft Visual C++ Redistributable für Visual Studio 2015, 2017 und 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Architektur (x86 oder x64) installiert ist.

Sie können Python für andere Plattformen von [Python.org](https://www.python.org/downloads/) herunterladen.

Mit einem der folgenden Befehle können Sie die aktuelle Python-Version auf Ihrem Entwicklungscomputer überprüfen:

```python
python --version
```

```python
python3 --version
```

Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Laden Sie das Python-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv (falls Sie dies nicht bereits getan haben).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-python.md) absolviert haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-python.md) absolviert haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    **MyPythonDevice**: Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyPythonDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

3. Darüber hinaus benötigen Sie eine _Dienstverbindungszeichenfolge_, damit die Back-End-Anwendung eine Verbindung mit Ihrer IoT Hub-Instanz herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName \
      --output table
    ```

    Notieren Sie sich die Dienstverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt. Die Dienstverbindungszeichenfolge unterscheidet sich von der Geräteverbindungszeichenfolge.

## <a name="listen-for-direct-method-calls"></a>Lauschen auf direkte Methodenaufrufe

Die simulierte Geräteanwendung stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her, sendet simulierte Telemetriedaten und lauscht auf Aufrufe direkter Methoden aus Ihrem Hub. In dieser Schnellstartanleitung weist der Aufruf einer direkten Methode aus dem Hub das Gerät an, das Intervall zu ändern, in dem es Telemetriedaten sendet. Das simulierte Gerät sendet eine Bestätigung an Ihren Hub, nachdem es die direkte Methode ausgeführt hat.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des Python-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device-2**.

1. Öffnen Sie die Datei **SimulatedDevice.py** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `CONNECTION_STRING` durch die Geräteverbindungszeichenfolge, die sie sich zuvor notiert haben. Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.py**.

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die simulierte Geräteanwendung auszuführen:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Aufrufen der direkten Methode

Die Back-End-Anwendung stellt eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her. Die Anwendung sendet über Ihre IoT Hub-Instanz Aufrufe direkter Methoden an ein Gerät und lauscht auf Bestätigungen. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt.

1. Navigieren Sie in einem anderen lokalen Terminalfenster zum Stammordner des Python-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\back-end-application**.

1. Öffnen Sie die Datei **BackEndApplication.py** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `CONNECTION_STRING` durch die Dienstverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann Ihre Änderungen an der Datei **BackEndApplication.py**.

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die Back-End-Anwendung auszuführen:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Der folgende Screenshot zeigt die Ausgabe, nachdem die Anwendung einen Aufruf einer direkten Methode an das Gerät gerichtet und eine Bestätigung empfangen hat:

    ![Ausführen der Back-End-Anwendung](./media/quickstart-control-device-python/BackEndApplication.png)

    Nachdem Sie die Back-End-Anwendung ausgeführt haben, sehen Sie eine Nachricht im Konsolenfenster, in dem das simulierte Gerät ausgeführt wird, und die Häufigkeit, mit der das Gerät Nachrichten sendet, ändert sich:

    ![Änderung im simulierten Client](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie in einer Back-End-Anwendung eine direkte Methode auf einem Gerät aufgerufen und in einer simulierten Geräteanwendung auf den Aufruf einer direkten Methode geantwortet.

Um zu erfahren, wie Sie Gerät-zu-Cloud-Nachrichten an verschiedene Ziele in der Cloud weiterleiten, fahren Sie mit dem nächsten Tutorial fort.

> [!div class="nextstepaction"]
> [Tutorial: Weiterleiten von Telemetriedaten zur Verarbeitung an verschiedene Endpunkte](tutorial-routing.md)