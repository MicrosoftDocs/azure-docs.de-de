---
title: Überwachungslösungen in Azure Monitor | Microsoft-Dokumentation
description: Bei Überwachungslösungen in Azure Monitor handelt es sich um eine Sammlung von Logik-, Visualisierungs- und Datenerfassungsregeln, die Metriken zu einem bestimmten Problembereich bereitstellen.  Dieser Artikel enthält Informationen zur Installation und Verwendung von Überwachungslösungen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9ced3dfeccdbac5f0eb220cf0e104679f263aac
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186863"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Überwachungslösungen in Azure Monitor

Überwachungslösungen in Azure Monitor ermöglichen eine Analyse des Betriebs einer bestimmten Azure-Anwendung oder eines bestimmten Azure-Diensts. Dieser Artikel enthält eine kurze Übersicht über Überwachungslösungen in Azure sowie Details zu deren Verwendung und Installation. Überwachungslösungen können Azure Monitor für beliebige Anwendungen und Dienste hinzugefügt werden, die Sie verwenden. Die Lösungen sind in der Regel kostenlos, durch die Datensammlung können jedoch Kosten entstehen.

## <a name="use-monitoring-solutions"></a>Verwenden von Überwachungslösungen

Auf der **Übersichtsseite** der Lösung in Azure Monitor wird für jede in einem Log Analytics-Arbeitsbereich installierte Lösung jeweils eine Kachel angezeigt. Um diese Seite zu öffnen, navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu **Azure Monitor**. Wählen Sie im Menü **Insights** die Option **Mehr** aus, um den **Insights-Hub** zu öffnen, und klicken Sie dann auf **Log Analytics-Arbeitsbereiche**.

[![Insights-Hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Ändern Sie über die Dropdownfelder am oberen Bildschirmrand den Arbeitsbereich oder den Zeitbereich für die Kacheln. Klicken Sie auf die Kachel für eine Lösung, um die dazugehörige Ansicht mit einer ausführlicheren Analyse der gesammelten Daten anzuzeigen.

[![Screenshot des Menüs des Azure-Portals mit ausgewählter Option „Lösungen“ und im Bereich „Lösungen“ angezeigten Lösungen](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Überwachungslösungen können mehrere Arten von Azure-Ressourcen enthalten, und Sie können beliebige, in einer Lösung enthaltene Ressourcen anzeigen (genau wie bei anderen Ressourcen). Beispielsweise werden alle in der Lösung enthaltenen Protokollabfragen unter **Lösungsabfragen** im [Abfrage-Explorer](../log-query/log-analytics-tutorial.md) aufgeführt. Sie können diese Abfragen bei Ad-hoc-Analysen mit [Protokollabfragen](../log-query/log-query-overview.md) verwenden.

## <a name="list-installed-monitoring-solutions"></a>Auflisten installierter Überwachungslösungen

### <a name="portal"></a>[Portal](#tab/portal)

Gehen Sie wie folgt vor, um die in Ihrem Abonnement installierten Überwachungslösungen aufzulisten.

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com). Suchen Sie nach **Lösungen**, und wählen Sie diese Option aus.
1. Alle in Ihren Arbeitsbereichen installierten Lösungen werden aufgelistet. Auf den Namen der Lösung folgt der Name des Arbeitsbereichs, in dem sie installiert ist.
1. Mit den Dropdownfeldern am oberen Bildschirmrand können Sie die Anzeige nach Abonnement oder Ressourcengruppe filtern.

![Auflisten aller Lösungen](media/solutions/list-solutions-all.png)

Klicken Sie auf den Namen einer Lösung, um die dazugehörige Zusammenfassungsseite zu öffnen. Auf dieser Seite werden alle in der Lösung enthaltenen Ansichten angezeigt, und es stehen verschiedene Optionen für die Lösung und den Arbeitsbereich zur Verfügung. Zeigen Sie die Zusammenfassungsseite für eine Lösung an. Verwenden Sie hierzu eines der obigen Verfahren zum Auflisten der Lösungen, und klicken Sie anschließend auf den Namen der Lösung.

![Lösungseigenschaften](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az monitor log-analytics solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list), um die in Ihrem Abonnement installierten Überwachungslösungen aufzulisten.   Überprüfen Sie vor dem Ausführen des Befehls `list` die Voraussetzungen in [Installieren einer Überwachungslösung](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution), um die in Ihrem Abonnement installierten Überwachungslösungen anzuzeigen. Überprüfen Sie vor dem Ausführen dieser Befehle die Voraussetzungen im Artikel [Installieren einer Überwachungslösung](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Installieren einer Überwachungslösung

### <a name="portal"></a>[Portal](#tab/portal)

Überwachungslösungen von Microsoft und Partnern sind im [Azure Marketplace](https://azuremarketplace.microsoft.com) verfügbar. Sie können nach verfügbaren Lösungen suchen und sie mit folgendem Verfahren installieren. Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../platform/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden.

1. Klicken Sie in der [Liste mit Lösungen für Ihr Abonnement](#list-installed-monitoring-solutions) auf **Hinzufügen**.
1. Durchsuchen Sie eine Projektmappe oder suchen Sie sie. Sie können auch Projektmappen über [diesen Suchlink](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions) durchsuchen.
1. Suchen Sie die gewünschte Überwachungslösung, und lesen Sie ihre Beschreibung.
1. Klicken Sie auf **Erstellen**, um den Installationsprozess zu starten.
1. Wenn der Installationsvorgang gestartet wird, werden Sie aufgefordert, den Log Analytics-Arbeitsbereich anzugeben und die erforderliche Konfiguration für die Projektmappe bereitzustellen.

![Installieren einer Lösung](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installieren einer Lösung aus der Community

Mitglieder der Community können Verwaltungslösungen für Azure-Schnellstartvorlagen senden. Sie können diese Lösungen entweder direkt installieren oder Vorlagen für eine spätere Installation herunterladen.

1. Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.
2. Gehen Sie zu [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/).
3. Suchen Sie nach einer Lösung, an der Sie interessiert sind.
4. Wählen Sie die Lösung aus den Ergebnissen aus, um ihre Details anzuzeigen.
5. Klicken Sie auf die Schaltfläche **Bereitstellen in Azure**.
6. Sie werden aufgefordert, Informationen wie die Ressourcengruppe und den Standort sowie Werte für Parameter in der Lösung anzugeben.
7. Klicken Sie auf **Kaufen**, um die Lösung zu installieren.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Installieren der Azure CLI

   Vor dem Ausführen der CLI-Referenzbefehle müssen Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) installieren.  Auf Wunsch können Sie auch Azure Cloud Shell verwenden, um die Schritte in diesem Artikel auszuführen.  Azure Cloud Shell ist eine interaktive Shellumgebung, die Sie über Ihren Browser nutzen können.  Starten Sie Cloud Shell mit einer der folgenden Methoden:

   - Öffnen Sie Cloud Shell, indem Sie zu [https://shell.azure.com](https://shell.azure.com) navigieren.

   - Wählen Sie im [Azure-Portal](https://portal.azure.com) rechts oben im Menü die Schaltfläche **Cloud Shell** aus.

1. Melden Sie sich an.

   Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) an, falls Sie eine lokale Installation der Befehlszeilenschnittstelle verwenden.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

    ```azurecli
    az login
    ```

1. Installieren Sie die `log-analytics-solution`-Erweiterung.

   Der Befehl `log-analytics-solution` ist eine experimentelle Erweiterung der eigentlichen Azure-Befehlszeilenschnittstelle. Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Die folgende Warnung wird erwartet.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Installieren einer Lösung mit der Azure-Befehlszeilenschnittstelle

Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../platform/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden.  Mit der Azure-Befehlszeilenschnittstelle verwalten Sie Arbeitsbereiche mithilfe der Referenzbefehle [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace).  Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.

Verwenden Sie [az monitor log-analytics solution create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution), um eine Überwachungslösung zu installieren.  Parameter in eckigen Klammern sind optional.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Hier finden Sie ein Codebeispiel, mit dem eine Log Analytics-Lösung für das Planprodukt von OMSGallery/Containers erstellt wird.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Installieren von Azure Powershell

   Vor dem Ausführen von PowerShell-Verweisbefehlen müssen Sie [Azure PowerShell installieren](/powershell/azure/install-az-ps). Wenn Sie möchten, können Sie auch Azure Cloud Shell verwenden, um die Schritte in diesem Artikel auszuführen. Azure Cloud Shell ist eine interaktive Shellumgebung, die Sie über Ihren Browser nutzen können. Starten Sie Cloud Shell mit einer der folgenden Methoden:

   - Öffnen Sie Cloud Shell, indem Sie zu [https://shell.azure.com](https://shell.azure.com) navigieren.

   - Wählen Sie im [Azure-Portal](https://portal.azure.com) rechts oben im Menü die Schaltfläche **Cloud Shell** aus.

   > [!IMPORTANT]
   > Solange nur eine Vorschauversion des PowerShell-Moduls **Az.MonitoringSolutions** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Melden Sie sich an.

   Wenn Sie eine lokale Installation von PowerShell verwenden, melden Sie sich mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) an. Führen Sie die in PowerShell angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Installieren einer Lösung mit Azure PowerShell

Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../platform/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden. Mit Azure PowerShell verwalten Sie Arbeitsbereiche mithilfe der Cmdlets im PowerShell-Modul [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions). Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.

Verwenden Sie das Cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution), um eine Überwachungslösung zu installieren. Parameter in eckigen Klammern sind optional.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Im folgenden Beispiel wird eine Log Analytics-Überwachungslösung für den Log Analytics-Arbeitsbereich erstellt.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-Arbeitsbereich und Automation-Konto

Für alle Überwachungslösungen wird ein [Log Analytics-Arbeitsbereich](../platform/manage-access.md) zum Speichern der von der Lösung gesammelten Daten sowie zum Hosten der Protokollsuchvorgänge und Ansichten benötigt. Einige Lösungen erfordern auch ein [Automation-Konto](../../automation/automation-security-overview.md) für Runbooks und zugehörige Ressourcen. Der Arbeitsbereich und das Konto müssen den folgenden Anforderungen entsprechen.

* Eine Lösungsinstallation kann jeweils nur einen Log Analytics-Arbeitsbereich und ein einzelnes Automation-Konto verwenden. Sie können die Lösung separat in mehreren Arbeitsbereichen installieren.
* Sollte eine Lösung ein Automation-Konto benötigen, müssen der Log Analytics-Arbeitsbereich und das Automation-Konto miteinander verknüpft werden. Ein Log Analytics-Arbeitsbereich kann nur mit einem Automation-Konto verknüpft werden, und ein Automation-Konto kann nur mit einem Log Analytics-Arbeitsbereich verknüpft werden.

Wenn Sie die Lösung über den Azure Marketplace installieren, werden Sie zur Angabe eines Arbeitsbereichs und eines Automation-Kontos aufgefordert. Die Verknüpfung zwischen beiden wird für Sie erstellt, sofern sie noch nicht besteht.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Überprüfen der Verknüpfung zwischen einem Log Analytics-Arbeitsbereich und einem Automation-Konto

Sie können die Verknüpfung zwischen einem Log Analytics-Arbeitsbereich und einem Automation-Konto mithilfe des folgenden Verfahrens überprüfen.

1. Wählen Sie im Azure-Portal das Automation-Konto aus.
1. Scrollen Sie zum Abschnitt **Zugehörige Ressourcen** des Menüs, und wählen Sie **Verknüpfter Arbeitsbereich** aus.
1. Wenn der **Arbeitsbereich** mit einem Automation-Konto verknüpft ist, wird auf dieser Seite der Arbeitsbereich aufgelistet, mit dem es verknüpft ist. Wenn Sie den Namen des aufgelisteten Arbeitsbereichs auswählen, werden Sie zur Übersichtsseite für diesen Arbeitsbereich weitergeleitet.

## <a name="remove-a-monitoring-solution"></a>Entfernen einer Überwachungslösung

### <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie eine installierte Lösung über das Portal entfernen möchten, suchen Sie sie in der [Liste mit den installierten Lösungen](#list-installed-monitoring-solutions). Klicken Sie auf den Namen der Lösung, um die dazugehörige Zusammenfassungsseite zu öffnen, und klicken Sie anschließend auf **Löschen**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az monitor log-analytics solution delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete), um eine installierte Lösung mithilfe der Azure-Befehlszeilenschnittstelle zu entfernen.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution), um eine installierte Lösung mithilfe von Azure PowerShell zu entfernen.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Nächste Schritte

* Rufen Sie eine [Liste mit Überwachungslösungen von Microsoft](../monitor-reference.md) ab.
* Machen Sie sich mit dem [Erstellen von Abfragen](../log-query/log-query-overview.md) für die Analyse der von Überwachungslösungen gesammelten Daten vertraut.
* Sehen Sie sich alle [Beispiel-CLI-Befehle für Azure Monitor](/cli/azure/azure-cli-reference-for-monitor) an.