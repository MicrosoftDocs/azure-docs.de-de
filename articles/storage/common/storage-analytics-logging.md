---
title: Azure Storage Analytics-Protokollierung
description: Erfahren Sie, wie Sie Details zu Anforderungen für Azure Storage protokollieren.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: a77cf20be30361abf6590dbd53bdb07c327eb9d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204999"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-Protokollierung

Storage Analytics protokolliert ausführliche Informationen zu erfolgreichen und nicht erfolgreichen Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert.

 Die Storage Analytics-Protokollierung ist für Ihr Speicherkonto nicht standardmäßig aktiviert. Sie können sie im [Azure-Portal](https://portal.azure.com/) aktivieren. Details finden Sie unter [Überwachen eines Speicherkontos im Azure-Portal](/azure/storage/storage-monitor-storage-account). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge [Get Blob Service Properties](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Get Queue Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties) und [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) können Sie Storage Analytics für jeden Dienst aktivieren.

 Protokolleinträge werden nur erstellt, wenn Anforderungen für den Dienstendpunkt gestellt wurden. Wenn ein Speicherkonto beispielsweise Aktivität im Blobendpunkt, jedoch nicht im Tabellen- oder Warteschlangenendpunkt aufweist, werden nur Protokolle für den Blob-Dienst erstellt.

> [!NOTE]
>  Die Storage Analytics-Protokollierung ist derzeit nur für Blob-, Warteschlangen- und Tabellenspeicherdienste verfügbar. Ein Premium-Speicherkonto wird nicht unterstützt.

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
 Get-AzureStorageBlob -Container '$logs' |  
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

Informationen zum programmgesteuerten Auflisten von Blobs finden Sie unter [Auflisten von Blobressourcen](https://msdn.microsoft.com/library/azure/hh452233.aspx) und [Festlegen und Abrufen von Eigenschaften und Metadaten für Blobressourcen](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Benennungskonventionen für Protokolle

 Jedes Protokoll wird im folgenden Format geschrieben:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 In der folgenden Tabelle werden alle Attribute im Protokollnamen beschrieben:

|Attribut|BESCHREIBUNG|
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

|Attribut|BESCHREIBUNG|
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

## <a name="enable-storage-logging"></a>Aktivieren der Speicherprotokollierung

Sie können die Speicherprotokollierung im Azure-Portal, über PowerShell und mit Storage SDKs aktivieren.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Aktivieren der Speicherprotokollierung im Azure-Portal  

Verwenden Sie im Azure-Portal zum Steuern der Speicherprotokollierung das Blatt **Diagnoseeinstellungen (klassisch)** , auf das im Abschnitt **Überwachung (klassisch)** des **Menüblatts** eines Speicherkontos zugegriffen werden kann.

Sie können die zu protokollierenden Speicherdienste und den Aufbewahrungszeitraum (in Tagen) für die protokollierten Daten angeben.  

### <a name="enable-storage-logging-using-powershell"></a>Aktivieren der Speicherprotokollierung mithilfe von PowerShell  

 Sie können PowerShell auf Ihrem lokalen Computer zum Konfigurieren der Speicherprotokollierung in Ihrem Speicherkonto verwenden, indem Sie das Azure PowerShell-Cmdlet **Get-AzureStorageServiceLoggingProperty** ausführen, um die aktuellen Einstellungen abzurufen. Mithilfe des Cmdlets **Set-AzureStorageServiceLoggingProperty** können Sie die aktuellen Einstellungen ändern.  

 Die Cmdlets, über die die Speicherprotokollierung gesteuert wird, verwenden einen **LoggingOperations**-Parameter. Dabei handelt es sich um eine Zeichenfolge mit einer durch Trennzeichen getrennten Liste von zu protokollierenden Anforderungstypen. Die drei möglichen Anforderungstypen sind **read**, **write** und **delete**. Um die Protokollierung zu deaktivieren, verwenden Sie den Wert **none** für den Parameter **LoggingOperations**.  

 Mit dem folgenden Befehl wird die Protokollierung für read-, write- und delete-Anforderungen im Warteschlangendienst mit einem Aufbewahrungszeitraum von fünf Tagen in Ihrem Standardspeicherkonto aktiviert:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Mit dem folgenden Befehl wird die Protokollierung für den Tabellenspeicherdienst in Ihrem Standardspeicherkonto deaktiviert:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter: [Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Programmgesteuertes Aktivieren der Speicherprotokollierung  

 Neben der Verwendung des Azure-Portals oder der Azure PowerShell-Cmdlets zum Steuern der Speicherprotokollierung können Sie auch eine der Azure Storage-APIs verwenden. Wenn Sie beispielsweise eine .NET-Programmiersprache verwenden, können Sie die Speicherclientbibliothek verwenden.  

 Die Klassen **CloudBlobClient**, **CloudQueueClient** und **CloudTableClient** verfügen alle über Methoden wie z. B. **SetServiceProperties** und **SetServicePropertiesAsync**, die ein **ServiceProperties**-Objekt als Parameter verwenden. Mit dem **ServiceProperties**-Objekt können Sie die Speicherprotokollierung konfigurieren. Mit dem folgenden C#-Codeausschnitt wird beispielsweise veranschaulicht, wie die protokollierten Daten und der Aufbewahrungszeitraum für die Protokollierung von Warteschlangen geändert werden:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Weitere Informationen zum Verwenden einer .NET-Programmiersprache zum Konfigurieren der Speicherprotokollierung finden Sie in der [Referenz zur Speicherclientbibliothek](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Allgemeine Informationen zum Konfigurieren der Speicherprotokollierung mithilfe der REST-API finden Sie unter [Aktivieren und Konfigurieren von Storage Analytics](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Herunterladen von Protokolldaten der Speicherprotokollierung

 Zum Anzeigen und Analysieren der Protokolldaten sollten Sie die Blobs, die die gewünschten Protokolldaten enthalten, auf einen lokalen Computer herunterladen. Mit vielen Tools zum Durchsuchen des Speichers können Sie Blobs von Ihrem Speicherkonto herunterladen. Außerdem können Sie das vom Azure Storage-Team bereitgestellte Befehlszeilentool Azure Copy (**AzCopy**) zum Herunterladen der Protokolldaten verwenden.  

 So stellen Sie sicher, dass Sie die gewünschten Protokolldaten herunterladen und dieselben Protokolldaten nicht mehrmals herunterladen  

-   Verwenden Sie die Benennungskonvention für Datum und Uhrzeit für Blobs, die Protokolldaten enthalten, um nachzuverfolgen, welche Blobs bereits zur Analyse heruntergeladen wurden, und zu vermeiden, dass dieselben Daten mehrmals erneut heruntergeladen werden.  

-   Verwenden Sie die Metadaten für die Blobs mit Protokolldaten, um den jeweiligen Zeitraum zu identifizieren, für den das Blob Protokolldaten enthält, und so zu das genaue herunterzuladende Blob zu ermitteln.  

> [!NOTE]
>  AzCopy ist Bestandteil des Azure SDK, unter [https://aka.ms/AzCopy](https://aka.ms/AzCopy) können Sie jedoch jederzeit die neueste Version herunterladen. AzCopy wird standardmäßig im Ordner **C:\Programme (x86)\Microsoft SDKs\Windows Azure\AzCopy** installiert. Fügen Sie diesen Ordner Ihrem Pfad hinzu, bevor Sie das Tool an einer Eingabeaufforderung oder in einem PowerShell-Fenster ausführen.  

 Im folgenden Beispiel wird veranschaulicht, wie Sie die Protokolldaten für den Warteschlangendienst für die Stunden ab 9 Uhr, 10 Uhr und 11 Uhr am 20. Mai 2014 herunterladen können. Der Parameter **/S** bewirkt, dass AzCopy eine lokale Ordnerstruktur basierend auf den Datums- und Uhrzeitangaben in den Protokolldateinamen erstellt. Der Parameter **/V** bewirkt, dass AzCopy eine ausführliche Ausgabe generiert. Der Parameter **/Y** bewirkt, dass AzCopy alle lokalen Dateien überschreibt. Ersetzen Sie **<yourstorageaccount\>** durch den Namen Ihres Speicherkontos und **<yourstoragekey\>** durch Ihren Speicherkontoschlüssel.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy enthält zudem einige nützliche Parameter, die steuern, wie der Zeitpunkt der letzten Änderung für die heruntergeladenen Dateien festgelegt wird und ob Dateien heruntergeladen werden, die älter oder neuer sind als bereits auf dem lokalen Computer vorhandene Dateien. Sie können das Tool auch im Neustartmodus ausführen. Ausführliche Informationen finden Sie in der Hilfe, indem Sie den Befehl **AzCopy /?** ausführen.  

 Ein Beispiel zum programmgesteuerten Herunterladen der Protokolldaten finden Sie im Blogbeitrag [Windows Azure Storage Logging: Using Logs to Track Storage Requests](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) (Microsoft Azure Storage-Protokollierung: Verwenden von Protokollen zur Nachverfolgung von Speicheranforderungen). Suchen Sie auf der Seite nach dem Begriff „DumpLogs“.  

 Nach dem Herunterladen der Protokolldaten können Sie die Protokolleinträge in den Dateien anzeigen. Diese Protokolldateien verwenden ein durch Trennzeichen getrenntes Textformat, das viele Protokolllesetools wie Microsoft Message Analyzer analysieren können. (Weitere Informationen finden Sie im Leitfaden [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).) Die verschiedenen Tools umfassen unterschiedliche Funktionen zum Formatieren, Filtern, Sortieren und Durchsuchen der Inhalte der Protokolldateien. Weitere Informationen zum Format und Inhalt von Protokolldateien der Speicherprotokollierung finden Sie unter [Storage Analytics-Protokollformat](/rest/api/storageservices/storage-analytics-log-format) und [Protokollierte Vorgänge und Statusmeldungen in Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nächste Schritte

* [Protokollformat der Speicheranalyse](/rest/api/storageservices/storage-analytics-log-format)
* [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriken von Storage Analytics (klassisch)](storage-analytics-metrics.md)
