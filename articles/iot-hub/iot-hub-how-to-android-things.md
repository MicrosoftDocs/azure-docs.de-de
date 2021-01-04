---
title: Entwickeln für die Android Things-Plattform mithilfe von Azure IoT SDKs | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Erfahren Sie etwas über das Entwickeln für Android Things mithilfe von Azure IoT Hub SDKs.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843756"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Entwickeln für die Android Things-Plattform mithilfe von Azure IoT-SDKs

[Azure IoT Hub-SDKs](./iot-hub-devguide-sdks.md) bieten direkte Unterstützung für gängige Plattformen, z.B. Windows, Linux, OSX, MBED, und mobile Plattformen wie Android und iOS.  Da wir die Auswahlmöglichkeiten und Flexibilität bei der IoT-Bereitstellungen ständig erweitern, unterstützt das Java SDK auch die Plattform [Android Things](https://developer.android.com/things/).  Entwickler können die Vorteile des Android Things-Betriebssystems auf der Geräteseite nutzen und [Azure IoT Hub](about-iot-hub.md) als zentralen Nachrichtenhub verwenden, der für Millionen gleichzeitig verbundene Geräte skaliert werden kann.

In diesem Tutorial werden die Schritte zum Erstellen einer geräteseitigen Anwendung auf Android Things mit dem Azure IoT-Java SDK beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

* Für Android Things unterstützte Hardware mit ausgeführtem Android Things-Betriebssystem.  Sie können in der [Dokumentation zu Android Things](https://developer.android.com/things/get-started/kits#flash-at) nachlesen, wie Sie Android Things OS flashen.  Stellen Sie sicher, dass Ihr Android Things-Gerät mit dem Internet verbunden ist und alle wichtigen Peripheriegeräte wie Tastatur, Display und Maus angeschlossen sind.  In diesem Tutorial wird ein Raspberry Pi 3 verwendet.

* Aktuelle Version von [Android Studio](https://developer.android.com/studio/)

* Aktuelle Version von [Git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die IoT Hub-CLI-Erweiterung hinzuzufügen und die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

   **MyAndroidThingsDevice:** Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyAndroidThingsDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die *Geräteverbindungszeichenfolge* für das soeben registrierte Gerät abzurufen: Ersetzen Sie `YourIoTHubName` unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

## <a name="building-an-android-things-application"></a>Erstellen einer Android Things-Anwendung

1. Der erste Schritt beim Erstellen einer Android Things-Anwendung besteht im Herstellen einer Verbindung mit Ihren Android Things-Geräten. Schließen Sie Ihr Android Things-Gerät an ein Display an, und verbinden Sie es mit dem Internet. Android Things bietet eine [Dokumentation](https://developer.android.com/things/get-started/kits) zum Herstellen einer Verbindung mit einem WLAN. Nachdem Sie eine Verbindung mit dem Internet hergestellt haben, notieren Sie sich die unter „Networks“ (Netzwerke) aufgeführte IP-Adresse.

2. Verwenden Sie das Tool [adb](https://developer.android.com/studio/command-line/adb), um eine Verbindung mit Ihrem Android Things-Geräts über die oben angegebene IP-Adresse herzustellen. Überprüfen Sie die Verbindung mit diesem Befehl über das Terminal genau. Ihre Geräte sollten als „connected“ (verbunden) angezeigt werden.

   ```
   adb devices
   ```

3. Laden Sie unser Beispiel für Android/Android Things aus diesem [Repository](https://github.com/Azure-Samples/azure-iot-samples-java) herunter, oder verwenden Sie Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Öffnen Sie in Android Studio das Android-Projekt in „\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample“.

5. Öffnen Sie die Datei „gradle.properties“, und ersetzen Sie „Device_connection_string“ durch Ihre zuvor angegebene Geräteverbindungszeichenfolge.
 
6. Klicken Sie auf „Run – Debug“ (Ausführen – Debuggen), und wählen Sie Ihr Gerät aus, um diesen Code auf Ihren Android Things-Geräten bereitzustellen.

7. Wenn die Anwendung erfolgreich gestartet wird, sehen Sie eine ausgeführte Anwendung auf Ihrem Android Things-Gerät. Diese Beispielanwendung sendet zufällig generierte Temperaturmesswerte.

## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

Die eingehenden Daten können über die IoT Hub-Instanz angezeigt werden. Die IoT Hub-CLI-Erweiterung stellt eine Verbindung mit dem dienstseitigen Endpunkt **Events** in Ihrer IoT Hub-Instanz her. Die Erweiterung empfängt die vom simulierten Gerät gesendeten Gerät-zu-Cloud-Nachrichten. Eine IoT Hub-Back-End-Anwendung wird in der Regel in der Cloud ausgeführt, um Gerät-zu-Cloud-Nachrichten zu empfangen und zu verarbeiten.

Führen Sie in Azure Cloud Shell die folgenden Befehle aus, und ersetzen Sie dabei `YourIoTHubName` durch den Namen Ihres IoT-Hubs:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Verwalten von Konnektivität und zuverlässigem Messaging](iot-hub-reliability-features-in-sdks.md) mithilfe von IoT Hub-SDKs.
* Informieren Sie sich über das [Entwickeln für mobile Plattformen](iot-hub-how-to-develop-for-mobile-devices.md) wie etwa iOS und Android.
* [Azure IoT SDK – Plattformunterstützung](iot-hub-device-sdk-platform-support.md)