---
title: JSON-Vereinfachungs- und -Escaperegeln — Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Erfahren Sie mehr über JSON-Vereinfachung, Verwenden von Escapezeichen und Arraybehandlung in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: 0839d2c734418824952f37cb177490e56e1133c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017966"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON-Vereinfachung, Verwenden von Escapezeichen und Arraybehandlung

Ihre Azure Time Series Insights Gen2-Umgebung erstellt dynamisch die Spalten Ihrer warmen (Warm Storage) und kalten Speicher (Cold Storage), wobei ein bestimmter Satz von Namenskonventionen berücksichtigt wird. Wenn ein Ereignis erfasst wird, wird ein Satz von Regeln auf die Namen der JSON-Nutzlast und JSON-Eigenschaften angewendet. Hierzu gehören das Verwenden von Escapezeichen für bestimmte Sonderzeichen und das Vereinfachen von geschachtelten JSON-Objekten. Sie sollten diese Regeln kennen, damit Sie verstehen, wie sich die Form Ihres JSON-Codes darauf auswirkt, wie Ihre Ereignisse gespeichert und abgefragt werden. Die vollständige Liste der Regeln finden Sie in der folgenden Tabelle. In den Beispielen A und B wird auch veranschaulicht, wie Sie mehrere Zeitreihen in einem Array effizient stapeln können.

> [!IMPORTANT]
>
> * Lesen Sie die unten aufgeführten Regeln sorgfältig, bevor Sie eine [Zeitreihen-ID-Eigenschaft](./how-to-select-tsid.md) und/oder die [Zeitstempeleigenschaft(en)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) für Ihre Ereignisquelle auswählen. Befindet sich Ihre TS-ID oder Ihr Zeitstempel in einem geschachtelten Objekt, oder enthält Ihre TS-ID oder Ihr Zeitstempel mindestens eines der folgenden Sonderzeichen, müssen Sie sicherstellen, dass der von Ihnen angegebene Eigenschaftsname mit dem Spaltennamen übereinstimmt, *nachdem* die Erfassungsregeln angewendet wurden. Sehen Sie sich dazu Beispiel [B](concepts-json-flattening-escaping-rules.md#example-b) weiter unten an.

| Regel | JSON-Beispiel | [Syntax des Zeitreihenausdrucks](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Spaltenname für Eigenschaft in Parquet
|---|---|---|---|
| Der Azure Time Series Insights Gen2-Datentyp wird an das Ende Ihres Spaltennamens als „_\<dataType\>“ angehängt. | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| Die [Zeitstempeleigenschaft](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) für eine Ereignisquelle wird in Azure Time Series Insights Gen2 als „timestamp“ im Speicher gespeichert, und der Wert wird in UTC gespeichert. Sie können Ihre Zeitstempeleigenschaft für Ereignisquellen anpassen, um die Anforderungen Ihrer Lösung zu erfüllen, aber der Spaltenname lautet in Warm und Cold Storage „timestamp“. Andere JSON-Eigenschaften mit Typ „datetime“, die nicht den Zeitstempel einer Ereignisquelle angeben, werden mit „_datetime“ im Spaltennamen gespeichert, wie dies in der obigen Regel beschrieben ist.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Für JSON-Eigenschaftsnamen, die Sonderzeichen enthalten. „[“, „\“ oder „'“ enthalten, werden „['“ und „']“ als Escapezeichen verwendet.  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| Zwischen „['“ und „']“ werden bei einfachen Anführungszeichen und umgekehrten Schrägstrichen ebenfalls Escapezeichen verwendet. Ein einfaches Anführungszeichen wird als „\’“ und ein umgekehrter Schrägstrich wird als „\\\“ geschrieben. | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Geschachtelte JSON-Objekte werden mit einem Punkt als Trennzeichen vereinfacht. Es wird Schachteln bis zu 10 Ebenen unterstützt. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` oder `$event.series['value'].Long` |  `series.value_long` |
| Arrays von primitiven Typen werden mit dem Typ „dynamic“ gespeichert. |  ```"values": [154, 149, 147]``` | Dynamische Typen können nur über die [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)-API abgerufen werden. | `values_dynamic` |
| Arrays, die Objekte enthalten, haben je nach Objektinhalt zwei Verhaltensweisen: Befinden sich entweder die TS-ID(s) oder die Zeitstempeleigenschaft(en) innerhalb der Objekte in einem Array, wird das Array so aufgelöst, dass die anfängliche JSON-Nutzlast mehrere Ereignisse erzeugt. Dies ermöglicht es Ihnen, mehrere Ereignisse in einer JSON-Struktur zu stapeln. Alle Eigenschaften der obersten Ebene, die Peers im Array sind, werden mit jedem aufgelösten Objekt gespeichert. Befinden sich Ihre TS-ID(s) und der Zeitstempel *nicht* im Array, wird es vollständig mit dem Typ „dynamic“ gespeichert. | Sehen Sie sich dazu die Beispiele [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b) und [C](concepts-json-flattening-escaping-rules.md#example-c) weiter unten an.
| Arrays, die gemischte Elemente enthalten, werden nicht vereinfacht. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Dynamische Typen können nur über die [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)-API abgerufen werden. | `values_dynamic` |
| Der Name einer JSON-Eigenschaft kann maximal 512 Zeichen lang sein. Hat ein Name mehr als 512 Zeichen, wird er auf 512 Zeichen gekürzt, und es wird „'_<'hashCode'>'“ angefügt. **Beachten Sie**, dass dies auch für Eigenschaftsnamen gilt, die aus einem vereinfachten Objekt verkettet wurden, was einen geschachtelten Objektpfad kennzeichnet. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Die zwei Verhaltensweisen für Arrays

Arrays von Objekten werden, je nachdem, wie Sie Ihre Daten modelliert haben, entweder vollständig gespeichert oder in mehrere Ereignisse aufgeteilt. Dies ermöglicht es Ihnen, Ereignisse in einem Array zu stapeln und sich wiederholende Telemetrieeigenschaften zu vermeiden, die auf der Stammobjektebene definiert sind. Stapelung kann vorteilhaft sein, da sie dazu führt, dass weniger Event Hubs- oder Azure IoT Hub-Nachrichten gesendet werden.

In einigen Fällen sind Arrays, die Objekte enthalten, jedoch nur im Kontext anderer Werte sinnvoll. Ein Erstellen mehrerer Ereignisse würde die Daten bedeutungslos werden lassen. Um sicherzustellen, dass ein Array von Objekten so, wie es vorliegt, mit dem Typ „dynamic“ gespeichert wird, gehen Sie gemäß der folgenden Datenmodellierungsanleitung vor, und sehen Sie sich [Beispiel C](concepts-json-flattening-escaping-rules.md#example-c) an.

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Woher weiß ich, ob für mein Array von Objekten mehrere Ereignisse erzeugt werden?

Ist mindestens eine Ihrer Zeitreihen-ID-Eigenschaften in Objekten in einem Array geschachtelt, *oder* ist Ihre Zeitstempeleigenschaft für eine Ereignisquelle geschachtelt, teilt die Erfassungs-Engine die Eigenschaft auf, um mehrere Ereignisse zu erstellen. Die Eigenschaftsnamen, die Sie für die TS-ID(s) und/oder Ihren Zeitstempel angegeben haben, sollten den oben beschriebenen Vereinfachungsregeln folgen und kennzeichnen daher die Form Ihres JSON-Codes. Sehen Sie sich die folgenden Beispiele an, und lesen Sie die Anleitung dazu, wie [eine Zeitreihen-ID-Eigenschaft ausgewählt wird](./how-to-select-tsid.md).

### <a name="example-a"></a>Beispiel A

Zeitreihen-ID im Objektstamm und Zeitstempel geschachtelt\
**Zeitreihen-ID für Umgebung:** `"id"`\
**Zeitstempel der Ereignisquelle:** `"values.time"`\
**JSON-Nutzlast:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Ergebnis in Parquet-Datei:** \
Für diese Konfiguration und Nutzlast werden drei Spalten und vier Ereignisse erzeugt.

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Beispiel B

Zusammengesetzte Zeitreihen-ID mit einer geschachtelten Eigenschaft\
**Zeitreihen-ID für Umgebung:** `"plantId"` und `"telemetry.tagId"`\
**Zeitstempel der Ereignisquelle:** `"timestamp"`\
**JSON-Nutzlast:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Ergebnis in Parquet-Datei:** \
Für diese Konfiguration und Nutzlast werden vier Spalten und sechs Ereignisse erzeugt.

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>Beispiel C

Zeitreihen-ID und Zeitstempel befinden sich im Objektstamm\
**Zeitreihen-ID für Umgebung:** `"id"`\
**Zeitstempel der Ereignisquelle:** `"timestamp"`\
**JSON-Nutzlast:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Ergebnis in Parquet-Datei:** \
Für diese Konfiguration und Nutzlast werden drei Spalten und ein Ereignis erzeugt.

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Nächste Schritte

* Ermitteln der [Durchsatzbeschränkungen](./concepts-streaming-ingress-throughput-limits.md) Ihrer Umgebung