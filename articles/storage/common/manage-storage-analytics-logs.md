---
title: Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie ein Speicherkonto in Azure mithilfe von Azure Storage Analytics überwacht wird.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221472"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch)

[Azure Storage Analytics](storage-analytics.md) bietet Protokolle für Blobs, Warteschlangen und Tabellen. Sie können über das [Azure-Portal](https://portal.azure.com) konfigurieren, welche Protokolle für Ihr Konto aufgezeichnet werden sollen. In diesem Artikel wird gezeigt, wie Sie Protokolle aktivieren und verwalten können. Wenn Sie erfahren möchten, wie Metriken aktiviert werden, lesen Sie [Aktivieren und Verwalten von Azure Storage Analytics-Metriken (klassisch)](storage-monitor-storage-account.md).  Für die Untersuchung und Speicherung von Überwachungsdaten im Azure-Portal fallen Kosten an. Weitere Informationen finden Sie unter [Storage Analytics](storage-analytics.md).

> [!NOTE]
> Wir empfehlen, Azure Storage-Protokolle in Azure Monitor (statt Storage Analytics-Protokollen) zu verwenden. Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen für die öffentliche Cloud für Vorschautests zur Verfügung. Diese Vorschau unterstützt Protokolle für Blobdateien (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen und Tabellen. Weitere Informationen finden Sie in einem der folgenden Artikel:
>
> - [Überwachen von Azure Blob Storage](../blobs/monitor-blob-storage.md)
> - [Überwachen von Azure Files](../files/storage-files-monitoring.md)
> - [Überwachen von Azure Queue Storage](../queues/monitor-queue-storage.md)
> - [Überwachen von Azure-Tabellenspeicher](../tables/monitor-table-storage.md)

Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Aktivieren von Protokollen

Sie können Azure Storage anweisen, Diagnoseprotokolle für Lese-, Schreib- und Löschanforderungen für die Blob-, Tabellen- und Warteschlangendienste zu speichern. Die Datenaufbewahrungsrichtlinie, die Sie festlegen, gilt auch für diese Protokolle.

> [!NOTE]
> Azure Files unterstützt zurzeit Storage Analytics-Metriken, aber keine Storage Analytics-Protokollierung.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Speicherkonten** und dann den Speicherkontonamen, um das Speicherkontoblatt zu öffnen.

2. Wählen Sie im Abschnitt **Überwachung (klassisch)** des Menüblatts die Option **Diagnoseeinstellungen (klassisch)** aus.

    ![Element des Menüs „Diagnose“ unter ÜBERWACHUNG im Azure-Portal.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Stellen Sie sicher, dass **Status** auf **Ein** festgelegt ist, und wählen Sie die **Dienste**, für die Sie die Protokollierung aktivieren möchten.

   > [!div class="mx-imgBorder"]
   > ![Konfigurieren Sie die Protokollierung im Azure-Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Stellen Sie sicher, dass das Kontrollkästchen **Daten löschen** aktiviert ist.  Legen Sie dann die Anzahl von Tagen fest, während denen die Protokolldaten aufbewahrt werden sollen, indem Sie den Schieberegler unter dem Kontrollkästchen verschieben oder den im Textfeld neben dem Schieberegler angezeigten Wert direkt ändern. Die Standardeinstellung für neue Speicherkonten beträgt sieben Tage. Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Wenn es keine Aufbewahrungsrichtlinie gibt, müssen Sie die Protokolldaten selbst löschen.

   > [!WARNING]
   > Protokolle werden in Ihrem Konto als Daten gespeichert. Protokolldaten können sich im Laufe der Zeit in Ihrem Konto ansammeln und so die Speicherkosten erhöhen. Wenn Sie Protokolldaten nur für einen kurzen Zeitraum benötigen, können Sie Ihre Kosten durch eine Änderung der Datenaufbewahrungsrichtlinie senken. Veraltete Protokolldaten (Daten, die älter als Ihre Aufbewahrungsrichtlinie sind) werden vom System gelöscht. Wir empfehlen, eine Aufbewahrungsrichtlinie entsprechend dem Zeitraum festzulegen, in dem Sie die Protokolldaten für Ihr Konto aufbewahren möchten. Weitere Informationen finden Sie unter [Abrechnung für Speichermetriken](storage-analytics-metrics.md#billing-on-storage-metrics).

4. Klicken Sie auf **Speichern**.

   Die Diagnoseprotokolle werden in einem Blobcontainer namens *$logs* in Ihrem Speicherkonto gespeichert. Sie können die Protokolldaten über einen Speicher-Explorer wie [Microsoft Azure Storage-Explorer](https://storageexplorer.com) oder programmgesteuert mit der Speicherclientbibliothek oder PowerShell anzeigen.

   Informationen zum Zugreifen auf den Container „$logs“ finden Sie unter [Protokollierung durch die Speicheranalyse](storage-analytics-logging.md).

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

6. Verwenden Sie die Eigenschaft **Set-AzStorageServiceLoggingProperty** zum Ändern der aktuellen Protokolleinstellungen. Die Cmdlets, über die die Speicherprotokollierung gesteuert wird, verwenden einen **LoggingOperations**-Parameter. Dabei handelt es sich um eine Zeichenfolge mit einer durch Trennzeichen getrennten Liste von zu protokollierenden Anforderungstypen. Die drei möglichen Anforderungstypen sind **read**, **write** und **delete**. Um die Protokollierung zu deaktivieren, verwenden Sie den Wert **none** für den Parameter **LoggingOperations**.  

   Mit dem folgenden Befehl wird die Protokollierung für read-, write- und delete-Anforderungen im Warteschlangendienst mit einem Aufbewahrungszeitraum von fünf Tagen in Ihrem Standardspeicherkonto aktiviert:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Protokolle werden in Ihrem Konto als Daten gespeichert. Protokolldaten können sich im Laufe der Zeit in Ihrem Konto ansammeln und so die Speicherkosten erhöhen. Wenn Sie Protokolldaten nur für einen kurzen Zeitraum benötigen, können Sie Ihre Kosten durch eine Änderung der Datenaufbewahrungsrichtlinie senken. Veraltete Protokolldaten (Daten, die älter als Ihre Aufbewahrungsrichtlinie sind) werden vom System gelöscht. Wir empfehlen, eine Aufbewahrungsrichtlinie entsprechend dem Zeitraum festzulegen, in dem Sie die Protokolldaten für Ihr Konto aufbewahren möchten. Weitere Informationen finden Sie unter [Abrechnung für Speichermetriken](storage-analytics-metrics.md#billing-on-storage-metrics).
   
   Mit dem folgenden Befehl wird die Protokollierung für den Tabellenspeicherdienst in Ihrem Standardspeicherkonto deaktiviert:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter: [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Ändern der Aufbewahrungsdauer für Protokolldaten

Protokolldaten können sich im Laufe der Zeit in Ihrem Konto ansammeln und so die Speicherkosten erhöhen. Wenn Sie Protokolldaten nur für einen kurzen Zeitraum benötigen, können Sie Ihre Kosten durch eine Änderung des Datenaufbewahrungszeitraums senken. Wenn Sie beispielsweise Protokolle für nur drei Tage benötigen, legen Sie Ihren Aufbewahrungszeitraum für Protokolldaten auf den Wert `3` fest. Auf diese Weise werden Protokolle nach 3 Tagen automatisch aus Ihrem Konto gelöscht. In diesem Abschnitt wird gezeigt, wie Sie Ihren aktuellen Aufbewahrungszeitraum für Protokolldaten anzeigen und dann aktualisieren, wenn Sie dies tun möchten.

> [!NOTE]
> Diese Schritte gelten nur für Konten, bei denen die Einstellung **Hierarchischer Namespace** nicht aktiviert wurde. Wenn Sie diese Einstellung in Ihrem Konto aktiviert haben, wird die Einstellung für Aufbewahrung (Tage) noch nicht unterstützt. Stattdessen müssen Sie Protokolle mit einem beliebigen unterstützten Tool wie Azure Storage-Explorer, REST oder einem SDK manuell löschen. Informationen, wie Sie diese Protokolle in Ihrem Speicherkonto finden können, finden Sie unter [Wie Protokolle gespeichert werden](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Speicherkonten** und dann den Speicherkontonamen, um das Speicherkontoblatt zu öffnen.
2. Wählen Sie im Abschnitt **Überwachung (klassisch)** des Menüblatts die Option **Diagnoseeinstellungen (klassisch)** aus.

    ![Menüelement „Diagnose“ unter „ÜBERWACHUNG“ im Azure-Portal](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Stellen Sie sicher, dass das Kontrollkästchen **Daten löschen** aktiviert ist.  Legen Sie dann die Anzahl von Tagen fest, während denen die Protokolldaten aufbewahrt werden sollen, indem Sie den Schieberegler unter dem Kontrollkästchen verschieben oder den im Textfeld neben dem Schieberegler angezeigten Wert direkt ändern.

   > [!div class="mx-imgBorder"]
   > ![Ändern des Aufbewahrungszeitraums im Azure-Portal](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Die Standardanzahl von Tagen für neue Speicherkonten beträgt sieben Tage. Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Ist keine Aufbewahrungsrichtlinie festgelegt, müssen Sie die Überwachungsdaten selbst löschen.
   
4. Klicken Sie auf **Speichern**.

   Die Diagnoseprotokolle werden in einem Blobcontainer namens *$logs* in Ihrem Speicherkonto gespeichert. Sie können die Protokolldaten über einen Speicher-Explorer wie [Microsoft Azure Storage-Explorer](https://storageexplorer.com) oder programmgesteuert mit der Speicherclientbibliothek oder PowerShell anzeigen.

   Informationen zum Zugreifen auf den Container „$logs“ finden Sie unter [Protokollierung durch die Speicheranalyse](storage-analytics-logging.md).

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

5. Rufen Sie den Kontext des Speicherkontos ab, mit dem dieses Konto definiert wird.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos. 

6. Verwenden Sie die Eigenschaft [Get-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) zum Anzeigen der aktuellen Protokollaufbewahrungsrichtlinie. Im folgenden Beispiel wird der Aufbewahrungszeitraum für Blob- und Warteschlangenspeicherdienste in der Konsole ausgegeben.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   In der Konsolenausgabe wird der Aufbewahrungszeitraum unter der Spaltenüberschrift `RetentionDays` angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Aufbewahrungsrichtlinie in der PowerShell-Ausgabe](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Verwenden Sie die Eigenschaft [Set-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) zum Ändern des Aufbewahrungszeitraums. Im folgenden Beispiel wird der Aufbewahrungszeitraum in „4 Tage“ geändert.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter: [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Im folgenden Beispiel wird der Aufbewahrungszeitraum für Blob- und Warteschlangenspeicherdienste in der Konsole ausgegeben.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Im folgenden Beispiel wird der Aufbewahrungszeitraum in „4 Tage“ geändert. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Im folgenden Beispiel wird der Aufbewahrungszeitraum für Blob- und Warteschlangenspeicherdienste in der Konsole ausgegeben.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Im folgenden Beispiel wird der Aufbewahrungszeitraum für Protokolle für den Blob- und Warteschlangenspeicherdienst auf 4 Tage geändert. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Überprüfen, ob Protokolldaten gelöscht werden

Sie können überprüfen, ob Protokolle gelöscht werden, indem Sie den Inhalt des Containers `$logs` Ihres Speicherkontos anzeigen. Die folgende Abbildung zeigt den Inhalt eines Ordners im Container `$logs`. Der Ordner entspricht dem Januar 2021, und jeder Ordner enthält Protokolle für einen einzigen Tag. Wenn der heutige Tag der 29. Januar 2021 wäre und Ihre Aufbewahrungsrichtlinie auf nur einen Tag festgelegt wird, sollte dieser Ordner Protokolle für nur einen Tag enthalten.

> [!div class="mx-imgBorder"]
> ![Liste von Protokollordnern im Azure-Portal](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Anzeigen von Protokolldaten

 Zum Anzeigen und Analysieren der Protokolldaten sollten Sie die Blobs, die die gewünschten Protokolldaten enthalten, auf einen lokalen Computer herunterladen. Mit vielen Tools zum Durchsuchen des Speichers können Sie Blobs von Ihrem Speicherkonto herunterladen. Außerdem können Sie das vom Azure Storage-Team bereitgestellte Befehlszeilentool Azure Copy ([AzCopy](storage-use-azcopy-v10.md)) zum Herunterladen der Protokolldaten verwenden.  
 
>[!NOTE]
> Der Container `$logs` ist nicht in Event Grid integriert. Sie erhalten daher keine Benachrichtigungen, wenn Protokolldateien geschrieben werden. 

 So stellen Sie sicher, dass Sie die gewünschten Protokolldaten herunterladen und dieselben Protokolldaten nicht mehrmals herunterladen  

-   Verwenden Sie die Benennungskonvention für Datum und Uhrzeit für Blobs, die Protokolldaten enthalten, um nachzuverfolgen, welche Blobs bereits zur Analyse heruntergeladen wurden, und zu vermeiden, dass dieselben Daten mehrmals erneut heruntergeladen werden.  

-   Verwenden Sie die Metadaten für die Blobs mit Protokolldaten, um den jeweiligen Zeitraum zu identifizieren, für den das Blob Protokolldaten enthält, und so zu das genaue herunterzuladende Blob zu ermitteln.  

Informationen zu den ersten Schritten mit AzCopy finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md). 

Im folgenden Beispiel wird veranschaulicht, wie Sie die Protokolldaten für den Warteschlangendienst für die Stunden ab 9 Uhr, 10 Uhr und 11 Uhr am 20. Mai 2014 herunterladen können.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Weitere Informationen zum Herunterladen bestimmter Dateien finden Sie unter [Herunterladen von Blobs aus Azure Blob Storage mithilfe von AzCopy v10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Nach dem Herunterladen der Protokolldaten können Sie die Protokolleinträge in den Dateien anzeigen. Diese Protokolldateien verwenden ein durch Trennzeichen getrenntes Textformat, das viele Protokolllesetools analysieren können. (Weitere Informationen finden Sie im Leitfaden [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).) Die verschiedenen Tools umfassen unterschiedliche Funktionen zum Formatieren, Filtern, Sortieren und Durchsuchen der Inhalte der Protokolldateien. Weitere Informationen zum Format und Inhalt von Protokolldateien der Speicherprotokollierung finden Sie unter [Storage Analytics-Protokollformat](/rest/api/storageservices/storage-analytics-log-format) und [Protokollierte Vorgänge und Statusmeldungen in Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Storage Analytics finden Sie unter [Storage Analytics](storage-analytics.md) für Storage Analytics.
* [Konfigurieren Sie Storage Analytics-Metriken](storage-monitor-storage-account.md).
* Weitere Informationen zum Verwenden einer .NET-Programmiersprache zum Konfigurieren der Speicherprotokollierung finden Sie in der [Referenz zur Speicherclientbibliothek](/previous-versions/azure/dn261237(v=azure.100)). 
* Allgemeine Informationen zum Konfigurieren der Speicherprotokollierung mithilfe der REST-API finden Sie unter [Aktivieren und Konfigurieren von Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Informieren Sie sich über das Format von Storage Analytics-Protokollen. Lesen Sie dazu [Storage Analytics-Protokollformat](/rest/api/storageservices/storage-analytics-log-format).