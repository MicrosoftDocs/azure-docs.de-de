---
title: 'Schnellstart: Senden von Telemetriedaten an Azure IoT Hub (C) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei C-Beispielanwendungen aus, um simulierte Telemetriedaten an eine IoT Hub-Instanz zu senden und zur Verarbeitung in der Cloud aus der IoT Hub-Instanz zu lesen.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 92575f2fc8e6dbcfc5767a179ddf60df1bce0c83
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872601"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie umfangreiche Telemetriedaten von Ihren Geräten in der Cloud erfassen können, um sie zu speichern oder zu verarbeiten. In dieser Schnellstartanleitung senden Sie Telemetriedaten von einer Anwendung zur Simulation eines Geräts über IoT Hub zur Verarbeitung an eine Back-End-Anwendung.

Die Schnellstartanleitung verwendet eine C-Beispielanwendung aus dem [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md), um Telemetriedaten an einen IoT-Hub zu senden. Die Azure IoT-Geräte-SDKs wurden in [ANSI C (C99)](https://wikipedia.org/wiki/C99) geschrieben und sind auf Portabilität und breit gefächerte Plattformkompatibilität ausgelegt. Bevor Sie den Beispielcode ausführen, erstellen Sie einen IoT-Hub und registrieren das simulierte Gerät bei diesem Hub.

Dieser Artikel wurde für Windows geschrieben, Sie können die Schnellstartanleitung aber auch unter Linux ausführen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/vs/) mit der aktivierten Workload [Desktopentwicklung mit C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/).
* Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Für diesen Schnellstart verwenden Sie das [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md). 

Sie können das SDK verwenden, indem Sie die Pakete und Bibliotheken für die folgenden Umgebungen installieren:

* **Linux:** apt-get-Pakete sind für Ubuntu 16.04 und 18.04 mit folgenden CPU-Architekturen verfügbar: amd64, arm64, armhf und i386. Weitere Informationen finden Sie unter [Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) (Verwenden von apt-get zum Erstellen eines C-Geräteclientprojekts unter Ubuntu).

* **mbed:** Für Entwickler, die Geräteanwendungen auf der mbed-Plattform erstellen, haben wir eine Bibliothek und Beispiele veröffentlicht, mit denen Sie in wenigen Minuten mit Azure IoT Hub beginnen können. Weitere Informationen finden Sie unter [Use the mbed library](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed) (Verwenden der mbed-Bibliothek).

* **Arduino:** Wenn Sie mit Arduino entwickeln, können Sie die Azure-IoT-Bibliothek verwenden, die im Bibliotheks-Manager der Arduino-IDE zur Verfügung steht. Weitere Informationen finden Sie unter [The Azure IoT Hub library for Arduino](https://github.com/azure/azure-iot-arduino) (Die Azure-IoT-Hub-Bibliothek für Arduino).

* **iOS:** Das IoT Hub-Geräte-SDK ist als CocoaPods für die Entwicklung von Mac- und iOS-Geräten verfügbar. Weitere Informationen finden Sie unter [iOS Samples for Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient) (iOS-Beispiele für Microsoft Azure IoT).

In diesem Schnellstart bereiten Sie eine Entwicklungsumgebung vor, die zum Klonen und Erstellen des [Azure-IoT-C-SDK](https://github.com/Azure/azure-iot-sdk-c) aus GitHub verwendet wird. Das SDK auf GitHub enthält den in dieser Schnellstartanleitung verwendeten Beispielcode. 

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die Voraussetzungen für Visual Studio (Visual Studio und die Workload „Desktopentwicklung mit C++“) müssen **vor** Beginn der Installation von `CMake` auf dem Computer installiert sein. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem.

2. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie den folgenden Befehl zum Klonen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.


3. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsclientplattform. Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert. 

    ```cmd
    cmake ..
    ```
    
    Wenn `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs) aus. 

    Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest), um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

   **MyCDevice:** Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyCDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das registrierte Gerät abzurufen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

## <a name="send-simulated-telemetry"></a>Senden simulierter Telemetriedaten

Die Anwendung des simulierten Geräts stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her und sendet simulierte Telemetriedaten in Form einer Zeichenfolge.

1. Öffnen Sie die Quelldatei „iothub_convenience_sample.c“ in einem Text-Editor, und überprüfen Sie den Beispielcode für das Senden von Telemetriedaten. Die Datei befindet sich an folgendem Speicherort:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Suchen Sie die Deklaration der Konstanten `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Ersetzen Sie den Wert der Konstanten `connectionString` durch die Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie Ihre Änderungen dann in **iothub_convenience_sample.c**.

3. Navigieren Sie in einem lokalen Terminalfenster zum Projektverzeichnis *iothub_convenience_sample* in dem CMake-Verzeichnis, das Sie im Azure IoT C SDK erstellt haben.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Führen Sie in Ihrem lokalen Terminalfenster CMake aus, um das Beispiel mit Ihrem aktualisierten Wert für `connectionString` zu erstellen:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Führen Sie in einem lokalen Terminalfenster den folgenden Befehl aus, um die simulierte Geräteanwendung auszuführen:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die simulierte Geräteanwendung Telemetriedaten an die IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub


In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest), um die Gerätenachrichten zu überwachen, die von dem simulierten Gerät gesendet werden.

1. Führen Sie über Azure Cloud Shell den folgenden Befehl aus, um eine Verbindung mit Ihrem IoT-Hub herzustellen und Nachrichten zu lesen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Lesen der Gerätenachrichten mithilfe der Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, mit einer C-Anwendung simulierte Telemetriedaten an den Hub gesendet und mithilfe von Azure Cloud Shell die Telemetriedaten aus dem Hub gelesen.

Weitere Informationen zum Entwickeln mit dem Azure IoT Hub C SDK finden Sie in der folgenden Schrittanleitung:

> [!div class="nextstepaction"]
> [Entwickeln mit dem Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)