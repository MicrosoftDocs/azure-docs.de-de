---
title: Azure Event Grid-Ereignisschema
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Ereignisse bereitgestellt werden.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 4a795221790a9d56bcbfe30a50b0c838fb8d9e56
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304251"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid-Ereignisschema

Dieser Artikel beschreibt die Eigenschaften und das Schema für alle Ereignisse. Ereignisse bestehen aus einer Gruppe von fünf erforderlichen Zeichenfolgeneigenschaften und einem erforderlichen Datenobjekt. Die Eigenschaften gelten für alle Ereignisse von jedem Herausgeber. Das Datenobjekt weist Eigenschaften auf, die für die einzelnen Herausgeber spezifisch sind. Bei Systemthemen sind diese Eigenschaften spezifisch für den Ressourcenanbieter, z.B. Azure Storage oder Azure Event Hubs.

Ereignisquellen senden Ereignisse an Azure Event Grid in einem Array, das mehrere Ereignisobjekte aufweisen kann. Beim Veröffentlichen von Ereignissen in einem Ereignisrasterthema kann das Array eine Gesamtgröße von bis zu 1 MB aufweisen. Jedes Ereignis in dem Array ist auf 64 KB (Allgemeine Verfügbarkeit) oder 1 MB (Vorschauversion) beschränkt. Wenn ein Ereignis oder das Array das zulässige Größenlimit überschreitet, erhalten Sie die Antwort **413 Nutzlast zu groß**.

> [!NOTE]
> Ein Ereignis mit einer Größe von bis zu 64 KB wird von der Vereinbarung zum Servicelevel (SLA) für die allgemeine Verfügbarkeit (GA) abgedeckt. Die Unterstützung für ein Ereignis von einer Größe bis zu 1 MB ist derzeit in der Vorschauversion verfügbar. Ereignisse, die größer als 64 KB sind, werden in Schritten von 64 KB in Rechnung gestellt. 

Event Grid sendet die Ereignisse an Abonnenten in einem Array, das ein einzelnes Ereignis aufweist. Dieses Verhalten kann sich in der Zukunft ändern.

Sie finden das JSON-Schema für das Event Grid-Ereignis und die Datennutzlast für jeden Azure-Herausgeber im [Event Schema-Speicher](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Ereignisschema

Das folgende Beispiel zeigt die Eigenschaften, die von allen Ereignisherausgebern verwendet werden:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Das für ein Azure Blob Storage-Ereignis veröffentlichte Schema sieht beispielsweise folgendermaßen aus:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Ereigniseigenschaften

Alle Ereignisse weisen die gleichen Daten auf oberster Ebene auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfad zu der Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | object | Die für den Ressourcenanbieter spezifischen Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Weitere Informationen zu den Eigenschaften im Datenobjekt finden Sie in der Ereignisquelle:

* [Azure-Abonnements (Verwaltungsvorgänge)](event-schema-subscriptions.md)
* [Containerregistrierung](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Ressourcengruppen (Verwaltungsvorgänge)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

Für benutzerdefinierte Themen bestimmt der Ereignisherausgeber das Datenobjekt. Die Daten auf oberster Ebene müssen die gleichen Felder wie über Standardressourcen definierte Ereignisse aufweisen.

Erstellen Sie beim Veröffentlichen von Ereignissen für benutzerdefinierte Themen Betreffinformationen für Ihre Ereignisse, an denen Abonnenten einfach erkennen können, ob Interesse am Ereignis besteht. Abonnenten verwenden den Betreff zum Filtern und Weiterleiten von Ereignissen. Erwägen Sie, den Pfad zum Ereignisort anzugeben, damit Abonnenten nach Segmenten dieses Pfads filtern können. Mit dem Pfad können Abonnenten Ereignisse speziell oder allgemein filtern. Wenn Sie beispielsweise einen Pfad mit drei Segmenten im Betreff angeben, z.B. `/A/B/C`, können Abonnenten nach dem ersten Segment `/A` filtern, um eine umfassendere Gruppe von Ereignissen angezeigt zu bekommen. Diese Abonnenten erhalten Ereignisse mit Betreffinformationen wie `/A/B/C` oder `/A/D/E`. Andere Abonnenten können nach `/A/B` filtern, um eine eingeschränktere Gruppe mit Ereignissen zu erhalten.

Es kann auch sein, dass Ihr Betreff weitere Details zu den Vorkommnissen enthalten muss. Mit dem Publisher **Speicherkonten** wird beispielsweise der Betreff `/blobServices/default/containers/<container-name>/blobs/<file>` angegeben, wenn einem Container eine Datei hinzugefügt wird. Ein Abonnent kann nach dem Pfad `/blobServices/default/containers/testcontainer` filtern, um alle Ereignisse für diesen Container abzurufen, aber nicht für andere Container des Speicherkontos. Außerdem kann ein Abonnent nach dem Suffix `.txt` filtern oder es für die Weiterleitung verwenden, um nur mit Textdateien zu arbeiten.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
