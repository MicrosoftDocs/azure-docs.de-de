---
title: Erstellen eines erweiterten simulierten Gerätemodells – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung erfahren Sie, wie Sie ein erweitertes Gerätemodell für die Verwendung mit dem Solution Accelerator für die Gerätesimulation erstellen.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852556"
---
# <a name="create-an-advanced-device-model"></a>Erstellen eines erweiterten Gerätemodells

In dieser Schrittanleitung werden die JSON- und JavaScript-Dateien beschrieben, die ein benutzerdefiniertes Gerätemodell definieren. Der Artikel enthält einige Beispieldateien für die Gerätemodelldefinition und veranschaulicht, wie sie in Ihre Instanz der Gerätesimulation hochgeladen werden. Sie können erweiterte Gerätemodelle erstellen, um realistischere Verhaltensweisen der Geräte für Ihre Tests zu simulieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Schritte dieser Schrittanleitung benötigen Sie eine in Ihrem Azure-Abonnement bereitgestellte Instanz der Gerätesimulation.

Wenn Sie die Gerätesimulation noch nicht bereitgestellt haben, finden Sie weitere Informationen auf GitHub unter [Device Simulation deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Bereitstellen der Gerätesimulation).

### <a name="open-device-simulation"></a>Öffnen der Gerätesimulation

Wenn Sie die Gerätesimulation noch nicht bereitgestellt haben, finden Sie weitere Informationen auf GitHub unter [Device Simulation deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Bereitstellen der Gerätesimulation).

## <a name="device-models"></a>Gerätemodelle

Jedes simuliertes Gerät gehört zu einem bestimmten Gerätemodell, das das Simulationsverhalten definiert. Dieses Verhalten umfasst, wie häufig Telemetriedaten gesendet werden, welche Art von Nachrichten gesendet werden und welche Methoden unterstützt werden.

Sie definieren ein Gerätemodell mit einer JSON-Gerätedefinitionsdatei und einem Satz von JavaScript-Dateien. Diese JavaScript-Dateien definieren das Simulationsverhalten, z.B. die zufälligen Telemetriedaten und die Logik der Methode.

Zu einem typischen Gerätemodell gehört Folgendes:

* Eine JSON-Datei für jedes Gerätemodell (z.B. „elevator.json“)
* Eine JavaScript-Skriptdatei für das Verhalten für jedes Gerätemodell (z.B. „elevator-state.js“)
* Eine JavaScript-Skriptdatei mit Methoden für jede Gerätemethode (z.B. „elevator-go-down.js“)

> [!NOTE]
> Nicht alle Gerätemodelle definieren Methoden. Aus diesem Grund kann ein Gerätemodell möglicherweise Methodenskripts aufweisen, muss es aber nicht. Allerdings müssen alle Gerätemodelle ein Verhaltensskript enthalten.

## <a name="device-definition-file"></a>Gerätedefinitionsdatei

Jede Gerätedefinitionsdatei enthält die Details eines simulierten Gerätemodells, einschließlich der folgenden Informationen:

* Name des Gerätemodells: Zeichenfolge
* Protokoll: AMQP | MQTT | HTTP.
* Der Anfangszustand des Geräts
* Wie oft der Gerätezustand aktualisiert werden soll
* Welche JavaScript-Datei zum Aktualisieren des Gerätezustands verwendet werden soll
* Eine Liste von zu sendenden Telemetrienachrichten, jeweils mit einer bestimmten Häufigkeit
* Das Schema der Telemetrienachrichten, das von der Back-End-Anwendung verwendet wird, um die empfangenen Telemetriedaten zu analysieren
* Eine Liste der unterstützten Methoden und der JavaScript-Datei, die zum Simulieren der einzelnen Methoden verwendet werden soll

### <a name="file-schema"></a>Dateischema

Die Schemaversion ist immer „1.0.0“ und gilt für das Format dieser Datei:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Gerätemodellbeschreibung

Die folgenden Eigenschaften beschreiben das Gerätemodell. Jeder Typ verfügt über einen eindeutigen Bezeichner, eine semantische Version, einen Namen und eine Beschreibung:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-Protokoll

IoT-Geräte können keine Verbindung mit unterschiedlichen Protokolle herstellen. In der Simulation können Sie entweder **AMQP**, **MQTT** oder **HTTP** verwenden:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulierter Gerätezustand

Jedes simulierte Gerät hat einen internen Zustand, der definiert werden muss. Der Zustand definiert auch die Eigenschaften, die in den Telemetriedaten gemeldet werden können. Eine Kältemaschine kann beispielsweise den folgenden Anfangszustand aufweisen:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Ein bewegliches Gerät mit mehreren Sensoren kann weitere Eigenschaften haben, z.B.:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Der Gerätestatus wird vom Simulationsdiensts im Arbeitsspeicher gespeichert und als Eingabe für die JavaScript-Funktion bereitgestellt. Die JavaScript-Funktion könnte wie folgt vorgehen:

* Sie könnte den Status ignorieren und einige zufällige Daten generieren.
* Sie könnte den Gerätestatus in realistischer Weise für ein bestimmtes Szenario aktualisieren.

Die Funktion, die den Zustand generiert, erhält auch folgende Eingaben:

* Der Geräte-ID
* Das Gerätemodell
* Die aktuelle Zeit. Dieser Wert ermöglicht es, unterschiedliche Daten nach Gerät und Zeit zu generieren.

### <a name="generating-telemetry-messages"></a>Generieren von Telemetrienachrichten

Der Simulationsdienst kann mehrere Typen von Telemetriedaten für jedes Gerät senden. Zu den Telemetriedaten gehören in der Regel Daten über den Gerätezustand. Beispielsweise kann ein simulierter Raum alle 10 Sekunden Informationen zu Temperatur und Luftfeuchtigkeit senden. Beachten Sie die Platzhalter im folgenden Codeausschnitt, die automatisch mit Werten über den Gerätezustand ersetzt werden:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Die Platzhalter verwenden die spezielle Syntax **${NAME}** , bei der **NAME** ein Schlüssel aus dem Gerätezustandsobjekt ist, das von der JavaScript-Funktion **main** zurückgegeben wird. Zeichenfolgen müssen in Anführungszeichen gesetzt werden, Zahlen nicht.

#### <a name="message-schema"></a>Nachrichtenschema

Jeder Nachrichtentyp muss ein klar definiertes Schema aufweisen. Das Nachrichtenschema wird auch in IoT Hub veröffentlicht, damit Back-End-Anwendungen die Informationen zum Interpretieren der eingehenden Telemetriedaten wiederverwenden können.

Das Schema unterstützt das JSON-Format, das einfache Analysen und Transformationen über mehrere Systeme und Dienste hinweg ermöglicht.

Die im Schema aufgeführten Felder können den folgenden Typen entsprechen:

* Objekt – mit JSON serialisiert
* Binär – mit base64 serialisiert
* Text
* Boolean
* Integer
* Double
* Datetime

### <a name="supported-methods"></a>Unterstützte Methoden

Simulierte Geräte können auch auf Methodenaufrufe reagieren. In diesem Fall führen sie Code aus und stellen eine Antwort bereit. Ähnlich wie die Simulation ist der Methodencode in einer JavaScript-Datei gespeichert und kann mit dem Gerätezustand interagieren. Beispiel:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Erstellen einer Gerätedefinitionsdatei

In dieser Schrittanleitung erfahren Sie, wie ein Gerätemodell für eine Drohne erstellt wird. Die Drohne fliegt nach dem Zufallsprinzip über einen anfänglichen Satz von Koordinaten und ändert dabei ihre Position und Höhe.

Kopieren Sie den folgenden JSON-Code in einen Text-Editor, und speichern Sie ihn als **drone.json**.

### <a name="device-definition-json-example"></a>JSON-Beispiel für die Gerätedefinition

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Skriptdateien für das Verhalten

Mit dem Code in der Skriptdatei für das Verhalten wird die Drohne bewegt. Das Skript ändert die Höhe und die Position der Drohne durch Ändern des Zustands des Geräts im Speicher.

Die JavaScript-Dateien müssen eine **main**-Funktion enthalten, die zwei Parameter akzeptiert:

* Ein **context**-Objekt, das drei Eigenschaften enthält:
    * **currentTime** als eine Zeichenfolge mit dem Format **JJJJ-MM-TT'T'HH:MM:SSZZZ**
    * **deviceId**, z.B. **Simulated.Elevator.123**
    * **deviceModel**, z.B. **Elevator**
* Ein **state**-Objekt, dies ist der von der Funktion im vorherigen Aufruf zurückgegebene Wert. Dieser Gerätezustand wird vom Simulationsdienst verwaltet und verwendet, um Telemetrienachrichten zu generieren.

Die **main**-Funktion gibt den neuen Gerätezustand zurück. Beispiel:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Erstellen einer Skriptdatei für das Verhalten

Kopieren Sie den folgenden JavaScript-Code in einen Text-Editor, und speichern Sie ihn als **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>JavaScript-Simulationsbeispiel für ein Gerätemodell

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Erstellen einer Skriptdatei mit Methoden

Methodenskripts ähneln Verhaltensskripts. Sie definieren das Geräteverhalten, wenn eine bestimmte Cloud-zu-Gerät-Methode aufgerufen wird.

Das Skript für den Rückruf der Drohne legt die Koordinaten der Drohne auf einen festen Punkt fest, um die Rückkehr der Drohne zum Ausgangspunkt zu simulieren.

Kopieren Sie den folgenden JavaScript-Code in einen Text-Editor, und speichern Sie ihn als **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>JavaScript-Simulationsbeispiel für ein Gerätemodell

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Debuggen von Skriptdateien

Sie können zwar keinen Debugger an eine aktive Verhaltensdatei anfügen, aber es ist möglich, mit der **log**-Funktion Informationen in das Protokoll zu schreiben. Bei Syntaxfehlern tritt im Interpreter ein Fehler auf, und Informationen über die Ausnahme werden in das Protokoll geschrieben.

Beispiel für die Protokollierung:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Bereitstellen eines erweiterten Gerätemodells

Um Ihr erweitertes Gerätemodell bereitzustellen, laden Sie die Dateien in die Instanz der Gerätesimulation hoch:

Wählen Sie auf der Menüleiste die Option **Device models** (Gerätemodelle) aus. Auf der Seite **Device models** (Gerätemodelle) werden die Geräte aufgeführt, die in dieser Instanz der Gerätesimulation verfügbar sind:

![Gerätemodelle](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klicken Sie rechts oben auf der Seite auf **+ Add Device Models** (+ Gerätemodelle hinzufügen):

![Gerätemodell hinzufügen](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klicken Sie auf **Advanced** (Erweitert), um die Registerkarte für erweiterte Gerätemodelle zu öffnen:

![Erweitert (Registerkarte)](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klicken Sie auf **Browse** (Durchsuchen), und wählen Sie die erstellten JSON- und JavaScript-Dateien aus. Sie müssen alle drei Dateien auswählen. Wenn eine Datei fehlt, schlägt die Überprüfung fehl:

![Nach Dateien suchen](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Wenn Ihre Dateien die Überprüfung bestanden haben, klicken Sie auf **Save** (Speichern). Jetzt kann Ihr Gerätemodell in einer Simulation verwendet werden. Beheben Sie andernfalls mögliche Fehler, und laden Sie die Dateien erneut hoch:

![Speichern](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schrittanleitung haben Sie die Dateien eines Gerätemodells kennengelernt, die in der Gerätesimulation verwendet werden, und haben erfahren, wie ein erweitertes Gerätemodell erstellt wird. Jetzt können Sie das [Verwenden von Time Series Insights zum Visualisieren der Telemetriedaten, die vom Solution Accelerator für Gerätesimulation gesendet wurden](./iot-accelerators-device-simulation-time-series-insights.md), untersuchen.