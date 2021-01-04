---
title: Überwachen von Azure Firewall-Protokollen und -Metriken
description: In diesem Artikel erfahren Sie, wie Sie Azure Firewall-Protokolle und -Metriken aktivieren und verwalten.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2dd1b51c6bcdbc531661d9ecf45d3d0282eb5b45
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358846"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Überwachen von Azure Firewall-Protokollen und -Metriken

Azure Firewall kann mithilfe von Firewallprotokollen überwacht werden. Sie können aber auch Aktivitätsprotokolle verwenden, um Vorgänge für Azure Firewall-Ressourcen zu überwachen. Mithilfe von Metriken können Sie Leistungsindikatoren im Portal anzeigen.

Sie können auf einige dieser Protokolle über das Portal zugreifen. Protokolle können an [Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md), Storage und Event Hubs gesendet und in Azure Monitor-Protokollen oder durch andere Tools wie Excel oder Power BI analysiert werden.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie vor Beginn den Artikel [Azure Firewall-Protokolle und -Metriken](logs-and-metrics.md), um einen Überblick über die für Azure Firewall verfügbaren Diagnoseprotokolle und Metriken zu erhalten.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Aktivieren der Diagnoseprotokollierung über das Azure-Portal

Nach dem Aktivieren der Diagnoseprotokollierung kann es noch einige Minuten dauern, bis die Daten in Ihren Protokollen erscheinen. Sollte zunächst nichts angezeigt werden, warten Sie noch ein paar Minuten, und versuchen Sie es dann erneut.

1. Öffnen Sie im Azure-Portal die Ressourcengruppe Ihrer Firewall, und klicken Sie auf die Firewall.
2. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

   Für Azure Firewall sind vier dienstspezifische Protokolle verfügbar:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Klicken Sie auf **Diagnoseeinstellung hinzufügen**. Auf der Seite **Diagnoseeinstellungen** befinden sich die Einstellungen für die Diagnoseprotokolle.
5. Da die Protokolle in diesem Beispiel in Azure Monitor-Protokollen gespeichert werden, geben Sie **Firewall log analytics** als Name ein.
6. Wählen Sie unter **Protokoll** die Einträge **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** und **AzureFirewallDnsProxy** aus, um die Protokolle zu erfassen.
7. Klicken Sie auf **An Log Analytics senden**, um Ihren Arbeitsbereich zu konfigurieren.
8. Wählen Sie Ihr Abonnement aus.
9. Wählen Sie **Speichern** aus.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Aktivieren der Diagnoseprotokollierung mithilfe von PowerShell

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Die Diagnoseprotokollierung muss aktiviert werden, um mit der Erfassung der Daten zu beginnen, die über diese Protokolle bereitgestellt werden.

Gehen Sie wie folgt vor, um die Diagnoseprotokollierung mithilfe von PowerShell zu aktivieren:

1. Notieren Sie sich die Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs, unter dem die Protokolldaten gespeichert werden. Dieser Wert hat das Format `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`.

   Sie können einen beliebigen Arbeitsbereich Ihres Abonnements verwenden. Sie können das Azure-Portal verwenden, um nach diesen Informationen zu suchen. Die Informationen befinden sich auf der Seite **Eigenschaften** der Ressource.

2. Notieren Sie sich die Ressourcen-ID Ihrer Firewall, für die die Protokollierung aktiviert wird. Dieser Wert hat das Format `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`.

   Sie können das Portal verwenden, um nach diesen Informationen zu suchen.

3. Aktivieren Sie die Diagnoseprotokollierung für alle Protokolle und Metriken mit dem folgenden PowerShell-Cmdlet:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Aktivieren der Diagnoseprotokollierung mithilfe der Azure-Befehlszeilenschnittstelle

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Die Diagnoseprotokollierung muss aktiviert werden, um mit der Erfassung der Daten zu beginnen, die über diese Protokolle bereitgestellt werden.

Gehen Sie wie folgt vor, um die Diagnoseprotokollierung mithilfe der Azure-Befehlszeilenschnittstelle zu aktivieren:

1. Notieren Sie sich die Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs, unter dem die Protokolldaten gespeichert werden. Dieser Wert hat das Format `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`.

   Sie können einen beliebigen Arbeitsbereich Ihres Abonnements verwenden. Sie können das Azure-Portal verwenden, um nach diesen Informationen zu suchen. Die Informationen befinden sich auf der Seite **Eigenschaften** der Ressource.

2. Notieren Sie sich die Ressourcen-ID Ihrer Firewall, für die die Protokollierung aktiviert wird. Dieser Wert hat das Format `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`.

   Sie können das Portal verwenden, um nach diesen Informationen zu suchen.

3. Aktivieren Sie die Diagnoseprotokollierung für alle Protokolle und Metriken mit dem folgenden Azure CLI-Befehl:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Anzeigen und Analysieren des Aktivitätsprotokolls

Mit einer der folgenden Methoden können Sie die Aktivitätsprotokolldaten anzeigen und analysieren:

* **Azure-Tools:** Rufen Sie Informationen aus dem Aktivitätsprotokoll über Azure PowerShell, über die Azure-Befehlszeilenschnittstelle, mithilfe der Azure-REST-API oder über das Azure-Portal ab. Detaillierte Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI**: Falls Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe des [Azure Activity Logs Content Pack for Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) können Sie Ihre Daten mit vorkonfigurierten Dashboards analysieren, die Sie im Istzustand oder angepasst verwenden können.
* **Azure Sentinel:** Sie können Azure Firewall-Protokolle mit Azure Sentinel verbinden, was es Ihnen ermöglicht, Protokolldaten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und sie zur Verbesserung Ihrer Untersuchung zu integrieren. Der Azure Firewall-Datenconnector in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zu Daten aus Azure Firewall](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Anzeigen und Analysieren der Netzwerk- und Anwendungsregelprotokolle

[Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md) erfassen die Leistungsindikator- und Ereignisprotokolldateien. Die Anwendung umfasst Visualisierungen und leistungsfähige Suchfunktionen zum Analysieren Ihrer Protokolle.

Log Analytics-Beispielabfragen für Azure Firewall finden Sie unter [Log Analytics-Beispiele für Azure Firewall](log-analytics-samples.md).

[Azure Firewall Workbook](firewall-workbook.md) bietet einen flexiblen Bereich für die Azure Firewall-Datenanalyse. Sie können damit umfangreiche visuelle Berichte innerhalb des Azure-Portals erstellen. Sie können mehrere in Azure bereitgestellte Firewalls nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren.

Sie können auch eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für Zugriffs- und Leistungsprotokolle abrufen. Nachdem Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, Power BI oder einem anderen Datenvisualisierungstool anzeigen.

> [!TIP]
> Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von GitHub verwenden.

## <a name="view-metrics"></a>Anzeigen von Metriken
Navigieren Sie zu einer Azure Firewall-Instanz, und klicken Sie unter **Überwachung** auf **Metriken**. Um die verfügbaren Werte anzuzeigen, wählen Sie die Dropdownliste **METRIK** aus.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Firewall für die Erfassung von Protokollen konfiguriert haben, können Sie sich als Nächstes mit dem Anzeigen der Daten in Azure Monitor-Protokollen beschäftigen.

[Überwachen von Protokollen mit Azure Firewall Workbook](firewall-workbook.md)

[Azure-Netzwerküberwachungslösungen in Azure Monitor-Protokollen](../azure-monitor/insights/azure-networking-analytics.md)
