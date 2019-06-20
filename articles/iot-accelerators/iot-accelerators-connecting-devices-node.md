---
title: Bereitstellen von Geräten für die Remoteüberwachung in Node.js – Azure | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie mittels einer in Node.js geschriebenen Anwendung die Verbindung eines Gerät mit dem Solution Accelerator für die Remoteüberwachung herstellen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: fdb2bed76a8e23a6034a57b3a5f1358c26e9e990
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61450264"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Herstellen einer Verbindung zwischen Ihrem Gerät und dem Solution Accelerator für die Remoteüberwachung (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In diesem Tutorial wird gezeigt, wie Sie eine Verbindung zwischen einem realen Gerät und dem Solution Accelerator für die Remoteüberwachung herstellen. In diesem Tutorial verwenden Sie Node.js. Dies ist eine gute Wahl für Umgebungen mit minimalen Ressourceneinschränkungen.

Wenn Sie lieber ein Gerät simulieren möchten, finden Sie weitere Informationen unter [Erstellen und Testen eines neuen simulierten Geräts](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="create-a-nodejs-solution"></a>Erstellen einer Node.js-Lösung

Stellen Sie sicher, dass auf dem Entwicklungscomputer mindestens die [Node.js](https://nodejs.org/)-Version 4.0.0 installiert ist. Sie können `node --version` in der Befehlszeile ausführen, um die Version zu überprüfen.

1. Erstellen Sie auf Ihrem Entwicklungscomputer den Ordner `remotemonitoring`. Navigieren Sie in der Befehlszeilenumgebung zu diesem Ordner.

1. Führen Sie die folgenden Befehle aus, um die Pakete, die Sie zum Fertigstellen der Beispiel-App benötigen, herunterzuladen und zu installieren:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. Erstellen Sie im Ordner `remotemonitoring` eine Datei mit dem Namen **remote_monitoring.js**. Öffnen Sie diese Datei in einem Texteditor.

1. Fügen Sie der Datei **remote_monitoring.js** die folgenden `require`-Anweisungen hinzu:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Fügen Sie die folgenden Variablendeklarationen nach den `require` -Anweisungen hinzu. Ersetzen Sie den Platzhalterwert `{device connection string}` durch den Wert, den Sie für das in der Remoteüberwachungslösung bereitgestellte Gerät notiert haben:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Fügen Sie die folgenden Variablen hinzu, um einige grundlegende Telemetriedaten zu definieren:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Fügen Sie die folgenden Variablen hinzu, um einige Eigenschaftswerte zu definieren:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Fügen Sie die folgende Variable hinzu, um die gemeldeten Eigenschaften zu definieren, die an die Lösung gesendet werden. Zu diesen Eigenschaften zählen Metadaten, die in der Webbenutzeroberfläche angezeigt werden:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Fügen Sie die folgende Hilfsfunktion hinzu, um Ergebnisse des Vorgangs auszugeben:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Fügen Sie die folgende Hilfsfunktion für die zufällige Festlegung der Telemetriewerte hinzu:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Fügen Sie die folgende generische Funktion zur Verarbeitung der direkten Methodenaufrufe aus der Lösung hinzu. Die Funktion zeigt Informationen über die direkte Methode an, die aufgerufen wurde, ändert aber in diesem Beispiel das Gerät in keiner Weise. Die Lösung verwendet direkte Methoden, die auf Geräte einwirken:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Fügen Sie die folgende Funktion zur Verarbeitung der direkten **FirmwareUpdate**-Methodenaufrufe aus der Lösung hinzu. Die Funktion überprüft die in der Nutzlast der direkten Methode übergebenen Parameter und führt dann asynchron eine Simulation der Firmwareaktualisierung aus:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Fügen Sie die folgende Funktion hinzu, um einen Firmwareupdatefluss mit langer Ausführungsdauer zu simulieren, der den Fortschritt wieder an die Lösung meldet:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Fügen Sie den folgenden Code zum Senden von Telemetriedaten an die Lösung hinzu. Die Clientanwendung fügt der Nachricht Eigenschaften zur Identifizierung des Nachrichtenschemas hinzu:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Fügen Sie den folgenden Code zum Erstellen einer Clientinstanz hinzu:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Fügen Sie den folgenden Code zu folgenden Zwecken hinzu:

    * Öffnen der Verbindung.
    * Einrichten eines Handlers für die gewünschten Eigenschaften.
    * Senden gemeldeter Eigenschaften.
    * Registrieren des Handlers für die direkten Methoden. Das Beispiel verwendet einen separaten Handler für die direkte Firmwareupdatemethode.
    * Starten des Sendens von Telemetriedaten.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Speichern Sie die Änderungen in der Datei **remote_monitoring.js**.

1. Führen Sie den folgenden Befehl an einer Eingabeaufforderung aus, um die Beispielanwendung zu starten:

     ```cmd/sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
