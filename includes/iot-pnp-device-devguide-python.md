---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 7658b5a51c9e24e5530114ebca7455a0ed3ea097
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511359"
---
## <a name="model-id-announcement"></a>Modell-ID-Ankündigung

Zum Ankündigen der Modell-ID muss das Gerät diese in den Verbindungsinformationen enthalten:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> Verwenden Sie bei Modulen und IoT Edge `IoTHubModuleClient` statt `IoTHubDeviceClient`.

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

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Schreibgeschützte Eigenschaften

Zum Melden einer Eigenschaft aus der Standardkomponente ist kein besonderes Konstrukt erforderlich:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

Diese Eigenschaften können vom Gerät festgelegt oder von der Lösung aktualisiert werden. Wenn die Lösung eine Eigenschaft aktualisiert, empfängt der Client eine Benachrichtigung als Rückruf im `IoTHubDeviceClient` oder `IoTHubModuleClient`. Um den IoT Plug & Play-Konventionen zu entsprechen, muss das Gerät den Dienst informieren, dass die Eigenschaft erfolgreich empfangen wurde.

#### <a name="report-a-writable-property"></a>Melden einer schreibbaren Eigenschaft

Wenn ein Gerät eine schreibbare Eigenschaft meldet, müssen die `ack`-Werte enthalten sein, die in den Konventionen definiert sind.

So melden Sie eine schreibbare Eigenschaft aus der Standardkomponente:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
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

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
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

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> Die Anforderungs- und Antwortnamen sind in den über die Verbindung übertragenen serialisierten Nutzlasten nicht vorhanden.
