---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7961dae0ef227a7e321992e0b239f7a3e6e66b0a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511654"
---
In diesem Schnellstart erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung erstellen, mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung wurde in Java geschrieben und ist im Azure IoT-Geräte-SDK für Java enthalten. Ein Lösungsentwickler kann mithilfe von Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um diesen Schnellstart unter Windows auszuführen zu können:

* Java SE Development Kit 8. Wählen Sie unter [Langfristiger Java-Support für Azure und Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) unter **Langfristiger Support** die Option **Java 8** aus.
* [Apache Maven 3](https://maven.apache.org/download.cgi)

## <a name="download-the-code"></a>Laden Sie den Code herunter.

In diesem Schnellstart bereiten Sie eine Entwicklungsumgebung vor, die Sie zum Klonen und Erstellen des Azure IoT Hub-Geräte-SDKs für Java verwenden können.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl zum Klonen des GitHub-Repositorys für [Azure IoT-Java-SDKs und die zugehörigen Bibliotheken](https://github.com/Azure/azure-iot-sdk-java) an diesem Speicherort aus:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Erstellen des Codes

Navigieren Sie unter Windows zum Stammordner des geklonten Java SDK-Repositorys.

Führen Sie den folgenden Befehl aus, um die Beispielanwendung zu erstellen:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Navigieren Sie zum Ordner *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Führen Sie den folgenden Befehl aus, um die Beispielanwendung auszuführen:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Das Gerät ist jetzt zum Empfangen von Befehlen und Eigenschaftsaktualisierungen bereit und hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein einfacher IoT Plug & Play-Thermostat implementiert. Das in diesem Beispiel implementierte Modell verwendet keine IoT Plug & Play-[Komponenten](../articles/iot-pnp/concepts-components.md). Die [DTDL-Modelldatei für das Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiert die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle.

Der Gerätecode verwendet die Standardklasse `DeviceClient` zum Herstellen einer Verbindung mit Ihrem IoT-Hub. Das Gerät sendet die Modell-ID des in der Verbindungsanforderung implementierten DTDL-Modells. Ein Gerät, das eine Modell-ID sendet, ist ein IoT Plug & Play-Gerät:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Die Modell-ID wird im Code gespeichert, wie im folgenden Codeausschnitt zu sehen ist:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Der Code, der Eigenschaften aktualisiert, Befehle verarbeitet und Telemetriedaten sendet, ist identisch mit dem Code für ein Gerät, das keine IoT Plug & Play Konventionen verwendet.

Im Beispiel werden JSON-Objekte mithilfe einer JSON-Bibliothek in den von Ihrem IoT-Hub gesendeten Nutzlasten analysiert:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
