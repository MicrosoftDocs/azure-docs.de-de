---
title: Azure-Aktivitätsprotokoll
description: In diesem Artikel wird erläutert, wie Sie das Azure-Aktivitätsprotokoll anzeigen und an Azure Monitor-Protokolle, Azure Event Hubs und Azure Storage senden können.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 1b49faabb1c61a10418bfce3ae2e8187429981ad
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186081"
---
# <a name="azure-activity-log"></a>Azure-Aktivitätsprotokoll
Das Aktivitätsprotokoll ist ein [Plattformprotokoll](platform-logs-overview.md) in Azure, das einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Dies sind beispielsweise Informationen wie das Ändern einer Ressource oder das Starten eines virtuellen Computers. Sie können das Aktivitätsprotokoll im Azure-Portal anzeigen oder Einträge mit PowerShell und der CLI abrufen. Um zusätzliche Funktionen zu erhalten, sollten Sie eine Diagnoseeinstellung erstellen, mit der das Aktivitätsprotokoll an [Azure Monitor-Protokolle](data-platform-logs.md) gesendet wird, an Azure Event Hubs außerhalb von Azure weitergeleitet oder für die Archivierung an Azure Storage gesendet wird. In diesem Artikel wird ausführlich beschrieben, wie das Aktivitätsprotokoll angezeigt und an verschiedene Ziele gesendet wird.

Einzelheiten zum Erstellen einer Diagnoseeinstellung finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen und -metriken in Azure](diagnostic-settings.md).

> [!NOTE]
> Einträge im Aktivitätsprotokoll werden vom System generiert und können nicht geändert oder gelöscht werden.

## <a name="view-the-activity-log"></a>Anzeigen des Aktivitätsprotokolls
Auf das Aktivitätsprotokoll können Sie über die meisten Menüs im Azure-Portal zugreifen. Das Menü, in dem Sie es öffnen, bestimmt seinen anfänglichen Filter. Wenn Sie es über das Menü **Überwachen** öffnen, wird nur der Filter für das Abonnement verwendet. Wenn Sie es über das Menü einer Ressource öffnen, wird der Filter auf die betreffende Ressource festgelegt. Sie können den Filter jedoch jederzeit ändern, um alle anderen Einträge anzuzeigen. Klicken Sie auf **Filter hinzufügen**, um dem Filter weitere Eigenschaften hinzuzufügen.

![Anzeigen des Aktivitätsprotokolls](./media/activity-logs-overview/view-activity-log.png)

Eine Beschreibung der einzelnen Kategorien des Aktivitätsprotokolls finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Anzeigen des Änderungsverlaufs

Bei einigen Ereignissen können Sie den Änderungsverlauf anzeigen, in dem ersichtlich wird, welche Änderungen während dieses Ereignisses aufgetreten sind. Wählen Sie aus dem Aktivitätsprotokoll ein Ereignis aus, das Sie eingehender überprüfen möchten. Wählen Sie die Registerkarte **Änderungsverlauf (Vorschau)** aus, um alle Änderungen anzuzeigen, die mit diesem Ereignis verknüpft sind.

![Ändern der Verlaufsliste für ein Ereignis](media/activity-logs-overview/change-history-event.png)

Wenn Änderungen zu dem Ereignis vorhanden sind, sehen Sie eine Liste der Änderungen, aus denen Sie eine Auswahl treffen können. Daraufhin öffnet sich die Seite **Änderungsverlauf (Vorschau)** . Auf dieser Seite sehen Sie die Änderungen an der Ressource. Im folgenden Beispiel können Sie nicht nur erkennen, dass sich die Größe des virtuellen Computers geändert hat, sondern es wird auch die Größe des virtuellen Computers vor und nach der Änderung angezeigt. Weitere Informationen zum Änderungsverlauf finden Sie unter [Abrufen von Ressourcenänderungen](../../governance/resource-graph/how-to/get-resource-changes.md).

![Seite „Änderungsverlauf“ mit den Unterschieden](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Weitere Methoden zum Abrufen von Aktivitätsprotokollereignissen
Sie können auch mithilfe der folgenden Methoden auf Aktivitätsprotokollereignisse zugreifen.

- Verwenden Sie das Cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog), um das Aktivitätsprotokoll über PowerShell abzurufen. Siehe [Beispiele zu PowerShell in Azure Monitor](../samples/powershell-samples.md#retrieve-activity-log).
- Verwenden Sie [az monitor activity-log](/cli/azure/monitor/activity-log), um das Aktivitätsprotokoll über die CLI abzurufen.  Siehe [CLI-Beispiele für Azure Monitor](../samples/cli-samples.md#view-activity-log).
- Verwenden Sie die [Azure Monitor-REST-API](/rest/api/monitor/), um das Aktivitätsprotokoll über einen REST-Client abzurufen. 


## <a name="send-to-log-analytics-workspace"></a>Senden an den Log Analytics-Arbeitsbereich
 Senden Sie das Aktivitätsprotokoll an einen Log Analytics-Arbeitsbereich, um die Features von [Azure Monitor-Protokolle](data-platform-logs.md) zu aktivieren. Hierzu zählen Folgende:

- Korrelieren von Aktivitätsprotokolldaten mit anderen von Azure Monitor gesammelten Überwachungsdaten
- Konsolidieren von Protokolleinträgen mehrerer Azure-Abonnements und -Mandanten an einem einzigen Ort zur gemeinsamen Analyse
- Verwenden von Protokollabfragen zum Ausführen komplexer Analysen und Erhalten tiefer Einblicke in Aktivitätsprotokolleinträge
- Verwenden von Protokollwarnungen mit Aktivitätseinträgen, die eine komplexere Warnungslogik ermöglichen
- Speichern von Aktivitätsprotokolleinträgen für mehr als 90 Tage
- Keine Gebühren für die Erfassung oder Aufbewahrung von Aktivitätsprotokolldaten, die in einem Log Analytics-Arbeitsbereich gespeichert sind

[Erstellen Sie eine Diagnoseeinstellung](diagnostic-settings.md), um das Aktivitätsprotokoll an einen Log Analytics-Arbeitsbereich zu senden. Sie können das Aktivitätsprotokoll von einem einzelnen Abonnement an bis zu fünf Arbeitsbereiche senden. Für eine mandantenübergreifende Erfassung von Protokollen ist [Azure Lighthouse](../../lighthouse/index.yml) erforderlich.

Aktivitätsprotokolldaten in einem Log Analytics-Arbeitsbereich werden in der Tabelle *AzureActivity* gespeichert. Diese können Sie mit einer [Protokollabfrage](../log-query/log-query-overview.md) in [Log Analytics](../log-query/log-analytics-tutorial.md) abrufen. Die Struktur dieser Tabelle ist je nach [Kategorie des Protokolleintrags](activity-log-schema.md) verschieden. Eine Beschreibung der Tabelleneigenschaften finden Sie in der [Azure Monitor-Datenreferenz](/azure/azure-monitor/reference/tables/azureactivity).

Verwenden Sie z. B. die folgende Abfrage, um die Anzahl der Aktivitätsprotokoll-Datensätze für jede Kategorie anzuzeigen.

```kusto
AzureActivity
| summarize count() by Category
```

Verwenden Sie die folgende Abfrage, um alle Datensätze in der Kategorie „Administrativ“ abzurufen.

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Senden an Azure Event Hubs
Senden Sie das Aktivitätsprotokoll an Azure Event Hubs, um Einträge an Ressourcen außerhalb von Azure zu senden, z. B. an eine SIEM-Lösung oder andere Protokollanalyselösungen von Drittanbietern. Aktivitätsprotokollereignisse von Event Hubs werden im JSON-Format mit einem `records`-Element genutzt, das die Datensätze in jeder Payload enthält. Das Schema hängt von der Kategorie ab und wird in [Schema aus Speicherkonto und Event Hubs](activity-log-schema.md) beschrieben.

Es folgt ein Beispiel für eine Datenausgabe aus Event Hubs für ein Aktivitätsprotokoll:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Senden an Azure Storage
Senden Sie das Aktivitätsprotokoll an ein Azure Storage Konto, wenn Sie die Protokolldaten für Überwachung, statische Analyse oder Sicherungszwecke länger als 90 Tage aufbewahren möchten. Falls Sie Ihre Ereignisse nur maximal 90 Tage lang aufbewahren möchten, müssen Sie keine Archivierung in einem Speicherkonto einrichten, da Aktivitätsprotokollereignisse in der Azure-Plattform 90 Tage lang aufbewahrt werden.

Wenn sie das Aktivitätsprotokoll an Azure senden, wird bei Auftreten eines Ereignisses ein Speichercontainer im Speicherkonto erstellt. Für die Blobs im Container gilt die folgende Benennungskonvention:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ein bestimmtes Blob kann beispielsweise einen Namen aufweisen, der dem Folgenden ähnelt:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Jedes Blob vom Typ „PT1H.json“ enthält ein JSON-Blob mit Ereignissen, die innerhalb der in der Blob-URL angegebenen Stunde (Beispiel: h=12) aufgetreten sind. Während der aktuellen Stunde werden Ereignisse an die Datei „PT1H.json“ angefügt, sobald sie auftreten. Der Minutenwert (m=00) ist immer „00“, da Ressourcenprotokollereignisse stundenweise in einzelne Blobs unterteilt werden.

Jedes Ereignis wird in der Datei „PT1H.json“ mit dem folgenden Format gespeichert, die ein gemeinsames Schema auf oberster Ebene verwendet, jedoch ansonsten eindeutig für jede Kategorie ist (siehe Beschreibung in [Aktivitätsprotokollschema](activity-log-schema.md)).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Legacy-Erfassungsmethoden
In diesem Abschnitt werden Legacy-Methoden zum Erfassen des Aktivitätsprotokolls beschrieben, die vor der Einführung von Diagnoseeinstellungen verwendet wurden. Wenn Sie diese Methoden verwenden, sollten Sie den Wechsel zu Diagnoseeinstellungen in Erwägung nehmen, denn diese bieten eine bessere Funktionalität und Konsistenz mit Ressourcenprotokollen.

### <a name="log-profiles"></a>Protokollprofile
Protokollprofile sind die Legacymethode zum Senden des Aktivitätsprotokolls an Azure Storage oder Event Hubs. Verwenden Sie das folgende Verfahren, um weiterhin mit einem Protokollprofil zu arbeiten oder um es in Vorbereitung auf die Migration zu einer Diagnoseeinstellung zu deaktivieren.

1. Wählen Sie im Azure-Portal im Menü **Azure Monitor** die Option **Aktivitätsprotokoll** aus.
3. Klicken Sie auf **Diagnoseeinstellungen**.

   ![Diagnoseeinstellungen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicken Sie auf das lila Banner, um die Legacybenutzeroberfläche anzuzeigen.

    ![Legacybenutzeroberfläche](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Konfigurieren eines Protokollprofils mit PowerShell

Wenn bereits ein Protokollprofil vorhanden ist, müssen Sie zuerst das vorhandene Protokollprofil entfernen und dann ein neues Profil erstellen.

1. Ermitteln Sie mithilfe von `Get-AzLogProfile`, ob ein Protokollprofil vorhanden ist.  Wenn ein Protokollprofil vorhanden ist, notieren Sie sich die *name*-Eigenschaft.

1. Verwenden Sie `Remove-AzLogProfile`, um das Protokollprofil mithilfe des Werts aus der *name*-Eigenschaft zu entfernen.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Verwenden Sie `Add-AzLogProfile`, um ein neues Protokollprofil zu erstellen:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Eigenschaft | Erforderlich | BESCHREIBUNG |
    | --- | --- | --- |
    | Name |Ja |Name des Protokollprofils. |
    | StorageAccountId |Nein |Ressourcen-ID des Speicherkontos, in dem das Aktivitätsprotokoll gespeichert werden soll. |
    | serviceBusRuleId |Nein |Service Bus-Regel-ID für den Service Bus-Namespace, unter dem Event Hubs erstellt werden sollen. Dies ist eine Zeichenfolge im Format `{service bus resource ID}/authorizationrules/{key name}`. |
    | Standort |Ja |Kommagetrennte Liste mit den Regionen, für die Sie Aktivitätsprotokollereignisse erfassen möchten. |
    | RetentionInDays |Ja |Anzahl von Tagen für die Aufbewahrung von Ereignissen im Speicherkonto (1 bis 365). Bei einem Wert von 0 werden die Protokolle dauerhaft gespeichert. |
    | Category |Nein |Kommagetrennte Liste mit den Ereigniskategorien, die erfasst werden sollen. Mögliche Werte sind _Write_, _Delete_ und _Action_. |

### <a name="example-script"></a>Beispielskript
Es folgt ein PowerShell-Beispielskript zum Erstellen eines Protokollprofils, mit dem das Aktivitätsprotokoll sowohl in ein Speicherkonto als auch einen Event Hub geschrieben wird.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurieren eines Protokollprofils mit der Azure CLI

Wenn bereits ein Protokollprofil vorhanden ist, müssen Sie zuerst das vorhandene Protokollprofil entfernen und dann ein neues Profil erstellen.

1. Ermitteln Sie mithilfe von `az monitor log-profiles list`, ob ein Protokollprofil vorhanden ist.
2. Verwenden Sie `az monitor log-profiles delete --name "<log profile name>`, um das Protokollprofil mithilfe des Werts aus der *name*-Eigenschaft zu entfernen.
3. Verwenden Sie `az monitor log-profiles create`, um ein neues Protokollprofil zu erstellen:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Eigenschaft | Erforderlich | BESCHREIBUNG |
    | --- | --- | --- |
    | name |Ja |Name des Protokollprofils. |
    | storage-account-id |Ja |Ressourcen-ID des Speicherkontos, in dem Aktivitätsprotokolle gespeichert werden sollen. |
    | locations |Ja |Durch Leerzeichen getrennte Liste mit den Regionen, für die Sie Aktivitätsprotokollereignisse erfassen möchten. Mit `az account list-locations --query [].name` können Sie eine Liste aller Regionen für Ihr Abonnement anzeigen. |
    | days |Ja |Anzahl von Tagen für die Aufbewahrung von Ereignissen (1 bis 365). Bei einem Wert von 0 werden die Protokolle dauerhaft (d.h. für immer) gespeichert.  Falls 0, muss der Parameter „enabled“ auf „false“ festgelegt werden. |
    |enabled | Ja |„True“ oder „False“.  Wird zum Aktivieren bzw. Deaktivieren der Aufbewahrungsrichtlinie verwendet.  Ist „True“ festgelegt, muss für den Parameter „days“ ein Wert größer 0 angegeben werden.
    | categories |Ja |Durch Leerzeichen getrennte Liste mit den Ereigniskategorien, die erfasst werden sollen. Mögliche Werte sind „Write“, „Delete“ und „Action“. |


### <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich
Die Legacy-Methode zum Senden des Aktivitätsprotokolls in einen Log Analytics-Arbeitsbereich besteht darin, das Protokoll in der Arbeitsbereichskonfiguration zu verbinden. 

1. Wählen Sie im Azure-Portal im Menü **Log Analytics-Arbeitsbereiche** den Arbeitsbereich zum Erfassen des Aktivitätsprotokolls aus.
1. Wählen Sie im Menü des Arbeitsbereichs im Abschnitt **Arbeitsbereichsdatenquellen** die Option **Azure-Aktivitätsprotokoll** aus.
1. Klicken Sie auf das Abonnement, mit dem Sie eine Verbindung herstellen möchten.

    ![Screenshot des Log Analytics-Arbeitsbereichs mit ausgewähltem Azure-Aktivitätsprotokoll](media/activity-log-collect/workspaces.png)

1. Klicken Sie auf **Verbinden**, um das Aktivitätsprotokoll im Abonnement mit dem ausgewählten Arbeitsbereich zu verbinden. Wenn das Abonnement bereits mit einem anderen Arbeitsbereich verbunden ist, klicken Sie zuerst auf **Trennen**, um die Verbindung zu trennen.

    ![Verbinden von Arbeitsbereichen](media/activity-log-collect/connect-workspace.png)


Zum Deaktivieren der Einstellung führen Sie dasselbe Verfahren aus, und klicken Sie auf **Trennen**, um das Abonnement aus dem Arbeitsbereich zu entfernen.

### <a name="data-structure-changes"></a>Änderungen der Datenstruktur
Mit Diagnoseeinstellungen werden dieselben Daten wie mit der Legacy-Methode zum Senden des Aktivitätsprotokolls gesendet, jedoch mit einigen Änderungen an der Struktur der Tabelle *AzureActivity*.

Die Spalten in der folgenden Tabelle sind im aktualisierten Schema veraltet. Sie sind weiterhin in *AzureActivity* vorhanden, verfügen aber über keine Daten. Die Ersatzspalten sind nicht neu, enthalten aber dieselben Daten wie die veralteten Spalte. Sie haben ein anderes Format, sodass Sie ggf. Protokollabfragen ändern müssen, die diese Spalten verwenden. 

| Veraltete Spalte | Ersatzspalte |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Vorgangsname     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In einigen Fällen werden die Werte in diesen Spalten vollständig in Großbuchstaben angegeben. Wenn Ihre Abfrage diese Spalten umfasst, sollten Sie den [=~](/azure/kusto/query/datatypes-string-operators)-Operator verwenden, um einen Vergleich ohne Beachtung der Groß- und Kleinschreibung durchzuführen.

Die folgenden Spalten wurden *AzureActivity* im aktualisierten Schema hinzugefügt:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Überwachungslösung der Aktivitätsprotokollanalyse
Die Azure Log Analytics-Überwachungslösung wird bald veraltet sein und durch eine Arbeitsmappe ersetzt, die das aktualisierte Schema im Log Analytics-Arbeitsbereich verwendet. Sie können die Lösung weiterhin verwenden, wenn sie bereits aktiviert ist, doch ist dies nur möglich, wenn Sie das Aktivitätsprotokoll mithilfe von Legacyeinstellungen erfassen. 



### <a name="use-the-solution"></a>Verwenden der Lösung
Auf Überwachungslösungen wird im Azure-Portal über das Menü **Monitor** zugegriffen. Wählen Sie im Abschnitt **Insights** die Option **Mehr** aus, um die Seite **Übersicht** mit den Lösungskacheln zu öffnen. Die Kachel **Azure-Aktivitätsprotokolle** zeigt die Anzahl der **AzureActivity**-Datensätze in Ihrem Arbeitsbereich.

![Kachel „Azure-Aktivitätsprotokolle“](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicken Sie auf die Kachel **Azure-Aktivitätsprotokolle**, um die Ansicht **Azure-Aktivitätsprotokolle** zu öffnen. Die Ansicht enthält die Visualisierungskomponenten, die in der folgenden Tabelle aufgeführt sind. Für jede Komponente sind bis zu 10 Einträge aufgeführt, die den Kriterien dieser Komponente für den angegebenen Zeitraum entsprechen. Sie können eine Protokollabfrage ausführen, die alle übereinstimmenden Datensätze zurückgibt. Dazu klicken Sie am unteren Rand der Komponente auf **Alle anzeigen**.

![Dashboard „Azure-Aktivitätsprotokolle“](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Aktivieren der Lösung für neue Abonnements
In Kürze können Sie die Lösung für die Aktivitätsprotokollanalyse Ihrem Abonnement nicht mehr über das Azure-Portal hinzufügen. Sie können sie mithilfe des folgenden Verfahrens mit einer Resource Manager-Vorlage hinzufügen. 

1. Kopieren Sie den folgenden JSON-Code in eine Datei mit dem Namen *ActivityLogTemplate.json*.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Stellen Sie die Vorlage mithilfe der folgenden PowerShell-Befehle bereit:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Nächste Schritte

* [Lesen einer Übersicht über Plattformprotokolle](platform-logs-overview.md)
* [Überprüfen des Ereignisschemas des Aktivitätsprotokolls](activity-log-schema.md)
* [Erstellen einer Diagnoseeinstellung zum Senden von Aktivitätsprotokollen an andere Ziele](diagnostic-settings.md)