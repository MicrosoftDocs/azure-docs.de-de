---
title: Reagieren auf Azure Blob Storage-Ereignisse | Microsoft-Dokumentation
description: Mithilfe von Azure Event Grid können Sie Blob Storage-Ereignisse abonnieren und darauf reagieren. Lernen Sie das Ereignismodell, das Filtern von Ereignissen und Methoden zum Nutzen von Ereignissen kennen.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: 00a7a081f29458ae81d8d8ea4dd8f7abef42f78f
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519007"
---
# <a name="reacting-to-blob-storage-events"></a>Reaktion auf Blob Storage-Ereignisse

Azure Storage-Ereignisse ermöglichen es Anwendungen, auf Ereignisse wie das Erstellen und Löschen von Blobs zu reagieren. Dies geschieht ohne komplizierten Code oder teure und ineffiziente Abrufdienste. Und am besten dabei: Sie bezahlen nur für die tatsächliche Nutzung.

Blob Storage-Ereignisse werden mithilfe von [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) an Abonnenten wie Azure Functions, Azure Logic Apps oder sogar an Ihren eigenen HTTP-Listener gepusht. Event Grid sorgt über umfassende Wiederholungsrichtlinien und unzustellbare Nachrichten für eine zuverlässige Ereignisübermittlung an Ihre Anwendungen.

Im Artikel zum [Blob Storage-Ereignisschema ](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) finden Sie eine vollständige Liste der Ereignisse, die von Blob Storage unterstützt werden.

Gängige Blob Storage-Ereignisszenarien enthalten Bild- oder Videobearbeitung, Suchindizierung oder dateiorientierte Workflows. Asynchrone Dateiuploads eignen sich hervorragend für Ereignisse. Wenn Änderungen selten sind, aber Ihr Szenario die sofortige Reaktion erfordert, kann die ereignisbasierte Architektur besonders effizient sein.

Wenn Sie Blob Storage-Ereignisse ausprobieren möchten, informieren Sie sich in folgenden Schnellstartartikeln:

|Gewünschtes Tool:    |In diesem Artikel finden Sie weitere Informationen: |
|--|-|
|Azure-Portal    |[Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen Webendpunkt über das Azure-Portal](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Schnellstart: Weiterleiten von Speicherereignissen an einen Webendpunkt per PowerShell](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Schnellstart: Weiterleiten von Speicherereignissen an einen Webendpunkt per Azure CLI](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Ausführliche Beispiele für die Reaktion auf Blobspeicherereignisse mithilfe von Azure-Funktionen finden Sie in den folgenden Artikeln:

- [Tutorial: Verwenden von Azure Data Lake Storage Gen2-Ereignissen zum Aktualisieren einer Databricks Delta-Tabelle](data-lake-storage-events.md).
- [Tutorial: Automatisieren der Größenänderung von hochgeladenen Bildern mit Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> Nur Speicherkonten vom Typ **StorageV2 (universell, Version 2)** , **BlockBlobStorage** und **BlobStorage** unterstützen die Ereignisintegration. **Storage (Universell V1)** unterstützt *nicht* die Integration in Event Grid.

## <a name="the-event-model"></a>Ereignismodell

Event Grid verwendet [Ereignisabonnements](../../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Diese Abbildung veranschaulicht die Beziehung zwischen Ereignisherausgebern, Ereignisabonnements und Ereignishandlern.

![Event Grid-Modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Abonnieren Sie zunächst einen Endpunkt für ein Ereignis. Sobald ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt.

Im Artikel zum [Blob Storage-Ereignisschema](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) finden Sie:

> [!div class="checklist"]
> * Eine vollständige Liste der Blob Storage-Ereignisse und Informationen dazu, wie jedes Ereignis ausgelöst wird
> * Ein Beispiel dafür, welche Daten von Event Grid zu den einzelnen Ereignissen gesendet werden
> * Angaben zum Zweck der jeweiligen Schlüssel-Wert-Paare, die in den Daten enthalten sind

## <a name="filtering-events"></a>Filtern von Ereignissen

Blob-[Ereignisse können gefiltert werden](/cli/azure/eventgrid/event-subscription) durch den Ereignistyp, den Containernamen oder den Namen des Objekts, das erstellt bzw. gelöscht wurde. Weil Filter im Event Grid den Anfang oder das Ende des Betreffs abgleichen, werden Ereignisse mit einem übereinstimmenden Betreff an den Abonnenten gesendet.

Weitere Informationen zum Anwenden von Filtern finden Sie unter [Filtern von Ereignissen für Event Grid](../../event-grid/how-to-filter-events.md).

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
> * Da Nachrichten mit Verzögerung eintreffen können, verwenden Sie die etag-Felder, um zu verstehen, ob Ihre Informationen zu Objekten weiterhin auf dem neuesten Stand sind. Informationen zur Verwendung des etag-Felds finden Sie unter [Verwalten von Parallelität in Blob Storage](../common/storage-concurrency.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage).
> * Da Nachrichten in falscher Reihenfolge eintreffen können, verwenden Sie die sequencer-Felder, um die Reihenfolge der Ereignisse für ein bestimmtes Objekt zu verstehen. Das sequencer-Feld ist ein Zeichenfolgenwert, der die logische Reihenfolge von Ereignissen für einen bestimmten Blobnamen darstellt. Sie können anhand des standardmäßigen Zeichenfolgenvergleichs die relative Reihenfolge von zwei Ereignissen unter dem gleichen Blobnamen nachvollziehen.
> * Storage-Ereignisse gewährleisten eine At-Least-Once-Zustellung an Abonnenten, wodurch sichergestellt wird, dass alle Nachrichten ausgegeben werden. Aufgrund von Wiederholungen oder abhängig von der Verfügbarkeit von Abonnements kommt es jedoch möglicherweise gelegentlich zu doppelten Nachrichten. Weitere Informationen zur Übermittlung und Wiederholung von Nachrichten finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](../../event-grid/delivery-and-retry.md).
> * Verwenden Sie das blobType-Feld, um zu verstehen, welche Arten von Vorgängen für das Blob zulässig sind, und welche Typen von Clientbibliotheken Sie für den Zugriff auf das Blob verwenden sollten. Gültige Werte sind `BlockBlob` oder `PageBlob`. 
> * Verwenden Sie das url-Feld mit `CloudBlockBlob`- und `CloudAppendBlob`-Konstruktor für den Zugriff auf das Blob.
> * Ignorieren Sie Felder, die Sie nicht verstehen. So müssen Sie sich nicht mit neuen Features auseinandersetzen, die in der Zukunft hinzugefügt werden könnten.
> * Wenn Sie sicherstellen möchten, dass das **Microsoft.Storage.BlobCreated**-Ereignis nur ausgelöst wird, nachdem ein Blockblob vollständig committed wurde, filtern Sie das Ereignis nach den REST-API-Aufrufen `CopyBlob`, `PutBlob`, `PutBlockList` oder `FlushWithClose`. Bei diesen API-Aufrufen wird das Ereignis **Microsoft.Storage.BlobCreated** erst ausgelöst, nachdem Daten vollständig in einem Blockblob committet wurden. Informationen zum Erstellen eines Filters finden Sie unter [Filtern von Ereignissen für Event Grid](../../event-grid/how-to-filter-events.md).


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid , und probieren Sie Blob Storage-Ereignisse aus:

- [Einführung in Azure Event Grid](../../event-grid/overview.md)
- [Blob Storage-Ereignisschema](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt](storage-blob-event-quickstart.md)