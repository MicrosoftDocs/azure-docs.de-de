---
title: Erweiterte Filterung – Azure Event Grid IoT Edge | Microsoft-Dokumentation
description: Erweiterte Filterung in Event Grid in IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001047"
---
# <a name="advanced-filtering"></a>Erweiterte Filterung
In Event Grid können Filter für eine beliebige Eigenschaft in der JSON-Nutzlast angegeben werden. Diese Filter werden als Gruppe von `AND`-Bedingungen erstellt, wobei jede äußere Bedingung über optionale innere `OR`-Bedingungen verfügt. Für jede `AND`-Bedingung geben Sie die folgenden Werte an:

* `OperatorType`: Typ des Vergleichs.
* `Key`: JSON-Pfad zur Eigenschaft, auf die der Filter angewendet werden soll.
* `Value`: Referenzwert, für den der Filter ausgeführt wird (oder) `Values`: Gruppe von Referenzwerten für die der Filter ausgeführt wird.

## <a name="json-syntax"></a>JSON-Syntax

Die JSON-Syntax für einen erweiterten Filter lautet wie folgt:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtern nach Arraywerten

Event Grid unterstützt derzeit nicht das Filtern nach einem Array von Werten. Wenn ein eingehendes Ereignis einen Arraywert für den Schlüssel des erweiterten Filters aufweist, schlägt der Abgleichsvorgang fehl. Für das eingehende Ereignis liegt eine Nichtübereinstimmung mit dem Ereignisabonnement vor.

## <a name="and-or-not-semantics"></a>AND-OR-NOT-Semantik

Beachten Sie, dass im obigen JSON-Beispiel `AdvancedFilters` ein Array darstellt. Stellen Sie sich jedes `AdvancedFilter`-Arrayelement als `AND`-Bedingung vor.

Für Operatoren, die mehrere Werte unterstützen (z. B. `NumberIn`, `NumberNotIn`, `StringIn` usw.) wird jeder Wert als `OR`-Bedingung behandelt. Damit entspricht ein `StringBeginsWith("a", "b", "c")` einem beliebigen Zeichenfolgenwert, der mit `a` oder `b` oder `c` beginnt.

> [!CAUTION]
> Die NOT-Operatoren (`NumberNotIn` und `StringNotIn`) verhalten sich wie AND-Bedingungen für jeden im Feld `Values` angegebenen Wert.
>
> Andernfalls wird der Filter zu einem Filter vom Typ „Alle annehmen“, und dies würde dem Zweck des Filterns widersprechen.

## <a name="floating-point-rounding-behavior"></a>Rundungsverhalten für Gleitkommawerte

Event Grid verwendet den .NET-Typ `decimal`, um alle numerischen Werte zu behandeln. Die im JSON des Ereignisabonnements angegebenen Zahlenwerte unterliegen nicht dem Rundungsverhalten für Gleitkommawerte.

## <a name="case-sensitivity-of-string-filters"></a>Berücksichtigung der Groß- und Kleinschreibung in Zeichenfolgenfiltern

Bei allen Zeichenfolgenvergleichen wird die Groß- und Kleinschreibung beachtet. Derzeit gibt es keine Möglichkeit, dieses Verhalten zu ändern.

## <a name="allowed-advanced-filter-keys"></a>Zulässige Schlüssel für erweiterte Filter

Die `Key`-Eigenschaft kann entweder eine bekannte Eigenschaft der obersten Ebene oder ein JSON-Pfad mit mehreren Punkten sein, wobei jeder Punkt einen Schritt in ein geschachteltes JSON-Objekt angibt.

Im Unterschied zur JSONPath-Spezifikation misst Event Grid dem Zeichen `$` im Schlüssel keine besondere Bedeutung bei.

### <a name="event-grid-schema"></a>Event Grid-Schema

Für Ereignisse im Event Grid-Schema:

* id
* Thema
* Subject
* EventType
* DataVersion
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Benutzerdefiniertes Ereignisschema

Es gibt keine Einschränkung für `Key` in einem benutzerdefinierten Ereignisschema, da Event Grid kein Umschlagschema für die Nutzlast erzwingt.

## <a name="numeric-single-value-filter-examples"></a>Beispiele für Filter von numerischen Einzelwerten

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Beispiele für Filter von numerischen Bereichswerten

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Beispiele für Filter von Zeichenfolgen-Bereichswerten

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Beispiele für Filter von booleschen Einzelwerten

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
