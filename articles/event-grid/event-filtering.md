---
title: Ereignisfilter für Azure Event Grid
description: Hier erfahren Sie, wie Sie Ereignisse beim Erstellen eines Azure Event Grid-Abonnements filtern können.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 76a4c16afc9edef0a88ac9f2892de9738fd30289
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305053"
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
* Wert oder Werte – Werte, die mit dem Schlüssel verglichen werden sollen.

Die JSON-Syntax für die Verwendung der erweiterten Filterung lautet:

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operator

Die verfügbaren Operatoren für Zahlen sind:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Der verfügbare Operator für boolesche Werte ist: BoolEquals

Die verfügbaren Operatoren für Zeichenfolgen sind:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Bei allen Zeichenfolgenvergleichen wird die Groß-/Kleinschreibung beachtet.

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
* Array

### <a name="limitations"></a>Einschränkungen

Für die erweiterte Filterung gelten folgende Einschränkungen:

* Fünf erweiterte Filter pro Event Grid-Abonnement
* 512 Zeichen pro Zeichenfolgenwert
* Fünf Werte für **in**- und **not in**-Operatoren

Der gleiche Schlüssel kann in mehr als einem Filter verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Filtern von Ereignissen mit PowerShell und Azure CLI finden Sie unter [Filtern von Ereignissen für Event Grid](how-to-filter-events.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
