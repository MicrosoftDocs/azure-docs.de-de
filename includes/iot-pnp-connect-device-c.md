---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 29ab00de030aa270ed33d076333cb06cd73b71d4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511638"
---
In diesem Schnellstart erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung erstellen, mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung ist in C geschrieben und im Azure IoT-Geräte-SDK für C enthalten. Ein Lösungsentwickler kann das Tool Azure IoT-Explorer verwenden, um mehr über die Funktionen eines IoT Plug & Play-Geräts zu erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Dieser Schnellstart kann unter Linux oder Windows ausgeführt werden. Die Shellbefehle in dieser Schnellstartanleitung entsprechen der Linux-Konvention für die Pfadtrennzeichen „`/`“. Wenn Sie den Anleitungen unter Windows folgen, achten Sie darauf, diese Trennzeichen durch „`\`“ zu ersetzen.

Die Voraussetzungen sind je nach Betriebssystem unterschiedlich:

### <a name="linux"></a>Linux

In diesem Schnellstart wird davon ausgegangen, dass Sie Ubuntu Linux verwenden. Die Schritte in dieser Schnellstartanleitung wurden unter Ubuntu 18.04 getestet.

Installieren Sie die folgende Software in Ihrer lokalen Linux-Umgebung, um diesen Schnellstart unter Linux auszuführen:

Installieren Sie **GCC**, **Git**, **cmake** und alle erforderlichen Abhängigkeiten mit dem Befehl `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Überprüfen Sie, ob die Version von `cmake` höher als **2.8.12** und die Version von **GCC** höher als **4.4.7** ist.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um diesen Schnellstart unter Windows auszuführen:

* [Visual Studio (Community, Professional oder Enterprise):](https://visualstudio.microsoft.com/downloads/) Stellen Sie sicher, dass Sie die Workload **Desktopentwicklung mit C++** aktivieren, wenn Sie Visual Studio [installieren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Laden Sie den Code herunter.

In diesem Schnellstart bereiten Sie eine Entwicklungsumgebung vor, die Sie zum Klonen und Erstellen des Azure IoT Hub-Geräte-SDK für C verwenden können.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl zum Klonen des GitHub-Repositorys für das [Azure IoT-C-SDK und die zugehörigen Bibliotheken](https://github.com/Azure/azure-iot-sdk-c) an diesem Speicherort aus:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

## <a name="build-the-code"></a>Erstellen des Codes

Sie verwenden das Geräte-SDK, um den enthaltenen Beispielcode zu erstellen:

1. Erstellen Sie im Stammordner des Geräte-SDK den Unterordner _cmake_, und navigieren Sie zu diesem Ordner:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Führen Sie die folgenden Befehle aus, um das SDK und die Beispiele zu erstellen:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> Unter Windows können Sie die Projektmappe öffnen, die mit dem Befehl `cmake` in Visual Studio 2019 generiert wurde. Öffnen Sie die Projektdatei *azure_iot_sdks.sln* im Verzeichnis _cmake_, und legen Sie das Projekt **pnp_simple_thermostat** als Startprojekt in der Projektmappe fest. Jetzt können Sie das Beispiel in Visual Studio erstellen und im Debugmodus ausführen.

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Gehen Sie folgendermaßen vor, um die Beispielanwendung im SDK auszuführen, mit der ein IoT Plug & Play-Gerät simuliert wird, das Telemetriedaten an Ihren IoT-Hub sendet:

Navigieren Sie im Ordner _cmake_ zu dem Ordner, der die ausführbare Datei enthält, und führen Sie diese aus:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Wenn Sie die Codeausführung in Visual Studio unter Windows verfolgen möchten, fügen Sie der Funktion `main` in der Datei _pnp_simple_thermostat.c_ einen Haltepunkt hinzu.

Das Gerät ist jetzt bereit zum Empfangen von Befehlen und Eigenschaftsaktualisierungen, und es hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein einfacher IoT Plug & Play-Thermostat implementiert. Das in diesem Beispiel implementierte Modell verwendet keine IoT Plug & Play-[Komponenten](../articles/iot-pnp/concepts-components.md). Die [DTDL-Modelldatei für den Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiert die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle.

Der Gerätecode verwendet die Standardfunktion zum Herstellen einer Verbindung mit Ihrem IoT-Hub:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Das Gerät sendet die Modell-ID des in der Verbindungsanforderung implementierten DTDL-Modells. Ein Gerät, das eine Modell-ID sendet, ist ein IoT Plug & Play-Gerät:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Der Code, der Eigenschaften aktualisiert, Befehle verarbeitet und Telemetriedaten sendet, ist identisch mit dem Code für ein Gerät, das keine IoT Plug & Play-Konventionen verwendet.

Im Code werden JSON-Objekte mithilfe der Parson-Bibliothek in den von Ihrem IoT-Hub gesendeten Nutzlasten analysiert:

```c
// JSON parser
#include "parson.h"
```
