---
title: Verbinden einer generischen Node.js-Clientanwendung mit Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein generisches Node.js-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888940"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Verbinden einer generischen Clientanwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler eine generische Node.js-Anwendung, die ein echtes Gerät darstellt, mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden.

## <a name="before-you-begin"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

1. Eine Azure IoT Central-Anwendung. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
1. Einen Entwicklungscomputer mit installierter [Node.js](https://nodejs.org/) Version 4.0.0 oder höher. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Node.js ist für eine Vielzahl von Betriebssystemen verfügbar.

## <a name="create-a-device-template"></a>Erstellen einer Gerätevorlage

In Ihrer Azure IoT Central-Anwendung benötigen Sie eine Gerätevorlage mit den folgenden Messungen, Geräteeigenschaften, Einstellungen und Befehlen:

### <a name="telemetry-measurements"></a>Telemetriemessungen

Fügen Sie auf der Seite **Messungen** folgende Telemetriedaten hinzu:

| Anzeigename | Feldname  | Units | Min | max | Dezimalstellen |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatur  | Temperatur | F     | 60  | 110 | 0              |
| Luftfeuchtigkeit     | Luftfeuchtigkeit    | %     | 0   | 100 | 0              |
| Pressure     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Die Telemetriemessung gibt Daten als Gleitkommazahl aus.

Geben Sie Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, können die Telemetriedaten nicht in der Anwendung angezeigt werden.

### <a name="state-measurements"></a>Statusmessungen

Fügen Sie auf der Seite **Messungen** folgenden Status hinzu:

| Anzeigename | Feldname  | Wert 1 | Anzeigename | Wert 2 | Anzeigename |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fan Mode     | fanmode     | 1       | Wird ausgeführt      | 0       | Beendet      |

> [!NOTE]
> Die Statusmessung gibt Daten vom Typ „string“ aus.

Geben Sie Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann der Status nicht in der Anwendung angezeigt werden.

### <a name="event-measurements"></a>Ereignismessung

Fügen Sie auf der Seite **Messungen** folgendes Ereignis hinzu:

| Anzeigename | Feldname  | Severity |
| ------------ | ----------- | -------- |
| Überhitzung  | overheat    | Error    |

> [!NOTE]
> Die Ereignismessung gibt Daten vom Typ „string“ aus.

### <a name="device-properties"></a>Geräteeigenschaften

Fügen Sie auf der Seite **Eigenschaften** folgende Geräteeigenschaften hinzu:

| Anzeigename        | Feldname        | Datentyp |
| ------------------- | ----------------- | --------- |
| Seriennummer       | serialNumber      | text      |
| Gerätehersteller | manufacturer      | text      |

Geben Sie die Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, können die Eigenschaften nicht in der Anwendung angezeigt werden.

### <a name="settings"></a>Einstellungen

Fügen Sie auf der Seite **Einstellungen** folgende **Nummerneinstellungen** hinzu:

| Anzeigename    | Feldname     | Units | Dezimalstellen | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Lüfterdrehzahl       | fanSpeed       | U/Min   | 0        | 0   | 3000 | 0       |
| Set Temperature | setTemperature | F     | 0        | 20  | 200  | 80      |

Geben Sie den Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann das Gerät den Einstellungswert nicht empfangen.

### <a name="commands"></a>Befehle

Fügen Sie auf der Seite **Befehle** folgenden Befehl hinzu:

| Anzeigename    | Feldname     | Standardzeitlimit | Datentyp |
| --------------- | -------------- | --------------- | --------- |
| Countdown       | countdown      | 30              | number    |

Fügen Sie dem Befehl „Countdown“ folgendes Eingabefeld hinzu:

| Anzeigename    | Feldname     | Datentyp | Wert |
| --------------- | -------------- | --------- | ----- |
| Zählen ab      | countFrom      | number    | 10    |

Geben Sie Feldnamen genau wie in der Tabelle angegeben in der Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann der Befehl auf dem Gerät nicht verarbeitet werden.

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

Fügen Sie in der Azure IoT Central-Anwendung in der Gerätevorlage, die Sie im vorherigen Abschnitt erstellt haben, ein echtes Gerät hinzu.

Befolgen Sie dann die Anweisungen im Tutorial „Hinzufügen eines Geräts“ zum [Generieren einer Verbindungszeichenfolge für das echte Gerät](tutorial-add-device.md#generate-connection-string). Diese Verbindungszeichenfolge verwenden Sie im nächsten Abschnitt:

### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

In den folgenden Schritten wird gezeigt, wie eine Clientanwendung mit Implementierung des echten Geräts, das Sie der Anwendung hinzugefügt haben, erstellt wird. Die Node.js-Anwendung stellt hier das echte Gerät dar. 

1. Erstellen Sie auf Ihrem Computer den Ordner `connected-air-conditioner-adv`. Navigieren Sie in der Befehlszeilenumgebung zu diesem Ordner.

1. Um das Node.js-Projekt zu initialisieren, führen Sie die folgenden Befehle aus:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Erstellen Sie im Ordner `connected-air-conditioner-adv` eine Datei namens **connectedAirConditionerAdv.js**.

1. Fügen Sie am Anfang der Datei **connectedAirConditionerAdv.js** die folgenden `require`-Anweisungen hinzu:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Fügen Sie der Datei folgende Variablendeklarationen hinzu:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Aktualisieren Sie den Platzhalter `{your device connection string}` mit der [Verbindungszeichenfolge des Geräts](tutorial-add-device.md#generate-connection-string). In diesem Beispiel wird `targetTemperature` mit null initialisiert, wobei Sie auch die aktuelle Ablesung vom Gerät oder den Wert vom Gerätezwilling übernehmen können.

1. Fügen Sie zum Senden von Telemetrie-, Status- und Ereignismessungen an Ihre Azure IoT Central-Anwendung der Datei folgende Funktion hinzu:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Fügen Sie zum Senden von Eigenschaften an Ihre Azure IoT Central-Anwendung folgende Funktion zu Ihrer Datei hinzu:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Um die Einstellungen zu definieren, auf die Ihr Gerät reagiert, fügen Sie folgende Definition hinzu:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Um aktualisierte Einstellungen von Ihrer Azure IoT Central-Anwendung zu verarbeiten, fügen Sie Folgendes zur Datei hinzu:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Fügen Sie den folgenden Code für die Verarbeitung eines von der IoT Central-Anwendung gesendeten Countdown-Befehls hinzu:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Fügen Sie den folgenden Code hinzu, um die Verbindungsherstellung mit Azure IoT Central abzuschließen und die Funktionen im Clientcode einzubinden:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Ausführen der Node.js-Anwendung

Führen Sie in der Befehlszeilenumgebung folgenden Befehl aus:

```cmd/sh
node connectedAirConditionerAdv.js
```

Als Operator in der Azure IoT Central-Anwendung für Ihre echtes Gerät können Sie Folgendes tun:

* Anzeigen von Telemetrie auf der Seite **Messungen**:

    ![Anzeigen von Telemetriedaten](media/howto-connect-nodejs/viewtelemetry.png)

* Anzeigen der von Ihrem Gerät gesendeten Geräteeigenschaftswerte auf der Seite **Eigenschaften**: Die Kacheln für die Geräteeigenschaften werden nach der Verbindung mit dem Gerät aktualisiert:

    ![Anzeigen von Geräteeigenschaften](media/howto-connect-nodejs/viewproperties.png)

* Festlegen der Lüfterdrehzahl und Zieltemperatur auf der Seite **Einstellungen**:

    ![Festlegen der Lüfterdrehzahl](media/howto-connect-nodejs/setfanspeed.png)

* Aufrufen des Countdown-Befehls auf der Seite **Befehle**:

    ![Aufrufen des Countdown-Befehls](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie ein generischer Node.js-Client mit Ihrer Azure IoT Central-Anwendung verbunden wird, empfiehlt sich als nächster Schritt das [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
