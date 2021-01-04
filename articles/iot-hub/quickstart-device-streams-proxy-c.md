---
title: 'Azure IoT Hub-Gerätestreams: C#-Schnellstartanleitung für SSH und RDP'
description: In dieser Schnellstartanleitung führen Sie eine C-Beispielanwendung aus, die als Proxy SSH- und RDP-Szenarien über IoT Hub-Gerätestreams ermöglicht.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 037ff64f4811515e7ce64d66a36e08e71de54058
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831989"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Schnellstart: Ermöglichen von SSH und RDP über einen IoT Hub-Gerätestream unter Verwendung einer C-Proxyanwendung (Vorschauversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub unterstützt derzeit Gerätestreams als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Über [IoT Hub-Gerätestreams](./iot-hub-device-streams-overview.md) können Dienst- und Geräteanwendungen sicher und firewallfreundlich kommunizieren. Eine Übersicht über das Setup finden Sie auf der [Seite mit Beispielen für lokale Proxys](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

In dieser Schnellstartanleitung wird das Setup zum Tunneln von SSH-Datenverkehr (Secure Shell) unter Verwendung von Port 22 über Gerätestreams beschrieben. Das Setup für RDP-Datenverkehr (Remotedesktopprotokoll) ist ähnlich und bedarf lediglich einer einfachen Konfigurationsänderung. Gerätestreams sind anwendungs- und protokollunabhängig. Dieser Schnellstart kann daher für andere Arten von Anwendungsdatenverkehr angepasst werden.

## <a name="how-it-works"></a>Funktionsweise

Die folgende Abbildung zeigt, wie die lokalen Geräte- und Dienstproxyprogramme eine End-to-End-Konnektivität zwischen den SSH-Clientprozessen und SSH-Daemonprozessen ermöglichen. Während der Public Preview-Phase unterstützt das C SDK nur geräteseitige Gerätestreams. Aus diesem Grund wird in dieser Schnellstartanleitung nur das Ausführen der lokalen Geräteproxyanwendung erläutert. Wenn Sie die zugehörige dienstseitige Anwendung erstellen und ausführen möchten, befolgen Sie die Anweisungen in den folgenden Schnellstartanleitungen:

* [SSH/RDP über IoT Hub-Gerätestreams unter Verwendung einer C#-Proxyanwendung](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP über IoT Hub-Gerätestreams unter Verwendung einer Node.js-Proxyanwendung](./quickstart-device-streams-proxy-nodejs.md)

![Setup des lokalen Proxys](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Der lokale Dienstproxy stellt eine Verbindung mit dem IoT-Hub her und initiiert einen Gerätestream an das Zielgerät.

2. Der lokale Geräteproxy schließt den Handshake für die Streaminitiierung ab und richtet über den Streamingendpunkt des IoT-Hubs einen End-to-End-Streamingtunnel mit der Dienstseite ein.

3. Der lokale Geräteproxy stellt eine Verbindung mit dem SSH-Daemon her, der an Port 22 auf dem Gerät lauscht. Diese Einstellung ist konfigurierbar, wie im Abschnitt „Ausführen der lokalen Geräteproxyanwendung“ beschrieben.

4. Der lokale Dienstproxy wartet auf neue SSH-Verbindungen des Benutzers, indem er am angegebenen Port (in diesem Fall Port 2222) lauscht. Diese Einstellung ist konfigurierbar, wie im Abschnitt „Ausführen der lokalen Geräteproxyanwendung“ beschrieben. Wenn der Benutzer eine Verbindung über den SSH-Client herstellt, ermöglicht der Tunnel die Übertragung von SSH-Anwendungsdatenverkehr zwischen SSH-Client und Serverprogrammen.

> [!NOTE]
> Über einen Gerätestream gesendeter SSH-Datenverkehr wird nicht direkt zwischen Dienst und Gerät gesendet, sondern über den Streamingendpunkt des IoT-Hubs getunnelt. Weitere Informationen finden Sie in der Beschreibung der [Vorteile der Verwendung von IoT Hub-Gerätestreams](iot-hub-device-streams-overview.md#benefits). Die Abbildung zeigt außerdem, dass der SSH-Daemon auf dem gleichen Gerät (oder Computer) ausgeführt wird wie der lokale Geräteproxy. Wenn Sie in dieser Schnellstartanleitung die IP-Adresse des SSH-Daemons angeben, können der lokale Geräteproxy und der Daemon auch auf unterschiedlichen Geräten ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Die Vorschau der Gerätestreams wird derzeit nur für IoT-Hubs unterstützt, die in folgenden Regionen erstellt werden:

  * USA (Mitte)
  * USA, Mitte (EUAP)
  * Nordeuropa
  * Asien, Südosten

* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/vs/) mit der aktivierten Workload [Desktopentwicklung mit C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Für diesen Schnellstart verwenden Sie das [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md). Sie bereiten eine Entwicklungsumgebung vor, die zum Klonen und Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) aus GitHub verwendet wird. Das SDK auf GitHub enthält den in dieser Schnellstartanleitung verwendeten Beispielcode.

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die erforderlichen Visual Studio-Komponenten (Visual Studio und die Workload *Desktopentwicklung mit C++*) müssen *vor* Beginn der Installation von CMake auf dem Computer installiert sein. Wenn die Voraussetzungen erfüllt sind und der Download überprüft wurde, können Sie das CMake-Buildsystem installieren.

1. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Dieser Vorgang dauert einige Minuten.

1. Erstellen Sie ein *cmake*-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. Führen Sie die folgenden Befehle aus dem Verzeichnis *azure-iot-sdk-c* aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Erstellen Sie mithilfe der folgenden Befehle aus dem Verzeichnis *cmake* eine spezifische SDK-Version für Ihre Entwicklungsclientplattform.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Führen Sie unter Windows an der Developer-Eingabeaufforderung für Visual Studio 2015 oder 2017 die folgenden Befehle aus. Im Verzeichnis *cmake* wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen:

   > [!NOTE]
   > * Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.
   > * Verwenden Sie für den Namen des Geräts, das Sie registrieren, am besten *MyDevice*, wie bereits gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, verwenden Sie diesen im gesamten Artikel, und aktualisieren Sie den Gerätenamen in den Beispielanwendungen, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Führen Sie die folgenden Befehle in Cloud Shell aus, um die *Geräteverbindungszeichenfolge* für das soeben registrierte Gerät abzurufen:

   > [!NOTE]
   > Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Notieren Sie sich die zurückgegebene Verbindungszeichenfolge des Geräts zur späteren Verwendung in dieser Schnellstartanleitung. Dies sieht in etwa wie im folgenden Beispiel aus:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Herstellen einer SSH-Verbindung mit einem Gerät über Gerätestreams

In diesem Abschnitt richten Sie einen End-to-End-Stream ein, um SSH-Datenverkehr zu tunneln.

### <a name="run-the-device-local-proxy-application"></a>Ausführen der lokalen Geräteproxyanwendung

1. Bearbeiten Sie die Quelldatei **iothub_client_c2d_streaming_proxy_sample.c** im Ordner `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`, und geben Sie die Geräteverbindungszeichenfolge, die IP-Adresse oder den Hostnamen des Zielgeräts sowie den SSH-Port 22 an:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompilieren Sie das Beispiel:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Führen Sie das kompilierte Programm auf dem Gerät aus:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Ausführen der lokalen Dienstproxyanwendung

Wie im Abschnitt „Funktionsweise“ beschrieben, wird zur Einrichtung eines End-to-End-Streams für das Tunneln von SSH-Datenverkehr ein lokaler Proxy an jedem Ende (im Dienst und auf dem Gerät) benötigt. Während der Public Preview-Phase unterstützt das IoT Hub C SDK nur geräteseitige Gerätestreams. Befolgen Sie die Anweisungen in einer der folgenden Schnellstartanleitungen, um den lokalen Dienstproxy zu erstellen und auszuführen:

   * [SSH/RDP über IoT Hub-Gerätestreams unter Verwendung von C#-Proxy-Apps](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP über IoT Hub-Gerätestreams unter Verwendung von Node.js-Proxy-Apps](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Einrichten einer SSH-Sitzung

Nachdem sowohl der lokale Geräteproxy als auch der lokale Dienstproxy ausgeführt wird, können Sie über Ihr SSH-Clientprogramm eine Verbindung mit dem lokalen Dienstproxy am Port 2222 herstellen (anstatt eine Direktverbindung mit dem SSH-Daemon herzustellen).

```cmd/sh
ssh {username}@localhost -p 2222
```

Nun werden Sie im SSH-Anmeldefenster zur Eingabe Ihrer Anmeldeinformationen aufgefordert.

In der folgenden Abbildung sehen Sie die Konsolenausgabe des lokalen Geräteproxys, der unter `IP_address:22` eine Verbindung mit dem SSH-Daemon herstellt:

![Ausgabe des lokalen Geräteproxys](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Die folgende Abbildung zeigt die Konsolenausgabe des SSH-Clientprogramms. Der SSH-Client kommuniziert mit dem SSH-Daemon, indem er eine Verbindung mit Port 22 herstellt, an dem der lokale Dienstproxy lauscht:

![Ausgabe des SSH-Clients](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, ein lokales Geräteproxyprogramm und ein lokales Dienstproxyprogramm bereitgestellt, um einen Gerätestream über IoT Hub einzurichten, sowie die Proxys zum Tunneln von SSH-Datenverkehr verwendet.

Weitere Informationen zu Gerätestreams finden Sie hier:

> [!div class="nextstepaction"]
> [Übersicht über Gerätestreams](./iot-hub-device-streams-overview.md)
