---
title: Beenden der Überwachung Ihres Azure Red Hat OpenShift 3-Clusters | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung Ihres Azure Red Hat OpenShift-Clusters mit Azure Monitor für Container beenden können.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6c21b17fd9faed7b133cd8ecad767ae7bd4aa868
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216529"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Beenden der Überwachung Ihres Azure Red Hat OpenShift 3-Clusters

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 wird zum Juni 2022 eingestellt.
>
> Ab Oktober 2020 können Sie keine neuen 3.11-Cluster mehr erstellen.
> Vorhandene 3.11-Cluster werden bis zum Juni 2022 weiter betrieben, jedoch nach diesem Datum nicht mehr unterstützt.
>
> Führen Sie die Schritte in diesem Leitfaden aus, um [einen Azure Red Hat OpenShift 4-Cluster zu erstellen](../../openshift/tutorial-create-cluster.md).
> Wenn Sie spezielle Fragen haben, [kontaktieren Sie uns](mailto:aro-feedback@microsoft.com).

Die Überwachung Ihres Azure Red Hat OpenShift 3-Clusters kann nach der Aktivierung wieder beendet werden, wenn Sie den Cluster nicht mehr mit Azure Monitor für Container überwachen möchten. In diesem Artikel erfahren Sie, wie Sie dies mithilfe der bereitgestellten Azure Resource Manager-Vorlage umsetzen.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Es stehen zwei Vorlagen für Azure Resource Manager zur Verfügung, die das konsistente und wiederholte Entfernen der Lösungsressourcen aus Ihrer Ressourcengruppe unterstützen. Eine ist eine JSON-Vorlage, die die Konfiguration zum Beenden der Überwachung angibt, während die andere die von Ihnen zu konfigurierenden Parameterwerte enthält, mit denen Sie die Ressourcen-ID des OpenShift-Clusters und die Azure-Region festlegen, in der der Cluster bereitgestellt ist.

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen die Azure CLI-Version 2.0.65 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Erstellen der Vorlage

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Bearbeiten Sie den Wert für **aroResourceId** und **aroResourceLocation** mit den Werten des OpenShift-Clusters, die auf der Seite **Eigenschaften** für den ausgewählten Cluster angegeben werden.

    ![Seite mit den Containereigenschaften](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Speichern Sie diese Datei als **OptOutParam.json** in einem lokalen Ordner.

6. Nun können Sie die Vorlage bereitstellen.

### <a name="remove-the-solution-using-azure-cli"></a>Entfernen der Lösung mithilfe der Azure-Befehlszeilenschnittstelle

Führen Sie den folgenden Befehl der Azure CLI unter Linux aus, um die Lösung zu entfernen und die Konfiguration auf Ihrem Cluster zu bereinigen.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das zurückgegebene Ergebnis anzeigt:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Entfernen der Lösung mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Führen Sie die folgenden PowerShell-Befehle in dem Ordner aus, der die Vorlage enthält, um die Lösung zu entfernen und die Konfiguration auf Ihrem Cluster zu bereinigen.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das zurückgegebene Ergebnis anzeigt:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Nächste Schritte

Wenn der Arbeitsbereich nur als Unterstützung für die Clusterüberwachung erstellt wurde und nicht mehr benötigt wird, müssen Sie ihn manuell löschen. Wie ein Arbeitsbereich gelöscht wird, erfahren Sie unter [Löschen eines Log Analytics-Arbeitsbereichs](../platform/delete-workspace.md).
