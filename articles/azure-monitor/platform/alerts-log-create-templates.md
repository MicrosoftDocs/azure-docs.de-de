---
title: Erstellen einer Protokollwarnung anhand einer Azure Resource Manager-Vorlage | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit einer Resource Manager-Vorlage eine Protokollwarnung erstellen.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 08/26/2020
ms.subservice: alerts
ms.openlocfilehash: 3fb0288d592af8bd23415b31ecf6a6e3448b92ba
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186030"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Erstellen einer Protokollwarnung anhand einer Resource Manager-Vorlage

Mithilfe von Protokollwarnungen können Benutzer eine [Log Analytics](../log-query/log-analytics-tutorial.md)-Abfrage verwenden, um Ressourcenprotokolle mit einer bestimmten Häufigkeit auszuwerten und basierend auf den Ergebnissen eine Warnung auszulösen. Durch Regeln können über [Aktionsgruppen](./action-groups.md) einzelne oder mehrere Aktionen ausgelöst werden. Weitere Informationen zu Funktionen und zur Terminologie von Protokollwarnungen finden Sie [hier](alerts-unified-log.md).

In diesem Artikel erfahren Sie, wie Sie mit [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) [Protokollwarnungen](alerts-unified-log.md) in Azure Monitor konfigurieren können. Mit Resource Manager-Vorlagen können Sie programmgesteuert konsistent und reproduzierbar Ihre Umgebungen übergreifende Warnungen einrichten. Protokollwarnungen werden im Ressourcenanbieter `Microsoft.Insights/scheduledQueryRules` erstellt. Weitere Informationen finden Sie in der API-Referenz für die [API für Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/).

Die grundlegenden Schritte lauten wie folgt:

1. Verwenden Sie eine der folgenden Vorlagen als JSON-Datei, die die Erstellung der Warnung beschreibt.
2. Bearbeiten und verwenden Sie die entsprechende Parameterdatei als JSON-Datei zum Anpassen der Warnung.
4. Stellen Sie die Vorlage mithilfe einer beliebigen Bereitstellungsmethode bereit.

> [!NOTE]
> Protokolldaten aus einem [Log Analytics-Arbeitsbereich](../log-query/log-analytics-tutorial.md) können an den Azure Monitor-Metrikspeicher gesendet werden. Metrikwarnungen weisen ein [anderes Verhalten](alerts-metric-overview.md) auf. Je nach den Daten, mit denen Sie arbeiten, kann dies wünschenswert sein. Informationen zu den Protokollen, die Sie an Metriken weiterleiten können, und der zugehörigen Vorgehensweise finden Sie unter [Metrikwarnungen für Protokolle](alerts-metric-logs.md).

> [!NOTE]
> Protokollwarnungen für Log Analytics wurden bisher über die ältere [Log Analytics-Warnungs-API](api-alerts.md) und über Legacyvorlagen von [gespeicherten Log Analytics-Suchen und -Warnungen](../insights/solutions.md) verwaltet. [Informieren Sie sich über den Wechsel zur aktuellen ScheduledQueryRules-API](alerts-log-api-switch.md).


## <a name="simple-template-up-to-api-version-2018-04-16"></a>Einfache Vorlage (bis API-Version 2018-04-16)

Die folgende Vorlage für die [Erstellung von Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/createorupdate) basiert auf der [Anzahl von Ergebnisprotokollwarnungen](alerts-unified-log.md#count-of-the-results-table-rows). Sie enthält Beispieldaten als Variablen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

Dieser JSON-Code kann mit dem [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) gespeichert und bereitgestellt werden.

## <a name="template-with-cross-resource-query-up-to-api-version-2018-04-16"></a>Vorlage mit ressourcenübergreifender Abfrage (bis API-Version 2018-04-16)

Die folgende Vorlage für die [Erstellung von Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/createorupdate), die auf der [Messung von Metriken](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) basiert, führt eine [ressourcenübergreifende](../log-query/cross-workspace-query.md) Abfrage aus. Sie enthält Beispieldaten als Variablen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}
```

> [!IMPORTANT]
> Bei Verwendung einer ressourcenübergreifenden Abfrage in der Protokollwarnung ist die Verwendung von [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) zwingend, und der Benutzer muss Zugriff auf die Liste der angegebenen Ressourcen besitzen.

Dieser JSON-Code kann mit dem [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) gespeichert und bereitgestellt werden.

## <a name="template-for-all-resource-types-from-api-version-2020-05-01-preview"></a>Vorlage für alle Ressourcentypen (ab API-Version 2020-05-01-preview)

Die folgende Vorlage für die [Erstellung von Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/createorupdate) ist für alle Ressourcentypen geeignet. Sie enthält Beispieldaten als Variablen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "location": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Location of the alert"
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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "query": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricMeasureColumn": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the measure column used in the alert evaluation."
            }
        },
        "resourceIdColumn": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the resource ID column used in the alert targeting the alerts."
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
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "muteActionsDuration": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Mute actions for the chosen period of time (in ISO 8601 duration format) after the alert is fired."
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
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "location": "[parameters('location')]",
            "apiVersion": "2020-05-01-preview",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "allOf": [
                        {
                            "query": "[parameters('query')]",
                            "metricMeasureColumn": "[parameters('metricMeasureColumn')]",
                            "resourceIdColumn": "[parameters('resourceIdColumn')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            }
                        }
                    ]
                },
                "muteActionsDuration": "[parameters('muteActionsDuration')]",
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

Dieser JSON-Code kann mit dem [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) gespeichert und bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Protokollwarnungen](./alerts-unified-log.md).
* Informieren Sie sich über das [Verwalten von Protokollwarnungen](./alerts-log.md).
* Machen Sie sich mit [Webhookaktionen für Protokollwarnungen](./alerts-log-webhook.md) vertraut.
* Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).