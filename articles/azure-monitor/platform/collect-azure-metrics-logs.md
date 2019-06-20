---
title: Sammeln von Azure-Dienstprotokollen und Metriken für einen Log Analytics-Arbeitsbereich | Microsoft-Dokumentation
description: Konfigurieren Sie Diagnosen für Azure-Ressourcen, um Protokolle und Metriken in einen Log Analytics-Arbeitsbereich in Azure Monitor zu schreiben.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129720"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Sammeln von Azure-Dienstprotokollen und Metriken für einen Log Analytics-Arbeitsbereich in Azure Monitor

Protokolle und Metriken für Azure-Dienste können auf vier Arten erfasst werden:

1. Azure-Diagnosen direkt an einen Log Analytics-Arbeitsbereich in Azure Monitor (*Diagnose* in der folgenden Tabelle)
2. Azure-Diagnosen in Azure-Speicher an einen Log Analytics-Arbeitsbereich in Azure Monitor (*Storage* in der folgenden Tabelle)
3. Connectors für Azure-Dienste (*Connectors* in der folgenden Tabelle)
4. Skripts zum Sammeln und anschließenden Veröffentlichen von Daten in einen Log Analytics-Arbeitsbereich in Azure Monitor (Leerstellen in der folgenden Tabelle und für nicht aufgeführte Dienste)


| Dienst                 | Ressourcentyp                           | Protokolle        | metrics     | Lösung |
| --- | --- | --- | --- | --- |
| Anwendungsgateways    | Microsoft.Network/applicationGateways   | Diagnose | Diagnose | [Azure Application Gateway-Analyse](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application Insights    |                                         | Connector   | Connector   | [Application Insights-Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (Vorschau) |
| Automation-Konten     | Microsoft.Automation/AutomationAccounts | Diagnose |             | [Weitere Informationen](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-Konten          | Microsoft.Batch/batchAccounts           | Diagnose | Diagnose | |
| Klassische Clouddienste  |                                         | Storage     |             | [Weitere Informationen](azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | Diagnose | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnose |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnose |             | |
| Event Hub-Namespace     | Microsoft.EventHub/namespaces           | Diagnose | Diagnose | |
| IoT Hubs                | Microsoft.Devices/IotHubs               |             | Diagnose | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnose |             | [KeyVault-Analyse](../insights/azure-key-vault.md) |
| Load Balancer          | Microsoft.Network/loadBalancers         | Diagnose |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnose | Diagnose | |
| Netzwerksicherheitsgruppen | Microsoft.Network/networksecuritygroups | Diagnose |             | [Azure-Netzwerksicherheitsgruppen-Analyse](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Recovery-Tresore         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (Vorschau)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Suchdienste         | Microsoft.Search/searchServices         | Diagnose | Diagnose | |
| Service Bus-Namespace   | Microsoft.ServiceBus/namespaces         | Diagnose | Diagnose | [Service Bus Analytics (Vorschau)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric-Analysen (Vorschau)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnose | [Azure SQL Analytics (Vorschau)](../insights/azure-sql.md) |
| Storage                 |                                         |             | Skript      | [Azure Storage Analytics (Vorschau)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | Durchwahl   | Durchwahl <br> Diagnose  | |
| VM-Skalierungsgruppen | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnose | |
| Webserverfarmen        | Microsoft.Web/serverfarms               |             | Diagnose | |
| Websites               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnose | [Azure Web Apps Analytics (Vorschau)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Für die Überwachung von virtuellen Azure-Computern (Linux und Windows) empfehlen wir die Installation der [Log Analytics-VM-Erweiterung](../../azure-monitor/learn/quick-collect-azurevm.md). Der Agent liefert Erkenntnisse, die aus Ihren virtuellen Computern zusammengetragen wurden. Die Erweiterung kann auch für VM-Skalierungsgruppen verwendet werden.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure-Diagnosen direkt an Log Analytics
Viele Azure-Ressourcen können Diagnoseprotokolle und Metriken direkt in einen Log Analytics-Arbeitsbereich in Azure Monitor schreiben. Dies ist die bevorzugte Datensammlungsmethode für die Analyse. Bei Verwendung von Azure-Diagnosen werden Daten unmittelbar in den Arbeitsbereich geschrieben und müssen nicht zuerst in den Speicher geschrieben werden.

Azure-Ressourcen, die [Azure Monitor](../../azure-monitor/overview.md) unterstützen, können ihre Protokolle und Metriken direkt an einen Log Analytics-Arbeitsbereich senden.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken an einen Log Analytics-Arbeitsbereich über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

* Ausführliche Informationen zu den verfügbaren Metriken finden Sie unter [Supported metrics with Azure Monitor](../../azure-monitor/platform/metrics-supported.md) (Von Azure Monitor unterstützte Metriken).
* Ausführliche Informationen zu den verfügbaren Protokollen finden Sie unter [Supported services and schema for Diagnostic Logs](../../azure-monitor/platform/diagnostic-logs-schema.md) (Unterstützte Dienste und Schema für Diagnoseprotokolle).

### <a name="enable-diagnostics-with-powershell"></a>Aktivieren der Diagnose mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Das folgende PowerShell-Beispiel zeigt, wie mithilfe von [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) die Diagnose für eine Netzwerksicherheitsgruppe aktiviert wird. Die gleiche Herangehensweise kann für alle unterstützten Ressourcen verwendet werden. Legen Sie `$resourceId` auf die Ressourcen-ID der Ressource fest, für die Sie die Diagnose aktivieren möchten.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Aktivieren der Diagnose mithilfe von Resource Manager-Vorlagen

Wenn die Diagnose für eine Ressource bei deren Erstellung aktiviert und an Ihren Log Analytics-Arbeitsbereich gesendet werden soll, können Sie eine Vorlage wie die folgende verwenden. Hierbei handelt es sich zwar um ein Beispiel für ein Automation-Konto, es kann jedoch für jeden unterstützten Ressourcentyp verwendet werden.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure-Diagnose über den Speicher an Log Analytics

Bei einigen Ressourcen können Sie die Protokolle an Azure Storage senden und den Log Analytics-Arbeitsbereich so konfigurieren, dass die Protokolle aus dem Speicher gelesen werden.

Mit dieser Vorgehensweise kann Azure Monitor Diagnosen für folgende Ressourcen und Protokolle aus Azure Storage erfassen:

| Resource | Protokolle |
| --- | --- |
| Service Fabric |ETWEvent <br> Operative Ereignisse <br> Reliable Actor-Ereignis <br> Reliable Services-Ereignis |
| Virtual Machines |Linux-Syslog <br> Windows-Ereignis <br> IIS-Protokoll <br> Windows-ETWEvent |
| Webrollen <br> Workerrollen |Linux-Syslog <br> Windows-Ereignis <br> IIS-Protokoll <br> Windows-ETWEvent |

> [!NOTE]
> Ihnen werden die üblichen Azure-Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnosedaten an ein Speicherkonto senden und der Log Analytics-Arbeitsbereich die Daten aus Ihrem Speicherkonto liest.
>
>

Weitere Information dazu, wie Azure Monitor diese Protokolle erfassen kann, finden Sie unter [Verwenden von Blob Storage für IIS und Table Storage für Ereignisse](azure-storage-iis-table.md).

## <a name="connectors-for-azure-services"></a>Connectors für Azure-Dienste

Es gibt einen Connector für Application Insights, mit dem Daten, die von Application Insights gesammelt wurden, an einen Log Analytics-Arbeitsbereich gesendet werden können.

Weitere Informationen zum Application Insights-Connector finden Sie [hier](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>Skripts zum Sammeln und Veröffentlichen von Daten in einem Log Analytics-Arbeitsbereich

Für Azure-Dienste, bei denen Protokolle und Metriken nicht direkt an einen Log Analytics-Arbeitsbereich gesendet werden können, können Sie das Protokoll und die Metriken mithilfe von Azure Automation erfassen. Das Skript kann die Daten dann über die [Datensammler-API](../../azure-monitor/platform/data-collector-api.md) an den Arbeitsbereich senden.

Der Azure-Vorlagenkatalog enthält [Beispiele für die Verwendung von Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) zum Sammeln von Daten aus Diensten und zum Senden der Daten an Azure Monitor.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Blob Storage für IIS und Table Storage für Ereignisse](azure-storage-iis-table.md) enthält Informationen zum Lesen der Protokolle für Azure-Dienste, die Diagnosedaten in Table Storage schreiben, oder der IIS-Protokolle, die in Blob Storage geschrieben werden.
* [Aktivieren Sie Lösungen](../../azure-monitor/insights/solutions.md) , um Einblick in die Daten bereitzustellen.
* [Erstellen Sie Suchabfragen](../../azure-monitor/log-query/log-query-overview.md) , um die Daten zu analysieren.
