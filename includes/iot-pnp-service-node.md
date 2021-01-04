---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487810"
---
IoT Plug & Play vereinfacht IoT, indem es Ihnen die Interaktion mit den Funktionen eines Geräts – ohne Kenntnisse der zugrunde liegenden Geräteimplementierung – ermöglicht. In diesem Schnellstart erfahren Sie, wie Sie mit Node.js eine Verbindung mit einem IoT Plug & Play-Gerät, das mit Ihrer Lösung verbunden ist, herstellen und dieses steuern.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Um diesen Schnellstart abzuschließen, benötigen Sie Node.js auf Ihrem Entwicklungscomputer. Sie können die neueste empfohlene Version für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonen des SDK-Repositorys mit dem Beispielcode

Klonen Sie die Beispiele aus einem [Node SDK-Repository](https://github.com/Azure/azure-iot-sdk-node). Öffnen Sie ein Terminalfenster in einem Ordner Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) zu klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Ausführen des Beispielgeräts

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

In diesem Schnellstart verwenden Sie ein Beispielthermostat, das in Node.js als IoT Plug & Play-Gerät geschrieben wird. So führen Sie das Beispielgerät aus:

1. Öffnen Sie ein Terminalfenster, und navigieren Sie zu dem lokalen Ordner mit dem Microsoft Azure IoT SDK for Node.js, das Sie auf GitHub geklont haben.

1. Dieses Terminalfenster wird als Ihr **Geräteterminal** verwendet. Wechseln Sie zum Ordner Ihres geklonten Repositorys, und navigieren Sie zum Ordner */azure-iot-sdk-node/device/samples/pnp*. Installieren Sie alle Abhängigkeiten, indem Sie den folgenden Befehl ausführen:

    ```cmd/sh
    npm install
    ```

1. Führen Sie den Beispielthermostat mit dem folgenden Befehl aus:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Es werden Nachrichten darüber angezeigt, dass das Gerät Informationen gesendet und sich selbst als „online“ gemeldet hat. Diese Nachrichten weisen darauf hin, dass das Gerät damit begonnen hat, Telemetriedaten an den Hub zu senden und jetzt bereit zum Empfangen von Befehlen und Eigenschaftsaktualisierungen ist. Schließen Sie dieses Terminal nicht. Sie benötigen es zur Prüfung, ob das Dienstbeispiel funktioniert.

## <a name="run-the-sample-solution"></a>Ausführen der Beispiellösung

In [Einrichten Ihrer Umgebung für die IoT Plug & Play-Schnellstarts und -Tutorials](../articles/iot-pnp/set-up-environment.md) haben Sie zwei Umgebungsvariablen erstellt, um das Beispiel so zu konfigurieren, dass eine Verbindung mit Ihrem IoT-Hub und -Gerät hergestellt wird:

* **IOTHUB_CONNECTION_STRING**: die IoT-Hub-Verbindungszeichenfolge, die Sie sich zuvor notiert haben.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In diesem Schnellstart verwenden Sie eine Beispiel-IoT-Lösung in Node.js für die Interaktion mit dem gerade eingerichteten Beispielgerät.

1. Öffnen Sie ein weiteres Terminalfenster zur Verwendung als Ihr **Dienstterminal**.

1. Navigieren Sie im geklonten Node SDK-Repository zum Ordner */azure-iot-sdk-node/service/samples/javascript*. Installieren Sie alle Abhängigkeiten, indem Sie den folgenden Befehl ausführen:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Lesen einer Eigenschaft

1. Bei Ihrer Ausführung des Beispielthermostats im **Geräteterminal** wurden folgende Nachrichten zur Mitteilung von dessen Onlinestatus angezeigt:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Wechseln Sie zum **Dienstterminal**, und führen Sie den folgenden Befehl aus, um das Beispiel für das Lesen von Geräteinformationen auszuführen:

    ```cmd/sh
    node twin.js
    ```

1. Beachten Sie in der Ausgabe des **Dienstterminals** die Antwort des Gerätezwillings. Die Modell-ID des Geräts und die zugehörigen Eigenschaften werden angezeigt:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Der folgende Codeausschnitt zeigt den Code in *twin.js*, mit dem die Modell-ID des Gerätezwillings abgerufen wird:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

In diesem Szenario wird `Model Id: dtmi:com:example:Thermostat;1` ausgegeben.

> [!NOTE]
> In diesen Dienstbeispielen wird die **Registry**-Klasse aus dem **IoT Hub-Dienstclient** verwendet. Weitere Informationen zu den APIs, einschließlich der Digital Twins-API, finden Sie im [Leitfaden für Dienstentwickler](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Aktualisieren einer schreibbaren Eigenschaft

1. Öffnen Sie die Datei *twin.js* in einem Code-Editor.

1. Sehen Sie sich den Beispielcode an. Darin werden zwei Vorgehensweisen zum Aktualisieren des Gerätezwillings veranschaulicht. Ändern Sie bei der ersten Vorgehensweise die Variable `twinPatch` wie folgt:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    Die Eigenschaft `targetTemperature` ist im Thermostat-Gerätemodell als schreibbare Eigenschaft definiert.

1. Verwenden Sie im **Dienstterminal** den folgenden Befehl zur Ausführung des Beispiels zum Aktualisieren der Eigenschaft:

    ```cmd/sh
    node twin.js
    ```

1. In Ihrem **Geräteterminal** sehen Sie, dass das Gerät die Aktualisierung erhalten hat:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. Führen Sie in Ihrem **Dienstterminal** den folgenden Befehl zur Bestätigung aus, dass die-Eigenschaft aktualisiert wurde:

    ```cmd/sh
    node twin.js
    ```

1. In der Ausgabe des **Dienstterminals** wird im Abschnitt mit den `reported` Eigenschaften die gemeldete aktualisierte Zieltemperatur angezeigt. Es kann eine Weile dauern, bis das Gerät die Aktualisierung abgeschlossen hat. Wiederholen Sie diesen Schritt so oft, bis das Gerät die Eigenschaftsaktualisierung verarbeitet hat:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Aufrufen eines Befehls

1. Öffnen Sie die Datei *device_method.js*, und sehen Sie sich den Code an.

1. Wechseln Sie zum **Dienstterminal**. Verwenden Sie den folgenden Befehl, um das Beispiel für das Aufrufen des Befehls auszuführen:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. Die Ausgabe im **Dienstterminal** zeigt die folgende Bestätigung:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. Im **Geräteterminal** sehen Sie, dass der Befehl bestätigt wird:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
