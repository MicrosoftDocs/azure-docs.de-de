---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 190e2144e92ffd77c098b4bf9205de03e55af7df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511261"
---
In diesem Tutorial erfahren Sie, wie Sie eine exemplarische IoT Plug & Play-Geräteanwendung mit Komponenten erstellen, mit Ihrem IoT-Hub verbinden und die an den Hub gesendeten Informationen mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung ist in C geschrieben und im Azure IoT-Geräte-SDK für C enthalten. Ein Lösungsentwickler kann das Tool Azure IoT-Explorer verwenden, um mehr über die Funktionen eines IoT Plug & Play-Geräts zu erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Sie können dieses Tutorial unter Linux oder Windows durcharbeiten. Die Shellbefehle in diesem Tutorial entsprechen der Linux-Konvention für die Pfadtrennzeichen `/`. Wenn Sie den Anleitungen unter Windows folgen, sollten Sie diese Trennzeichen durch `\` ersetzen.

Die Voraussetzungen sind je nach Betriebssystem unterschiedlich:

### <a name="linux"></a>Linux

In diesem Tutorial wird davon ausgegangen, dass Sie Ubuntu Linux nutzen. Die Schritte in diesem Tutorial wurden unter Ubuntu 18.04 getestet.

Installieren Sie die folgende Software in Ihrer lokalen Linux-Umgebung, um dieses Tutorial unter Linux durchzuarbeiten:

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

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um dieses Tutorial unter Windows ausführen zu können:

* [Visual Studio (Community, Professional oder Enterprise):](https://visualstudio.microsoft.com/downloads/) Stellen Sie sicher, dass Sie die Workload **Desktopentwicklung mit C++** aktivieren, wenn Sie Visual Studio [installieren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Laden Sie den Code herunter.

Wenn Sie den [Schnellstart: Verbinden einer unter Linux oder Windows ausgeführten IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub (C)](../articles/iot-pnp/quickstart-connect-device.md) abgeschlossen haben, wurde der Code bereits heruntergeladen.

In diesem Tutorial wird eine Entwicklungsumgebung vorbereitet, die Sie zum Klonen und Erstellen des Azure IoT Hub-Geräte-SDK für C verwenden können.

Öffnen Sie eine Eingabeaufforderung in einem Ordner Ihrer Wahl. Führen Sie den folgenden Befehl zum Klonen des GitHub-Repositorys für das [Azure IoT-C-SDK und die zugehörigen Bibliotheken](https://github.com/Azure/azure-iot-sdk-c) an diesem Speicherort aus:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

## <a name="build-and-run-the-code"></a>Erstellen und Ausführen des Codes

Sie können den Code erstellen und ausführen, indem Sie Visual Studio oder `cmake` in der Befehlszeile verwenden.

### <a name="use-visual-studio"></a>Verwenden von Visual Studio

1. Öffnen Sie den Stammordner des geklonten Repositorys. Nach einigen Sekunden werden über die **CMake**-Unterstützung in Visual Studio alle Komponenten erstellt, die Sie zum Ausführen und Debuggen des Projekts benötigen.
1. Navigieren Sie im **Projektmappen-Explorer** zum Beispiel *iothub_client/samples/pnp/pnp_temperature_controller/* , wenn der Vorgang in Visual Studio abgeschlossen ist.
1. Klicken Sie mit der rechten Maustaste auf die Datei *pnp_temperature_controller.c*, und wählen Sie die Option **Debugkonfiguration hinzufügen** aus. Wählen Sie **Default** (Standard) aus.
1. In Visual Studio wird die Datei *launch.vs.json* geöffnet. Bearbeiten Sie diese Datei wie im folgenden Codeausschnitt gezeigt, um die erforderlichen Umgebungsvariablen festzulegen. Sie haben sich nach Abschluss des Vorgangs [Einrichten Ihrer Umgebung für die IoT Plug & Play-Schnellstarts und -Tutorials](../articles/iot-pnp/set-up-environment.md) die Bereichs-ID und den Primärschlüssel für die Registrierung notiert:

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Klicken Sie mit der rechten Maustaste auf die Datei *pnp_temperature_controller.c*, und wählen Sie die Option **Als Startelement festlegen** aus.
1. Fügen Sie zum Nachverfolgen der Codeausführung in Visual Studio der Funktion `main` in der Datei *pnp_temperature_controller.c* einen Breakpoint hinzu.
1. Sie können das Beispiel jetzt über das Menü **Debuggen** ausführen und debuggen.

Das Gerät ist jetzt zum Empfangen von Befehlen und Eigenschaftsaktualisierungen bereit und hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

### <a name="use-cmake-at-the-command-line"></a>Verwenden von `cmake` in der Befehlszeile

So erstellen Sie das Beispiel:

1. Erstellen Sie im Stammordner des geklonten Geräte-SDK den Unterordner _cmake_, und navigieren Sie zu diesem Ordner:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Führen Sie die folgenden Befehle aus, um die Projektdateien für das SDK und die Beispiele zu generieren und zu erstellen:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

So führen Sie das Beispiel aus:

1. Navigieren Sie im Ordner _cmake_ zu dem Ordner, der die ausführbare Datei enthält, und führen Sie diese aus:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Das Gerät ist jetzt zum Empfangen von Befehlen und Eigenschaftsaktualisierungen bereit und hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Verwenden von Azure IoT-Explorer zum Überprüfen des Codes

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein IoT Plug & Play-Temperaturregler implementiert. Bei diesem Beispiel wird ein Modell mit [mehreren Komponenten](../articles/iot-pnp/concepts-components.md) implementiert. In der [DTDL-Modelldatei (Digital Twins Definition Language) für das Temperaturgerät](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) werden die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle definiert.

### <a name="iot-plug-and-play-helper-functions"></a>Hilfsfunktionen für IoT Plug & Play

In diesem Beispiel werden im Code einige Hilfsfunktionen aus dem Ordner */common* genutzt:

*pnp_device_client_ll* enthält die Verbindungsmethode für IoT Plug & Play mit der `model-id` als Parameter: `PnP_CreateDeviceClientLLHandle`.

*pnp_protocol* enthält die Hilfsfunktionen für IoT Plug & Play:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Diese Hilfsfunktionen sind so generisch gehalten, dass Sie sie auch in Ihrem eigenen Projekt verwenden können. In diesem Beispiel werden sie in den drei Dateien verwendet, die den einzelnen Komponenten des Modells entsprechen:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

In der Datei *pnp_deviceinfo_component* werden in der Funktion `SendReportedPropertyForDeviceInformation` beispielsweise zwei Hilfsfunktionen genutzt:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Dieses Muster gilt für alle Komponenten des Beispiels.

### <a name="code-flow"></a>Codeflow

Mit der Funktion `main` wird die Verbindung initialisiert und die Modell-ID gesendet:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Im Code wird `PnP_CreateDeviceClientLLHandle` genutzt, um eine Verbindung mit dem IoT-Hub herzustellen, `modelId` als Option festzulegen und die Gerätemethode und Gerätezwilling-Rückrufhandler für direkte Methoden und Gerätezwillingsaktualisierungen festzulegen:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` enthält auch die Verbindungsinformationen. Die Umgebungsvariable **IOTHUB_DEVICE_SECURITY_TYPE** bestimmt, ob im Beispiel eine Verbindungszeichenfolge oder der Device Provisioning-Dienst zum Herstellen der Verbindung mit dem IoT-Hub genutzt wird.

Wenn vom Gerät eine Modell-ID gesendet wird, wird es zu einem IoT Plug & Play-Gerät.

Nachdem die Rückrufhandler eingerichtet wurden, reagiert das Gerät auf Zwillingsaktualisierungen und Aufrufe direkter Methoden:

* Für den Gerätezwillingsrückruf wird von `PnP_TempControlComponent_DeviceTwinCallback` die Funktion `PnP_ProcessTwinData` zum Verarbeiten der Daten aufgerufen. Für `PnP_ProcessTwinData` wird das *Besuchermuster* verwendet, um den JSON-Code zu analysieren und anschließend die einzelnen Eigenschaften durchzugehen. Hierbei wird für jedes Element `PnP_TempControlComponent_ApplicationPropertyCallback` aufgerufen.

* Beim Befehlsrückruf wird von der Funktion `PnP_TempControlComponent_DeviceMethodCallback` die Hilfsfunktion verwendet, um die Befehls- und Komponentennamen zu analysieren:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    Mit der Funktion `PnP_TempControlComponent_DeviceMethodCallback` wird anschließend der Befehl für die Komponente aufgerufen:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

Mit der Funktion `main` werden die schreibgeschützten Eigenschaften initialisiert, die an den IoT-Hub gesendet werden:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

Die Funktion `main` wird als Schleife ausgeführt, um die Ereignis- und Telemetriedaten für die einzelnen Komponenten zu aktualisieren:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

Die Funktion `PnP_ThermostatComponent_SendTelemetry` veranschaulicht, wie Sie die Struktur `PNP_THERMOSTAT_COMPONENT` verwenden. Im Beispiel wird diese Struktur genutzt, um Informationen zu den zwei Thermostaten im Temperaturregler zu speichern. Im Code wird die Funktion `PnP_CreateTelemetryMessageHandle` zum Vorbereiten und Senden der Nachricht verwendet:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

Mit der Funktion `main` werden die unterschiedlichen Komponenten zerstört, und die Verbindung mit dem Hub wird getrennt.

[!INCLUDE [iot-pnp-clean-resources.md](iot-pnp-clean-resources.md)]
