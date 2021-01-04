---
title: Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele
description: Senden Sie Metriken und Protokolle der Azure Monitor-Plattform mithilfe von Diagnoseeinstellungen an Azure Monitor-Protokolle, Azure Storage oder Azure Event Hubs.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 950fcdc5cd6a5bbf3fa61ebd5e23be89691c4370
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95535791"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele
[Plattformprotokolle](platform-logs-overview.md) in Azure, z. B. das Azure-Aktivitätsprotokoll und Ressourcenprotokolle, liefern ausführliche Diagnose- und Überwachungsinformationen für Azure-Ressourcen und die Azure-Plattform, von der sie abhängen. [Plattformmetriken](data-platform-metrics.md) werden standardmäßig gesammelt und in der Regel in der Azure Monitor-Metrikdatenbank gespeichert. Dieser Artikel enthält Details zum Erstellen und Konfigurieren von Diagnoseeinstellungen, um Plattformmetriken und -protokolle an verschiedene Ziele zu senden.

> [!IMPORTANT]
> Bevor Sie eine Diagnoseeinstellung für das Aktivitätsprotokoll erstellen, sollten Sie zunächst vorhandene Legacykonfigurationen deaktivieren. Weitere Informationen hierzu finden Sie unter [Legacy-Erfassungsmethoden](activity-log.md#legacy-collection-methods).

Jede Azure-Ressource erfordert eine eigene Diagnoseeinstellung, die folgende Kriterien definiert:

- Kategorien der Protokolle und Metrikdaten, die an die in der Einstellung definierten Ziele gesendet werden. Die verfügbaren Kategorien sind je nach Ressourcentyp verschieden.
- Ein oder mehrere Ziele zum Senden der Protokolle. Zu den aktuellen Zielen gehören der Log Analytics-Arbeitsbereich, Event Hubs und Azure Storage.

Mit einer einzelnen Diagnoseeinstellung kann maximal eines der Ziele definiert werden. Wenn Sie Daten an mehrere Ziele eines bestimmten Typs senden möchten (z.B. zwei verschiedene Log Analytics-Arbeitsbereiche), dann erstellen Sie mehrere Einstellungen. Jede Ressource kann bis zu fünf Diagnoseeinstellungen aufweisen.

Das folgende Video führt Sie durch das Routing von Plattformprotokollen mit Diagnoseeinstellungen.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Plattformmetriken](metrics-supported.md) werden automatisch an die [Azure Monitor-Metriken](data-platform-metrics.md) gesendet. Mit Diagnoseeinstellungen können Metriken für bestimmte Azure-Dienste an Azure Monitor-Protokolle gesendet werden, damit diese mit anderen Überwachungsdaten anhand von [Protokollabfragen](../log-query/log-query-overview.md) mit bestimmten Einschränkungen analysiert werden. 
>  
>  
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert. *Beispiel*: Die Metrik „IOReadBytes“ in einer Blockchain kann für jeden Knoten einzeln untersucht und dargestellt werden. Wenn sie jedoch über Diagnoseeinstellungen exportiert wird, stellt die exportierte Metrik alle gelesenen Bytes für alle Knoten dar. Darüber hinaus können aufgrund von internen Einschränkungen nicht alle Metriken in Azure Monitor-Protokolle oder in Log Analytics exportiert werden. Weitere Informationen finden Sie in der [Liste mit den exportierbaren Metriken](metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Um diese Einschränkungen für bestimmte Metriken zu umgehen, empfiehlt es sich, diese mithilfe der [REST-API für Metriken](/rest/api/monitor/metrics/list) manuell zu extrahieren und mithilfe der [Azure Monitor-Datensammler-API](data-collector-api.md) in Azure Monitor-Protokolle zu importieren.  


## <a name="destinations"></a>Destinations
Plattformprotokolle und -metriken können an die Ziele in der folgenden Tabelle gesendet werden. 

| Destination | BESCHREIBUNG |
|:---|:---|
| [Log Analytics-Arbeitsbereich](design-logs-deployment.md) | Das Senden von Protokollen und Metriken in einen Log Analytics-Arbeitsbereich gibt Ihnen die Möglichkeit, diese Daten zusammen mit anderen Überwachungsdaten, die mithilfe leistungsstarker Protokollabfragen von Azure Monitor erfasst werden, zu analysieren und andere Azure Monitor-Funktionen wie Warnungen und Visualisierungen zu nutzen. |
| [Event Hubs](../../event-hubs/index.yml) | Das Senden von Protokollen und Metriken an Event Hubs ermöglicht Ihnen das Streamen von Daten an externe Systeme, z. B. SIEMs und andere Protokollanalyselösungen von Drittanbietern.  |
| [Azure-Speicherkonto](../../storage/blobs/index.yml) | Das Archivieren von Protokollen und Metriken in einem Azure Storage-Konto ist für die Überwachung, statische Analyse oder Sicherung nützlich. Im Vergleich zu Azure Monitor-Protokollen und einem Log Analytics Arbeitsbereich ist Azure Storage kostengünstiger, und die Protokolle können unbegrenzt aufbewahrt werden.  |


### <a name="destination-requirements"></a>Anforderungen für Ziele

Alle Ziele für Diagnoseeinstellungen müssen vor den Diagnoseeinstellungen erstellt werden. Das Ziel muss sich nicht in demselben Abonnement befinden wie die Ressource, die Protokolle sendet, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden Azure RBAC-Zugriff auf beide Abonnements besitzt. In der folgenden Tabelle sind besondere Anforderungen für die einzelnen Ziele einschließlich regionaler Einschränkungen aufgeführt.

| Destination | Requirements (Anforderungen) |
|:---|:---|
| Log Analytics-Arbeitsbereich | Der Arbeitsbereich muss sich nicht in derselben Region wie die überwachte Ressource befinden.|
| Event Hubs | Die SAS-Richtlinie für den Namespace definiert die Berechtigungen für den Streamingmechanismus. Für das Streaming an Event Hubs werden Berechtigungen für das Verwalten, Senden und Lauschen benötigt. Wenn Sie der Diagnoseeinstellung das Streamingfeature hinzufügen möchten, müssen Sie in der Event Hubs-Autorisierungsregel über die ListKey-Berechtigung verfügen.<br><br>Wenn die überwachte Ressource regional ist, muss sich der Event Hub-Namespace in derselben Region wie die Ressource befinden. |
| Azure-Speicherkonto | Um den Zugriff auf die Daten besser steuern zu können, sollten Sie kein bereits vorhandenes Speicherkonto mit anderen, nicht überwachungsbezogenen Daten verwenden. Wenn Sie das Aktivitätsprotokoll und Ressourcenprotokollen zusammen archivieren, können Sie aber dasselbe Speicherkonto verwenden, damit sich alle Überwachungsdaten an einem zentralen Ort befinden.<br><br>Legen Sie die unveränderliche Richtlinie für das Speicherkonto wie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](../../storage/blobs/storage-blob-immutability-policies-manage.md) beschrieben fest, um die Daten an einen unveränderlichen Speicher zu senden. Sie müssen alle Schritte in diesem Artikel ausführen, einschließlich der Aktivierung von Schreibvorgängen in geschützten Anfügeblobs.<br><br>Wenn die überwachte Ressource regional ist, muss sich das Speicherkonto in derselben Region wie die Ressource befinden. |

> [!NOTE]
> Azure Data Lake Storage Gen2-Konten werden derzeit nicht als Ziel für Diagnoseeinstellungen unterstützt, auch wenn sie möglicherweise als gültige Option im Azure-Portal aufgeführt werden.

> [!NOTE]
> Azure Monitor (Diagnoseeinstellungen) kann nicht auf Event Hubs-Ressourcen zugreifen, wenn virtuelle Netzwerke aktiviert sind. Wenn Sie die Einstellung „Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben“ in Event Hubs aktivieren, wird Azure Monitor (Diagnoseeinstellungen) der Zugriff auf Ihre Event Hubs-Ressourcen gewährt. 


## <a name="create-in-azure-portal"></a>Im Azure-Portal erstellen

Sie können Diagnoseeinstellungen im-Azure-Portal entweder über das Azure Monitor-Menü oder über das Menü für die Ressource konfigurieren.

1. Wo Sie Diagnoseeinstellungen im Azure-Portal konfigurieren, hängt von der Ressource ab.

   - Für eine einzelne Ressource klicken Sie im Ressourcenmenü unter **Überwachen** auf **Diagnoseeinstellungen**.

        ![Screenshot des Bereichs „Überwachung“ eines Ressourcenmenüs im Azure-Portal mit hervorgehobener Option „Diagnoseeinstellungen“](media/diagnostic-settings/menu-resource.png)

   - Für eine oder mehrere Ressourcen klicken Sie im Azure Monitor-Menü unter **Einstellungen** auf **Diagnoseeinstellungen**, und klicken Sie dann auf die Ressource.

        ![Screenshot des Abschnitts „Einstellungen“ im Azure Monitor-Menü mit hervorgehobener Option „Diagnoseeinstellungen“](media/diagnostic-settings/menu-monitor.png)

   - Für das Aktivitätsprotokoll klicken Sie im **Azure Monitor**-Menü auf **Aktivitätsprotokoll** und dann auf **Diagnoseeinstellungen**. Stellen Sie sicher, dass Sie alle Legacykonfigurationen für das Aktivitätsprotokoll deaktivieren. Ausführliche Informationen dazu finden Sie unter [Deaktivieren vorhandener Einstellungen](./activity-log.md#legacy-collection-methods).

        ![Screenshot des Azure Monitor-Menüs mit ausgewähltem Aktivitätsprotokoll und hervorgehobener Option „Diagnoseeinstellungen“ in der Menüleiste „Aktivitätsprotokoll“ in Azure Monitor](media/diagnostic-settings/menu-activity-log.png)

2. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/diagnostic-settings/add-setting.png)

   Wenn bereits Einstellungen für die Ressource vorhanden sind, wird eine Liste der konfigurierten Einstellungen anagezeigt. Klicken Sie entweder auf **Diagnoseeinstellung hinzufügen**, um eine neue Einstellung hinzuzufügen, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten. Jede Einstellung kann höchstens einen der Zieltypen aufweisen.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-settings/edit-setting.png)

3. Wenn noch kein Name für die Einstellung vorhanden ist, geben Sie ihr einen Namen.

    ![Hinzufügen einer Diagnoseeinstellung](media/diagnostic-settings/setting-new-blank.png)

4. **Kategoriedetails (weiterzuleitende Elemente):** Aktivieren Sie das Kontrollkästchen für jede Datenkategorie, die an die später angegebenen Ziele gesendet werden soll. Die Liste der Kategorien unterscheidet sich je nach Azure-Dienst.

     - **AllMetrics** leitet die Plattformmetriken einer Ressource an den Azure-Protokollspeicher weiter, jedoch in Protokollform. Diese Metriken werden in der Regel nur an die Zeitreihendatenbank für Azure Monitor-Metriken gesendet. Wenn Sie sie an den Azure Monitor-Protokollspeicher (der über Log Analytics durchsucht werden kann) senden, können Sie sie in Abfragen integrieren, die andere Protokolle durchsuchen. Diese Option ist möglicherweise nicht für alle Ressourcentypen verfügbar. Bei Unterstützung dieser Option wird für [unterstützte Azure Monitor-Metriken](metrics-supported.md) aufgelistet, welche Metriken für welche Ressourcentypen gesammelt werden.

       > [!NOTE]
       > Informationen zu Einschränkungen bei der Weiterleitung von Metriken an Azure Monitor-Protokolle finden Sie weiter oben in diesem Artikel.  


     - Für **Protokolle** werden die verschiedenen verfügbaren Kategorien abhängig vom Ressourcentyp aufgelistet. Überprüfen Sie alle Kategorien, die Sie an ein Ziel weiterleiten möchten.

5. **Zieldetails:** Aktivieren Sie das Kontrollkästchen für jedes Ziel. Beim Aktivieren der einzelnen Kontrollkästchen werden Optionen angezeigt, mit denen Sie zusätzliche Informationen hinzufügen können.

      ![Senden an Log Analytics oder Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics:** Geben Sie das Abonnement und den Arbeitsbereich ein.  Wenn Sie noch nicht über einen Arbeitsbereich verfügen, [erstellen Sie vor dem Fortfahren einen](../learn/quick-create-workspace.md).

    1. **Event Hubs:** Legen Sie die folgenden Kriterien fest:
       - Das Abonnement, zu dem der Event Hub gehört
       - Der Event Hub-Namespace: Wenn Sie noch keinen besitzen, müssen Sie [einen erstellen](../../event-hubs/event-hubs-create.md).
       - Der Name eines Event Hub (optional), an den alle Daten gesendet werden sollen. Wenn Sie keinen Namen angeben, wird eine Event Hub für jede Protokollkategorie erstellt. Wenn Sie mehrere Kategorien senden, können Sie einen Namen angeben, um die Anzahl der erstellten Event Hubs zu beschränken. Ausführliche Informationen dazu finden Sie unter [Kontingente und Grenzwerte in Azure Event Hubs](../../event-hubs/event-hubs-quotas.md).
       - Eine Event Hub-Richtlinie (optional): definiert die Berechtigungen für den Streamingmechanismus. Weitere Informationen finden Sie unter [Event Hubs-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Speicher:** Wählen Sie das Abonnement, das Speicherkonto und die Aufbewahrungsrichtlinie aus.

        ![Senden an den Speicher](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Legen Sie die Aufbewahrungsrichtlinie auf 0 fest, und löschen Sie die Daten mithilfe eines geplanten Auftrags manuell aus dem Speicher, um mögliche Verwirrung in Zukunft zu vermeiden.
        >
        > Erstens: Wenn Sie Speicher für die Archivierung verwenden, sollten Sie die Daten in der Regel für mehr als 365 Tage aufbewahren. Zweitens: Wenn Sie eine Aufbewahrungsrichtlinie über 0 auswählen, wird das Ablaufdatum beim Zeitpunkt des Speicherns an die Protokolle angefügt. Nach dem Speichern können Sie das Datum für diese Protokolle nicht mehr ändern.
        >
        > Wenn Sie z. B. die Aufbewahrungsrichtlinie für *WorkflowRuntime* auf 180 Tage und dann 24 Stunden später auf 365 Tage festlegen, werden die in den ersten 24 Stunden gespeicherten Protokolle automatisch nach 180 Tagen gelöscht, während alle nachfolgenden Protokolle dieses Typs nach 365 Tagen automatisch gelöscht werden. Das Ändern der Aufbewahrungsrichtlinie zu einem späteren Zeitpunkt sorgt nicht dafür, dass die Protokolle aus den ersten 24 Stunden 365 Tage lang aufbewahrt werden.

6. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Ressource angezeigt, und Protokolle werden an die angegebenen Ziele gestreamt, sobald neue Ereignisdaten generiert werden. Zwischen der Ausgabe eines Ereignisses und dessen [Anzeige in einem Log Analytics-Arbeitsbereich](data-ingestion-time.md) können bis zu 15 Minuten vergehen.

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

Verwenden Sie das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting), um eine Diagnoseeinstellung mit [Azure PowerShell](../samples/powershell-samples.md) zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Cmdlet.

> [!IMPORTANT]
> Sie können diese Methode nicht für das Azure-Aktivitätsprotokoll verwenden. Gehen Sie stattdessen nach der Methode [Erstellen von Diagnoseeinstellungen in Azure Monitor mithilfe einer Resource Manager-Vorlage](../samples/resource-manager-diagnostic-settings.md) vor, um eine Resource Manager-Vorlage zu erstellen und mit PowerShell bereitzustellen.

Es folgt ein Beispiel für ein PowerShell-Cmdlet zum Erstellen einer Diagnoseeinstellung, die alle drei Ziele verwendet.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Erstellen mithilfe der Azure CLI

Verwenden Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create), um eine Diagnoseeinstellung mit der [Azure CLI](/cli/azure/monitor?view=azure-cli-latest) zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Befehl.

> [!IMPORTANT]
> Sie können diese Methode nicht für das Azure-Aktivitätsprotokoll verwenden. Gehen Sie stattdessen nach der Methode [Erstellen von Diagnoseeinstellungen in Azure Monitor mithilfe einer Resource Manager-Vorlage](../samples/resource-manager-diagnostic-settings.md) vor, um eine Resource Manager-Vorlage zu erstellen und mit der CLI bereitzustellen.

Es folgt ein Beispiel für einen CLI-Befehl zum Erstellen einer Diagnoseeinstellung, die alle drei Ziele verwendet.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Erstellen mithilfe einer Resource Manager-Vorlage
Informationen zum Erstellen oder Aktualisieren von Diagnoseeinstellungen mithilfe einer Resource Manager-Vorlage finden Sie unter [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen in Azure Monitor](../samples/resource-manager-diagnostic-settings.md).

## <a name="create-using-rest-api"></a>Erstellen mithilfe der REST-API
Informationen zum Erstellen oder Aktualisieren von Diagnoseeinstellungen mithilfe der [Azure Monitor-REST-API](/rest/api/monitor/) finden Sie unter [Diagnoseeinstellungen](/rest/api/monitor/diagnosticsettings).

## <a name="create-using-azure-policy"></a>Erstellen mithilfe von Azure Policy
Da für jede Azure-Ressource eine Diagnoseeinstellung erstellt werden muss, können Sie mit Azure Policy beim Erstellen der einzelnen Ressourcen automatisch eine Diagnoseeinstellung erstellen. Weitere Informationen finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](../deploy-scale.md).


## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Protokollen der Azure-Plattform](platform-logs-overview.md)
