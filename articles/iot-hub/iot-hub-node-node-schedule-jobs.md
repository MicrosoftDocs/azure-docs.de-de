---
title: Planen von Aufträgen mit Azure IoT Hub (Node) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um eine direkte Methode auf mehreren Geräten aufzurufen. Sie verwenden die Azure IoT SDKs für Node.js, um die simulierten Geräte-Apps und eine Dienst-App für die Auftragsausführung zu implementieren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: af35a84c299544e43988547771ddce75fd71bd90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065787"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Planen und Übertragen von Aufträgen (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, mit dem eine Back-End-App Aufträge zur Planung und Aktualisierung von Millionen von Geräten erstellen und nachverfolgen kann.  Aufträge können für die folgenden Aktionen verwendet werden:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

Vom Konzept her umschließt ein Auftrag eine dieser Aktionen und verfolgt den Ausführungsfortschritt für eine Gruppe von Geräten nach, die anhand einer Gerätezwillingsabfrage definiert wird.  Mit einem Auftrag kann eine Back-End-App beispielsweise eine Neustartmethode auf 10.000 Geräten aufrufen – angegeben durch eine Gerätezwillingsabfrage und geplant für einen späteren Zeitpunkt. Diese Anwendung kann dann den Fortschritt nachverfolgen, wenn diese Geräte jeweils die Neustartmethode empfangen und ausführen.

Weitere Informationen zu diesen Funktionen finden Sie in den folgenden Artikeln:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-node-node-twin-getstarted.md) und [Tutorial: Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)

* Direkte Methoden: [Verstehen und Aufrufen direkter Methoden von IoT Hub](iot-hub-devguide-direct-methods.md) und [Tutorial: Verwenden von direkten Methoden](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen einer simulierten Node.js-Geräte-App mit einer direkten Methode, um die Verwendung der **lockDoor**-Funktion zu ermöglichen, die vom Lösungs-Back-End aufgerufen werden kann

* Erstellen einer Node.js-Konsolen-App, die die direkte **lockDoor**-Methode in der simulierten Geräte-App über einen Auftrag aufruft und die gewünschten Eigenschaften über einen Geräteauftrag aktualisiert

Am Ende dieses Tutorials verfügen Sie über zwei Node.js-Apps:

* **simDevice.js**: Stellt mit der Geräteidentität eine Verbindung mit Ihrem IoT Hub her und empfängt eine direkte **lockDoor**-Methode.

* **scheduleJobService.js**: Ruft eine direkte Methode in der simulierten Geräte-App auf und aktualisiert die gewünschten Eigenschaften des Gerätezwillings per Auftrag.

Für dieses Tutorial benötigen Sie Folgendes:

* Node.js, Version 10.0.x oder höher: Im Artikel [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet. Dadurch wird eine simulierte **lockDoor**-Methode ausgelöst.

1. Erstellen Sie einen leeren Ordner mit dem Namen **simDevice**.  Erstellen Sie im Ordner **simDevice** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

   ```
   npm init
   ```

2. Führen Sie an der Eingabeaufforderung im Ordner **simDevice** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iot-device** und das Paket **azure-iot-device-mqtt** zu installieren:
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Erstellen Sie mit einem Text-Editor im Ordner **simDevice** die neue Datei **simDevice.js**.

4. Fügen Sie am Anfang der Datei **simDevice.js** die folgenden require-Anweisungen hinzu:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Fügen Sie die Variable **connectionString** hinzu, und verwenden Sie sie zum Erstellen einer **Client**-Instanz.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Fügen Sie die folgende Funktion zur Behandlung der **lockDoor**-Methode hinzu.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Fügen Sie den folgenden Code hinzu, um den Handler für die **lockDoor**-Methode zu registrieren.

   ```
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Speichern und schließen Sie die Datei **simDevice.js**.

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planen von Aufträgen zum Aufrufen einer direkten Methode und Aktualisieren der Eigenschaften eines Gerätezwillings

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, mit der eine **lockDoor**-Remotefunktion auf einem Gerät mit einer direkten Methode initiiert wird, und aktualisieren die Eigenschaften des Gerätezwillings.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **scheduleJobService**.  Erstellen Sie im Ordner **scheduleJobService** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **scheduleJobService** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iothub** und das Paket **azure-iot-device-mqtt** zu installieren:
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Erstellen Sie mit einem Text-Editor die neue Datei **scheduleJobService.js** im Ordner **scheduleJobService**.

4. Fügen Sie am Anfang der Datei **dmpatterns_gscheduleJobServiceetstarted_service.js** die folgenden require-Anweisungen ein:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Fügen Sie die folgenden Variablendeklarationen hinzu, und ersetzen Sie die Platzhalterwerte:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Fügen Sie die folgende Funktion hinzu, die zum Überwachen der Auftragsausführung verwendet werden soll:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Fügen Sie den folgenden Code zum Planen des Auftrags hinzu, mit dem die Gerätemethode aufgerufen wird:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Fügen Sie den folgenden Code zum Planen des Auftrags hinzu, mit dem der Gerätezwilling aktualisiert wird:
   
    ```
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Speichern und schließen Sie die Datei **scheduleJobService.js**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an der Eingabeaufforderung im Ordner **simDevice** den folgenden Befehl aus, um mit dem Lauschen auf die direkte Methode zum Neustarten zu beginnen.
   
    ```
    node simDevice.js
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **scheduleJobService** den folgenden Befehl aus, um die Aufträge zum Absperren der Tür und zum Aktualisieren des Zwillings auszulösen.
   
    ```
    node scheduleJobService.js
    ```

3. Die Reaktion des Geräts auf die direkte Methode wird in der Konsole angezeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Auftrag zum Planen einer direkten Methode für ein Gerät und eines Updates der Eigenschaften eines Gerätezwillings verwendet.

Weitere Informationen zu den ersten Schritten mit IoT Hub und Geräteverwaltungsmustern, z. B. drahtloses Firmware-Remoteupdate, finden Sie unter [Tutorial: Durchführen eines Firmwareupdates](tutorial-firmware-update.md).

Weitere Informationen zu den ersten Schritten mit IoT Hub finden Sie unter [Erste Schritte mit Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
