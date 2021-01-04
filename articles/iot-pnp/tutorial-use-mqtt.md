---
title: 'Tutorial: Erstellen eines Azure IoT Plug & Play-Geräteclients mithilfe von MQTT | Microsoft-Dokumentation'
description: 'Tutorial: Direktes Verwenden des MQTT-Protokolls zur Erstellung eines IoT Plug & Play-Geräteclients, ohne die Azure IoT-Geräte-SDKs verwenden zu müssen'
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6852b0532b23e46c7b986926b21cd0b7e9f9736d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421378"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Tutorial: Entwickeln eines IoT Plug & Play-Geräteclients mithilfe von MQTT

IoT Plug & Play-Geräteclients sollten nach Möglichkeit immer mithilfe eines der Azure IoT-Geräte-SDKs erstellt werden. In Szenarien, in denen beispielsweise ein Gerät mit eingeschränktem Arbeitsspeicher verwendet wird, muss jedoch für die Kommunikation mit Ihrem IoT-Hub unter Umständen eine MQTT-Bibliothek verwendet werden.

Im Beispiel dieses Tutorials werden die MQTT-Bibliothek [Eclipse Mosquitto](http://mosquitto.org/) und Visual Studio verwendet. Bei den Schritten in diesem Tutorial wird davon ausgegangen, dass einen Entwicklungscomputer unter Windows verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um dieses Tutorial unter Windows ausführen zu können:

* [Visual Studio (Community, Professional oder Enterprise):](https://visualstudio.microsoft.com/downloads/) Schließen Sie die Workload **Desktopentwicklung mit C++** ein, wenn Sie Visual Studio [installieren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019).
* [Git-Client](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Mithilfe des Tools *Azure IoT* können Sie Ihrem IoT-Hub ein neues Gerät hinzufügen. Sie haben Ihren IoT-Hub und das Tool Azure IoT-Explorer konfiguriert beim Abschluss von [Einrichten Ihrer Umgebung für die IoT Plug & Play-Schnellstarts und -Tutorials](set-up-environment.md):

1. Starten Sie das Tool **Azure IoT-Explorer**.
1. Wählen Sie auf der Seite **IoT-Hubs** die Option **Geräte in diesem Hub anzeigen** aus.
1. Wählen Sie auf der Seite **Geräte**  die Option **+ Neu** aus.
1. Erstellen Sie ein Gerät mit dem Namen *my-mqtt-device*, das einen automatisch generierten symmetrischen Schlüssel verwendet.
1. Erweitern Sie auf der Seite **Geräteidentität** die Option **Verbindungszeichenfolge mit SAS-Token**.
1. Wählen Sie den **Primärschlüssel**, der als **Symmetrischer Schlüssel** verwendet werden soll, legen Sie die Ablaufzeit auf 60 Minuten fest, und wählen Sie **Generieren** aus.
1. Kopieren Sie die generierte **SAS-Tokenverbindungszeichenfolge**. Sie verwenden diesen Wert später im Tutorial.

## <a name="clone-sample-repo"></a>Klonen des Beispielrepositorys

Verwenden Sie den folgenden Befehl, um das Beispielrepository an einem geeigneten Ort auf Ihrem lokalen Computer zu klonen:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Installieren der MQTT-Bibliothek

Verwenden Sie das Tool `vcpkg`, um die Bibliothek „Eclipse Mosquitto“ herunterzuladen und zu erstellen.

Verwenden Sie den folgenden Befehl, um das Repository **Vcpkg** an einem geeigneten Ort auf Ihrem lokalen Computer zu klonen:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Bereiten Sie die Umgebung `vcpkg` mithilfe der folgenden Befehle vor. `vcpkg integrate install` muss an einer Eingabeaufforderung mit erhöhten Rechten ausgeführt werden:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Verwenden Sie den folgenden Befehl, um die Bibliothek „Eclipse Mosquitto“ herunterzuladen und zu erstellen:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrieren des Beispiels zu IoT Plug & Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Überprüfen des IoT Plug & Play-fremden Beispielcodes

Aktualisieren Sie den Code mit Details zu Ihrem IoT-Hub und zu Ihrem Gerät, bevor Sie ihn erstellen und ausführen.

Öffnen Sie die Projektmappendatei *MQTTWin32.sln* aus dem Ordner *IoTMQTTSample\src\Windows* in Visual Studio, um den Beispielcode anzuzeigen.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **TelemetryMQTTWin32**, und wählen Sie **Als Startprojekt festlegen** aus.

Öffnen Sie im Projekt **TelemetryMQTTWin32** die Quelldatei **MQTT_Mosquitto.cpp**. Aktualisieren Sie die Verbindungsinformationsdefinitionen mit den Gerätedetails, die Sie sich zuvor notiert haben. Ersetzen Sie die Platzhalter für die Tokenzeichenfolge für diese Elemente folgendermaßen:

* den Bezeichner `IOTHUBNAME` durch den Namen Ihres IoT-Hubs.
* den Bezeichner `DEVICEID` durch `my-mqtt-device`.
* den Bezeichner `PWD` durch den richtigen Pfad der SAS-Tokenverbindungszeichenfolge, die Sie für das Gerät generiert haben. Verwenden Sie den Teil der Verbindungszeichenfolge von `SharedAccessSignature sr=` bis zum Ende.

Vergewissern Sie sich, dass der Code ordnungsgemäß funktioniert, indem Sie Azure IoT-Explorer und das Lauschen auf Telemetriedaten starten.

Führen Sie die Anwendung durch Drücken von STRG+F5 aus. Nach einigen Sekunden wird eine Ausgabe wie die folgende angezeigt:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Ausgabe der MQTT-Beispielanwendung":::

In Azure IoT-Explorer sehen Sie, dass es sich bei dem Gerät nicht um ein IoT Plug & Play-Gerät handelt:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="IoT Plug & Play-fremdes Gerät in Azure IoT-Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Konfigurieren des Geräts als IoT Plug & Play-Gerät

IoT Plug & Play-Geräte müssen eine Reihe einfacher Konventionen erfüllen. Wenn ein Gerät bei der Verbindungsherstellung eine Modell-ID sendet, wird es zu einem IoT Plug & Play-Gerät.

In diesem Beispiel fügen Sie dem MQTT-Verbindungspaket eine Modell-ID hinzu. Sie übergeben die Modell-ID als Abfragezeichenfolgenparameter in `USERNAME` und ändern die API-Version (`api-version`) in `2020-09-30`:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Erstellen Sie das Beispiel neu, und führen Sie es aus.

Der Gerätezwilling enthält nun die Modell-ID:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Anzeigen der Modell-ID in Azure IoT-Explorer":::

Nun können Sie durch die IoT Plug & Play-Komponente navigieren:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Anzeigen von Komponenten im Azure IoT-Explorer":::

Sie können nun Ihren Gerätecode ändern, um die in Ihrem Modell definierten Telemetriedaten, Eigenschaften und Befehle zu implementieren. Ein Beispiel für die Implementierung des Thermostatgeräts unter Verwendung der Mosquitto-Bibliothek finden Sie auf GitHub unter [Verwenden von MQTT-PnP mit Azure IoT Hub ohne IoT SDK unter Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32).

> [!NOTE]
>Der Client verwendet die Stammzertifikatdatei `IoTHubRootCA_Baltimore.pem`, um die Identität des IoT-Hubs zu überprüfen, mit dem er eine Verbindung herstellt.

### <a name="mqtt-topics"></a>MQTT-Themen

Die folgenden Definitionen gelten für die MQTT-Themen, die das Gerät zum Senden von Informationen an den IoT-Hub verwendet. Weitere Informationen finden Sie unter [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE`: Definiert das Thema, das vom Gerät zum Melden der implementierten Schnittstellen verwendet wird.
* `DEVICETWIN_PATCH_MESSAGE`: Definiert das Thema, das vom Gerät verwendet wird, um Eigenschaftsaktualisierungen an den IoT-Hub zu melden.
* `DEVICE_TELEMETRY_MESSAGE`: Definiert das Thema, das vom Gerät verwendet wird, um Telemetriedaten an Ihren IoT-Hub zu senden.

Weitere Informationen zu MQTT finden Sie im GitHub-Repository [Verwenden von MQTT mit Azure IoT Hub ohne SDK](https://github.com/Azure-Samples/IoTMQTTSample/).
  
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie einen MQTT-Geräteclient so ändern, dass er den IoT Plug & Play-Konventionen entspricht. Weitere Informationen zu IoT Plug & Play finden Sie in dem folgenden Artikel:

> [!div class="nextstepaction"]
> [Aufbau](concepts-architecture.md)

Weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll finden Sie hier:

> [!div class="nextstepaction"]
> [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](../iot-hub/iot-hub-mqtt-support.md)