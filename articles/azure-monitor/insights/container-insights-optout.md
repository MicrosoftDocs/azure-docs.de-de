---
title: Beenden der Überwachung Ihres Azure Kubernetes Service-Clusters | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung Ihres Azure AKS-Clusters mit Azure Monitor für Container beenden können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2018
ms.author: magoedte
ms.openlocfilehash: 0e4268cb3a8d6ac62da12f689560338eee7e6935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071821"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Beenden der Überwachung Ihres Azure Kubernetes Service (AKS) Azure Monitors für Container

Die Überwachung Ihres AKS-Clusters kann nach der Aktivierung wieder beendet werden, wenn Sie den Cluster nicht mehr überwachen möchten. In diesem Artikel erfahren Sie, wie Sie dies mithilfe der Azure CLI oder der bereitgestellten Azure Resource Manager-Vorlagen umsetzen.  


## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Verwenden Sie den Befehl [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons), um Azure Monitor für Container zu deaktivieren. Der Befehl entfernt den Agent von den Clusterknoten, er entfernt jedoch nicht die Lösung oder die bereits gesammelten und in Ihrer Azure Monitor-Ressource gespeicherten Daten.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Informationen zum erneuten Aktivieren der Überwachung für Ihren Cluster finden Sie unter [Aktivieren der Überwachung mithilfe der Azure-Befehlszeilenschnittstelle](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Es stehen zwei Vorlagen für Azure Resource Manager zur Verfügung, die das konsistente und wiederholte Entfernen der Lösungsressourcen aus Ihrer Ressourcengruppe unterstützen. Eine ist eine JSON-Vorlage, die die Konfiguration zum Beenden der Überwachung angibt, während die andere die von Ihnen zu konfigurierenden Parameterwerte enthält, mit denen Sie die Ressourcen-ID des AKS-Clusters und die Ressourcengruppe festlegen, in der der Cluster bereitgestellt ist. 

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Die Vorlage muss in derselben Ressourcengruppe wie der Cluster bereitgestellt werden. Wenn Sie bei Verwendung dieser Vorlage andere Eigenschaften oder Add-Ons weglassen, werden diese ggf. aus dem Cluster entfernt. Beispiel: *enableRBAC*.  
>

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.27 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Erstellen der Vorlage

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Speichern Sie diese Datei als **OptOutTemplate.json** in einem lokalen Ordner.
3. Fügen Sie die folgende JSON-Syntax in Ihre Datei ein:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Aktualisieren Sie den Wert für **aksResourceId** und **aksResourceLocation** mit den Werten des AKS-Clusters, die auf der Seite **Eigenschaften** für den ausgewählten Cluster angegeben werden.

    ![Seite mit den Containereigenschaften](media/container-insights-optout/container-properties-page.png)

    Kopieren Sie auf der Seite **Eigenschaften** außerdem die **Arbeitsbereichsressourcen-ID**. Sie benötigen diesen Wert, falls Sie den Log Analytics-Arbeitsbereich später löschen möchten. Das Löschen des Log Analytics-Arbeitsbereichs wird nicht im Rahmen dieses Prozesses ausgeführt. 

5. Speichern Sie diese Datei als **OptOutParam.json** in einem lokalen Ordner.
6. Nun können Sie die Vorlage bereitstellen. 

### <a name="remove-the-solution-using-azure-cli"></a>Entfernen der Lösung mithilfe der Azure-Befehlszeilenschnittstelle
Führen Sie den folgenden Befehl der Azure CLI unter Linux aus, um die Lösung zu entfernen und die Konfiguration auf Ihrem AKS-Cluster zu bereinigen.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das zurückgegebene Ergebnis anzeigt:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Entfernen der Lösung mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Führen Sie die folgenden PowerShell-Befehle in dem Ordner aus, der die Vorlage enthält, um die Lösung zu entfernen und die Konfiguration auf Ihrem AKS-Cluster zu bereinigen.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das zurückgegebene Ergebnis anzeigt:

```powershell
ProvisioningState       : Succeeded
```

Wenn der Arbeitsbereich nur als Unterstützung für die Clusterüberwachung erstellt wurde und nicht mehr benötigt wird, müssen Sie ihn manuell löschen. Wie ein Arbeitsbereich gelöscht wird, erfahren Sie unter [Löschen eines Log Analytics-Arbeitsbereichs mit dem Azure-Portal](../../log-analytics/log-analytics-manage-del-workspace.md). Denken Sie an die **Arbeitsbereichsressourcen-ID**, die Sie weiter oben in Schritt 4 kopiert haben. Diese werden Sie später benötigen. 

