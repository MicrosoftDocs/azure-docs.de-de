---
title: 'Schnellstart: Senden von Telemetriedaten an Azure IoT Hub (Python) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie eine Python-Beispielanwendung aus, um simulierte Telemetriedaten an eine IoT Hub-Instanz zu senden und mithilfe eines Hilfsprogramms Telemetriedaten aus der IoT Hub-Instanz zu lesen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: 03c7da3e17e8e606b46c5c5e104a1271e8fbfd33
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873116"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub und Lesen der Telemetriedaten mit einer Back-End-Anwendung (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie umfangreiche Telemetriedaten von Ihren Geräten in der Cloud erfassen können, um sie zu speichern oder zu verarbeiten. In dieser Schnellstartanleitung senden Sie Telemetriedaten von einer Anwendung zur Simulation eines Geräts über IoT Hub zur Verarbeitung an eine Back-End-Anwendung.

In dieser Schnellstartanleitung wird eine vorgefertigte Python-Anwendung verwendet, um die Telemetriedaten zu senden. Zum Lesen der Telemetriedaten aus IoT Hub wird ein CLI-Hilfsprogramm verwendet. Vor dem Ausführen dieser beiden Anwendungen erstellen Sie eine IoT Hub-Instanz und registrieren ein Gerät bei dieser Instanz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Die in dieser Schnellstartanleitung ausgeführte Beispielanwendung ist in Python geschrieben. Derzeit unterstützen die Microsoft Azure IoT SDKs für Python nur bestimmte Python-Versionen für jede Plattform. Weitere Informationen finden Sie in der [Infodatei zum Python SDK](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

In dieser Schnellstartanleitung wird davon ausgegangen, dass Sie einen Windows-Entwicklungscomputer verwenden. Auf Windows-Systemen wird ausschließlich [Python 3.6.x](https://www.python.org/downloads/release/python-368/) unterstützt. Das von Ihnen gewählte Python-Installationsprogramm sollte auf der Architektur des Systems basieren, mit dem Sie arbeiten. Wenn Sie eine CPU-Systemarchitektur mit 32 Bit verwenden, laden Sie das x86-Installationsprogramm herunter. Laden Sie für eine 64-Bit-Architektur das x86-64-Installationsprogramm herunter. Stellen Sie darüber hinaus sicher, dass [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Architektur (x86 oder x64) installiert ist.

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

Laden Sie das Python-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    **MyPythonDevice:** Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyPythonDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

## <a name="send-simulated-telemetry"></a>Senden simulierter Telemetriedaten

Die Anwendung zur Simulation eines Geräts stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her und sendet simulierte Telemetriedaten für Temperatur und Luftfeuchtigkeit.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des Python-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device**.

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

    ![Ausführen des simulierten Geräts](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

Die IoT Hub-CLI-Erweiterung stellt eine Verbindung mit dem dienstseitigen Endpunkt **Events** in Ihrer IoT Hub-Instanz her. Die Erweiterung empfängt die vom simulierten Gerät gesendeten Gerät-zu-Cloud-Nachrichten. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt, um Gerät-zu-Cloud-Nachrichten zu empfangen und zu verarbeiten.

Führen Sie in Azure Cloud Shell die folgenden Befehle aus, und ersetzen Sie dabei `YourIoTHubName` durch den Namen Ihres IoT-Hubs:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

Der folgende Screenshot zeigt die Ausgabe, während die Erweiterung die vom simulierten Gerät an den Hub gesendeten Telemetriedaten empfängt:

![Ausführen der Back-End-Anwendung](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine IoT Hub-Instanz eingerichtet, ein Gerät registriert, mit einer Python-Anwendung simulierte Telemetriedaten an die Hub-Instanz gesendet und mit einer einfachen Back-End-Anwendung die Telemetriedaten aus der Hub-Instanz gelesen.

Um zu erfahren, wie Sie das simulierte Gerät über eine Back-End-Anwendung steuern, fahren Sie mit der nächsten Schnellstartanleitung fort.

> [!div class="nextstepaction"]
> [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts](quickstart-control-device-python.md)