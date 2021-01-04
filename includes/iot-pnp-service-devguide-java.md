---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 70df45877a310d74e7c5c82292d18b1c0eb32da8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521381"
---
Die folgenden Ressourcen stehen ebenfalls zur Verfügung:

- [Java SDK-Referenzdokumentation](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable)
- [Beispiele für Dienstclients](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [Digital Twins-Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>Beispiele für den IoT Hub-Dienstclient

In diesem Abschnitt werden die Java-Beispiele für die Verwendung des IoT Hub-Dienstclients und der Klassen **DeviceTwin** und **DeviceMethod** aus dem Namespace **com.microsoft.azure.sdk.iot.service.devicetwin** veranschaulicht. Die **DeviceTwin** -Klasse dient dazu, mithilfe von Gerätezwillingen mit dem Gerätestatus zu interagieren. Sie können die **DeviceTwin** -Klasse außerdem verwenden, um in Ihrer IoT Hub-Instanz [Geräteregistrierungen abzufragen](../articles/iot-hub/iot-hub-devguide-query-language.md). Mit der **DeviceMethod** -Klasse rufen Sie Befehle auf dem Gerät auf. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden. In den Codeausschnitten enthält die `deviceId`-Variable die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-device-twin-and-model-id"></a>Abrufen von Gerätezwilling und Modell-ID

So rufen Sie den Gerätezwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Sie müssen den Zwilling vor der Aktualisierung abrufen. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Sie müssen den Zwilling vor der Aktualisierung abrufen. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

Für eine Eigenschaft in einer Komponente sieht der Eigenschaftspatch aus wie im folgenden Beispiel:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>Beispiele für digitale IoT Hub-Zwillinge

Sie verwenden die Klasse **DigitalTwinAsyncClient** im Namespace **com.microsoft.azure.sdk.iot.service.digitaltwin** , um mithilfe digitaler Zwillinge mit dem Gerätestatus zu interagieren. In den folgenden Beispielen werden darüber hinaus die Klassen **UpdateOperationUtility** und **BasicDigitalTwin** aus demselben Namespace verwendet. Das [DTDL](../articles/iot-pnp/concepts-digital-twin.md)-Modell für das Gerät definiert die Eigenschaften und Befehle, die vom Gerät implementiert werden.

Die `digitalTwinid`-Variable enthält die Geräte-ID des bei Ihrem IoT-Hub registrierten IoT Plug & Play-Geräts.

### <a name="get-the-digital-twin-and-model-id"></a>Abrufen von digitalem Zwilling und Modell-ID

So rufen Sie den digitalen Zwilling und die Modell-ID des IoT Plug & Play-Geräts ab, das die Verbindung mit Ihrem IoT-Hub hergestellt hat:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>Aktualisieren des digitalen Zwillings

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft auf einem Gerät aktualisiert wird. Die Eigenschaft ist in der Standardkomponente des Geräts definiert:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

Der folgende Codeausschnitt zeigt, wie die `targetTemperature`-Eigenschaft für eine Komponente aktualisiert wird. Die Eigenschaft ist in der Komponente **thermostat1** definiert:

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>Befehl aufrufen

Der folgende Codeausschnitt zeigt, wie Sie den `getMaxMinReport`-Befehl aufrufen, der in einer Standardkomponente definiert ist:

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

Der folgende Codeausschnitt zeigt, wie die `getMaxMinReport`-Eigenschaft für eine Komponente aufgerufen wird. Der Befehl ist in der Komponente **thermostat1** definiert:

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>Lesen von Gerätetelemetrie

IoT Plug & Play-Geräte senden die im DTDL-Modell definierten Telemetriedaten an IoT Hub. Standardmäßig leitet der IoT Hub die Telemetriedaten an einen Event Hubs-Endpunkt weiter, wo Sie sie nutzen können. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Im folgenden Codeausschnitt können Sie sehen, wie die Telemetriedaten vom Event Hubs-Standardendpunkt gelesen werden. Der Code in diesem Codeausschnitt stammt aus dem IoT Hub-Schnellstart [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten mit einer Back-End-Anwendung](../articles/iot-hub/quickstart-send-telemetry-java.md):

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **Thermostat** ohne Komponenten gesendet wurde, das nur über die Standardkomponente verfügt. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

Die folgende Ausgabe aus dem vorherigen Code zeigt die Temperaturtelemetrie, die vom IoT Plug & Play-Gerät **TemperatureController** gesendet wird, das aus mehreren Komponenten besteht. Die Systemeigenschaft `dt-subject` zeigt den Namen der Komponente, von der die Telemetriedaten gesendet wurden. In diesem Beispiel sind die zwei Komponenten `thermostat1` und `thermostat2`, wie im DTDL-Modell definiert. Die Systemeigenschaft `dt-dataschema` zeigt die Modell-ID:

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei Gerätezwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für Gerätezwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden Java-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Thermostat ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

Der im vorstehenden Java-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für Gerätezwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>Lesen von Änderungsbenachrichtigungen bei digitalen Zwillingen

Sie können IoT Hub für das Generieren von Änderungsbenachrichtigungen für digitale Zwillinge konfigurieren, die an einen unterstützten Endpunkt weitergeleitet werden. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte > Nicht telemetriebezogene Ereignisse](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Der im vorstehenden Java-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Thermostat ohne Komponenten generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

Der im vorstehenden Java-Codeausschnitt gezeigte Code generiert die folgende Ausgabe, wenn IoT Hub Änderungsbenachrichtigungen für digitale Zwillinge für ein Gerät mit Komponenten generiert. Dieses Beispiel zeigt die Ausgabe, wenn ein Temperatursensorgerät mit einer Thermostatkomponente Benachrichtigungen generiert. Die Anwendungseigenschaften `iothub-message-schema` und `opType` enthalten Informationen über den Typ der Änderungsbenachrichtigung:

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
