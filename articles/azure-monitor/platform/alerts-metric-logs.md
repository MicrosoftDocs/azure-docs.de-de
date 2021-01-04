---
title: Erstellen von Metrikwarnungen für Protokolle in Azure Monitor
description: Tutorial zur Erstellung von Metrikwarnungen in Echtzeit für gängige Log Analytics-Daten.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 06/17/2020
ms.subservice: alerts
ms.openlocfilehash: 7c1e1d52b8434db930fcdd05c5c5441ba027abc8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186013"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Erstellen von Metrikwarnungen für Protokolle in Azure Monitor

## <a name="overview"></a>Übersicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor unterstützt den [Metrikwarnungstyp](./alerts-metric-near-real-time.md). Dies hat Vorteile gegenüber den [klassischen Warnungen](./alerts-classic-portal.md). Metriken stehen für [eine umfangreiche Liste von Azure-Diensten](./metrics-supported.md) zur Verfügung. In diesem Artikel wird die Verwendung einer Teilmenge erläutert, d. h. für eine Ressource – `Microsoft.OperationalInsights/workspaces`.

Sie können Metrikwarnungen für gängige Log Analytics-Protokolle verwenden, die als Metriken als Teil von Metriken aus Protokollen extrahiert wurden, einschließlich Ressourcen in Azure oder lokal. Die unterstützten Log Analytics-Lösungen sind nachfolgend aufgeführt:

- [Leistungsindikatoren](./data-sources-performance-counters.md) für Windows- und Linux-Computer
- [Heartbeat-Datensätze für Agent-Integritätsdiagnose](../insights/solution-agenthealth.md)
- Datensätze der [Updateverwaltung](../../automation/update-management/overview.md)
- [Ereignisdaten](./data-sources-windows-events.md)protokolle

Es gibt viele Vorteile für die Verwendung von **Metrikwarnungen für Protokolle** gegenüber abfragebasierten [Protokollwarnungen](./alerts-log.md) in Azure. Einige von ihnen sind unten aufgeführt:

- Metrikwarnungen bieten Überwachungsfunktionen nahezu in Echtzeit, und Metrikwarnungen für Protokolle geben Daten aus der Protokollquelle an, um dasselbe sicherzustellen.
- Metrikwarnungen sind zustandsbehaftet. Es erfolgt nur eine Benachrichtigung, wenn ein Alarm ausgelöst wird, und eine Benachrichtigung, wenn der Alarm aufgehoben wird. Dies steht im Gegensatz zu Protokollwarnungen, die zustandslos sind und in jedem Intervall ausgelöst werden, wenn die Bedingung der Warnung erfüllt ist.
- Metrikwarnungen für Protokolle bieten mehrere Dimensionen, die es ermöglichen, bestimmte Werte wie Computer, Betriebssystemtyp usw. einfacher zu filtern, ohne dass eine verfassende Abfrage bei der Analyse erforderlich ist.

> [!NOTE]
> Bestimmte Metriken und/oder Dimensionen werden nur angezeigt, wenn im gewählten Zeitraum dafür Daten vorhanden sind. Diese Metriken sind für Kunden mit Azure Log Analytics-Arbeitsbereichen verfügbar.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Für Protokolle unterstützte Metriken und Dimensionen

 Metrikwarnungen unterstützen Warnungen für Metriken mit Dimensionen. Mithilfe von Dimensionen können Sie die Metrik nach der richtigen Ebene filtern. Die vollständige Liste der unterstützten Metriken für Protokolle aus [Log Analytics-Arbeitsbereichen](./metrics-supported.md#microsoftoperationalinsightsworkspaces) ist aufgelistet (für unterstützte Lösungen).

> [!NOTE]
> Um eine unterstützte Metrik anzuzeigen, die über [Azure Monitor – Metriken](./metrics-charts.md) aus dem Log Analytics-Arbeitsbereich extrahiert werden können, muss für diese Metrik eine Metrikwarnung für Protokolle erstellt werden. Die in der Metrikwarnung für Protokolle gewählten Dimensionen werden nur für die Untersuchung über „Azure Monitor – Metriken“ angezeigt.

## <a name="creating-metric-alert-for-log-analytics"></a>Erstellen von Metrikwarnungen für Log Analytics

Metrikdaten aus gängigen Protokollen werden vor der Verarbeitung in Log Analytics an „Azure Monitor – Metriken“ weitergeleitet. Auf diese Weise können Benutzer die Funktionen der Metrikplattform sowie die Metrikwarnung nutzen, einschließlich der Möglichkeit, Warnungen mit einer Häufigkeit von bis zu einer Minute zu erhalten.
Nachfolgend sind die Möglichkeiten aufgeführt, eine Metrikwarnung für Protokolle zu erstellen.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Voraussetzungen für Metrikwarnungen für Protokolle

Bevor die Metrik für Protokolle, die über Log Analytics-Daten erfasst wurden, funktioniert, muss Folgendes eingerichtet und verfügbar sein:

1. **Active Log Analytics-Arbeitsbereich**: Es muss ein gültiger und aktiver Log Analytics-Arbeitsbereich vorhanden sein. Weitere Informationen finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md).
2. **Agent ist für Log Analytics-Arbeitsbereich konfiguriert**: Der Agent muss für Azure VMs (und/oder) lokale VMs konfiguriert werden, um Daten in den Log Analytics-Arbeitsbereich zu senden, der in einem früheren Schritt verwendet wurde. Weitere Informationen finden Sie unter [Log Analytics – Übersicht über Agents](./agents-overview.md).
3. **Unterstützte Log Analytics-Lösungen sind installiert:** : Die Log Analytics-Lösung sollte konfiguriert sein und Daten in den Log Analytics-Arbeitsbereich senden – unterstützte Lösungen sind [Leistungsindikatoren für Windows und Linux](./data-sources-performance-counters.md), [Heartbeat-Datensätze für Agent-Integritätsdiagnose](../insights/solution-agenthealth.md), [Update-Management](../../automation/update-management/overview.md) und [Ereignisdaten](./data-sources-windows-events.md).
4. **Zum Senden von Protokollen konfigurierte Log Analytics-Lösungen**: Für die Log Analytics-Lösung müssen die erforderlichen Protokolle/Daten entsprechend der für [Log Analytics-Arbeitsbereiche unterstützten Metriken](./metrics-supported.md#microsoftoperationalinsightsworkspaces) aktiviert sein. Der Zähler *% Verfügbarer Arbeitsspeicher* muss z. B. zuerst in der Lösung [Leistungsindikatoren](./data-sources-performance-counters.md) konfiguriert sein.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurieren der Metrikwarnung für Protokolle

 Metrikwarnungen können über das Azure-Portal, Resource Manager-Vorlagen, REST-API, PowerShell und die Azure CLI erstellt und verwaltet werden. Da Metrikwarnungen für Protokolle eine Variante von Metrikwarnungen sind, können nach Erfüllung der Voraussetzungen Metrikwarnungen für Protokolle für den angegebenen Log Analytics-Arbeitsbereich erstellt werden. Alle Merkmale und Funktionalitäten von [Metrikwarnungen](./alerts-metric-near-real-time.md) gelten auch für Metrikwarnungen für Protokolle; einschließlich Nutzlastschema, anwendbaren Kontingentgrenzen und fakturiertem Preis.

Schritt-für-Schritt-Anleitungen und Beispiele finden Sie unter [Erstellen und Verwalten von Metrikwarnungen](./alerts-metric.md). Befolgen Sie insbesondere für Metrikwarnungen für Protokolle die Anweisungen zur Verwaltung von Metrikwarnungen, und stellen Sie Folgendes sicher:

- Ziel der Metrikwarnung ist ein gültiger *Log Analytics-Arbeitsbereich*.
- Das für die Metrikwarnung für den ausgewählten *Log Analytics-Arbeitsbereich* gewählte Signal besitzt den Typ **Metrik**.
- Filter für bestimmte Bedingungen oder Ressourcen unter Verwendung von Dimensionsfiltern; Metriken für Protokolle sind mehrdimensional.
- Bei der Konfiguration der *Signallogik* kann eine einzelne Warnung erstellt werden, um mehrere Werte der Dimension (z.B. Computer) zu erfassen.
- Wenn **nicht** das Azure-Portal zum Erstellen einer Metrikwarnung für den ausgewählten *Log Analytics-Arbeitsbereich* verwendet wird, dann muss der Benutzer zunächst manuell eine explizite Regel zum Konvertieren von Protokolldaten in eine Metrik mit [Azure Monitor – Geplante Abfrageregeln](/rest/api/monitor/scheduledqueryrules) erstellen.

> [!NOTE]
> Bei der Erstellung von Metrikwarnungen für den Log Analytics-Arbeitsbereich über das Azure-Portal wird automatisch im Hintergrund eine entsprechende Regel für die Konvertierung von Protokolldaten in Metrik über [Azure Monitor – Geplante Abfrageregeln](/rest/api/monitor/scheduledqueryrules) erstellt, *ohne dass ein Benutzereingriff oder eine Aktion erforderlich ist*. Für Metrikwarnungen zum Erstellen von Protokollen mit anderen Mitteln als dem Azure-Portal finden Sie weitere Informationen im Abschnitt [Ressourcenvorlage für Metrikwarnungen für Protokolle](#resource-template-for-metric-alerts-for-logs) über Beispielmittel zum Erstellen eines auf „ScheduledQueryRule“ basierenden Protokolls zur Metrikkonvertierungsregel vor der Erstellung von Metrikwarnungen, andernfalls gibt es keine Daten für den Metrikwarnung für erstellte Protokolle.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Ressourcenvorlage für Metrikwarnungen für Protokolle

Wie bereits erwähnt, ist der Prozess zum Erstellen von Metrikwarnungen aus Protokollen zweigliedrig:

1. Erstellen einer Regel zum Extrahieren von Metriken aus unterstützten Protokollen mithilfe der scheduledQueryRule-API
2. Erstellen einer Metrikwarnung für die aus dem Protokoll (in Schritt 1) und dem Log Analytics-Arbeitsbereich als Zielressource extrahierte Metrik.

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Metrikwarnungen für Protokolle mit statischem Schwellenwert

Um dasselbe zu erreichen, kann die untenstehende Azure Resource Manager-Beispielvorlage verwendet werden, wobei die Erstellung einer Metrikwarnung mit statischem Schwellenwert von der erfolgreichen Erstellung der Regel für die Extraktion von Metriken aus Protokollen über „scheduledQueryRule“ abhängt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Angenommen, das obige JSON wird als „metricfromLogsAlertStatic.json“ gespeichert, dann kann es mit einer JSON-Parameterdatei für die ressourcenvorlagenbasierte Erstellung gekoppelt werden. Eine JSON-Beispieldatei ist nachfolgend aufgeführt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Angenommen, die obige Parameterdatei wird als „metricfromLogsAlertStatic.parameters.json“ gespeichert. In dem Fall kann eine Metrikwarnung für Protokolle erstellt werden, die eine [Ressourcenvorlage für die Erstellung im Azure Portal](../../azure-resource-manager/templates/deploy-portal.md) verwendet.

Alternativ kann auch der folgende Azure PowerShell-Befehl verwendet werden:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Oder verwenden Sie die Bereitstellung von Ressourcenvorlagen mithilfe der Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Metrikwarnungen für Protokolle mit dynamischen Schwellenwerten

Um dasselbe zu erreichen, kann die untenstehende Azure Resource Manager-Beispielvorlage verwendet werden, wobei die Erstellung einer Metrikwarnung mit dynamischem Schwellenwert von der erfolgreichen Erstellung der Regel für die Extraktion von Metriken aus Protokollen über „scheduledQueryRule“ abhängt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Angenommen, das obige JSON wird als „metricfromLogsAlertDynamic.json“ gespeichert, dann kann es mit einer JSON-Parameterdatei für die ressourcenvorlagenbasierte Erstellung gekoppelt werden. Eine JSON-Beispieldatei ist nachfolgend aufgeführt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Angenommen, die obige Parameterdatei wird als „metricfromLogsAlertDynamic.parameters.json“ gespeichert. In dem Fall kann eine Metrikwarnung für Protokolle erstellt werden, die eine [Ressourcenvorlage für die Erstellung im Azure Portal](../../azure-resource-manager/templates/deploy-portal.md) verwendet.

Alternativ kann auch der folgende Azure PowerShell-Befehl verwendet werden:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Oder verwenden Sie die Bereitstellung von Ressourcenvorlagen mithilfe der Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Metrikwarnungen](alerts-metric.md).
- Erfahren Sie mehr über [Protokollwarnungen in Azure](./alerts-unified-log.md).
- Erfahren Sie mehr über [Warnungen in Azure](alerts-overview.md).