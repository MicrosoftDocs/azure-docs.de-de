---
title: Arbeiten mit Zeichenfolgen in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Dieser Artikel enthält ein Tutorial zur Verwendung von Azure Monitor Log Analytics im Azure-Portal zum Abfragen und Analysieren von Protokolldaten in Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 718b12c8a66d66a75796f88ef31b5f0f62abbbc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519651"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Arbeiten mit JSON und Datenstrukturen in Azure Monitor-Protokollabfragen

> [!NOTE]
> Sie sollten zunächst [Erste Schritte mit Azure Monitor Log Analytics](get-started-portal.md) und [Erste Schritte mit Azure Monitor-Protokollabfragen](get-started-queries.md) lesen, bevor Sie mit dieser Lektion beginnen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Geschachtelte Objekte sind Objekte, die andere Objekte in Form eines Arrays oder einer Zuordnung von Schlüssel-Wert-Paaren enthalten. Diese Objekte werden als JSON-Zeichenfolgen dargestellt. In diesem Artikel wird beschrieben, wie Sie mit JSON Daten abrufen und geschachtelte Objekte analysieren.

## <a name="working-with-json-strings"></a>Arbeiten mit JSON-Zeichenfolgen
Greifen Sie mit `extractjson` auf ein bestimmtes JSON-Element in einem bekannten Pfad zu. Zur Nutzung dieser Funktion ist ein Pfadausdruck erforderlich, für den die folgenden Konventionen gelten:

- _$_ verweist auf den Stammordner.
- Verwenden Sie die Klammer- oder Punktnotation, um wie in den folgenden Beispielen dargestellt auf Indizes und Elemente zu verweisen.


Verwenden Sie Klammern für Indizes und Punkte zum Trennen von Elementen:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Das gleiche Ergebnis mit der Klammernotation erhalten Sie wie folgt:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Wenn nur ein Element vorhanden ist, können Sie nur die Punktnotation verwenden:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Arbeiten mit Objekten

### <a name="parsejson"></a>parsejson
Sie können besonders leicht auf mehrere Elemente in Ihrer JSON-Struktur zugreifen, wenn der Zugriff über ein dynamisches Objekt erfolgt. Wandeln Sie die Textdaten mit `parsejson` in ein dynamisches Objekt um. Anschließend können Sie die Daten mithilfe zusätzlicher Funktionen analysieren.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Verwenden Sie `arraylength`, um die Anzahl der Elemente in einem Array zählen zu lassen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Verwenden Sie `mvexpand`, um die Eigenschaften eines Objekts in separate Zeilen aufzuteilen.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Verwenden Sie `buildschema`, um das Schema für zulässige Objektwerte abzurufen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Die Ausgabe ist ein Schema im JSON-Format:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Diese Ausgabe enthält die Namen der Objektfelder und die übereinstimmenden Datentypen. 

Für geschachtelte Objekte liegen möglicherweise wie im folgenden Beispiel verschiedene Schemas vor:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schemaerstellung](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung von Protokollabfragen in Azure Monitor finden Sie in folgenden weiteren Lektionen:

- [Zeichenfolgenvorgänge](string-operations.md)
- [Datums- und Uhrzeitvorgänge](datetime-operations.md)
- [Aggregationsfunktionen](aggregations.md)
- [Erweiterte Aggregationen](advanced-aggregations.md)
- [Schreiben von erweiterten Abfragen](advanced-query-writing.md)
- [Verknüpfungen](joins.md)
- [Diagramme](charts.md)