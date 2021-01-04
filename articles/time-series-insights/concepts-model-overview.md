---
title: Zeitreihenmodell – Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über das Zeitreihenmodell in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 0c201ce984a216a5cc62e221c0433f83a7eeabae
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021761"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Zeitreihenmodell in Azure Time Series Insights Gen2

Dieser Artikel beschreibt das Zeitreihenmodell sowie die zugehörigen Funktionen und erläutert, wie Sie Ihre eigenen Modelle in der Azure Time Series Insights Gen2-Umgebung erstellen und aktualisieren können.

> [!TIP]
>
> * Wechseln Sie zur Demoumgebung  [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples), um ein Livebeispiel für ein Zeitreihenmodell zu sehen.
> * Informieren Sie sich über das [Arbeiten mit dem Zeitreihenmodell](./how-to-edit-your-model.md) mithilfe des Azure Time Series Insights-Explorers.

## <a name="summary"></a>Zusammenfassung

Üblicherweise fehlt den von IoT-Geräten erfassten Daten Kontextinformationen, was es erschwert, Sensoren schnell zu finden und zu analysieren. Das Hauptargument für ein Zeitreihenmodell ist die Vereinfachung der Suche nach und Analyse von IoT- oder Zeitreihendaten. Dieses Ziel wird erreicht, indem das Modell die Zusammenstellung, Wartung und Anreicherung von Zeitreihendaten ermöglicht, um die Vorbereitung von Datasets für die Analyse zu unterstützen, die direkt vom Consumer eingesetzt werden können.

## <a name="scenario-contosos-new-smart-oven"></a>Szenario: Der neue intelligente Herd von Contoso

**Sehen Sie sich dieses fiktive Szenario eines intelligenten Herds von Contoso an.** In diesem Szenario nehmen wir an, dass jeder intelligente Herd von Contoso über fünf Temperatursensoren verfügt: einen für jede der vier Herdplatten und einen für den Backofen. Bis vor Kurzem hat jeder Contoso-Temperatursensor Daten individuell gesendet, gespeichert und visualisiert. Zur Überwachung der Küchengeräte verwendete Contoso für jeden einzelnen Sensor ein einfaches Diagramm.

Contoso war zwar mit der ursprünglichen Daten- und Visualisierungslösung zufrieden, allerdings wurden bald einige Einschränkungen offensichtlich:

* Kunden wollten wissen, wie heiß der Herd insgesamt wird, wenn die meisten Platten eingeschaltet sind. Contoso hatte einige Schwierigkeiten, die Bedingungen des Herds zu analysieren und eine einheitliche Antwort zu finden.
* Die Techniker von Contoso wollten sicherstellen, dass es nicht zu einem Leistungsabfall kommt, wenn alle Herdplatten gleichzeitig eingeschaltet sind. Es war gar nicht so einfach, die richtigen Querverbindungen zu ermitteln und herauszufinden, welche Temperatur- und Spannungssensoren einander zugeordnet sind und wie sich diese im Laden lokalisieren lassen.
* Das Qualitätssicherungsteam von Contoso wollte den Entwicklungsverlauf überwachen und die beiden Sensorversionen miteinander vergleichen. Herauszufinden, welche Daten zu welcher Sensorversion gehörten, war ebenfalls nicht leicht.

Ohne die Möglichkeit, ein übergreifendes Zeitreihenmodell für den intelligenten Herd zu strukturieren, zu organisieren und zu definieren, verwaltete jeder Sensor nicht lokalisierte, isolierte und wenig informative Datenpunkte. Da jedes Dataset unabhängig von den anderen vorlag, war es ziemlich kompliziert, aus diesen Datenpunkten umsetzbare Erkenntnisse zu ziehen.

Diese Einschränkungen machten deutlich, wie wichtig intelligente Tools zur Aggregierung und Visualisierung von Daten für den neuen Herd von Contoso waren:

* Die Datenvisualisierung ist nützlich, wenn Sie Daten in einer komfortablen Ansicht zuordnen und miteinander kombinieren können. Ein Beispiel hierfür ist die parallele Anzeige von Spannungs- und Temperatursensoren.
* Die Verwaltung mehrdimensionaler Daten für eine Vielzahl von Entitäten sowie von Funktionen für Vergleiche, Zoomen und Zeitbereiche kann ausgesprochen knifflig sein.

Ein **Zeitreihenmodell bietet eine praktische Lösung** für viele Szenarien in diesem fiktiven Beispiel:

[![Beispiel für intelligentes Herddiagramm des Zeitreihenmodells](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Das Zeitreihenmodell spielt eine wichtige Rolle bei Abfragen und Navigation, weil es Daten in Kontext bringt, indem es Vergleiche über Zeitbereiche hinweg und zwischen verschiedenen Arten von Sensoren und Geräten ermöglicht. (**A**)
* Der Kontext wird für Daten weiter konkretisiert, weil im Zeitreihenmodell gespeicherte Daten Berechnungen von Zeitreihenabfragen als Variablen beibehalten und diese zur Abfragezeit erneut verwenden.
* Das Zeitreihenmodell organisiert und aggregiert Daten, um Funktionen für Visualisierung und Verwaltung zu verbessern. (**B**)

### <a name="key-capabilities"></a>Wichtige Funktionen

Mit dem Ziel, die Verwaltung der Zeitreihenkontextualisierung einfach und mühelos zu gestalten, ermöglicht das Zeitreihenmodell die folgenden Funktionen in Azure Time Series Insights Gen2. Es ermöglicht Folgendes:

* Erstellen und Verwalten von Berechnungen oder Formeln auf Basis von Skalarfunktionen, Aggregieren von Vorgängen usw.
* Definieren von Beziehungen zwischen über- und untergeordneten Elementen, um Navigation, Suche und Verweise zu ermöglichen.
* Definieren von Eigenschaften, die den Instanzen zugeordnet sind – definiert als *Instanzfelder* –, und Verwenden dieser Eigenschaften zum Erstellen von Hierarchien.

### <a name="components"></a>Komponenten

Das Zeitreihenmodell besteht aus drei Kernkomponenten:

* [Zeitreihenmodellinstanzen](#time-series-model-instances)
* [Zeitreihenmodellhierarchien](#time-series-model-hierarchies)
* [Zeitreihenmodelltypen](#time-series-model-types)

Diese Komponenten werden kombiniert, um ein Zeitreihenmodell anzugeben und Ihre Daten zu organisieren.

[![Übersicht über Zeitreihenmodelldiagramm](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Ein Zeitreihenmodell kann über die Schnittstelle [Azure Time Series Insights-Explorer]() erstellt und verwaltet werden. Die Einstellungen eines Zeitreihenmodells können über die [Modelleinstellungs-API](/rest/api/time-series-insights/reference-model-apis) verwaltet werden.

## <a name="time-series-model-instances"></a>Zeitreihenmodellinstanzen

*Instanzen* von Zeitreihenmodellen sind virtuelle Darstellungen der Zeitreihe selbst.

In den meisten Fällen werden die Instanzen durch die **deviceId** oder die **assetId** eindeutig identifiziert, die als Zeitreihen-IDs gespeichert sind.

Instanzen sind beschreibende Informationen zugeordnet, die als *Instanzeigenschaften* bezeichnet werden – z. B. ID, Typ, Name, Beschreibung, Hierarchien und Instanzfelder einer Zeitreihe. Instanzeigenschaften enthalten mindestens Hierarchieinformationen.

*Instanzfelder* sind eine Sammlung beschreibender Informationen, die Werte für Hierarchieebenen sowie für Hersteller, Bediener usw. umfassen können.

Nachdem eine Ereignisquelle für die Azure Time Series Insights Gen2-Umgebung konfiguriert wurde, werden Instanzen in einem Zeitreihenmodell automatisch entdeckt und erstellt. Die Instanzen können mithilfe von Zeitreihenmodellabfragen über den Azure Time Series Insights-Explorer erstellt oder aktualisiert werden.

Die Demoumgebung [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) stellt zahlreiche Beispiele für Live-Instanzen bereit.

[![Beispiel für Zeitreihenmodellinstanz](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instanzeigenschaften

Instanzen werden durch **timeSeriesId**, **typeId**, **name**, **description**, **hierarchyIds** und **instanceFields** definiert. Jede Instanz wird nur einem *Typ* sowie einer oder mehreren *Hierarchien* zugeordnet.

| Eigenschaft | BESCHREIBUNG |
| --- | ---|
| timeSeriesId | Die eindeutige ID der Zeitreihe, der die Instanz zugeordnet ist. In den meisten Fällen werden die Instanzen durch eine Eigenschaft wie deviceId oder assetId eindeutig identifiziert. In einigen Fällen kann eine spezifischere zusammengesetzte ID verwendet werden, die bis zu drei Eigenschaften kombiniert. |
| typeId | Die eindeutige Zeichenfolgen-ID des Zeitreihenmodelltyps mit Beachtung von Groß-/Kleinschreibung, dem die Instanz zugeordnet ist. Standardmäßig werden alle entdeckten neuen Instanzen einem Standardtyp zugeordnet.
| name | Die **name**-Eigenschaft ist optional, und die Groß-/Kleinschreibung wird berücksichtigt. Wenn für **name** kein Wert verfügbar ist, wird die Eigenschaft standardmäßig auf **timeSeriesId** festgelegt. Auch wenn ein Name angegeben wurde, ist **timeSeriesId** weiterhin im [Well](./concepts-ux-panels.md#4-time-series-well) verfügbar. |
| description | Eine Textbeschreibung der Instanz. |
| hierarchyIds | Definiert, zu welchen Hierarchien die Instanz gehört. |
| instanceFields | Die Eigenschaften einer Instanz sowie aller statischen Daten, die eine Instanz definieren. Sie definieren Werte von Hierarchie- oder Nicht-Hierarchie-Eigenschaften, während sie gleichzeitig die Indizierung unterstützen, um Suchvorgänge auszuführen. |

> [!NOTE]
> Hierarchien werden mithilfe von Instanzfeldern erstellt. Für weitere Definitionen von Instanzeigenschaften können weitere **instanceFields** hinzugefügt werden.

Instanzen weisen die folgende JSON-Darstellung auf:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Informationen zur Unterstützung von CRUD-Vorgängen (Create, Read, Update, Delete) bei der Instanz-API finden Sie im Artikel [Datenabfragen](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) und in der [REST-Dokumentation für die Instanz-API](/rest/api/time-series-insights/reference-model-apis#instances-api).

## <a name="time-series-model-hierarchies"></a>Zeitreihenmodellhierarchien

*Hierarchien* von Zeitreihenmodellen organisieren Instanzen durch Angabe von Eigenschaftsnamen und Beziehungen.

Sie können in einer bestimmten Azure Time Series Insights Gen2-Umgebung mehrere Hierarchien konfigurieren. Eine Zeitreihenmodellinstanz kann einer einzelnen Hierarchie oder mehreren Hierarchien (m:n-Beziehung) zugeordnet werden.

Die [Demo „Contoso Wind Farm“](https://insights.timeseries.azure.com/preview/samples) zeigt eine standardmäßige Instanz- und Typhierarchie an.

[![Beispiel für Zeitreihenmodellhierarchie](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hierarchiedefinition

Hierarchien werden durch **id**, **name** und **source** der Hierarchie definiert.

| Eigenschaft | BESCHREIBUNG |
| ---| ---|
| id | Der eindeutige Bezeichner für die Hierarchie, der z. B. beim Definieren einer Instanz verwendet wird. |
| name | Eine Zeichenfolge zum Angeben eines Namens für die Hierarchie. |
| source | Gibt die Organisationshierarchie bzw. den Organisationspfad an – eine Anordnung der über- und untergeordneten Elemente einer Hierarchie, die von Benutzern erstellt werden soll. Die über- und untergeordneten Eigenschaften entsprechen Instanzfeldern. |

Hierarchien werden folgendermaßen in JSON dargestellt:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Im oben stehenden JSON-Beispiel gilt:

* `Location` definiert eine Hierarchie mit übergeordneten `states`-Elementen und untergeordneten `cities`-Elementen. Jedes `location`-Element kann mehrere `states`-Elemente enthalten, die wiederum mehrere `cities`-Elemente umfassen können.
* `ManufactureDate` definiert eine Hierarchie mit übergeordneten `year`-Elementen und untergeordneten `month`-Elementen. Jedes `ManufactureDate`-Element kann mehrere `years`-Elemente enthalten, die wiederum mehrere `months`-Elemente umfassen können.

> [!TIP]
> Informationen zur Unterstützung von CRUD-Vorgängen (Create, Read, Update, Delete) bei der Hierarchie-API finden Sie im Artikel [Datenabfragen](concepts-query-overview.md#time-series-model-query-tsm-q-apis) und in der [REST-Dokumentation für die Hierarchie-API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Beispiel für eine Hierarchie

Stellen Sie sich ein Beispiel vor, in dem die Hierarchie **H1** in ihrer **instanceFieldNames**-Definition die Elemente `building`, `floor` und `room` enthält.

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Mit den in der vorherigen Definition verwendeten Instanzfeldern und verschiedenen Zeitreihen ergeben sich die in der folgenden Tabelle gezeigten Hierarchieattribute und Werte:

| Time Series-ID | Instanzenfelder |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | Weder „building“ noch „floor“ noch „room“ wurden festgelegt. |

Die Zeitreihen **ID1** und **ID4** werden im [Azure Time Series Insights-Explorer](./concepts-ux-panels.md) als Teil der Hierarchie **H1** angezeigt, weil sie vollständig definierte und richtig angeordnete Parameter *building*, *floor* und *room* aufweisen.

Die anderen Zeitreihen sind unter *Unparented Instances* klassifiziert, weil sie der angegebenen Datenhierarchie nicht entsprechen.

## <a name="time-series-model-types"></a>Zeitreihenmodelltypen

Zeitreihenmodell *typen* helfen Ihnen beim Definieren von Variablen oder Formeln zur Durchführung von Berechnungen. Typen werden einer bestimmten-Instanz zugeordnet.

Ein Typ kann eine oder mehrere Variablen enthalten. Beispielsweise könnte eine Zeitreihenmodellinstanz den Typ *Temperatursensor* aufweisen, der aus den Variablen *durchschnittliche Temperatur*, *Mindesttemperatur* und *Höchsttemperatur* besteht.

Die Demoumgebung [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) visualisiert verschiedene Zeitreihenmodelltypen mit ihren jeweiligen Instanzen.

[![Beispiel für Zeitreihenmodelltyp](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Informationen zur Unterstützung von CRUD-Vorgängen (Create, Read, Update, Delete) bei der Typen-API finden Sie im Artikel [Datenabfragen](concepts-query-overview.md#time-series-model-query-tsm-q-apis) und in der [REST-Dokumentation für die Typen-API](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Typeigenschaften

Zeitreihenmodelltypen werden durch **id**, **name**, **description** und **variables** definiert.

| Eigenschaft | BESCHREIBUNG |
| ---| ---|
| id | Die eindeutige Zeichenfolgen-ID für den Typ mit Beachtung der Groß-/Kleinschreibung. |
| name | Eine Zeichenfolge zum Angeben eines Namens für den Typ. |
| description | Eine Zeichenfolgenbeschreibung für den Typ. |
| variables | Geben Variablen an, die dem Typ zugeordnet sind. |

Typen entsprechen dem folgenden JSON-Beispiel:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Zeitreihenmodelltypen können viele Variablen haben, die Formel- und Berechnungsregeln bei Ereignissen angeben. Weitere Informationen finden Sie unter [Definieren von Zeitreihenmodellvariablen](./concepts-variables.md)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Bearbeiten des Modells über APIs finden Sie in der Referenzdokumentation zum [Zeitreihenmodell](/rest/api/time-series-insights/reference-model-apis).

* Untersuchen Sie die Formeln und Berechnungen, die Sie mit [Zeitreihenmodellvariablen](./concepts-variables.md) erstellen können.

* Weitere Informationen zum [Abfragen von Daten](concepts-query-overview.md) in Azure Time Series Insights Gen2