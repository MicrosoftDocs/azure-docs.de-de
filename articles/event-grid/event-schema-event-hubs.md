---
title: Azure Event Grid-Ereignisschema für Event Hubs
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Event Hubs-Ereignisse bereitgestellt werden
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561827"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid-Ereignisschema für Event Hubs

In diesem Artikel werden die Eigenschaften und das Schema für Event Hubs-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

Eine Liste der Beispielskripts und Tutorials finden Sie unter [Event Hubs-Ereignisquelle](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Verfügbare Ereignistypen

Event Hubs gibt den Ereignistyp **Microsoft.EventHub.CaptureFileCreated** aus, wenn eine Erfassungsdatei erstellt wird.

## <a name="example-event"></a>Beispielereignis

In diesem Beispielereignis ist das Schema eines Event Hubs-Ereignisses dargestellt, das ausgelöst wird, wenn eine Datei über das Feature „Erfassen“ gespeichert wird: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | object | Event Hub-Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| fileUrl | Zeichenfolge | Der Pfad zur Erfassungsdatei. |
| fileType | Zeichenfolge | Der Dateityp der Erfassungsdatei. |
| partitionId | Zeichenfolge | Die Shard-ID. |
| sizeInBytes | integer | Die Dateigröße. |
| eventCount | integer | Die Anzahl der Ereignisse in der Datei. |
| firstSequenceNumber | integer | Die kleinste Sequenznummer aus der Warteschlange. |
| lastSequenceNumber | integer | Die größte Sequenznummer aus der Warteschlange. |
| firstEnqueueTime | Zeichenfolge | Die erste Zeit aus der Warteschlange. |
| lastEnqueueTime | Zeichenfolge | Die letzte Zeit aus der Warteschlange. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Informationen zur Behandlung von Event Hubs-Ereignissen finden Sie unter [Streamen von Big Data in einem Data Warehouse](event-grid-event-hubs-integration.md).