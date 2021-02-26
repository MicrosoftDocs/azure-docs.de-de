---
title: Aktivieren von Azure Monitor auf einem Azure Stack Edge Pro-GPU-Gerät
description: Erfahren Sie, wie Sie Azure Monitor auf einem Azure Stack Edge Pro-GPU-Gerät aktivieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 171b4dbfb2a5852e270c483a28cad31f97dcb42b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493911"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivieren von Azure Monitor auf Ihrem Azure Stack Edge Pro-GPU-Gerät

Es ist insbesondere dann wichtig, Container auf Ihrem Azure Stack Edge Pro-GPU-Gerät zu überwachen, wenn Sie mehrere Computeanwendungen ausführen. Mit Azure Monitor können Sie Containerprotokolle sowie Speicher- und Prozessormetriken zu dem Kubernetes-Cluster erfassen, der auf Ihrem Gerät ausgeführt wird.

In diesem Artikel werden die erforderlichen Schritte zum Aktivieren von Azure Monitor auf Ihrem Gerät und zum Erfassen von Containerprotokollen im Log Analytics-Arbeitsbereich beschrieben. Der Azure Monitor-Metrikspeicher wird von Ihrem Azure Stack Edge Pro-GPU-Gerät derzeit nicht unterstützt. 


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

- Ein Azure Stack Edge Pro-Gerät muss vorhanden sein. Stellen Sie sicher, dass das Gerät gemäß den Schritten im [Tutorial: Aktivieren Ihres Geräts](azure-stack-edge-gpu-deploy-activate.md) aktiviert wurde.
- Sie haben den Schritt **Konfigurieren der Computeumgebung** unter [Tutorial: Konfigurieren von Compute auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) auf Ihrem Gerät abgeschlossen. Ihrem Gerät sollte eine IoT Hub-Ressource, ein IoT-Gerät und ein IoT Edge-Gerät zugeordnet sein.


## <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Führen Sie zum Erstellen eines Log Analytics-Arbeitsbereichs die folgenden Schritte aus. Ein Log Analytics-Arbeitsbereich ist eine logische Speichereinheit, in der die Protokolldaten erfasst und gespeichert werden.

1. Wählen Sie im Azure-Portal **+ Ressource erstellen** aus, suchen Sie nach **Log Analytics-Arbeitsbereich**, und wählen Sie dann **Erstellen** aus. 
1. Konfigurieren Sie unter **Log Analytics-Arbeitsbereich erstellen** die folgenden Einstellungen. Akzeptieren Sie die übrigen Einstellungen als Standard.

    1. Geben Sie auf der Registerkarte **Grundlagen** das Abonnement, die Ressourcengruppe, den Namen und die Region für den Arbeitsbereich an. 

        ![Registerkarte „Grundlagen“ für den Log Analytics-Arbeitsbereich](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Akzeptieren Sie auf der Registerkarte **Tarif** den standardmäßigen **Plan für die nutzungsbasierte Bezahlung**.

        ![Registerkarte „Tarif“ für den Log Analytics-Arbeitsbereich](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Informationen für Ihren Arbeitsbereich, und wählen Sie **Erstellen** aus.

        ![„Überprüfen + erstellen“ für den Log Analytics-Arbeitsbereich](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Weitere Informationen finden Sie in den ausführlichen Schritten unter [Erstellen eines Log Analytics-Arbeitsbereichs über das Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Aktivieren von Container Insights

Führen Sie die folgenden Schritte aus, um Container Insights für Ihren Arbeitsbereich zu aktivieren. 

1. Führen Sie die ausführlichen Schritte unter [Hinzufügen von Azure Monitor für Container](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution) aus. Verwenden Sie die Vorlagendatei `containerSolution.json`:

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Rufen Sie die Ressourcen-ID und den Speicherort ab. Gehe zu `Your Log Analytics workspace > General > Properties`. Kopieren Sie die folgenden Informationen:

    - **resource ID:** die vollqualifizierte Azure-Ressourcen-ID des Azure Log Analytics-Arbeitsbereichs 
    - **location:** die Azure-Region

    ![Eigenschaften des Log Analytics-Arbeitsbereichs](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Verwenden Sie die Parameterdatei `containerSolutionParams.json`. Ersetzen Sie `workspaceResourceId` durch die Ressourcen-ID und `workspaceRegion` durch den Speicherort, den Sie im vorherigen Schritt kopiert haben.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Im Folgenden die Beispielausgabe eines Log Analytics-Arbeitsbereichs mit aktivierten Container Insights:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Konfigurieren von Azure Monitor auf Ihrem Gerät

1. Wechseln Sie zur neu erstellten Log Analytics-Ressource, und kopieren Sie die **Arbeitsbereichs-ID** und den **Primärschlüssel** (Arbeitsbereichsschlüssel).

    ![Agent-Verwaltung im Log Analytics-Arbeitsbereich](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Speichern Sie diese Informationen, da sie in einem späteren Schritt verwendet werden.

1. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 
    
1. Verwenden Sie die Log Analytics-Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel mit dem folgenden Cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Nachdem Azure Monitor aktiviert wurde, sollten Protokolle im Log Analytics-Arbeitsbereich angezeigt werden. Wechseln Sie zu **Azure Monitor > Insights > Container**, um den Status des Kubernetes-Clusters anzuzeigen, der auf Ihrem Gerät bereitgestellt wurde. Wählen Sie für die Option „Umgebung“ **Alle** aus. 

    ![Metriken im Log Analytics-Arbeitsbereich](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Kubernetes-Workloads über das Kubernetes-Dashboard überwachen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Erfahren Sie, wie Sie [Warnbenachrichtigungen zu Geräteereignissen verwalten](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 