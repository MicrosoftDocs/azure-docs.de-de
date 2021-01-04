---
title: Azure Blob Storage als Event Grid-Quelle
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Blob Storage-Ereignisse bereitgestellt werden.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1a81b30fcb775f5e8bc99bda70307f7f1aed9796
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452541"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob Storage als Event Grid-Quelle

In diesem Artikel werden die Eigenschaften und das Schema für Blob Storage-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). Außerdem erhalten Sie eine Liste mit Schnellstarts und Tutorials, die Azure Blob Storage als Ereignisquelle verwenden.


>[!NOTE]
> Nur Speicherkonten vom Typ **StorageV2 (universell, Version 2)** , **BlockBlobStorage** und **BlobStorage** unterstützen die Ereignisintegration. **Storage (Universell V1)** unterstützt *nicht* die Integration in Event Grid.

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

### <a name="list-of-events-for-blob-rest-apis"></a>Ereignisliste für Blob-REST-APIs

Die folgenden Ereignisse werden ausgelöst, wenn ein Client durch Aufrufen von Blob-REST-APIs ein Blob erstellt, ersetzt oder löscht:

> [!NOTE]
> Die Container `$logs` und `$blobchangefeed` sind nicht in Event Grid integriert, sodass die Aktivität in diesen Containern keine Ereignisse generiert. Außerdem werden bei Verwendung des DFS-Endpunkts *`(abfss://URI) `* für Konten, die nicht hierarchische Namespaces verarbeiten können, werden keine Ereignisse generiert, aber der Blob-Endpunkt *`(wasb:// URI)`* generiert Ereignisse.

 |Ereignisname |BESCHREIBUNG|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Wird ausgelöst, wenn ein Blob erstellt oder ersetzt wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `PutBlob`, `PutBlockList` oder `CopyBlob` verwenden, die in der Blob-REST-API zur Verfügung stehen.   |
 |**Microsoft.Storage.BlobDeleted** |Wird ausgelöst, wenn ein Blob gelöscht wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `DeleteBlob` aufrufen, der in der Blob-REST-API zur Verfügung steht. |

> [!NOTE]
> Wenn Sie für **Azure Blob Storage** sicherstellen möchten, dass das Ereignis **Microsoft.Storage.BlobCreated** erst ausgelöst wird, nachdem ein Blockblob vollständig committet wurde, filtern Sie das Ereignis nach Aufrufen der REST-APIs `CopyBlob`, `PutBlob` und `PutBlockList`. Bei diesen API-Aufrufen wird das Ereignis **Microsoft.Storage.BlobCreated** erst ausgelöst, nachdem Daten vollständig in einem Blockblob committet wurden. Informationen zum Erstellen eines Filters finden Sie unter [Filtern von Ereignissen für Event Grid](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Ereignisliste für REST-APIs von Azure Data Lake Storage Gen2

Die folgenden Ereignisse werden ausgelöst, wenn Sie einen hierarchischen Namespace für das Speicherkonto aktivieren und Clients REST-APIs von Azure Data Lake Storage Gen2 verwenden. Weitere Informationen zu Data Lake Storage Gen2 finden Sie in der [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Ereignisname|BESCHREIBUNG|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Wird ausgelöst, wenn ein Blob erstellt oder ersetzt wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients die Vorgänge `CreateFile` und `FlushWithClose` verwenden, die in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung stehen. |
|**Microsoft.Storage.BlobDeleted** |Wird ausgelöst, wenn ein Blob gelöscht wird. <br>Dieses Ereignis wird insbesondere auch ausgelöst, wenn Clients den Vorgang `DeleteFile` aufrufen, der in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung steht. |
|**Microsoft.Storage.BlobRenamed**|Wird ausgelöst, wenn ein Blob umbenannt wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `RenameFile` verwenden, der in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung steht.|
|**Microsoft.Storage.DirectoryCreated**|Wird ausgelöst, wenn ein Verzeichnis erstellt wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `CreateDirectory` verwenden, der in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung steht.|
|**Microsoft.Storage.DirectoryRenamed**|Wird ausgelöst, wenn ein Verzeichnis umbenannt wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `RenameDirectory` verwenden, der in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung steht.|
|**Microsoft.Storage.DirectoryDeleted**|Wird ausgelöst, wenn ein Verzeichnis gelöscht wird. <br>Dieses Ereignis wird insbesondere ausgelöst, wenn Clients den Vorgang `DeleteDirectory` verwenden, der in der REST-API von Azure Data Lake Storage Gen2 zur Verfügung steht.|

> [!NOTE]
> Wenn Sie für **Azure Blob Azure Data Lake Storage Gen2** sicherstellen möchten, dass das Ereignis **Microsoft.Storage.BlobCreated** erst ausgelöst wird, nachdem ein Blockblob vollständig committet wurde, filtern Sie das Ereignis nach Aufrufen der REST-API `FlushWithClose`. Bei diesem API-Aufruf wird das Ereignis **Microsoft.Storage.BlobCreated** erst ausgelöst, nachdem Daten vollständig in einem Blockblob committet wurden. Informationen zum Erstellen eines Filters finden Sie unter [Filtern von Ereignissen für Event Grid](./how-to-filter-events.md).

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Der Inhalt einer Ereignisantwort

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.

In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für ein Blob Storage-Ereignis aussehen.

### <a name="microsoftstorageblobcreated-event"></a>Ereignis „Microsoft.Storage.BlobCreated“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Ereignis „Microsoft.Storage.BlobCreated“ (Data Lake Storage Gen2)

Wenn das Blob Storage-Konto über einen hierarchischen Namespace verfügt, sehen die Daten, mit Ausnahme dieser Änderungen, in etwa wie im vorherigen Beispiel aus:

* Der Schlüssel `dataVersion` wird auf den Wert `2` festgelegt.

* Der Schlüssel `data.api` wird auf die Zeichenfolge `CreateFile` oder `FlushWithClose` festgelegt.

* Der Schlüssel `contentOffset` wird in das Dataset eingeschlossen.

> [!NOTE]
> Wenn Anwendungen den Vorgang `PutBlockList` verwenden, um ein neues Blob in das Konto hochzuladen, sind diese Änderungen nicht in den Daten enthalten.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Ereignis „Microsoft.Storage.BlobDeleted“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Ereignis „Microsoft.Storage.BlobDeleted“ (Data Lake Storage Gen2)

Wenn das Blob Storage-Konto über einen hierarchischen Namespace verfügt, sehen die Daten, mit Ausnahme dieser Änderungen, in etwa wie im vorherigen Beispiel aus:

* Der Schlüssel `dataVersion` wird auf den Wert `2` festgelegt.

* Der Schlüssel `data.api` wird auf die Zeichenfolge `DeleteFile` festgelegt.

* Die Schlüssel `url` enthält den Pfad `dfs.core.windows.net`.

> [!NOTE]
> Wenn Anwendungen den Vorgang `DeleteBlob` verwenden, um ein Blob aus dem Konto zu löschen, sind diese Änderungen nicht in den Daten enthalten.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Ereignis „Microsoft.Storage.BlobRenamed“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Ereignis „Microsoft.Storage.DirectoryCreated“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Ereignis „Microsoft.Storage.DirectoryRenamed“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Ereignis „Microsoft.Storage.DirectoryDeleted“

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | Beschreibung |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | Objekt (object) | Ereignisdaten für Blob Storage. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| api | Zeichenfolge | Der Vorgang, durch den das Ereignis ausgelöst wurde. |
| clientRequestId | Zeichenfolge | Vom Client bereitgestellte Anforderungs-ID für den Speicher-API-Vorgang. Diese ID kann zur Korrelation mit Azure Storage-Diagnoseprotokollen anhand des Felds „client-request-id“ in den Protokollen verwendet und in Clientanforderungen mit dem Header „x-ms-client-request-id“ bereitgestellt werden. Informationen finden Sie unter [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Storage Analytics-Protokollformat). |
| requestId | Zeichenfolge | Vom Dienst generierte Anforderungs-ID für den Speicher-API-Vorgang. Kann zum Korrelieren mit Azure Storage-Diagnoseprotokolle mithilfe des Felds „request-id-header“ in den Protokollen verwendet werden, und wird vom einleitenden API-Aufruf im „x-ms-request-id“-Header zurückgegeben. Informationen finden Sie unter [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Storage Analytics-Protokollformat). |
| eTag | Zeichenfolge | Der Wert, mit dem Sie Vorgänge bedingt ausführen können. |
| contentType | Zeichenfolge | Der für das Blob angegebene Inhaltstyp. |
| contentLength | integer | Die Größe des Blobs in Byte. |
| blobType | Zeichenfolge | Der Blobtyp. Gültige Werte sind „BlockBlob“ oder „PageBlob“. |
| contentOffset | number | Das Offset (in Bytes) eines Schreibvorgangs zu dem Zeitpunkt, zu dem die ereignisauslösende Anwendung das Schreiben in die Datei abgeschlossen hat. <br>Nur relevant für Ereignisse, die für Blob Storage-Konten mit einem hierarchischen Namespace ausgelöst wurden.|
| destinationUrl |Zeichenfolge | Die URL der Datei, die nach Abschluss des Vorgangs vorhanden ist. Wenn also beispielsweise eine Datei umbenannt wird, enthält die Eigenschaft `destinationUrl` die URL des neuen Dateinamens. <br>Nur relevant für Ereignisse, die für Blob Storage-Konten mit einem hierarchischen Namespace ausgelöst wurden.|
| sourceUrl |Zeichenfolge | Die URL der Datei, die vor dem erfolgten Vorgang vorhanden ist. Wenn also beispielsweise eine Datei umbenannt wird, enthält `sourceUrl` die URL des ursprünglichen Dateinamens vor dem Umbenennungsvorgang. <br>Nur relevant für Ereignisse, die für Blob Storage-Konten mit einem hierarchischen Namespace ausgelöst wurden. |
| url | Zeichenfolge | Der Pfad des Blobs. <br>Wenn der Client eine Blob-REST-API verwendet, hat die URL die folgende Struktur: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>`. <br>Wenn der Client eine Data Lake Storage-REST-API verwendet, hat die URL die folgende Struktur: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>`. |
| recursive | Zeichenfolge | `True`, um den Vorgang für alle untergeordneten Verzeichnisse auszuführen; andernfalls `False`. <br>Nur relevant für Ereignisse, die für Blob Storage-Konten mit einem hierarchischen Namespace ausgelöst wurden. |
| sequencer | Zeichenfolge | Ein nicht transparenter Zeichenfolgenwert, der die logische Reihenfolge von Ereignissen für einen bestimmten Blobnamen darstellt.  Benutzer können anhand des standardmäßigen Zeichenfolgenvergleichs die relative Reihenfolge von zwei Ereignissen unter dem gleichen Blobnamen verstehen. |
| storageDiagnostics | Objekt (object) | Diagnosedaten, die gelegentlich vom Azure Storage-Dienst einbezogen werden. Falls vorhanden, sollten sie vom Ereignisconsumer ignoriert werden. |

## <a name="tutorials-and-how-tos"></a>Tutorials und Vorgehensweisen
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure CLI zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure PowerShell zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Erstellen und Weiterleiten von Blob Storage-Ereignissen mit dem Azure-Portal](blob-event-quickstart-portal.md) | Zeigt die Verwendung des Portals zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Azure CLI: Abonnieren von Ereignissen für ein Blobspeicherkonto](./scripts/event-grid-cli-blob.md) | Beispielskript, mit dem Ereignisse für ein Blobspeicherkonto abonniert werden. Sendet das Ereignis an einen WebHook. |
| [PowerShell: Abonnieren von Ereignissen für ein Blobspeicherkonto](./scripts/event-grid-powershell-blob.md) | Beispielskript, mit dem Ereignisse für ein Blobspeicherkonto abonniert werden. Sendet das Ereignis an einen WebHook. |
| [Resource Manager-Vorlage: Erstellen eines Blobspeichers und Abonnements](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Stellt ein Azure Blob Storage-Konto bereit und abonniert Ereignisse für dieses Speicherkonto. Sendet Ereignisse an einen WebHook. |
| [Übersicht: Reaktion auf Blob Storage-Ereignisse](../storage/blobs/storage-blob-event-overview.md) | Übersicht über die Integration von Blob Storage in Event Grid. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Eine Einführung zum Arbeiten mit Blob Storage-Ereignissen finden Sie unter [Routen von Blob Storage-Ereignissen -– Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
