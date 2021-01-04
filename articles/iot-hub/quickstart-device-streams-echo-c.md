---
title: Kommunizieren mit einer Geräte-App in C über Azure IoT Hub-Gerätestreams
description: In diesem Schnellstart führen Sie eine geräteseitige C-Anwendung aus, die über einen Gerätestream mit einem IoT-Gerät kommuniziert.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: de310846ad0449a0dac7eccd60d82d4c68ef519b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832205"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Schnellstart: Kommunizieren mit einer Geräteanwendung in C über IoT Hub-Gerätestreams (Vorschauversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub unterstützt derzeit Gerätestreams als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Über [IoT Hub-Gerätestreams](iot-hub-device-streams-overview.md) können Dienst- und Geräteanwendungen sicher und firewallfreundlich kommunizieren. Während der Public Preview-Phase unterstützt das C SDK nur geräteseitige Gerätestreams. Aus diesem Grund wird in dieser Schnellstartanleitung nur das Ausführen der geräteseitigen Anwendung erläutert. Informationen zum Ausführen einer entsprechenden dienstseitigen Anwendung finden Sie in den folgenden Artikeln:

* [Kommunizieren mit Geräte-Apps in C# über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-csharp.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)](./quickstart-device-streams-echo-nodejs.md)

Die geräteseitige C-Anwendung in dieser Schnellstartanleitung hat folgende Funktionen:

* Einrichten eines Gerätestreams mit einem IoT-Gerät

* Empfangen der von der dienstseitigen Anwendung gesendeten Daten und Zurückgeben dieser Daten

Der Code veranschaulicht den Initiierungsprozess eines Gerätestreams sowie das Verwenden dieses Streams zum Senden und Empfangen von Daten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein:

* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/vs/) mit der aktivierten Workload **Desktopentwicklung mit C++** .

* Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Die Vorschau der Gerätestreams wird derzeit nur für IoT-Hubs unterstützt, die in folgenden Regionen erstellt werden:

  * USA (Mitte)
  * USA, Mitte (EUAP)
  * Nordeuropa
  * Asien, Südosten

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Für diesen Schnellstart verwenden Sie das [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md). Sie bereiten eine Entwicklungsumgebung vor, die zum Klonen und Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) aus GitHub verwendet wird. Das SDK auf GitHub enthält den in dieser Schnellstartanleitung verwendeten Beispielcode.

   > [!NOTE]
   > Vergewissern Sie sich vor diesem Verfahren, dass Visual Studio mit der Workload **Desktopentwicklung mit C++** installiert ist.

1. Installieren Sie das [CMake-Buildsystem](https://cmake.org/download/), wie auf der Downloadseite beschrieben.

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

   * Öffnen Sie unter Windows eine [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Führen Sie den Befehl für Ihre Version von Visual Studio aus. In dieser Schnellstartanleitung wird Visual Studio 2019 verwendet. Mit diesen Befehlen wird im Verzeichnis *cmake* eine Visual Studio-Projektmappe für das simulierte Gerät erstellt.

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

Sie müssen ein Gerät bei Ihrem IoT-Hub registrieren, damit eine Verbindung hergestellt werden kann. In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen:

   > [!NOTE]
   > * Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.
   > * Verwenden Sie für den Namen des Geräts, das Sie registrieren, am besten *MyDevice*, wie bereits gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, verwenden Sie diesen im gesamten Artikel, und aktualisieren Sie den Gerätenamen in den Beispielanwendungen, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Führen Sie den folgenden Befehl in Cloud Shell aus, um die *Geräteverbindungszeichenfolge* für das soeben registrierte Gerät abzurufen:

   > [!NOTE]
   > Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Notieren Sie sich die zurückgegebene Verbindungszeichenfolge des Geräts zur späteren Verwendung in dieser Schnellstartanleitung. Dies sieht in etwa wie im folgenden Beispiel aus:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunizieren zwischen Gerät und Dienst über Gerätestreams

In diesem Abschnitt führen Sie die geräteseitige Anwendung und die dienstseitige Anwendung aus und stellen eine Kommunikation zwischen beiden her.

### <a name="run-the-device-side-application"></a>Ausführen der geräteseitigen Anwendung

Gehen Sie zum Ausführen der geräteseitigen Anwendung folgendermaßen vor:

1. Geben Sie Ihre Geräteanmeldeinformationen an, indem Sie die Quelldatei **iothub_client_c2d_streaming_sample.c** im Ordner `iothub_client/samples/iothub_client_c2d_streaming_sample` bearbeiten und Ihre Geräteverbindungszeichenfolge angeben.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Kompilieren Sie den Code mit den folgenden Befehlen aus:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Führen Sie das kompilierte Programm aus:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Ausführen der dienstseitigen Anwendung

Wie bereits erwähnt, unterstützt das IoT Hub C SDK nur geräteseitige Gerätestreams. Wenn Sie die zugehörige dienstseitige Anwendung erstellen und ausführen möchten, befolgen Sie die Anweisungen in den folgenden Schnellstartanleitungen:

* [Kommunizieren mit einer Geräte-App in C# über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-csharp.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, einen Gerätestream zwischen einer C-Anwendung auf dem Gerät und einer anderen dienstseitigen Anwendung eingerichtet und mithilfe des Streams Daten zwischen den Anwendungen hin und her gesendet.

Weitere Informationen zu Gerätestreams finden Sie hier:

> [!div class="nextstepaction"]
> [Übersicht über Gerätestreams](./iot-hub-device-streams-overview.md)
