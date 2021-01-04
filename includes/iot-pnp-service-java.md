---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 788aa0ebe9df91caba2ee279df96cbea175975e4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487820"
---
IoT Plug & Play vereinfacht IoT, indem es Ihnen die Interaktion mit den Funktionen eines Geräts – ohne Kenntnisse der zugrunde liegenden Geräteimplementierung – ermöglicht. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Java eine Verbindung mit einem IoT Plug & Play-Gerät herstellen, das mit Ihrer Lösung verbunden ist, und wie Sie dieses Gerät steuern.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um diesen Schnellstart unter Windows auszuführen zu können:

* Java SE Development Kit 8. Wählen Sie unter [Langfristiger Java-Support für Azure und Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) unter **Langfristiger Support** die Option **Java 8** aus.
* [Apache Maven 3](https://maven.apache.org/download.cgi)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonen des SDK-Repositorys mit dem Beispielcode

Wenn Sie [Schnellstart: Verbinden einer unter Windows ausgeführten IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md) abgeschlossen haben, wurde das Repository bereits geklont.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [Microsoft Azure IoT SDKs für Java](https://github.com/Azure/azure-iot-sdk-java) an diesem Speicherort zu klonen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Erstellen und Ausführen des Beispielgeräts

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

In dieser Schnellstartanleitung verwenden Sie ein in Java geschriebenes Thermostat als Beispiel für ein IoT Plug & Play-Gerät. So führen Sie das Beispielgerät aus:

1. Öffnen Sie ein Terminalfenster, und navigieren Sie zu dem lokalen Ordner mit dem Microsoft Azure IoT SDK für Java, das Sie auf GitHub geklont haben.

1. Dieses Terminalfenster wird als Ihr **Geräteterminal** verwendet. Navigieren Sie zum Stammordner des geklonten Repositorys. Installieren Sie alle Abhängigkeiten, indem Sie den folgenden Befehl ausführen:

1. Führen Sie den folgenden Befehl aus, um die Beispielgeräteanwendung zu erstellen:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Navigieren Sie zum Ausführen der Beispielgeräteanwendung zum Ordner *device\iot-device-samples\pnp-device-sample\thermostat-device-sample*, und führen Sie den folgenden Befehl aus:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Das Gerät ist jetzt zum Empfangen von Befehlen und Eigenschaftsaktualisierungen bereit und hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispielgeräts während der nächsten Schritte bei.

## <a name="run-the-sample-solution"></a>Ausführen der Beispiellösung

In [Einrichten Ihrer Umgebung für die IoT Plug & Play-Schnellstarts und -Tutorials](../articles/iot-pnp/set-up-environment.md) haben Sie zwei Umgebungsvariablen erstellt, um das Beispiel so zu konfigurieren, dass eine Verbindung mit Ihrem IoT-Hub und -Gerät hergestellt wird:

* **IOTHUB_CONNECTION_STRING**: die IoT-Hub-Verbindungszeichenfolge, die Sie sich zuvor notiert haben.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In dieser Schnellstartanleitung verwenden Sie eine in Java geschriebene IoT-Beispiellösung für die Interaktion mit dem gerade eingerichteten Beispielgerät.

> [!NOTE]
> In diesem Beispiel wird der Namespace **com.microsoft.azure.sdk.iot.service** des **IoT Hub-Dienstclients** verwendet. Weitere Informationen zu den APIs, einschließlich der Digital Twins-API, finden Sie im [Leitfaden für Dienstentwickler](../articles/iot-pnp/concepts-developer-guide-service.md).

1. Öffnen Sie ein weiteres Terminalfenster zur Verwendung als Ihr **Dienstterminal**.

1. Navigieren Sie im geklonten Java SDK-Repository zum Ordner *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Führen Sie den folgenden Befehl aus, um die Beispieldienstanwendung auszuführen:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Gerätezwilling abrufen

Im folgenden Codeausschnitt ist dargestellt, wie Sie den Gerätezwilling im Dienst abrufen:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Aktualisieren eines Gerätezwillings

Der folgende Codeausschnitt zeigt, wie Sie Eigenschaften über den Gerätezwilling mit einem *Patch* aktualisieren:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

In der Geräteausgabe können Sie sehen, wie das Gerät auf diese Aktualisierung der Eigenschaft reagiert.

### <a name="invoke-a-command"></a>Aufrufen eines Befehls

Der folgende Codeausschnitt veranschaulicht, wie Sie auf dem Gerät einen Befehl aufrufen:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

In der Geräteausgabe können Sie sehen, wie das Gerät auf diesen Befehl reagiert.
