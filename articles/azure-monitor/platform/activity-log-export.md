---
title: Exportieren des Azure-Aktivitätsprotokolls
description: Exportieren Sie das Azure-Aktivitätsprotokoll zur Archivierung in den Speicher oder zum Exportieren außerhalb von Azure in Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245597"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportieren des Aktivitätsprotokolls in den Speicher oder in Azure Event Hubs
Das [Azure-Aktivitätsprotokoll](activity-logs-overview.md) bietet Einblick in Ereignisse auf Abonnementebene, die in Ihrem Azure-Abonnement aufgetreten sind. Zusätzlich zum Anzeigen des Aktivitätsprotokolls im Azure-Portal oder Kopieren des Protokolls in einen Log Analytics-Arbeitsbereich, in dem es mit anderen von Azure Monitor gesammelten Daten analysiert werden kann, können Sie ein Protokollprofil zum Archivieren des Aktivitätsprotokolls in einem Azure-Speicherkonto oder zum Streamen an einen Event Hub erstellen.

## <a name="archive-activity-log"></a>Archivieren des Aktivitätsprotokolls
Das Archivieren des Aktivitätsprotokolls in einem Speicherkonto ist hilfreich, wenn Sie Ihre Protokolldaten (bei vollständiger Kontrolle über die Aufbewahrungsrichtlinie) zur Überwachung, statischen Analyse oder als Sicherungskopie länger als 90 Tage aufbewahren möchten. Falls Sie Ihre Ereignisse nur maximal 90 Tage lang aufbewahren möchten, müssen Sie keine Archivierung in einem Speicherkonto einrichten, da Aktivitätsprotokollereignisse in der Azure-Plattform 90 Tage lang aufbewahrt werden.

## <a name="stream-activity-log-to-event-hub"></a>Streamen von Aktivitätsprotokollen an Event Hubs
[Azure Event Hubs](/azure/event-hubs/) ist eine Datenstreamingplattform und ein Ereigniserfassungsdienst, der Millionen von Ereignissen pro Sekunde empfangen und verarbeiten kann. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Nachfolgend sind zwei Verwendungsmöglichkeiten des Streamingfeatures für das Aktivitätsprotokoll angegeben:
* **Streamen an Protokollierungs- und Telemetriesysteme von Drittanbietern:** Azure Event Hubs-Streaming entwickelt sich mehr und mehr zum Mechanismus für die Weiterreichung des Aktivitätsprotokolls an SIEM- (Security Information & Event Management) und Protokollanalyselösungen von Drittanbietern.
* **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform:** Event Hubs ermöglicht dank des hochgradig skalierbaren Veröffentlichen/Abonnieren-Konzepts eine flexible Erfassung des Aktivitätsprotokolls. Dies ist interessant, wenn Sie bereits über eine benutzerdefinierte Telemetrieplattform verfügen oder eine solche Plattform erstellen möchten. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="storage-account"></a>Speicherkonto
Wenn Sie Ihr Aktivitätsprotokoll archivieren, müssen Sie [ein Speicherkonto erstellen](../../storage/common/storage-quickstart-create-account.md), falls Sie noch keines besitzen. Um den Zugriff auf Überwachungsdaten besser steuern zu können, sollten Sie kein bereits vorhandenes Speicherkonto mit anderen, nicht überwachungsbezogenen Daten verwenden. Wenn Sie auch Diagnoseprotokolle und Metriken in einem Speicherkonto archivieren, können Sie dasselbe Speicherkonto verwenden, damit sich alle Überwachungsdaten an einem zentralen Ort befinden.

Das Speicherkonto muss sich nicht unter demselben Abonnement befinden, das Protokolle ausgibt, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden RBAC-Zugriff auf beide Abonnements hat.
> [!NOTE]
>  Sie können derzeit keine Daten in einem Speicherkonto archivieren, das sich hinter einem geschützten virtuellen Netzwerk befindet.

### <a name="event-hubs"></a>Event Hubs
Wenn Sie Ihr Aktivitätsprotokoll an einen Event Hub senden, müssen Sie [einen Event Hub erstellen](../../event-hubs/event-hubs-create.md), falls Sie noch keinen besitzen. Wenn Sie bereits Aktivitätsprotokollereignisse an diesen Event Hubs-Namespace gestreamt haben, wird dieser Event Hub wiederverwendet.

Die SAS-Richtlinie definiert die Berechtigungen für den Streamingmechanismus. Für das Streaming an Event Hubs werden Berechtigungen für das Verwalten, Senden und Lauschen benötigt. Sie können freigegebene Zugriffsrichtlinien für den Event Hubs-Namespace im Azure-Portal auf der Registerkarte „Konfigurieren“ für Ihren Event Hubs-Namespace erstellen oder ändern.

Wenn Sie dem Protokollprofil für das Aktivitätsprotokoll das Streamingfeature hinzufügen möchten, müssen Sie in der Event Hubs-Autorisierungsregel über die ListKey-Berechtigung verfügen. Der Event Hubs-Namespace muss sich nicht in demselben Abonnement wie das Abonnement befinden, das Protokolle ausgibt – sofern der Benutzer, der die Einstellung konfiguriert, über den entsprechenden RBAC-Zugriff auf beide Abonnements verfügt und beide Abonnements sich in demselben AAD-Mandanten befinden.

Streamen Sie das Aktivitätsprotokoll an einen Event Hub, indem Sie [ein Protokollprofil erstellen](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Erstellen eines Protokollprofils
Mithilfe eines **Protokollprofils** definieren Sie, wie Ihr Azure-Aktivitätsprotokoll exportiert wird. Jedes Azure-Abonnement kann nur über ein Protokollprofil verfügen. Diese Einstellungen können über die Option **Exportieren** auf dem Blatt „Aktivitätsprotokoll“ im Portal konfiguriert werden. Sie können auch [mithilfe der Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx), über PowerShell-Cmdlets oder über die Befehlszeilenschnittstelle programmgesteuert konfiguriert werden.

Das Protokollprofil definiert Folgendes:

**Wohin das Aktivitätsprotokoll gesendet werden soll.** Derzeit sind die verfügbaren Optionen „Speicherkonto“ oder „Event Hubs“.

**Welche Ereigniskategorien gesendet werden sollen.** *Kategorie* hat in Protokollprofilen und Aktivitätsprotokollereignissen eine unterschiedliche Bedeutung. Im Protokollprofil ist *Kategorie* der Vorgangstyp („Write“, „Delete“, „Action“). In einem Aktivitätsprotokollereignis stellt die Eigenschaft *Kategorie* die Quelle oder den Typ des Ereignisses dar (z.B. „Administration“, „ServiceHealth“ und „Alert“).

**Welche Regionen (Standorte) exportiert werden sollen.** Sie sollten alle Standorte einbeziehen, da viele Ereignisse im Aktivitätsprotokoll globale Ereignisse sind.

**Wie lange das Aktivitätsprotokoll in einem Speicherkonto beibehalten werden soll.** Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.

Wenn Aufbewahrungsrichtlinien festgelegt werden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist, werden die Aufbewahrungsrichtlinien ignoriert. Aufbewahrungsrichtlinien werden pro Tag angewendet, sodass Protokolle am Ende eines Tages (UTC) ab dem Tag, der nun außerhalb der Aufbewahrungsrichtlinie liegt, gelöscht werden. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht. Der Löschvorgang beginnt um Mitternacht (UTC), jedoch kann es bis zu 24 Stunden dauern, bis die Protokolle aus Ihrem Speicherkonto gelöscht werden.



> [!WARNING]
> Das Format der Protokolldaten im Speicherkonto wurde am 1. November 2018 in JSON Lines geändert. [Dieser Artikel enthält eine Beschreibung der Auswirkungen und der Aktualisierung Ihrer Tools zur Verarbeitung des neuen Formats.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Erstellen eines Protokollprofils mit dem Azure-Portal

Erstellen oder bearbeiten Sie ein Protokollprofils im Azure-Portal mit der Option **In Event Hub exportieren**.

1. Wählen Sie im Azure-Portal im Menü **Monitor** die Option **In Event Hubs exportieren** aus.

    ![Schaltfläche „Exportieren“ im Portal](media/activity-log-export/portal-export.png)

3. Geben Sie auf dem daraufhin angezeigten Blatt die folgenden Informationen ein:
   * Regionen mit den zu exportierenden Ereignissen. Sie sollten alle Regionen auswählen, um sicherzustellen, dass Ihnen keine wichtigen Ereignisse entgehen, da das Aktivitätsprotokoll ein globales (kein regionales) Protokoll ist und daher den meisten Ereignissen keine Region zugeordnet ist. 
   * Zum Schreiben ins Speicherkonto:
       * Das Speicherkonto, in dem Sie Ereignisse speichern möchten.
       * Die Anzahl der Tage, die diese Ereignisse im Speicher aufbewahrt werden sollen. Bei einer Einstellung von 0 Tagen werden die Protokolle unbegrenzt aufbewahrt.
   * Zum Schreiben in den Event Hub:
       * Den Service Bus-Namespace, in dem Sie einen Event Hub für das Streamen dieser Ereignisse erstellen möchten.

     ![Blatt zum Exportieren des Aktivitätsprotokolls](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klicken Sie auf **Speichern** , um die Einstellungen zu speichern. Die Einstellungen werden sofort auf Ihr Abonnement angewendet.


### <a name="configure-log-profile-using-powershell"></a>Konfigurieren eines Protokollprofils mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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
    | Location |Ja |Kommagetrennte Liste mit den Regionen, für die Sie Aktivitätsprotokollereignisse erfassen möchten. |
    | RetentionInDays |Ja |Anzahl von Tagen für die Aufbewahrung von Ereignissen im Speicherkonto (1 bis 2147483647). Bei einem Wert von 0 werden die Protokolle dauerhaft gespeichert. |
    | Category (Kategorie) |Nein |Kommagetrennte Liste mit den Ereigniskategorien, die erfasst werden sollen. Mögliche Werte sind _Write_, _Delete_ und _Action_. |

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

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
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
    | Locations |Ja |Durch Leerzeichen getrennte Liste mit den Regionen, für die Sie Aktivitätsprotokollereignisse erfassen möchten. Mit `az account list-locations --query [].name` können Sie eine Liste aller Regionen für Ihr Abonnement anzeigen. |
    | days |Ja |Anzahl von Tagen für die Aufbewahrung von Ereignissen (1 bis 365). Bei einem Wert von 0 werden die Protokolle dauerhaft (d.h. für immer) gespeichert.  Wenn der Wert 0 ist, muss der aktivierte Parameter auf „true“ festgelegt werden. |
    |enabled | Ja |„True“ oder „False“.  Wird zum Aktivieren bzw. Deaktivieren der Aufbewahrungsrichtlinie verwendet.  Ist „True“ festgelegt, muss für den Parameter „days“ ein Wert größer 0 angegeben werden.
    | categories |Ja |Durch Leerzeichen getrennte Liste mit den Ereigniskategorien, die erfasst werden sollen. Mögliche Werte sind „Write“, „Delete“ und „Action“. |



## <a name="activity-log-schema"></a>Aktivitätsprotokollschema
Die Aktivitätsprotokolldaten werden im folgenden Format in JSON geschrieben, unabhängig davon, ob sie an den Azure-Speicher oder Event Hub gesendet werden.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
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
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
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
Die Elemente in diesem JSON-Code sind in der folgenden Tabelle beschrieben.

| Elementname | BESCHREIBUNG |
| --- | --- |
| time |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| resourceId |Ressourcen-ID der betroffenen Ressource. |
| operationName |Name des Vorgangs. |
| category |Kategorie der Aktion, z.B. Schreiben, Lesen, Aktion. |
| resultType |Der Typ des Ergebnisses, z.B. Erfolg, Fehler, Start |
| resultSignature |Abhängig vom Ressourcentyp. |
| durationMs |Dauer des Vorgangs in Millisekunden. |
| callerIpAddress |IP-Adresse des Benutzers, der den Vorgang, UPN-Anspruch oder SPN-Anspruch ausgeführt hat (sofern verfügbar). |
| correlationId |Normalerweise eine GUID im Zeichenfolgenformat. Ereignisse, die über die gleiche correlationId verfügen, gehören zu derselben übergeordneten Aktion. |
| identity |JSON-Blob zur Beschreibung der Autorisierung und Ansprüche. |
| authorization |Blob mit RBAC-Eigenschaften des Ereignisses. Enthält normalerweise die Eigenschaften „action“, „role“ und „scope“. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: _Critical_, _Error_, _Warning_, _Informational_ oder _Verbose_ |
| location |Region des Speicherorts (oder „global“). |
| properties |Satz mit `<Key, Value>` -Paaren (Wörterbuch), die die Details des Ereignisses beschreiben. |

> [!NOTE]
> Die Eigenschaften und deren Verwendung können je nach Ressource variieren.



## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Aktivitätsprotokoll](../../azure-resource-manager/resource-group-audit.md)
* [Erfassen des Aktivitätsprotokolls in Azure Monitor-Protokollen](activity-log-collect.md)
