---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 7bf32de017a5f8ad19eb044ae7dbcdc2eaa96ca5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521383"
---
Die folgende Ressourcen stehen ebenfalls zur Verfügung:

- [Node.js SDK-Referenzdokumentation](/javascript/api/azure-iothub?preserve-view=true&view=azure-node-latest)
- [Beispiele für Dienstclients](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Digital Twins-Beispiele](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Beispiele für den IoT Hub-Dienstclient

In diesem Abschnitt werden JavaScript-Beispiele gezeigt, die den IoT Hub-Dienstclient und die Klassen **Registry** und **Client** verwenden. Die **Registry** -Klasse dient dazu, mithilfe von Gerätezwillingen mit dem Gerätestatus zu interagieren. Sie können die **Registry** -Klasse außerdem verwenden, um in Ihrem IoT-Hub [Geräteregistrierungen abzufragen](../articles/iot-hub/iot-hub-devguide-query-language.md). Mit der **Client** -Klasse rufen Sie Befehle auf dem Gerät auf. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden. In den Codeausschnitten enthält die `deviceId`-Variable die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-device-twin-and-model-id"></a>Abrufen von Gerätezwilling und Modell-ID

So rufen Sie den Gerätezwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Im Beispiel ist zu sehen, dass Sie den Zwilling abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Im Beispiel ist zu sehen, dass Sie den Zwilling abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Für eine Eigenschaft in einer Komponente sieht der Eigenschaftspatch aus wie im folgenden Beispiel:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Beispiele für IoT Hub Digital Twins

Sie können die **DigitalTwinClient** -Klasse verwenden, um mithilfe digitaler Zwillinge mit dem Gerätestatus zu interagieren. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden.

In diesem Abschnitt werden JavaScript-Beispiele zur Verwendung der Digital Twins-API gezeigt.

Die `digitalTwinId`-Variable enthält die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-digital-twin-and-model-id"></a>Abrufen von digitalem Zwilling und Modell-ID

So rufen Sie den digitalen Zwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Aktualisieren des digitalen Zwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Lesen von Gerätetelemetrie

IoT Plug & Play-Geräte senden die im DTDL-Modell definierten Telemetriedaten an IoT Hub. Standardmäßig leitet der IoT Hub die Telemetriedaten an einen Event Hubs-Endpunkt weiter, wo Sie sie nutzen können. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Im folgenden Codeausschnitt können Sie sehen, wie die Telemetriedaten vom Event Hubs-Standardendpunkt gelesen werden. Der Code in diesem Codeausschnitt stammt aus dem IoT Hub-Schnellstart [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten mit einer Back-End-Anwendung](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **TemperatureController** gesendet wird, das aus mehreren Komponenten besteht. Die Systemeigenschaft `dt-subject` zeigt den Namen der Komponente, von der die Telemetriedaten gesendet wurden. In diesem Beispiel sind die zwei Komponenten `thermostat1` und `thermostat2`, wie im DTDL-Modell definiert. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei Gerätezwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für Gerätezwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden JavaScript-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Thermostatgerät ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Der im vorstehenden JavaScript-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei digitalen Zwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für digitale Zwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden JavaScript-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Thermostatgerät ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Der im vorstehenden JavaScript-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
