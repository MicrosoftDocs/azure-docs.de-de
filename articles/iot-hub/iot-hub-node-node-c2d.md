---
title: C2D-Nachrichten mit Azure IoT Hub (Node) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Node.js C2D-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: e2c3c3988193242cd0afe0135b019c7e6f73b59c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596723"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Senden von C2D-Nachrichten mit IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Einführung
Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md) erfahren Sie, wie ein IoT-Hub erstellt, eine Geräteidentität im Hub bereitgestellt und eine simulierte Geräte-App programmiert wird, die D2C-Nachrichten (Device to Cloud, Gerät zu Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md)auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.
* Empfangen von C2D-Nachrichten auf einem Gerät.
* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Node.js-Konsolen-Apps aus:

* **SimulatedDevice**, eine abgewandelte Version der in [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md)erstellten App, die eine Verbindung mit IoT Hub herstellt und Cloud-zu-Gerät-Nachrichten empfängt.

* **SendCloudToDeviceMessage**, die über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App sendet und dann die zugehörige Übermittlungsbestätigung empfängt.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java und JavaScript). Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.
>

Für dieses Tutorial benötigen Sie Folgendes:

* Node.js, Version 10.0.x oder höher.
* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) erstellen.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt ändern Sie die simulierte Geräte-App, die Sie in [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md) erstellt haben, um C2D-Nachrichten von IoT Hub zu empfangen.

1. Öffnen Sie die Datei „SimulatedDevice.js“ mit einem Text-Editor.

2. Ändern Sie die **connectCallback** -Funktion, sodass sie von IoT Hub gesendete Nachrichten verarbeitet. In diesem Beispiel ruft das Gerät immer die **complete** -Funktion auf, um IoT Hub mitzuteilen, dass sie die Nachricht verarbeitet hat. Die neue Version der **connectCallback**-Funktion sieht wie der folgende Codeausschnitt aus:
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Wenn Sie anstelle von MQTT oder AMQP den HTTPS-Transport verwenden, prüft die **DeviceClient**-Instanz nur selten (seltener als alle 25 Minuten), ob Nachrichten von IoT Hub vorliegen. Weitere Informationen zu den Unterschieden zwischen der MQTT-, AMQP- und HTTPS-Unterstützung sowie zur IoT Hub-Drosselung finden Sie im [Entwicklungshandbuch für IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md) hinzugefügt haben. Sie benötigen auch die IoT Hub-Verbindungszeichenfolge für den Hub, die Sie im [Azure-Portal](https://portal.azure.com) finden.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **sendcloudtodevicemessage**. Erstellen Sie im Ordner **sendcloudtodevicemessage** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen. Übernehmen Sie alle Standardeinstellungen:

    ```shell
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **sendcloudtodevicemessage** den folgenden Befehl aus, um das Paket **azure-iothub** zu installieren:
   
    ```shell
    npm install azure-iothub --save
    ```

3. Erstellen Sie mithilfe eines Text-Editors die Datei **SendCloudToDeviceMessage.js** im Ordner **sendcloudtodevicemessage**.

4. Fügen Sie am Anfang der Datei **SendCloudToDeviceMessage.js** die folgenden `require`-Anweisungen hinzu:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Fügen Sie der Datei **SendCloudToDeviceMessage.js** den folgenden Code hinzu. Ersetzen Sie den Platzhalterwert „{iot hub connection string}“ durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md) erstellt haben. Ersetzen Sie den Platzhalter „{device id}“ durch die Geräte-ID des Geräts, das Sie im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-node.md) hinzugefügt haben:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Fügen Sie die folgende Funktion hinzu, um Ergebnisse des Vorgangs an der Konsole auszugeben:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Fügen Sie die folgende Funktion hinzu, um Übermittlungsfeedbacknachrichten an der Konsole auszugeben:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Fügen Sie den folgenden Code hinzu, um eine Nachricht an Ihr Gerät zu senden und die Feedbacknachricht zu verarbeiten, wenn das Gerät die C2D-Nachricht bestätigt:
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Speichern und schließen Sie die Datei **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie im Ordner **simulateddevice** an einer Eingabeaufforderung den folgenden Befehl aus, um Telemetriedaten an IoT Hub zu senden und auf C2D-Nachrichten zu lauschen:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Ausführen der simulierten Geräte-App](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Führen Sie im Ordner **sendcloudtodevicemessage** an einer Eingabeaufforderung den folgenden Befehl aus, um eine C2D-Nachricht zu senden, und warten Sie auf das Bestätigungsfeedback:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Ausführen der App zum Senden des C2D-Befehls](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
   >

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden. 

Beispiele vollständiger Lösungen, die IoT Hub nutzen, finden Sie unter [Azure IoT-Solution Accelerator für die Remoteüberwachung](https://azure.microsoft.com/documentation/suites/iot-suite/).

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).