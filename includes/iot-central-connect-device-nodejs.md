---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 4b19ae1b584a2d300e4144e79ef76245c71035cf
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126112"
---
## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Eine Azure IoT Central-Anwendung, die mit der Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](../articles/iot-central/core/quick-deploy-iot-central.md). Die Anwendung muss am 14. Juli 2020 oder nach diesem Datum erstellt worden sein.
* Einen Entwicklungscomputer, auf dem mindestens Version 6 von [Node.js](https://nodejs.org/) installiert ist. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Bei den Anweisungen in diesem Tutorial wird davon ausgegangen, dass Sie den Befehl **node** an der Windows-Eingabeaufforderung ausführen. Node.js kann jedoch unter einer Reihe anderer Betriebssysteme verwendet werden.
* Eine lokale Kopie des GitHub-Repositorys mit dem [Microsoft Azure IoT SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node), das den Beispielcode enthält. Laden Sie über den folgenden Link eine Kopie des Repositorys herunter: [ZIP herunterladen](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Entzippen Sie anschließend die Datei an einem geeigneten Speicherort auf Ihrem lokalen Computer.

## <a name="review-the-code"></a>Überprüfen des Codes

Öffnen Sie in der Kopie des zuvor heruntergeladenen Microsoft Azure IoT SDK für Node.js die Datei *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* in einem Text-Editor.

Wenn Sie das Beispiel ausführen, um eine Verbindung mit IoT Central herzustellen, wird Device Provisioning Service (DPS) zum Registrieren des Geräts und zum Generieren einer Verbindungszeichenfolge verwendet. Das Beispiel ruft die erforderlichen DPS-Verbindungsinformationen aus der Befehlszeilenumgebung ab.

Die `main`-Methode:

* Erstellen eines Objekts vom Typ `client` und Festlegen der Modell-ID `dtmi:com:example:Thermostat;1` vor dem Öffnen der Verbindung
* Erstellen eines Befehlshandlers
* Starten einer Schleife, um alle zehn Sekunden Temperaturtelemetriedaten zu senden
* Senden der Eigenschaft `maxTempSinceLastReboot` an IoT Central. IoT Central ignoriert die Eigenschaft `serialNumber`, da sie nicht Teil des Gerätemodells ist.
* Erstellen eines schreibbaren Eigenschaftenhandlers

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

Die Funktion `provisionDevice` zeigt, wie das Gerät mithilfe von DPS die Registrierung bei IoT Central durchführt und eine Verbindung mit IoT Central herstellt. Die Nutzlast beinhaltet die Modell-ID:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

Die Funktion `sendTelemetry` zeigt, wie das Gerät die Temperaturtelemetriedaten an IoT Central sendet: Die `getCurrentTemperatureObject`-Methode gibt ein Objekt zurück, das wie `{ temperature: 45.6 }` aussieht:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

Die `main`-Methode nutzt die folgenden zwei Methoden, um die Eigenschaft `maxTempSinceLastReboot` an IoT Central zu senden: Die `main`-Methode ruft `createReportPropPatch` mit einem Objekt auf, das wie `{maxTempSinceLastReboot: 80.9}` aussieht:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

Die `main`-Methode nutzt die folgenden zwei Methoden, um Aktualisierungen der schreibbaren Eigenschaft für die _Zieltemperatur_ aus IoT Central zu behandeln. Beachten Sie, wie `propertyUpdateHandle` die Antwort mit der Version und dem Statuscode erstellt:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

Die `main`-Methode verwendet die folgenden zwei Methoden, um Aufrufe des Befehls `getMaxMinReport` zu verarbeiten: Die `getMaxMinReportObject`-Methode generiert den Bericht als JSON-Objekt:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Ausführen des Codes

Öffnen Sie zum Ausführen der Beispielanwendung eine Befehlszeilenumgebung, und navigieren Sie zum Ordner *azure-iot-sdk-node/device/samples/pnp*, der die Beispieldatei *simple_thermostat.js* enthält.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Installieren Sie die erforderlichen Pakete:

```cmd/sh
npm install
```

Führen Sie das Beispiel aus:

```cmd/sh
node simple_thermostat.js
```

Die folgende Ausgabe zeigt, wie das Gerät registriert und eine Verbindung mit IoT Central hergestellt wird. In dem Beispiel wird dann die Eigenschaft `maxTempSinceLastReboot` gesendet, bevor mit dem Senden von Telemetriedaten begonnen wird:

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Sie können sehen, wie das Gerät auf Befehle und Eigenschaftsaktualisierungen reagiert:

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
