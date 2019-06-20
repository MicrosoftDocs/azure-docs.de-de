---
title: Erstellen von benutzerdefinierten Funktionen in Azure Digital Twins | Microsoft-Dokumentation
description: Erstellen benutzerdefinierter Funktionen, Matcher und Rollenzuweisungen in Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: b5e230f45fd5889e216f2993f58adf6940ef7679
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072914"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Erstellen von benutzerdefinierten Funktionen in Azure Digital Twins

[Benutzerdefinierte Funktionen](./concepts-user-defined-functions.md) ermöglichen es Benutzern, benutzerdefinierte Logik so zu konfigurieren, dass sie aus eingehenden Telemetrienachrichten und Raumgraphmetadaten ausgeführt wird. Benutzer können auch Ereignisse an vordefinierte [Endpunkte](./how-to-egress-endpoints.md) senden.

In diesem Leitfaden durchlaufen Sie ein Beispiel, das veranschaulicht, wie Sie jede Messung von empfangenen Temperaturereignissen erkennen, bei der eine bestimmte Temperatur überschritten wurde, und eine Warnung für eine solche Messung senden.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referenz zur Clientbibliothek

Die als Hilfsmethoden in der Runtime für benutzerdefinierte Funktionen verfügbaren Funktionen werden im Dokument [Clientbibliotheksreferenz](./reference-user-defined-functions-client-library.md) aufgeführt.

## <a name="create-a-matcher"></a>Erstellen eine Matchers

Matcher (Abgleicher) sind Graphobjekte, die bestimmen, welche benutzerdefinierten Funktionen für eine bestimmte Telemetrienachricht ausgeführt werden.

- Gültige Matcherbedingungsvergleiche:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Gültige Matcherbedingungsziele:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Der folgende Beispielmatcher besitzt den Ergebniswert TRUE für jedes Sensortelemetrieereignis, das den Datentyp `"Temperature"` aufweist. Sie können mehrere Matcher für eine benutzerdefinierte Funktion erstellen, indem Sie eine authentifizierte HTTP-POST-Anforderung an folgende URL stellen:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Mit JSON-Text:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Die Serverregion, in der Ihre Instanz gehostet wird |

## <a name="create-a-user-defined-function"></a>Erstellen einer benutzerdefinierten Funktion

Das Erstellen einer benutzerdefinierten Funktion beinhaltet das Erstellen einer mehrteiligen HTTP-Anforderung an die Azure Digital Twins-Verwaltungs-APIs.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Nachdem die Matcher erstellt wurden, laden Sie den Funktionsocdeausschnitt mit der folgenden authentifizierten mehrteiligen HTTP-POST-Anforderung hoch:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Verwenden Sie folgenden Text:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Wert | Ersetzen durch |
| --- | --- |
| USER_DEFINED_BOUNDARY | Name für die Begrenzung mehrteiliger Inhalte |
| YOUR_SPACE_IDENTIFIER | Den Raumbezeichner (Raum-ID)  |
| YOUR_MATCHER_IDENTIFIER | Die ID des Matchers, den Sie verwenden möchten |

1. Vergewissern Sie sich, dass die Header Folgendes enthalten: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Vergewissern Sie sich, dass der Text mehrteilig ist:

   - Der erste Teil enthält die erforderliche Metadaten für die benutzerdefinierte Funktion.
   - Der zweite Teil enthält die JavaScript-Computelogik.

1. Ersetzen Sie im Abschnitt **USER_DEFINED_BOUNDARY** die Werte für **spaceId** (`YOUR_SPACE_IDENTIFIER`) und **Matchers** (`YOUR_MATCHER_IDENTIFIER`).
1. Vergewissern Sie sich, dass die benutzerdefinierte JavaScript-Funktion als `Content-Type: text/javascript` bereitgestellt wird.

### <a name="example-functions"></a>Beispielfunktionen

Legen Sie den Sensortelemetriemesswert direkt für den Sensor mit dem Datentyp **Temperature** fest, der `sensor.DataType` entspricht:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Der **telemetry**-Parameter macht die Attribute **SensorId** und **Message** entsprechend einer von einem Sensor gesendeten Nachricht verfügbar. Der **executionContext**-Parameter macht die folgenden Attribute verfügbar:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Im nächsten Beispiel protokollieren wir eine Nachricht, wenn der Sensortelemetriemesswert einen vordefinierten Schwellenwert überschreitet. Wenn Ihre Diagnoseeinstellungen in der Azure Digital Twins-Instanz aktiviert sind, werden Protokolle von benutzerdefinierten Funktionen ebenfalls weitergeleitet:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Im folgenden Code wird eine Benachrichtigung ausgelöst, wenn die Temperatur den festgelegten Wert der Konstanten überschreitet:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Ein komplexeres Beispiel für eine benutzerdefinierte Funktion finden Sie im [Belegungsschnellstart](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

Erstellen Sie eine Rollenzuweisung, unter der die benutzerdefinierte Funktion ausgeführt werden soll. Wenn für die benutzerdefinierte Funktion keine Rollenzuweisung vorhanden ist, verfügt sie nicht über die erforderlichen Berechtigungen für die Interaktion mit der Verwaltungs-API oder den Zugriff zum Ausführen von Aktionen an Graphobjekten. Aktionen, die eine benutzerdefinierte Funktion ausführen kann, werden über eine rollenbasierte Zugriffskontrolle innerhalb der Azure Digital Twins-Verwaltungs-APIs spezifiziert und definiert. Benutzerdefinierte Funktionen können beispielsweise hinsichtlich des Geltungsbereichs eingeschränkt werden, indem bestimmte Rollen oder bestimmte Zugriffssteuerungspfade angegeben werden. Weitere Informationen finden Sie in der Dokumentation zur [rollenbasierten Zugriffssteuerung](./security-role-based-access-control.md).

1. [Fragen Sie die System-API](./security-create-manage-role-assignments.md#all) nach allen Rollen ab, um die Rollen-ID zu erhalten, die Sie Ihrer benutzerdefinierten Funktion zuweisen möchten. Stellen Sie hierzu eine authentifizierte HTTP-GET-Anforderung an:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Behalten Sie die gewünschten Rollen-ID. Sie wird als JSON-Textattribut **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) unten übergeben.

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) ist dann die zuvor erstellte ID der benutzerdefinierten Funktion.
1. Suchen Sie den Wert von **Path** (`YOUR_ACCESS_CONTROL_PATH`) durch Abfragen Ihrer Räume mit `fullpath`.
1. Kopieren Sie den zurückgegebenen `spacePaths`-Wert. Diesen verwenden Sie dann weiter unten. Stellen Sie eine authentifizierte HTTP-GET-Anforderung an:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_SPACE_NAME | Der Name des Raums, den Sie verwenden möchten |

1. Fügen Sie den zurückgegebenen `spacePaths`-Wert in **path** ein, um eine benutzerdefinierte Funktionsrollenzuweisung zu erstellen, indem Sie eine authentifizierte HTTP-POST-Anforderung an folgende URL stellen:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Mit JSON-Text:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Der Bezeichner für die gewünschte Rolle |
    | YOUR_USER_DEFINED_FUNCTION_ID | Die ID für die benutzerdefinierte Funktion, die Sie verwenden möchten. |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Die ID, die den Typ der benutzerdefinierten Funktion angibt. |
    | YOUR_ACCESS_CONTROL_PATH | Der Zugriffssteuerungspfad |

>[!TIP]
> Weitere Informationen zu auf benutzerdefinierte Funktionen bezogenen Verwaltungs-API-Vorgängen und Endpunkten finden Sie in dem Artikel [Erstellen und Verwalten von Rollenzuweisungen](./security-create-manage-role-assignments.md).

## <a name="send-telemetry-to-be-processed"></a>Senden der Telemetriedaten, die verarbeitet werden sollen

Der Raumintelligenzgraph definierte Sensor sendet Telemetriedaten. Im Gegenzug löst die Telemetrie die Ausführung der benutzerdefinierten Funktion aus, die hochgeladen wurde. Die Datenverarbeitung übernimmt die Telemetriedaten. Anschließend wird ein Ausführungsplan für den Aufruf der benutzerdefinierten Funktion erstellt.

1. Rufen Sie die Matcher für den Sensor ab, von dem der Messwert generiert wurde.
1. Je nachdem, welche Matcher erfolgreich ausgewertet wurden, rufen Sie die zugehörigen benutzerdefinierten Funktionen ab.
1. Führen Sie jede benutzerdefinierte Funktion aus.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Erstellen von Azure Digital Twins-Endpunkten](./how-to-egress-endpoints.md), an die Ereignisse gesendet werden sollen.

- Weitere Details zum Weiterleiten von Ereignissen in Azure Digital Twins finden Sie unter [Weiterleiten von Ereignissen und Nachrichten](./concepts-events-routing.md).

- Lesen Sie die [Clientbibliotheksreferenz-Dokumentation](./reference-user-defined-functions-client-library.md).
