---
title: 'Schnellstart: Senden von Telemetriedaten an Azure IoT Hub (Android) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie eine Android-Beispielanwendung aus, um simulierte Telemetriedaten an eine IoT Hub-Instanz zu senden und zur Verarbeitung in der Cloud aus der IoT Hub-Instanz zu lesen.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: ef5f81090f4d06f15f1a7263699961cd360e12e5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842191"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Schnellstart: Senden von IoT-Telemetriedaten von einem Android-Gerät

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

In dieser Schnellstartanleitung senden Sie Telemetriedaten von einer auf einem physischen oder simulierten Gerät ausgeführten Android-Anwendung an Azure IoT Hub. IoT Hub ist ein Azure-Dienst, mit dem Sie umfangreiche Telemetriedaten von Ihren Geräten in der Cloud erfassen können, um sie zu speichern oder zu verarbeiten. In dieser Schnellstartanleitung wird eine vorab geschriebene Android-Anwendung zum Senden der Telemetriedaten verwendet. Die Telemetriedaten werden mithilfe von Azure Cloud Shell aus IoT Hub gelesen. Vor dem Ausführen der Anwendung erstellen Sie eine IoT Hub-Instanz und registrieren ein Gerät bei dieser Instanz.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio mit Android SDK 27.](https://developer.android.com/studio/) Weitere Informationen finden Sie im Artikel zur [Android-Installation](https://developer.android.com/studio/install). In dem Beispiel in diesem Artikel wird Android SDK 27 verwendet.

* [Eine Android-Beispielanwendung.](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Dieses Beispiel finden Sie im Repository [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

* Der Port 8883 muss in Ihrer Firewall geöffnet sein. Für das Beispielgerät in dieser Schnellstartanleitung wird das MQTT-Protokoll verwendet, das über den Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyAndroidDevice**: Der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyAndroidDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Führen Sie den folgenden Befehl in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Sie verwenden diesen Wert später in dieser Schnellstartanleitung, um Telemetriedaten zu senden.

## <a name="send-simulated-telemetry"></a>Senden simulierter Telemetriedaten

1. Öffnen Sie das Android-Beispielprojekt von GitHub in Android Studio. Das Projekt befindet sich im folgenden Verzeichnis Ihrer geklonten oder heruntergeladenen Kopie des Repositorys [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java): *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. Öffnen Sie in Android Studio die Datei *gradle.properties* für das Beispielprojekt, und ersetzen Sie den Platzhalter **Device_Connection_String** durch die zuvor notierte Geräteverbindungszeichenfolge.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Klicken Sie in Android Studio auf **File** > **Sync Project with Gradle Files** (Datei > Projekt mit Gradle-Dateien synchronisieren). Vergewissern Sie sich, dass der Buildvorgang abgeschlossen wurde.

   > [!NOTE]
   > Sollte bei der Synchronisierung des Projekts ein Fehler auftreten, kann einer der folgenden Gründe vorliegen:
   >
   > * Die Versionen von Android Gradle-Plug-In und Gradle, auf die im Projekt verwiesen wird, sind für Ihre Version von Android Studio veraltet. Gehen Sie wie [hier](https://developer.android.com/studio/releases/gradle-plugin) beschrieben vor, um die richtige Plug-In- und Gradle-Version für Ihre Installation zu installieren und auf sie zu verweisen.
   > * Der Lizenzvertrag für das Android SDK wurde nicht unterzeichnet. Befolgen Sie die Anweisungen in der Buildausgabe, um den Lizenzvertrag zu unterzeichnen, und laden Sie das SDK herunter.

4. Klicken Sie anschließend auf **Run** > **Run 'app'** (Ausführen > App ausführen). Konfigurieren Sie die App für die Ausführung auf einem physischen Android-Gerät oder in einem Android-Emulator. Weitere Informationen zum Ausführen einer Android-App auf einem physischen Gerät oder in einem Emulator finden Sie unter [Run your app](https://developer.android.com/training/basics/firstapp/running-app) (Ausführen Ihrer App).

5. Klicken Sie nach dem Laden der App auf die Schaltfläche **Start** (Starten), um mit dem Senden von Telemetriedaten an Ihre IoT Hub-Instanz zu beginnen:

    ![Application](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), um die Gerätenachrichten zu überwachen, die vom Android-Gerät gesendet werden.

1. Führen Sie über Azure Cloud Shell den folgenden Befehl aus, um eine Verbindung mit Ihrem IoT-Hub herzustellen und Nachrichten zu lesen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Der folgende Screenshot zeigt die Ausgabe, während der IoT-Hub die vom Android-Gerät gesendeten Telemetriedaten empfängt:

      ![Lesen der Gerätenachrichten mithilfe der Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, mit einer Android-Anwendung simulierte Telemetriedaten an den Hub gesendet und mithilfe von Azure Cloud Shell die Telemetriedaten aus dem Hub gelesen.

Um zu erfahren, wie Sie das simulierte Gerät über eine Back-End-Anwendung steuern, fahren Sie mit der nächsten Schnellstartanleitung fort.

> [!div class="nextstepaction"]
> [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts](quickstart-control-device-android.md)
