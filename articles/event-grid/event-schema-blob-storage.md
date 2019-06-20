---
title: Azure Event Grid-Ereignisschema für Blob Storage
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Blob Storage-Ereignisse bereitgestellt werden.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614905"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid-Ereignisschema für Blob Storage

In diesem Artikel werden die Eigenschaften und das Schema für Blob Storage-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

Eine Liste von Beispielskripts und Tutorials finden Sie unter [Ereignisquellen in Azure Event Grid](event-sources.md#storage) im Abschnitt „Speicher“.

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Blob Storage gibt die folgenden Ereignistypen aus:

| Ereignistypen | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Wird ausgelöst, wenn ein Blob erstellt wird. |
| Microsoft.Storage.BlobDeleted | Wird ausgelöst, wenn ein Blob gelöscht wird. |

## <a name="example-event"></a>Beispielereignis

Das folgende Beispiel zeigt das Schema eines Blob-Erstellungsereignisses: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Das Schema für ein Blob-Löschungsereignis sieht ähnlich aus: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
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
| data | object | Ereignisdaten für Blob Storage. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| api | Zeichenfolge | Der Vorgang, durch den das Ereignis ausgelöst wurde. |
| clientRequestId | Zeichenfolge | Ein vom Client generierter, nicht transparenter Wert mit einer Zeichenbeschränkung von 1 KB. Wenn Sie die Speicheranalyseprotokollierung aktiviert haben, wird er in den Analyseprotokollen aufgezeichnet. |
| requestId | Zeichenfolge | Der eindeutige Bezeichner für die Anforderung. Verwenden Sie diesen für die Problembehandlung bei der Anforderung. |
| eTag | Zeichenfolge | Der Wert, den Sie verwenden können, um Vorgänge bedingt auszuführen. |
| contentType | Zeichenfolge | Der für das Blob angegebene Inhaltstyp. |
| contentLength | integer | Die Größe des Blobs in Byte. |
| blobType | Zeichenfolge | Der Blobtyp. Gültige Werte sind „BlockBlob“ oder „PageBlob“. |
| url | Zeichenfolge | Der Pfad des Blobs. |
| sequencer | Zeichenfolge | Ein vom Benutzer festgelegter Wert, mit dem Sie Anforderungen nachverfolgen können. |
| storageDiagnostics | object | Informationen zur Speicherdiagnose. |
 
## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Eine Einführung zum Arbeiten mit Blob Storage-Ereignissen finden Sie unter [Routen von Blob Storage-Ereignissen -– Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
