---
title: Aktivieren der VM-Erweiterung mithilfe einer Azure Resource Manager-Vorlage
description: In diesem Artikel wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage VM-Erweiterungen auf Azure Arc-fähigen Servern bereitstellen, die in Hybrid Cloud-Umgebungen ausgeführt werden.
ms.date: 02/03/2021
ms.topic: conceptual
ms.openlocfilehash: cfba14ac30553178bd509d0b0e7ba9c60332d299
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493327"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe einer ARM-Vorlage

In diesem Artikel erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um Azure-VM-Erweiterungen bereitzustellen, die von Azure Arc-fähigen Servern unterstützt werden.

VM-Erweiterungen können einer Azure Resource Manager-Vorlage hinzugefügt und mit der Bereitstellung der Vorlage ausgeführt werden. Mit den von Azure Arc-fähigen Servern unterstützten VM-Erweiterungen können Sie die unterstützte VM-Erweiterung mithilfe von Azure PowerShell auf Linux- oder Windows-Computern bereitstellen. Jedes Beispiel unten beinhaltet eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

>[!NOTE]
>Zwar können mehrere Erweiterungen in einem Batch zusammengefasst und verarbeitet werden, die Installation erfolgt jedoch nacheinander. Sobald die Installation der ersten Erweiterung abgeschlossen ist, wird die Installation der nächsten Erweiterung versucht.

## <a name="deploy-the-log-analytics-vm-extension"></a>Bereitstellen der Log Analytics-VM-Erweiterung

Um den Log Analytics-Agent komfortabel bereitzustellen, steht das folgende Beispiel zum Installieren des Agents wahlweise unter Windows oder Linux zur Verfügung.

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Speichern Sie die Vorlagen- und Parameterdateien auf dem Datenträger, und bearbeiten Sie die Parameterdatei mit den passenden Werten für Ihre Bereitstellung. Anschließend können Sie mit dem folgenden Befehl die Erweiterung auf allen verbundenen Computern innerhalb einer Ressourcengruppe installieren. Der Befehl verwendet den *TemplateFile*-Parameter zum Angeben der Vorlage und den *TemplateParameterFile*-Parameter zum Angeben einer Datei, die Parameter und Parameterwerte enthält.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Bereitstellen der Erweiterung für benutzerdefinierte Skripts

Zum Verwenden der Erweiterung für benutzerdefinierte Skripts steht das folgende Beispiel für die Ausführung unter Windows oder Linux zur Verfügung. Wenn Sie mit der Erweiterung für benutzerdefinierte Skripts nicht vertraut sind, lesen Sie [Erweiterung für benutzerdefinierte Skripts für Windows](../../virtual-machines/extensions/custom-script-windows.md) oder [Erweiterung für benutzerdefinierte Skripts für Linux](../../virtual-machines/extensions/custom-script-linux.md). Es gibt eine Reihe abweichender Merkmale, die Sie kennen sollten, wenn Sie diese Erweiterung für Hybridcomputer verwenden:

* Die Liste der unterstützten Betriebssysteme für die Azure VM-Erweiterung für benutzerdefinierte Skripts trifft auf Azure Arc-fähige Server nicht zu. Die Liste der unterstützten Betriebssysteme für Arc-fähige Server finden Sie [hier](agent-overview.md#supported-operating-systems).

* Konfigurationsdetails, die Azure-VM-Skalierungsgruppen oder klassische VMs betreffen, sind nicht anwendbar.

* Wenn Ihre Computer ein Skript extern herunterladen müssen und nur über einen Proxyserver Daten austauschen können, müssen Sie den [Connected Machine-Agent so konfigurieren](manage-agent.md#update-or-remove-proxy-settings), dass er die Umgebungsvariable des Proxyservers festlegt.

In der Konfiguration der benutzerdefinierten Skripterweiterung werden Aspekte wie der Skriptspeicherort und der auszuführende Befehl angegeben. Diese Konfiguration ist in einer Azure Resource Manager-Vorlage angegeben, die unten sowohl für Linux- als auch für Windows-Hybridcomputer zur Verfügung steht.

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-powershell-dsc-extension"></a>Bereitstellen der PowerShell DSC-Erweiterung

Zum Verwenden der PowerShell DSC-Erweiterung steht das folgende Beispiel für die Ausführung unter Windows oder Linux zur Verfügung. Wenn Sie mit der PowerShell DSC-Erweiterung nicht vertraut sind, informieren Sie sich in der [Übersicht zum DSC-Erweiterungshandler](../../virtual-machines/extensions/dsc-overview.md). Es gibt eine Reihe abweichender Merkmale, die Sie kennen sollten, wenn Sie diese Erweiterung für Hybridcomputer verwenden:

* Die Liste der unterstützten Betriebssysteme für die Azure VM PowerShell DSC-Erweiterung trifft auf Azure Arc-fähige Server nicht zu. Die Liste der unterstützten Betriebssysteme für Arc-fähige Server finden Sie [hier](agent-overview.md#supported-operating-systems).

* Wenn Ihre Computer ein Skript extern herunterladen müssen und nur über einen Proxyserver Daten austauschen können, müssen Sie den [Connected Machine-Agent so konfigurieren](manage-agent.md#update-or-remove-proxy-settings), dass er die Umgebungsvariable des Proxyservers festlegt.

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>Bereitstellen der Dependency-Agent-Erweiterung

Zum Verwenden der Azure Monitor Dependency-Agent-Erweiterung steht das folgende Beisiel für Windows und Linux zur Verfügung. Wenn Sie mit dem Dependency-Agent nicht vertraut sind, informieren Sie sich in der [Übersicht zu Azure Monitor-Agents](../../azure-monitor/platform/agents-overview.md#dependency-agent).

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Bereitstellen der Azure Key Vault-VM-Erweiterung (Vorschau)

Im folgenden JSON-Code ist das Schema für die Key Vault-VM-Erweiterung (Vorschau) dargestellt. Für die Erweiterung sind keine geschützten Einstellungen erforderlich. Alle Einstellungen werden als öffentliche Informationen betrachtet. Für die Erweiterung werden eine Liste mit berücksichtigten Zertifikaten, die Abrufhäufigkeit und ein Zielzertifikatspeicher benötigt. Dies gilt insbesondere in folgenden Fällen:

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          }
      }
     }
}
```

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
     }
}
```

> [!NOTE]
> Die URLs der berücksichtigten Zertifikate müssen das folgende Format haben: `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Der Grund: Der Pfad `/secrets` gibt das vollständige Zertifikat einschließlich des privaten Schlüssels zurück, der Pfad `/certificates` dagegen nicht. Weitere Informationen zu Zertifikaten finden Sie hier: [Key Vault-Zertifikate](../../key-vault/general/about-keys-secrets-certificates.md)

Speichern Sie die Vorlagendatei auf dem Datenträger. Anschließend können Sie mit dem folgenden Befehl die Erweiterung auf allen verbundenen Computern innerhalb einer Ressourcengruppe installieren.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Bereitstellen des in Azure Defender integrierten Scanners

Zum Verwenden der Erweiterung für den in Azure Defender integrierten Scanner steht das folgende Beispiel für Windows und Linux zur Verfügung. Wenn Sie nicht mit dem integrierten Scanner vertraut sind, finden Sie in der [Übersicht über die Lösung zur Sicherheitsrisikobewertung von Azure Defender](../../security-center/deploy-vulnerability-assessment-vm.md) für Hybridcomputer weitere Informationen.

### <a name="template-file-for-windows"></a>Vorlagendatei für Windows

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Vorlagendatei für Linux

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

Speichern Sie die Vorlagendatei auf dem Datenträger. Anschließend können Sie mit dem folgenden Befehl die Erweiterung auf allen verbundenen Computern innerhalb einer Ressourcengruppe installieren.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Nächste Schritte

* Sie können VM-Erweiterungen über [Azure PowerShell](manage-vm-extensions-powershell.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md) bereitstellen, verwalten und entfernen.

* Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).