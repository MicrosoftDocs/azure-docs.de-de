---
title: Bevorstehende Änderungen an den Erfassungs- und Vereinfachungsregeln in Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Änderungen an den Erfassungsregeln
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995232"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Bevorstehende Änderungen an den JSON-Vereinfachungs- und -Escaperegeln für neue Umgebungen

> [!IMPORTANT]
> Diese Änderungen werden nur auf *neu erstellte* Microsoft Azure Time Series Insights Gen2-Umgebungen angewandt. Sie gelten nicht für Gen1-Umgebungen.

Ihre Azure Time Series Insights Gen2-Umgebung erstellt die Speicherspalten dynamisch unter Berücksichtigung bestimmter Namenskonventionen. Wenn ein Ereignis erfasst wird, wendet Time Series Insights bestimmte Regeln auf die Namen der JSON-Nutzdaten und JSON-Eigenschaften an. Seit Juli 2020 gelten für neue Azure Time Series Insights Gen2-Umgebungen Änderungen an der Art und Weise, wie JSON-Daten vereinfacht und gespeichert werden. Dies betrifft Sie in den folgenden Fällen:

* Ihre JSON-Nutzdaten enthalten geschachtelte Objekte.
* Ihre JSON-Nutzdaten enthalten Arrays.
* Sie verwenden eines der folgenden vier Sonderzeichen in einem JSON-Eigenschaftennamen: `[`, `\`, `.`, `'`.
* Eine oder mehrere Ihrer Zeitreihen-ID-Eigenschaften sind in einem geschachtelten Objekt enthalten.

Wenn Sie eine neue Umgebung erstellen und mindestens einer der Fälle auf Ihre Ereignisnutzdaten zutrifft, werden Ihre Daten anders vereinfacht und gespeichert. Die Änderungen sind in der folgenden Tabelle zusammengefasst:

| Aktuelle Regel | Neue Regel | JSON-Beispiel | Vorheriger Spaltenname | Neuer Spaltenname
|---|---| ---| ---|  ---|
| Geschachtelter JSON-Code wird mithilfe eines Unterstrichs als Trennzeichen vereinfacht. |Geschachtelter JSON-Code wird mithilfe eines Punkts als Trennzeichen vereinfacht.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Bei Sonderzeichen werden keine Escapezeichen verwendet. | Für JSON-Eigenschaftennamen, die die Sonderzeichen `.`, `[`,   `\` oder `'` enthalten, werden als Escapezeichen `['` und `']` verwendet. Zwischen `['` und `']` werden bei einfachen Anführungszeichen und umgekehrten Schrägstrichen ebenfalls Escapezeichen verwendet. Ein einfaches Anführungszeichen wird als `\'` geschrieben und ein umgekehrter Schrägstrich als `\\`.  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Arrays von primitiven Typen werden als Zeichenfolgen gespeichert. | Arrays von primitiven Typen werden als dynamische Typen gespeichert.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Arrays von Objekten werden immer vereinfacht, und es werden mehrere Ereignisse generiert. | Wenn die Objekte in einem Array weder über die Zeitreihen-ID noch über Zeitstempeleigenschaften verfügen, wird das gesamte Array von Objekten als dynamischer Typ gespeichert. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Empfohlene Änderungen für neue Umgebungen

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Wenn die TS-ID und/oder die Zeitstempeleigenschaft innerhalb eines Objekts geschachtelt ist

Alle neuen Bereitstellungen müssen den neuen Erfassungsregeln entsprechen. Wenn Ihre Zeitreihen-ID z. B. `telemetry_tagId` lautet, müssen Sie alle Azure Resource Manager-Vorlagen oder automatisierten Bereitstellungsskripts so aktualisieren, dass `telemetry.tagId` als Zeitreihen-ID für die Umgebung konfiguriert ist. Diese Änderung ist auch bei Zeitstempeln für Ereignisquellen in geschachteltem JSON-Code erforderlich.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Wenn Ihre Nutzlast geschachtelten JSON-Code oder Sonderzeichen enthält, und Sie die Erstellung von variablen Ausdrücken für das [Zeitreihenmodell](./concepts-model-overview.md) automatisieren

Aktualisieren Sie den Clientcode, über den [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) ausgeführt wird, um ihn an die neuen Erfassungsregeln anzupassen. Beispielsweise sollte ein bisheriger [Zeitreihenausdruck](/rest/api/time-series-insights/reference-time-series-expression-syntax) von `"value": {"tsx": "$event.series_value.Double"}` in eine der folgenden Optionen geändert werden:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Datenspeicherung und -erfassung in Azure Time Series Insights Gen2](./concepts-ingestion-overview.md).

* Erfahren Sie, wie Sie Ihre Daten mit [Zeitreihenabfrage-APIs](./concepts-query-overview.md) abfragen.

* Erfahren Sie mehr über die [neue Time Series-Ausdruckssyntax](/rest/api/time-series-insights/reference-time-series-expression-syntax).