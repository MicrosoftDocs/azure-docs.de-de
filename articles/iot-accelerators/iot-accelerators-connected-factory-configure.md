---
title: 'Konfigurieren der Connected Factory-Topologie: Azure | Microsoft-Dokumentation'
description: In diesem Artikel wird die Konfiguration des Solution Accelerators für Connected Factory einschließlich der Topologie beschrieben.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002237"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurieren des Solution Accelerators für Connected Factory

> [!IMPORTANT]
> Während wir diesen Artikel aktualisieren, können Sie unter [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) den Inhalt auf dem neuesten Stand lesen.

Der Solution Accelerator für Connected Factory zeigt ein simuliertes Dashboard für das fiktive Unternehmen Contoso. Dieses Unternehmen verfügt weltweit über zahlreiche Fabrikanlagen.

In diesem Artikel wird anhand des fiktiven Unternehmens Contoso beschrieben, wie die Topologie einer Connected Factory-Lösung konfiguriert wird.

## <a name="simulated-factories-configuration"></a>Simulierte Konfiguration von Fabrikanlagen

Jede der Fabrikanlagen von Contoso verfügt über Produktionslinien mit jeweils drei Stationen. Jede dieser Stationen ist ein OPC UA-Server mit einer bestimmten Funktion:

* Montagestation
* Teststation
* Verpackungsstation

Diese OPC UA-Server umfassen OPC UA-Knoten, und der [OPC-Herausgeber](overview-opc-publisher.md) sendet die Werte dieser Knoten an die Connected Factory-Lösung. Dies umfasst Folgendes:

* Aktueller Betriebsstatus, beispielsweise der aktuelle Energieverbrauch.
* Produktionsinformationen wie z.B. die Anzahl der produzierten Produkte.

Mit dem Dashboard können Sie für die Contoso-Fabriktopologie einen Drilldown von einer globalen Ansicht bis hin zu einer Ansicht auf Stationsebene durchführen. Das Connected Factory-Dashboard bietet folgende Möglichkeiten:

* Visualisierung der OEE- und KPI-Zahlen für jede Ebene in der Topologie
* Visualisierung der aktuellen Werte der OPC UA-Knoten in den Stationen
* Aggregation der OEE- und KPI-Zahlen von der Stationsebene bis zur globalen Ebene
* Visualisierung von Warnungen und Aktionen, die beim Erreichen bestimmter Schwellenwerte ausgegeben bzw. ausgeführt werden sollen

## <a name="connected-factory-topology"></a>Connected Factory-Topologie

Die Topologie von Fabrikanlagen, Produktionslinien und Stationen ist hierarchisch aufgebaut:

* Die globale Ebene umfasst Fabriken als untergeordnete Knoten.
* Die Fabriken umfassen Produktionslinien als untergeordnete Knoten.
* Die Produktionslinien umfassen Stationen als untergeordnete Knoten.
* Die Stationen (OPC UA-Server) umfassen OPC UA-Knoten als untergeordnete Elemente.

Jeder Knoten in der Topologie weist einen gemeinsamen Eigenschaftensatz auf, der Folgendes definiert:

* Einen eindeutigen Bezeichner für den Topologieknoten
* Einen Namen
* Eine Beschreibung
* Ein Bild
* Die untergeordneten Elemente des Topologieknotens
* Mindest-, Ziel- und Höchstwerte für OEE- und PKI-Zahlen sowie die auszuführenden Warnungsaktionen

## <a name="topology-configuration-file"></a>Datei für die Topologiekonfiguration

Um die im vorherigen Abschnitt aufgeführten Eigenschaften zu konfigurieren, verwendet die Connected Factory-Lösung eine Konfigurationsdatei namens [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Sie finden diese Datei im Ordner `WebApp/Contoso/Topology` des Quellcodes der Lösung.

Der folgende Ausschnitt zeigt die Gliederung der Konfigurationsdatei `ContosoTopologyDescription.json`:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Die gemeinsamen Eigenschaften von `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>` und `<station_configuration>` lauten:

* **Name** (Zeichenfolgentyp)

  Definiert einen beschreibenden Namen. Dieser sollte nur aus einem Wort bestehen und wird im Dashboard angezeigt.

* **Description** (Zeichenfolgentyp)

  Beschreibt den Topologieknoten ausführlicher.

* **Image** (Zeichenfolgentyp)

  Der Pfad zu einem Bild in der WebApp-Lösung, das angezeigt wird, wenn Informationen zum Topologieknoten im Dashboard angezeigt werden.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (Typ: `<performance_definition>`)

  Diese Eigenschaften definieren Mindest-, Ziel- und Höchstwerte der Betriebskennzahlen zum Generieren von Warnungen. Darüber hinaus werden anhand dieser Eigenschaften die Aktionen definiert, die beim Auftreten einer Warnung ausgeführt werden sollen.

Die Elemente `<factory_configuration>` und `<production_line_configuration>` umfassen eine Eigenschaft:

* **Guid** (Zeichenfolgentyp)

  Hiermit wird der Topologieknoten eindeutig identifiziert.

`<factory_configuration>` verfügt über eine Eigenschaft:

* **Location** (Typ: `<location_definition>`)

  Gibt an, wo sich die Fabrikanlage befindet.

`<station_configuration>` umfasst diese Eigenschaften:

* **OpcUri** (Zeichenfolgentyp)

  Diese Eigenschaft muss auf den OPC UA-Anwendungs-URI des OPC UA-Servers festgelegt werden.
  Weil diese Eigenschaft gemäß OPC UA-Spezifikation global eindeutig sein muss, wird sie zum Identifizieren des Stationstopologieknotens verwendet.

* **OpcNodes**, diese geben ein Array aus OPC UA-Knoten an (Typ: `<opc_node_description>`).

`<location_definition>` umfasst diese Eigenschaften:

* **City** (Zeichenfolgentyp)

  Name der Stadt, die dem Standort am nächsten gelegen ist.

* **Country** (Zeichenfolgentyp)

  Land/Region des Standorts.

* **Latitude** (Double-Typ)

  Breitengrad des Standorts.

* **Longitude** (Double-Typ)

  Längengrad des Standorts.

`<performance_definition>` umfasst diese Eigenschaften:

* **Minimum** (Double-Typ)

  Unterer Schwellenwert, der erreicht werden kann. Wenn der aktuelle Wert unter diesem Schwellenwert liegt, wird eine Warnung generiert.

* **Target** (Double-Typ)

  Idealerweise erreichter Zielwert.

* **Maximum** (Double-Typ)

  Oberer Schwellenwert, der erreicht werden kann. Wenn der aktuelle Wert über diesem Schwellenwert liegt, wird eine Warnung generiert.

* **MinimumAlertActions** (Typ: `<alert_action>`)

  Definiert den Satz an Aktionen, die als Reaktion auf eine Mindestwertwarnung ergriffen werden können.

* **MaximumAlertActions** (Typ: `<alert_action>`)

  Definiert den Satz an Aktionen, die als Reaktion auf eine Höchstwertwarnung ergriffen werden können.

`<alert_action`> umfasst diese Eigenschaften:

* **Type** (Zeichenfolgentyp)

  Der Typ der Warnungsaktion. Die folgenden Typen werden unterstützt:

  * **AcknowledgeAlert**: Die Warnung soll in den bestätigten Status wechseln.
  * **CloseAlert**: Alle älteren Warnungen desselben Typs sollen nicht länger im Dashboard angezeigt werden.
  * **CallOpcMethod**: Es soll eine OPC UA-Methode aufgerufen werden.
  * **OpenWebPage**: Es soll ein Browserfenster mit zusätzlichen Kontextinformationen geöffnet werden.

* **Description** (Zeichenfolgentyp)

  Beschreibung der Aktion, die im Dashboard angezeigt wird.

* **Parameter** (Zeichenfolgentyp)

  Parameter, die zum Ausführen der Aktion erforderlich sind. Der Wert richtet sich nach dem Aktionstyp.

  * **AcknowledgeAlert**: Keine Parameter erforderlich.
  * **CloseAlert**: Keine Parameter erforderlich.
  * **CallOpcMethod**: Knoteninformationen und Parameter der OPC UA-Methode, die in folgendem Format aufgerufen wird: NodeId des übergeordneten Knotens, NodeId der aufzurufenden Methode, URI des OPC UA-Servers.
  * **OpenWebPage**: URL, die im Browserfenster angezeigt werden soll.

`<opc_node_description>` enthält Informationen zu den OPC UA-Knoten in einer Station (OPC UA-Server). Knoten, die keine vorhandenen OPC UA-Knoten repräsentieren, sondern als Speicher in der Berechnungslogik von Connected Factory verwendet werden, sind ebenfalls zulässig. Es stehen folgende Eigenschaften zur Verfügung:

* **NodeId** (Zeichenfolgentyp)

  Adresse des OPC UA-Knotens im Adressraum der Station (des OPC UA-Servers). Die Syntax muss der für eine NodeId in der OPC UA-Spezifikation entsprechen.

* **SymbolicName** (Zeichenfolgentyp)

  Name, der im Dashboard sichtbar ist, wenn der Wert dieses OPC UA-Knotens angezeigt wird.

* **Relevance** (Array vom Zeichenfolgentyp)

  Gibt an, für welche Berechnung von OEE- oder KPI-Zahlen der OPC UA-Knotenwert relevant ist. Jedes Arrayelement kann einen der folgenden Werte annehmen:

  * **OeeAvailability_Running**: Der Wert ist für die Berechnung der OEE-Verfügbarkeit relevant.
  * **OeeAvailability_Fault**: Der Wert ist für die Berechnung der OEE-Verfügbarkeit relevant.
  * **OeePerformance_Ideal**: Der Wert ist für die Berechnung der OEE-Leistung relevant, es handelt sich typischerweise um einen konstanten Wert.
  * **OeePerformance_Actual**: Der Wert ist für die Berechnung der OEE-Leistung relevant.
  * **OeeQuality_Good**: Der Wert ist für die Berechnung der OEE-Qualität relevant.
  * **OeeQuality_Bad**: Der Wert ist für die Berechnung der OEE-Qualität relevant.
  * **Kpi1**: Der Wert ist für die KPI1-Berechnung relevant.
  * **Kpi2**: Der Wert ist für die KPI2-Berechnung relevant.

* **OpCode** (Zeichenfolgentyp)

  Gibt an, wie der Wert des OPC UA-Knotens in Time Series Insights-Abfragen und in OEE/KPI-Berechnungen verarbeitet wird. Jede Time Series Insights-Abfrage gilt für einen bestimmten Zeitraum, der als Parameter der Abfrage angegeben wird und ein Ergebnis liefert. Der OpCode steuert, wie das Ergebnis berechnet wird und kann folgende Werte annehmen:

  * **Diff**: Differenz zwischen dem letzten und dem ersten Wert innerhalb des Zeitraums.
  * **Avg**: Durchschnitt aller Werte im Zeitraum.
  * **Sum**: Summe aller Werte im Zeitraum.
  * **Last**: Wird derzeit nicht verwendet.
  * **Count**: Anzahl von Werten im Zeitraum.
  * **Max**: Höchstwert im Zeitraum.
  * **Min**: Mindestwert im Zeitraum.
  * **Const**: Das Ergebnis ist der durch die ConstValue-Eigenschaft angegebene Wert.
  * **SubMaxMin**: Differenz zwischen dem Höchst- und dem Mindestwert.
  * **Timespan**: Zeitraum.

* **Units** (Zeichenfolgentyp)

  Definiert eine Einheit des Werts zur Anzeige im Dashboard.

* **Visible** (boolescher Typ)

  Steuert, ob der Wert im Dashboard angezeigt werden soll.

* **ConstValue** (Double-Typ)

  Wenn **OpCode** auf **Const** festgelegt ist, dann gibt diese Eigenschaft den Wert des Knotens an.

* **Minimum** (Double-Typ)

  Wenn der aktuelle Wert unter diesen Wert fällt, wird eine Mindestwertwarnung generiert.

* **Maximum** (Double-Typ)

  Wenn der aktuelle Wert über diesen Wert ansteigt, wird eine Höchstwertwarnung generiert.

* **MinimumAlertActions** (Typ: `<alert_action>`)

  Definiert den Satz an Aktionen, die als Reaktion auf eine Mindestwertwarnung ergriffen werden können.

* **MaximumAlertActions** (Typ: `<alert_action>`)

  Definiert den Satz an Aktionen, die als Reaktion auf eine Höchstwertwarnung ergriffen werden können.

Auf Stationsebene werden auch **Simulation**-Objekte angezeigt. Diese Objekte dienen nur zum Konfigurieren der Connected Factory-Simulation und dürfen nicht zum Konfigurieren einer echten Topologie verwendet werden.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Verwendung der Konfigurationsdaten zur Laufzeit

Alle in der Konfigurationsdatei verwendeten Eigenschaften können je nach ihrer Verwendung in unterschiedliche Kategorien eingeteilt werden. Die Kategorien lauten wie folgt:

### <a name="visual-appearance"></a>Visuelle Darstellung

Eigenschaften in dieser Kategorie definieren die visuelle Darstellung des Connected Factory-Dashboards. Beispiele:

* Name
* Beschreibung
* Image
* Standort
* Einheiten
* Sichtbar

### <a name="internal-topology-tree-addressing"></a>Adressierung der internen Topologiestruktur

Die WebApp verwaltet ein internes Datenwörterbuch mit Informationen zu allen Topologieknoten. Die Eigenschaften **Guid** und **OpcUri** werden als Schlüssel für den Zugriff auf dieses Wörterbuch verwendet und müssen eindeutig sein.

### <a name="oeekpi-computation"></a>OEE/KPI-Berechnung

Die OEE/KPI-Zahlen für die Connected Factory-Simulation werden parametrisiert durch:

* Die OPC UA-Knotenwerte, die in die Berechnung einbezogen werden sollen.
* Art der Berechnung der Zahl aus den Telemetriewerten.

Connected Factory verwendet die von [http://www.oeefoundation.org](http://www.oeefoundation.org) veröffentlichten OEE-Formeln.

OPC UA-Knotenobjekte in Stationen ermöglichen eine Kennzeichnung für die Verwendung in der OEE/KPI-Berechnung. Die Eigenschaft **Relevance** gibt an, für welche OEE/KPI-Zahl der OPC UA-Knoten verwendet werden soll. Die Eigenschaft **OpCode** definiert, wie der Wert in die Berechnung einbezogen wird.

### <a name="alert-handling"></a>Warnungsverarbeitung

Die Connected Factory-Lösung unterstützt einen einfachen Mechanismus zum Generieren von Warnungen, der auf Mindest- und Maximalschwellenwerten basiert. Es gibt verschiedene vordefinierte Aktionen, die Sie als Reaktion auf diese Warnungen konfigurieren können. Die folgenden Eigenschaften steuern diesen Mechanismus:

* Maximum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korrelation mit Telemetriedaten

Für bestimmte Vorgänge, z.B. zur Visualisierung des letzten Werts oder zum Erstellen von Time Series Insights-Abfragen, benötigt die WebApp ein Adressierungsschema für die erfassten Telemetriedaten. Die an die Connected Factory-Lösung gesendeten Telemetriedaten müssen außerdem in internen Datenstrukturen gespeichert werden. Die folgenden zwei Eigenschaften ermöglichen diese Vorgänge auf Ebene der Station (OPC UA-Server) und auf Ebene des OPC UA-Knotens:

* **OpcUri**

  Diese Eigenschaft sorgt für eine (global eindeutige) Identifizierung des OPC UA-Servers, von dem die Telemetriedaten stammen. In den erfassten Nachrichten wird diese Eigenschaft als **ApplicationUri** gesendet.

* **NodeId**

  Identifiziert den Knotenwert im OPC UA-Server. Das Format der Eigenschaft muss der OPC UA-Spezifikation entsprechen. In den erfassten Nachrichten wird diese Eigenschaft als **NodeId** gesendet.

Unter [What is OPC Publisher?](overview-opc-publisher.md) (Was ist der OPC-Herausgeber?) erhalten Sie weitere Informationen dazu, wie die Telemetriedaten in der Lösung „Connected Factory“ (Vernetzte Fabrik) erfasst werden.

## <a name="example-how-kpi1-is-calculated"></a>Beispiel: KPI1-Berechnung

Die Konfiguration in der Datei `ContosoTopologyDescription.json` steuert, wie OEE/KPI-Zahlen berechnet werden. Das folgende Beispiel zeigt, wie die Eigenschaften in dieser Datei die Berechnung von KPI1 steuern.

In der Connected Factory-Simulation wird KPI1 dazu verwendet, die Anzahl von Produkten zu messen, die in der letzten Stunde erfolgreich produziert wurden. Jede Station (OPC UA-Server) in der Connected Factory-Simulation umfasst einen OPC UA-Knoten (`NodeId: "ns=2;i=385"`), der die Telemetriedaten zum Berechnen dieser KPI liefert.

Die Konfiguration für diesen OPC UA-Knoten ähnelt der im folgenden Abschnitt:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Diese Konfiguration ermöglicht eine Abfrage der Telemetriewerte für diesen Knoten mithilfe von Time Series Insights. Mit der Time Series Insights-Abfrage werden folgende Werte abgerufen:

* Anzahl von Werten
* Mindestwert
* Höchstwert
* Durchschnitt aller Werte
* Summe aller Werte für alle eindeutigen **OpcUri**- (**ApplicationUri**) und **NodeId**-Paare in einem vorgegebenen Zeitraum

Ein Merkmal des **NumberOfManufactureredProducts**-Knotens ist, dass der zugehörige Wert ausschließlich ansteigt. Um die Anzahl von Produkten zu berechnen, die innerhalb des vorgegebenen Zeitraums hergestellt wurden, verwendet die Connected Factory-Simulation für **OpCode den Wert** **SubMaxMin**. Bei der Berechnung werden der Mindestwert zu Beginn des angegebenen Zeitraums und der Höchstwert am Ende des Zeitraums abgerufen.

**OpCode** konfiguriert die Berechnungslogik so, dass die Differenz zwischen Höchst- und Mindestwert als Ergebnis ausgegeben wird. Diese Ergebnisse werden anschließend von der niedrigsten bis zur (globalen) Stammebene akkumuliert und im Dashboard angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Als nächsten Schritt bietet sich an, zu lernen, wie Sie die [Lösung „Connected Factory“ (Vernetzte Fabrik) anpassen](iot-accelerators-connected-factory-customize.md).
