---
title: Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen
description: Hier finden Sie Beispiele für Azure Resource Manager-Vorlagen zum Anwenden von Azure Monitor-Diagnoseeinstellungen auf eine Azure-Ressource.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/11/2020
ms.openlocfilehash: 87951c0366090c1ea4eba490d114771e47bf452c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597575"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen in Azure Monitor
Dieser Artikel enthält Beispiele für [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) zum Erstellen von Diagnoseeinstellungen für eine Azure-Ressource. Jedes Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

Zum Erstellen einer Diagnoseeinstellung für eine Azure-Ressource fügen Sie der Vorlage eine Ressource vom Typ `<resource namespace>/providers/diagnosticSettings` hinzu. Dieser Artikel enthält Beispiele für einige Ressourcentypen, doch kann das gleiche Muster auch auf andere Ressourcentypen angewendet werden. Die Sammlung zulässiger Protokolle und Metriken variiert je nach Ressourcentyp.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>Diagnoseeinstellung für Aktivitätsprotokoll
Im folgenden Beispiel wird eine Diagnoseeinstellung für ein Aktivitätsprotokoll erstellt, indem der Vorlage eine Ressource vom Typ `Microsoft.Insights/diagnosticSettings` hinzugefügt wird.

> [!IMPORTANT]
> Diagnoseeinstellungen für Aktivitätsprotokolle werden für ein Abonnement erstellt und nicht für eine Ressourcengruppe wie Einstellungen für Azure-Ressourcen. Zum Bereitstellen der Resource Manager-Vorlage verwenden Sie `New-AzSubscriptionDeployment` für PowerShell oder `az deployment sub create` für die Azure CLI.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
          "type": "String"
        },
        "workspaceId": {
          "type": "String"
        },
        "storageAccountId": {
          "type": "String"
        },
        "eventHubAuthorizationRuleId": {
          "type": "String"
        },
        "eventHubName": {
          "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
        "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Diagnoseeinstellung für Azure Key Vault 
Im folgenden Beispiel wird eine Diagnoseeinstellung für eine Azure Key Vault-Instanz erstellt, indem der Vorlage eine Ressource vom Typ `Microsoft.KeyVault/vaults/providers/diagnosticSettings` hinzugefügt wird.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "AllMetrics",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Diagnoseeinstellung für Azure SQL-Datenbank
Im folgenden Beispiel wird eine Diagnoseeinstellung für eine Azure SQL-Datenbank erstellt, indem der Vorlage eine Ressource vom Typ `microsoft.sql/servers/databases/providers/diagnosticSettings` hinzugefügt wird.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-recovery-services-vault"></a>Diagnoseeinstellung für den Recovery Services-Tresor
Im folgenden Beispiel wird eine Diagnoseeinstellung für einen Azure Recovery Services-Tresor erstellt, indem der Vorlage eine Ressource vom Typ `microsoft.recoveryservices/vaults/providers/diagnosticSettings` hinzugefügt wird. In diesem Beispiel wird der Sammlungsmodus wie unter [Azure-Ressourcenprotokolle](../platform/resource-logs.md#send-to-log-analytics-workspace) beschrieben angegeben. Geben Sie `Dedicated` oder `AzureDiagnostics` für die Eigenschaft `logAnalyticsDestinationType` an.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "recoveryServicesName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "microsoft.recoveryservices/vaults/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('recoveryServicesName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "AzureBackupReport",
                        "enabled": false
                    },
                    {
                        "category": "CoreAzureBackup",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupJobs",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupAlerts",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupPolicy",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupStorage",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupProtectedInstance",
                        "enabled": true
                    },
                    {
                        "category": "AzureSiteRecoveryJobs",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryEvents",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicatedItems",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationStats",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryRecoveryPoints",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationDataUploadRate",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryProtectedDiskDataChurn",
                        "enabled": false
                    }
                ],
                "logAnalyticsDestinationType": "Dedicated"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "recoveryServicesName": {
        "value": "my-vault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-log-analytics-workspace"></a>Diagnoseeinstellung für den Log Analytics-Arbeitsbereich
Im folgenden Beispiel wird eine Diagnoseeinstellung für einen Log Analytics-Arbeitsbereichstresor erstellt, indem der Vorlage eine Ressource vom Typ `Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings` hinzugefügt wird. In diesem Beispiel werden Überwachungsdaten zu Abfragen, die im Arbeitsbereich ausgeführt werden, an denselben Arbeitsbereich gesendet.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('workspaceName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "Audit",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "workspaceName": {
        "value": "MyWorkspace"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-storage"></a>Diagnoseeinstellung für Azure Storage
Im folgenden Beispiel wird eine Diagnoseeinstellung für jeden Speicherdienstendpunkt erstellt, der im Speicherkonto verfügbar ist. Eine Einstellung wird auf jeden einzelnen Speicherdienst angewendet, der im Konto verfügbar ist. Welche Speicherdienste verfügbar sind, hängt vom Typ des Speicherkontos ab. Mit dieser Vorlage wird nur dann eine Diagnoseeinstellung für einen Speicherdienst im Konto erstellt, wenn er für das Konto vorhanden ist. Die Diagnoseeinstellung ermöglicht für jeden verfügbaren Dienst Transaktionsmetriken und die Erfassung von Ressourcenprotokollen für Lese-, Schreib- und Löschvorgänge.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        },
        "settingName": {
            "type": "string"
        },
        "storageSyncName": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-10-01",
            "name": "nested",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "parameters": {
                    "endpoints": {
                        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01', 'Full').properties.primaryEndpoints]"
                    },
                    "settingName": {
                        "value": "[parameters('settingName')]"
                    },
                    "storageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    },
                    "storageSyncName": {
                        "value": "[parameters('storageSyncName')]"
                    },
                    "workspaceId": {
                        "value": "[parameters('workspaceId')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "endpoints": {
                            "type": "object"
                        },
                        "settingName": {
                            "type": "String"
                        },
                        "storageAccountName": {
                            "type": "String"
                        },
                        "storageSyncName": {
                            "type": "String"
                        },
                        "workspaceId": {
                            "type": "String"
                        }
                    },
                    "variables": {
                        "hasblob": "[contains(parameters('endpoints'),'blob')]",
                        "hastable": "[contains(parameters('endpoints'),'table')]",
                        "hasfile": "[contains(parameters('endpoints'),'file')]",
                        "hasqueue": "[contains(parameters('endpoints'),'queue')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasblob')]",
                            "type": "Microsoft.Storage/storageAccounts/blobServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hastable')]",
                            "type": "Microsoft.Storage/storageAccounts/tableServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasfile')]",
                            "type": "Microsoft.Storage/storageAccounts/fileServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasqueue')]",
                            "type": "Microsoft.Storage/storageAccounts/queueServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountName": {
          "value": "mymonitoredstorageaccount"
      },
      "settingName": {
          "value": "Send to all locations"
      },
      "storageSyncName": {
          "value": "mystorageaccount"
      },
      "workspaceId": {
          "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      }
    }
  }
```

## <a name="next-steps"></a>Nächste Schritte

* [Rufen Sie weitere Beispielvorlagen für Azure Monitor ab](../samples/resource-manager-samples.md).
* [Erfahren Sie mehr über Diagnoseeinstellungen](../essentials/diagnostic-settings.md).
