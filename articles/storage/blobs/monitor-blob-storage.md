---
title: Überwachen von Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Leistung und Verfügbarkeit von Azure Blob Storage überwachen können. Überwachen Sie Azure Blob Storage-Daten, erfahren Sie mehr über die Konfiguration, und analysieren Sie Metrik- und Protokolldaten.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b73146302c740dfd8d97d275bad003a6d8a9a23c
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852777"
---
# <a name="monitoring-azure-blob-storage"></a>Überwachen von Azure Blob Storage

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen basieren, sollten Sie diese Ressourcen auf Verfügbarkeit, Leistung und Betrieb überwachen. In diesem Artikel wird das Überwachen von Daten beschrieben, die von Azure Blob Storage generiert wurden. Außerdem wird erläutert, wie Sie die Funktionen von Azure Monitor verwenden können, um Warnungen für diese Daten zu analysieren.

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen für die öffentliche Cloud für Vorschautests zur Verfügung. Diese Vorschau unterstützt Protokolle für Blobdateien (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen und Tabellen. Dieses Feature ist für alle Speicherkonten verfügbar, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden. Weitere Informationen finden Sie in der [Speicherkontoübersicht](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Azure Monitor: Übersicht

Im Azure-Portal enthält die Seite **Übersicht** für jede Blobspeicherressource eine kurze Übersicht über die Ressourcennutzung, beispielsweise Anforderungen und stündliche Abrechnung. Dies sind nützliche Informationen, die aber nur einen kleinen Teil der verfügbaren Überwachungsdaten ausmachen. Einige dieser Daten werden automatisch erfasst und sind für die Analyse verfügbar, sobald Sie die Ressource erstellen. Mit einigen Konfigurationsschritten können Sie zusätzliche Typen von Datensammlungen aktivieren.

## <a name="what-is-azure-monitor"></a>Was ist Azure Monitor?
Azure Blob Storage protokolliert Überwachungsdaten mit [Azure Monitor](../../azure-monitor/overview.md), einem Azure-Dienst zur vollständigen Stapelüberwachung. Azure Monitor bietet einen vollständigen Satz von Funktionen zum Überwachen Ihrer Azure-Ressourcen, von Ressourcen in anderen Clouds sowie von lokalen Ressourcen. 

Beginnen Sie mit dem Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md), in dem die folgenden Punkte beschrieben werden:

- Was ist Azure Monitor?
- Kosten für die Überwachung
- In Azure gesammelte Überwachungsdaten
- Konfigurieren der Datensammlung
- Standardtools in Azure zum Analysieren von Überwachungsdaten sowie zum Generieren von Warnungen

Die folgenden Abschnitte basieren auf diesem Artikel. Darin werden die spezifischen von Azure Storage erfassten Daten beschrieben. In Beispielen wird gezeigt, wie Sie die Datensammlung konfigurieren und diese Daten mit Azure-Tools analysieren.

## <a name="monitoring-data"></a>Überwachungsdaten

Azure Blob Storage erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die unter [Überwachungsdaten von Azure-Ressourcen](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) beschrieben sind. 

Ausführliche Informationen zu den Metriken und Protokollen, die von Azure Blob Storage erstellt werden, finden Sie in der [Überwachungsdatenreferenz zu Azure Blob Storage](monitor-blob-storage-reference.md).

Die Metriken und Protokolle in Azure Monitor unterstützen nur Azure Resource Manager-Speicherkonten. Klassische Speicherkonten werden von Azure Monitor nicht unterstützt. Wenn Sie Metriken oder Protokolle für ein klassisches Speicherkonto verwenden möchten, müssen Sie das Konto zu einem Azure Resource Manager-Speicherkonto migrieren. Weitere Informationen finden Sie unter [Migrieren zu Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Wenn Sie möchten, können Sie klassische Metriken und Protokolle auch weiterhin verwenden. Klassische Metriken und Protokolle sind parallel zu den Metriken und Protokollen in Azure Monitor verfügbar. Die Unterstützung bleibt bestehen, bis Azure Storage den Dienst für Legacymetriken und -protokolle einstellt.

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden. 

Damit Sie Ressourcenprotokolle erfassen können, müssen Sie eine Diagnoseeinstellung erstellen. Wählen Sie beim Erstellen dieser Einstellung **Blob** als den Speichertyp aus, für den Sie Protokolle aktivieren möchten. Geben Sie dann eine der folgenden Kategorien von Vorgängen an, für die Protokolle erfasst werden sollen. 

| Kategorie | BESCHREIBUNG |
|:---|:---|
| StorageRead | Lesevorgänge für Objekte. |
| StorageWrite | Schreibvorgänge für Objekte. |
| StorageDelete | Löschvorgänge für Objekte. |

> [!NOTE]
> Data Lake Storage Gen2 wird nicht als Speichertyp angezeigt. Dies liegt daran, dass es sich bei Data Lake Storage Gen2 um eine Reihe von in Blob Storage verfügbaren Funktionen handelt. 

## <a name="creating-a-diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung

Sie können eine Diagnoseeinstellung über das Azure-Portal, PowerShell, die Azure-Befehlszeilenschnittstelle oder eine Azure Resource Manager-Vorlage erstellen. 

Eine allgemeine Anleitung finden Sie unter [Erstellen einer Diagnoseeinstellung zum Erfassen von Plattformprotokollen und Metriken in Azure](../../azure-monitor/platform/diagnostic-settings.md).

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen für die öffentliche Cloud für Vorschautests zur Verfügung. Diese Vorschau unterstützt Protokolle für Blobdateien (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen und Tabellen. Dieses Feature ist für alle Speicherkonten verfügbar, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden. Weitere Informationen finden Sie in der [Speicherkontoübersicht](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Melden Sie sich beim Azure-Portal an.

2. Navigieren Sie zu Ihrem Speicherkonto.

3. Klicken Sie im Abschnitt **Überwachung** auf **Diagnoseeinstellungen (Vorschau)** .

   > [!div class="mx-imgBorder"]
   > ![Portal – Diagnoseprotokolle](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Wählen Sie **Blob** als den Speichertyp aus, für den Sie Protokolle aktivieren möchten.

5. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   > [!div class="mx-imgBorder"]
   > ![Portal: Ressourcenprotokolle – „Diagnoseeinstellung hinzufügen“](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   Die Seite **Diagnoseeinstellungen** wird angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Seite „Ressourcenprotokolle“](media/monitor-blob-storage/diagnostic-logs-page.png)

6. Geben Sie im Feld **Name** auf der Seite einen Namen für diese Ressourcenprotokolleinstellung ein. Wählen Sie dann aus, welche Vorgänge (Lese-, Schreib- und Löschvorgänge) protokolliert und wohin die Protokolle gesendet werden sollen.

#### <a name="archive-logs-to-a-storage-account"></a>Archivieren von Protokollen in einem Speicherkonto

Wenn Sie Ihre Protokolle in einem Speicherkonto archivieren, bezahlen Sie für die Menge an Protokolldaten, die an das Speicherkonto gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

1. Aktivieren Sie das Kontrollkästchen **In ein Speicherkonto archivieren**, und klicken Sie dann auf die Schaltfläche **Konfigurieren**.

   > [!div class="mx-imgBorder"]   
   > ![Seite „Diagnoseeinstellungen“: „In ein Speicherkonto archivieren“](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Wählen Sie in der Dropdownliste **Speicherkonto** das Speicherkonto aus, in dem Sie Ihre Protokolle archivieren möchten. Klicken Sie auf die Schaltfläche **OK** und dann auf die Schaltfläche **Speichern**.

   > [!NOTE]
   > Bevor Sie ein Speicherkonto als Exportziel auswählen, informieren Sie sich unter [Archivieren von Azure-Ressourcenprotokollen](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) über die Voraussetzungen für das Speicherkonto.

#### <a name="stream-logs-to-azure-event-hubs"></a>Streamen von Protokollen an Azure Event Hubs

Wenn Sie Ihre Protokolle an einen Event Hub streamen, bezahlen Sie für die Menge an Protokolldaten, die an den Event Hub gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

1. Aktivieren Sie das Kontrollkästchen **An einen Event Hub streamen**, und klicken Sie dann auf die Schaltfläche **Konfigurieren**.

2. Wählen Sie im Bereich **Event Hub auswählen** den Namespace, den Namen und den Richtliniennamen der Event Hub-Instanz aus, an die Sie Ihre Protokolle streamen möchten. 

   > [!div class="mx-imgBorder"]
   > ![Seite „Diagnoseeinstellungen“ mit Event Hub](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klicken Sie auf die Schaltfläche **OK** und dann auf die Schaltfläche **Speichern**.

#### <a name="send-logs-to-azure-log-analytics"></a>Senden von Protokollen an Azure Log Analytics

1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**, wählen Sie einen Log Analytics-Arbeitsbereich aus, und klicken Sie dann auf die Schaltfläche **Speichern**.

   > [!div class="mx-imgBorder"]   
   > ![Seite „Diagnoseeinstellungen“ für Log Analytics](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster, und melden Sie sich dann mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an. Befolgen Sie dann die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

2. Legen Sie Ihr aktives Abonnement auf das Abonnement des Speicherkontos fest, für das Sie die Protokollierung aktivieren möchten.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archivieren von Protokollen in einem Speicherkonto

Wenn Sie Ihre Protokolle in einem Speicherkonto archivieren, bezahlen Sie für die Menge an Protokolldaten, die an das Speicherkonto gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

Aktivieren Sie Protokolle mit dem PowerShell-Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) unter Angabe des Parameters `StorageAccountId`.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ersetzen Sie den Platzhalter `<storage-service-resource--id>` in diesem Codeausschnitt durch die Ressourcen-ID des Blobdiensts. Sie finden die Ressourcen-ID im Azure-Portal, indem Sie die Seite **Eigenschaften** Ihres Speicherkontos öffnen.

Sie können `StorageRead`, `StorageWrite` und `StorageDelete` als Wert für den Parameter **Category** angeben.

Hier sehen Sie ein Beispiel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Eine Beschreibung der einzelnen Parameter finden Sie unter [Archivieren von Azure-Ressourcenprotokollen mithilfe von Azure PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamen von Protokollen an einen Event Hub

Wenn Sie Ihre Protokolle an einen Event Hub streamen, bezahlen Sie für die Menge an Protokolldaten, die an den Event Hub gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

Aktivieren Sie Protokollen mit dem PowerShell-Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) und dem Parameter `EventHubAuthorizationRuleId`.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Hier sehen Sie ein Beispiel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Eine Beschreibung der einzelnen Parameter finden Sie unter [Streamen von Daten an Event Hubs mithilfe von PowerShell-Cmdlets](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Senden von Protokollen an Log Analytics

Aktivieren Sie Protokollen mit dem PowerShell-Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) und dem Parameter `WorkspaceId`.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Hier sehen Sie ein Beispiel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Weitere Informationen finden Sie unter [Streamen von Azure-Ressourcenprotokollen an einen Log Analytics-Arbeitsbereich in Azure Monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Öffnen Sie zunächst [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-CLI lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, für das Sie Protokolle aktivieren möchten.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

#### <a name="archive-logs-to-a-storage-account"></a>Archivieren von Protokollen in einem Speicherkonto

Wenn Sie Ihre Protokolle in einem Speicherkonto archivieren, bezahlen Sie für die Menge an Protokolldaten, die an das Speicherkonto gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

Aktivieren Sie Protokolle mit dem Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Ersetzen Sie den Platzhalter `<storage-service-resource--id>` in diesem Codeausschnitt durch die Ressourcen-ID des Blobspeicherdiensts. Sie finden die Ressourcen-ID im Azure-Portal, indem Sie die Seite **Eigenschaften** Ihres Speicherkontos öffnen.

Sie können `StorageRead`, `StorageWrite` und `StorageDelete` als Wert für den Parameter **Category** angeben.

Hier sehen Sie ein Beispiel:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Eine Beschreibung der einzelnen Parameter finden Sie unter [Archivieren von Ressourcenprotokollen mithilfe der Azure-Befehlszeilenschnittstelle](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamen von Protokollen an einen Event Hub

Wenn Sie Ihre Protokolle an einen Event Hub streamen, bezahlen Sie für die Menge an Protokolldaten, die an den Event Hub gesendet werden. Die genauen Preise finden Sie im Abschnitt **Plattformprotokolle** auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

Aktivieren Sie Protokolle mit dem Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Hier sehen Sie ein Beispiel:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Eine Beschreibung der einzelnen Parameter finden Sie unter [Streamen von Daten an Event Hubs mithilfe der Azure-Befehlszeilenschnittstelle](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Senden von Protokollen an Log Analytics

Aktivieren Sie Protokolle mit dem Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Hier sehen Sie ein Beispiel:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Weitere Informationen finden Sie unter [Streamen von Azure-Ressourcenprotokollen an einen Log Analytics-Arbeitsbereich in Azure Monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Vorlage](#tab/template)

Eine Azure Resource Manager-Vorlage zum Erstellen einer Diagnoseeinstellung finden Sie unter [Diagnoseeinstellung für Azure Storage](../../azure-monitor/samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Mit dem Metrik-Explorer können Sie Metriken für Azure Storage mit Metriken aus anderen Azure-Diensten analysieren. Öffnen Sie den Metrik-Explorer, indem Sie im Menü **Azure Monitor** die Option **Metriken** auswählen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Dieses Beispiel zeigt, wie Sie **Transaktionen** auf der Kontoebene anzeigen.

![Screenshot für den Zugriff auf Metriken über das Azure-Portal](./media/monitor-blob-storage/access-metrics-portal.png)

Bei Metriken mit Dimensionsunterstützung können Sie die Metrik nach dem gewünschten Dimensionswert filtern. Dieses Beispiel zeigt, wie Sie **Transaktionen** auf der Kontoebene für einen bestimmten Vorgang anzeigen, indem Sie Werte für die Dimension **API-Name** auswählen.

![Screenshot für den Zugriff auf Metriken mit Dimension über das Azure-Portal](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Eine vollständige Liste der von Azure Storage unterstützten Dimensionen finden Sie unter [Metrikdimensionen](monitor-blob-storage-reference.md#metrics-dimensions).

Metriken für Azure Blob Storage befinden sich in diesen Namespaces: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

Eine Liste aller von Azure Monitor unterstützten Metriken (einschließlich Azure Blob Storage) finden Sie unter [Unterstützte Metriken von Azure Monitor](../../azure-monitor/platform/metrics-supported.md).


### <a name="accessing-metrics"></a>Zugreifen auf Metriken

> [!TIP]
> Wählen Sie zum Anzeigen von Azure CLI- oder .NET-Beispielen die hier aufgeführten entsprechenden Registerkarten aus.

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor bietet das [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) zum Lesen von Metrikdefinition und -werten. Die [Beispielcode](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) zeigt, wie das SDK mit unterschiedlichen Parametern verwendet wird. Sie benötigen `0.18.0-preview` oder eine höhere Version für Speichermetriken.
 
Ersetzen Sie in diesen Beispielen den Platzhalter `<resource-ID>` durch die Ressourcen-ID des gesamten Speicherkontos oder des Blobspeicherdiensts. Sie finden diese Ressourcen-IDs im Azure-Portal auf der jeweiligen Seite **Eigenschaften** Ihres Speicherkontos.

Ersetzen Sie die Variable `<subscription-ID>` durch die ID Ihres Abonnements. Eine Anleitung zum Abrufen von Werten für `<tenant-ID>`, `<application-ID>`, und `<AccessKey>` finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>Auflisten der Metrikdefinition auf Kontoebene

Das folgende Beispiel zeigt, wie Sie die Metrikdefinition auf der Kontoebene auflisten:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Lesen von Metrikwerten auf Kontoebene

Das folgende Beispiel zeigt, wie Sie `UsedCapacity`-Daten auf der Kontoebene lesen:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Lesen von Werten mehrdimensionaler Metriken

Für mehrdimensionale Metriken müssen Sie Metadatenfilter definieren, wenn Metrikdaten für bestimmte Dimensionswerte gelesen werden sollen.

Im folgenden Beispiel wird gezeigt, wie Metrikdaten bei Metriken mit Unterstützung für mehrere Dimensionen gelesen werden:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Auflisten der Metrikdefinition

Sie können die Metrikdefinition Ihres Speicherkontos oder des Blobspeicherdiensts auflisten. Verwenden Sie das Cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

Ersetzen Sie in diesem Beispiel den Platzhalter `<resource-ID>` durch die Ressourcen-ID des gesamten Speicherkontos oder die Ressourcen-ID des Blobspeicherdiensts.  Sie finden diese Ressourcen-IDs im Azure-Portal auf der jeweiligen Seite **Eigenschaften** Ihres Speicherkontos.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lesen von Metrikwerten

Sie können die Metrikwerte auf der Kontoebene Ihres Speicherkontos oder des Blobspeicherdiensts lesen. Verwenden Sie das Cmdlet [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Auflisten der Metrikdefinition auf Kontoebene

Sie können die Metrikdefinition Ihres Speicherkontos oder des Blobspeicherdiensts auflisten. Verwenden Sie den Befehl [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
Ersetzen Sie in diesem Beispiel den Platzhalter `<resource-ID>` durch die Ressourcen-ID des gesamten Speicherkontos oder die Ressourcen-ID des Blobspeicherdiensts. Sie finden diese Ressourcen-IDs im Azure-Portal auf der jeweiligen Seite **Eigenschaften** Ihres Speicherkontos.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lesen der Metrikwerte auf der Kontoebene

Sie können die Metrikwerte Ihres Speicherkontos oder des Blobspeicherdiensts lesen. Verwenden Sie den Befehl [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Vorlage](#tab/template)

N/V.

---

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Sie können auf Ressourcenprotokolle entweder als Blob in einem Speicherkonto, als Ereignisdaten oder über Log Analytics-Abfragen zugreifen.

Eine ausführliche Referenz zu den in diesen Protokollen angezeigten Feldern finden Sie in der [Überwachungsdatenreferenz zu Azure Blob Storage](monitor-blob-storage-reference.md).

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen für die öffentliche Cloud für Vorschautests zur Verfügung. Diese Vorschauversion ermöglicht Protokolle für Blobs (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen, Tabellen, Universell V1-Premium-Speicherkonten und Universell V2-Speicherkonten. Klassische Speicherkonten werden nicht unterstützt.

Protokolleinträge werden nur erstellt, wenn Anforderungen für den Dienstendpunkt gestellt wurden. Wenn beispielsweise ein Speicherkonto Aktivität im Blob-Endpunkt, jedoch nicht im Tabellen- oder Warteschlangenendpunkt aufweist, werden nur Protokolle für den Blobdienst erstellt. Azure Storage-Protokolle enthalten ausführliche Informationen über erfolgreiche und fehlgeschlagene Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert.

### <a name="log-authenticated-requests"></a>Protokollieren von authentifizierten Anforderungen

 Die folgenden Typen authentifizierter Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen
- Fehlerhafte Anforderungen, einschließlich Timeout-, Drosselungs-, Netzwerk- und Autorisierungsfehler sowie anderer Fehler
- Anforderungen, die eine SAS (Shared Access Signature) oder OAuth verwenden, einschließlich fehlerhafter und erfolgreicher Anforderungen
- Anforderungen an Analysedaten (klassische Protokolldaten im Container **$logs** und klassische Metrikdaten in den **$metric**-Tabellen)

Anforderungen, die durch den Blobspeicherdienst selbst erfolgen, wie z. B. Protokollerstellungs- oder -löschvorgänge, werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten finden Sie unter [Protokollierte Storage Analytics-Vorgänge und -Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) und [Storage Analytics-Protokollformat](monitor-blob-storage-reference.md).

### <a name="log-anonymous-requests"></a>Protokollieren anonymer Anforderungen

 Die folgenden Typen anonymer Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen
- Serverfehler
- Timeoutfehler für Client und Server
- Mit Fehlercode 304 (nicht geändert) fehlgeschlagene GET-Anforderungen

Alle anderen fehlgeschlagenen, anonymen Anforderungen werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten finden Sie unter [Protokollierte Storage Analytics-Vorgänge und -Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) und [Storage Analytics-Protokollformat](monitor-blob-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Zugreifen auf Protokolle in einem Speicherkonto

Protokolle werden als Blobs angezeigt, die im Zielspeicherkonto in einem Container gespeichert sind. Die erfassten Daten werden in einem einzelnen Blob als durch Zeilen getrennte JSON-Nutzlast gespeichert. Für den Namen des Blobs wird diese Benennungskonvention verwendet:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Hier sehen Sie ein Beispiel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Zugreifen auf Protokolle in einem Event Hub

An einen Event Hub gesendete Protokolle werden nicht als Datei gespeichert. Sie können jedoch überprüfen, ob der Event Hub die Protokollinformationen empfangen hat. Wechseln Sie im Azure-Portal zu Ihrem Event Hub, und vergewissern Sie sich, dass die Anzahl **eingehender Nachrichten** größer als Null ist. 

![Überwachungsprotokolle](media/monitor-blob-storage/event-hub-log.png)

Mithilfe von SIEM- (Security Information & Event Management) und Überwachungstools können Sie auf die an Ihren Event Hub gesendeten Protokolldaten zugreifen und diese lesen. Unter [Wie kann ich die an meinen Event Hub gesendeten Überwachungsdaten nutzen?](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) finden Sie weitere Informationen dazu.

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Zugreifen auf Protokolle in einem Log Analytics-Arbeitsbereich

Mithilfe von Azure Monitor-Protokollabfragen können Sie auf Protokolle zugreifen, die an einen Log Analytics-Arbeitsbereich gesendet wurden.

Weitere Informationen finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../../azure-monitor/log-query/log-analytics-tutorial.md).

Die Daten werden in der **StorageBlobLog**-Tabelle gespeichert. Protokolle für Data Lake Storage Gen2 werden nicht in einer dedizierten Tabelle angezeigt. Dies liegt daran, dass Data Lake Storage Gen2 kein Dienst ist. Es handelt sich dabei um eine Reihe von Funktionen, die Sie in Ihrem Speicherkonto aktivieren können. Wenn Sie diese Funktionen aktiviert haben, werden die entsprechenden Protokolle weiterhin in der Tabelle StorageBlobLogs angezeigt. 

#### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

Im Folgenden finden Sie einige Abfragen, die Sie in die **Protokollsuchleiste** eingeben können, um die Überwachung Ihres Blobspeichers zu vereinfachen. Diese Abfragen arbeiten mit der [neuen Sprache](../../azure-monitor/log-query/log-query-overview.md).

> [!IMPORTANT]
> Wenn Sie **Protokolle** im Speicherkonto-Ressourcengruppenmenü auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf die aktuelle Ressourcengruppe festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressourcengruppe umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Ressourcen oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](../../azure-monitor/log-query/scope.md).

Verwenden Sie diese Abfragen, um Ihre Azure Storage-Konten zu überwachen:

* Abfrage zum Auflisten der 10 häufigsten Fehler in den letzten 3 Tagen.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Abfrage zum Auflisten der 10 häufigsten Vorgänge, die in den letzten 3 Tagen die meisten Fehler verursacht haben.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Abfrage zum Auflisten der 10 häufigsten Vorgänge mit der längsten End-to-End-Latenz in den letzten 3 Tagen.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Abfrage zum Auflisten aller Vorgänge, die in den letzten 3 Tagen serverseitige Drosselungsfehler verursacht haben.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Abfrage zum Auflisten aller Anforderungen mit anonymem Zugriff in den letzten 3 Tagen.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Abfrage zum Erstellen eines Kreisdiagramms der in den letzten 3 Tagen verwendeten Vorgänge.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Häufig gestellte Fragen

**Unterstützt Azure Storage Metriken für verwaltete oder nicht verwaltete Datenträger?**

Nein. Die Metriken für Datenträger werden von Azure Compute unterstützt. Weitere Informationen finden Sie unter [Metriken pro Datenträger für verwaltete und nicht verwaltete Datenträger](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den von Azure Blob Storage erstellten Protokollen und Metriken finden Sie in der [Überwachungsdatenreferenz zu Azure Blob Storage](monitor-blob-storage-reference.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Weitere Informationen zur Migration von Metriken finden Sie unter [Migration von Azure Storage-Metriken](../common/storage-metrics-migration.md).