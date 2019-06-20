---
title: Kommunizieren mit einer Geräteanwendung in C# über Azure IoT Hub-Gerätestreams (Vorschauversion) | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung führen Sie zwei C#-Beispielanwendungen aus, die über einen per IoT Hub eingerichteten Gerätestream kommunizieren.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 74a8fc40cff12070f7cea99981eb4e8321d7c1ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735149"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Schnellstart: Kommunizieren mit einer Geräteanwendung in C# über IoT Hub-Gerätestreams (Vorschauversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub unterstützt derzeit Gerätestreams als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Über [IoT Hub-Gerätestreams](./iot-hub-device-streams-overview.md) können Dienst- und Geräteanwendungen sicher und firewallfreundlich kommunizieren. In dieser Schnellstartanleitung werden zwei C#-Anwendungen verwendet, die Daten über Gerätestreams hin und her senden (Echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Die Vorschau der Gerätestreams wird derzeit nur für IoT-Hubs unterstützt, die in folgenden Regionen erstellt werden:
  * USA (Mitte)
  * USA, Mitte (EUAP)

* Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in C# geschrieben. Sie benötigen auf Ihrem Entwicklungscomputer das .NET Core SDK 2.1.0 oder höher.
  * Laden Sie das [.NET Core SDK für mehrere Plattformen](https://www.microsoft.com/net/download/all) von .NET herunter.
  * Überprüfen Sie mit dem folgenden Befehl die aktuelle C#-Version auf Ihrem Entwicklungscomputer:

   ```
   dotnet --version
   ```

* Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Laden Sie das C#-Beispielprojekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter, und extrahieren Sie das ZIP-Archiv. Sie benötigen es sowohl auf der Geräte- als auch der Dienstseite.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In diesem Abschnitt verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen:

   > [!NOTE]
   > * Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub wählen.
   > * Verwenden Sie *MyDevice* wie gezeigt. Der für das registrierte Gerät angegebene Name. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, verwenden Sie diesen im gesamten Artikel, und aktualisieren Sie den Gerätenamen in den Beispielanwendungen, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Führen Sie den folgenden Befehl in Cloud Shell aus, um die *Geräteverbindungszeichenfolge* für das soeben registrierte Gerät abzurufen:

   > [!NOTE]
   > Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge zur späteren Verwendung in dieser Schnellstartanleitung. Dies sieht in etwa wie im folgenden Beispiel aus:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Darüber hinaus benötigen Sie die *Dienstverbindungszeichenfolge* aus Ihrem IoT-Hub, damit die dienstseitige Anwendung eine Verbindung mit Ihrem IoT-Hub herstellen und einen Gerätestream einrichten kann. Der folgende Befehl ruft diesen Wert für Ihren IoT-Hub ab:

   > [!NOTE]
   > Ersetzen Sie den Platzhalter *YourIoTHubName* durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Notieren Sie sich den zurückgegebenen Wert zur späteren Verwendung in dieser Schnellstartanleitung. Dies sieht in etwa wie im folgenden Beispiel aus:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunizieren zwischen Gerät und Dienst über Gerätestreams

In diesem Abschnitt führen Sie die geräteseitige Anwendung und die dienstseitige Anwendung aus und stellen eine Kommunikation zwischen beiden her.

### <a name="run-the-service-side-application"></a>Ausführen der dienstseitigen Anwendung

Navigieren Sie im entzippten Projektordner zum Verzeichnis *iot-hub/Quickstarts/device-streams-echo/service*. Halten Sie die folgenden Informationen bereit:

| Parametername | Parameterwert |
|----------------|-----------------|
| `ServiceConnectionString` | Geben Sie die Dienstverbindungszeichenfolge Ihres IoT-Hubs an. |
| `DeviceId` | Geben Sie die ID des zuvor erstellten Geräts an, z. B. *MyDevice*. |

Kompilieren Sie den Code, und führen Sie ihn aus:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Ein Timeout tritt auf, wenn die geräteseitige Anwendung nicht rechtzeitig reagiert.

### <a name="run-the-device-side-application"></a>Ausführen der geräteseitigen Anwendung

Navigieren Sie in Ihrem entzippten Projektordner zum Verzeichnis *iot-hub/Quickstarts/device-streams-echo/device*. Halten Sie die folgenden Informationen bereit:

| Parametername | Parameterwert |
|----------------|-----------------|
| `DeviceConnectionString` | Geben Sie die Geräteverbindungszeichenfolge Ihres IoT-Hubs an. |

Kompilieren Sie den Code, und führen Sie ihn aus:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Am Ende des letzten Schritts initiiert die dienstseitige Anwendung einen Stream an Ihr Gerät. Nach der Einrichtung des Streams sendet die Anwendung über den Stream einen Zeichenfolgenpuffer an den Dienst. In diesem Beispiel gibt die dienstseitige Anwendung einfach die gleichen Daten an das Gerät zurück, um eine erfolgreiche bidirektionale Kommunikation zwischen den beiden Anwendungen zu veranschaulichen.

Konsolenausgabe auf Geräteseite:

![Konsolenausgabe auf Geräteseite](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsolenausgabe auf Dienstseite:

![Konsolenausgabe auf Dienstseite](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Der über den Stream gesendete Datenverkehr wird nicht direkt gesendet, sondern über den IoT-Hub getunnelt. Die gebotenen Vorteile werden unter [Vorteile von Gerätestreams](./iot-hub-device-streams-overview.md#benefits) beschrieben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, einen Gerätestream zwischen geräte- und dienstseitigen C#-Anwendungen eingerichtet und mithilfe des Streams Daten zwischen den Anwendungen hin und her gesendet.

Weitere Informationen zu Gerätestreams finden Sie hier:

> [!div class="nextstepaction"]
> [IoT Hub-Gerätestreams (Vorschau)](./iot-hub-device-streams-overview.md)
