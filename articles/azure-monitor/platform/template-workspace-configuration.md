---
title: Verwenden von Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs | Microsoft-Dokumentation
description: Sie können Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren von Log Analytics-Arbeitsbereichen verwenden.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: magoedte
ms.openlocfilehash: 0578b50952c12d4587f7a4751bc831d3134c64e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129428"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Verwalten von Log Analytics-Arbeitsbereichen mithilfe von Azure Resource Manager-Vorlagen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sie können [Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md) zum Erstellen und Konfigurieren von Log Analytics-Arbeitsbereichen in Azure Monitor verwenden. Beispiele für die Aufgaben, die Sie mit Vorlagen ausführen können:

* Erstellen eines Arbeitsbereichs und Festlegen des Tarifs 
* Hinzufügen einer Lösung
* Erstellen gespeicherter Suchvorgänge
* Erstellen einer Computergruppe
* Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
* Sammeln von Leistungsindikatoren von Windows- und Linux-Computern
* Sammeln von Ereignissen aus Syslog auf Linux-Computern 
* Sammeln von Ereignissen aus Windows-Ereignisprotokollen
* Hinzufügen des Log Analytics-Agents auf virtuellen Azure-Computern
* Konfiguration von Log Analytics zum Indizieren der Daten, die mit der Azure-Diagnose gesammelt werden

Dieser Artikel enthält Vorlagenbeispiele, die einen Teil der Konfiguration veranschaulichen, die Sie mit Vorlagen ausführen können.

## <a name="api-versions"></a>API-Versionen
Die folgende Tabelle enthält die API-Versionen für die Ressourcen, die in diesem Beispiel verwendet werden.

| Resource | Ressourcentyp | API-Version |
|:---|:---|:---|
| Arbeitsbereich   | workspaces    | 2017-03-15-preview |
| Suchen,      | savedSearches | 2015-03-20 |
| Datenquelle | datasources   | 2015-11-01-preview |
| Lösung    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
Im folgenden Beispiel wird mithilfe einer Vorlage von Ihrem lokalen Computer ein Arbeitsbereich erstellt. Die JSON-Vorlage ist so konfiguriert, dass Sie nur zur Eingabe des Namens für den Arbeitsbereich aufgefordert werden und Standardwerte für die anderen Parameter angegeben werden, die wahrscheinlich als Standardkonfiguration in Ihrer Umgebung verwendet werden.  

Für folgende Parameter wird ein Standardwert festgelegt:

* Standort: Als Standardwert wird „USA, Osten“ verwendet.
* SKU: Als Standardwert wird der neue im Preismodell von April 2018 veröffentlichte Tarif pro GB verwendet.

> [!NOTE]
>Wenn Sie einen Log Analytics-Arbeitsbereich in einem Abonnement mit dem neuen Preismodell von April 2018 erstellen oder konfigurieren, ist **PerGB2018** als einziger gültiger Log Analytics-Tarif verfügbar.  
>Wenn Sie über Abonnements mit dem [Preismodell vor April 2018](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#new-pricing-model) verfügen, können Sie den Tarif **Eigenständig** auswählen. Dieser Tarif gilt dann für Abonnements mit dem Preismodell vor April 2018 sowie für Abonnements mit dem neuen Preismodell. Für Arbeitsbereiche in Abonnements, für die das neue Preismodell gewählt wurde, wird der Tarif auf **PerGB2018** festgelegt. 

### <a name="create-and-deploy-template"></a>Erstellen und Bereitstellen der Vorlage

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. Bearbeiten Sie die Vorlage entsprechend Ihren Anforderungen.  Informationen zu den unterstützten Eigenschaften und Werten finden Sie in der Referenz [Microsoft.OperationalInsights/workspaces template](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces). 
3. Speichern Sie diese Datei unter dem Namen **deploylaworkspacetemplate.json** in einem lokalen Ordner.
4. Nun können Sie die Vorlage bereitstellen. Den Arbeitsbereich können Sie über PowerShell oder die Befehlszeile erstellen.

   * Führen Sie bei Verwendung von PowerShell die folgenden Befehle in dem Ordner mit der Vorlage aus:
   
        ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * Führen Sie bei Verwendung der Befehlszeile die folgenden Befehle in dem Ordner mit der Vorlage aus:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

Die Bereitstellung kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung ähnlich der folgenden mit dem Ergebnis angezeigt:<br><br> ![Beispielergebnis nach abgeschlossener Bereitstellung](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurieren eines Log Analytics-Arbeitsbereichs
Das folgende Vorlagenbeispiel veranschaulicht Folgendes:

1. Hinzufügen von Lösungen zum Arbeitsbereich
2. Erstellen gespeicherter Suchvorgänge
3. Erstellen einer Computergruppe
4. Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
5. Sammeln von Leistungsindikatoren logischer Datenträger von Linux-Computern (Prozentsatz verwendeter I-Knoten, MB frei, Prozentsatz des verwendeten Speicherplatzes, Übertragungen/s, Lesevorgänge/s, Schreibvorgänge/s)
6. Sammeln von Syslog-Ereignissen auf Linux-Computern
7. Sammeln von Fehler- und Warnereignissen aus dem Anwendungsereignisprotokoll von Windows-Computern
8. Erfassen des Leistungsindikators „Verfügbarer Arbeitsspeicher in MB“ von Windows-Computern
9. Sammeln von IIS-Protokollen und Windows-Ereignisprotokollen, die von der Azure-Diagnose in ein Speicherkonto geschrieben werden

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and Log Analytics plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-11-01-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
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
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
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
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
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
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Bereitstellen der Beispielvorlage
So stellen Sie die Beispielvorlage bereit

1. Speichern Sie das angefügte Beispiel in einer Datei, z.B. `azuredeploy.json`. 
2. Bearbeiten Sie die Vorlage, um die gewünschte Konfiguration zu erstellen.
3. Verwenden Sie PowerShell oder die Befehlszeile zum Bereitstellen der Vorlage.

#### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Befehlszeile
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Resource Manager-Beispielvorlagen
Der Katalog mit Azure-Schnellstartvorlagen enthält u.a. folgende Vorlagen für Log Analytics:

* [Bereitstellen eines virtuellen Computers unter Windows mit der Log Analytics-VM-Erweiterung](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Bereitstellen eines virtuellen Computers unter Linux mit der Log Analytics-VM-Erweiterung](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Überwachen von Azure Site Recovery mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Überwachen von Azure-Web-Apps mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Hinzufügen eines vorhandenen Speicherkontos zu Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen des Windows-Agents für virtuelle Azure-Computer mithilfe einer Resource Manager-Vorlage](../../virtual-machines/extensions/oms-windows.md).
* [Bereitstellen des Linux-Agents für virtuelle Azure-Computer mithilfe einer Resource Manager-Vorlage](../../virtual-machines/extensions/oms-linux.md).

