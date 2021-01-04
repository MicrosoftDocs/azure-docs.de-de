---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: bef7807c0df580a6763a69619cdaa3d9d29f72e6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521380"
---
Die folgende Ressourcen stehen ebenfalls zur Verfügung:

- [Referenzdokumentation zum Python-SDK](/python/api/azure-iot-hub/azure.iot.hub?preserve-view=true&view=azure-python)
- [Beispiele für Dienstclients](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digital Twins-Beispiele](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Beispiele für den IoT Hub-Dienstclient

In diesem Abschnitt werden Python-Beispiele gezeigt, die den IoT Hub-Dienstclient und die Klassen **IoTHubRegistryManager** und **CloudToDeviceMethod** verwenden. Die Klasse **IoTHubRegistryManager** dient dazu, mithilfe von Gerätezwillingen mit dem Gerätestatus zu interagieren. Sie können die **IoTHubRegistryManager** -Klasse außerdem verwenden, um in Ihrem IoT-Hub [Geräteregistrierungen abzufragen](../articles/iot-hub/iot-hub-devguide-query-language.md). Mit der **CloudToDeviceMethod** -Klasse rufen Sie Befehle auf dem Gerät auf. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden. In den Codeausschnitten enthält die `device_id`-Variable die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-device-twin-and-model-id"></a>Abrufen von Gerätezwilling und Modell-ID

So rufen Sie den Gerätezwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Im Beispiel ist zu sehen, dass Sie die `etag` des Zwillings abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Im Beispiel ist zu sehen, dass Sie die `ETag` des Zwillings abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Beispiele für digitale IoT Hub-Zwillinge

Sie können die **DigitalTwinClient** -Klasse verwenden, um mithilfe digitaler Zwillinge mit dem Gerätestatus zu interagieren. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden.

Die `device_id`-Variable enthält die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-digital-twin-and-model-id"></a>Abrufen von digitalem Zwilling und Modell-ID

So rufen Sie den digitalen Zwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Aktualisieren des digitalen Zwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Lesen von Gerätetelemetrie

IoT Plug & Play-Geräte senden die im DTDL-Modell definierten Telemetriedaten an IoT Hub. Standardmäßig leitet der IoT Hub die Telemetriedaten an einen Event Hubs-Endpunkt weiter, wo Sie sie nutzen können. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Im folgenden Codeausschnitt können Sie sehen, wie die Telemetriedaten vom Event Hubs-Standardendpunkt gelesen werden. Der Code in diesem Codeausschnitt stammt aus dem IoT Hub-Schnellstart [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten mit einer Back-End-Anwendung](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **Thermostat** ohne Komponenten gesendet wurde, das nur über die Standardkomponente verfügt. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **TemperatureController** gesendet wird, das aus mehreren Komponenten besteht. Die Systemeigenschaft `dt-subject` zeigt den Namen der Komponente, von der die Telemetriedaten gesendet wurden. In diesem Beispiel sind die zwei Komponenten `thermostat1` und `thermostat2`, wie im DTDL-Modell definiert. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei Gerätezwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für Gerätezwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden Python-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Thermostatgerät ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Der im vorstehenden Python-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei digitalen Zwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für digitale Zwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden Python-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Thermostat ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Der im vorstehenden Python-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
