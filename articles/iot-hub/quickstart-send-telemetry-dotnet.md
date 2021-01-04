---
title: 'Schnellstart: Senden von Telemetriedaten an Azure IoT Hub (C#) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei C#-Beispielanwendung aus, um simulierte Telemetriedaten an eine IoT Hub-Instanz zu senden und zur Verarbeitung in der Cloud aus der IoT Hub-Instanz zu lesen.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 21410f7137a76b43f57ca7a1e037908410eae365
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844521"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub und Lesen der Telemetriedaten mit einer Back-End-Anwendung (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie umfangreiche Telemetriedaten von Ihren Geräten in der Cloud erfassen können, um sie zu speichern oder zu verarbeiten. In dieser Schnellstartanleitung senden Sie Telemetriedaten von einer Anwendung zur Simulation eines Geräts über IoT Hub zur Verarbeitung an eine Back-End-Anwendung.

In der Schnellstartanleitung werden zwei vorab geschriebene C#-Anwendungen verwendet: eine zum Senden der Telemetriedaten und eine andere zum Lesen der Telemetriedaten aus dem Hub. Vor dem Ausführen dieser beiden Anwendungen erstellen Sie eine IoT Hub-Instanz und registrieren ein Gerät bei dieser Instanz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in C# geschrieben. Sie benötigen auf Ihrem Entwicklungscomputer das .NET Core SDK 3.0 oder höher.

    Sie können das .NET Core SDK für mehrere Plattformen von [.NET](https://www.microsoft.com/net/download/all) herunterladen.

    Mit dem folgenden Befehl können Sie die aktuelle C#-Version auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK 3.0 oder höher wird zum Kompilieren des Event Hubs-Dienstcodes empfohlen, der in diesem Schnellstart zum Lesen von Telemetriedaten verwendet wird. Sie können .NET Core SDK 2.1 verwenden, wenn Sie die Sprachversion für Dienstcode auf „Vorschau“ festgelegt haben, wie im Abschnitt [Lesen der Telemetriedaten aus Ihrem Hub](#read-the-telemetry-from-your-hub) beschrieben wird.


* Laden Sie unter [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) die Azure IoT-C#-Beispiele herunter, und extrahieren Sie das ZIP-Archiv.

* Stellen Sie sicher, dass Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in dieser Schnellstartanleitung wird das MQTT-Protokoll verwendet, das über den Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyDotnetDevice**: Der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyDotnetDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Führen Sie den folgenden Befehl in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

3. Darüber hinaus benötigen Sie den mit _Event Hubs kompatiblen Endpunkt_, den mit _Event Hubs kompatiblen Pfad_ und den _Dienstprimärschlüssel_ Ihres IoT-Hubs, um der Back-End-Anwendung das Herstellen einer Verbindung mit Ihrem IoT-Hub und das Abrufen der Nachrichten zu ermöglichen. Die folgenden Befehle rufen diese Werte für Ihre IoT Hub-Instanz ab:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Notieren Sie sich diese drei Werte. Sie benötigen sie später in der Schnellstartanleitung.

## <a name="send-simulated-telemetry"></a>Senden simulierter Telemetriedaten

Die Anwendung zur Simulation eines Geräts stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her und sendet simulierte Telemetriedaten für Temperatur und Luftfeuchtigkeit.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des C#-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device**.

2. Öffnen Sie die Datei **SimulatedDevice.cs** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `s_connectionString` durch die Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben. Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.cs**.

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Pakete für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

4. Führen Sie im lokalen Terminalfenster den folgenden Befehl aus, um die simulierte Geräteanwendung zu erstellen und auszuführen:

    ```cmd/sh
    dotnet run
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

Die Back-End-Anwendung stellt eine Verbindung mit dem dienstseitigen Endpunkt **Events** in Ihrer IoT Hub-Instanz her. Die Anwendung empfängt die vom simulierten Gerät gesendeten Gerät-zu-Cloud-Nachrichten. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt, um Gerät-zu-Cloud-Nachrichten zu empfangen und zu verarbeiten.

1. Navigieren Sie in einem anderen lokalen Terminalfenster zum Stammordner des C#-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\read-d2c-messages**.

2. Öffnen Sie die Datei **ReadDeviceToCloudMessages.cs** in einem Text-Editor Ihrer Wahl. Aktualisieren Sie die folgenden Variablen, und speichern Sie Ihre Änderungen an der Datei.

    | Variable | Wert |
    | -------- | ----------- |
    | `EventHubsCompatibleEndpoint` | Ersetzen Sie den Wert der Variablen durch den mit Event Hubs kompatiblen Endpunkt, den Sie sich zuvor notiert haben. |
    | `EventHubName`                | Ersetzen Sie den Wert der Variablen durch den mit Event Hubs kompatiblen Pfad, den Sie sich zuvor notiert haben. |
    | `IotHubSasKey`                | Ersetzen Sie den Wert der Variablen durch den Dienstprimärschlüssel, den Sie sich zuvor notiert haben. |

    > [!NOTE]
    > Wenn Sie .NET Core SDK 2.1 verwenden, müssen Sie die Sprachversion zum Kompilieren des Codes auf „Vorschau“ festlegen. Öffnen Sie dazu die Datei **read-d2c-messages.csproj**, und legen Sie den Wert des Elements `<LangVersion>` auf `preview` fest.

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die Back-End-Anwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

4. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die Back-End-Anwendung zu erstellen und auszuführen:

    ```cmd/sh
    dotnet run
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Back-End-Anwendung vom simulierten Gerät an den Hub gesendete Telemetriedaten empfängt:

    ![Ausführen der Back-End-Anwendung](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, mit einer C#-Anwendung simulierte Telemetriedaten an den IoT-Hub gesendet und mit einer einfachen Back-End-Anwendung die Telemetriedaten aus der Instanz gelesen.

Um zu erfahren, wie Sie das simulierte Gerät über eine Back-End-Anwendung steuern, fahren Sie mit der nächsten Schnellstartanleitung fort.

> [!div class="nextstepaction"]
> [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts](quickstart-control-device-dotnet.md)
