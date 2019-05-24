---
title: Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Azure Resource Manager-Vorlage für eine VM-Skalierungsgruppe von Windows
description: Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für eine VM-Skalierungsgruppe von Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 573c205cd2e208a1cb2b526d96fb08ca21331c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129632"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Azure Resource Manager-Vorlage für eine VM-Skalierungsgruppe von Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die [WAD-Erweiterung](diagnostics-extension-overview.md) (Windows Azure-Diagnose) von Azure Monitor ermöglicht es Ihnen, Metriken und Protokolle von einem Gastbetriebssystem zu erfassen, das als Teil eines virtuellen Computers, eines Clouddiensts oder eines Azure Service Fabric-Clusters ausgeführt wird. Die Erweiterung kann Telemetriedaten an viele verschiedene Standorte senden, die im zuvor verlinkten Artikel aufgeführt sind.  

In diesem Artikel wird der Prozess zum Senden von Leistungsmetriken des Gastbetriebssystems für eine VM-Skalierungsgruppe von Windows an den Azure Monitor-Datenspeicher beschrieben. Ab Version 1.11 von Windows Azure-Diagnose können Sie Metriken direkt in den Azure Monitor-Metrikspeicher schreiben, in dem bereits Metriken der Standardplattformen gesammelt werden. Wenn Sie diesen Speicherort verwenden möchten, können Sie die gleichen Aktionen wie für Plattformmetriken nutzen. Zu den Aktionen gehören Benachrichtigung nahezu in Echtzeit, Diagrammerstellung, Routing, Zugriff über eine REST-API und vieles mehr. In der Vergangenheit hat die Windows Azure-Diagnoseerweiterung in Azure Storage geschrieben, aber nicht in den Azure Monitor-Datenspeicher.  

Wenn Sie noch nicht mit Resource Manager-Vorlagen vertraut sind, informieren Sie sich über [Vorlagenbereitstellungen](../../azure-resource-manager/resource-group-overview.md) sowie deren Struktur und Syntax.  

## <a name="prerequisites"></a>Voraussetzungen

- Ihr Abonnement muss mit [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) registriert werden. 

- Sie müssen [Azure PowerShell](/powershell/azure) installiert haben, oder Sie können [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) verwenden. 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Einrichten von Azure Monitor als Datensenke 
Die Azure-Diagnoseerweiterung verwendet eine Feature namens **Datensenken**, um Metriken und Protokolle an verschiedene Orte zu leiten. Die folgenden Schritte zeigen, wie Sie eine Resource Manager-Vorlage und PowerShell verwenden, um eine VM mit der neuen Datensenke „Azure Monitor“ bereitzustellen. 

## <a name="author-a-resource-manager-template"></a>Erstellen einer Resource Manager-Vorlage 
Für dieses Beispiel können Sie eine öffentlich verfügbare [Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale) verwenden:  

- **Azuredeploy.json** ist eine vorkonfigurierte Resource Manager-Vorlage für die Bereitstellung einer VM-Skalierungsgruppe.

- **Azuredeploy.parameters.json** ist eine Parameterdatei, die Informationen wie Benutzername und Kennwort speichert, die Sie für Ihre VM festlegen. Während der Bereitstellung verwendet die Resource Manager-Vorlage die in dieser Datei festgelegten Parameter. 

Laden Sie beide Dateien herunter und speichern Sie sie lokal. 

###  <a name="modify-azuredeployparametersjson"></a>Ändern von azuredeploy.parameters.json
Öffnen Sie die Datei **azuredeploy.parameters.json**:  
 
- Geben Sie eine **vmSKU** an, die Sie bereitstellen möchten. Empfehlung: Standard_D2_v3. 
- Geben Sie eine **windowsOSVersion** an, die Sie für Ihre VM-Skalierungsgruppe verwenden möchten. Empfehlung: 2016-Datacenter. 
- Benennen Sie die bereitzustellende VM-Skalierungsgruppe mithilfe einer **vmssName**-Eigenschaft. Beispiel: **VMSS-WAD-TEST**.    
- Geben Sie über die **instanceCount**-Eigenschaft die Anzahl der VMs an, die Sie in der VM-Skalierungsgruppe ausführen möchten.
- Geben Sie Werte für **adminUsername** und **adminPassword** für die VM-Skalierungsgruppe ein. Diese Parameter werden für den Remotezugriff auf die VMs in der Skalierungsgruppe verwendet. Verwenden Sie **nicht** die in dieser Vorlage enthaltenen Werte, um eine Übernahme Ihrer VM zu vermeiden. Bots durchsuchen das Internet nach Benutzernamen und Kennwörtern in öffentlichen GitHub-Repositorys. Es ist wahrscheinlich, dass sie VMs mit diesen Standardwerten testen. 


###  <a name="modify-azuredeployjson"></a>Ändern von azuredeploy.json
Öffnen Sie die Datei **azuredeploy.json**. 

Fügen Sie eine Variable hinzu, um die Informationen zum Speicherkonto in der Resource Manager-Vorlage zu speichern. Alle Protokolle und Leistungsindikatoren, die in der Diagnosekonfigurationsdatei angegeben sind, werden in den Metrikspeicher von Azure Monitor und das angegebene Speicherkonto geschrieben: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Suchen Sie die Definition der VM-Skalierungsgruppe im Abschnitt „resources“, und fügen Sie der Konfiguration den Abschnitt **identity** hinzu. Mit dieser Ergänzung wird sichergestellt, dass Azure eine Systemidentität zuweist. Dieser Schritt stellt auch sicher, dass die VMs in der Skalierungsgruppe Gastmetriken über sich selbst an Azure Monitor senden können:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Suchen Sie in der VM-Skalierungsgruppenressource den Abschnitt **virtualMachineProfile**. Fügen Sie ein neues Profil namens **extensionsProfile** hinzu, um Erweiterungen zu verwalten.  


Fügen Sie der Vorlage in **extensionProfile** eine neue Erweiterung hinzu, wie im Abschnitt zur **VMSS-WAD-Erweiterung** veranschaulicht.  Dieser Abschnitt ist die Erweiterung für verwaltete Identitäten für Azure-Ressourcen, die sicherstellt, dass die ausgegebenen Metriken von Azure Monitor akzeptiert werden. Das Feld **Name** kann einen beliebigen Namen enthalten. 

Mit dem folgenden Code aus der MSI-Erweiterung wird die Diagnoseerweiterung und -konfiguration der VM-Skalierungsgruppenressource auch als Erweiterungsressource hinzugefügt. Sie können Leistungsindikatoren nach Bedarf hinzufügen oder entfernen: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Fügen Sie **dependsOn** für das Speicherkonto hinzu, um sicherzustellen, dass es in der richtigen Reihenfolge erstellt wird: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Erstellen Sie ein Speicherkonto, wenn dieses nicht bereits in der Vorlage erstellt wurde: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Speichern und schließen Sie beide Dateien. 

## <a name="deploy-the-resource-manager-template"></a>Bereitstellen der Resource Manager-Vorlage 

> [!NOTE]  
> Sie müssen die Azure-Diagnoseerweiterung Version 1.5 oder höher ausführen **und** die **autoUpgradeMinorVersion:**-Eigenschaft in Ihrer Resource Manager-Vorlage auf **TRUE** festgelegt haben. Azure lädt dann die richtige Erweiterung, wenn die VM gestartet wird. Wenn diese Einstellungen in Ihrer Vorlage nicht vorhanden sind, ändern Sie sie, und stellen Sie die Vorlage erneut bereit. 


Für die Bereitstellung der Resource Manager-Vorlage wird Azure PowerShell verwendet:  

1. Starten Sie PowerShell. 
1. Melden Sie sich mit `Login-AzAccount` bei Azure an.
1. Rufen Sie Ihre Liste der Abonnements mit `Get-AzSubscription` ab.
1. Legen Sie das Abonnement fest, das Sie erstellen, oder aktualisieren Sie den virtuellen Computer: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Erstellen Sie eine neue Ressourcengruppe für die bereitzustellende VM. Führen Sie den folgenden Befehl aus: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Denken Sie daran, eine Azure-Region zu verwenden, in der benutzerdefinierte Metriken aktiviert sind. Denken Sie daran, eine [Azure-Region zu verwenden, in der benutzerdefinierte Metriken aktiviert sind](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Führen Sie die folgenden Befehle aus, um den virtuellen Computers bereitzustellen:  

   > [!NOTE]  
   > Wenn Sie eine bestehende Skalierungsgruppe aktualisieren möchten, fügen Sie an das Ende des folgenden Befehls **-Mode Incremental** an. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Nach der erfolgreichen Bereitstellung sollte die VM-Skalierungsgruppe im Azure-Portal angezeigt werden. Sie sollte Metriken an Azure Monitor ausgeben. 

   > [!NOTE]  
   > Möglicherweise treten im Bereich um die **vmSkuSize** Fehler auf. Wechseln Sie in diesem Fall zurück zu Ihrer Datei **azuredeploy.json**, und aktualisieren Sie den Standardwert des Parameters **vmSkuSize**. Es wird empfohlen, **Standard_DS1_v2** zu versuchen. 


## <a name="chart-your-metrics"></a>Erfassen Ihrer Metriken 

1. Melden Sie sich beim Azure-Portal an. 

1. Wählen Sie im Menü auf der linken Seite **Monitor** aus. 

1. Wählen Sie auf der Seite **Überwachen** die Option **Metriken** aus. 

   ![Monitor – Seite „Metriken“](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Ändern Sie den Aggregationszeitraum in **Letzte 30 Minuten**.  

1. Wählen Sie im Dropdownliste mit den Ressourcen die VM-Skalierungsgruppe aus, die Sie erstellt haben.  

1. Wählen Sie im Dropdownmenü mit den Namespaces **azure.vm.windows.guest** aus. 

1. Wählen Sie im Dropdownmenü mit den Metriken **Memory\%Committed Bytes in Use** aus.  

Sie können dann auch die Verwendung der Dimensionen zu dieser Metrik auswählen, um diese für eine bestimmte VM oder für jede VM in der Skalierungsgruppe zu zeichnen. 



## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [benutzerdefinierte Metriken](metrics-custom-overview.md).


