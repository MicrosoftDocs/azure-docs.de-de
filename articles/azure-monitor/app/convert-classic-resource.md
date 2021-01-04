---
title: Migrieren einer klassischen Application Insights-Ressource in Azure Monitor zu einer arbeitsbereichsbasierten Ressource | Microsoft-Dokumentation
description: Erfahren Sie mehr über die erforderlichen Schritte zum Aktualisieren einer klassischen Application Insights-Ressource in Azure Monitor auf das neue arbeitsbereichsbasierte Modell.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 709cff1326bb6393a14c594ea434a6c16fb80860
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536522"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrieren zu arbeitsbereichsbasierten Application Insights-Ressourcen

Dieser Leitfaden führt Sie durch die Migration einer klassischen Application Insights-Ressource zu einer arbeitsbereichsbasierten Ressource. Arbeitsbereichsbasierte Ressourcen unterstützen die vollständige Integration zwischen Application Insights und Log Analytics. Arbeitsbereichsbasierte Ressourcen senden Application Insights-Telemetriedaten an einen gemeinsamen Log Analytics-Arbeitsbereich, über den Sie auf die [aktuellen Features von Azure Monitor](#new-capabilities) zugreifen und gleichzeitig Anwendungs-, Infrastruktur- und Plattformprotokolle an einem konsolidierten Speicherort sammeln können.

Arbeitsbereichsbasierte Ressourcen ermöglichen eine gemeinsame rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) in allen Ihren Ressourcen, und die Notwendigkeit anwendungs- bzw. arbeitsbereichsübergreifender Abfragen entfällt.

**Arbeitsbereichsbasierte Ressourcen sind zurzeit in allen kommerziellen Regionen und für Azure US Government verfügbar.**

## <a name="new-capabilities"></a>Neue Funktionen

Mithilfe des arbeitsbereichsbasierten Application Insights können Sie die neuesten Funktionen von Azure Monitor und Log Analytics nutzen, einschließlich:

* [CMK (Customer-Managed Keys, kundenseitig verwaltete Schlüssel)](../platform/customer-managed-keys.md) bietet Verschlüsselung im Ruhezustand für Ihre Daten mit Verschlüsselungsschlüsseln, auf die nur Sie Zugriff haben.
* Mit [Azure Private Link](../platform/private-link-security.md) können Sie Azure-PaaS-Dienste über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen.
* [Bring your own Storage (BYOS) für Profiler und Momentaufnahmedebugger](./profiler-bring-your-own-storage.md) verleiht Ihnen vollständige Kontrolle über die Richtlinie zur Verschlüsselung ruhender Daten, die Richtlinie zur Lebensdauerverwaltung und den Netzwerkzugriff auf alle Daten, die Application Insights Profiler und dem Momentaufnahmedebugger zugeordnet sind. 
* [Mit Kapazitätsreservierungsstufen](../platform/manage-cost-storage.md#pricing-model) können Sie im Vergleich zur nutzungsbasierten Zahlung bis zu 25 % sparen. 
* Schnellere Datenerfassung über Log Analytics-Streamingerfassung.

## <a name="migration-process"></a>Migrationsprozess

Wenn Sie zu einer arbeitsbereichsbasierten Ressource migrieren, werden keine Daten aus dem Speicher der klassischen Ressource in den neuen arbeitsbereichsbasierten Speicher übertragen. Wenn Sie sich für die Migration entscheiden, ändern Sie stattdessen den Speicherort, an den neue Daten geschrieben werden, in einen Log Analytics-Arbeitsbereich und behalten gleichzeitig den Zugriff auf die Daten in der klassischen Ressource. 

Die Daten in der klassischen Ressource bleiben erhalten, und es gelten die Aufbewahrungseinstellungen in Ihrer klassischen Application Insight-Ressource. Alle neuen Daten, die nach der Migration erfasst werden, unterliegen den [Aufbewahrungseinstellungen](../platform/manage-cost-storage.md#change-the-data-retention-period) für den zugeordneten Log Analytics-Arbeitsbereich, der auch [unterschiedliche Aufbewahrungseinstellungen nach Datentyp](../platform/manage-cost-storage.md#retention-by-data-type) unterstützt.
Die Migration ist **dauerhaft und kann nicht rückgängig gemacht werden**. Nach der Migration einer Ressource zum arbeitsbereichsbasierten Application Insights ist sie immer eine arbeitsbereichsbasierte Ressource. Nach der Migration können Sie jedoch den Zielarbeitsbereich beliebig oft ändern. 

> [!NOTE]
> Datenerfassung und -aufbewahrung für arbeitsbereichsbasierte Application Insights-Ressourcen werden [über den Log Analytics-Arbeitsbereich abgerechnet](../platform/manage-cost-storage.md), in dem sich die Daten befinden. Wenn Sie vor der Migration eine Datenaufbewahrung von mehr als 90 Tagen für Daten ausgewählt haben, die in der klassischen Application Insights-Ressource erfasst werden, wird die Datenaufbewahrung weiterhin über diese Application Insights-Ressource abgerechnet. [Erfahren Sie mehr]( ./pricing.md#workspace-based-application-insights) über die Abrechnung für arbeitsbereichsbasierte Application Insights-Ressourcen.

Wenn Sie eine vorhandene Ressource nicht migrieren müssen und stattdessen eine neue arbeitsbereichsbasierte Application Insights-Ressource erstellen möchten, finden Sie weitere Informationen in der [Anleitung zum Erstellen arbeitsbereichsbasierter Ressourcen](create-workspace-resource.md).

## <a name="pre-requisites"></a>Voraussetzungen 

- Ein Log Analytics Arbeitsbereich, bei dem der Zugriffssteuerungsmodus auf die Einstellung **`use resource or workspace permissions`** festgelegt ist. 

    - Arbeitsbereichsbasierte Application Insights-Ressourcen sind nicht kompatibel mit Arbeitsbereichen, die auf die dedizierte Einstellung **`workspace based permissions`** festgelegt sind. Weitere Informationen zur Zugriffssteuerung für Log Analytics-Arbeitsbereiche finden Sie in der [Anleitung zum Konfigurieren des Zugriffssteuerungsmodus für Log Analytics](../platform/manage-access.md#configure-access-control-mode).

    - Wenn Sie noch nicht über einen Log Analytics-Arbeitsbereich verfügen, [sehen Sie sich die Dokumentation zur Erstellung von Log Analytics-Arbeitsbereichen an](../learn/quick-create-workspace.md).
    
- Der fortlaufende Export wird für arbeitsbereichsbasierte Ressourcen nicht unterstützt und muss deaktiviert werden.
Nachdem die Migration durchgeführt wurde, können Sie mit [Diagnoseeinstellungen](../platform/diagnostic-settings.md) die Datenarchivierung in einem Speicherkonto oder das Streaming an Azure Event Hub konfigurieren.  

- Überprüfen Sie die aktuellen Aufbewahrungseinstellungen unter **Allgemein** > **Nutzung und geschätzte Kosten** > **Datenaufbewahrung** für den Log Analytics-Arbeitsbereich. Diese Einstellung wirkt sich darauf aus, wie lange neue erfasste Daten gespeichert werden, nachdem Sie Ihre Application Insights-Ressource migriert haben. Wenn Sie Application Insights-Daten zurzeit länger als die standardmäßigen 90 Tage speichern und diesen längeren Aufbewahrungszeitraum beibehalten möchten, müssen Sie möglicherweise die Aufbewahrungseinstellungen für den Arbeitsbereich anpassen.

## <a name="migrate-your-resource"></a>Migrieren der Ressource

In diesem Abschnitt wird die Migration einer klassischen Application Insights-Ressource zum neuen arbeitsbereichsbasierten Ressourcentyp beschrieben.

1. Wählen Sie in der Application Insights-Ressource in der linken Menüleiste unter **Konfigurieren** die Option **Eigenschaften** aus.

    ![Mit rotem Rahmen markierte Option „Eigenschaften“](./media/convert-classic-resource/properties.png)

2. Klicken Sie auf **`Migrate to Workspace-based`** (Zur Kasse).
    
     ![Schaltfläche „Ressource migrieren“](./media/convert-classic-resource/migrate.png)

3. Wählen Sie den Log Analytics-Arbeitsbereich aus, in dem alle in Zukunft erfassten Application Insights-Telemetriedaten gespeichert werden sollen.

     ![Benutzeroberfläche des Migrations-Assistenten mit Option zum Auswählen des Zielarbeitsbereichs](./media/convert-classic-resource/migration.png)
    

Nachdem Ihre Ressource migriert wurde, sehen Sie die Informationen zum entsprechenden Arbeitsbereich im Bereich **Übersicht**:

![Name des Arbeitsbereichs](./media/create-workspace-resource/workspace-name.png)

Durch Klicken auf den blauen Linktext gelangen Sie zum zugeordneten Log Analytics-Arbeitsbereich, wo Sie die neue einheitliche Umgebung für Arbeitsbereichsabfragen nutzen können.

## <a name="understanding-log-queries"></a>Grundlegendes zu Protokollabfragen

Wir stellen auf der Application Insights-Benutzeroberfläche weiterhin vollständige Abwärtskompatibilität für Ihre klassischen Ressourcenabfragen, Arbeitsmappen und protokollbasierten Warnungen von Application Insights bereit. 

Um Abfragen für die [neue arbeitsbereichsbasierte Tabellenstruktur und das neue Tabellenschema](apm-tables.md) zu schreiben, müssen Sie zuerst zu Ihrem Log Analytics-Arbeitsbereich navigieren. 

Bei Abfragen direkt in der Log Analytics-Benutzeroberfläche in Ihrem Arbeitsbereich sehen Sie nur die Daten, die nach der Migration erfasst werden. Wenn Sie sowohl die Daten aus dem klassischen Application Insights aus auch die nach der Migration erfassten neuen Daten in einer einheitlichen Abfrage anzeigen möchten, verwenden Sie die Abfrageansicht „Protokolle (Analytics)“ in der migrierten Application Insights-Ressource.

## <a name="programmatic-resource-migration"></a>Programmgesteuerte Migration von Ressourcen

### <a name="azure-cli"></a>Azure CLI

Für den Zugriff auf die Azure CLI-Befehle (Vorschauversion) von Application Insights müssen Sie zuerst Folgendes ausführen:

```azurecli
 az extension add -n application-insights
```

Wenn Sie den Befehl `az extension add` nicht ausführen, wird eine Fehlermeldung mit dem folgenden Hinweis angezeigt: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Jetzt können Sie Folgendes ausführen, um Ihre Application Insights-Ressource zu erstellen:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Beispiel

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Vollständige Informationen zu diesem Befehl finden Sie in der [Azure CLI-Dokumentation](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

Der PowerShell-Befehl `Update-AzApplicationInsights` unterstützt derzeit nicht die Migration einer klassischen Application Insights-Ressource zu einer arbeitsbereichsbasierten Ressource. Um eine arbeitsbereichsbasierte Ressource mit PowerShell zu erstellen, können Sie die folgenden Azure Resource Manager-Vorlagen verwenden und die Ressource über PowerShell bereitstellen.

### <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

#### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameterdatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Ändern des zugeordneten Arbeitsbereichs

Nach dem Erstellen einer arbeitsbereichsbasierten Application Insights-Ressource können Sie den zugeordneten Log Analytics-Arbeitsbereich ändern.

Wählen Sie im Bereich der Application Insights-Ressource **Eigenschaften** > **Arbeitsbereich ändern** > **Log Analytics-Arbeitsbereiche** aus.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="access-mode"></a>Zugriffsmodus

**Fehlermeldung:** *Der ausgewählte Arbeitsbereich ist mit dem arbeitsbereichsbasierten Zugriffsmodus konfiguriert. Dies kann zu Beeinträchtigungen bei einigen APM-Funktionen führen. Wählen Sie einen anderen Arbeitsbereich aus, oder gewähren Sie den ressourcenbasierten Zugriff in den Arbeitsbereichseinstellungen. Sie können diesen Fehler mithilfe der CLI überschreiben.* 

Damit die arbeitsbereichsbasierte Application Insights-Ressource ordnungsgemäß ausgeführt werden kann, müssen Sie den Zugriffssteuerungsmodus Ihres Log Analytics-Zielarbeitsbereichs auf die Einstellung **Ressourcen- oder Arbeitsbereichsberechtigungen verwenden** festlegen. Diese Einstellung befindet sich in der Benutzeroberfläche des Log Analytics-Arbeitsbereichs unter **Eigenschaften** > **Modus für Zugriffssteuerung**. Ausführliche Anweisungen finden Sie in der [Anleitung zum Konfigurieren des Log Analytics-Zugriffssteuerungsmodus](../platform/manage-access.md#configure-access-control-mode). Wenn der Zugriffssteuerungsmodus auf die exklusive Einstellung **Arbeitsbereichsberechtigungen erforderlich** festgelegt ist, bleibt die Migration über die Portal-Migrationsumgebung blockiert.

Wenn Sie aus Sicherheitsgründen den Zugriffssteuerungsmodus für den aktuellen Zielarbeitsbereich nicht ändern können, sollten Sie einen neuen Log Analytics-Arbeitsbereich für die Migration erstellen. 

### <a name="continuous-export"></a>Fortlaufendem Export

**Fehlermeldung:** *Der fortlaufende Export muss deaktiviert werden, bevor Sie fortfahren. Verwenden Sie nach der Migration die Diagnoseeinstellungen für den Export.* 

Die Legacyfunktion für den fortlaufenden Export wird für arbeitsbereichsbasierte Ressourcen nicht unterstützt. Vor der Migration müssen Sie den fortlaufenden Export deaktivieren.

1. Wählen Sie in der Application Insights-Ressourcenansicht unter der Überschrift **Konfigurieren** die Option **Fortlaufender Export** aus.

    ![Menüelement „Fortlaufender Export“](./media/convert-classic-resource/continuous-export.png)

2. Wählen Sie **Deaktivieren** aus.

    ![Schaltfläche zum Deaktivieren des fortlaufenden Exports](./media/convert-classic-resource/disable.png)

- Nachdem Sie „Deaktivieren“ ausgewählt haben, können Sie zurück zur Benutzeroberfläche für die Migration navigieren. Wenn auf der Seite zum Bearbeiten des fortlaufenden Exports angezeigt wird, dass Ihre Einstellungen nicht gespeichert werden, können Sie „OK“ auswählen, da dies nicht das Deaktivieren oder Aktivieren des fortlaufenden Exports betrifft.

- Nachdem Sie Ihre Application Insights-Ressource zu einer arbeitsbereichsbasierten Ressource migriert haben, können Sie mithilfe der Diagnoseeinstellungen die Funktionalität ersetzen, die vom fortlaufenden Export bereitgestellt wurde. Wählen Sie in der Application Insights-Ressource **Diagnoseeinstellungen** > **Diagnoseeinstellung hinzufügen** aus. Sie können alle Tabellen oder eine Teilmenge der Tabellen auswählen, die Sie in einem Speicherkonto archivieren oder an Azure Event Hub streamen möchten. Eine ausführliche Anleitung zu Diagnoseeinstellungen finden Sie in der [Anleitung zu Azure Monitor-Diagnoseeinstellungen](../platform/diagnostic-settings.md).

### <a name="retention-settings"></a>Aufbewahrungseinstellungen

**Warnmeldung:** *Die benutzerdefinierten Application Insights-Aufbewahrungseinstellungen gelten nicht für Daten, die an den Arbeitsbereich gesendet werden. Sie müssen dies separat konfigurieren.*

Sie müssen vor der Migration keine Änderungen vornehmen, aber Sie werden in dieser Meldung darauf aufmerksam gemacht, dass die aktuellen Application Insights-Aufbewahrungseinstellungen nicht auf den Standardwert von 90 Tagen festgelegt sind. Diese Warnmeldung bedeutet, dass Sie die Aufbewahrungseinstellungen für Ihren Log Analytics-Arbeitsbereich ändern sollten, bevor Sie die Migration und das Erfassen neuer Daten starten. 

Sie können die aktuellen Aufbewahrungseinstellungen für Log Analytics in der Log Analytics-Benutzeroberfläche unter **Allgemein** > **Nutzung und geschätzte Kosten** > **Datenaufbewahrung** überprüfen. Diese Einstellung wirkt sich darauf aus, wie lange neue erfasste Daten gespeichert werden, nachdem Sie Ihre Application Insights-Ressource migriert haben.

## <a name="next-steps"></a>Nächste Schritte

* [Untersuchen von Metriken](../platform/metrics-charts.md)
* [Schreiben von Analytics-Abfragen](../log-query/log-query-overview.md)
