---
title: Azure Storage Analytics-Protokollierung
description: Mithilfe von Storage Analytics können Sie Details zu Azure Storage-Anforderungen protokollieren. Hier erfahren Sie, welche Anforderungen protokolliert werden, wie Protokolle gespeichert werden, wie die Storage-Protokollierung aktiviert wird, und vieles mehr.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 217a804b0155d7886a068283f8669ace0bc81856
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218518"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-Protokollierung

Storage Analytics protokolliert ausführliche Informationen zu erfolgreichen und nicht erfolgreichen Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert.

> [!NOTE]
> Wir empfehlen, Azure Storage-Protokolle in Azure Monitor (statt Storage Analytics-Protokollen) zu verwenden. Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen für die öffentliche Cloud für Vorschautests zur Verfügung. Diese Vorschau unterstützt Protokolle für Blobdateien (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen und Tabellen. Weitere Informationen finden Sie in einem der folgenden Artikel:
>
> - [Überwachen von Azure Blob Storage](../blobs/monitor-blob-storage.md)
> - [Überwachen von Azure Files](../files/storage-files-monitoring.md)
> - [Überwachen von Azure Queue Storage](../queues/monitor-queue-storage.md)
> - [Überwachen von Azure-Tabellenspeicher](../tables/monitor-table-storage.md)

 Die Storage Analytics-Protokollierung ist für Ihr Speicherkonto nicht standardmäßig aktiviert. Sie können sie im [Azure-Portal](https://portal.azure.com/) oder mithilfe von PowerShell oder der Azure CLI aktivieren. Eine Schrittanleitung finden Sie unter [Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch)](manage-storage-analytics-logs.md). 

Sie können Azure Storage Analytics auch programmgesteuert über die REST-API oder Clientbibliothek aktivieren. Über die Vorgänge [Get Blob Service Properties](/rest/api/storageservices/Blob-Service-REST-API), [Get Queue Service Properties](/rest/api/storageservices/Get-Queue-Service-Properties) und [Get Table Service Properties](/rest/api/storageservices/Get-Table-Service-Properties) können Sie Storage Analytics für jeden Dienst aktivieren. Ein Beispiel für die Aktivierung von Storage Analytics-Protokollen mithilfe von .NET finden Sie unter [Aktivieren von Protokollen](manage-storage-analytics-logs.md).

 Protokolleinträge werden nur erstellt, wenn Anforderungen für den Dienstendpunkt gestellt wurden. Wenn ein Speicherkonto beispielsweise Aktivität im Blobendpunkt, jedoch nicht im Tabellen- oder Warteschlangenendpunkt aufweist, werden nur Protokolle für den Blob-Dienst erstellt.

> [!NOTE]
>  Die Storage Analytics-Protokollierung ist derzeit nur für Blob-, Warteschlangen- und Tabellenspeicherdienste verfügbar. Storage Analytics-Protokollierung ist außerdem für Premium-Leistung von [BlockBlobStorage-Konten](../blobs/storage-blob-create-account-block-blob.md) verfügbar. Allerdings ist sie für allgemeine v2-Konten (GPv2) mit Premium-Leistung nicht verfügbar.

## <a name="requests-logged-in-logging"></a>Erfasste Anforderungen bei der Protokollierung
### <a name="logging-authenticated-requests"></a>Protokollierung authentifizierter Anforderungen

 Die folgenden Typen authentifizierter Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen
- Fehlerhafte Anforderungen, einschließlich Timeout-, Drosselungs-, Netzwerk- und Autorisierungsfehler sowie anderer Fehler
- Anforderungen mithilfe einer SAS (Shared Access Signature) oder mit OAuth, einschließlich fehlerhafter und erfolgreicher Anforderungen
- Anforderungen von Analysedaten

  Anforderungen, die durch die Speicheranalyse selbst erfolgen, z. B. Protokollerstellung oder -löschung, werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) und [Protokollformat der Speicheranalyse](/rest/api/storageservices/storage-analytics-log-format) dokumentiert.

### <a name="logging-anonymous-requests"></a>Protokollieren anonymer Anforderungen

 Die folgenden Typen anonymer Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen
- Serverfehler
- Timeoutfehler für Client und Server
- Mit Fehlercode 304 (Nicht geändert) misslungene GET-Anforderungen

  Alle anderen misslungenen anonymen Anforderungen werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) und [Protokollformat der Speicheranalyse](/rest/api/storageservices/storage-analytics-log-format) dokumentiert.

## <a name="how-logs-are-stored"></a>Wie Protokolle gespeichert werden

Alle Protokolle werden in Blockblobs in einem Container mit der Bezeichnung `$logs` gespeichert, der automatisch erstellt wird, wenn Storage Analytics für ein Speicherkonto aktiviert ist. Der Container `$logs` befindet sich im Blobnamespace des Speicherkontos, z. B. `http://<accountname>.blob.core.windows.net/$logs`. Dieser Container kann nicht gelöscht werden, nachdem die Speicheranalyse aktiviert wurde. Sein Inhalt kann hingegen gelöscht werden. Wenn Sie über das Tool zum Durchsuchen des Speichers direkt zu diesem Container navigieren, werden alle Blobs angezeigt, die Ihre Protokollierungsdaten enthalten.

> [!NOTE]
>  Der Container `$logs` wird nicht angezeigt, wenn ein Containerauflistungsvorgang durchgeführt wird, z. B. der Vorgang „List Containers“. Der Zugriff auf ihn muss direkt erfolgen. Beispielsweise können Sie den Vorgang „List Blobs“ verwenden, um auf die Blobs im Container `$logs` zuzugreifen.

Sobald Anforderungen protokolliert werden, lädt die Speicheranalyse Zwischenergebnisse als Blöcke hoch. Die Speicheranalyse führt regelmäßig Commits dieser Blöcke aus und macht sie als BLOB verfügbar. Aufgrund der Häufigkeit, mit der der Speicherdienst die Protokollwriter leert, kann es bis zu einer Stunde dauern, bis die Protokolldaten in den Blobs im Container **$logs** angezeigt werden. Für Protokolle, die in derselben Stunde erstellt werden, können doppelte Einträge vorhanden sein. Sie können feststellen, ob ein Datensatz ein Duplikat ist, indem Sie **RequestId** und **Vorgangsnummer** prüfen.

Bei einer großen Menge an Protokolldaten mit mehreren Dateien pro Stunde können Sie anhand der Blobmetadaten ermitteln, welche Daten das Protokoll enthält, indem Sie die Blobmetadatenfelder untersuchen. Dies ist auch deshalb nützlich, weil es in manchen Fällen zu Verzögerungen beim Schreiben von Daten in die Protokolldateien kommen kann: Die Blobmetadaten bieten eine genauere Angabe des Blobinhalts als der Blobname.

Mit den meisten Tools zum Durchsuchen des Speichers können Sie die Metadaten von Blobs anzeigen. Zudem können Sie diese Informationen über PowerShell oder programmgesteuert anzeigen. Der folgende PowerShell-Codeausschnitt ist ein Beispiel für die Filterung der Liste mit Protokollblobs nach dem Namen, um einen Zeitpunkt anzugeben, und nach Metadaten, um nur die Protokolle zu identifizieren, die **write**-Vorgänge enthalten.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Informationen zum programmgesteuerten Auflisten von Blobs finden Sie unter [Auflisten von Blobressourcen](/rest/api/storageservices/Enumerating-Blob-Resources) und [Festlegen und Abrufen von Eigenschaften und Metadaten für Blobressourcen](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Benennungskonventionen für Protokolle

 Jedes Protokoll wird im folgenden Format geschrieben:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 In der folgenden Tabelle werden alle Attribute im Protokollnamen beschrieben:

|attribute|BESCHREIBUNG|
|---------------|-----------------|
|`<service-name>`|Der Name des Speicherdiensts. Beispiel: `blob`, `table` oder `queue`|
|`YYYY`|Die vierstellige Jahresangabe für das Protokoll. Beispiel: `2011`|
|`MM`|Die zweistellige Monatsangabe für das Protokoll. Beispiel: `07`|
|`DD`|Die zweistellige Tagesangabe für das Protokoll. Beispiel: `31`|
|`hh`|Die zweistellige Angabe der Stunde des Protokollbeginns im 24-Stunden-Format (UTC). Beispiel: `18`|
|`mm`|Die zweistellige Zahl, mit der die Anfangsminute der Protokolle angegeben wird. **Hinweis:**  Dieser Wert wird in der aktuellen Version von Storage Analytics nicht unterstützt und ist immer `00`.|
|`<counter>`|Ein nullbasierter Zähler mit sechs Stellen, der die Anzahl der im Zeitraum von einer Stunde für den Speicherdienst generierten Protokoll-BLOBs angibt. Dieser Zähler beginnt bei `000000`. Beispiel: `000001`|

 In dem folgenden vollständigen Beispielprotokollnamen sind alle oben aufgeführten Beispiele enthalten:

 `blob/2011/07/31/1800/000001.log`

 Der folgende Beispiel-URI kann für den Zugriff auf das vorherige Protokoll verwendet werden:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Wenn eine Speicheranforderung protokolliert wird, gibt der resultierende Protokollname die Stunde wieder, zu der der angeforderte Vorgang abgeschlossen wurde. Wenn eine GetBlob-Anforderung z. B. am 31.7.2011 um 18:30 Uhr abgeschlossen wurde, wird das Protokoll mit dem folgenden Präfix geschrieben: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Protokollmetadaten

 Alle Protokoll-BLOBs werden mit Metadaten gespeichert, mit deren Hilfe die im BLOB enthaltenen Protokollierungsdaten ermittelt werden können. In der folgenden Tabelle werden die einzelnen Metadatenattribute beschrieben:

|attribute|BESCHREIBUNG|
|---------------|-----------------|
|`LogType`|Gibt an, ob das Protokoll Informationen über Lese-, Schreib- oder Löschvorgänge enthält. Dieser Wert kann einen Typ oder eine durch Kommas getrennte Kombination aller drei Typen enthalten.<br /><br /> Beispiel 1: `write`<br /><br /> Beispiel 2: `read,write`<br /><br /> Beispiel 3: `read,write,delete`|
|`StartTime`|Der erste Zeitpunkt eines Eintrags im Protokoll, im Format `YYYY-MM-DDThh:mm:ssZ`. Beispiel: `2011-07-31T18:21:46Z`|
|`EndTime`|Der letzte Zeitpunkt eines Eintrags im Protokoll, im Format `YYYY-MM-DDThh:mm:ssZ`. Beispiel: `2011-07-31T18:22:09Z`|
|`LogVersion`|Die Version des Protokollformats.|

 In der folgenden Liste werden alle Beispielmetadaten unter Verwendung der obigen Beispiele dargestellt:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch)](manage-storage-analytics-logs.md)
* [Protokollformat der Speicheranalyse](/rest/api/storageservices/storage-analytics-log-format)
* [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriken von Storage Analytics (klassisch)](storage-analytics-metrics.md)
