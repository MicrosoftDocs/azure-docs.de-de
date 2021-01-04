---
title: Abfragen des Azure IoT Hub-Nachrichtenroutings | Microsoft-Dokumentation
description: Hier erfahren Sie etwas zur Abfragesprache für das IoT Hub-Nachrichtenrouting, mit der Sie umfassende Abfragen für Nachrichten ausführen können, um die für Sie wichtigen Daten zu empfangen.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547706"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Abfragesyntax für das IoT Hub-Nachrichtenrouting

Das Nachrichtenrouting ermöglicht Benutzern, verschiedene Datentypen wie Gerätetelemetrienachrichten, Gerätelebenszyklusereignisse und Änderungsereignisse für Gerätezwillinge an verschiedene Endpunkte weiterzuleiten. Sie können auch umfassende Abfragen für diese Daten ausführen, bevor Sie sie weiterleiten, um die Daten zu empfangen, die für Sie wichtig sind. In diesem Artikel werden die Abfragesprache für das IoT Hub-Nachrichtenrouting beschrieben und allgemeine Abfragemuster bereitgestellt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Mithilfe des Nachrichtenroutings können Sie Abfragen für Nachrichteneigenschaften und Nachrichtentext sowie für Gerätezwillingstags und Gerätezwillingseigenschaften durchführen. Wenn der Text nicht das JSON-Format aufweist, kann die Nachricht dennoch durch das Nachrichtenrouting weitergeleitet werden, jedoch können keine Abfragen des Nachrichtentexts ausgeführt werden.  Abfragen werden mit booleschen Ausdrücken beschrieben. Hierbei gibt der boolesche Wert TRUE an, dass die Abfrage erfolgreich ist, wodurch alle eingehenden Daten weitergeleitet werden, und der boolesche Wert FALSE gibt an, dass die Abfrage fehlgeschlagen ist und Daten nicht weitergeleitet werden. Wenn die Auswertung des Ausdrucks NULL oder „nicht definiert“ ergibt, wird er als FALSE angesehen. Außerdem wird im Fall eines Fehlers eine Fehlermeldung in den [Protokollen zu Routenressourcen](monitor-iot-hub-reference.md#routes) von IoT Hub generiert. Damit die Weiterleitung gespeichert und ausgewertet wird, muss die Abfragesyntax richtig sein.  

## <a name="message-routing-query-based-on-message-properties"></a>Abfrage des Nachrichtenroutings basierend auf Nachrichteneigenschaften 

Der IoT Hub definiert ein [gemeinsames Format](iot-hub-devguide-messages-construct.md) für alle Gerät-zu-Cloud-Nachrichten, um Interoperabilität zwischen Protokollen zu ermöglichen. Die IoT Hub-Nachricht geht von der folgenden JSON-Darstellung der Nachricht aus. Systemeigenschaften identifizieren den Nachrichteninhalt und werden allen Benutzern hinzugefügt. Benutzer können der Nachricht selektiv Anwendungseigenschaften hinzufügen. Die Verwendung eindeutiger Eigenschaftennamen wird empfohlen, da das Gerät-zu-Cloud-Messaging von IoT Hub die Groß-/Kleinschreibung nicht beachtet. Wenn Sie beispielsweise über mehrere Eigenschaften mit dem gleichen Namen verfügen, sendet IoT Hub nur eine der Eigenschaften.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systemeigenschaften

Mithilfe von Systemeigenschaften werden Inhalt und Quelle von Nachrichten identifiziert. 

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| contentType | string | Der Benutzer gibt den Inhaltstyp der Nachricht an. Dieser Wert sollte auf „application/JSON“ festgelegt werden, damit Abfragen für den Nachrichtentext ausgeführt werden können. |
| contentEncoding | string | Der Benutzer gibt den Codierungstyp der Nachricht an. Wenn contentType auf „application/JSON“ festgelegt ist, sind die folgenden Werte gültig: UTF-8, UTF-16 und UTF-32. |
| iothub-connection-device-id | string | Dieser Wert wird von IoT Hub festgelegt, und er identifiziert die ID des Geräts. Verwenden Sie `$connectionDeviceId` für die Abfrage. |
| iothub-enqueuedtime | string | Dieser Wert wird von IoT Hub festgelegt und stellt den tatsächlichen Zeitpunkt dar, zu dem die Nachricht in UTC eingereiht wird. Verwenden Sie `enqueuedTime` für die Abfrage. |
| dt-dataschema | string |  Dieser Wert wird von IoT Hub für Gerät-zu-Cloud-Nachrichten festgelegt. Er enthält die in der Geräteverbindung festgelegte Gerätemodell-ID. Verwenden Sie `$dt-dataschema` für die Abfrage. |
| dt-subject | Zeichenfolge | Der Name der Komponente, die die Gerät-zu-Cloud-Nachrichten sendet. Verwenden Sie `$dt-subject` für die Abfrage. |

Wie im Artikel zu [IoT Hub-Nachrichten](iot-hub-devguide-messages-construct.md) beschrieben wird, gibt es mehrere zusätzliche Systemeigenschaften in einer Nachricht. Zusätzlich zu den Eigenschaften in der obigen Tabelle können Sie auch **connectionDeviceId** und **connectionModuleId** abfragen.

### <a name="application-properties"></a>Anwendungseigenschaften

Anwendungseigenschaften sind benutzerdefinierte Zeichenfolgen, die der Nachricht hinzugefügt werden können. Diese Felder sind optional.  

### <a name="query-expressions"></a>Abfrageausdrücke

Abfragen der Nachrichtensystemeigenschaften muss das Symbol `$` vorangestellt werden. Auf Abfragen von Anwendungseigenschaften wird anhand der Namen zugegriffen, ihnen sollte das Symbol `$` nicht vorangestellt werden. Wenn der Name einer Anwendungseigenschaft mit `$` beginnt, sucht IoT Hub zunächst in den Systemeigenschaften nach dieser. Wenn sie dort nicht gefunden werden konnte, wird in den Anwendungseigenschaften gesucht. Beispiel: 

So führen Sie eine Abfrage der Systemeigenschaft „contentEncoding“ durch 

```sql
$contentEncoding = 'UTF-8'
```

So führen Sie eine Abfrage der Anwendungseigenschaft „processingPath“ durch

```sql
processingPath = 'hot'
```

Sie können boolesche Ausdrücke und Funktionen verwenden, um diese Abfragen zu kombinieren:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Eine vollständige Liste der unterstützten Operatoren und Funktionen finden Sie unter [Ausdrücke und Bedingungen](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Abfrage des Nachrichtenroutings basierend auf dem Nachrichtentext

Die Nachricht sollte ein mit UTF-8, UTF-16 oder UTF-32 codiertes JSON-Format aufweisen, um das Abfragen des Nachrichtentexts zu ermöglichen. `contentType` muss auf `application/JSON` und `contentEncoding` muss in der Systemeigenschaft auf eine der unterstützten UTF-Codierungen festgelegt sein. Wenn diese Eigenschaften nicht festgelegt sind, wird der Abfrageausdruck für den Nachrichtentext nicht von IoT Hub ausgewertet. 

Das folgende Beispiel zeigt, wie eine Nachricht mit einem ordnungsgemäß formatierten und codierten JSON-Text erstellt wird: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Hier sehen Sie, wie die Codierung des Texts in JavaScript behandelt werden muss. Wenn Sie sich ein Beispiel in C# ansehen möchten, laden Sie die [Azure IoT-Beispiele für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Entzippen Sie die Datei „master.zip“. In der Datei „Program.cs“ aus der Visual Studio-Projektmappe *SimulatedDevice* werden die Codierung und Übermittlung von Nachrichten an eine IoT Hub-Instanz gezeigt. Dieses Beispiel wird auch im [Nachrichtenrouting-Tutorial](tutorial-routing.md) zum Testen des Nachrichtenroutings verwendet. Am Ende von „Program.cs“ befindet sich auch eine Methode, die dazu dient, in einer der codierten Dateien zu lesen, die Datei zu decodieren und den Inhalt als lesbaren ASCII-Code auszugeben. 


### <a name="query-expressions"></a>Abfrageausdrücke

Einer Abfrage des Nachrichtentexts muss das Präfix `$body` vorangestellt werden. Sie können einen Textverweis, Textarrayverweis oder mehrere Textverweise im Abfrageausdruck verwenden. Der Abfrageausdruck kann auch einen Textverweis mit Nachrichtensystemeigenschaften und einem Verweis auf Nachrichtenanwendungseigenschaften kombinieren. Die folgenden Abfrageausdrücke sind beispielsweise sämtlich gültig: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Abfrage des Nachrichtenroutings basierend auf dem Gerätezwilling 

Das Nachrichtenrouting ermöglicht Ihnen das Abfragen von Tags und Eigenschaften des [Gerätezwillings](iot-hub-devguide-device-twins.md), die JSON-Objekte sind. Das Abfragen des Modulzwillings wird ebenfalls unterstützt. Im Folgenden wird ein Beispiel für Gerätezwillingstags gezeigt.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Abfrageausdrücke

Einer Abfrage des Nachrichtenzwillings muss das Präfix `$twin` vorangestellt werden. Der Abfrageausdruck kann auch ein Zwillingstag oder Eigenschaftenverweis mit einem Textverweis und mit Nachrichtensystemeigenschaften sowie einem Verweis auf Nachrichtenanwendungseigenschaften kombinieren. Die Verwendung eindeutiger Namen wird für Tags und Eigenschaften empfohlen, da die Abfrage die Groß- und Kleinschreibung nicht beachtet. Dies gilt für Gerätezwillinge und Modulzwillinge. Außerdem sollten Sie `twin`, `$twin`, `body` oder `$body` nicht als Eigenschaftennamen verwenden. Die folgenden Abfrageausdrücke sind beispielsweise sämtlich gültig: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routingabfragen für Text oder Gerätezwilling mit einem Punkt in der Nutzlast oder im Eigenschaftsnamen werden nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Nachrichtenrouting](iot-hub-devguide-messages-d2c.md).
* Sehen Sie sich das [Tutorial zum Nachrichtenrouting](tutorial-routing.md) an.
