---
title: IoT Plug & Play-Konventionen | Microsoft-Dokumentation
description: Beschreibung der Konventionen, bei denen IoT Plug & Play erwartet, dass sie von Geräten verwendet werden, wenn sie Telemetriedaten und Eigenschaften senden und Befehle und Aktualisierungen von Eigenschaften durchführen.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 86c6ea9dded423e7bd513faf73adfd293f2bd38f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302611"
---
# <a name="iot-plug-and-play-conventions"></a>IoT Plug & Play-Konventionen

Für IoT Plug & Play-Geräte muss beim Austauschen von Nachrichten mit einem IoT-Hub eine Reihe von Konventionen eingehalten werden. Für IoT Plug & Play-Geräte wird für die Kommunikation mit IoT Hub das MQTT-Protokoll verwendet.

Geräte können [Module](../iot-hub/iot-hub-devguide-module-twins.md) enthalten oder in einem [IoT Edge-Modul](../iot-edge/about-iot-edge.md) implementiert werden, das von der IoT Edge-Runtime gehostet wird.

Die Telemetriedaten, Eigenschaften und Befehle, die ein Gerät in IoT Plug & Play implementiert, werden mit einem [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)-_Modell_ beschrieben. In diesem Artikel werden zwei Modelltypen erläutert:

- **Ohne Komponenten**: Ein Modell ohne Komponenten. Bei diesem Modell werden Telemetriedaten, Eigenschaften und Befehle im Inhaltsabschnitt der Hauptschnittstelle als Eigenschaften der obersten Ebene deklariert. Im Azure IoT-Explorer-Tool wird dieses Modell als einzelne _Standardkomponente_ angezeigt.
- **Mit mehreren Komponenten**: Ein Modell, das aus mindestens zwei Schnittstellen besteht. Eine Hauptschnittstelle, die als _Standardkomponente_ mit Telemetriedaten, Eigenschaften und Befehlen angezeigt wird. Und aus mindestens einer Schnittstelle, die als Komponente mit zusätzlichen Telemetriedaten, Eigenschaften und Befehlen deklariert wird.

Weitere Informationen finden Sie unter [IoT Plug & Play-Komponenten in Modellen](concepts-components.md).

## <a name="identify-the-model"></a>Identifizieren des Modells

Zur Ankündigung des Modells, das vom IoT Plug & Play-Gerät oder -Modul implementiert wird, wird die Modell-ID in das MQTT-Verbindungspaket eingebunden, indem dem Feld `USERNAME` der Eintrag `model-id` hinzugefügt wird.

Um das von einem Gerät oder einem Modul implementierte Modell zu identifizieren, kann ein Dienst die Modell-ID vom folgenden Ort abrufen:

- Vom `modelId`-Feld des Gerätezwillings
- Vom `$metadata.$model`-Feld des digitalen Zwillings
- Aus einer Änderungsbenachrichtigung bei einem digitalen Zwilling

## <a name="telemetry"></a>Telemetrie

Telemetriedaten, die von einem Gerät ohne Komponenten gesendet werden, erfordern keine zusätzlichen Metadaten. Vom System wird die `dt-dataschema`-Eigenschaft hinzugefügt.

Telemetriedaten, die von einem Gerät mit mehreren Komponenten gesendet werden, müssen zusätzlich `$.sub` als Nachrichteneigenschaft enthalten. Vom System werden die Eigenschaften `dt-subject` und `dt-dataschema` hinzugefügt.

## <a name="read-only-properties"></a>Schreibgeschützte Eigenschaften

### <a name="sample-no-component-read-only-property"></a>Beispiel für eine schreibgeschützte Eigenschaft eines Geräts ohne Komponenten

Ein Gerät oder Modul kann eine gültige JSON-Nachricht senden, die den DTDL v2-Regeln entspricht.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Beispiel für Nutzdaten der gemeldeten Eigenschaft:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Beispiel für eine schreibgeschützte Eigenschaft eines Geräts mit mehreren Komponenten

Das Gerät oder Modul muss zusätzlich den Marker `{"__t": "c"}` aufweisen, um anzugeben, dass das Element auf eine Komponente verweist.

DTDL zum Verweisen auf eine Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL zum Definieren der Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Beispiel für Nutzdaten der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Schreibbare Eigenschaften

Das Gerät oder Modul muss bestätigen, dass es die Eigenschaft erhalten hat, indem es eine gemeldete Eigenschaft sendet. Die gemeldete Eigenschaft muss Folgendes enthalten:

- `value`: den tatsächlichen Wert der Eigenschaft (in der Regel der empfangene Wert, aber das Gerät kann bei Bedarf einen anderen Wert melden).
- `ac`: einen Bestätigungscode, der einen HTTP-Statuscode enthält
- `av`: eine Bestätigungsversion, die auf die `$version` der gewünschten Eigenschaft verweist. Sie finden diesen Wert in den JSON-Nutzdaten der gewünschten Eigenschaft.
- `ad`: eine optionale Beschreibung der Bestätigung

Nachdem ein Gerät gestartet wurde, sollte es den Gerätezwilling anfordern und eine Überprüfung auf Updates für schreibbare Eigenschaften durchführen. Wenn sich die Versionsnummer einer schreibbaren Eigenschaft erhöht hat, während sich das Gerät im Offlinezustand befunden hat, sollte vom Gerät eine Antwort vom Typ „Gemeldete Eigenschaft“ gesendet werden, um den Empfang des Updates zu bestätigen.

Wenn ein Gerät zum ersten Mal gestartet wird, kann es einen Anfangswert für eine gemeldete Eigenschaft senden, falls es vom Hub keine anfängliche gewünschte Eigenschaft erhält. In diesem Fall sollte `av` vom Gerät auf `1` festgelegt werden. Beispiel:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Ein Gerät kann die gemeldete Eigenschaft verwenden, um weitere Informationen für den Hub bereitzustellen. Beispielsweise kann das Gerät mit einer Reihe von Meldungen vom Typ „Wird ausgeführt“ antworten, z. B.:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Wenn das Gerät die Zieltemperatur erreicht, sendet es die folgende Meldung:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Ein Gerät kann einen Fehler melden, z. B.:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Beispiel für eine schreibbare Eigenschaft eines Geräts ohne Komponenten

Wenn ein Gerät über nur ein Nutzdatenelement mehrere gemeldete Eigenschaften empfängt, kann es die Antworten zu gemeldeten Eigenschaften übergreifend für mehrere Nutzdatenelemente senden.

Ein Gerät oder Modul kann eine gültige JSON-Nachricht senden, die den DTDL v2-Regeln entspricht:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Beispiel für Nutzdaten einer gewünschten Eigenschaft:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Beispiel für erstes Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Beispiel für zweites Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Beispiel für eine schreibbare Eigenschaft eines Geräts mit mehreren Komponenten

Das Gerät oder Modul muss zusätzlich den Marker `{"__t": "c"}` aufweisen, um anzugeben, dass das Element auf eine Komponente verweist.

Der Marker wird nur für Updates von Eigenschaften gesendet, die in einer Komponente definiert sind. Updates von Eigenschaften, die in der Standardkomponente definiert sind, enthalten den Marker nicht. Weitere Informationen finden Sie unter [Beispiel für eine schreibbare Eigenschaft eines Geräts ohne Komponenten](#sample-no-component-writable-property).

Wenn ein Gerät über nur ein Nutzdatenelement mehrere gemeldete Eigenschaften empfängt, kann es die Antworten zu gemeldeten Eigenschaften übergreifend für mehrere Nutzdatenelemente senden.

Das Gerät oder Modul muss bestätigen, dass es die Eigenschaften erhalten hat, indem es gemeldete Eigenschaften sendet:

DTDL zum Verweisen auf eine Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL zum Definieren der Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Beispiel für Nutzdaten einer gewünschten Eigenschaft:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Beispiel für erstes Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Beispiel für zweites Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Befehle

Bei Schnittstellen ohne Komponenten wird der Befehlsname ohne Präfix verwendet.

Bei einem Gerät oder Modul werden bei Schnittstellen mit mehreren Komponenten Befehlsnamen im folgenden Format verwendet: `componentName*commandName`.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit IoT Plug & Play-Konventionen vertraut gemacht haben, finden Sie weitere Informationen in folgenden Artikeln:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C-Geräte-SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST-API](/rest/api/iothub/device)
- [Modellkomponenten](./concepts-components.md)