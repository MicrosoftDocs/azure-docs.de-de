---
title: 'Modellvariablen: Azure Time Series Insights Gen2 | Microsoft-Dokumentation'
description: Modellvariablen
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: f1c394bb1a568d59e0821b61e7acfcf8f25290f7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020758"
---
# <a name="time-series-model-variables"></a>Zeitreihenmodellvariablen

In diesem Artikel werden die Zeitreihenmodellvariablen beschrieben, die Formel- und Berechnungsregeln bei Ereignissen angeben.

Jede Variable kann eine der drei folgenden Arten aufweisen: *numerisch*, *kategorisch* und *aggregiert*.

* **Numerische** Variablen arbeiten mit kontinuierlichen numerischen Werten.
* **Kategorische** Variablen arbeiten mit einem definierten Satz diskreter Werte.
* Bei **aggregierten** Werten werden mehrere Variablen einer einzigen Art kombiniert (entweder alle numerischen oder alle kategorischen Variablen).

Die folgende Tabelle zeigt die Eigenschaften, die für die jeweilige Variablenart relevant sind.

[![Tabelle für Zeitreihenmodellvariable](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numerische Variablen

| Variableneigenschaft | BESCHREIBUNG |
| --- | ---|
| Variablenfilter | Filter sind optionale Bedingungsklauseln, um die Anzahl von Zeilen zu beschränken, die bei der Berechnung berücksichtigt werden. |
| Variablenwert | Für Berechnungen verwendete Telemetriewerte, die aus Geräten oder Sensoren stammen oder mithilfe von Zeitreihenausdrücken transformiert werden. Numerische Variablen müssen den Typ *Double* aufweisen.|
| Variableninterpolation | Die Interpolation gibt an, wie ein Signal anhand von vorhandenen Daten rekonstruiert wird. Für numerische Variablen sind die Interpolationsoptionen *Schritt* und *linear* verfügbar. |
| Variablenaggregation | Führen Sie Berechnungen über die unterstützten [Aggregationsfunktionen für numerische Variablen](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind) durch. |

Variablen entsprechen dem folgenden JSON-Beispiel:

```JSON
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
```

## <a name="categorical-variables"></a>Kategorische Variablen

| Variableneigenschaft | BESCHREIBUNG |
| --- | ---|
| Variablenfilter | Filter sind optionale Bedingungsklauseln, um die Anzahl von Zeilen zu beschränken, die bei der Berechnung berücksichtigt werden. |
| Variablenwert | Für Berechnungen verwendete Telemetriewerte, die aus Geräten oder Sensoren stammen. Kategorische Variablen müssen entweder den Typ *Long* oder den Typ *String* aufweisen. |
| Variableninterpolation | Die Interpolation gibt an, wie ein Signal anhand von vorhandenen Daten rekonstruiert wird. Die Option *Schrittinterpolation* ist für kategorische Variablen verfügbar. |
| Variablenkategorien | Kategorien sorgen für die Zuordnung zwischen den aus einem Gerät oder Sensor stammenden Werten und einer Bezeichnung. |
| Standardkategorie für Variablen | Die Standardkategorie steht für alle Werte zur Verfügung, die nicht in der Eigenschaft „Kategorien“ zugeordnet werden. |

Variablen entsprechen dem folgenden JSON-Beispiel:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Aggregieren von Variablen

| Variableneigenschaft | BESCHREIBUNG |
| --- | ---|
| Variablenfilter | Filter sind optionale Bedingungsklauseln, um die Anzahl von Zeilen zu beschränken, die bei der Berechnung berücksichtigt werden. |
| Variablenaggregation | Führen Sie Berechnungen über die unterstützten [Aggregationsfunktionen für aggregierte Variablen](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind) durch. |

Variablen entsprechen dem folgenden JSON-Beispiel:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Variablen werden in der Typdefinition eines Zeitreihenmodells gespeichert und können inline über APIs angegeben werden, um die gespeicherte Definition zu überschreiben.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Zeitreihenmodelle](./concepts-model-overview.md).

* Erfahren Sie mehr darüber, wie Sie mithilfe von [Abfrage-APIs](./concepts-query-overview.md) Variablen inline definieren.