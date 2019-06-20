---
title: Datenverarbeitung und benutzerdefinierte Funktionen mit Azure Digital Twins | Microsoft-Dokumentation
description: Übersicht über Datenverarbeitung, Matcher und benutzerdefinierte Funktionen in Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60925869"
---
# <a name="data-processing-and-user-defined-functions"></a>Datenverarbeitung und benutzerdefinierte Funktionen

Azure Digital Twins stellt erweiterte Computefunktionen bereit. Entwickler können benutzerdefinierte Funktionen erstellen und für eingehende Telemetrienachrichten ausführen, um Ereignisse an vordefinierte Endpunkte zu senden.

## <a name="data-processing-flow"></a>Datenverarbeitungsfluss

Nachdem Geräte Telemetriedaten an Azure Digital Twins gesendet haben, können Entwickler diese Daten in vier Phasen verarbeiten: *Überprüfen*, *Abgleichen*, *Berechnen* und *Versenden*.

![Datenverarbeitungsfluss in Azure Digital Twins][1]

1. In der Phase „Überprüfen“ wird die eingehende Telemetrienachricht in ein allgemein verständliches Format eines [Datenübertragungsobjekts](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) transformiert. In dieser Phase erfolgt auch die Überprüfung von Geräten und Sensoren.
1. In der Phase „Abgleichen“ wird nach den entsprechenden benutzerdefinierten Funktionen gesucht, die ausgeführt werden sollen. Vordefinierte Matcher (Abgleicher) finden die benutzerdefinierten Funktionen anhand der Geräte-, Sensor- und Rauminformationen aus der eingehenden Telemetrienachricht.
1. In der Phase „Berechnen“ werden die benutzerdefinierten Funktionen ausgeführt, für die in der vorherigen Phase eine Übereinstimmung gefunden wurde. Diese Funktionen können berechnete Werte aus Raumgraphknoten lesen und aktualisieren sowie benutzerdefinierte Benachrichtigungen ausgeben.
1. In der Phase „Versenden“ werden alle benutzerdefinierten Benachrichtigungen aus der Berechnungsphase an Endpunkte gesendet, die im Graphen definiert sind.

## <a name="data-processing-objects"></a>Datenverarbeitungsobjekte

Die Datenverarbeitung in Azure Digital Twins umfasst das Definieren von drei Objekten: *Matcher*, *benutzerdefinierte Funktionen* und *Rollenzuweisungen*.

![Datenverarbeitungsobjekte in Azure Digital Twins][2]

<div id="matcher"></div>

### <a name="matchers"></a>Matcher (Abgleicher)

Matcher definieren eine Reihe von Bedingungen, mit denen anhand der eingehenden Telemetriedaten eines Sensors bestimmt wird, welche Aktionen ausgeführt werden. In diesen Bedingungen können Eigenschaften des Sensors, des übergeordneten Geräts des Sensors und des übergeordneten Gebäudebereichs des Sensors enthalten sein. Die Bedingungen werden wie im folgenden Beispiel dargestellt als Vergleiche mit einem [JSON-Pfad](https://jsonpath.com/) formuliert:

- Alle Sensoren des Datentyps **Temperature**, die durch den mit Escapezeichen versehenen Zeichenfolgenwert `\"Temperature\"` dargestellt werden
- Die den Wert `01` im Port aufweisen
- Die zu Geräten gehören, für die der erweiterte Eigenschaftenschlüssel **Hersteller** auf den mit Escapezeichen versehenen Zeichenfolgenwert `\"GoodCorp\"` festgelegt ist
- Die zu Räumen des Typs gehören, der durch den mit Escapezeichen versehenen Zeichenfolgenwert `\"Venue\"` angegeben ist
- Die Nachfolger der übergeordneten **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` sind

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Bei JSON-Pfaden muss die Groß-/Kleinschreibung beachtet werden.
> - Die JSON-Nutzlast ist identisch mit der Nutzlast, die für Folgendes zurückgegeben wird:
>   - `/sensors/{id}?includes=properties,types` für den Sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` für das übergeordnete Gerät des Sensors.
>   - `/spaces/{id}?includes=properties,types,location,timezone` für den übergeordneten Raum des Sensors.
> - Bei den Vergleichen muss die Groß-/Kleinschreibung nicht beachtet werden.

### <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

Eine benutzerdefinierte Funktion ist eine angepasste Funktion, die in einer isolierten Azure Digital Twins-Umgebung ausgeführt wird. Benutzerdefinierte Funktionen haben Zugriff auf die empfangene unformatierte Telemetrienachricht eines Sensors. Benutzerdefinierte Funktionen haben auch Zugriff auf den Raumgraph und den Verteilerdienst. Nachdem die benutzerdefinierte Funktion im Graph registriert wurde, muss ein Matcher (Beschreibung weiter [oben](#matcher)) erstellt werden, um anzugeben, wann die Funktion ausgeführt werden soll. Wenn Azure Digital Twins beispielsweise neue Telemetriedaten von einem bestimmten Sensor empfängt, kann die übereinstimmende benutzerdefinierte Funktion einen gleitenden Durchschnitt der letzten Sensormesswerte berechnen.

Benutzerdefinierte Funktionen können in JavaScript geschrieben werden. Hilfsmethoden interagieren mit dem Graphen in der benutzerdefinierten Ausführungsumgebung. Entwickler können benutzerdefinierte Codeausschnitte für Telemetrienachrichten von Sensoren ausführen. Beispiele:

- Festlegen eines Sensormesswerts direkt im Sensorobjekt im Graphen
- Ausführen einer Aktion basierend auf verschiedenen Sensormesswerten in einem Raum im Graphen
- Erstellen einer Benachrichtigung, wenn bestimmte Bedingungen für einen eingehenden Sensormesswert erfüllt sind
- Hinzufügen von Graphmetadaten zu einem Sensormesswert, bevor eine Benachrichtigung gesendet wird

Weitere Informationen finden Sie unter [Definieren von benutzerdefinierten Funktionen in Azure Digital Twins](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Beispiele

Das [GitHub-Repository mit dem C#-Beispiel für Digital Twins](https://github.com/Azure-Samples/digital-twins-samples-csharp/) enthält einige Beispiele für benutzerdefinierte Funktionen:
- [Mit dieser Funktion](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) wird nach Kohlendioxid-, Bewegungs- und Temperaturwerten gesucht, um festzustellen, ob ein Raum verfügbar ist, bei dem diese Werte im zulässigen Bereich liegen. In den [Tutorials für Digital Twins](tutorial-facilities-udf.md) wird diese Funktion genauer untersucht. 
- [Mit dieser Funktion](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) wird nach Daten von mehreren Bewegungssensoren gesucht und festgestellt, dass der Platz verfügbar ist, wenn von keinem Sensor eine Bewegung erkannt wird. Sie können die im [Schnellstart](quickstart-view-occupancy-dotnet.md) oder den [Tutorials](tutorial-facilities-setup.md) verwendete benutzerdefinierte Funktion problemlos ersetzen, indem Sie die Änderungen vornehmen, die im Kommentarabschnitt der Datei genannt sind. 



### <a name="role-assignment"></a>Rollenzuweisung

Die Aktionen einer benutzerdefinierten Funktion unterliegen der [rollenbasierten Zugriffssteuerung](./security-role-based-access-control.md) von Azure Digital Twins, um die Daten im Dienst zu schützen. Mit Rollenzuweisungen wird definiert, welche benutzerdefinierte Funktionen über die erforderlichen Berechtigungen für die Interaktion mit dem Raumgraph und seinen Entitäten verfügen. Eine benutzerdefinierte Funktion kann beispielsweise über die Möglichkeit und Berechtigung verfügen, Diagrammdaten unter einem bestimmten Bereich zu *ERSTELLEN*, zu *LESEN*, zu *AKTUALISIEREN* oder zu *LÖSCHEN*. Die Zugriffsebene einer benutzerdefinierten Funktion wird überprüft, wenn diese Daten aus dem Graph abrufen möchte oder versucht, eine Aktion auszuführen. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung](./security-create-manage-role-assignments.md).

Möglicherweise kann ein Matcher eine benutzerdefinierte Funktion auslösen, die über keine Rollenzuweisungen verfügt. In diesem Fall kann die benutzerdefinierte Funktion keine Daten aus dem Graph lesen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Weiterleiten von Ereignissen und Telemetrienachrichten an andere Azure-Dienste finden Sie unter [Weiterleiten von Ereignissen und Nachrichten](./concepts-events-routing.md).

- Weitere Informationen dazu, wie Matcher, benutzerdefinierte Funktionen und Rollenzuweisungen erstellt werden, finden Sie unter [Verwenden von benutzerdefinierten Funktionen in Azure Digital Twins](./how-to-user-defined-functions.md).

- Weitere Informationen finden Sie in der [Referenzdokumentation zur UDF-Clientbibliothek](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
