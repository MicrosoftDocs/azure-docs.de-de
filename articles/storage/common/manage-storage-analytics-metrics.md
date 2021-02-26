---
title: Aktivieren und Verwalten von Azure Storage Analytics-Metriken (klassisch) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Storage Analytics-Metriken aktivieren, bearbeiten und anzeigen können.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221440"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Aktivieren und Verwalten von Azure Storage Analytics-Metriken (klassisch)

[Azure Storage Analytics](storage-analytics.md) enthält Metriken für alle Speicherdienste für Blobs, Warteschlangen und Tabellen. Sie können im [Azure-Portal](https://portal.azure.com) konfigurieren, welche Metriken für Ihr Konto aufgezeichnet werden sollen, und Diagramme konfigurieren, die visuelle Darstellungen Ihrer Metrikdaten bieten. In diesem Artikel wird gezeigt, wie Sie Metriken aktivieren und verwalten können. Wenn Sie erfahren möchten, wie Protokolle aktiviert werden, lesen Sie[Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch)](manage-storage-analytics-logs.md).

Es wird empfohlen, [Azure Monitor für Storage](../../azure-monitor/insights/storage-insights-overview.md) (Vorschau) zu überprüfen. Dieses Feature von Azure Monitor ermöglicht eine umfassende Überwachung ihrer Azure Storage-Konten, indem eine einheitliche Ansicht der Leistung, Kapazität und Verfügbarkeit Ihrer Azure Storage-Dienste bereitgestellt wird. Sie müssen nichts aktivieren oder konfigurieren und können diese Metriken aus den vordefinierten interaktiven Diagrammen und anderen darin enthaltenen Visualisierungen sofort anzeigen.

> [!NOTE]
> Für die Untersuchung von Überwachungsdaten im Azure-Portal fallen Kosten an. Weitere Informationen finden Sie unter [Storage Analytics](storage-analytics.md).
>
> Blockblob-Speicherkonten mit Premium-Leistung unterstützen keine Storage Analytics-Metriken. Wenn Sie Metriken über Blockblob-Speicherkonten mit Premium-Leistung anzeigen möchten, erwägen Sie die Verwendung von [Azure Storage-Metriken in Azure Monitor](../blobs/monitor-blob-storage.md).
>
> Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Aktivieren von Metriken

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Speicher** und dann den Speicherkontonamen, um das Kontodashboard zu öffnen.

2. Wählen Sie im Abschnitt **Überwachung (klassisch)** des Menüblatts die Option **Diagnoseeinstellungen (klassisch)** aus.
   
   ![Screenshot: Hervorgehobene Option „Diagnoseeinstellungen (klassisch)“ im Abschnitt „Überwachung (klassisch)“](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Wählen Sie den **Typ** der Metrikdaten für jeden **Dienst**, den Sie überwachen möchten, und die **Aufbewahrungsrichtlinie** für die Daten. Sie können die Überwachung auch deaktivieren, indem Sie für **Status** **Aus** festlegen.

   > [!div class="mx-imgBorder"]
   > ![Konfigurieren Sie die Protokollierung im Azure-Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Zum Festlegen der Datenaufbewahrungsrichtlinie verschieben Sie den Schieberegler **Aufbewahrung (Tage)** , oder geben Sie die Anzahl der Tage ein, für die Daten aufbewahrt werden sollen (zwischen 1 und 365). Die Standardeinstellung für neue Speicherkonten beträgt sieben Tage. Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Ist keine Aufbewahrungsrichtlinie festgelegt, müssen Sie die Überwachungsdaten selbst löschen.

   > [!WARNING]
   > Metriken werden in Ihrem Konto als Daten gespeichert. Metrikdaten können sich im Laufe der Zeit in Ihrem Konto ansammeln und so die Speicherkosten erhöhen. Wenn Sie Metrikdaten nur für einen kurzen Zeitraum benötigen, können Sie Ihre Kosten durch eine Änderung der Datenaufbewahrungsrichtlinie senken. Veraltete Metrikdaten (Daten, die älter als Ihre Aufbewahrungsrichtlinie sind) werden vom System gelöscht. Wir empfehlen, eine Aufbewahrungsrichtlinie entsprechend dem Zeitraum festzulegen, in dem Sie die Metrikdaten für Ihr Konto aufbewahren möchten. Weitere Informationen finden Sie unter [Abrechnung für Speichermetriken](storage-analytics-metrics.md#billing-on-storage-metrics).
   >

4. Wählen Sie nach Abschluss der Überwachungskonfiguration **Speichern**.

Ein Standardsatz von Metriken wird in Diagrammen auf dem Blatt **Übersicht** sowie auf dem Blatt **Metriken (klassisch)** angezeigt. Nachdem Sie die Metriken für einen Dienst aktiviert haben, kann es bis zu einer Stunde dauern, bis Daten in den Diagrammen angezeigt werden. Sie können **Bearbeiten** auf jedem beliebigen Metrikdiagramm auswählen, um zu konfigurieren, welche Metriken im Diagramm angezeigt werden.

Sie können die Sammlung von Metriken und die Protokollierung deaktivieren, indem Sie **Status** auf **Aus** setzen.

> [!NOTE]
> Azure Storage verwendet [Tabellenspeicher](storage-introduction.md#table-storage), um die Metriken für Ihr Speicherkonto zu speichern, und speichert die Metriken in Tabellen in Ihrem Konto. Weitere Informationen finden Sie weiter oben unter [Speichern von Metriken](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster.

2. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

3. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

5. Rufen Sie den Kontext des Speicherkontos ab, der das zu verwendende Speicherkonto definiert.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos. 

6. Sie können mithilfe von PowerShell auf Ihrem lokalen Computer Speichermetriken in Ihrem Speicherkonto konfigurieren. Mithilfe des Azure PowerShell-Cmdlets **Set-AzStorageServiceMetricsProperty** können Sie die aktuellen Einstellungen ändern. 

   Der folgende Befehl aktiviert minütliche Metriken für den Blob-Dienst in Ihrem Speicherkonto mit einem Aufbewahrungszeitraum, der auf fünf Tage festgelegt wurde.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Für dieses Cmdlet werden die folgenden Parameter verwendet:  

   - **ServiceType**: Mögliche Werte sind **Blob**, **Queue**, **Table** und **File**.
   - **MetricsType**: Mögliche Werte sind **Hour** und **Minute**.  
   - **MetricsLevel**: Mögliche Werte:
      - **Keine:** Deaktiviert die Überwachung.
      - **Dienst**: Erfasst Metriken wie Ein- und Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Warteschlangen-, Tabellen- und Dateidienste aggregiert werden.
      - **ServiceAndApi**: Sammelt zusätzlich zu den Dienstmetriken den gleichen Satz Metriken für jeden Speichervorgang in der Azure Storage-Dienst-API.

   Der folgende Befehl ruft die aktuelle stündliche Metrikstufe und die Aufbewahrungstage für den Blob-Dienst in Ihrem Standardspeicherkonto ab:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Weitere Informationen zum Verwenden einer .NET-Programmiersprache zum Konfigurieren von Speichermetriken finden Sie unter [Azure Storage-Clientbibliotheken für .NET](/dotnet/api/overview/azure/storage).  

Allgemeine Informationen zum Konfigurieren der Speichermetriken mithilfe der REST-API finden Sie unter [Aktivieren und Konfigurieren von Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Weitere Informationen zum Verwenden einer .NET-Programmiersprache zum Konfigurieren von Speichermetriken finden Sie unter [Azure Storage-Clientbibliotheken für .NET](/dotnet/api/overview/azure/storage).  

Allgemeine Informationen zum Konfigurieren der Speichermetriken mithilfe der REST-API finden Sie unter [Aktivieren und Konfigurieren von Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Anzeigen von Metriken in einem Diagramm

Nachdem Sie die Metriken der Speicheranalyse zum Überwachen Ihres Speicherkontos konfiguriert haben, erfasst die Speicheranalyse die Metriken in bekannten Tabellen in Ihrem Speicherkonto. Sie können Diagramme zum Anzeigen stündlicher Metriken im [Azure-Portal](https://portal.azure.com) konfigurieren.

Verwenden Sie das folgende Verfahren, um auszuwählen, welche Speichermetriken in einem Metrikdiagramm angezeigt werden.

1. Beginnen Sie mit der Anzeige eines Speichermetrikdiagramms im Azure-Portal. Diagramme können Sie auf dem Blatt **Speicherkonto** und auf dem Blatt **Metriken (klassisch)** finden.

   In diesem Beispiel wird das folgende auf dem **Speicherkontoblatt** angezeigte Diagramm verwendet:

   ![Diagrammauswahl im Azure-Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Klicken Sie auf eine beliebige Stelle innerhalb des Diagramms, um es zu bearbeiten.

3. Wählen Sie als Nächstes den **Zeitraum** der Metriken, die im Diagramm angezeigt werden sollen, und den **Dienst** (Blob, Warteschlange, Tabelle, Datei) aus, dessen Metriken angezeigt werden sollen. Hier sind die Metriken für den Blobdienst der vergangenen Woche zur Anzeige ausgewählt:

   ![Auswahl von Zeitraum und Dienst auf dem Blatt „Diagramm bearbeiten“](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Wählen Sie die einzelnen **Metriken** aus, die im Diagramm angezeigt werden sollen, und klicken Sie dann auf **OK**.

   ![Individuelle Auswahl von Metriken auf dem Blatt „Diagramm bearbeiten“](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Ihre Diagrammeinstellungen haben keine Auswirkung auf Sammlung, Aggregierung oder Speicherung von Überwachungsdaten im Speicherkonto.

#### <a name="metrics-availability-in-charts"></a>Verfügbarkeit von Metriken in Diagrammen

Die Liste der verfügbaren Metriken ändert sich je nach dem Dienst, den Sie in der Dropdownliste ausgewählt haben, und dem Einheitentyp des Diagramms, das Sie gerade bearbeiten. Sie können z.B. prozentuale Metriken wie *PercentNetworkError* und *PercentThrottlingError* nur dann auswählen, wenn Sie ein Diagramm bearbeiten, das Einheiten in Prozent angezeigt:

![Fehlerprozentsatz-Diagramm für Anforderung im Azure-Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Auflösung von Metriken

Die Metriken, die Sie in **Diagnose** ausgewählt haben, bestimmen die Auflösung der Metriken, die für Ihr Konto verfügbar sind:

* **Aggregieren**-Überwachung erfasst Metriken wie Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte. Diese Metriken werden aus Blob-, Tabellen-, Datei- und Warteschlangendienst aggregiert.
* **Pro API** bietet eine feinere Auflösung mit Metriken, die für einzelne Speicheroperationen verfügbar sind, in Ergänzung der Aggregate auf Dienstebene.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Herunterladen von Metriken zum lokalen Archivieren oder Analysieren

Wenn Sie die Metriken zur langfristigen Speicherung oder für eine lokale Analyse herunterladen möchten, müssen Sie ein Tool verwenden oder Code zum Lesen der Tabellen schreiben. Die Tabellen werden nicht angezeigt, wenn Sie alle Tabellen in Ihrem Speicherkonto auflisten. Sie können jedoch direkt anhand des Namens darauf zugreifen. Zahlreiche Tools zum Durchsuchen des Speichers erkennen diese Tabellen und ermöglichen die direkte Anzeige. Eine Liste der verfügbaren Tools finden Sie unter [Azure Storage-Clienttools](./storage-explorers.md).

|Metriken|Tabellennamen|Notizen| 
|-|-|-|  
|Stundenmetriken|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|In Versionen vor dem 15. August 2013 hatten diese Tabellen folgende Namen:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriken für den Dateidienst sind ab der Version vom 5. April 2015 verfügbar.|  
|Minutenmetriken|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kann nur mithilfe der PowerShell oder programmgesteuert aktiviert werden.<br /><br /> Metriken für den Dateidienst sind ab der Version vom 5. April 2015 verfügbar.|  
|Capacity|$MetricsCapacityBlob|Nur Blob-Dienst.|  

Die vollständigen Details der Schemas für diese Tabellen finden Sie unter [Schema der Tabellen für Storage Analytics-Metriken](/rest/api/storageservices/storage-analytics-metrics-table-schema). Die folgenden Beispielzeilen zeigen nur eine Teilmenge der verfügbaren Spalten. Sie verdeutlichen jedoch einige wichtige Funktionen der Art, in der Speichermetriken diese Metriken speichern:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Verfügbarkeit|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

In diesen minütlichen Metrikbeispieldaten verwendet der Partitionsschlüssel die Auflösung „Uhrzeit zur Minute“. Der Zeilenschlüssel identifiziert den Typ der Informationen, die in der Zeile gespeichert werden. Die Informationen besteht aus dem Zugriffstyp und dem Anforderungstyp:  

-   Der Zugriffstyp ist **user** oder **system**. Dabei bezieht sich **user** auf alle Benutzeranforderungen des Speicherdiensts und **system** auf Anforderungen, die von Storage Analytics vorgenommen werden.  
-   Der Anforderungstyp ist entweder **all**, in diesem Fall handelt es sich um eine Zusammenfassungszeile, oder er identifiziert die jeweilige API, z. B. **QueryEntity** oder **UpdateEntity**.  

Die Beispieldaten zeigen alle Datensätze für eine einzelne Minute (Beginn um 11:00 Uhr). Die Anzahl der **QueryEntities**-Anforderungen zuzüglich der Anzahl der **QueryEntity**-Anforderungen zuzüglich der Anzahl der **UpdateEntity**-Anforderungen ergibt daher den Wert 7. Diese Gesamtsumme wird in der Zeile **user:All** angezeigt. Analog können Sie die durchschnittliche End-to-End-Latenz 104,4286 für die Zeile **user:All** ableiten, indem Sie die Berechnung ((143,8 * 5) + 3 + 9)/7 ausführen.  

## <a name="view-metrics-data-programmatically"></a>Programmgesteuertes Anzeigen von Metrikdaten

Das folgende Listing zeigt C#-Beispielcode, der auf die minütlichen Metriken für einen bestimmten Zeitraum von Minuten zugreift und die Ergebnisse in einem Konsolenfenster anzeigt. Im Beispielcode wird die Version 4.x oder höher von Azure Storage Client Library verwendet, die die Klasse **CloudAnalyticsClient** enthält, durch die der Zugriff auf Metriktabellen im Speicher vereinfacht wird. 

> [!NOTE]
> Die **CloudAnalyticsClient**-Klasse ist nicht in der Azure Blob Storage-Clientbibliothek v12 für .NET enthalten. Am **31. August 2023** werden Storage Analytics-Metriken (auch als *klassische Metriken* bezeichnet) eingestellt. Weitere Informationen finden Sie in der [offiziellen Ankündigung](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Wenn Sie klassische Metriken verwenden, sollten Sie vor diesem Datum zu den Metriken in Azure Monitor wechseln. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Hinzufügen von Metrikdiagrammen zum Portaldashboard

Sie können Ihrem Portaldashboard Azure Storage-Metrikdiagramme für beliebige Ihrer Speicherkonten hinzufügen.

1. Wählen Sie **Dashboard bearbeiten**, während Ihr Dashboard im [Azure-Portal](https://portal.azure.com) angezeigt wird.
1. Wählen Sie im **Kachelkatalog** die Option **Kacheln suchen nach:**  > **Typ**.
1. Wählen Sie **Typ** > **Speicherkonten**.
1. Wählen Sie in **Ressourcen** das Speicherkonto aus, dessen Metriken Sie dem Dashboard hinzufügen möchten.
1. Wählen Sie **Kategorien** > **Überwachung**.
1. Ziehen Sie die Diagrammkachel für die Metrik, die Sie anzeigen möchten, auf Ihr Dashboard, und legen Sie sie ab. Wiederholen Sie dies für alle Metriken, die Sie auf dem Dashboard anzeigen möchten. In der folgenden Abbildung ist das Diagramm „Blobs – Anforderungen insgesamt“ als Beispiel hervorgehoben, aber alle Diagramme sind für die Platzierung auf Ihrem Dashboard verfügbar.

   ![Kachelkatalog im Azure-Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Wählen Sie **Anpassung abgeschlossen** am oberen Rand des Dashboards aus, wenn Sie keine weiteren Diagramme hinzufügen möchten.

Nachdem Sie Ihrem Dashboard Diagramme hinzugefügt haben, können Sie sie weiter anpassen, wie in „Anpassen von Metrikdiagrammen“ beschrieben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Storage Analytics finden Sie unter [Storage Analytics](storage-analytics.md) für Storage Analytics.
* [Konfigurieren Sie Storage Analytics-Protokolle](manage-storage-analytics-logs.md).
* Informieren Sie sich zum Metrikenschema. Sehen Sie sich das [Schema der Tabellen für Storage Analytics-Metriken](/rest/api/storageservices/storage-analytics-metrics-table-schema) an.