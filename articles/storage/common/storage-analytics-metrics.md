---
title: Metriken von Azure Storage Analytics (klassisch)
description: Hier erfahren Sie, wie Metriken in Azure Storage verwendet werden.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: f0dfed10190685c1d51822b8bec2b3c80cea7bb2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153940"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriken von Azure Storage Analytics (klassisch)

Von der Speicheranalyse können Metriken gespeichert werden, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst gesendeten Anforderungen zählen. Berichte zu Transaktionen werden sowohl auf API-Vorgangsebene als auch auf Speicherdienstebene erstellt, während Berichte zur Kapazität nur auf Speicherdienstebene erstellt werden. Mit den Metrikdaten können die Speicherdienstnutzung analysiert, Probleme mit Anforderungen für den Speicherdienst diagnostiziert und die Leistung von Anwendungen verbessert werden, die einen Dienst verwenden.  

 Metriken der Speicheranalyse sind standardmäßig für neue Speicherkonten aktiviert. Sie können sie im [Azure-Portal](https://portal.azure.com/) konfigurieren. Details finden Sie unter [Überwachen eines Speicherkontos im Azure-Portal](/azure/storage/storage-monitor-storage-account). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Verwenden Sie den Vorgang „Diensteigenschaften festlegen“, um Storage Analytics für alle Dienste zu aktivieren.  

> [!NOTE]
> Storage Analytics-Metriken sind für die Blob-, Warteschlangen-, Tabellen- und Dateidienste verfügbar.
> Storage Analytics-Metriken sind jetzt klassische Metriken. Microsoft empfiehlt, anstelle von Storage Analytics-Metriken [Azure Storage-Metriken in Azure Monitor](storage-metrics-in-azure-monitor.md) zu verwenden.

## <a name="transaction-metrics"></a>Transaktionsmetriken  
 Ein umfassender Datensatz wird stündlich oder minütlich für jeden Speicherdienst und jeden angeforderten API-Vorgang aufgezeichnet, einschließlich Eingang/Ausgang, Verfügbarkeit, Fehlern und nach Prozentanteil kategorisierten Anforderungen. Eine vollständige Liste der Transaktionsdetails finden Sie im Thema [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Transaktionsdaten werden auf zwei Ebenen aufgezeichnet, auf Dienstebene und API-Vorgangsebene. Auf Dienstebene werden stündlich zusammenfassende Statistiken für alle angeforderten API-Vorgänge in eine Tabellenentität geschrieben, wenn keine Anforderungen für den Dienst gesendet wurden. Auf API-Vorgangsebene werden Statistiken nur dann in eine Entität geschrieben, wenn der Vorgang während der betreffenden Stunde angefordert wurde.  

 Wenn Sie beispielsweise einen **GetBlob**-Vorgang für Ihren Blob-Dienst ausführen, protokollieren Speicheranalysemetriken die Anforderung und fügen diese in die aggregierten Daten für den Blob-Dienst und den **GetBlob**-Vorgang ein. Wenn jedoch im Verlauf der Stunde kein **GetBlob**-Vorgang angefordert wurde, wird für den betreffenden Vorgang keine Entität in *$MetricsTransactionsBlob* geschrieben.  

 Transaktionsmetriken werden sowohl für Anforderungen von Benutzern als auch für Anforderungen aufgezeichnet, die von der Speicheranalyse selbst generiert wurden. So werden z. B. Anforderungen von Storage Analytics zum Schreiben von Protokollen und Tabellenentitäten aufgezeichnet.

## <a name="capacity-metrics"></a>Kapazitätsmetriken  

> [!NOTE]
>  Kapazitätsmetriken sind derzeit nur für den BLOB-Dienst verfügbar.

 Kapazitätsdaten werden für den BLOB-Dienst eines Speicherkontos täglich aufgezeichnet, und es werden zwei Tabellenentitäten geschrieben. Eine Entität stellt Statistiken für Benutzerdaten bereit, während die andere Statistiken zum Blob-Container `$logs` bereitstellt, der von der Speicheranalyse verwendet wird. Die Tabelle *$MetricsCapacityBlob* enthält die folgenden Statistiken:  

- **Capacity**: Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.  
- **ContainerCount**: Die Anzahl von Blobcontainern im Blob-Dienst des Speicherkontos.  
- **ObjectCount**: Die Anzahl der Block- oder Seitenblobs mit und ohne ausgeführtem Commit im Blob-Dienst des Speicherkontos.  

  Weitere Informationen zu den Kapazitätsmetriken finden Sie unter [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Speichern von Metriken  

 Alle Metrikdaten für jeden der Speicherdienste werden in drei Tabellen gespeichert, die für diesen Dienst reserviert sind: eine Tabelle für Transaktionsinformationen, eine Tabelle für minutenbezogene Transaktionsinformationen und eine weitere Tabelle für Kapazitätsinformationen. Informationen zur Transaktion und deren Dauer umfassen Anforderungs- und Antwortdaten, während Kapazitätsinformationen Daten zur Speicherverwendung enthalten. Stunden- und Minutenmetriken sowie Kapazitätsinformationen zum Blob-Dienst eines Speicherkontos können aus Tabellen abgerufen werden, die wie in der folgenden Tabelle beschrieben benannt sind.  

|Metrikebene|Tabellennamen|Unterstützt für folgende Versionen|  
|-------------------|-----------------|----------------------------|  
|Stundenmetriken, primärer Standort|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Nur Versionen vor dem 15.08.2013. Obwohl diese Namen weiterhin unterstützt werden, wird empfohlen, die unten aufgeführten Tabellen zu verwenden.|  
|Stundenmetriken, primärer Standort|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Alle Versionen. Die Unterstützung für Dateidienstmetriken ist nur in der Version 2015-04-05 und höher verfügbar.|  
|Minutenmetriken, primärer Standort|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Alle Versionen. Die Unterstützung für Dateidienstmetriken ist nur in der Version 2015-04-05 und höher verfügbar.|  
|Stundenmetriken, sekundärer Standort|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Alle Versionen. Georedundante Replikation mit Lesezugriff muss aktiviert sein.|  
|Minutenmetriken, sekundärer Standort|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Alle Versionen. Georedundante Replikation mit Lesezugriff muss aktiviert sein.|  
|Kapazität (nur BLOB-Dienst)|$MetricsCapacityBlob|Alle Versionen.|  

 Diese Tabellen werden automatisch erstellt, wenn Storage Analytics für einen Speicherdienstendpunkt aktiviert wird. Der Zugriff auf diese Tabellen erfolgt über den Namespace des Speicherkontos. Beispiel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Die Metriktabellen werden bei einem Auflistungsvorgang nicht angezeigt und müssen über den Tabellennamen direkt aufgerufen werden.  

## <a name="enable-metrics-using-the-azure-portal"></a>Aktivieren von Metriken über das Azure-Portal
Gehen Sie wie folgt vor, um Metriken im [Azure-Portal](https://portal.azure.com)zu aktivieren:

1. Navigieren Sie zum Speicherkonto.
1. Wählen Sie im Bereich **Menü** die Option **Diagnostics settings (classic)** (Diagnoseeinstellungen (klassisch)) aus.
1. Prüfen Sie, ob der **Status** auf **Ein** festgelegt ist.
1. Wählen Sie die Metriken für die Dienste, die Sie überwachen möchten.
1. Geben Sie eine Aufbewahrungsrichtlinie an, um festzulegen, wie lange Metriken und Protokolldaten beibehalten werden sollen.
1. Wählen Sie **Speichern** aus.

Das [Azure-Portal](https://portal.azure.com) unterstützt zurzeit die Konfiguration von minütlichen Metriken in Ihrem Speicherkonto nicht. Sie müssen minütliche Metriken mithilfe von PowerShell oder programmgesteuert aktivieren.

> [!NOTE]
>  Beachten Sie, dass das Azure-Portal zurzeit die Konfiguration von minütlichen Metriken in Ihrem Speicherkonto nicht unterstützt. Minütliche Metriken müssen mithilfe von PowerShell oder programmgesteuert aktiviert werden.

## <a name="enable-storage-metrics-using-powershell"></a>Aktivieren von Speichermetriken mithilfe von PowerShell  
Sie können PowerShell auf Ihrem lokalen Computer zum Konfigurieren der Speichermetriken in Ihrem Speicherkonto verwenden, indem Sie das Azure PowerShell-Cmdlet **Get-AzureStorageServiceMetricsProperty** ausführen, um die aktuellen Einstellungen abzurufen. Mithilfe des Cmdlets **Set-AzureStorageServiceMetricsProperty** können Sie die aktuellen Einstellungen ändern.  

Die Cmdlets zur Steuerung der Speichermetriken verwenden die folgenden Parameter:  

* **ServiceType**, mögliche Werte sind **Blob**, **Queue**, **Table** und **File**.
* **MetricsType**, mögliche Werte sind **Hour** und **Minute**.  
* **MetricsLevel**, mögliche Werte:
* **Keine:** Deaktiviert die Überwachung.
* **Dienst**: Erfasst Metriken wie Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Warteschlangen-, Tabellen- und Dateidienste aggregiert werden.
* **ServiceAndApi**: Sammelt zusätzlich zu den Dienstmetriken den gleichen Satz Metriken für jeden Speichervorgang in der Azure Storage-Dienst-API.

Der folgende Befehl aktiviert z. B. minütliche Metriken für den Blob-Dienst in Ihrem Standardspeicherkonto mit einem Aufbewahrungszeitraum, der auf fünf Tage festgelegt ist:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

Der folgende Befehl ruft die aktuelle stündliche Metrikstufe und die Aufbewahrungstage für den Blob-Dienst in Ihrem Standardspeicherkonto ab:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter: [Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programmgesteuertes Aktivieren von Speichermetriken  
Neben der Verwendung des Azure-Portals oder der Azure PowerShell-Cmdlets zum Steuern der Speichermetriken können Sie auch eine der Azure Storage-APIs verwenden. Wenn Sie beispielsweise eine .NET-Programmiersprache verwenden, können Sie die Speicherclientbibliothek verwenden.  

Die Klassen **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient** und **CloudFileClient** verfügen alle über Methoden wie **SetServiceProperties** und **SetServicePropertiesAsync**, die ein **ServiceProperties**-Objekt als Parameter verwenden. Mit dem Objekt **ServiceProperties** können Sie Speichermetriken konfigurieren. Der folgende C#-Codeausschnitt zeigt beispielsweise, wie die Metrikebene und die Aufbewahrungstage für die stündlichen Warteschlangenmetriken geändert werden:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Weitere Informationen zum Verwenden einer .NET-Programmiersprache zum Konfigurieren der Speichermetriken finden Sie in der [Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Allgemeine Informationen zum Konfigurieren der Speichermetriken mithilfe der REST-API finden Sie unter [Aktivieren und Konfigurieren von Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Anzeigen von Speichermetriken  
Nachdem Sie die Metriken der Speicheranalyse zum Überwachen Ihres Speicherkontos konfiguriert haben, erfasst die Speicheranalyse die Metriken in bekannten Tabellen in Ihrem Speicherkonto. Sie können Diagramme zum Anzeigen stündlicher Metriken im [Azure-Portal](https://portal.azure.com) konfigurieren:

1. Navigieren Sie zum Speicherkonto im [Azure-Portal](https://portal.azure.com).
1. Wählen Sie für den Dienst, dessen Metriken Sie anzeigen möchten, auf dem Blatt **Menü** die Option **Metriken (klassisch)** aus.
1. Klicken Sie auf das Diagramm, das Sie konfigurieren möchten.
1. Wählen Sie auf dem Blatt **Diagramm bearbeiten** die Optionen **Zeitbereich** und **Diagrammtyp** sowie die Metriken, die im Diagramm angezeigt werden sollen.

Im Abschnitt **Überwachung (klassisch)** auf dem Menüblatt Ihres Speicherkontos im Azure-Portal können Sie [Warnungsregeln](#metrics-alerts) aktivieren, sodass beispielsweise eine Warnung per E-Mail gesendet wird, um Sie zu benachrichtigen, wenn eine bestimmte Metrik einen bestimmten Wert erreicht.

Wenn Sie die Metriken zur langfristigen Speicherung oder für eine lokale Analyse herunterladen möchten, müssen Sie ein Tool verwenden oder Code zum Lesen der Tabellen schreiben. Sie müssen die minütlichen Metriken für die Analyse herunterladen. Die Tabellen werden nicht angezeigt, wenn Sie alle Tabellen in Ihrem Speicherkonto auflisten. Sie können jedoch direkt anhand des Namens darauf zugreifen. Zahlreiche Tools zum Durchsuchen des Speichers erkennen diese Tabellen und ermöglichen die direkte Anzeige (unter [Azure Storage-Clienttools](/azure/storage/storage-explorers) finden Sie eine Liste der verfügbaren Tools).

||||  
|-|-|-|  
|**Metriken**|**Tabellennamen**|**Hinweise**|  
|Stundenmetriken|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|In Versionen vor 2013-08-15 hatten diese Tabellen folgende Namen:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriken für den Dateidienst sind ab Version 2015-04-05 verfügbar.|  
|Minutenmetriken|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kann nur mithilfe von PowerShell oder programmgesteuert aktiviert werden.<br /><br /> Metriken für den Dateidienst sind ab Version 2015-04-05 verfügbar.|  
|Capacity|$MetricsCapacityBlob|Nur Blob-Dienst.|  

Die vollständigen Details der Schemas für diese Tabellen finden Sie unter [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/storage-analytics-metrics-table-schema). Die Beispielzeilen unten zeigen nur eine Teilmenge der verfügbaren Spalten. Sie zeigen jedoch einige wichtige Funktionen, wie die Speichermetriken diese Metriken speichern:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**Zeilenschlüssel**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Verfügbarkeit**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

In diesen minütlichen Metrikbeispieldaten verwendet der Partitionsschlüssel die Auflösung "Uhrzeit zur Minute". Der Zeilenschlüssel identifiziert den Informationstyp, der in der Zeile gespeichert ist. Dieser besteht aus zwei Informationseinheiten: dem Zugriffstyp und dem Anforderungstyp:  

-   Der Zugriffstyp ist **user** oder **system**. Dabei bezieht sich **user** auf alle Benutzeranforderungen des Speicherdiensts und **system** auf Anforderungen, die von Storage Analytics vorgenommen werden.  

-   Der Anforderungstyp ist entweder **all**, in diesem Fall handelt es sich um eine Zusammenfassungszeile, oder er identifiziert die jeweilige API, z. B. **QueryEntity** oder **UpdateEntity**.  

Die Beispieldaten oben zeigen alle Datensätze für eine einzelne Minute (Beginn um 11:00 Uhr). Die Anzahl der **QueryEntities**-Anforderungen zuzüglich der Anzahl der **QueryEntity**-Anforderungen zuzüglich der Anzahl der **UpdateEntity**-Anforderungen ergibt daher den Wert 7. Dies ist die Gesamtsumme, die in der Zeile **user:All** angezeigt wird. Analog können Sie die durchschnittliche End-to-End-Latenz 104,4286 für die Zeile **user:All** ableiten, indem Sie die Berechnung ((143,8 * 5) + 3 + 9)/7 ausführen.  

## <a name="metrics-alerts"></a>Metrikwarnungen
Sie sollten die Einrichtung von Warnungen im [Azure-Portal](https://portal.azure.com) in Betracht ziehen, damit Sie automatisch über wichtige Änderungen im Verhalten der Speicherdienste informiert werden. Wenn Sie ein Storage-Explorer-Tool zum Herunterladen dieser Metrikdaten in einem Trennzeichen-getrennten Format verwenden, können Sie die Daten mithilfe von Microsoft Excel analysieren. Eine Liste der verfügbaren Tools für Storage-Explorer finden Sie unter [Microsoft Azure Storage-Explorer](/azure/storage/storage-explorers). Sie können Warnungen auf dem Blatt **Warnung (klassisch)** konfigurieren, auf das Sie im Menüblatt des Speicherkontos unter **Überwachung (klassisch)** zugreifen können.

> [!IMPORTANT]
> Möglicherweise gibt es eine Verzögerung zwischen einem Speicherereignis und der Aufzeichnung der zugehörigen stündlichen oder minütlichen Metrikdaten. Bei Minutenmetriken werden möglicherweise mehrere Minuten von Daten gleichzeitig geschrieben. Dies kann dazu führen, dass Transaktionen früherer Minuten in der Transaktion der aktuellen Minute zusammengeführt werden. In diesem Fall stehen dem Benachrichtigungsdienst möglicherweise nicht alle verfügbaren Metrikdaten für das konfigurierte Warnungsintervall zur Verfügung, sodass Warnungen unerwartet ausgelöst werden können.
>

## <a name="accessing-metrics-data-programmatically"></a>Programmgesteuertes Zugreifen auf Metrikdaten  
Das folgende Listing zeigt C#-Beispielcode, der auf die minütlichen Metriken für einen bestimmten Zeitraum von Minuten zugreift und die Ergebnisse in einem Konsolenfenster anzeigt. Im Beispielcode wird die Version 4.x oder höher von Azure Storage Client Library verwendet, die die Klasse **CloudAnalyticsClient** enthält, durch die der Zugriff auf Metriktabellen im Speicher vereinfacht wird.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Abrechnung von Speichermetriken  
Für Schreibanforderungen zum Erstellen von Tabellenentitäten für Metriken fallen die Standardraten an, die für alle Azure Storage-Vorgänge gelten.  

Für Lese- und Löschanforderungen für Metrikdaten durch einen Client gelten ebenfalls Standardraten. Wenn Sie eine Datenbeibehaltungsrichtlinie konfiguriert haben, fallen keine Kosten an, wenn Azure Storage alte Metrikdaten löscht. Wenn Sie jedoch Analysedaten löschen, wird Ihr Konto für die Löschvorgänge belastet.  

Die von Metriktabellen beanspruchte Kapazität ist ebenfalls kostenpflichtig. Sie können die folgenden Angaben verwenden, um die erforderliche Kapazität zum Speichern von Metrikdaten einzuschätzen:  

-   Wenn ein Dienst jede Stunde alle APIs in jedem Dienst nutzt, werden ungefähr 148 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, wenn Sie die Zusammenfassung auf Dienst- und API-Ebene aktiviert haben.  
-   Wenn ein Dienst jede Stunde alle APIs im Dienst nutzt, werden ungefähr 12 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, sofern Sie die Zusammenfassung nur auf Dienstebene aktiviert haben.  
-   In der Kapazitätstabelle für Blobs werden pro Tag zwei Zeilen hinzugefügt, vorausgesetzt, Sie haben Protokolle abonniert. Dies bedeutet, dass die Größe dieser Tabelle pro Tag um ungefähr 300 Byte zunimmt.

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen eines Speicherkontos](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokollierung durch die Speicheranalyse](storage-analytics-logging.md)
