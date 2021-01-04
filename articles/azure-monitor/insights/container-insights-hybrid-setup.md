---
title: Konfigurieren von Kubernetes-Hybridclustern mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für Container zum Überwachen von Kubernetes-Clustern konfigurieren können, die in Azure Stack oder einer anderen Umgebung gehostet werden.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d481af07013c0a5b4c5a381527c6f555400a2559
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890461"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurieren von Kubernetes-Hybridclustern mit Azure Monitor für Container

Azure Monitor für Container bietet umfassende Überwachungsfunktionen für Azure Kubernetes Service (AKS) und die [AKS-Engine in Azure](https://github.com/Azure/aks-engine), bei der es sich um einen selbstverwalteten Kubernetes-Cluster handelt, der in Azure gehostet wird. In diesem Artikel wird beschrieben, wie Sie die Überwachung von Kubernetes-Clustern, die außerhalb von Azure gehostet werden, aktivieren und eine ähnliche Überwachung erreichen können.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

Folgende Konfigurationen werden offiziell für Azure Monitor für Container unterstützt. Wenn Sie über eine andere Version von Kubernetes und andere Betriebssystemversionen verfügen, senden Sie eine E-Mail an askcoin@microsoft.com.

- Umgebungen:

    - Lokales Kubernetes
    - AKS-Engine in Azure und Azure Stack Weitere Informationen finden Sie unter [AKS-Engine in Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908&preserve-view=true).
    - [Openshift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) Version 4 und höher, lokale oder andere Cloudumgebungen.

- Die Versionen von Kubernetes und die Supportrichtlinie entsprechen den unterstützten Versionen von [AKS](../../aks/supported-kubernetes-versions.md).

- Folgende Containerruntimes werden unterstützt: Docker-, Moby- und CRI-kompatible Runtimes wie CRI-O und ContainerD.

- Unterstützte Linux-Betriebssystemreleases für Master- und Workerknoten: Ubuntu (18.04 LTS und 16.04 LTS) und Red Hat Enterprise Linux CoreOS 43.81.

- Unterstützte Zugriffssteuerung: Kubernetes mit und ohne RBAC

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

- Einen [Log Analytics-Arbeitsbereich](../platform/design-logs-deployment.md).

    Azure Monitor für Container unterstützt einen Log Analytics-Arbeitsbereich in den unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) aufgeführten Regionen in Azure. Ihren eigenen Arbeitsbereich können Sie über [Azure Resource Manager](../samples/resource-manager-workspace.md), über [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) oder im [Azure-Portal](../learn/quick-create-workspace.md) erstellen.

    >[!NOTE]
    >Die Aktivierung der Überwachung mehrerer Cluster mit dem gleichen Clusternamen im gleichen Log Analytics-Arbeitsbereich ist nicht möglich. Clusternamen müssen eindeutig sein.
    >

- Sie müssen der **Rolle „Log Analytics-Mitwirkender“ angehören** , um die Containerüberwachung aktivieren zu können. Weitere Informationen zum Steuern des Zugriffs auf einen Log Analytics-Arbeitsbereich finden Sie unter [Verwalten des Zugriffs auf Arbeitsbereich und Logdaten](../platform/manage-access.md).

- Sie müssen über die Rolle [*Log Analytics-Leser*](../platform/manage-access.md#manage-access-using-azure-permissions) im mit Azure Monitor für Container konfigurierten Log Analytics-Arbeitsbereich verfügen, um die Überwachungsdaten anzeigen zu können.

- [HELM-Client](https://helm.sh/docs/using_helm/) zum Integrieren des Azure Monitor für Containercharts für den angegebenen Kubernetes-Cluster

- Für die Kommunikation zwischen der Containerversion des Log Analytics-Agents für Linux und Azure Monitor sind die folgenden Proxy- und Firewallkonfigurationsinformationen erforderlich:

    |Agent-Ressource|Ports |
    |------|---------|
    |*.ods.opinsights.azure.com |Port 443 |
    |*.oms.opinsights.azure.com |Port 443 |
    |*.dc.services.visualstudio.com |Port 443 |

- Für den Container-Agent muss `cAdvisor secure port: 10250` oder `unsecure port :10255` von Kubelet auf allen Knoten im Cluster geöffnet werden, damit Leistungsmetriken erfasst werden können. Es wird empfohlen, `secure port: 10250` in cAdvisor von Kubelet zu konfigurieren, wenn dieser noch nicht konfiguriert ist.

- Für den Container-Agent müssen die folgenden Umgebungsvariablen im Container angegeben werden, damit die Kommunikation mit dem Kubernetes-API-Dienst innerhalb des Clusters zum Erfassen von Bestandsdaten möglich ist: `KUBERNETES_SERVICE_HOST` und `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>Die für die Überwachung von Kubernetes-Hybridclustern unterstützte Agent-Mindestversion ist ciprod10182019 oder höher.

## <a name="enable-monitoring"></a>Aktivieren der Überwachung

Die Aktivierung von Azure Monitor für Container für den Kubernetes-Hybridcluster umfasst die folgenden Schritte in der angegebenen Reihenfolge.

1. Konfigurieren Ihres Log Analytics-Arbeitsbereichs mit der Container Insights-Lösung   

2. Aktivieren des HELM-Charts von Azure Monitor für Container über den Log Analytics-Arbeitsbereich

Weitere Informationen zum Überwachen von Lösungen in Azure Monitor finden Sie [hier](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Hinzufügen von Azure Monitor für Container

Sie können die Lösung mit der bereitgestellten Azure Resource Manager-Vorlage mithilfe des Azure PowerShell-Cmdlets `New-AzResourceGroupDeployment` oder mit der Azure-Befehlszeilenschnittstelle bereitstellen.

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.59 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

Diese Methode umfasst zwei JSON-Vorlagen. Eine Vorlage gibt die Konfiguration zur Aktivierung der Überwachung an, während die andere Vorlage die zu konfigurierenden Parameterwerte enthält, mit denen Folgendes festgelegt wird:

- **workspaceResourceId:** die vollständige Ressourcen-ID Ihres Log Analytics Arbeitsbereichs
- **workspaceRegion:** die Region, in der der Arbeitsbereich erstellt wird. Beim Anzeigen über das Azure-Portal wird sie in den Arbeitsbereichseigenschaften auch als **Standort** bezeichnet.

Führen Sie die folgenden Schritte aus, um zunächst die für den Wert des `workspaceResourceId`-Parameters erforderliche vollständige Ressourcen-ID des Log Analytics-Arbeitsbereichs in der Datei **containerSolutionParams.json** zu ermitteln. Führen Sie anschließend das PowerShell-Cmdlet oder den Azure CLI-Befehl aus, um die Lösung hinzuzufügen.

1. Listen Sie mit dem folgenden Befehl alle Abonnements auf, auf die Sie Zugriff haben:

    ```azurecli
    az account list --all -o table
    ```

    Die Ausgabe ähnelt der folgenden:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Kopieren Sie den Wert für **SubscriptionId**.

2. Wechseln Sie mit dem folgenden Befehl zu dem Abonnement, das den Log Analytics-Arbeitsbereich hostet:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Im folgenden Beispiel wird die Liste mit den Arbeitsbereichen Ihrer Abonnements im JSON-Standardformat angezeigt.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Suchen Sie in der Ausgabe nach dem Namen des Arbeitsbereichs, und kopieren Sie dann die vollständige Ressourcen-ID dieses Log Analytics-Arbeitsbereichs unter dem Feld **ID**.

4. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
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

5. Speichern Sie diese Datei als „containerSolution.json“ in einem lokalen Ordner.

6. Fügen Sie die folgende JSON-Syntax in Ihre Datei ein:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Ändern Sie nach dem Ausführen des Azure CLI-Befehls [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list&preserve-view=true) die Werte für **workspaceResourceId** in den Wert, den Sie in Schritt 3 kopiert haben, und kopieren Sie für **workspaceRegion** den Wert **Region**.

8. Speichern Sie diese Datei als „containerSolutionParams.json“ in einem lokalen Ordner.

9. Nun können Sie die Vorlage bereitstellen.

   - Verwenden Sie zum Bereitstellen mit Azure PowerShell die folgenden Befehle in dem Ordner mit der Vorlage:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Führen Sie zum Bereitstellen mit Azure CLI die folgenden Befehle aus:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Nach dem Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis Integritätsmetriken für den Cluster angezeigt werden.

## <a name="install-the-helm-chart"></a>Installieren des Helm-Charts

In diesem Abschnitt installieren Sie den Container-Agent für Azure Monitor für Container. Zunächst müssen Sie die für den Parameter `omsagent.secret.wsid` erforderliche Arbeitsbereichs-ID und den für den Parameter `omsagent.secret.key` erforderlichen Primärschlüssel ermitteln. Diese Informationen können Sie durch Ausführen der folgenden Schritte ermitteln. Führen Sie dann die Befehle zum Installieren des Agents mithilfe des Helm-Charts aus.

1. Führen Sie den folgenden Befehl aus, um die Arbeitsbereichs-ID zu ermitteln:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Suchen Sie in der Ausgabe den Namen des Arbeitsbereichs im Feld **name** , und kopieren Sie die Arbeitsbereichs-ID dieses Log Analytics-Arbeitsbereichs dann in das Feld **customerID**.

2. Führen Sie den folgenden Befehl aus, um den Primärschlüssel für den Arbeitsbereich zu ermitteln:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Suchen Sie in der Ausgabe den Primärschlüssel im Feld **primarySharedKey** , und kopieren Sie den Wert.

>[!NOTE]
>Die folgenden Befehle gelten nur für die Helm-Version 2. Die Verwendung des Parameters `--name` gilt nicht für die Helm-Version 3. 

>[!NOTE]
>Wenn der Kubernetes-Cluster über einen Proxyserver kommuniziert, konfigurieren Sie den Parameter `omsagent.proxy` mit der URL des Proxyservers. Wenn der Cluster nicht über einen Proxyserver kommuniziert, müssen Sie diesen Parameter nicht angeben. Weitere Informationen finden Sie unter [Konfigurieren des Proxyendpunkts](#configure-proxy-endpoint) weiter unten in diesem Artikel.

3. Führen Sie folgenden Befehl aus, um das Repository der Azure-Charts in die lokale Liste einzufügen:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installieren Sie das Chart, indem Sie den folgenden Befehl ausführen:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Wenn sich der Log Analytics-Arbeitsbereich in Azure China 21Vianet befindet, führen Sie den folgenden Befehl aus:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Wenn sich der Log Analytics-Arbeitsbereich in einer Azure-Region von „US Government“ befindet, führen Sie den folgenden Befehl aus:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Aktivieren des Helm-Diagramms mit dem API-Modell

Sie können ein Add-On in der JSON-Datei mit der Clusterspezifikation der AKS-Engine angeben, das auch als API-Modell bezeichnet wird. Stellen Sie in diesem Add-On die Base64-codierte Version von `WorkspaceGUID` und `WorkspaceKey` des Log Analytics-Arbeitsbereichs bereit, in dem die gesammelten Überwachungsdaten gespeichert werden. `WorkspaceGUID` und `WorkspaceKey` ermitteln Sie mit den Schritten 1 und 2 im vorherigen Abschnitt.

Unterstützte API-Definitionen für den Azure Stack Hub-Cluster finden Sie in diesem Beispiel: [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Suchen Sie nach der **addons** -Eigenschaft im Abschnitt **kubernetesConfig** :

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Konfigurieren der Datensammlung des Agents

Ab Chartversion 1.0.0 werden die Einstellungen für die Datensammlung des Agents über die ConfigMap gesteuert. Die Dokumentation zu den Einstellungen für die Datensammlung des Agents finden Sie [hier](container-insights-agent-config.md).

Nachdem Sie das Chart erfolgreich bereitgestellt haben, können Sie im Azure-Portal die Daten für den Kubernetes-Hybridcluster in Azure Monitor für Container überprüfen.  

>[!NOTE]
>Die Latenz bei der Erfassung liegt bei rund fünf bis zehn Minuten, vom Agent bis zum Commit im Azure Log Analytics-Arbeitsbereich. Als Status des Clusters wird der Wert **Keine Daten** oder **Unbekannt** angegeben, bis alle erforderlichen Überwachungsdaten in Azure Monitor verfügbar sind.

## <a name="configure-proxy-endpoint"></a>Konfigurieren des Proxyendpunkts

Ab Chartversion 2.7.1 kann im Chart der Proxyendpunkt mit dem Chartparameter `omsagent.proxy` angegeben werden. Dies ermöglicht die Kommunikation über den Proxyserver. Die Kommunikation zwischen dem Azure Monitor für Container-Agent und Azure Monitor kann über einen HTTP- oder HTTPS-Proxyserver erfolgen. Außerdem werden die anonyme und die Standardauthentifizierung (mit Benutzername und Kennwort) unterstützt.

Der Wert für die Proxykonfiguration weist folgende Syntax auf: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Wenn für den Proxyserver keine Authentifizierung erforderlich ist, müssen Sie dennoch einen Pseudobenutzernamen und ein Pseudokennwort angeben. Dies kann ein beliebiger Benutzername oder ein beliebiges Kennwort sein.

|Eigenschaft| BESCHREIBUNG |
|--------|-------------|
|Protocol | HTTP oder HTTPS |
|user | Optionaler Benutzername für die Proxyauthentifizierung |
|password | Optionales Kennwort für die Proxyauthentifizierung |
|proxyhost | Adresse oder FQDN des Proxyservers |
|port | Optionale Portnummer für den Proxyserver |

Beispiel: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Wenn Sie das Protokoll als **http** angeben, werden die HTTP-Anforderungen mit einer sicheren SSL/TLS-Verbindung erstellt. Der Proxyserver muss SSL/TLS-Protokolle unterstützen.

## <a name="troubleshooting"></a>Problembehandlung

Tritt beim Aktivieren der Überwachung für Ihren Kubernetes-Hybridcluster ein Fehler auf, kopieren Sie das PowerShell-Skript [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s), und speichern Sie es in einem Ordner auf Ihrem Computer. Dieses Skript soll Ihnen beim Erkennen und Beheben von Problemen helfen. Es wurde entworfen, um die folgenden Probleme zu erkennen und zu beheben:

- Der angegebene Log Analytics-Arbeitsbereich ist gültig.
- Der Log Analytics-Arbeitsbereich ist mit der Lösung „Azure Monitor für Container“ konfiguriert. Ist das nicht der Fall, konfigurieren Sie den Arbeitsbereich.
- ReplicaSet-Pods des OMS-Agents werden ausgeführt.
- DaemonSet-Pods des OMS-Agents werden ausgeführt.
- Der Integritätsdienst des OMS-Agents wird ausgeführt.
- Die ID und der Schlüssel des Log Analytics-Arbeitsbereichs, die für den Container-Agent konfiguriert wurden, entsprechen dem Arbeitsbereich, mit dem Insight konfiguriert ist.
- Überprüfen Sie, ob alle Linux-Workerknoten über die Bezeichnung `kubernetes.io/role=agent` verfügen, um den RS-Pod zu planen. Fügen Sie sie anderenfalls hinzu.
- Vergewissern Sie sich, dass `cAdvisor secure port:10250` oder `unsecure port: 10255` für alle Knoten im Cluster geöffnet ist.

Verwenden Sie zum Ausführen mit Azure PowerShell die folgenden Befehle in dem Ordner mit dem Skript:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Nächste Schritte

Nach erfolgter Aktivierung der Überwachung zum Erfassen der Integrität und Ressourcennutzung des Kubernetes-Hybridclusters und der darin ausgeführten Workloads finden Sie [hier](container-insights-analyze.md) Informationen zur Verwendung von Azure Monitor für Container.