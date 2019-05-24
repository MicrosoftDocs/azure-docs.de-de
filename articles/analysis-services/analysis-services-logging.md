---
title: Diagnoseprotokollierung für Azure Analysis Services | Microsoft-Dokumentation
description: Informationen zum Einrichten der Diagnoseprotokollierung für Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2303d385d3d688050a8d82c07e78a68588f41e88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66142621"
---
# <a name="setup-diagnostic-logging"></a>Einrichten der Diagnoseprotokollierung

Die Überwachung der Leistung Ihrer Server ist ein wesentlicher Bestandteil jeder Analysis Services-Lösung. Mit [Diagnoseprotokollen auf Azure-Ressourcenebene](../azure-monitor/platform/diagnostic-logs-overview.md) können Sie Protokolle überwachen und an [Azure Storage](https://azure.microsoft.com/services/storage/) senden, diese in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) streamen und in [Azure Monitor-Protokolle](../azure-monitor/azure-monitor-log-hub.md) exportieren.

![Diagnoseprotokollierung für Storage, Event Hubs oder Azure Monitor-Protokolle](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Protokollierte Daten

Sie können die Kategorien **Modul**, **Dienst** und **Metriken** auswählen.

### <a name="engine"></a>Motor

Bei Auswahl von **Modul** werden alle [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) protokolliert. Einzelne Ereignisse können nicht ausgewählt werden. 

|xEvent-Kategorien |Ereignisname  |
|---------|---------|
|Sicherheitsüberwachung    |   Audit Login      |
|Sicherheitsüberwachung    |   Audit Logout      |
|Sicherheitsüberwachung    |   Audit Server Starts And Stops      |
|Statusberichte     |   Progress Report Begin      |
|Statusberichte     |   Progress Report End      |
|Statusberichte     |   Progress Report Current      |
|Abfragen     |  Query Begin       |
|Abfragen     |   Query End      |
|Befehle     |  Command Begin       |
|Befehle     |  Command End       |
|Fehler und Warnungen     |   Error      |
|Entdecken     |   Discover End      |
|Benachrichtigung     |    Benachrichtigung     |
|Sitzung     |  Session Initialize       |
|Sperren    |  Deadlock       |
|Verarbeitung von Abfragen     |   VertiPaq SE Query Begin      |
|Verarbeitung von Abfragen     |   VertiPaq SE Query End      |
|Verarbeitung von Abfragen     |   VertiPaq SE Query Cache Match      |
|Verarbeitung von Abfragen     |   Direct Query Begin      |
|Verarbeitung von Abfragen     |  Direct Query End       |

### <a name="service"></a>Dienst

|Vorgangsname  |Auftreten bei folgendem Vorgang  |
|---------|---------|
|ResumeServer     |    Fortsetzen eines Servers     |
|SuspendServer    |   Anhalten eines Servers      |
|DeleteServer     |    Löschen eines Servers     |
|RestartServer    |     Benutzer startet einen Server über SSMS oder PowerShell neu.    |
|GetServerLogFiles    |    Benutzer exportiert Serverprotokoll über PowerShell.     |
|ExportModel     |   Benutzer exportiert ein Modell im Portal mithilfe von „Öffnen“ in Visual Studio.     |

### <a name="all-metrics"></a>Alle Metriken

Mit der Kategorie „Metriken“ werden dieselben [Servermetriken](analysis-services-monitor.md#server-metrics) protokolliert, die in „Metriken“ angezeigt werden.

## <a name="setup-diagnostics-logging"></a>Einrichten der Diagnoseprotokollierung

### <a name="azure-portal"></a>Azure-Portal

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) > „Server“ im linken Navigationsbereich auf **Diagnoseprotokolle** und dann auf **Diagnose aktivieren**.

    ![Aktivieren der Diagnoseprotokollierung für Azure Cosmos DB im Azure-Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Geben Sie in **Diagnoseeinstellungen** die folgenden Optionen an: 

    * **Name**: Geben Sie einen Namen für die zu erstellenden Protokolle ein.

    * **In einem Speicherkonto archivieren**. Sie benötigen ein vorhandenes Speicherkonto, mit dem eine Verbindung hergestellt werden kann, um diese Option verwenden zu können. Siehe [Erstellen Sie ein Speicherkonto](../storage/common/storage-create-storage-account.md). Befolgen Sie die Anweisungen zum Erstellen eines allgemeinen Resource Manager-Kontos, und wählen Sie dann Ihr Speicherkonto aus, indem Sie zu dieser Seite im Portal zurückwechseln. Es dauert möglicherweise einige Minuten, bis neu erstellte Speicherkonten im Dropdownmenü angezeigt werden.
    * **An einen Event Hub streamen**. Sie benötigen einen vorhandenen Event Hub-Namespace und einen Event Hub. mit dem eine Verbindung hergestellt werden kann, um diese Option verwenden zu können. Weitere Informationen finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md). Kehren Sie anschließend auf diese Seite im Portal zurück, um den Event Hub-Namespace und den Richtliniennamen auszuwählen.
    * **An Azure Monitor senden (Log Analytics-Arbeitsbereich)**. Für diese Option können Sie entweder einen vorhandenen Arbeitsbereich verwenden oder im Portal [eine neue Arbeitsbereichsressource erstellen](../azure-monitor/learn/quick-create-workspace.md). Weitere Informationen zum Anzeigen Ihrer Protokolle finden Sie unter [Anzeigen von Protokollen im Log Analytics-Arbeitsbereich](#view-logs-in-log-analytics-workspace) in diesem Artikel.

    * **Modul:** Wählen Sie diese Option aus, um xEvents zu protokollieren. Wenn die Archivierung in einem Speicherkonto erfolgt, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf des Aufbewahrungszeitraums automatisch gelöscht.
    * **Dienst**. Wählen Sie diese Option aus, um Ereignisse auf Dienstebene zu protokollieren. Wenn Sie auf einem Speicherkonto archivieren, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf des Aufbewahrungszeitraums automatisch gelöscht.
    * **Metriken:** Wählen Sie diese Option aus, um ausführliche Daten in [Metriken](analysis-services-monitor.md#server-metrics) zu speichern. Wenn Sie auf einem Speicherkonto archivieren, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf des Aufbewahrungszeitraums automatisch gelöscht.

3. Klicken Sie auf **Speichern**.

    Sie erhalten die Fehlermeldung: „Fehler beim Aktualisieren der Diagnose für \<Arbeitsbereichsname>. Das Abonnement \<Abonnement-ID> ist nicht für die Verwendung von microsoft.insights registriert.“ Befolgen Sie die Anweisungen zur [Problembehandlung bei der Azure-Diagnose](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage), um das Konto zu registrieren, und wiederholen Sie dann dieses Verfahren.

    Wenn Sie ändern möchten, wie die Diagnoseprotokolle zu einem zukünftigen Zeitpunkt gespeichert werden, können Sie zum Ändern der Einstellungen zu dieser Seite zurückkehren.

### <a name="powershell"></a>PowerShell

Hier sind die grundlegenden Befehle zum Einstieg aufgeführt. Detaillierte Hilfe zum Einrichten der Protokollierung in einem Speicherkonto mithilfe von PowerShell finden Sie im Tutorial weiter unten in diesem Artikel.

Verwenden Sie die folgenden Befehle, um die Metrik- und Diagnoseprotokollierung mit PowerShell zu aktivieren:

- Verwenden Sie den folgenden Befehl, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Die Speicherkonto-ID ist die Ressourcen-ID für das Speicherkonto, an das die Protokolle gesendet werden sollen.

- Verwenden Sie den folgenden Befehl, um das Streamen von Diagnoseprotokollen an eine Event Hub-Instanz zu aktivieren:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Die Azure Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Verwenden Sie den folgenden Befehl, um das Senden von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Sie können die Ressourcen-ID mit dem folgenden Befehl aus Ihrem Log Analytics-Arbeitsbereich abrufen:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

### <a name="rest-api"></a>REST-API

Informieren Sie sich darüber, wie Sie [Diagnoseeinstellungen mithilfe der Azure Monitor-REST-API ändern](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

Informieren Sie sich darüber, wie Sie [Diagnoseeinstellungen beim Erstellen von Ressourcen mithilfe einer Resource Manager-Vorlage aktivieren](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Verwalten Ihrer Protokolle

Die Protokolle stehen in der Regel einige Stunden nach dem Einrichten der Protokollierung zur Verfügung. Die Verwaltung der Protokolle im Speicherkonto ist Ihre Aufgabe:

* Verwenden Sie zum Schützen der Protokolle standardmäßige Azure-Zugriffssteuerungsmethoden, indem Sie einschränken, wer darauf zugreifen kann.
* Löschen Sie Protokolle, die im Speicherkonto nicht mehr aufbewahrt werden sollen.
* Legen Sie eine Beibehaltungsdauer fest, sodass ältere Protokolle aus dem Speicherkonto gelöscht werden.

## <a name="view-logs-in-log-analytics-workspace"></a>Anzeigen von Protokollen im Log Analytics-Arbeitsbereich

Metriken und Serverereignisse werden in Ihren Log Analytics-Arbeitsbereich in xEvents integriert, sodass eine parallele Analyse erfolgt. Der Log Analytics-Arbeitsbereich kann zudem so konfiguriert werden, dass Ereignisse von anderen Azure-Diensten empfangen werden, sodass die Diagnoseprotokollierungsdaten Ihrer Architektur ganzheitlich angezeigt werden.

Öffnen Sie zum Anzeigen Ihrer Diagnosedaten im Log Analytics-Arbeitsbereich im linken Menü **Protokolle**.

![Protokollsuchoptionen im Azure-Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Erweitern Sie im Abfrage-Generator **LogManagement** > **AzureDiagnostics**. AzureDiagnostics umfasst Modul- und Dienstereignisse. Beachten Sie, dass direkt eine Abfrage erstellt wird. Das Feld EventClass\_s enthält xEvent-Namen, die Ihnen vertraut vorkommen können, wenn Sie xEvents für die lokale Protokollierung verwendet haben. Wenn Sie auf **EventClass\_s** oder einen der Ereignisnamen klicken, wird im Log Analytics-Arbeitsbereich eine Abfrage erstellt. Speichern Sie die Abfragen, um sie zu einem späteren Zeitpunkt wiederverwenden zu können.

### <a name="example-query"></a>Beispielabfrage
Diese Abfrage berechnet die CPU für jedes Abfrageende-/Aktualisierungsendeergebnis für eine Modelldatenbank und einen Server und gibt sie zurück:

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and ServerName_s =~"MyServerName" and DatabaseName_s == "Adventure Works Localhost" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| extend Engine_CPUTime=extract(@"([^,]*)", 1,CPUTime_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g ,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs,Engine_CPUTime
| join kind=leftouter (
window
    | where OperationName == "ProgressReportEnd" or (OperationName == "VertiPaqSEQueryEnd" and EventSubclass_s  != 10) or OperationName == "DiscoverEnd" or (OperationName has "CommandEnd" and EventSubclass_s != 38)
    | summarize sum_Engine_CPUTime = sum(extract(@"([^,]*)", 1,CPUTime_s, typeof(long))) by RootActivityId_g
    ) on RootActivityId_g
| extend totalCPU = sum_Engine_CPUTime + Engine_CPUTime

```


Ihnen stehen Hunderte von Abfragen zur Verwendung zur Verfügung. Weitere Informationen zu Abfragen finden Sie unter [Erste Schritte mit Azure Monitor-Protokollabfragen](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Aktivieren der Protokollierung mit PowerShell

In diesem kurzen Tutorial erstellen Sie ein Speicherkonto im gleichen Abonnement und der gleichen Ressourcengruppe, in denen sich Ihr Analysis Services-Server befindet. Anschließend verwenden Sie „Set-AzDiagnosticSetting“, um die Diagnoseprotokollierung zu aktivieren, damit Ausgaben an das neue Speicherkonto gesendet werden.

### <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie die folgenden Ressourcen:

* Einen vorhandenen Azure Analysis Services-Server. Anweisungen zum Erstellen einer Serverressource finden Sie unter [Erstellen eines Servers im Azure-Portal](analysis-services-create-server.md) oder [Erstellen eines Azure Analysis Services-Servers mithilfe von PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Herstellen von Verbindungen mit Ihren Abonnements

Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:  

```powershell
Connect-AzAccount
```

Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. Azure PowerShell ruft alle Abonnements ab, die diesem Konto zugeordnet sind, und verwendet standardmäßig das erste Abonnement.

Wenn Sie über mehrere Abonnements verfügen, müssen Sie unter Umständen ein bestimmtes Abonnement angeben, das zum Erstellen der Azure Key Vault-Instanz verwendet wurde. Geben Sie Folgendes ein, um die Abonnements für Ihr Konto anzuzeigen:

```powershell
Get-AzSubscription
```

Geben Sie anschließend Folgendes ein, um das Abonnement anzugeben, das dem protokollierten Azure Analysis Services-Konto zugeordnet ist:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Wenn Ihrem Konto mehrere Abonnements zugeordnet sind, müssen Sie unbedingt das Abonnement angeben.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Erstellen eines neuen Speicherkontos für Ihre Protokolle

Sie können ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, sofern es sich im gleichen Abonnement wie der Server befindet. Für dieses Tutorial erstellen Sie ein neues Speicherkonto für Analysis Services-Protokolle. Der Einfachheit halber speichern Sie die Details zum Speicherkonto in einer Variable mit dem Namen **sa**.

Zudem verwenden Sie dieselbe Ressourcengruppe, in der auch der Analysis Services-Server enthalten ist. Ersetzen Sie die Werte für `awsales_resgroup`, `awsaleslogs` und `West Central US` durch Ihre eigenen Werte:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifizieren des Serverkontos für Ihre Protokolle

Legen Sie den Kontonamen auf die Variable **account** fest, wobei ResourceName der Name des Kontos ist.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivieren der Protokollierung

Verwenden Sie zum Aktivieren der Protokollierung das Cmdlet „Set-AzDiagnosticSetting“ zusammen mit den Variablen für das neue Speicherkonto, das Serverkonto und die Kategorie. Führen Sie den folgenden Befehl aus, und legen Sie das Flag **-Enabled** auf **$true** fest:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Die Ausgabe sollte in etwa wie das folgende Beispiel aussehen:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Diese Ausgabe bestätigt, dass die Protokollierung für den Server jetzt aktiviert ist und Informationen im Speicherkonto gespeichert werden.

Sie können außerdem eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, sodass ältere Protokolle automatisch gelöscht werden. Richten Sie beispielsweise eine Aufbewahrungsrichtlinie ein, bei der das Flag **-RetentionEnabled** auf **$true** und der Parameter **-RetentionInDays** auf **90** festgelegt sind. Protokolle, die älter als 90 Tage sind, werden dann automatisch gelöscht.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zur [Diagnoseprotokollierung auf Azure-Ressourcenebene](../azure-monitor/platform/diagnostic-logs-overview.md).

Weitere Informationen finden Sie in der PowerShell-Hilfe unter [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting).
