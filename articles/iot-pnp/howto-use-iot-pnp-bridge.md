---
title: Herstellen einer Verbindung eines IoT Plug & Play-Bridgebeispiels unter Linux oder Windows mit einem IoT-Hub | Microsoft-Dokumentation
description: Erstellen Sie unter Linux oder Windows eine IoT Plug & Play-Bridge, die eine Verbindung mit einem IoT-Hub herstellt, und führen Sie sie aus. Verwenden Sie das Tool Azure IoT-Explorer, um die vom Gerät an den Hub gesendeten Informationen anzuzeigen.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834101"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Herstellen einer Verbindung eines IoT Plug & Play-Bridgebeispiels unter Linux oder Windows mit IoT-Hub

In diesem Artikel erfahren Sie, wie Sie den Beispielumgebungsadapter der IoT Plug & Play-Bridge erstellen, ihn mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die IoT Plug and Play-Bridge ist in C geschrieben und schließt das Azure IoT-Geräte-SDK für C ein. An Ende dieses Tutorials sollten Sie in der Lage sein, die IoT Plug & Play-Bridge auszuführen und sie Telemetriedaten im Azure IoT-Explorer ausgeben zu sehen:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Ein Screenshot des Azure IoT-Explorers mit einer Tabelle der gemeldeten Telemetriedaten (Feuchtigkeit, Temperatur) aus der Iot Plug & Play-Bridge.":::

## <a name="prerequisites"></a>Voraussetzungen

Sie können das Beispiel im Artikel unter Windows oder Linux ausführen. Die Shellbefehle in dieser Schnellstartanleitung entsprechen der Windows-Konvention für die Pfadtrennzeichen `\`. Wenn Sie den Anleitungen unter Linux folgen, sollten Sie diese Trennzeichen durch `/` ersetzen.

### <a name="azure-iot-explorer"></a>Azure IoT-Explorer

Wenn Sie im zweiten Teil dieses Artikels mit dem Beispielgerät interagieren möchten, verwenden Sie das Tool **Azure IoT-Explorer**. Dazu müssen Sie [die neueste Version von Azure IoT-Explorer für Ihr Betriebssystem herunterladen und installieren](./howto-use-iot-explorer.md).

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Führen Sie den folgenden Befehl aus, um die _IoT-Hub-Verbindungszeichenfolge_ für Ihren Hub abzurufen. Notieren Sie sich diese Verbindungszeichenfolge zur späteren Verwendung in diesem Artikel:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Führen Sie den folgenden Befehl aus, um die _Geräteverbindungszeichenfolge_ für das dem Hub hinzugefügte Gerät abzurufen. Notieren Sie sich diese Verbindungszeichenfolge zur späteren Verwendung in diesem Artikel:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Herunterladen und Ausführen der Bridge

In diesem Artikel haben Sie zwei Möglichkeiten zur Ausführung der Bridge. Ihre Möglichkeiten:

- Laden Sie eine vordefinierte ausführbare Datei herunter, und führen Sie sie entsprechend der Beschreibung in diesem Abschnitt aus.
- Laden Sie den Quellcode herunter, [erstellen Sie die Bridge, und führen Sie sie aus](#build-and-run-the-bridge), wie im folgenden Abschnitt beschrieben wird.

So können Sie die Bridge herunterladen und ausführen:

1. Wechseln Sie zur [Seite mit den Releases](https://github.com/Azure/iot-plug-and-play-bridge/releases) für IoT Plug & Play.
1. Laden Sie die vordefinierte ausführbare Datei für Ihr Betriebssystem herunter: **pnpbridge_bin. exe** für Windows oder **pnpbridge_bin** für Linux.
1. Laden Sie die Beispielkonfigurationsdatei [config.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) für das Beispiel „Umgebungssensor“ herunter. Stellen Sie sicher, dass die Konfigurationsdatei in demselben Ordner wie die ausführbare Datei gespeichert wird.
1. Bearbeiten Sie die Datei *config.json*:

    - Fügen Sie den Wert `connection-string` hinzu. Dies ist die _Verbindungszeichenfolge_ für Geräte, die Sie sich vorher notiert haben.
    - Fügen Sie den Wert `symmetric_key` hinzu. Dies ist der Wert des Schlüssels für den gemeinsamen Zugriff aus der _Verbindungszeichenfolge_.
    - Ersetzen Sie den Wert `root_interface_model_id` durch `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`.

    Der erste Abschnitt der Datei *config.json*  sieht jetzt wie der folgende Codeausschnitt aus:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Führen Sie die ausführbare Datei in Ihrer Befehlszeilenumgebung aus. Die Bridge generiert eine Ausgabe, die so aussieht:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Erstellen und Ausführen der Bridge

Wenn Sie die ausführbare Datei lieber selbst erstellen möchten, können Sie den Quellcode herunterladen und Skripts erstellen.

Öffnen Sie eine Eingabeaufforderung in einem Ordner Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [IoT Plug & Play-Bridge](https://github.com/Azure/iot-plug-and-play-bridge) an diesen Speicherort zu klonen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Nachdem Sie das Repository geklont haben, aktualisieren Sie die Submodule. Die Submodule enthalten das Azure IoT-SDK für C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

> [!TIP]
> Wenn die Aktualisierung des Git-Klon-Submoduls fehlschlägt, ist dies ein bekanntes Problem bei Windows-Dateipfaden. Führen Sie den folgenden Befehl aus, um die Behebung des Problems zu versuchen: `git config --system core.longpaths true`

Die Voraussetzungen für das Erstellen der Bridge variieren je nach Betriebssystem:

### <a name="windows"></a>Windows

Wenn Sie die IoT Plug & Play-Bridge unter Windows erstellen möchten, installieren Sie die folgende Software:

* [Visual Studio (Community, Professional oder Enterprise):](https://visualstudio.microsoft.com/downloads/) Stellen Sie sicher, dass Sie die Workload **Desktopentwicklung mit C++** aktivieren, wenn Sie Visual Studio [installieren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

In diesem Artikel wird davon ausgegangen, dass Sie Ubuntu Linux verwenden. Die Schritte in diesem Artikel wurden unter Ubuntu 18.04 getestet.

Wenn Sie die IoT Plug & Play-Bridge unter Linux erstellen möchten, installieren Sie **GCC**, **Git**, **CMake** und alle erforderlichen Abhängigkeiten mit dem Befehl `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Überprüfen Sie, ob die Version von `cmake` höher als **2.8.12** und die Version von **GCC** höher als **4.4.7** ist.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Erstellen der IoT Plug & Play-Bridge

Navigieren Sie zum Ordner *pnpbridge* im Repositoryverzeichnis.

Führen Sie unter Windows folgende Befehlseingaben in einer [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) aus:

```cmd
cd scripts\windows
build.cmd
```

Führen Sie analog dazu unter Linux Folgendes aus:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Unter Windows können Sie die Projektmappe öffnen, die mit dem Befehl „cmake“ in Visual Studio 2019 generiert wurde. Öffnen Sie die Projektdatei *azure_iot_pnp_bridge.sln* im Verzeichnis cmake, und legen Sie das Projekt *pnpbridge_bin* als Startprojekt in der Projektmappe fest. Jetzt können Sie das Beispiel in Visual Studio erstellen und im Debugmodus ausführen.

### <a name="edit-the-configuration-file"></a>Bearbeiten der Konfigurationsdatei

Mehr über Konfigurationsdateien können Sie im [Konzeptdokument zur IoT Plug & Play-Bridge](concepts-iot-pnp-bridge.md) erfahren.

Öffnen Sie die Datei *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.json* in einem Text-Editor.

- Fügen Sie den Wert `connection-string` hinzu. Dies ist die _Verbindungszeichenfolge_ für Geräte, die Sie sich vorher notiert haben.
- Fügen Sie den Wert `symmetric_key` hinzu. Dies ist der Wert des Schlüssels für den gemeinsamen Zugriff aus der _Verbindungszeichenfolge_.
- Ersetzen Sie den Wert `root_interface_model_id` durch `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`.

Der erste Abschnitt der Datei *config.json*  sieht jetzt wie der folgende Codeausschnitt aus:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Ausführen der IoT Plug & Play-Bridge

Starten Sie das Beispiel für den IoT Plug & Play-Bridge-Umgebungssensor. Der-Parameter ist der Pfad zur Datei `config.json`, die Sie im vorhergehenden Abschnitt bearbeitet haben:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Die Bridge generiert eine Ausgabe, die so aussieht:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Verwenden Sie die folgenden Befehle zum Ausführen der Bridge unter Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Herunterladen der Modelldateien

Sie verwenden Azure IoT-Explorer zu einem späteren Zeitpunkt zum Anzeigen des Geräts, wenn es eine Verbindung mit Ihrem IoT-Hub herstellt. Für Azure IoT-Explorer wird eine lokale Kopie der Modelldatei benötigt, die mit der von Ihrem Gerät gesendeten **Modell-ID** übereinstimmt. Mit der Modelldatei kann der IoT-Explorer die Telemetriedaten, Eigenschaften und Befehle anzeigen, die von Ihrem Gerät implementiert werden.

So laden Sie die Modelle für Azure IoT-Explorer herunter:

1. Erstellen Sie auf Ihrem lokalen Computer einen Ordner mit dem Namen *models*.
1. Speichern Sie [EnvironmentalSensor.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) im Ordner *models*, den Sie im vorhergehenden Schritt erstellt haben.
1. Wenn Sie diese Modelldatei in einem Text-Editor öffnen, können Sie sehen, dass das Modell eine Komponente mit `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` als ID definiert. Dies ist dieselbe Modell-ID, die Sie in der Datei *config.json* verwendet haben.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start der Bridge mithilfe des Tools Azure IoT-Explorer, ob der Code funktioniert. Die im Modell `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` definierten Telemetriedaten, Eigenschaften und Befehle werden angezeigt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie ein IoT Plug & Play-Gerät mit einem IoT-Hub verbunden wird. Weitere Informationen zum Erstellen einer Lösung, die mit Ihren IoT Plug & Play-Geräten interagiert, finden Sie im folgenden Artikel:

* [Was ist IoT Plug & Play-Bridge](./concepts-iot-pnp-bridge.md)
* [Erstellen, Bereitstellen und Erweitern der IoT Plug & Play-Bridge](howto-build-deploy-extend-pnp-bridge.md)
* [IoT Plug & Play-Bridge auf GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
