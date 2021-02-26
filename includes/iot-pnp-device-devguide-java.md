---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: ea136069da73d88174d9d40fe9c60e84982909df
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616340"
---
## <a name="model-id-announcement"></a>Modell-ID-Ankündigung

Zum Ankündigen der Modell-ID muss das Gerät diese in den Verbindungsinformationen enthalten:

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

Die `ClientOptions`-Überladung ist in allen `DeviceClient`-Methoden verfügbar, die zum Initialisieren einer Verbindung verwendet werden.

> [!TIP]
> Verwenden Sie bei Modulen und IoT Edge `ModuleClient` statt `DeviceClient`.

> [!TIP]
> Dies ist der einzige Zeitpunkt, zu dem ein Gerät die Modell-ID festlegen kann. Sie kann nach dem Verbinden des Geräts nicht mehr geändert werden.

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

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

### <a name="read-only-properties"></a>Schreibgeschützte Eigenschaften

Zum Melden einer Eigenschaft aus der Standardkomponente ist kein besonderes Konstrukt erforderlich:

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
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

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Schreibbare Eigenschaften

Diese Eigenschaften können vom Gerät festgelegt oder von der Lösung aktualisiert werden. Wenn die Lösung eine Eigenschaft aktualisiert, empfängt der Client eine Benachrichtigung als Rückruf im `DeviceClient` oder `ModuleClient`. Um den IoT Plug & Play-Konventionen zu entsprechen, muss das Gerät den Dienst informieren, dass die Eigenschaft erfolgreich empfangen wurde.

#### <a name="report-a-writable-property"></a>Melden einer schreibbaren Eigenschaft

Wenn ein Gerät eine schreibbare Eigenschaft meldet, müssen die `ack`-Werte enthalten sein, die in den Konventionen definiert sind.

So melden Sie eine schreibbare Eigenschaft aus der Standardkomponente:

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

Der Gerätezwilling wird mit der nächsten gemeldeten Eigenschaft aktualisiert:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Zum Melden einer schreibbaren Eigenschaft aus einer geschachtelten Komponente muss der Zwilling einen Marker enthalten:

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
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
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonnieren von Aktualisierungen der gewünschten Eigenschaften

Dienste können gewünschte Eigenschaften aktualisieren, die eine Benachrichtigung auf den verbundenen Geräten auslösen. Diese Benachrichtigung enthält die aktualisierten gewünschten Eigenschaften, einschließlich der Versionsnummer zum Identifizieren der Aktualisierung. Geräte müssen mit der gleichen `ack`-Nachricht wie gemeldete Eigenschaften antworten.

Eine Standardkomponente sieht die einzelne Eigenschaft und erstellt die gemeldete `ack` mit der empfangenen Version:

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
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
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Eine geschachtelte Komponente empfängt die gewünschten Eigenschaften, eingeschlossen im Komponentennamen, und sollte die mit `ack` gemeldete Eigenschaft zurückmelden:

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Der Gerätezwilling für eine geschachtelte Komponente zeigt die Abschnitte für gewünschte und gemeldete Eigenschaften wie folgt an:

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

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

#### <a name="request-and-response-payloads"></a>Anforderungs- und Antwortnutzlasten

Befehle verwenden Typen zum Definieren ihrer Anforderungs- und Antwortnutzlasten. Ein Gerät muss den eingehenden Eingabeparameter deserialisieren und die Antwort serialisieren.

Im folgenden Beispiel wird gezeigt, wie ein Befehl mit komplexen Typen implementiert wird, die in den Nutzlasten definiert sind:

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

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> Die Anforderungs- und Antwortnamen sind in den über die Verbindung übertragenen serialisierten Nutzlasten nicht vorhanden.
