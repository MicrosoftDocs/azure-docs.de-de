---
title: Ereignisfilter für Azure Event Grid
description: Hier erfahren Sie, wie Sie Ereignisse beim Erstellen eines Azure Event Grid-Abonnements filtern können.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: bc3e84037693fcd909961ba409871d947ef1de7d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574905"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements

Dieser Artikel beschreibt die verschiedenen Möglichkeiten, um zu filtern, welche Ereignisse an den Endpunkt gesendet werden. Beim Erstellen eines Ereignisabonnements haben Sie drei Optionen zum Filtern:

* Ereignistypen
* Betreff beginnt oder endet mit
* Erweiterte Felder und Operatoren

## <a name="event-type-filtering"></a>Ereignistypfilterung

Standardmäßig werden alle [Ereignistypen](event-schema.md) für die Ereignisquelle an den Endpunkt gesendet. Sie können entscheiden, nur bestimmte Ereignistypen an Ihren Endpunkt zu senden. Beispielsweise können Sie sich bezüglich Aktualisierungen Ihrer Ressourcen benachrichtigen lassen, aber nicht zu anderen Vorgängen, wie z.B. das Löschen. Filtern Sie in diesem Fall nach dem Ereignistyp `Microsoft.Resources.ResourceWriteSuccess`. Geben Sie ein Array mit den Ereignistypen an oder geben Sie `All` an, um alle Ereignistypen für die Ereignisquelle zu erhalten.

Die JSON-Syntax für das Filtern nach Ereignistyp lautet:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Betrefffilterung

Zum einfachen Filtern nach Betreff geben Sie einen Start- oder Endwert für den Betreff ein. Sie können beispielsweise angeben, dass der Betreff mit `.txt` endet, um nur Ereignisse im Zusammenhang mit dem Hochladen einer Textdatei in das Speicherkonto zu erhalten. Oder Sie können nach Betreffs filtern, die mit `/blobServices/default/containers/testcontainer` beginnen, um alle Ereignisse für diesen Container abzurufen, aber nicht für andere Container des Speicherkontos.

Erstellen Sie beim Veröffentlichen von Ereignissen für benutzerdefinierte Themen Betreffinformationen für Ihre Ereignisse, an denen Abonnenten einfach erkennen können, ob Interesse am Ereignis besteht. Abonnenten verwenden die Betreffeigenschaft zum Filtern und Weiterleiten von Ereignissen. Erwägen Sie, den Pfad zum Ereignisort hinzuzufügen, damit Abonnenten nach Segmenten dieses Pfads filtern können. Mit dem Pfad können Abonnenten Ereignisse speziell oder allgemein filtern. Wenn Sie einen Pfad mit drei Segmenten im Betreff angeben, z.B. `/A/B/C`, können Abonnenten nach dem ersten Segment `/A` filtern, um eine umfassendere Gruppe von Ereignissen angezeigt zu bekommen. Diese Abonnenten erhalten Ereignisse mit Betreffinformationen wie `/A/B/C` oder `/A/D/E`. Andere Abonnenten können nach `/A/B` filtern, um eine eingeschränktere Gruppe mit Ereignissen zu erhalten.

Die JSON-Syntax für das Filtern nach Betreff lautet:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Erweiterte Filterung

Um nach Werten in den Datenfeldern zu filtern und den Vergleichsoperator anzugeben, verwenden Sie die erweiterte Filteroption. Bei der erweiterten Filterung können Sie folgende Informationen angeben:

* Operatortyp – Typ des Vergleichs.
* Schlüssel – Feld in den Ereignisdaten, die Sie für die Filterung verwenden. Sie können eine Zahl, boolescher Wert oder Zeichenfolge eingeben.
* Werte – Werte, die mit dem Schlüssel verglichen werden sollen.

Wenn Sie einen einzelnen Filter mit mehreren Werten angeben, wird ein **OR**-Vorgang ausgeführt, daher muss das Schlüsselfeld einen der folgenden Werte aufweisen. Beispiel:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Wenn Sie mehrere verschiedene Filter angeben, wird ein **AND**-Vorgang ausgeführt, sodass jede Filterbedingung erfüllt sein muss. Hier sehen Sie ein Beispiel: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operatoren

Die verfügbaren Operatoren für **Zahlen** sind:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Der verfügbare Operator für **boolesche Werte** ist: 
- BoolEquals

Die verfügbaren Operatoren für **Zeichenfolgen** sind:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Bei sämtlichen Zeichenfolgenvergleichen wird die Groß-/Kleinschreibung **nicht** beachtet.

> [!NOTE]
> Wenn der JSON-Code des Ereignisses nicht den erweiterten Filterschlüssel enthält, wird der Filter für die folgenden Operatoren als **nicht übereinstimmend** ausgewertet: 
> - NumberGreaterThan
> - NumberGreaterThanOrEquals
> - NumberLessThan
> - NumberLessThanOrEquals
> - NumberIn
> - BoolEquals
> - StringContains
> - StringBeginsWith
> - StringEndsWith
> - StringIn
> 
>Der Filter wird für die folgenden Operatoren als **übereinstimmend** ausgewertet:
> - NumberNotIn
> - StringNotIn

### <a name="key"></a>Schlüssel

Verwenden Sie für Ereignisse im Event Grid-Schema die folgenden Werte für den Schlüssel:

* id
* Thema
* Subject
* EventType
* DataVersion
* Ereignisdaten (z.B. Data.key1)

Verwenden Sie für Ereignisse im Cloud Events-Schema die folgenden Werte für den Schlüssel:

* EventId
* `Source`
* EventType
* EventTypeVersion
* Ereignisdaten (z.B. Data.key1)

Verwenden Sie für ein benutzerdefiniertes Eingabeschema die Ereignisdatenfelder (z.B. Data.key1).

### <a name="values"></a>Werte

Diese Werte sind möglich:

* number
* Zeichenfolge
* boolean
* array

### <a name="limitations"></a>Einschränkungen

Für die erweiterte Filterung gelten folgende Einschränkungen:

* 5 erweiterte Filter und 25 Filterwerte für alle Filter pro Event Grid-Abonnement
* 512 Zeichen pro Zeichenfolgenwert
* Fünf Werte für **in**- und **not in**-Operatoren
* Schlüssel mit enthaltenem **`.` (Punktzeichen)** . Zum Beispiel: `http://schemas.microsoft.com/claims/authnclassreference` oder `john.doe@contoso.com`. Derzeit gibt es keine Unterstützung für Escapezeichen in Schlüsseln. 

Der gleiche Schlüssel kann in mehr als einem Filter verwendet werden.

### <a name="examples"></a>Beispiele

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Filtern von Ereignissen mit PowerShell und Azure CLI finden Sie unter [Filtern von Ereignissen für Event Grid](how-to-filter-events.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
