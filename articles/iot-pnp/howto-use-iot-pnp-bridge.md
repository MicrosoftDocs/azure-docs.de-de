---
title: Herstellen einer Verbindung eines IoT Plug & Play-Bridgebeispiels unter Linux oder Windows mit einem IoT-Hub | Microsoft-Dokumentation
description: Erstellen Sie unter Linux oder Windows eine IoT Plug & Play-Bridge, die eine Verbindung mit einem IoT-Hub herstellt, und führen Sie sie aus. Verwenden Sie das Tool Azure IoT-Explorer, um die vom Gerät an den Hub gesendeten Informationen anzuzeigen.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eedd19189d1e1ccedd3d505aecf407aca8fca831
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413359"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Herstellen einer Verbindung eines IoT Plug & Play-Bridgebeispiels unter Linux oder Windows mit IoT-Hub

In dieser Schrittanleitung erfahren Sie, wie Sie den Beispielumgebungsadapter der IoT Plug & Play-Bridge erstellen, ihn mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die IoT Plug and Play-Bridge ist in C geschrieben und schließt das Azure IoT-Geräte-SDK für C ein. An Ende dieses Tutorials sollten Sie in der Lage sein, die IoT Plug & Play-Bridge auszuführen und sie Telemetriedaten im Azure IoT-Explorer ausgeben zu sehen: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Ein Screenshot des Azure IoT-Explorers mit einer Tabelle gemeldeter Telemetriedaten (Feuchtigkeit, Temperatur) von der Iot Plug & Play-Bridge.":::

## <a name="prerequisites"></a>Voraussetzungen

Dieser Schnellstart kann unter Linux oder Windows ausgeführt werden. Die Shellbefehle in dieser Schnellstartanleitung entsprechen der Windows-Konvention für die Pfadtrennzeichen `\`. Wenn Sie den Anleitungen unter Linux folgen, sollten Sie diese Trennzeichen durch `/` ersetzen.

Die Voraussetzungen sind je nach Betriebssystem unterschiedlich:

### <a name="linux"></a>Linux

In diesem Schnellstart wird davon ausgegangen, dass Sie Ubuntu Linux verwenden. Die Schritte in dieser Schnellstartanleitung wurden unter Ubuntu 18.04 getestet.

Installieren Sie die folgende Software in Ihrer lokalen Linux-Umgebung, um diesen Schnellstart unter Linux auszuführen:

Installieren Sie **GCC** , **Git** , **cmake** und alle erforderlichen Abhängigkeiten mit dem Befehl `apt-get`:

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

### <a name="azure-iot-explorer"></a>Azure IoT-Explorer

Wenn Sie im zweiten Teil dieses Schnellstarts mit dem Beispielgerät interagieren möchten, verwenden Sie das Tool **Azure IoT-Explorer**. Dazu müssen Sie [die neueste Version von Azure IoT-Explorer für Ihr Betriebssystem herunterladen und installieren](./howto-use-iot-explorer.md).

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Führen Sie den folgenden Befehl aus, um die _IoT-Hub-Verbindungszeichenfolge_ für Ihren Hub abzurufen. Notieren Sie sich diese Verbindungszeichenfolge, die Sie später in diesem Schnellstart verwenden werden:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Die IoT-Hub-Verbindungszeichenfolge kann auch per Azure IoT-Explorer ermittelt werden.

Führen Sie den folgenden Befehl aus, um die _Geräteverbindungszeichenfolge_ für das dem Hub hinzugefügte Gerät abzurufen. Notieren Sie sich diese Verbindungszeichenfolge, die Sie später in diesem Schnellstart verwenden werden:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Anzeigen des Modells

Sie verwenden den Azure IoT-Explorer in späteren Schritten zum Anzeigen des Geräts, wenn dafür die Verbindung mit Ihrem IoT-Hub hergestellt wird. Für Azure IoT-Explorer wird eine lokale Kopie der Modelldatei benötigt, die mit der von Ihrem Gerät gesendeten **Modell-ID** übereinstimmt. Mit der Modelldatei kann der IoT-Explorer die Telemetriedaten, Eigenschaften und Befehle anzeigen, die von Ihrem Gerät implementiert werden.

Wenn Sie den Code im folgenden Schritt herunterladen, sind die Dateien des Beispielmodells im Ordner "`pnpbridge/docs/schema`" enthalten. Vorbereiten des Azure-IoT-Explorers:

1. Erstellen Sie auf Ihrem lokalen Computer einen Ordner mit dem Namen *models*.
1. Zeigen Sie [EnvironmentalSensor.json](https://aka.ms/iot-pnp-bridge-env-model) an, und speichern Sie die JSON-Datei im Ordner *models*.
1. Zeigen Sie [RootBridgeSampleDevice.json](https://aka.ms/iot-pnp-bridge-root-model) an, und speichern Sie die JSON-Datei im Ordner *models*.

## <a name="download-the-code"></a>Laden Sie den Code herunter.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [IoT Plug & Play-Bridge](https://aka.ms/iotplugandplaybridge) an diesem Speicherort zu klonen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Öffnen Sie nach dem Klonen der IoT Plug & Play-Bridge eine administrative CMD-Eingabeaufforderung, und navigieren Sie zum Verzeichnis mit dem geklonten Repository:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

>[!NOTE]
> Wenn ein Problem mit Updatefehlern des Untermoduls des Git-Klons auftritt, handelt es sich um ein bekanntes Problem mit Windows-Dateipfaden und Git. Mehr dazu finden Sie unter: https://github.com/msysgit/git/pull/110 . Führen Sie den folgenden Befehl aus, um die Behebung des Problems zu versuchen: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Einrichten der Konfigurations-JSON

Navigieren Sie nach dem Klonen des IoT Plug & Play-Bridgerepositorys auf Ihren Computer zum Verzeichnis `pnpbridge/docs/schema` des geklonten Repositorys, wo Sie die [Konfigurations-JSON](https://aka.ms/iot-pnp-bridge-env-config) oder `config.json` für das Umgebungssensorbeispiel der Bridge finden. Mehr über Konfigurationsdateien können Sie im [Konzeptdokument zur IoT Plug & Play-Bridge](concepts-iot-pnp-bridge.md) erfahren.

Für das `root-_interface_model_id`-Feld müssen Sie die ID des IoT Plug & Play-Modells, die das Modell für Ihr Gerät identifiziert, kopieren. In diesem Beispiel lautet er `dtmi:com:example:SampleDevice;1`. Ändern Sie die folgenden Parameter unter dem Knoten **pnp_bridge_parameters** in der `config.json`-Datei:

* connection_string 
* symmetric_key 

>[!NOTE]
> „symmetric_key“ muss mit dem SAS-Schlüssel in der Verbindungszeichenfolge übereinstimmen.

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Nach der Änderung sollte die `config.json`-Datei etwa wie folgt aussehen:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Nach dem Erstellen der Bridge müssen Sie diese `config.json` im gleichen Verzeichnis wie die Bridge platzieren oder andernfalls bei der Ausführung ihren Pfad angeben.

## <a name="build-the-iot-plug-and-play-bridge"></a>Erstellen der IoT Plug & Play-Bridge

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

## <a name="start-the-iot-plug-and-play-bridge"></a>Starten der IoT Plug & Play-Bridge

 Starten Sie das IoT Plug & Play-Bridgebeispiel für Umgebungssensoren, indem Sie zum Ordner *pnpbridge* navigieren und den folgenden Befehl an einer Eingabeaufforderung ausführen:

```bash
 cd cmake/pnpbridge_linux/src/adapters/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adapters\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie ein IoT Plug & Play-Gerät mit einem IoT-Hub verbinden. Weitere Informationen zum Erstellen einer Lösung, die mit Ihren IoT Plug & Play-Geräten interagiert, finden Sie im folgenden Artikel:

* [Was ist IoT Plug & Play-Bridge](./concepts-iot-pnp-bridge.md)
* [Informationen zur IoT Plug & Play-Bridge finden Sie in der GitHub-Entwicklerreferenz](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT Plug & Play-Bridge auf GitHub](https://aka.ms/iotplugandplaybridge)
