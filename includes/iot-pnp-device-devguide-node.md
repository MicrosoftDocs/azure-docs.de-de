---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: c5846c42fe55dab7328e89a6d7bc5987b0937416
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511357"
---
## <a name="model-id-announcement"></a>Modell-ID-Ankündigung

Zum Ankündigen der Modell-ID muss das Gerät diese in den Verbindungsinformationen enthalten:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> Verwenden Sie bei Modulen und IoT Edge `ModuleClient` statt `Client`.

## <a name="dps-payload"></a>DPS-Nutzlast

Geräte, die [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) verwenden, können das `modelId`-Element enthalten, das während des Bereitstellungsprozesses mit der folgenden JSON-Nutzlast verwendet werden soll:

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementieren von Telemetrie, Eigenschaften und Befehlen

Wie unter [Verstehen der Komponenten in IoT Plug & Play-Modellen](../articles/iot-pnp/concepts-components.md) beschrieben, müssen Geräteentwickler entscheiden, ob sie Komponenten zum Beschreiben ihrer Geräte verwenden möchten. Bei Verwendung von Komponenten müssen Geräte den in diesem Abschnitt beschriebenen Regeln entsprechen.

### <a name="telemetry"></a>Telemetrie

Für eine Standardkomponente ist keine besondere Eigenschaft erforderlich.

Bei Verwendung von geschachtelten Komponenten müssen Geräte eine Nachrichteneigenschaft mit dem Komponentennamen festlegen:

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Schreibgeschützte Eigenschaften

Zum Melden einer Eigenschaft aus der Standardkomponente ist kein besonderes Konstrukt erforderlich:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Bei Verwendung von geschachtelten Komponenten müssen Eigenschaften innerhalb des Komponentennamens erstellt werden:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Schreibbare Eigenschaften

Diese Eigenschaften können vom Gerät festgelegt oder von der Lösung aktualisiert werden. Wenn die Lösung eine Eigenschaft aktualisiert, empfängt der Client eine Benachrichtigung als Rückruf im `Client` oder `ModuleClient`. Um den IoT Plug & Play-Konventionen zu entsprechen, muss das Gerät den Dienst informieren, dass die Eigenschaft erfolgreich empfangen wurde.

#### <a name="report-a-writable-property"></a>Melden einer schreibbaren Eigenschaft

Wenn ein Gerät eine schreibbare Eigenschaft meldet, müssen die `ack`-Werte enthalten sein, die in den Konventionen definiert sind.

So melden Sie eine schreibbare Eigenschaft aus der Standardkomponente:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Zum Melden einer schreibbaren Eigenschaft aus einer geschachtelten Komponente muss der Zwilling einen Marker enthalten:

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonnieren von Aktualisierungen der gewünschten Eigenschaften

Dienste können gewünschte Eigenschaften aktualisieren, die eine Benachrichtigung auf den verbundenen Geräten auslösen. Diese Benachrichtigung enthält die aktualisierten gewünschten Eigenschaften, einschließlich der Versionsnummer zum Identifizieren der Aktualisierung. Geräte müssen mit der gleichen `ack`-Nachricht wie gemeldete Eigenschaften antworten.

Eine Standardkomponente sieht die einzelne Eigenschaft und erstellt die gemeldete `ack` mit der empfangenen Version:

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
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
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
```

Der Gerätezwilling zeigt die Eigenschaft in den Abschnitten für gewünschte und gemeldete Eigenschaften:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Eine geschachtelte Komponente empfängt die gewünschten Eigenschaften, eingeschlossen im Komponentennamen, und sollte die mit `ack` gemeldete Eigenschaft zurückmelden:

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

Der Gerätezwilling für Komponenten zeigt die Abschnitte für gewünschte und gemeldete Eigenschaften wie folgt an:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Befehle

Eine Standardkomponente empfängt den Befehlsnamen so, wie er vom Dienst aufgerufen wurde.

Eine geschachtelte Komponente empfängt den Befehlsnamen, dem der Komponentenname und das Trennzeichen `*` vorangestellt sind.

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>Anforderungs- und Antwortnutzlasten

Befehle verwenden Typen zum Definieren ihrer Anforderungs- und Antwortnutzlasten. Ein Gerät muss den eingehenden Eingabeparameter deserialisieren und die Antwort serialisieren. Im folgenden Beispiel wird gezeigt, wie ein Befehl mit komplexen Typen implementiert wird, die in den Nutzlasten definiert sind:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Die folgenden Codeausschnitte zeigen, wie ein Gerät diese Befehlsdefinition implementiert, einschließlich der Typen, die zum Aktivieren der Serialisierung und Deserialisierung verwendet werden:

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> Die Anforderungs- und Antwortnamen sind in den über die Verbindung übertragenen serialisierten Nutzlasten nicht vorhanden.
