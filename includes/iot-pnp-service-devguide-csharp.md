---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a92e37326cf9db0eba186b2bdca44c452c02ea91
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521382"
---
Die folgende Ressourcen stehen ebenfalls zur Verfügung:

- [.NET SDK-Referenzdokumentation](/dotnet/api/microsoft.azure.devices)
- [Beispiele für Dienstclients](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)
- [Digital Twins-Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples)

## <a name="iot-hub-service-client-examples"></a>Beispiele für den IoT Hub-Dienstclient

In diesem Abschnitt werden C#-Beispiele gezeigt, die den IoT Hub-Dienstclient und die Klassen **RegistryManager** und **ServiceClient** verwenden. Die **RegistryManager** -Klasse dient dazu, mithilfe von Gerätezwillingen mit dem Gerätestatus zu interagieren. Sie können die **RegistryManager** -Klasse außerdem verwenden, um in Ihrem IoT Hub [Geräteregistrierungen abzufragen](../articles/iot-hub/iot-hub-devguide-query-language.md). Die **ServiceClient** -Klasse verwenden Sie, um Befehle auf dem Gerät aufzurufen. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden. In den Codeausschnitten enthält die `deviceTwinId`-Variable die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-device-twin-and-model-id"></a>Abrufen von Gerätezwilling und Modell-ID

So rufen Sie den Gerätezwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Im Beispiel ist zu sehen, dass Sie die `ETag` des Zwillings abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Im Beispiel ist zu sehen, dass Sie die `ETag` des Zwillings abrufen müssen, bevor Sie ihn aktualisieren. Die Eigenschaft ist in der Komponente **Thermostat1** definiert:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

Für eine Eigenschaft in einer Komponente sieht der Eigenschaftspatch aus wie im folgenden Beispiel:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **Thermostat1** definiert:

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="iot-hub-digital-twin-examples"></a>Beispiele für IoT Hub Digital Twins

Sie können die **DigitalTwinClient** -Klasse verwenden, um mithilfe digitaler Zwillinge mit dem Gerätestatus zu interagieren. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden.

In diesem Abschnitt werden C#-Beispiele zum Verwenden der Digital Twins-API gezeigt. In den folgenden Codeausschnitten werden die folgenden Klassen verwendet, um den digitalen Zwilling der Geräte „Thermostat“ und „Temperatursteuerung“ darzustellen:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

Die `digitalTwinId`-Variable enthält die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-digital-twin-and-model-id"></a>Abrufen von digitalem Zwilling und Modell-ID

So rufen Sie den digitalen Zwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Aktualisieren des digitalen Zwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature",   desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Die Eigenschaft ist in der Komponente **Thermostat1** definiert:

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **Thermostat1** definiert:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="read-device-telemetry"></a>Lesen von Gerätetelemetrie

IoT Plug & Play-Geräte senden die im DTDL-Modell definierten Telemetriedaten an IoT Hub. Standardmäßig leitet der IoT Hub die Telemetriedaten an einen Event Hubs-Endpunkt weiter, wo Sie sie nutzen können. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Im folgenden Codeausschnitt können Sie sehen, wie die Telemetriedaten vom Event Hubs-Standardendpunkt gelesen werden. Der Code in diesem Codeausschnitt stammt aus dem IoT Hub-Schnellstart [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten mit einer Back-End-Anwendung](../articles/iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **Thermostat** ohne Komponenten gesendet wurde, das nur über die Standardkomponente verfügt. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **TemperatureController** gesendet wird, das aus mehreren Komponenten besteht. Die Systemeigenschaft `dt-subject` zeigt den Namen der Komponente, von der die Telemetriedaten gesendet wurden. In diesem Beispiel sind die zwei Komponenten `thermostat1` und `thermostat2`, wie im DTDL-Modell definiert. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei Gerätezwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für Gerätezwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden C#-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Thermostatgerät ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

Der im vorstehenden C#-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei digitalen Zwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für digitale Zwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden C#-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Thermostatgerät ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

Der im vorstehenden C#-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```
