---
title: Beginnen mit der Modulidentität und dem Modulzwilling von Azure IoT Hub (Node.js)
description: Erfahren Sie, wie Sie mit IoT SDKs für Node.js eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 9de3f45a9d62a9d131583c133440ac8a311a468d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993291"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts und geben Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen sie isolierte Konfigurationen und Zustände für die einzelnen Komponenten.

Am Ende dieses Tutorials verfügen Sie über zwei Node.js-Apps:

* **CreateIdentities**: Hiermit werden eine Geräteidentität, eine Modulidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden Ihrer Geräte- und Modulclients erstellt.

* **UpdateModuleTwinReportedProperties**: Hiermit werden aktualisierte vom Modulzwilling gemeldete Eigenschaften an Ihren IoT Hub sendet.

> [!NOTE]
> Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Voraussetzungen

* Node.js, Version 10.0.x oder höher. Unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Erstellen einer Geräteidentität und einer Modulidentität in IoT Hub

In diesem Abschnitt erstellen Sie eine Node.js-App, mit der eine Geräte- und eine Modulidentität in der Identitätsregistrierung Ihres IoT Hub erstellt wird. Ein Gerät oder Modul kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im Abschnitt zur Identitätsregistrierung des [IoT Hub-Entwicklerhandbuchs](iot-hub-devguide-identity-registry.md). Wenn Sie diese Konsolen-App ausführen, generiert sie eine eindeutige ID und einen eindeutigen Schlüssel für das Gerät und das Modul. Ihr Gerät und Ihr Modul verwenden diese Werte, um sich beim Senden von D2C-Nachrichten an IoT Hub zu identifizieren. Bei den IDs gilt Groß-/Kleinschreibung.

1. Erstellen Sie ein Verzeichnis zum Speichern Ihres Codes.

2. Führen Sie in diesem Verzeichnis zuerst **npm init -y** aus, um eine leere Datei „package.json“ mit Standardwerten zu erstellen. Dies ist die Projektdatei für Ihren Code.

3. Führen Sie **npm install -S azure-iothub\@modules-preview** aus, um das Dienst-SDK im Unterverzeichnis **node_modules** zu installieren.

    > [!NOTE]
    > Beim Namen des Unterverzeichnisses „node_modules“ steht das Wort „module“ für Node-Bibliotheken. Hier hat der Begriff nichts mit IoT Hub-Modulen zu tun.

4. Erstellen Sie die folgende JS-Datei in Ihrem Verzeichnis. Geben Sie ihr den Namen **add.js**. Kopieren Sie Ihre Hub-Verbindungszeichenfolge und den Hub-Namen, und fügen Sie sie ein.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Diese App erstellt eine Geräteidentität mit der ID **myFirstDevice** und ein Identitätsmodul mit der ID **myFirstModule** unter dem Gerät **myFirstDevice**. (Falls diese Modul-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Modulinformationen abgerufen.) Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel in der simulierten Modul-App, um eine Verbindung mit Ihrem IoT Hub herzustellen.

Führen Sie sie mit „node add.js“ aus. Dadurch erhalten Sie eine Verbindungszeichenfolge für Ihre Geräteidentität und eine weitere für Ihre Modulidentität.

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräte- und Modulidentitäten, um einen sicheren Zugriff auf IoT Hub zu ermöglichen. In der Identitätsregistrierung werden Geräte-IDs und -schlüssel für die Verwendung als Sicherheitsanmeldeinformationen gespeichert. Darüber hinaus wird in der Identitätsregistrierung ein Flag für den Aktivierungszustand des jeweiligen Geräts gespeichert, mit dem Sie den Zugriff für das betreffende Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Es gibt keinen Flag „Aktiviert/deaktiviert“ für Modulidentitäten. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aktualisieren des Modulzwillings mithilfe des Node.js-Geräte-SDK

In diesem Abschnitt erstellen Sie eine Node.js-App auf Ihrem simulierten Gerät, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert.

1. **Abrufen der Modulverbindungszeichenfolge:** Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Navigieren Sie zu Ihrer IoT Hub-Instanz, und klicken Sie auf „IoT-Geräte“. Suchen Sie nach „myFirstDevice“, und öffnen Sie den Eintrag. Sie sehen, dass „myFirstModule“ erfolgreich erstellt wurde. Kopieren Sie die Modulverbindungszeichenfolge. Sie wird im nächsten Schritt benötigt.

   ![Moduldetails im Azure-Portal](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Erstellen Sie ähnlich wie im vorherigen Schritt ein Verzeichnis für Ihren Gerätecode, und verwenden Sie NPM, um es zu initialisieren und das Geräte-SDK zu installieren (**npm install -S azure-iot-device-amqp\@modules-preview**).

   > [!NOTE]
   > Der NPM-Installationsbefehl kann längere Zeit in Anspruch nehmen. Sie müssen etwas Geduld haben, da sehr viel Code aus dem Paketrepository übertragen wird.

   > [!NOTE]
   > Wenn eine Fehlermeldung mit dem Inhalt, dass bei NPM ein Fehler bei der Analyse des JSON-Codes aufgetreten ist, angezeigt wird, können Sie diese ignorieren. Wenn eine Fehlermeldung mit dem Inhalt, dass bei NPM ein Fehler bei der Analyse des JSON-Codes aufgetreten ist, angezeigt wird, können Sie diese ignorieren.

3. Erstellen Sie eine Datei namens „twin.js“. Kopieren Sie die Modulidentitätszeichenfolge, und fügen Sie sie ein.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Führen Sie die dann mit dem Befehl **node twin.js** aus.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Anschließend wird Folgendes angezeigt:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md)

* [Erste Schritte mit IoT Edge](../iot-edge/quickstart-linux.md)