---
title: Erste Schritte mit der Azure IoT Hub-Geräteverwaltung (Node) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der IoT Hub-Geräteverwaltung den Neustart eines Remotegeräts initiieren. Sie verwenden das Azure IoT SDK für Node.js, um eine simulierte Geräte-App mit einer direkten Methode und eine Dienst-App zu implementieren, die die direkte Methode aufruft.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 9bc34110b85119c7b8b303774b585d03ce9d3a0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596645"
---
# <a name="get-started-with-device-management-node"></a>Erste Schritte mit der Geräteverwaltung (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Verwenden Sie das [Azure-Portal](https://portal.azure.com) zum Erstellen einer IoT Hub-Instanz und einer Geräteidentität im IoT Hub.

* Erstellen Sie eine simulierte Geräte-App, die eine direkte Methode enthält, die das Gerät neu startet. Direkte Methoden werden aus der Cloud aufgerufen.

* Erstellen einer Node.js-Konsolenanwendung, die über Ihre IoT Hub-Instanz eine direkte Neustartmethode in der simulierten Geräte-App aufruft

Am Ende dieses Tutorials verfügen Sie über zwei Node.js-Konsolen-Apps:

* **dmpatterns_getstarted_device.js**: Stellt mit der zuvor erstellten Geräteidentität eine Verbindung mit dem IoT Hub her, empfängt eine direkte Methode zum Neustart, simuliert einen physischen Neustart und meldet den Zeitpunkt des letzten Neustarts.

* **dmpatterns_getstarted_service.js**: Ruft eine direkte Methode in der simulierten Geräte-App auf und zeigt die Antwort sowie die gemeldeten aktualisierten Eigenschaften an.

Für dieses Tutorial benötigen Sie Folgendes:

* Node.js, Version 10.0.x oder höher. Unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt führen Sie die folgenden Schritte aus:

* Erstellen einer Node.js-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet

* Auslösen eines simulierten Geräteneustarts

* Verwenden der gemeldeten Eigenschaften, um Gerätezwillingsabfragen zu ermöglichen, die Geräte und den Zeitpunkt ihres letzten Neustarts ermitteln

1. Erstellen Sie einen leeren Ordner mit dem Namen **manageddevice**.  Erstellen Sie im Ordner **manageddevice** die Datei „package.json“, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:
      
    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **manageddevice** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iot-device** und das Paket **azure-iot-device-mqtt** zu installieren:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Erstellen Sie mit einem Text-Editor im Ordner **manageddevice** die Datei **dmpatterns_getstarted_device.js**.

4. Fügen Sie am Anfang der Datei **dmpatterns_getstarted_device.js** die folgenden require-Anweisungen ein:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Fügen Sie die Variable **connectionString** hinzu, und verwenden Sie sie zum Erstellen einer **Client**-Instanz.  Ersetzen Sie die Verbindungszeichenfolge durch Ihre Geräteverbindungszeichenfolge.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Fügen Sie die folgende Funktion hinzu, um die direkte Methode auf dem Gerät zu implementieren
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Öffnen Sie die Verbindung mit dem IoT Hub, und starten Sie den Listener der direkten Methode:

   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Speichern und schließen Sie die Datei **dmpatterns_getstarted_device.js**.

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Auslösen eines Remoteneustarts auf dem Gerät über eine direkte Methode

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die einen Remote-Neustart auf einem Gerät über eine direkte Methode auslöst. Die App verwendet Gerätezwillingsabfragen, um den Zeitpunkt des letzten Neustarts bei diesem Gerät zu ermitteln.

1. Erstellen Sie einen leeren Ordner mit dem Namen **triggerrebootondevice**. Erstellen Sie im Ordner **triggerrebootondevice** die Datei „package.json“, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen. Übernehmen Sie alle Standardeinstellungen:
   
    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **triggerrebootondevice** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iothub** und das Paket **azure-iot-device-mqtt** zu installieren:
   
    ```
    npm install azure-iothub --save
    ```

3. Erstellen Sie mit einem Text-Editor im Ordner **triggerrebootondevice** die Datei **dmpatterns_getstarted_service.js**.

4. Fügen Sie am Anfang der Datei **dmpatterns_getstarted_service.js** die folgenden require-Anweisungen ein:

  
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Fügen Sie die folgenden Variablendeklarationen hinzu, und ersetzen Sie die Platzhalterwerte:

   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Fügen Sie die folgende Funktion hinzu, um durch Aufrufen der Gerätemethode das Zielgerät neu zu starten:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Fügen Sie die folgende Funktion hinzu, um das Gerät abzufragen und den letzten Neustartzeitpunkt zu erhalten:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Fügen Sie den folgenden Code hinzu, um die Funktionen aufrufen, die die direkte Methode zum Neustart auslösen und den letzten Neustartzeitpunkt abfragen:

   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Speichern und schließen Sie die Datei **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen.

1. Führen Sie an der Eingabeaufforderung im Ordner **manageddevice** den folgenden Befehl aus, um mit dem Lauschen auf die direkte Methode zum Neustarten zu beginnen.

   
    ```
    node dmpatterns_getstarted_device.js
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **triggerrebootondevice** den folgenden Befehl aus, um den Remoteneustart und die Abfrage an den Gerätezwilling zum Ermitteln des letzten Neustartzeitpunkts auszulösen.

   
    ```
    node dmpatterns_getstarted_service.js
    ```

3. Die Reaktion des Geräts auf die direkte Methode wird in der Konsole angezeigt.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]