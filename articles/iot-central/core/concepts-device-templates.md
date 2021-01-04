---
title: Gerätevorlagen in Azure IoT Central | Microsoft-Dokumentation
description: Mit Azure IoT Central-Gerätevorlagen können Sie das Verhalten der mit Ihrer Anwendung verbundenen Geräte festlegen. Eine Gerätevorlage legt die Telemetrie, Eigenschaften und Befehle fest, die das Gerät implementieren muss. Außerdem definiert eine Gerätevorlage die Benutzeroberfläche für das Gerät in IoT Central, z. B. die von einem Operator verwendeten Formulare und Dashboards.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 1a352849cb5bb8563a7e09500f081139a24e97ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750695"
---
# <a name="what-are-device-templates"></a>Was sind Gerätevorlagen?

_Dieser Artikel richtet sich an Geräte- und Lösungsentwickler._

Eine Gerätevorlage in Azure IoT Central ist eine Blaupause, die die Merkmale und das Verhalten eines Gerätetyps definiert, der eine Verbindung mit Ihrer Anwendung herstellt. Beispielsweise definiert die Gerätevorlage die Telemetrie, die ein Gerät sendet, sodass IoT Central Visualisierungen mit den richtigen Einheiten und Datentypen erstellen kann.

Ein Lösungsgenerator fügt einer IoT Central-Anwendung Gerätevorlagen hinzu. Ein Geräteentwickler schreibt den Gerätecode, der die in der Gerätevorlage definierten Verhalten umsetzt.

Eine Gerätevorlage enthält die folgenden Abschnitte:

- _Ein Gerätemodell_. In diesem Teil der Gerätevorlage wird definiert, wie das Gerät mit Ihrer Anwendung interagiert. Ein Geräteentwickler setzt die im Modell definierten Verhalten um.
    - _Standardkomponente_. Jedes Gerätemodell hat eine Standardkomponente. Die Schnittstelle der Standardkomponente beschreibt die spezifischen Funktionen des Gerätemodells.
    - _Komponenten_. Ein Gerätemodell kann neben der Standardkomponente auch Komponenten enthalten, um Gerätefunktionen zu beschreiben. Jede Komponente verfügt über eine Schnittstelle, mit der die Funktionen der Komponente beschrieben werden. Komponentenschnittstellen können in anderen Gerätemodellen wiederverwendet werden. Beispielsweise können mehrere Telefongerätemodelle dieselbe Kameraschnittstelle verwenden.
    - _Geerbte Schnittstellen_. Ein Gerätemodell enthält eine oder mehrere Schnittstellen, mit denen die Funktionen der Standardkomponente erweitert werden.
- _Cloudeigenschaften_. In diesem Teil der Gerätevorlage kann der Entwickler der Lösung alle zu speichernden Gerätemetadaten angeben. Cloudeigenschaften werden nicht mit Geräten synchronisiert und sind ausschließlich in der Anwendung vorhanden. Cloudeigenschaften haben keinen Einfluss auf den Code, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt.
- _Anpassungen_. In diesem Teil der Gerätevorlage kann der Lösungsentwickler einige der Definitionen im Gerätemodell überschreiben. Anpassungen sind nützlich, wenn der Lösungsentwickler präzisieren möchte, wie die Anwendung mit einem Wert umgeht, z. B. wenn der Anzeigename für eine Eigenschaft oder die zur Anzeige eines Telemetriewerts verwendete Farbe geändert werden soll. Anpassungen wirken sich nicht auf den Code aus, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt.
- _Ansichten_. In diesem Teil der Gerätevorlage kann der Lösungsentwickler Visualisierungen zum Anzeigen von Daten aus dem Gerät und Formulare zur Verwaltung und Steuerung eines Geräts definieren. Die Ansichten enthalten das Gerätemodell, Cloudeigenschaften und Anpassungen. Ansichten haben keinen Einfluss auf den Code, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt.

## <a name="device-models"></a>Gerätemodelle

Ein Gerätemodell definiert, wie ein Gerät mit Ihrer IoT Central-Anwendung interagiert. Der Geräteentwickler muss sicherstellen, dass das Gerät die im Gerätemodell definierten Verhaltensweisen umsetzt, damit IoT Central das Gerät überwachen und verwalten kann. Ein Gerätemodell besteht aus einer oder mehreren _Schnittstellen_. Jede Schnittstelle kann eine Sammlung von _Telemetrietypen_, _Geräteeigenschaften_ und _Befehlen_ definieren. Ein Lösungsentwickler kann eine JSON-Datei mit der Definition des Gerätemodells in eine Gerätevorlage importieren oder ein Gerätemodell auf der Webbenutzeroberfläche von IoT Central erstellen oder bearbeiten. Änderungen an einem Gerätemodell, die auf der Webbenutzeroberfläche vorgenommen werden, erfordern eine [Versionsverwaltung der Gerätevorlage](./howto-version-device-template.md).

Ein Lösungsentwickler kann auch eine JSON-Datei exportieren, die das Gerätemodell enthält. Ein Geräteentwickler kann anhand dieses JSON-Dokuments verstehen, wie das Gerät mit der IoT Central-Anwendung kommunizieren soll.

Die JSON-Datei mit der Definition des Gerätemodells verwendet [Digital Twin Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central erwartet, dass die JSON-Datei das Gerätemodell mit inline und nicht in separaten Dateien definierten Schnittstellen enthält.

Ein typisches IoT-Gerät besteht aus folgenden Komponenten:

- Benutzerdefinierte Komponenten, die Ihr Gerät einzigartig machen
- Standardkomponenten, die bei allen Geräten identisch sind

Diese Komponenten werden in einem Gerätemodell als _Schnittstellen_ bezeichnet. Schnittstellen definieren die Details der einzelnen mit Ihrem Gerät implementierten Komponenten. Schnittstellen lassen sich in mehreren Gerätemodellen wiederverwenden. In der DTDL verweist eine Komponente auf eine Schnittstelle, die in einer separaten DTDL-Datei definiert ist.

Das folgende Beispiel zeigt die Gliederung des Gerätemodells für ein Temperatursteuerungsgerät. Die Standardkomponente enthält Definitionen für `workingSet`, `serialNumber`und `reboot`. Das Gerätemodell umfasst auch die Schnittstellen `thermostat` und `deviceInformation`:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Eine Schnittstelle umfasst einige Pflichtfelder:

- `@id`: eine eindeutige ID im Format eines einfachen Uniform Resource Name
- `@type`: deklariert dieses Objekt als Schnittstelle
- `@context`: gibt die für die Schnittstelle verwendete DTDL-Version an
- `contents`: listet die Eigenschaften, Telemetriedaten und Befehle auf, die das Gerät ausmachen Die Funktionen können in mehreren Schnittstellen definiert werden.

Es gibt einige optionale Felder, über die Sie dem Funktionsmodell weitere Details hinzufügen können, z. B. den Anzeigenamen und eine Beschreibung.

Jeder Eintrag in der Liste der Schnittstellen im Abschnitt „implements“ enthält jeweils Folgendes:

- `name`: der Programmiername der Schnittstelle
- `schema`: die Schnittstelle, die mit dem Funktionsmodell implementiert wird

## <a name="interfaces"></a>Schnittstellen

Mit DTDL beschreiben Sie die Funktionen Ihres Geräts. Zusammengehörige Funktionen sind in Schnittstellen gruppiert. Schnittstellen beschreiben die Eigenschaften, Telemetrie und Befehle, die eine Komponente des Geräts implementiert:

- `Properties`. Eigenschaften sind Datenfelder, die den Zustand des Geräts darstellen. Verwenden Sie Eigenschaften, um den dauerhaften Zustand des Geräts darzustellen, z. B. den Ein/Aus-Status einer Kühlmittelpumpe. Eigenschaften können außerdem grundlegende Geräteeigenschaften darstellen, z. B. die Firmwareversion des Geräts. Sie können Eigenschaften als schreibgeschützt oder beschreibbar deklarieren. Nur Geräte können den Wert einer schreibgeschützten Eigenschaft aktualisieren. Ein Operator kann den Wert einer beschreibbaren Eigenschaft festlegen, die an ein Gerät gesendet werden soll.
- `Telemetry`. Telemetriefelder stellen Messungen von Sensoren dar. Immer wenn das Gerät eine Sensormessung vornimmt, sollte ein Telemetrieereignis mit den Sensordaten gesendet werden.
- `Commands`. Befehle stellen Methoden dar, die Benutzer des Geräts auf dem Gerät ausführen können, beispielsweise ein Befehl zum Zurücksetzen oder ein Befehl zum Ein- oder Ausschalten eines Lüfters.

Das folgende Beispiel zeigt die Definition für eine Thermostatschnittstelle:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
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
  ]
}
```

Dieses Beispiel zeigt zwei Eigenschaften (eine schreibgeschützte und eine beschreibbare), einen Telemetrietyp und einen Befehl. Eine minimale Feldbeschreibung umfasst Folgendes:

- `@type` zum Angeben des Typs der Funktion (`Telemetry`, `Property` oder `Command`).  In einigen Fällen enthält der Typ einen semantischen Typ, damit IoT Central einige Annahmen darüber treffen kann, wie der Wert zu behandeln ist.
- `name` für den Telemetriewert.
- `schema` zum Angeben des Datentyps der Telemetrie oder Eigenschaft. Dieser Wert kann ein primitiver Typ sein, z. B. „double“, „integer“, „boolean“ oder „string“. Komplexe Objekttypen und Zuordnungen werden ebenfalls unterstützt.

Über optionale Felder, z. B. Anzeigename und Beschreibung, können Sie der Schnittstelle und den Funktionen weitere Details hinzufügen.

## <a name="properties"></a>Eigenschaften

Standardmäßig sind Eigenschaften schreibgeschützt. Schreibgeschützte Eigenschaften bedeuten, dass das Gerät Aktualisierungen der Eigenschaftswerte an Ihre IoT Central-Anwendung sendet. Ihre IoT Central-Anwendung kann den Wert einer schreibgeschützten Eigenschaft nicht festlegen.

Sie können eine Eigenschaft auch als beschreibbar für eine Schnittstelle markieren. Ein Gerät kann eine Aktualisierung einer beschreibbaren Eigenschaft von Ihrer IoT Central-Anwendung empfangen sowie Aktualisierungen der Eigenschaftswerte an Ihre Anwendung senden.

Geräte müssen nicht verbunden sein, um Eigenschaftswerte festzulegen. Die aktualisierten Werte werden übertragen, wenn das Gerät das nächste Mal mit der Anwendung verbunden wird. Dieses Verhalten gilt sowohl für schreibgeschützte als auch für beschreibbare Eigenschaften.

Verwenden Sie keine Eigenschaften, um Telemetriedaten von Ihrem Gerät zu senden. Beispielsweise bedeutet die schreibgeschützte `temperatureSetting=80`-Eigenschaft, dass die Gerätetemperatur auf 80 festgelegt wurde und das Gerät versucht, diese Temperatur zu erreichen oder zu halten.

Bei beschreibbaren Eigenschaften gibt die Geräteanwendung den Statuscode, die Version und die Beschreibung eines gewünschten Zustands zurück, um anzugeben, ob der Eigenschaftswert empfangen und angewandt wurde.

## <a name="telemetry"></a>Telemetrie

Mit IoT Central können Sie Telemetrie auf Dashboards und in Diagrammen anzeigen und Regeln verwenden, um Aktionen auszulösen, wenn Schwellenwerte erreicht werden. IoT Central verwendet die Informationen im Gerätemodell wie Datentypen, Einheiten und Anzeigenamen, um zu bestimmen, wie Telemetriewerte angezeigt werden sollen.

Sie können die Datenexportfunktion von IoT Central nutzen, um Telemetrie an andere Ziele wie Azure Storage oder Event Hubs zu streamen.

## <a name="commands"></a>Befehle

Ein Befehl muss standardmäßig innerhalb von 30 Sekunden ausgeführt werden, und das Gerät muss beim Eingehen des Befehls verbunden sein. Wenn das Gerät nicht rechtzeitig antwortet oder nicht verbunden ist, wird der Befehl nicht ausgeführt.

Befehle können Anforderungsparameter aufweisen und eine Antwort zurückgeben.

### <a name="offline-commands"></a>Offlinebefehle

Wenn ein Gerät zurzeit offline ist, können Sie Warteschlangenbefehle auswählen, indem Sie in der Gerätevorlage die Option **Warteschlange (falls offline)** für einen Befehl aktivieren.

Offlinebefehle sind unidirektionale Benachrichtigungen aus Ihrer Lösung an das Gerät. Offlinebefehle können Anforderungsparameter aufweisen, geben aber keine Antwort zurück.

> [!NOTE]
> Diese Option steht nur in der IoT Central-Webbenutzeroberfläche zur Verfügung. Diese Einstellung ist nicht enthalten, wenn Sie ein Modell oder eine Schnittstelle aus der Gerätevorlage exportieren.

## <a name="cloud-properties"></a>Cloudeigenschaften

Cloudeigenschaften sind Teil der Gerätevorlage, aber nicht des Gerätemodells. Mit Cloudeigenschaften kann der Lösungsentwickler Gerätemetadaten angeben, die in der IoT Central-Anwendung gespeichert werden sollen. Cloudeigenschaften haben keinen Einfluss auf den Code, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt.

Ein Lösungsentwickler kann neben den Geräteeigenschaften auch Cloudeigenschaften zu Dashboards und Ansichten hinzufügen, um einen Bediener in die Lage zu versetzen, die mit der Anwendung verbundenen Geräte zu verwalten. Ein Lösungsentwickler kann Cloudeigenschaften auch als Teil einer Regeldefinition verwenden, damit ein Schwellenwert von einem Bediener bearbeitet werden kann.

## <a name="customizations"></a>Anpassungen

Anpassungen sind Teil der Gerätevorlage, jedoch nicht des Gerätemodells. Durch Anpassungen kann der Lösungsentwickler einige der Definitionen im Gerätemodell optimieren oder überschreiben. Beispielsweise kann ein Lösungsentwickler den Anzeigenamen für einen Telemetrietyp oder eine Eigenschaft ändern. Ein Lösungsentwickler kann auch Anpassungen verwenden, um eine Überprüfung hinzuzufügen, z. B. eine minimale oder maximale Länge für eine Geräteeigenschaft in Form einer Zeichenfolge.

Anpassungen wirken sich ggf. auf den Code aus, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt. Eine Anpassung kann beispielsweise minimale und maximale Zeichenfolgenlängen oder minimale und maximale numerische Werte für Telemetrie festlegen.

## <a name="views"></a>Ansichten

Ein Lösungsentwickler erstellt Ansichten, mit denen Bediener angeschlossene Geräte überwachen und verwalten können. Ansichten sind Teil der Gerätevorlage, sodass eine Ansicht einem bestimmten Gerätetyp zugeordnet ist. Eine Ansicht kann Folgendes enthalten:

- Diagramme zum Zeichnen von Telemetrie
- Kacheln zum Anzeigen schreibgeschützter Geräteeigenschaften
- Kacheln, damit der Bediener beschreibbare Geräteeigenschaften bearbeiten kann
- Kacheln, damit der Bediener Cloudeigenschaften bearbeiten kann
- Kacheln, damit der Bediener Befehle aufrufen kann, darunter solche, die eine Nutzlast erwarten
- Kacheln zum Anzeigen von Beschriftungen, Bildern oder Markdowntext

Die Telemetrie, Eigenschaften und Befehle, die Sie einer Ansicht hinzufügen können, werden durch das Gerätemodell, die Cloudeigenschaften und benutzerdefinierte Anpassungen in der Gerätevorlage bestimmt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich als Geräteentwickler über Gerätevorlagen informiert haben, sollten Sie als Nächstes den Artikel [Telemetrie-, Eigenschaften- und Befehlsnutzlasten](./concepts-telemetry-properties-commands.md) lesen, um mehr über die Daten zu erfahren, die ein Gerät mit IoT Central austauscht.

Wir empfehlen Ihnen als Nächstes die Lektüre von [Definieren eines neuen IoT-Gerätetyps in Ihrer Azure IoT Central-Anwendung](./howto-set-up-template.md), um mehr darüber zu erfahren, wie Sie eine Gerätevorlage erstellen können.
