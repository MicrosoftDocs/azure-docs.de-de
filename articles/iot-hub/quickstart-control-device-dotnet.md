---
title: 'Schnellstart: Steuern eines Geräts über Azure IoT Hub (.NET) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei C#-Beispielanwendungen aus. Eine dieser Anwendungen ist eine Dienstanwendung, die mit Ihrem Hub verbundene Geräte remote steuern kann. Die andere Anwendung simuliert ein Gerät, das mit Ihrem Hub verbunden ist und remote gesteuert werden kann.
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
ms.date: 03/04/2020
ms.openlocfilehash: 28d80a20c50f846146aed069028303e86426df27
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072084"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie Ihre IoT-Geräte über die Cloud verwalten und große Mengen von Gerätetelemetriedaten zum Speichern oder Verarbeiten in der Cloud erfassen können. In dieser Schnellstartanleitung verwenden Sie eine *direkte Methode*, um ein simuliertes Gerät zu steuern, das mit Ihrer IoT Hub-Instanz verbunden ist. Sie können direkte Methoden verwenden, um das Verhalten eines mit Ihrer IoT Hub-Instanz verbundenen Geräts zu ändern.

In dieser Schnellstartanleitung werden zwei vorab geschriebene .NET-Anwendungen verwendet:

* Eine simulierte Geräteanwendung, die auf die von einer Dienstanwendung aufgerufenen direkten Methoden reagiert. Um die Aufrufe der direkten Methode zu empfangen, stellt diese Anwendung eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her.

* Eine Dienstanwendung, die die direkten Methoden auf dem simulierten Gerät aufruft. Um eine direkte Methode auf einem Gerät aufzurufen, stellt diese Anwendung eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in C# geschrieben. Sie benötigen auf Ihrem Entwicklungscomputer das .NET Core SDK 3.1 oder höher.

    Sie können das .NET Core SDK für mehrere Plattformen von [.NET](https://www.microsoft.com/net/download/all) herunterladen.

    Mit dem folgenden Befehl können Sie die aktuelle C#-Version auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    dotnet --version
    ```
* Laden Sie bei Bedarf unter https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip die Azure IoT-C#-Beispiele herunter, und extrahieren Sie das ZIP-Archiv.

* Stellen Sie sicher, dass Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in dieser Schnellstartanleitung wird das MQTT-Protokoll verwendet, das über den Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-dotnet.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-dotnet.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyDotnetDevice**: Der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyDotnetDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

## <a name="retrieve-the-service-connection-string"></a>Abrufen der Verbindungszeichenfolge für den Dienst

Sie benötigen auch die _Verbindungszeichenfolge für den Dienst_, Ihres IoT-Hubs, damit die Dienstanwendung eine Verbindung mit dem Hub herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Notieren Sie sich die Dienstverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Dieser Wert wird später in der Schnellstartanleitung benötigt. Die Dienstverbindungszeichenfolge unterscheidet sich von der Geräteverbindungszeichenfolge, die Sie sich im vorherigen Schritt notiert haben.

## <a name="listen-for-direct-method-calls"></a>Lauschen auf Aufrufe direkter Methoden

Die simulierte Geräteanwendung stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her, sendet simulierte Telemetriedaten und lauscht auf Aufrufe direkter Methoden aus Ihrem Hub. In dieser Schnellstartanleitung weist der Aufruf einer direkten Methode aus dem Hub das Gerät an, das Intervall zu ändern, in dem es Telemetriedaten sendet. Das simulierte Gerät sendet eine Bestätigung an Ihren Hub, nachdem es die direkte Methode ausgeführt hat.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des C#-Beispielprojekts. Navigieren Sie dann zum Ordner **iot-hub\Quickstarts\SimulatedDeviceWithCommand**.

2. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Pakete für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

3. Führen Sie im lokalen Terminalfenster den folgenden Befehl aus, um die simulierte Geräteanwendung zu erstellen und auszuführen. Ersetzen Sie dabei `{DeviceConnectionString}` durch die zuvor notierte Geräteverbindungszeichenfolge:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Aufrufen der direkten Methode

Die Dienstanwendung stellt eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her. Die Anwendung sendet über Ihren IoT-Hub Aufrufe direkter Methoden an ein Gerät und lauscht auf Bestätigungen. Eine IoT Hub-Dienstanwendung wird normalerweise in der Cloud ausgeführt.

1. Navigieren Sie in einem anderen lokalen Terminalfenster zum Stammordner des C#-Beispielprojekts. Navigieren Sie dann zum Ordner **iot-hub\Quickstarts\InvokeDeviceMethod**.

2. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken für die Dienstanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

3. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die simulierte Geräteanwendung zu erstellen und auszuführen. Ersetzen Sie dabei `{ServiceConnectionString}` durch die zuvor notierte Geräteverbindungszeichenfolge:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Der folgende Screenshot zeigt die Ausgabe, nachdem die Anwendung einen Aufruf einer direkten Methode an das Gerät gerichtet und eine Bestätigung empfangen hat:

    ![Ausführen der Dienstanwendung](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Nachdem Sie die Dienstanwendung ausgeführt haben, wird im Konsolenfenster, in dem das simulierte Gerät ausgeführt wird, eine Nachricht angezeigt, und die Häufigkeit, mit der das Gerät Nachrichten sendet, ändert sich:

    ![Änderung im simulierten Client](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie in einer Dienstanwendung eine direkte Methode auf einem Gerät aufgerufen und in einer simulierten Geräteanwendung auf den Aufruf einer direkten Methode reagiert.

Um zu erfahren, wie Sie Gerät-zu-Cloud-Nachrichten an verschiedene Ziele in der Cloud weiterleiten, fahren Sie mit dem nächsten Tutorial fort.

> [!div class="nextstepaction"]
> [Tutorial: Weiterleiten von Telemetriedaten zur Verarbeitung an verschiedene Endpunkte](tutorial-routing.md)
