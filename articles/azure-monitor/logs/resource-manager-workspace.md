---
title: Beispiele für Resource Manager-Vorlagen für Log Analytics-Arbeitsbereiche
description: Hier finden Sie Beispiele für Azure Resource Manager-Vorlagen zum Bereitstellen von Log Analytics-Arbeitsbereichen und Konfigurieren von Datenquellen in Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: f57ff130fcf61ae8f831cda33c9510b37a5880e5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597869"
---
# <a name="resource-manager-template-samples-for-log-analytics-workspaces-in-azure-monitor"></a>Beispiele für Resource Manager-Vorlagen für Log Analytics-Arbeitsbereiche in Azure Monitor
Dieser Artikel enthält Beispiele für [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) zum Erstellen und Konfigurieren von Log Analytics-Arbeitsbereichen in Azure Monitor. Jedes Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Vorlagenreferenzen

- [Microsoft.OperationalInsights workspaces](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces
) 
- [Microsoft.OperationalInsights workspaces/dataSources](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/datasources)

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
Im folgenden Beispiel wird ein neuer leerer Log Analytics-Arbeitsbereich erstellt.

### <a name="notes"></a>Hinweise

- Wenn Sie als Tarif **Free** angeben, entfernen Sie das Element **retentionInDays**.

### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "sku": {
          "type": "string",
          "allowedValues": [
            "pergb2018",
            "Free",
            "Standalone",
            "PerNode",
            "Standard",
            "Premium"
            ],
          "defaultValue": "pergb2018",
          "metadata": {
          "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
          "australiacentral", 
          "australiaeast", 
          "australiasoutheast", 
          "brazilsouth",
          "canadacentral", 
          "centralindia", 
          "centralus", 
          "eastasia", 
          "eastus", 
          "eastus2", 
          "francecentral", 
          "japaneast", 
          "koreacentral", 
          "northcentralus", 
          "northeurope", 
          "southafricanorth", 
          "southcentralus", 
          "southeastasia",
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
      }

      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-08-01",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "sku": {
      "value": "pergb2018"
    },
    "location": {
      "value": "eastus"
    },
    "resourcePermissions": {
      "value": true
    }
  }
}
```

## <a name="collect-windows-events"></a>Erfassen von Windows-Ereignissen
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung von [Windows-Ereignissen](../agents/data-sources-windows-events.md) hinzugefügt.

### <a name="notes"></a>Hinweise

- Fügen Sie für jedes zu erfassende Ereignisprotokoll ein **datasources**-Element hinzu. Sie können für jedes Protokoll eine andere Gruppe von Ereignistypen angeben.

### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
  {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsSystem",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "System",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsApplication",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              },
              {
                "eventType": "Information"
              }
            ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-syslog"></a>Erfassen von Syslog-Ereignissen
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung von [Syslog-Ereignissen](../agents/data-sources-syslog.md) hinzugefügt.

### <a name="notes"></a>Hinweise

- Fügen Sie für jede zu erfassende Einrichtung ein **datasources**-Element hinzu. Sie können für jede Einrichtung eine andere Gruppe von Schweregraden angeben.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogKern",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "kern",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        },
                        {
                            "severity": "notice"
                        },
                        {
                            "severity": "info"
                        },
                        {
                            "severity": "debug"
                        }
                    ]
                }
            },
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogDaemon",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "daemon",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "SyslogCollection",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslogCollection",
                "properties": {
                    "state": "Enabled"
                }
            }
        ]
      }
    ]
}

```


### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-windows-performance-counters"></a>Erfassen von Windows-Leistungsindikatoren
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung von [Windows-Leistungsindikatoren](../agents/data-sources-performance-counters.md) hinzugefügt.

### <a name="notes"></a>Hinweise

- Fügen Sie für jeden zu erfassenden Leistungsindikator und jede Instanz ein **datasources**-Element hinzu. Sie können für jede Kombination aus Leistungsindikator und Instanz unterschiedliche Erfassungsraten angeben.
  
### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Location of the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryAvailableBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "Available MBytes "
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryPercentageBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "% Committed Bytes in Use"
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfProcessorPercentage",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Processor",
              "instanceName": "_Total",
              "intervalSeconds": 10,
              "counterName": "% Processor Time"
            }
          }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-linux-performance-counters"></a>Erfassen von Linux-Leistungsindikatoren
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung von [Linux-Leistungsindikatoren](../agents/data-sources-performance-counters.md) hinzugefügt.

### <a name="notes"></a>Hinweise

- Fügen Sie für jedes zu erfassende Objekt und jede Instanz ein **datasources**-Element hinzu. Sie können für jede Kombination aus Objekt und Instanz eine andere Gruppe von Indikatoren angeben, aber Sie können für alle Indikatoren nur eine einzige Rate angeben.
  
### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceLogicalDisk",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Logical Disk",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Used Inodes"
                        },
                        {
                            "counterName": "Free Megabytes"
                        },
                        {
                            "counterName": "% Used Space"
                        },
                        {
                            "counterName": "Disk Transfers/sec"
                        },
                        {
                            "counterName": "Disk Reads/sec"
                        },
                        {
                            "counterName": "Disk Writes/sec"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceProcessor",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Processor",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Processor Time"
                        },
                        {
                            "counterName": "% Privileged Time"
                        }
                    ]
                }
            }
        ]
      }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-custom-logs"></a>Erfassen benutzerdefinierter Protokolle
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung [benutzerdefinierter Protokolle](../agents/data-sources-custom-logs.md) hinzugefügt.

### <a name="notes"></a>Hinweise

- Die Konfiguration von Trennzeichen und Extraktionen kann komplex sein. Zur Unterstützung können Sie ein benutzerdefiniertes Protokoll mithilfe des Azure-Portal definieren und dann dessen Konfiguration mithilfe von [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) abrufen, wobei **-Kind** auf **CustomLog** festgelegt ist.

  
### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Specifies the location in which to create the workspace."
        }
      }
  },
  "resources": [
  {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "apiVersion": "2020-08-01",
            "type": "dataSources",
            "name": "[concat(parameters('workspaceName'), 'armlog_timedelimited')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
            ],
            "kind": "CustomLog",
            "properties": {
                "customLogName": "arm_log_timedelimited",
                "description": "this is a description",
                "inputs": [
                  {
                      "location": {
                        "fileSystemLocations": {
                            "linuxFileTypeLogPaths": [ "/var/logs" ],
                            "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                        }
                      },
                      "recordDelimiter": {
                        "regexDelimiter": {
                          "matchIndex": 0,
                          "numberdGroup": null,
                          "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                        }
                      }
                  }
                ],
                "extractions": [
                {
                    "extractionName": "TimeGenerated",
                    "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": [
                          {
                              "matchIndex": 0,
                              "numberdGroup": null,
                              "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                          }
                        ]
                    }
                    },
                    "extractionType": "DateTime"
                }
                ]
            }
        },
        {
          "apiVersion": "2020-08-01",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), 'armlog_newline')]",
          "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
              "customLogName": "armlog_newline",
              "description": "this is a description",
              "inputs": [
                {
                    "location": {
                      "fileSystemLocations": {
                          "linuxFileTypeLogPaths": [ "/var/logs" ],
                          "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                      }
                    },
                    "recordDelimiter": {
                      "regexDelimiter": {
                        "pattern": "\\n",
                        "matchIndex": 0,
                        "numberdGroup": null
                      }
                    }
                }
              ],
              "extractions": [
                {
                  "extractionName": "TimeGenerated",
                  "extractionType": "DateTime",
                  "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": null,
                        "joinStringRegex": null
                    }
                  }
                }
              ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-iis-log"></a>Erfassen von IIS-Protokollen
Im folgenden Beispiel wird einem vorhandenen Arbeitsbereich die Erfassung von [IIS-Protokollen](../agents/data-sources-iis-logs.md) hinzugefügt.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "type": "Microsoft.OperationalInsights/workspaces",
        "apiVersion": "2020-08-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "IISLog",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "IISLogs",
                "properties": {
                    "state": "OnPremiseEnabled"
                }
            }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```



## <a name="next-steps"></a>Nächste Schritte

* [Rufen Sie weitere Beispielvorlagen für Azure Monitor ab](../samples/resource-manager-samples.md).
* [Erfahren Sie mehr Log Analytics-Arbeitsbereiche](../learn/quick-create-workspace.md).
* [Erfahren Sie mehr über Agent-Datenquellen](../agents/agent-data-sources.md).
