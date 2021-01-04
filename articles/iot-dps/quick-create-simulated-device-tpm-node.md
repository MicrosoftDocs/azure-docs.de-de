---
title: 'Schnellstart: Bereitstellen eines simulierten TPM-Geräts für Azure IoT Hub mithilfe von Node.js'
description: 'Schnellstart: Erstellen und Bereitstellen eines simulierten TPM-Geräts mithilfe des Node.js-Geräte-SDK für Azure IoT Hub Device Provisioning Service (DPS) In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 6e7e986f658570553763001afdd58d7bb1880f94
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968178"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Schnellstart: Erstellen und Bereitstellen eines simulierten TPM-Geräts mithilfe des Node.js-Geräte-SDKs für den IoT Hub Device Provisioning-Dienst

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In dieser Schnellstartanleitung erstellen Sie ein simuliertes IoT-Gerät auf einem Windows-Computer. Das simulierte Gerät enthält einen TPM-Simulator als Hardwaresicherheitsmodul (HSM). Sie verwenden Node.js-Beispielcode für das Gerät, um dieses simulierte Gerät mit Ihrem IoT-Hub zu verbinden, indem eine einzelne Registrierung beim Device Provisioning Service (DPS) verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

- Mit Konzepten für die [Bereitstellung](about-iot-dps.md#provisioning-process) vertraut sein.
- Ausführung der Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md).
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung 

1. Stellen Sie sicher, dass auf Ihrem Computer [Node.js v4.0 oder höher](https://nodejs.org) installiert ist.

1. Vergewissern Sie sich, dass `git` auf Ihrem Computer installiert ist und den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Unter [Git-Clienttools von Software Freedom Conservancy](https://git-scm.com/download/) finden Sie die neueste Version der zu installierenden `git`-Tools. Hierzu zählt auch die Befehlszeilen-App **Git Bash**, über die Sie mit Ihrem lokalen Git-Repository interagieren können. 


## <a name="simulate-a-tpm-device"></a>Simulieren eines TPM-Geräts

1. Öffnen Sie eine Eingabeaufforderung oder Git Bash. Klonen Sie das GitHub-Repository `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Navigieren Sie zum GitHub-Stammordner, und führen Sie den [TPM](/windows/device-security/tpm/trusted-platform-module-overview)-Simulator aus, damit er als [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) für das simulierte Gerät verwendet wird. Dieser lauscht über einen Socket an den Ports 2321 und 2322. Lassen Sie das Befehlsfenster geöffnet. Es wird benötigt, um den Simulator bis zum Ende dieses Schnellstarts auszuführen: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Erstellen Sie einen neuen leeren Ordner namens **registerdevice**. Erstellen Sie im Ordner **registerdevice** eine Datei namens „package.json“, indem Sie an der Eingabeaufforderung den im Anschluss angegebenen Befehl ausführen. Beantworten Sie alle von `npm` gestellten Fragen, oder akzeptieren Sie die Standardeinstellungen, wenn diese für Sie geeignet sind:
   
    ```cmd/sh
    npm init
    ```

1. Installieren Sie die folgenden Vorstufenpakete:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Die Installation der obigen Pakete kann zu einigen bekannten Problemen führen. Um diese Probleme zu beheben, führen Sie an einer Eingabeaufforderung `npm install --global --production windows-build-tools` im Modus **Als Administrator ausführen** aus. Führen Sie `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` aus, nachdem Sie den Pfad durch Ihre installierte Version ersetzt haben, und führen Sie anschließend die obigen Installationsbefehle erneut aus.
    >

1. Installieren Sie die folgenden Pakete mit den während der Registrierung verwendeten Komponenten:

   - Einen für TPM geeigneten Sicherheitsclient: `azure-iot-security-tpm`
   - Eine Transportkomponente für das Gerät, um eine Verbindung mit dem Gerätebereitstellungsdienst herzustellen: `azure-iot-provisioning-device-http` oder `azure-iot-provisioning-device-amqp`
   - Einen Client zur Verwendung der Transportkomponente und des Sicherheitsclients: `azure-iot-provisioning-device`

     Nach der Registrierung des Geräts können Sie die üblichen IoT Hub-Geräteclientpakete verwenden, um mit dem Gerät eine Verbindung unter Verwendung der Anmeldeinformationen herzustellen, die im Rahmen der Registrierung angegeben wurden. Sie benötigen Folgendes:

   - Den Geräteclient: `azure-iot-device`
   - Eine Transportkomponente: `azure-iot-device-amqp`, `azure-iot-device-mqtt` oder `azure-iot-device-http`
   - Den bereit installierten Sicherheitsclient: `azure-iot-security-tpm`

     > [!NOTE]
     > In den folgenden Beispielen werden die Transportkomponenten `azure-iot-provisioning-device-http` und `azure-iot-device-mqtt` verwendet.
     > 

     Die Pakete können alle gleichzeitig installiert werden. Führen Sie dazu an der Eingabeaufforderung im Ordner **registereddevice** den folgenden Befehl aus:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Erstellen Sie mit einem Text-Editor im Ordner **registerdevice** eine neue Datei namens **ExtractDevice.js**.

1. Fügen Sie am Anfang der Datei **ExtractDevice.js** die folgenden `require`-Anweisungen hinzu:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Fügen Sie die folgende Funktion hinzu, um die Methode zu implementieren:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Speichern und schließen Sie die Datei **ExtractDevice.js**. Führen Sie das Beispiel aus:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. Im Ausgabefenster werden der **_Endorsement Key_** und die **_Registrierungs-ID_** für die Geräteregistrierung angezeigt. Notieren Sie sich diese Werte. 


## <a name="create-a-device-entry"></a>Erstellen eines Geräteeintrags

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

In diesem Artikel werden individuelle Registrierungen veranschaulicht.

1. Melden Sie sich beim Azure-Portal an, wählen Sie im Menü links die Schaltfläche **Alle Ressourcen** aus, und öffnen Sie Ihren Gerätebereitstellungsdienst.

1. Wählen Sie im Menü von Device Provisioning Service die Option **Registrierungen verwalten** aus. Wählen Sie die Registerkarte **Individuelle Registrierungen** und dann oben die Schaltfläche **Individuelle Registrierung hinzufügen** aus. 

1. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:
   - Wählen Sie **TPM** als *Mechanismus* für den Nachweis der Identität.
   - Geben Sie die zuvor notierte *Registrierungs-ID* und den *Endorsement Key* für Ihr TPM-Gerät entsprechend den zuvor notierten Werten ein.
   - Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
   - Optional können Sie die folgenden Informationen angeben:
       - Geben Sie eine eindeutige *Geräte-ID* ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben. Wenn Sie keine Angabe machen, wird das Gerät stattdessen über die Registrierungs-ID identifiziert.
       - Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
   - Klicken Sie abschließend auf die Schaltfläche **Speichern**. 

     ![Eingeben von Geräteregistrierungsinformationen auf dem Portalblatt](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Nach erfolgreicher Registrierung wird die *Registrierungs-ID* Ihres Geräts in der Liste auf der Registerkarte *Individuelle Registrierungen* angezeigt. 


## <a name="register-the-device"></a>Registrieren des Geräts

1. Wählen Sie im Azure-Portal das Blatt **Übersicht** für Ihren Gerätebereitstellungsdienst aus, und notieren Sie sich die Werte für **_Globaler Geräteendpunkt_** und **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Erstellen Sie mit einem Text-Editor im Ordner **registerdevice** eine neue Datei namens **RegisterDevice.js**.

1. Fügen Sie am Anfang der Datei **RegisterDevice.js** die folgenden `require`-Anweisungen hinzu:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > Das **Azure IoT SDK für Node.js** unterstützt weitere Protokolle wie etwa _AMQP_, _AMQP WS_ und _MQTT WS_.  Weitere Beispiele finden Sie unter [Samples for the Azure IoT Device Provisioning Device SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples) (Beispiele für das Azure IoT Device Provisioning Device SDK für Node.js).
    > 

1. Fügen Sie die Variablen **globalDeviceEndpoint** und **idScope** hinzu, und verwenden Sie sie zum Erstellen einer Instanz von **ProvisioningDeviceClient**. Ersetzen Sie **{globalDeviceEndpoint}** und **{idScope}** durch die Werte für **_Globaler Geräteendpunkt_** und **_ID-Bereich_** aus **Schritt 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Fügen Sie die folgende Funktion hinzu, um die Methode auf dem Gerät zu implementieren:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Speichern und schließen Sie die Datei **RegisterDevice.js**. Führen Sie das Beispiel aus:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Beachten Sie die Nachrichten, die den Start und die Verbindungsherstellung des Geräts mit dem Device Provisioning-Dienst simulieren, um Ihre IoT Hub-Informationen abzurufen. Nachdem das simulierte Gerät erfolgreich für den mit Ihrem Bereitstellungsdienst verknüpften IoT-Hub bereitgestellt wurde, wird die Geräte-ID auf dem Blatt **IoT-Geräte** des Hubs angezeigt. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/quick-create-simulated-device/hub-registration.png) 

    Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiterhin verwenden und erkunden möchten, überspringen Sie die Bereinigung der in diesem Schnellstart erstellten Ressourcen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Schnellstarts erstellt wurden.

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
1. Schließen Sie auf Ihrem Computer das TPM-Simulatorfenster.
1. Wählen Sie im Azure-Portal im Menü links **Alle Ressourcen** und dann Ihren Gerätebereitstellungsdienst aus. Öffnen Sie das Blatt **Registrierungen verwalten** für Ihren Dienst, und wählen Sie die Registerkarte **Individuelle Registrierungen** aus. Aktivieren Sie das Kontrollkästchen *REGISTRIERUNGS-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie oben im Bereich die Schaltfläche **Löschen** aus. 
1. Wählen Sie im linken Menü im Azure-Portal **Alle Ressourcen** und dann Ihren IoT-Hub aus. Öffnen Sie das Blatt **IoT-Geräte** für Ihren Hub, aktivieren Sie das Kontrollkästchen *GERÄTE-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie dann oben im Bereich die Schaltfläche **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie auf Ihrem Computer ein simuliertes TPM-Gerät erstellt und mithilfe von IoT Hub Device Provisioning Service für Ihren IoT-Hub bereitgestellt. Informationen zum programmgesteuerten Registrieren Ihres TPM-Geräts finden Sie im Schnellstart für die programmgesteuerte Registrierung von TPM-Geräten. 

> [!div class="nextstepaction"]
> [Azure-Schnellstart: Registrieren eines TPM-Geräts bei Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)