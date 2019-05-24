---
title: Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für einen virtuellen Windows-Computer
description: Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für einen virtuellen Windows-Computer
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 5647802ff383ce046d108f25384df81bcbd08cd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129662"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für einen virtuellen Windows-Computer

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die [Diagnoseerweiterung](diagnostics-extension-overview.md) von Azure Monitor ermöglicht es Ihnen, Metriken und Protokolle von einem Gastbetriebssystem zu erfassen, das als Teil eines virtuellen Computers, eines Clouddiensts oder eines Service Fabric-Clusters ausgeführt wird. Die Erweiterung kann Telemetriedaten an [viele verschiedene Standorte](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) senden.

In diesem Artikel wird der Prozess zum Senden von Leistungsmetriken des Gastbetriebssystems für einen virtuellen Windows-Computer an den Azure Monitor-Datenspeicher beschrieben. Ab Version 1.11 der Diagnoseerweiterung können Sie Metriken direkt in den Azure Monitor-Metrikspeicher schreiben, in dem bereits Metriken der Standardplattformen gesammelt werden.

Wenn Sie diesen Speicherort verwenden möchten, können Sie die gleichen Aktionen wie für Plattformmetriken nutzen. Zu den Aktionen gehören Benachrichtigung nahezu in Echtzeit, Diagrammerstellung, Routing, Zugriff über eine REST-API und vieles mehr. In der Vergangenheit hat die Diagnoseerweiterung in Azure Storage geschrieben, aber nicht in den Azure Monitor-Datenspeicher.

Wenn Sie noch nicht mit Resource Manager-Vorlagen vertraut sind, informieren Sie sich über [Vorlagenbereitstellungen](../../azure-resource-manager/resource-group-overview.md) sowie deren Struktur und Syntax.

## <a name="prerequisites"></a>Voraussetzungen

- Ihr Abonnement muss mit [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) registriert werden.

- [Azure PowerShell](/powershell/azure) oder [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) muss installiert sein.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Einrichten von Azure Monitor als Datensenke
Die Azure-Diagnoseerweiterung verwendet eine Funktion namens „Datensenken“, um Metriken und Protokolle an verschiedene Standorte zu leiten. Die folgenden Schritte zeigen, wie Sie eine Resource Manager-Vorlage und PowerShell verwenden, um eine VM mit der neuen Datensenke „Azure Monitor“ bereitzustellen.

## <a name="author-resource-manager-template"></a>Erstellen von Resource Manager-Vorlagen
Für dieses Beispiel können Sie eine öffentlich zugängliche Beispielvorlage verwenden. Die Startvorlagen befinden sich unter https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.json** ist eine vorkonfigurierte Resource Manager-Vorlage für die Bereitstellung eines virtuellen Computers.

- **Azuredeploy.parameters.json** ist eine Parameterdatei, die Informationen wie Benutzername und Kennwort speichert, die Sie für Ihre VM festlegen. Während der Bereitstellung verwendet die Resource Manager-Vorlage die in dieser Datei festgelegten Parameter.

Laden Sie beide Dateien herunter und speichern Sie sie lokal.

### <a name="modify-azuredeployparametersjson"></a>Ändern von azuredeploy.parameters.json
Öffnen Sie die Datei *azuredeploy.parameters.json*.

1. Geben Sie Werte für **adminUsername** und **adminPassword** für die VM ein. Diese Parameter werden für den Remotezugriff auf die VMs verwendet. Verwenden Sie NICHT die in dieser Vorlage enthaltenen Werte, um eine Übernahme Ihrer VM zu vermeiden. Bots durchsuchen das Internet nach Benutzernamen und Kennwörtern in öffentlichen GitHub-Repositorys. Es ist wahrscheinlich, dass sie VMs mit diesen Standardwerten testen.

1. Erstellen Sie einen eindeutigen DNS-Namen für die VM.

### <a name="modify-azuredeployjson"></a>Ändern von azuredeploy.json

Öffnen Sie die Datei *azuredeploy.json*.

Fügen Sie dem Abschnitt **variables** der Vorlage hinter dem Eintrag für **storageAccountName** eine Speicherkonto-ID hinzu.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Fügen Sie der Vorlage diese MSI-Erweiterung (verwaltete Dienstidentität) oben im Abschnitt **resources** hinzu. Die Erweiterung stellt sicher, dass Azure Monitor die ausgegebenen Metriken akzeptiert.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "WADExtensionSetup",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Fügen Sie der VM-Ressource die Konfiguration **identity** hinzu, um sicherzustellen, dass Azure der MSI-Erweiterung eine Systemidentität zuweist. Dieser Schritt stellt sicher, dass der virtuelle Computer Gastmetriken über sich selbst an Azure Monitor senden kann.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Fügen Sie die folgende Konfiguration hinzu, um die Diagnoseerweiterung auf einem virtuellen Windows-Computer zu aktivieren. Bei einem einfachen, auf Resource Manager basierenden virtuellen Computer kann die Erweiterungskonfiguration dem Array „resources“ für den virtuellen Computer hinzugefügt werden. Die Zeile „sinks“ – „AzMonSink“ und das später im Abschnitt folgende entsprechende „SinksConfig“ – ermöglichen es der Erweiterung, Metriken direkt an Azure Monitor zu senden. Sie können Leistungsindikatoren nach Bedarf hinzufügen oder entfernen.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Speichern und schließen Sie beide Dateien.


## <a name="deploy-the-resource-manager-template"></a>Bereitstellen der Resource Manager-Vorlage

> [!NOTE]
> Sie müssen die Azure-Diagnoseerweiterung Version 1.5 oder höher ausführen UND die **autoUpgradeMinorVersion**-Eigenschaft in Ihrer Resource Manager-Vorlage auf TRUE festgelegt haben. Azure lädt dann die richtige Erweiterung, wenn die VM gestartet wird. Wenn diese Einstellungen in Ihrer Vorlage nicht vorhanden sind, ändern Sie sie, und stellen Sie die Vorlage erneut bereit.


Für die Bereitstellung der Resource Manager-Vorlage wird Azure PowerShell verwendet.

1. Starten Sie PowerShell.
1. Melden Sie sich mit `Login-AzAccount` bei Azure an.
1. Rufen Sie Ihre Liste der Abonnements mit `Get-AzSubscription` ab.
1. Legen Sie das Abonnement, das Sie zum Erstellen/Aktualisieren des virtuellen Computers verwenden, fest:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Erstellen Sie eine neue Ressourcengruppe für die bereitzustellende VM, indem Sie den folgenden Befehl ausführen:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Denken Sie daran, eine [Azure-Region zu verwenden, die für benutzerdefinierte Metriken aktiviert ist](metrics-custom-overview.md).

1. Führen Sie die folgenden Befehle aus, um den virtuellen Computer mithilfe der Resource Manager-Vorlage bereitzustellen.
   > [!NOTE]
   > Wenn Sie einen bestehenden virtuellen Computer aktualisieren möchten, fügen Sie einfach *-Mode Incremental* am Ende des folgenden Befehls hinzu.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Nachdem Ihre Bereitstellung erfolgreich abgeschlossen wurde, sollte der virtuelle Computer im Azure-Portal vorhanden sein und Metriken an Azure Monitor ausgeben.

   > [!NOTE]
   > Möglicherweise treten im Bereich um die vmSkuSize Fehler auf. Wechseln Sie in diesem Fall zurück zu Ihrer Datei „azuredeploy.json“, und aktualisieren Sie den Standardwert des Parameters vmSkuSize. In diesem Fall wird empfohlen, es mit „Standard_DS1_v2“ zu versuchen.

## <a name="chart-your-metrics"></a>Erfassen Ihrer Metriken

1. Melden Sie sich beim Azure-Portal an.

2. Wählen Sie im Menü auf der linken Seite **Überwachen** aus.

3. Wählen Sie auf der Seite „Überwachen“ die Option **Metriken** aus.

   ![Seite „Metriken“](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Ändern Sie den Aggregationszeitraum in **Letzte 30 Minuten**.

5. Wählen Sie im Dropdownmenü mit den Ressourcen den von Ihnen erstellten virtuellen Computer aus. Wenn Sie den Namen in der Vorlage nicht geändert haben, sollte er *SimpleWinVM2* lauten.

6. Wählen Sie im Dropdownmenü mit den Namespaces **azure.vm.windows.guest** aus.

7. Wählen Sie im Dropdownmenü mit den Metriken **Memory\%Committed Bytes in Use** aus.


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [benutzerdefinierte Metriken](metrics-custom-overview.md).

