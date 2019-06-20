---
title: Reagieren auf Azure Blob Storage-Ereignisse | Microsoft-Dokumentation
description: Abonnieren Sie Blob Storage-Ereignisse mit Azure Event Grid.
services: storage,event-grid
author: normesta
ms.author: normesta
ms.reviewer: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 146b33c1a52838279f000a7f793902e2f35dbfaa
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826491"
---
# <a name="reacting-to-blob-storage-events"></a>Reaktion auf Blob Storage-Ereignisse

Azure Storage-Ereignisse bieten Anwendungen die Möglichkeit, auf das Erstellen und Löschen von Blobs mithilfe moderner serverloser Architekturen zu reagieren. Dies geschieht ohne komplizierten Code oder teure und ineffiziente Abrufdienste.  Stattdessen werden die Ereignisse per Push über [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) an Abonnenten wie [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) oder sogar Ihren eigenen benutzerdefinierten HTTP-Listener übertragen, und Sie zahlen nur für das, was Sie tatsächliche verwenden.

Blob Storage-Ereignisse werden zuverlässig an den Event Grid-Dienst gesendet, der zuverlässige Zustellungsdienste für Ihre Anwendungen durch umfangreiche Wiederholungsrichtlinien und Zustellung unzustellbarer Nachrichten bereitstellt. Weitere Informationen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Gängige Blob Storage-Ereignisszenarien enthalten Bild- oder Videobearbeitung, Suchindizierung oder dateiorientierte Workflows.  Asynchrone Dateiuploads eignen sich hervorragend für Ereignisse.  Wenn Änderungen selten sind, aber Ihr Szenario die sofortige Reaktion erfordert, kann die ereignisbasierte Architektur besonders effizient sein.

Ein kurzes Beispiel finden Sie unter [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit CLI](storage-blob-event-quickstart.md) oder [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit PowerShell](storage-blob-event-quickstart-powershell.md). 

![Event Grid-Modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob-Speicherkonten
Blob Storage-Ereignisse stehen in Speicherkonten vom Typ „Allgemein v2“ und in Blob Storage-Konten zur Verfügung. Speicherkonten vom Typ **Allgemein v2** unterstützen alle Features für alle Speicherdienste, z.B. Blob Storage, Files, Queue Storage und Table Storage. **Blob Storage-Konten** sind spezielle Speicherkonten und dienen dazu, unstrukturierte Daten als Blobs (Objekte) in Azure Storage zu speichern. BLOB-Speicherkonten sind wie allgemeine Speicherkonten und besitzen die gleichen Haltbarkeits-, Verfügbarkeits-, Skalierbarkeits- und Leistungseigenschaften, die Sie schon heute verwenden – einschließlich vollständiger API-Konsistenz für Blockblobs und Anfügeblobs. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

## <a name="available-blob-storage-events"></a>Verfügbare Blob Storage-Ereignisse
Event Grid verwendet [Ereignisabonnements](../../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten.  Blob Storage-Ereignisabonnements können zwei Arten von Ereignissen enthalten:  

> |Veranstaltungsname|BESCHREIBUNG|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Wird ausgelöst, wenn ein Blob erstellt oder durch die Vorgänge `PutBlob`, `PutBlockList` oder `CopyBlob` ersetzt wird.|
> |`Microsoft.Storage.BlobDeleted`|Wird ausgelöst, wenn ein Blob, durch den Vorgang `DeleteBlob` gelöscht wird.|

## <a name="event-schema"></a>Ereignisschema
Blob Storage-Ereignisse enthalten alle Informationen, die Sie für die Reaktion auf Änderungen in Ihren Daten benötigen.  Sie erkennen ein Blob Storage-Ereignis daran, dass die eventType-Eigenschaft mit „Microsoft.Storage“ beginnt. Weitere Informationen zur Verwendung von Event Grid-Ereigniseigenschaften finden Sie unter [Event Grid-Ereignisschema](../../event-grid/event-schema.md).  

> |Eigenschaft|Typ|BESCHREIBUNG|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Thema|Zeichenfolge|Vollständige Azure Resource Manager-ID des Speicherkontos, das das Ereignis ausgibt.|
> |subject|Zeichenfolge|Der relative Ressourcenpfad zu dem Objekt, das der Betreff des Ereignisses ist, in dem gleichen erweiterten Azure Resource Manager-Format, mit dem Speicherkonten, Dienste und Container für RBAC von Azure beschrieben werden.  Dieses Format schließt einen Blobnamen ein, bei dem die Groß-/Kleinschreibung beibehalten wird.|
> |eventTime|Zeichenfolge|Datum und Uhrzeit der Ereignisgenerierung im ISO 8601-Format.|
> |eventType|Zeichenfolge|„Microsoft.Storage.BlobCreated“ oder „Microsoft.Storage.BlobDeleted“|
> |id|Zeichenfolge|Eindeutiger Bezeichner dieses Ereignisses|
> |dataVersion|Zeichenfolge|Die Schemaversion des Datenobjekts.|
> |metadataVersion|Zeichenfolge|Die Schemaversion der Eigenschaften oberster Ebene.|
> |data|object|Sammlung Blob Storage-spezifischer Ereignisdaten|
> |data.contentType|Zeichenfolge|Der Inhaltstyp des Blobs, wie er im Content-Type-Header aus dem Blob zurückgegeben werden würde.|
> |data.contentLength|number|Die Größe des Blobs im Ganzzahlformat, eine Anzahl von Bytes darstellend, wie sie im Content-Length-Header aus dem Blob zurückgegeben werden würde.  Gesendet mit BlobCreated-Ereignis, jedoch nicht mit BlobDeleted.|
> |data.url|Zeichenfolge|Die URL des Objekts, das der Betreff des Ereignisses ist.|
> |data.eTag|Zeichenfolge|Das ETag des Objekts, wenn dieses Ereignis ausgelöst wird.  Für das Ereignis BlobDeleted nicht verfügbar.|
> |data.api|Zeichenfolge|Der Name des API-Vorgangs, der dieses Ereignis ausgelöst hat. Für BlobCreated-Ereignisse ist dieser Wert „PutBlob“, „PutBlockList“ oder „CopyBlob“. Für BlobDeleted-Ereignisse ist dieser Wert „DeleteBlob“. Diese Werte sind die gleichen API-Namen, die in den Azure Storage-Diagnoseprotokollen vorhanden sind. Informationen finden Sie unter [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|Zeichenfolge|Ein nicht transparenter Zeichenfolgenwert, der die logische Reihenfolge von Ereignissen für einen bestimmten Blobnamen darstellt.  Benutzer können anhand des standardmäßigen Zeichenfolgenvergleichs die relative Reihenfolge von zwei Ereignissen unter dem gleichen Blobnamen verstehen.|
> |data.requestId|Zeichenfolge|Dienstgenerierte Anforderungs-ID für den Speicher-API-Vorgang. Kann zum Korrelieren mit Azure Storage-Diagnoseprotokolle mithilfe des Felds „request-id-header“ in den Protokollen verwendet werden, und wird vom einleitenden API-Aufruf im „x-ms-request-id“-Header zurückgegeben. Informationen finden Sie unter [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Storage Analytics-Protokollformat).|
> |data.clientRequestId|Zeichenfolge|Vom Client bereitgestellte Anforderungs-ID für den Speicher-API-Vorgang. Kann zum Korrelieren mit Azure Storage-Diagnoseprotokollen mithilfe des Felds „client-request-id“ in den Protokollen verwendet und in Clientanforderungen mit dem „x-ms-client-request-id“-Header bereitgestellt werden. Informationen finden Sie unter [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Storage Analytics-Protokollformat). |
> |data.storageDiagnostics|object|Diagnosedaten, die gelegentlich vom Azure Storage-Dienst einbezogen werden. Falls vorhanden, sollten sie vom Ereignisconsumer ignoriert werden.|
|data.blobType|Zeichenfolge|Der Typ des Blobs. Gültige Werte sind „BlockBlob“ oder „PageBlob“.| 

Hier sehen Sie ein Beispiel für ein BlobCreated-Ereignis:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Weitere Informationen finden Sie unter [Azure Blob Storage](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtern von Ereignissen
Blobereignisabonnements können basierend auf dem Ereignistyp sowie auf dem Container- und Blobnamen des Objekts, das erstellt oder gelöscht wurde, gefiltert werden.  Auf Ereignisabonnements können während der [Erstellung](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) des Ereignisabonnements oder [zu einem späteren Zeitpunkt](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) Filter angewendet werden. Betrefffilter funktionieren in Event Grid auf Grundlage von „beginnt mit“- und „endet mit“-Übereinstimmungen, sodass Ereignisse mit einem übereinstimmenden Betreff an den Abonnenten übermittelt werden. 

Der Betreff von Blob Storage-Ereignissen verwendet das Format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Zur Übereinstimmung mit allen Ereignissen für ein Speicherkonto können Sie den Betrefffilter leer lassen.

Verwenden Sie zur Übereinstimmung mit Ereignissen aus Blobs, die in einem Satz von Containern erstellt wurden und ein gemeinsames Präfix verwenden, einen `subjectBeginsWith`-Filter wie:

```
/blobServices/default/containers/containerprefix
```

Verwenden Sie zur Übereinstimmung mit Ereignissen aus Blobs, die in einem bestimmten Container erstellt wurden, einen `subjectBeginsWith`-Filter wie:

```
/blobServices/default/containers/containername/
```

Verwenden Sie zur Übereinstimmung mit Ereignissen aus Blobs, die in einem bestimmten Container erstellt wurden und ein gemeinsames Blobnamenspräfix verwenden, einen `subjectBeginsWith`-Filter wie:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Verwenden Sie zur Übereinstimmung mit Ereignissen aus Blobs, die in einem bestimmten Container erstellt wurden und ein gemeinsames Blobsuffix verwenden, einen `subjectEndsWith`-Filter wie „.log“ oder „.jpg“. Weitere Informationen finden Sie unter [Begriffe in Azure Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Methoden zum Nutzen von Ereignissen
Anwendungen, die Blob Storage-Ereignisse behandeln, sollten einige bewährte Methoden nutzen:
> [!div class="checklist"]
> * Da mehrere Abonnements zum Weiterleiten von Ereignissen an den gleichen Ereignishandler konfiguriert werden können, ist es wichtig, nicht davon auszugehen, dass Ereignisse aus einer bestimmten Quelle stammen, sondern das Thema der Nachricht zu überprüfen, um sicherzustellen, dass es aus dem Speicherkonto stammt, das Sie erwarten.
> * Überprüfen Sie auf ähnliche Weise, ob Sie auf die Verarbeitung des eventType vorbereitet sind, und gehen Sie nicht davon aus, dass alle Ereignisse, die Sie empfangen, den von Ihnen erwarteten Typen entsprechen.
> * Da Nachrichten in falscher Reihenfolge und mit Verzögerung eintreffen können, verwenden Sie die etag-Felder, um zu verstehen, ob Ihre Informationen zu Objekten weiterhin auf dem neuesten Stand ist.  Verwenden Sie auch die sequencer-Felder, um die Reihenfolge der Ereignisse für ein bestimmtes Objekt zu verstehen.
> * Verwenden Sie das blobType-Feld, um zu verstehen, welche Arten von Vorgängen für das Blob zulässig sind, und welche Typen von Clientbibliotheken Sie für den Zugriff auf das Blob verwenden sollten. Gültige Werte sind `BlockBlob` oder `PageBlob`. 
> * Verwenden Sie das url-Feld mit `CloudBlockBlob`- und `CloudAppendBlob`-Konstruktor für den Zugriff auf das Blob.
> * Ignorieren Sie Felder, die Sie nicht verstehen. So müssen Sie sich nicht mit neuen Features auseinandersetzen, die in der Zukunft hinzugefügt werden könnten.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid , und probieren Sie Blob Storage-Ereignisse aus:

- [Einführung in Azure Event Grid](../../event-grid/overview.md)
- [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt](storage-blob-event-quickstart.md)
