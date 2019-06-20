---
title: Grundlegendes zu Digital Twins-Objektmodellen und zum Raumintelligenzgraphen | Microsoft-Dokumentation
description: Modellieren von Beziehungen zwischen Personen, Orten und Geräten mithilfe von Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e7efe1a8632643e2a299b6c9a1b1407414deee4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60925812"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Grundlegendes zu Digital Twins-Objektmodellen und zum Raumintelligenzgraphen

Azure Digital Twins ist ein Azure IoT-Dienst, der umfassende virtuelle Darstellungen von physischen Umgebungen und zugehörigen Geräten, Sensoren und Personen unterstützt. Er verbessert die Entwicklungsumgebung, indem er domänenspezifische Konzepte in hilfreichen Modellen organisiert. Die Modelle werden anschließend innerhalb eines Raumintelligenzgraphen platziert. In solchen Konzepten werden die Beziehungen und Interaktionen zwischen Menschen, Räumen und Geräten naturgetreu modelliert.

In Digital Twins-Objektmodellen werden domänenspezifische Konzepte, Kategorien und Eigenschaften beschrieben. Modelle werden von Benutzern vordefiniert, die die Lösung an ihre speziellen Anforderungen anpassen möchten. Zusammen bilden diese vordefinierten Digital Twins-Objektmodelle eine _Ontologie_. In der Ontologie eines intelligenten Gebäudes werden Regionen, Orte, Etagen, Büros, Zonen, Konferenzräume und Konzentrationsräume beschrieben. In der Ontologie eines Energienetzes werden verschiedene Kraftwerke, Umspannwerke, Energieressourcen und Kunden beschrieben. Mit Digital Twins-Objektmodellen und Ontologien lassen sich unterschiedliche Szenarien und Anforderungen spezifisch anpassen.

Mit formulierten Digital Twins-Objektmodellen und formulierter Ontologie kann ein _Raumgraph_ bestückt werden. Raumgraphen sind virtuelle Darstellungen der vielen Beziehungen zwischen Räumen, Geräten und Personen, die für eine IoT-Lösung relevant sind. In diesem Diagramm sehen Sie ein Beispiel für einen Raumgraphen, für den die Ontologie eines intelligenten Gebäudes verwendet wurde.

![Digital Twins-Raumgraph für ein Gebäude][1]

<a id="model"></a>

In dem Raumgraph werden Räume, Geräte, Sensoren und Benutzer zusammengeführt. Die einzelnen Elemente sind auf eine Weise verknüpft, die die reale Welt im Modell darstellt. In diesem Beispiel weist Ort 43 vier Etagen auf, die jeweils viele verschiedene Bereiche umfassen. Benutzer sind ihren Arbeitsstationen zugeordnet und erhalten Zugriff auf Teile des Graphen. Ein Administrator besitzt die Berechtigungen zum Durchführen von Änderungen am Raumgraphen, während ein Besucher nur Berechtigungen zum Anzeigen bestimmter Gebäudedaten besitzt.

## <a name="digital-twins-object-models"></a>Digitale Twins-Objektmodelle

Für Digital Twins-Objektmodelle werden diese Objekthauptkategorien unterstützt:

- **Räume** sind virtuelle oder physische Orte, z.B. `Tenant`, `Customer`, `Region` und `Venue`.
- **Geräte** sind virtuelle oder physische Geräte, z.B. `AwesomeCompany Device` und `Raspberry Pi 3`.
- **Sensoren** sind Objekte, die Ereignisse erkennen, z.B. `AwesomeCompany Temperature Sensor` und `AwesomeCompany Presence Sensor`.
- **Benutzer** kennzeichnen Personen und deren Merkmale.

Weitere Objektkategorien sind:

- **Ressourcen** sind mit einem Raum verknüpft und entsprechen üblicherweise Azure-Ressourcen, die von Objekten im Raumgraphen verwendet werden, z.B. `IoTHub`.
- **Blobs** sind Objekten zugeordnet (z.B. Räumen, Geräten, Sensoren und Benutzern). Sie werden als Dateien mit MIME-Typ und Metadaten verwendet, z.B. `maps`, `pictures` und `manuals`.
- **Erweiterte Typen** sind erweiterbare Enumerationen, mit denen Entitäten bestimmte Merkmale hinzugefügt werden, z.B. `SpaceType` und `SpaceSubtype`.
- **Ontologien** entsprechen einem Satz erweiterter Typen, z.B. `Default`, `Building`, `BACnet` und `EnergyGrid`.
- **Eigenschaftenschlüssel und -werte** sind benutzerdefinierte Merkmale von Räumen, Geräten, Sensoren und Benutzern. Sie können zusammen mit integrierten Merkmalen verwendet werden, z.B. `DeltaProcessingRefreshTime` als Schlüssel und `10` als Wert.
- **Rollen** sind Sätze von Berechtigungen, die Benutzern und Geräten im Raumgraphen zugeordnet sind, z. B. `Space Administrator`, `User Administrator` und `Device Administrator`.
- **Rollenzuweisungen** sind die Zuordnung zwischen einer Rolle und ein Objekt im Raumgraphen. Beispielsweise kann einem Benutzer oder einem Dienstprinzipal die Berechtigung zum Verwalten eines Raums im Raumgraphen erteilt werden.
- **Sicherheitsschlüsselspeicher** stellen die Sicherheitsschlüssel für alle Geräte in der Hierarchie unter einem bestimmten Raumobjekt bereit, um dem Gerät die sichere Kommunikation mit Digital Twins zu ermöglichen.
- **Benutzerdefinierte Funktionen** (User-Defined Functions, UDFs) ermöglichen die anpassbare Verarbeitung von Sensortelemetriedaten im Raumgraphen. Eine benutzerdefinierte Funktion kann beispielsweise folgende Aktionen ausführen:
  - Festlegen eines Sensorwerts.
  - Ausführen benutzerdefinierter Logik anhand von Sensormesswerten und Festlegen der Ausgabe für einen Raum.
  - Anfügen von Metadaten an einen Raum.
  - Senden von Benachrichtigungen, wenn vordefinierte Bedingungen erfüllt sind. Derzeit können benutzerdefinierte Funktionen in JavaScript geschrieben werden.
- **Matcher** (Abgleicher) sind Objekte, die festlegen, welche benutzerdefinierten Funktionen für eine bestimmte Telemetrienachricht ausgeführt werden sollen.
- **Endpunkte** sind die Orte, an denen Telemetrienachrichten und Digital Twins-Ereignisse weitergeleitet werden können, z.B. `Event Hub`, `Service Bus` und `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Raumintelligenzgraph

Ein Raumgraph ist ein hierarchischer Graph aus Räumen, Geräten und Personen, die im Digital Twins-Objektmodell definiert sind. Für einen Raumgraphen werden Vererbung, Filterung, Durchlaufen, Skalierbarkeit und Erweiterbarkeit unterstützt. Sie können Ihren Raumgraphen über eine Sammlung von REST-APIs verwalten und nutzen.

Wenn Sie einen Digital Twins-Dienst in Ihrem Abonnement bereitstellen, fungieren Sie als globaler Administrator des Stammknotens. In diesem Fall erhalten Sie automatisch Vollzugriff auf die gesamte Struktur. Stellen Sie Räume im Graphen über die Räume-API bereit. Stellen Sie Dienste über die Geräte-API und Sensoren über die Sensor-API bereit. Außerdem stehen [Open Source-Tools](https://github.com/Azure-Samples/digital-twins-samples-csharp) zur Verfügung, um den Graphen in großem Umfang bereitzustellen.

**Vererbung im Graphen**. Die Vererbung bezieht sich auf die Berechtigungen und Eigenschaften, die von einem übergeordneten Knoten an alle unter ihm befindlichen Knoten weitergegeben werden. Ist beispielsweise einem Benutzer in einem bestimmten Knoten eine Rolle zugewiesen, besitzt der Benutzer die Berechtigungen dieser Rolle für den jeweiligen Knoten und für jeden darunter befindlichen Knoten. Alle Eigenschaftenschlüssel und erweiterten Typen, die für einen bestimmten Knoten definiert sind, werden an alle Knoten unterhalb dieses Knotens vererbt.

**Filterung im Graphen**. Mithilfe der Filterung werden Anforderungsergebnisse eingegrenzt. Sie können nach IDs, Namen, Typen, Untertypen, übergeordnetem Bereich und zugehörigen Räumen filtern. Außerdem können Sie nach Sensordatentypen, Eigenschaftenschlüsseln und Werten, *traverse*, *minLevel*, *maxLevel* und anderen OData-Filterparametern filtern.

**Durchlaufen des Graphen**. Sie können den Raumgraphen in Tiefe und Breite durchlaufen. Hinsichtlich der Tiefe kann der Graph über die Parameter *traverse*, *minLevel* und *maxLevel* von oben nach unten oder von unten nach oben durchlaufen werden. Hinsichtlich der Breite durchlaufen Sie den Graphen, um gleichgeordnete Knoten abzurufen, die direkt mit einem übergeordneten Raum oder einem ihrer Nachfolgerelemente verknüpft sind. Wenn Sie ein Objekt abfragen, können Sie alle Objekte ermitteln, die zu diesem Objekt in Beziehung stehen, indem Sie den *includes*-Parameter der GET-APIs verwenden.

**Skalierbarkeit des Graphen**. Digital Twins garantiert die Skalierbarkeit von Graphen, sodass Ihre tatsächlichen Workloads verarbeitet werden können. Digital Twins kann verwendet werden, um große Portfolios von Immobilien, Infrastruktur, Geräten, Sensoren, Telemetrie usw. darzustellen.

**Erweiterbarkeit des Graphen**. Durch die Erweiterbarkeit können Sie die zugrunde liegenden Digital Twins-Objektmodelle durch neue Typen und Ontologien anpassen. Die Digital Twins-Daten können auch durch erweiterbare Eigenschaften und Werte ergänzt werden.

### <a name="spatial-intelligence-graph-management-apis"></a>Verwaltungs-APIs für Raumintelligenzgraph

Nach der Bereitstellung von Digital Twins über das [Azure-Portal](https://portal.azure.com) wird die [Swagger](https://swagger.io/tools/swagger-ui/)-URL der Verwaltungs-APIs automatisch generiert. Sie wird im Azure-Portal im Abschnitt **Übersicht** im folgenden Format angezeigt.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| NAME | Ersetzen durch |
| --- | --- |
| YOUR_INSTANCE_NAME | Den Namen Ihrer Digital Twins-Instanz |
| YOUR_LOCATION | Die Serverregion, in der Ihre Instanz gehostet wird |

 In dieser Abbildung wird das vollständige URL-Format angezeigt.

![Digital Twins-Verwaltungs-API im Portal][2]

Wenn Sie weitere Informationen zur Verwendung von Raumintelligenzgraphen wünschen, sehen Sie sich die Vorschau der Azure Digital Twins-Verwaltungs-APIs an.

> [!TIP]
> Es wird eine Swagger-Vorschau bereitgestellt, um den API-Funktionsumfang zu veranschaulichen.
> Diese Vorschau finden Sie unter [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Erfahren Sie mehr über das [Verwenden von Swagger](how-to-use-swagger.md).

Alle API-Aufrufe müssen mit [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) authentifiziert werden. Für die APIs gelten die [REST-API-Richtlinien von Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Für die meisten APIs, die Auflistungen zurückgeben, werden die [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData)-Systemabfrageoptionen unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Gerätekonnektivität und zum Senden von Telemetrienachrichten an Digital Twins finden Sie unter [Gerätekonnektivität und eingehende Telemetriedaten](concepts-device-ingress.md).

- Informationen über Einschränkungen und Drosselungen von Verwaltungs-APIs finden Sie unter [Diensteinschränkungen der öffentlichen Vorschauversion](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
