---
title: Diagnoseprotokollierung in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie in Azure Cosmos DB gespeicherte Daten protokollieren und überwachen können.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 67a6eec938a4a18455e4063925e21e26fe362f76
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243472"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Diagnoseprotokollierung in Azure Cosmos DB 

Sobald Sie damit begonnen haben, mindestens eine Azure Cosmos DB-Datenbank zu verwenden, möchten Sie wahrscheinlich überwachen, wie und wann auf Ihre Datenbanken zugegriffen wird. Dieser Artikel enthält eine Übersicht über die Protokolle, die auf der Azure-Plattform verfügbar sind. Sie erfahren, wie die Diagnoseprotokollierung für Überwachungszwecke zum Senden von Protokollen an [Azure Storage](https://azure.microsoft.com/services/storage/) aktiviert wird, wie Protokolle an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gestreamt werden und wie Protokolle in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) exportiert werden.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>In Azure verfügbare Protokolle

Bevor wir uns mit der Überwachung Ihres Azure Cosmos DB-Kontos befassen, sollten einige Punkte zur Protokollierung und Überwachung verdeutlicht werden. Auf der Azure-Plattform stehen verschiedene Typen von Protokollen bereit. Es gibt [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure-Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure-Metriken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), Ereignisse, Taktüberwachung, Vorgangsprotokolle usw. Eine Vielzahl von Protokollen ist verfügbar. Die vollständige Liste der Protokolle ist in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) im Azure-Portal enthalten. 

Die folgende Abbildung zeigt die verschiedenen Arten verfügbarer Azure-Protokolle:

![Verschiedene Arten von Azure-Protokollen](./media/logging/azurelogging.png)

In der Abbildung sind die **Computeressourcen** die Azure-Ressourcen, für die Sie auf das Microsoft-Gastbetriebssystem zugreifen können. Beispielsweise werden Azure Virtual Machines, VM-Skalierungsgruppen, Azure Container Service usw. als Computeressourcen angesehen. Computeressourcen generieren Aktivitätsprotokolle, Diagnoseprotokolle und Anwendungsprotokolle. Weitere Informationen finden Sie im Artikel [Quellen für Daten in Azure Monitor](../azure-monitor/platform/data-sources.md).

**Nicht-Computeressourcen** sind Ressourcen, bei denen Sie nicht auf das zugrunde liegende Betriebssystem zugreifen und direkt mit der Ressource arbeiten können. Beispiele hierfür sind Netzwerksicherheitsgruppen, Logik-Apps usw. Azure Cosmos DB ist eine Nicht-Computeressource. Sie können Protokolle für Nicht-Computeressourcen im Aktivitätsprotokoll anzeigen, oder Sie können im Portal die Option „Diagnoseprotokolle“ aktivieren. Weitere Informationen finden Sie im Artikel [Quellen für Daten in Azure Monitor](../azure-monitor/platform/data-sources.md).

Das Aktivitätsprotokoll zeichnet die Vorgänge auf einer Abonnementebene für Azure Cosmos DB auf. Vorgänge wie ListKeys, Write DatabaseAccounts usw. werden protokolliert. Diagnoseprotokolle ermöglichen eine feiner abgestimmte Protokollierung, und Sie können DataPlaneRequests (Create, Read, Query usw.) sowie MongoRequests protokollieren.


In diesem Artikel liegt der Schwerpunkt auf dem Azure-Aktivitätsprotokoll, Azure-Diagnoseprotokollen und Azure Metriken. Was ist der Unterschied zwischen diesen drei Protokollen? 

### <a name="azure-activity-log"></a>Azure-Aktivitätsprotokoll

Das Azure-Aktivitätsprotokoll ist ein Abonnementprotokoll, das Einblicke in Ereignisse auf Abonnementebene ermöglicht, die in Azure aufgetreten sind. Das Aktivitätsprotokoll meldet Ereignisse der Steuerungsebene für Ihre Abonnements unter der Kategorie „Administration“. Sie können das Aktivitätsprotokoll verwenden, um Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) zu ermitteln, die für die Ressourcen Ihres Abonnements ausgeführt wurden. Sie können auch den Status des Vorgangs und andere relevante Eigenschaften verstehen. 

Das Aktivitätsprotokoll unterscheidet sich von Diagnoseprotokollen. Das Aktivitätsprotokoll enthält Daten zu den externen Vorgängen an einer Ressource (die _Steuerungsebene_). Im Kontext von Azure Cosmos DB sind Vorgänge auf Steuerungsebene das Erstellen eines Containers, Auflisten von Schlüsseln, Löschen von Schlüsseln, Auflisten der Datenbank usw. Diagnoseprotokolle werden von einer Ressource ausgegeben und enthalten Informationen zu den Vorgängen dieser Ressource (der _Datenebene_). Einige Beispiele für Vorgänger der Datenebene im Diagnoseprotokoll sind „Delete“, „Insert“ und „ReadFeed“.

Aktivitätsprotokolle (Vorgänge auf Steuerungsebene) können weitaus umfangreicher sein und die vollständige E-Mail-Adresse des Aufrufers, die IP-Adresse des Aufrufers, den Ressourcennamen, den Vorgangsnamen und die Mandanten-ID usw. enthalten. Das Aktivitätsprotokoll enthält verschiedene [Kategorien](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) von Daten. Umfassende Informationen zu den Schemas dieser Kategorien finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnoseprotokolle können jedoch restriktiv sein, da personenbezogene Daten häufig aus diesen Protokollen entfernt werden. Daher ist möglicherweise die IP-Adresse des Aufrufers vorhanden, jedoch wurde der letzte Inhaltsteil entfernt.

### <a name="azure-metrics"></a>Azure-Metriken

[Azure-Metriken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) umfassen die wichtigsten Typen von Azure-Telemetriedaten (auch als _Leistungsindikatoren_ bezeichnet), die von den meisten Azure-Ressourcen ausgegeben werden. Mit Metriken können Sie Informationen zu Durchsatz, Speicher, Konsistenz, Verfügbarkeit und Latenz Ihrer Azure Cosmos DB-Ressourcen anzeigen. Weitere Informationen finden Sie unter [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure-Diagnoseprotokolle

Azure-Diagnoseprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche und in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp. Diagnoseprotokolle auf Ressourcenebene unterscheiden sich auch von Diagnoseprotokollen auf Gastbetriebssystemebene. Diagnoseprotokolle auf Gastbetriebssystemebene werden von einem Agent erfasst, der auf einem virtuellen Computer oder einem anderen Ressourcentyp ausgeführt wird. Diagnoseprotokolle auf Ressourcenebene erfordern keinen Agent und erfassen ressourcenspezifischen Daten von der Azure-Plattform selbst. Diagnoseprotokolle auf Gastbetriebssystemebene erfassen Daten vom Betriebssystem und von Anwendungen, die auf einem virtuellen Computer ausgeführt werden.

![Diagnoseprotokollierung für Storage, Event Hubs oder Azure Monitor-Protokolle](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Was wird in Azure-Diagnoseprotokollen erfasst?

* Alle authentifizierten Back-End-Anforderungen (TCP/REST) werden über alle APIs hinweg protokolliert. Dies umfasst auch Anforderungen, die aufgrund von Zugriffsberechtigungen, Systemfehlern oder fehlerhaften Anforderungen nicht erfolgreich sind. Unterstützung für vom Benutzer initiierte Graph-, Cassandra- und Table API-Anforderungen ist zurzeit nicht verfügbar.
* Vorgänge für die Datenbank selbst (hierzu zählen CRUD-Vorgänge für alle Dokumente, Container und Datenbanken).
* Vorgänge für Kontoschlüssel (dies umfasst das Erstellen, Ändern oder Löschen der Schlüssel).
* Bei nicht authentifizierten Anforderungen wird eine 401-Antwort zurückgegeben. Dies gilt beispielsweise für Anforderungen ohne Bearertoken, falsch formatierte oder abgelaufene Anforderungen und Anforderungen, deren Token ungültig ist.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Aktivieren der Protokollierung im Azure-Portal

Zum Aktivieren der Diagnoseprotokollierung sind die folgenden Ressourcen erforderlich:

* Azure Cosmos DB-Konto, -Datenbank und -Container müssen vorhanden sein. Anweisungen zum Erstellen dieser Ressourcen finden Sie unter [Erstellen eines Datenbankkontos mit dem Azure-Portal](create-sql-api-dotnet.md#create-account), [Azure CLI-Beispiele](cli-samples.md) und [PowerShell-Beispiele](powershell-samples.md).

Führen Sie zum Aktivieren der Diagnoseprotokollierung im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) in Ihrem Azure Cosmos DB-Konto im linken Navigationsbereich **Diagnoseprotokolle** aus, und wählen Sie dann **Diagnose aktivieren** aus.

    ![Aktivieren der Diagnoseprotokollierung für Azure Cosmos DB im Azure-Portal](./media/logging/turn-on-portal-logging.png)

2. Führen Sie auf der Seite **Diagnoseeinstellungen** die folgenden Schritte aus: 

    * **Name:** Geben Sie einen Namen für die zu erstellenden Protokolle ein.

    * **Archivieren in einem Speicherkonto:** Sie benötigen ein vorhandenes Speicherkonto, mit dem eine Verbindung hergestellt werden kann, um diese Option verwenden zu können. Informationen zum Erstellen eines neuen Speicherkontos im Portal finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-create-storage-account.md). Befolgen Sie die Anweisungen zum Erstellen eines Azure Resource Manager-Kontos für allgemeine Zwecke. Kehren Sie anschließend auf diese Seite im Portal zurück, und wählen Sie Ihr Speicherkonto aus. Es dauert möglicherweise einige Minuten, bis neu erstellte Speicherkonten im Dropdownmenü angezeigt werden.
    * **Streamen an einen Event Hub:** Für diese Option benötigen Sie einen vorhandenen Event Hubs-Namespace und einen Event Hub, mit dem eine Verbindung hergestellt werden kann. Weitere Informationen zum Erstellen eines Event Hubs-Namespaces finden Sie unter [Erstellen eines Event Hubs-Namespaces und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md). Kehren Sie anschließend auf diese Seite im Portal zurück, um den Event Hubs-Namespace und den Richtliniennamen auszuwählen.
    * **Senden an Log Analytics:** Für diese Option können Sie entweder einen vorhandenen Arbeitsbereich verwenden oder über das Portal mithilfe der Schritte zum [Erstellen eines neuen Arbeitsbereichs](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) einen neuen Log Analytics-Arbeitsbereich erstellen. Weitere Informationen zum Anzeigen Ihrer Protokolle in Azure Monitor-Protokolle finden Sie unter „Anzeigen von Protokollen in Azure Monitor-Protokolle“.
    * **Protokollieren von „DataPlaneRequests“:** Verwenden Sie diese Option, um Back-End-Anforderungen der zugrunde liegenden verteilten Azure Cosmos DB-Plattform für SQL-, Graph-, MongoDB-, Cassandra- und Tabellen-API-Konten zu protokollieren. Wenn die Archivierung in einem Speicherkonto erfolgt, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf der Aufbewahrungsdauer automatisch gelöscht.
    * **Protokollieren von „MongoRequests“:** Wählen Sie diese Option aus, um vom Benutzer initiierte Anforderungen vom Front-End von Azure Cosmos DB für Cosmos-Konten zu protokollieren, die mit der API für MongoDB von Azure Cosmos DB konfiguriert wurden. Wenn die Archivierung in einem Speicherkonto erfolgt, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf der Aufbewahrungsdauer automatisch gelöscht.
    * **Metrikanforderungen:** Verwenden Sie diese Option, um ausführliche Daten in [Azure-Metriken](../azure-monitor/platform/metrics-supported.md) zu speichern. Wenn die Archivierung in einem Speicherkonto erfolgt, können Sie die Beibehaltungsdauer für die Diagnoseprotokolle auswählen. Protokolle werden nach Ablauf der Aufbewahrungsdauer automatisch gelöscht.

3. Wählen Sie **Speichern** aus.

    Sie erhalten die Fehlermeldung: „Fehler beim Aktualisieren der Diagnose für \<Arbeitsbereichsname>. Das Abonnement \<Abonnement-ID> ist nicht für die Verwendung von microsoft.insights registriert“. Führen Sie die Anweisungen unter [Problembehandlung der Azure-Diagnose](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) aus, um das Konto zu registrieren, und wiederholen Sie dann dieses Verfahren.

    Wenn Sie ändern möchten, wie die Diagnoseprotokolle zu einem beliebigen zukünftigen Zeitpunkt gespeichert werden, kehren Sie zum Ändern der Einstellungen des Diagnoseprotokolls für Ihr Konto zu dieser Seite zurück.

## <a name="turn-on-logging-by-using-azure-cli"></a>Aktivieren der Protokollierung mithilfe der Azure CLI

Verwenden Sie die folgenden Befehle, um die Metrik- und Diagnoseprotokollierung mit der Azure CLI zu aktivieren:

- Verwenden Sie den folgenden Befehl, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` ist der Name des Azure Cosmos DB-Kontos. Die Ressource hat das Format „/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/<Name_des_Azure_Cosmos_Kontos>“. Dabei ist `storage-account` der Name des Speicherkontos, an das die Protokolle gesendet werden sollen. Sie können andere Protokolle erfassen, indem Sie die Parameterwerte für die Kategorie in „MongoRequests“ oder „DataPlaneRequests“ ändern. 

- Verwenden Sie den folgenden Befehl, um das Streamen von Diagnoseprotokollen an eine Event Hub-Instanz zu aktivieren:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` ist der Name des Azure Cosmos DB-Kontos. Die `event-hub-rule` ist die ID der Event Hub-Regel. 

- Verwenden Sie den folgenden Befehl, um das Senden von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

## <a name="turn-on-logging-by-using-powershell"></a>Aktivieren der Protokollierung mit PowerShell

Zum Aktivieren der Diagnoseprotokollierung mit PowerShell benötigen Sie mindestens Azure Powershell, Version 1.0.1.

Um Azure PowerShell zu installieren und Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

Wenn Sie Azure PowerShell bereits installiert haben und Ihnen die Version nicht bekannt ist, geben Sie an der Azure PowerShell-Konsole `(Get-Module azure -ListAvailable).Version` ein.  

### <a id="connect"></a>Verbindungsherstellung mit Ihren Abonnements
Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:  

```powershell
Connect-AzAccount
```

Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. Azure PowerShell ruft alle Abonnements ab, die diesem Konto zugeordnet sind, und verwendet standardmäßig das erste Abonnement.

Wenn Sie über mehrere Abonnements verfügen, müssen Sie unter Umständen ein bestimmtes Abonnement angeben, das zum Erstellen Ihres Azure-Schlüsseltresors verwendet wurde. Geben Sie den folgenden Befehl ein, um die Abonnements für Ihr Konto anzuzeigen:

```powershell
Get-AzSubscription
```

Geben Sie anschließend den folgenden Befehl ein, um das Abonnement anzugeben, das dem protokollierten Azure Cosmos DB-Konto zugeordnet ist:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Wenn Ihrem Konto mehrere Abonnements zugeordnet sind, müssen Sie unbedingt das Abonnement angeben, das Sie verwenden möchten.
>
>

Weitere Informationen zum Konfigurieren von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Erstellen eines neuen Speicherkontos für Ihre Protokolle
Sie können zwar ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, in diesem Tutorial wird jedoch ein neues Speicherkonto erstellt, das für Azure Cosmos DB-Protokolle bestimmt ist. Der Einfachheit halber speichern wir die Details zum Speicherkonto in einer Variablen mit dem Namen **sa**.

Um die Verwaltung noch weiter zu vereinfachen, verwenden wir in diesem Tutorial dieselbe Ressourcengruppe, die auch die Azure Cosmos DB-Datenbank enthält. Ersetzen Sie die Werte für die Parameter **ContosoResourceGroup**, **contosocosmosdblogs** und **North Central US** durch Ihre eigenen Werte:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Wenn Sie sich entscheiden, ein vorhandenes Speicherkonto zu verwenden, muss das Konto das gleiche Abonnement wie Ihr Azure Cosmos DB-Abonnement verwenden. Anstelle des klassischen Bereitstellungsmodells muss das Konto außerdem das Resource Manager-Bereitstellungsmodell verwenden.
>
>

### <a id="identify"></a>Bestimmen des Azure Cosmos DB-Kontos für Ihre Protokolle
Legen Sie den Azure Cosmos DB-Kontonamen auf eine Variable namens **account** fest, wobei **ResourceName** der Name des Azure Cosmos DB-Kontos ist.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Aktivieren der Protokollierung
Verwenden Sie zum Aktivieren der Protokollierung für Azure Cosmos DB das Cmdlet `Set-AzDiagnosticSetting` zusammen mit den Variablen für das neue Speicherkonto, das Azure Cosmos DB-Konto und die Kategorie, für welche Protokollierung aktiviert werden soll. Führen Sie den folgenden Befehl aus, und legen Sie das Flag **-Enabled** auf **$true** fest:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Die Ausgabe des Befehls sollte dem folgenden Beispiel ähneln:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Die Ausgabe aus dem Befehl bestätigt, dass die Protokollierung jetzt für die Datenbank aktiviert ist und Informationen in Ihrem Speicherkonto gespeichert werden.

Optional können Sie eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, mit der ältere Protokolle automatisch gelöscht werden. Legen Sie die Aufbewahrungsrichtlinie z.B. fest, indem Sie das Flag **-RetentionEnabled** auf **$true** festlegen. Legen Sie die den Parameter **-RetentionInDays** auf **90** fest, sodass Protokolle, die älter als 90 Tagen sind, automatisch gelöscht werden.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Zugreifen auf Ihre Protokolle
Azure Cosmos DB-Protokolle für die Kategorie **DataPlaneRequests** werden im Container **insights-logs-data-plane-requests** im angegebenen Speicherkonto gespeichert. 

Erstellen Sie zunächst eine Variable für den Containernamen. Die Variable wird in der gesamten exemplarischen Vorgehensweise verwendet.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Geben Sie Folgendes ein, um alle Blobs in diesem Container aufzulisten:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Die Ausgabe des Befehls sollte dem folgenden Beispiel ähneln:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Wie Sie in dieser Ausgabe sehen, wird für die Blobs eine Benennungskonvention genutzt: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Für die Werte für Datum und Uhrzeit wird UTC verwendet.

Da dasselbe Speicherkonto zum Erfassen von Protokollen für mehrere Ressourcen verwendet werden kann, kann die vollqualifizierte Ressourcen-ID im Blobnamen verwendet werden, um auf die jeweils benötigten Blobs zuzugreifen bzw. diese herunterzuladen. Zunächst wird aber beschrieben, wie Sie alle Blobs herunterladen.

Erstellen Sie zunächst einen Ordner zum Herunterladen der Blobs. Beispiel: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Rufen Sie anschließend eine Liste mit allen Blobs ab:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Reichen Sie diese Liste über den Befehl `Get-AzStorageBlobContent` so weiter, dass die Blobs in den Zielordner heruntergeladen werden:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Beim Ausführen dieses zweiten Befehls wird mit dem Trennzeichen **/** in den Blobnamen eine vollständige Ordnerstruktur unter dem Zielordner erstellt. Diese Struktur wird zum Herunterladen und Speichern der Blobs als Dateien verwendet.

Verwenden Sie Platzhalter, um Blobs selektiv herunterzuladen. Beispiel: 

* Wenn Sie über mehrere Datenbanken verfügen und Protokolle für nur eine Datenbank mit dem Namen **CONTOSOCOSMOSDB3** herunterladen möchten, verwenden Sie den folgenden Befehl:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Wenn Sie über mehrere Ressourcengruppen verfügen und nur Protokolle für eine Ressourcengruppe herunterladen möchten, verwenden Sie den Befehl `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Wenn Sie alle Protokolle für den Monat Juli 2017 herunterladen möchten, verwenden Sie den Befehl `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Sie können auch die folgenden Befehle ausführen:

* Zum Abfragen des Status von Diagnoseeinstellungen für Ihre Datenbankressource verwenden Sie den Befehl `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Zum Deaktivieren der Protokollierung der Kategorie **DataPlaneRequests** für Ihre Datenbankkontoressource verwenden Sie den Befehl `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Die in jeder dieser Abfragen zurückgegebenen Blobs werden (wie im folgenden Code gezeigt) als Text gespeichert und als JSON-Blob formatiert:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Weitere Informationen zu den Daten in jedem einzelnen JSON-Blob finden Sie unter [Interpretieren der Azure Cosmos DB-Protokolle](#interpret).

## <a name="manage-your-logs"></a>Verwalten Ihrer Protokolle

Diagnoseprotokolle werden zwei Stunden nach Ausführung des Azure Cosmos DB-Vorgangs in Ihrem Konto zur Verfügung gestellt. Die Verwaltung der Protokolle im Speicherkonto ist Ihre Aufgabe:

* Verwenden Sie zum Schützen der Protokolle standardmäßige Azure-Zugriffssteuerungsmethoden, indem Sie einschränken, wer darauf zugreifen darf.
* Löschen Sie Protokolle, die im Speicherkonto nicht mehr aufbewahrt werden sollen.
* Der Aufbewahrungszeitraum für in einem Speicherkonto archivierte Anforderungen auf der Datenebene wird im Portal konfiguriert, wenn die Einstellung **DataPlaneRequests protokollieren** ausgewählt wird. Informationen zum Ändern dieser Einstellung finden Sie unter [Aktivieren der Protokollierung im Azure-Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Anzeigen von Protokollen in Azure Monitor-Protokollen

Wenn Sie beim Aktivieren der Diagnoseprotokollierung die Option **An Log Analytics senden** ausgewählt haben, werden Diagnosedaten aus Ihrem Container innerhalb von zwei Stunden an Azure Monitor-Protokolle weitergeleitet. Wenn Sie sofort nach dem Aktivieren der Protokollierung Azure Monitor-Protokolle anzeigen, sehen Sie keine Daten. Warten Sie zwei Stunden, und wiederholen Sie den Vorgang. 

Bevor Sie Ihre Protokolle anzeigen, sollten Sie überprüfen, ob Ihr Log Analytics-Arbeitsbereich für die Verwendung der neuen Kusto-Abfragesprache aktualisiert wurde. Um dies zu überprüfen, öffnen Sie das [Azure-Portal](https://portal.azure.com), wählen Sie ganz links **Log Analytics-Arbeitsbereiche** aus, und wählen Sie dann den Arbeitsbereichsnamen wie in der folgenden Abbildung dargestellt aus. Die Seite **Log Analytics-Arbeitsbereich** wird angezeigt:

![Azure Monitor-Protokolle im Azure-Portal](./media/logging/azure-portal.png)

>[!NOTE]
>OMS-Arbeitsbereiche werden jetzt als Log Analytics-Arbeitsbereiche bezeichnet.  

Wenn die folgende Meldung auf der Seite **Log Analytics-Arbeitsbereich** angezeigt wird, wurde Ihr Arbeitsbereich nicht für die Verwendung der neuen Sprache aktualisiert. Weitere Informationen zum Upgrade auf die neue Abfragesprache finden Sie unter [Upgrade des Azure Log Analytics-Arbeitsbereichs auf die neue Protokollsuche](../log-analytics/log-analytics-log-search-upgrade.md). 

![Upgradenachricht für Azure Monitor-Protokolle](./media/logging/upgrade-notification.png)

Zum Anzeigen der Diagnosedaten in Azure Monitor-Protokolle öffnen Sie die Seite **Protokollsuche** im linken Menü oder den Bereich **Verwaltung** der Seite, wie in der folgenden Abbildung gezeigt:

![Protokollsuchoptionen im Azure-Portal](./media/logging/log-analytics-open-log-search.png)

Jetzt haben Sie die Datensammlung aktiviert und können das folgende Protokollsuchbeispiel mit der neuen Abfragesprache ausführen, um die letzten 10 Protokolle anzuzeigen: `AzureDiagnostics | take 10`.

![Beispielprotokollsuche für die 10 neuesten Protokolle](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Abfragen

Hier sind einige zusätzliche Abfragen, die Sie in das Feld **Protokollsuche** eingeben können, um die Überwachung Ihrer Azure Cosmos DB-Container zu vereinfachen. Diese Abfragen arbeiten mit der [neuen Sprache](../log-analytics/log-analytics-log-search-upgrade.md). 

Informationen zur Bedeutung der von jeder Protokollsuche zurückgegebenen Daten finden Sie unter [Interpretieren der Azure Cosmos DB-Protokolle](#interpret).

* Das folgende Beispiel zeigt eine Abfrage für alle Diagnoseprotokolle von Azure Cosmos DB für einen angegebenen Zeitraum:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* So fragen Sie die letzten 10 protokollierten Ereignisse ab:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* So fragen Sie alle Vorgänge gruppiert nach Vorgangstyp ab:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* So fragen Sie alle Vorgänge gruppiert nach **Ressource** ab:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* So fragen Sie die gesamte Benutzeraktivität gruppiert nach Ressource ab:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Dieser Befehl bezieht sich auf ein Aktivitätsprotokoll und nicht auf ein Diagnoseprotokoll.

* So fragen Sie ab, welche Vorgänge länger als 3 Millisekunden dauern:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* So fragen Sie ab, welcher Agent die Vorgänge ausführt:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* So fragen Sie ab, wann die Vorgänge mit langer Ausführungszeit ausgeführt wurden:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Weitere Informationen zur Verwendung der neuen Sprache für die Protokollsuche finden Sie unter [Grundlegendes zu Protokollsuchvorgängen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretieren Ihrer Protokolle

Die Diagnosedaten, die in Azure Storage und Azure Monitor-Protokolle gespeichert werden, verwenden ein ähnliches Schema. 

Die folgende Tabelle beschreibt die Inhalte der einzelnen Protokolleinträge.

| Azure Storage-Feld oder -Eigenschaft | Eigenschaft von Azure Monitor-Protokolle | BESCHREIBUNG |
| --- | --- | --- |
| **time** | **TimeGenerated** | Datum und Uhrzeit (UTC), zu denen der Vorgang aufgetreten ist. |
| **Ressourcen-ID** | **Ressource** | Das Azure Cosmos DB-Konto, für das Protokolle aktiviert sind.|
| **category** | **Kategorie** | Für Azure Cosmos DB-Protokolle ist **DataPlaneRequests** der einzige verfügbare Wert. |
| **operationName** | **OperationName** | Name des Vorgangs. Bei diesem Wert kann es sich um einen der folgenden Vorgänge handeln: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed, Upsert.   |
| **properties** | – | Die Inhalte dieser Felder werden in den folgenden Zeilen beschrieben. |
| **activityId** | **activityId_g** | Die eindeutige GUID für den protokollierten Vorgang. |
| **userAgent** | **userAgent_s** | Eine Zeichenfolge, die den Benutzer-Agent des Clients angibt, der die Anforderung ausführt. Das Format lautet {Name des Benutzer-Agents}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Der Typ der Ressource, auf die zugegriffen wird. Bei diesem Wert kann es sich um einen der folgenden Ressourcentypen handeln: Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction, Offer. |
| **statusCode** | **statusCode_s** | Der Antwortstatus des Vorgangs. |
| **requestResourceId** | **ResourceId** | Die Ressourcen-ID der Anforderung. Der Wert kann je nach ausgeführtem Vorgang auf databaseRid, collectionRid oder documentRid zeigen.|
| **clientIpAddress** | **clientIpAddress_s** | Die IP-Adresse des Clients. |
| **requestCharge** | **requestCharge_s** | Die Anzahl der vom Vorgang verwendeten RUs. |
| **collectionRid** | **collectionId_s** | Die eindeutige ID für die Sammlung.|
| **duration** | **duration_s** | Die Dauer des Vorgangs in Ticks. |
| **requestLength** | **requestLength_s** | Die Länge der Anforderung in Bytes. |
| **responseLength** | **responseLength_s** | Die Länge der Antwort in Bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Dieser Wert ist nicht leer, wenn [Ressourcentoken](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) für die Authentifizierung verwendet werden. Der Wert verweist auf die Ressourcen-ID des Benutzers. |

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie sich mit dem Aktivieren der Protokollierung vertraut machen und darüber hinaus ein Verständnis der von den verschiedenen Azure-Diensten unterstützten Metriken und Protokollkategorien erlangen möchten, lesen Sie die Artikel [Überblick über Metriken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) und [Übersicht über Azure-Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md).
- Lesen Sie diese Artikel durch, um sich über Event Hubs zu informieren:
   - [Was ist Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Erste Schritte mit Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Lesen Sie [Herunterladen von Metrik- und Diagnoseprotokollen aus Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Lesen Sie [Grundlegendes zu Protokollsuchvorgängen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-search-new.md).
