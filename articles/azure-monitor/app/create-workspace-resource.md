---
title: Erstellen einer neuen arbeitsbereichsbasierten Application Insights-Ressource in Azure Monitor | Microsoft-Dokumentation
description: Erfahren Sie mehr über die erforderlichen Schritte zum Aktivieren der neuen arbeitsbereichsbasierte Application Insights-Ressourcen in Azure Monitor.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: ecbac02bcb4d9b4f0db36eab854a91366c774d6c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536913"
---
# <a name="workspace-based-application-insights-resources"></a>Arbeitsbereichsbasierte Application Insights-Ressourcen

Arbeitsbereichsbasierte Ressourcen unterstützen die vollständige Integration zwischen Application Insights und Log Analytics. Sie können jetzt Ihre Application Insights-Telemetriedaten an einen gemeinsamen Log Analytics-Arbeitsbereich senden, über den Sie auf alle Features von Log Analytics zugreifen und gleichzeitig Anwendungs-, Infrastruktur- und Plattformprotokolle an einem einzigen konsolidierten Speicherort sammeln können.

Damit ist auch eine gemeinsame rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) in allen Ihren Ressourcen möglich, und die Notwendigkeit anwendungs- bzw. arbeitsbereichsübergreifender Abfragen entfällt.

> [!NOTE]
> Die Abrechnung der Datenerfassung und -aufbewahrung für arbeitsbereichsbasierte Application Insights-Ressourcen erfolgt über den Log Analytics-Arbeitsbereich, in dem sich die Daten befinden. [Erfahren Sie mehr]( ./pricing.md#workspace-based-application-insights) über die Abrechnung für arbeitsbereichsbasierte Application Insights-Ressourcen.

## <a name="new-capabilities"></a>Neue Funktionen

Mithilfe des arbeitsbereichsbasierten Application Insights können Sie die neuesten Funktionen von Azure Monitor und Log Analytics nutzen, einschließlich:

* [CMK (Customer-Managed Keys, kundenseitig verwaltete Schlüssel)](../platform/customer-managed-keys.md) bietet Verschlüsselung im Ruhezustand für Ihre Daten mit Verschlüsselungsschlüsseln, auf die nur Sie Zugriff haben.
* Mit [Azure Private Link](../platform/private-link-security.md) können Sie Azure-PaaS-Dienste über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen.
* [Bring your own Storage (BYOS) für Profiler und Momentaufnahmedebugger](./profiler-bring-your-own-storage.md) verleiht Ihnen vollständige Kontrolle über die Richtlinie zur Verschlüsselung ruhender Daten, die Richtlinie zur Lebensdauerverwaltung und den Netzwerkzugriff auf alle Daten, die Application Insights Profiler und dem Momentaufnahmedebugger zugeordnet sind. 
* Mit [Kapazitätsreservierungsstufen](../platform/manage-cost-storage.md#pricing-model) können Sie im Vergleich zur nutzungsbasierten Zahlung bis zu 25 % sparen. 
* Schnellere Datenerfassung über Log Analytics-Streamingerfassung.

## <a name="create-workspace-based-resource"></a>Erstellen einer arbeitsbereichsbasierten Ressource

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und erstellen Sie eine Application Insights-Ressource:

![Arbeitsbereichsbasierte Application Insights-Ressource](./media/create-workspace-resource/create-workspace-based.png)

Wenn Sie noch nicht über einen Log Analytics-Arbeitsbereich verfügen, [sehen Sie sich die Dokumentation zur Erstellung von Log Analytics-Arbeitsbereichen an](../learn/quick-create-workspace.md).

**Arbeitsbereichsbasierte Ressourcen sind zurzeit in allen kommerziellen Regionen und für Azure Government verfügbar.**

Nachdem Ihre Ressource erstellt wurde, sehen Sie die Informationen zum entsprechenden Arbeitsbereich im Bereich **Übersicht**:

![Name des Arbeitsbereichs](./media/create-workspace-resource/workspace-name.png)

Durch Klicken auf den blauen Linktext gelangen Sie zum zugeordneten Log Analytics-Arbeitsbereich, wo Sie die neue einheitliche Umgebung für Arbeitsbereichsabfragen nutzen können.

> [!NOTE]
> Wir stellen auf der Application Insights-Benutzeroberfläche weiterhin vollständige Abwärtskompatibilität für Ihre klassischen Ressourcenabfragen, Arbeitsmappen und protokollbasierten Warnungen von Application Insights bereit. Um die [neue arbeitsbereichsbasierte Tabellenstruktur und das neue Tabellenschema](apm-tables.md) anzuzeigen und abzufragen, müssen Sie zuerst zu Ihrem Log Analytics-Arbeitsbereich navigieren. Durch Auswahl der Option **Protokolle (Analytics)** in den Application Insights-Bereichen erhalten Sie Zugriff auf die klassische Application Insights-Benutzeroberfläche für Abfragen.

## <a name="copy-the-connection-string"></a>Verbindungszeichenfolge kopieren

Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=net) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

## <a name="monitoring-configuration"></a>Überwachungskonfiguration

Nachdem die arbeitsbereichsbasierte Application Insights-Ressource erstellt wurde, ist die Konfiguration der Überwachung relativ unkompliziert.

### <a name="code-based-application-monitoring"></a>Codebasierte Anwendungsüberwachung

Bei der codebasierten Anwendungsüberwachung installieren Sie einfach das entsprechende Application Insights SDK und verweisen auf den Instrumentierungsschlüssel oder die Verbindungszeichenfolge für Ihre neu erstellte Ressource.  

Detaillierte Informationen zum Einrichten eines Application Insights SDK für die codebasierte Überwachung finden Sie in der jeweiligen sprach- bzw. frameworkspezifischen Dokumentation:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Hintergrundaufgaben und moderne Konsolenanwendungen (.NET/.NET Core)](./worker-service.md)
- [Klassische Konsolenanwendungen (.NET)](./console.md) 
- [Java](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Codelose Überwachung und Erstellung von Visual Studio-Ressourcen

Bei der codelosen Überwachung von Diensten wie Azure Functions und Azure App Services müssen Sie ebenfalls zuerst eine arbeitsbereichsbasierte Application Insights-Ressource erstellen und dann während der Konfigurationsphase der Überwachung auf diese Ressource verweisen.

Diese Dienste bieten zwar die Option, im Rahmen des dienstinternen Ressourcenerstellungsprozesses eine neue Application Insights-Ressource zu erstellen, allerdings sind Ressourcen, die über diese Benutzeroberflächenoptionen erstellt wurden, derzeit auf die klassische Application Insights-Benutzeroberfläche begrenzt.

Dasselbe gilt für die Benutzeroberfläche zur Erstellung von Application Insights-Ressourcen in Visual Studio für ASP.NET und ASP.NET Core. Sie müssen eine vorhandene arbeitsbereichsbasierte Ressource auf der Visual Studio-Benutzeroberfläche zur Aktivierung der Überwachung auswählen. Über die Option zum Erstellen einer neuen Ressource in Visual Studio können Sie nur klassische Application Insights-Ressourcen erstellen.

## <a name="creating-a-resource-automatically"></a>Automatisches Erstellen einer Ressource

### <a name="azure-cli"></a>Azure CLI

Für den Zugriff auf die Azure CLI-Befehle (Vorschauversion) von Application Insights müssen Sie zuerst Folgendes ausführen:

```azurecli
 az extension add -n application-insights
```

Wenn Sie den Befehl `az extension add` nicht ausführen, wird eine Fehlermeldung mit dem folgenden Hinweis angezeigt: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Jetzt können Sie Folgendes ausführen, um Ihre Application Insights-Ressource zu erstellen:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Beispiel

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Vollständige Informationen zu diesem Befehl finden Sie in der [Azure CLI-Dokumentation](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

Der PowerShell-Befehl `New-AzApplicationInsights` unterstützt derzeit nicht die Erstellung einer arbeitsbereichsbasierten Application Insights-Ressource. Um eine arbeitsbereichsbasierte Ressource mit PowerShell zu erstellen, können Sie die folgenden Azure Resource Manager-Vorlagen verwenden und die Ressource über PowerShell bereitstellen.

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

Wählen Sie im Bereich der Application Insights-Ressource die Optionen **Eigenschaften** > **Arbeitsbereich ändern** > **Log Analytics-Arbeitsbereiche**.

## <a name="export-telemetry"></a>Exportieren von Telemetriedaten

Die Legacyfunktion für den fortlaufenden Export wird für arbeitsbereichsbasierte Ressourcen nicht unterstützt. Wählen Sie stattdessen in Ihre Application Insights-Ressource die Optionen **Diagnoseeinstellungen** > **Diagnoseeinstellung hinzufügen**. Sie können alle Tabellen oder eine Teilmenge der Tabellen auswählen, die Sie in einem Speicherkonto archivieren oder an Azure Event Hub streamen möchten.

> [!NOTE]
> Für den Export von Streamingdaten fallen zurzeit keine zusätzlichen Gebühren an. Die Preisinformationen für dieses Feature finden Sie auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).  Zu Beginn des Abrechnungszeitraums erhalten Sie eine entsprechende Benachrichtigung. Falls Sie sich dafür entscheiden, <feature name> über den Benachrichtigungszeitraum hinaus zu verwenden, wird Ihnen der entsprechende Tarif in Rechnung gestellt. 
 

## <a name="next-steps"></a>Nächste Schritte

* [Untersuchen von Metriken](../platform/metrics-charts.md)
* [Schreiben von Analytics-Abfragen](../log-query/log-query-overview.md)