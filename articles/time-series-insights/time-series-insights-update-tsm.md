---
title: Zeitreihenmodell in Azure Time Series Insights (Vorschauversion) | Microsoft-Dokumentation
description: Grundlegendes zum Zeitreihenmodell in Azure Time Series.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 3e6e8ae76c0ae6f688dd4a039b34c52af16b6e0f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244024"
---
# <a name="time-series-model"></a>Time Series-Modell

In diesem Artikel wird der Zeitreihenmodell-Teil von Azure Time Series Insights Preview beschrieben. Das Modell selbst wird besprochen, seine Funktionen und wie Sie am besten mit dem Erstellen und Aktualisieren Ihres eigenen Modells beginnen können.

Üblicherweise fehlt den von IoT-Geräten erfassten Daten Kontextinformationen, was es erschwert, Sensoren schnell zu finden und zu analysieren. Das Hauptargument für ein Zeitreihenmodell ist die Vereinfachung der Suche und Analyse von IoT-Daten. Es verwirklicht dieses Ziel, indem die Zusammenstellung, Wartung und Anreicherung von Zeitreihendaten ermöglicht wird, um bei der Vorbereitung für Consumer bereiter Datasets zu helfen.

Zeitreihenmodelle spielen eine wichtige Rolle bei Abfragen und der Navigation, da sie Entitäten von Geräten und Nicht-Geräten mit Kontext versehen. Daten, die im Zeitreihenmodell beibehalten werden, treiben Zeitreihenabfrageberechnungen an, indem die in ihnen gespeicherten Formeln genutzt werden.

[![Übersicht über Zeitreihenmodelle](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Wichtige Funktionen

Mit dem Ziel, die Verwaltung der Zeitreihenkontextualisierung einfach und mühelos zu gestalten, ermöglicht das Zeitreihenmodell die folgenden Funktionen in Time Series Insights Preview. Es ermöglicht Folgendes:

* Erstellen und Verwalten von Berechnungen oder Formeln, Transformieren von Daten mit Skalarfunktionen und Aggregieren von Vorgängen usw.
* Definieren von Beziehungen zwischen über- und untergeordneten Elementen, um Navigation und Referenz zu ermöglichen, sowie Bereitstellung von Kontext für die Zeitreihentelemetrie.
* Definieren von Eigenschaften, die dem Instanzenteil von *Instanzenfeldern* zugeordnet sind, und deren Verwendung zum Erstellen von Hierarchien.

## <a name="entity-components"></a>Entitätskomponenten

Zeitreihenmodelle bestehen aus drei Hauptkomponenten:

* <a href="#time-series-model-types">Zeitreihenmodelltypen</a>
* <a href="#time-series-model-hierarchies">Zeitreihenmodellhierarchien</a>
* <a href="#time-series-model-instances">Zeitreihenmodellinstanzen</a>

Diese Komponenten werden zu einem Zeitreihenmodell kombiniert, um Ihre Azure Time Series Insights-Daten zu organisieren.

## <a name="time-series-model-types"></a>Zeitreihenmodelltypen

Zeitreihenmodell*typen* helfen Ihnen beim Definieren von Variablen oder Formeln zur Durchführung von Berechnungen. Die Typen sind mit einer bestimmten Time Series Insights-Instanz verknüpft. Ein Typ kann eine oder mehrere Variablen enthalten. Z. B. könnte eine Time Series Insights-Instanz vom Typ *Temperatursensor* sein, der aus den Variablen besteht *durchschn Temperatur*, *Min Temperatur* und *Max Temperatur* besteht. Wir erstellen einen Standardtyp, wenn der Fluss der Daten in Time Series Insights beginnt. Der Standardtyp kann aus den Modelleinstellungen abgerufen und aktualisiert werden. Standardtypen besitzen eine Variable, die die Anzahl der Ereignisse zählt.

### <a name="time-series-model-type-json-example"></a>JSON-Beispiel für Zeitreihenmodelltyp

Beispiel:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Weitere Informationen zu Zeitreihenmodelltypen finden Sie in der [Referenzdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Variables

Time Series Insights-Typen verfügen über Variablen, die benannte Berechnungen mit Werten aus den Ereignissen darstellen. Time Series Insights-Variablendefinitionen enthalten Formeln und Berechnungsregeln. Variablendefinitionen umfassen *Art*, *Wert*, *Filter*, *Verringerung* (reduction) und *Grenzen*. Variablen werden in der Typdefinition im Zeitreihenmodell gespeichert und können inline über Abfrage-APIs bereitgestellt werden, um die gespeicherte Definition zu überschreiben.

Die folgende Matrix funktioniert wie eine Legende für Variablendefinitionen:

[![Tabelle der Typvariablendefinitionen](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definition | BESCHREIBUNG |
| --- | ---|
| Variablenart |  Die Arten *Numerisch* und *Aggregiert* werden unterstützt. |
| Variablenfilter | Variablenfilter geben eine optionale Filterklausel an, um die Anzahl der Zeilen, die bei der Berechnung berücksichtigt werden, basierend auf Bedingungen einzuschränken. |
| Variablenwert | Variablenwerte werden und sollten in Berechnungen verwendet werden. Das entsprechende Feld, auf das sich der betreffende Datenpunkt beziehen muss. |
| Variablenaggregation | Die Aggregatfunktion der Variablen ermöglicht einen Teil der Berechnung. Time Series Insights unterstützt reguläre Aggregate (nämlich *min*, *max*, *avg*, *sum* und *count*). |

## <a name="time-series-model-hierarchies"></a>Zeitreihenmodellhierarchien

Hierarchien organisieren Instanzen durch Angabe von Eigenschaftsnamen und deren Beziehungen. Sie können eine einzelne Hierarchie oder mehrere Hierarchien haben. Sie müssen kein aktueller Bestandteil Ihrer Daten sein, aber jede Instanz sollte einer Hierarchie zugeordnet sein. Eine Zeitreihenmodellinstanz kann einer einzelnen Hierarchie oder mehreren Hierarchien zugeordnet werden.

Hierarchien werden durch *Hierarchie-ID*, *Name* und *Quelle* definiert. Hierarchien weisen einen Pfad auf, der die Hierarchie als Anordnung von oben nach unten und von über- zu untergeordnet darstellt, die Benutzer erstellen möchten. Die über-/untergeordneten Eigenschaften entsprechen *Instanzenfeldern*.

### <a name="time-series-model-hierarchy-json-example"></a>JSON-Beispiel für Zeitreihenmodellhierarchie

Beispiel:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Weitere Informationen zu Zeitreihenmodellhierarchien finden Sie in der [Referenzdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Verhalten der Hierarchiedefinition

Sehen Sie sich das folgende Beispiel an, bei dem die Hierarchie H1 *Gebäude* (building), *Stockwerk* (floor) und *Raum* (room) als Teil ihrer Definition enthält:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

In Abhängigkeit von den *Instanzenfeldern* werden die Hierarchieattribute und -werte wie in der folgenden Tabelle angezeigt:

| Time Series-ID | Instanzenfelder |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | Weder „building“ noch „floor“ noch „room“ wurde festgelegt. |

Im vorherigen Beispiel werden **ID1** und **ID4** als Teile der Hierarchie H1 im Azure Time Series Insights-Explorer angezeigt, und die restlichen Elemente werden unter *Unparented Instances* (Instanzen ohne übergeordnete Elemente) klassifiziert, da sie nicht der angegebenen Datenhierarchie entsprechen.

## <a name="time-series-model-instances"></a>Zeitreihenmodellinstanzen

Instanzen sind die Zeitreihen selbst. In den meisten Fällen ist die *deviceId* oder die *assetId* der eindeutige Bezeichner des Objekts in der Umgebung. Instanzen sind beschreibende Informationen zugeordnet, die als Instanzeigenschaften bezeichnet werden. Instanzeigenschaften enthalten mindestens Hierarchieinformationen. Sie können auch nützliche, beschreibende Daten enthalten wie Hersteller, Bediener oder letztes Wartungsdatum.

Instanzen werden durch *typeId*, *timeSeriesId*, *name*, *description*, *hierarchyIds* und *instanceFields* definiert. Jede Instanz wird nur einem *Typ* zugeordnet sowie einer oder mehreren Hierarchien. Instanzen erben alle Eigenschaften von Hierarchien, und es können zusätzliche *instanceFields* zur weiteren Definition der Instanzeigenschaft hinzugefügt werden.

*instanceFields* sind Eigenschaften einer Instanz sowie aller statischen Daten, die eine Instanz definieren. Sie definieren Werte von Hierarchie- oder Nicht-Hierarchie-Eigenschaften, während sie gleichzeitig die Indizierung unterstützen, um Suchvorgänge auszuführen.

Die *name*-Eigenschaft ist optional, und die Groß-/Kleinschreibung wird berücksichtigt. Wenn *name* nicht verfügbar ist, wird standardmäßig die Time Series-ID verwendet. Wenn *name* angegeben ist, ist die Time Series-ID weiterhin im Ursprung (dem Raster unterhalb der Diagramme im Explorer) verfügbar.

### <a name="time-series-model-instance-json-example"></a>JSON-Beispiel für Zeitreihenmodellinstanz

Beispiel:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Weitere Informationen zu Zeitreihenmodellinstanzen finden Sie in der [Referenzdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Beispiel für Zeitreihenmodelleinstellungen

Beispiel:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Weitere Informationen zu Zeitreihenmodelleinstellungen finden Sie in der [Referenzdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Nächste Schritte

- Informationen finden Sie unter [Speicherung und Datenerfassung in Azure Time Series Insights Preview](./time-series-insights-update-storage-ingress.md).

- Erfahren Sie mehr über das neue [Zeitreihenmodell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).