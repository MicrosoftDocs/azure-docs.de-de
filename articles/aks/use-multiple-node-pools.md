---
title: Verwenden mehrerer Knotenpools in Azure Kubernetes Service (AKS)
description: Informationen zum Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 679d91da774b3e4d2c53c70cdc0abfd4da9c6953
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059631"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Vorschau – Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in Azure Kubernetes Service (AKS)

Im Azure Kubernetes Service (AKS) werden Knoten derselben Konfiguration zu *Knotenpools* zusammengefasst. Diese Knotenpools enthalten die zugrunde liegenden virtuellen Computer, die Ihre Anwendungen ausführen. Die anfängliche Anzahl der Knoten und ihre Größe (SKU) werden beim Erstellen eines AKS-Clusters festgelegt, der einen *Standardknotenpool* erstellt. Sie können zusätzliche Knotenpools erstellen, um Anwendungen mit unterschiedlichen Compute- oder Speicheranforderungen zu unterstützen. Verwenden Sie diese zusätzlichen Knotenpools z. B. zum Bereitstellen von GPUs für rechenintensive Anwendungen oder für den Zugriff auf leistungsstarken SSD-Speicher.

In diesem Artikel erfahren Sie, wie Sie mehrere Knotenpools in einem AKS-Cluster erstellen und verwalten. Diese Funktion steht derzeit als Vorschau zur Verfügung.

> [!IMPORTANT]
> AKS-Previewfunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Sie werden zum Sammeln von Feedback und Fehlern mithilfe unserer Community bereitgestellt. In der Vorschauversion sind diese Features nicht für den Einsatz in der Produktion vorgesehen. Features in der öffentlichen Vorschau unterliegen dem Prinzip des „bestmöglichen Supports“. Unterstützung durch die Teams für den technischen AKS-Support steht nur während der Geschäftszeiten in der Zeitzone „Pacific Standard Time“ (PST) zur Verfügung. Weitere Informationen hierzu finden Sie in den folgenden Supportartikeln:
>
> * [Unterstützungsrichtlinien für Azure Kubernetes Service][aks-support-policies]
> * [Häufig gestellte Fragen zum Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][install-azure-cli] Informationen dazu.

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Die CLI-Befehle zum Erstellen und Verwalten mehrerer Knotenpools sind in der CLI-Erweiterung *aks-preview* verfügbar. Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Wenn Sie zuvor die Erweiterung *aks-preview* installiert haben, installieren Sie alle verfügbaren Updates mit dem Befehl `az extension update --name aks-preview`.

### <a name="register-multiple-node-pool-feature-provider"></a>Registrieren von Featureanbietern für mehrere Knotenpools

Aktivieren Sie zunächst zwei Featureflags in Ihrem Abonnement, um einen AKS-Cluster zu erstellen, der mehrere Knotenpools verwenden kann. Poolcluster mit mehreren Knoten verwenden eine VM-Skalierungsgruppe (VMSS), um die Bereitstellung und Konfiguration der Kubernetes-Knoten zu verwalten. Registrieren Sie die Featureflags *MultiAgentpoolPreview* und *VMSSPreview* mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Jeder von Ihnen nach der erfolgreichen Registrierung von *MultiAgentpoolPreview* erstellte AKS-Cluster verwendet diese Möglichkeit zur Clustervorschau. Um weiterhin normale, vollständig unterstützte Cluster zu erstellen, sollten Sie keine Vorschaufeatures für Produktionsabonnements aktivieren. Verwenden Sie ein separates Test- oder Entwickungsabonnement von Azure, um Vorschaufeatures zu testen.

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Wenn Sie fertig sind, aktualisieren Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern, die mehrere Knotenpools unterstützen:

* Mehrere Knotenpools sind nur für Cluster verfügbar, die nach der erfolgreichen Registrierung der Features *MultiAgentpoolPreview* und *VMSSPreview* für Ihr Abonnement erstellt wurden. Sie können keine Knotenpools mit einem bestehenden AKS-Cluster hinzufügen oder verwalten, die vor der erfolgreichen Registrierung dieser Features erstellt wurden.
* Sie können den ersten Knotenpool nicht löschen.
* Das Add-On für das HTTP-Anwendungsrouting kann nicht verwendet werden.
* Sie können Knotenpools nicht mit einer vorhandenen Resource Manager-Vorlage hinzufügen/aktualisieren/löschen wie mit den meisten Vorgängen. Stattdessen [verwenden Sie eine gesonderte Resource Manager-Vorlage](#manage-node-pools-using-a-resource-manager-template), um Änderungen an Knotenpools in einem AKS-Cluster vorzunehmen.
* Die automatische Skalierung des Clusters (zurzeit in der Vorschauphase in AKS) kann nicht verwendet werden.

Während sich dieses Feature in der Vorschauversion befindet, gelten die folgenden zusätzlichen Einschränkungen:

* Der AKS-Cluster kann maximal acht Knotenpools umfassen.
* Der AKS-Cluster kann maximal 400 Knoten in diesen acht Knotenpools enthalten.

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Erstellen Sie zu Beginn einen AKS-Cluster mit einem einzelnen Knotenpool. Das folgende Beispiel verwendet den Befehl [az group create][az-group-create], um eine Ressourcengruppe namens *myResourceGroup* in der Region *eastus* zu erstellen. Ein AKS-Cluster namens *myAKSCluster* wird dann mit dem Befehl [az aks create][az-aks-create] erstellt. Eine *--kubernetes-version* von *1.12.6* wird verwendet, um die Aktualisierung eines Knotenpools in einem nachfolgenden Schritt zu veranschaulichen. Sie können eine beliebige [unterstützte Kubernetes-Version][supported-versions] angeben.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

Die Erstellung des Clusters dauert einige Minuten.

Wenn der Cluster bereit ist, verwenden Sie den Befehl [az aks get-credentials][az-aks-get-credentials], um die Clusteranmeldeinformationen für die Verwendung mit `kubectl` zu erhalten:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Hinzufügen eines Knotenpools

Der im vorherigen Schritt erstellte Cluster verfügt über einen einzelnen Knotenpool. Lassen Sie uns einen zweiten Knotenpool mit dem Befehl [az aks node pool add][az-aks-nodepool-add] hinzufügen. Das folgende Beispiel erstellt einen Knotenpool namens *mynodepool*, der *3* Knoten ausführt:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Verwenden Sie den Befehl [az aks node pool list][az-aks-nodepool-list], um den Status Ihrer Knotenpools anzuzeigen, und geben Sie Ihre Ressourcengruppe und Ihren Clusternamen an:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Die folgende Beispielausgabe zeigt, dass *mynodepool* erfolgreich mit drei Knoten im Knotenpool erstellt wurde. Wenn der AKS-Cluster im vorherigen Schritt erstellt wurde, wurde ein standardmäßiges *nodepool1* mit einer Knotenanzahl von *1* erstellt.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Wenn beim Hinzufügen eines Knotenpools kein *OrchestratorVersion* oder *VmSize* angegeben wurde, werden die Knoten basierend auf den Standardwerten für den AKS-Cluster erstellt. In diesem Beispiel wurde die Kubernetes-Version *1.12.6* und eine Knotengröße von *Standard_DS2_v2* verwendet.

## <a name="upgrade-a-node-pool"></a>Durchführen eines Upgrades für einen Knotenpool

Als Ihr AKS-Cluster im ersten Schritt erstellt wurde, war eine `--kubernetes-version` von *1.12.6* angegeben. Lassen Sie uns für *mynodepool* ein Upgrade auf Kubernetes *1.12.7* durchführen. Verwenden Sie den Befehl [az aks node pool upgrade][az-aks-nodepool-upgrade], um das Upgrade des Knotenpools durchzuführen, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Listen Sie den Status Ihrer Knotenpools mit dem Befehl [az aks node pool list][az-aks-nodepool-list] erneut auf. Das folgende Beispiel zeigt, dass *mynodepool* den Zustand *Upgrading* (Wird aktualisiert) auf *1.12.7* aufweist:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Es dauert einige Minuten, bis ein Upgrade der Knoten auf die angegebene Version erfolgt ist.

Als bewährte Methode sollten Sie alle Knotenpools in einem AKS-Cluster auf dieselbe Kubernetes-Version aktualisieren. Die Möglichkeit, ein Upgrade für einzelne Knotenpools durchzuführen, gestattet es Ihnen, ein paralleles Upgrade durchzuführen und Pods zwischen Knotenpools zu planen, um die Anwendungsbetriebszeit aufrechtzuerhalten.

## <a name="scale-a-node-pool"></a>Skalieren eines Knotenpools

Wenn sich die Anforderungen der Workloads Ihrer Anwendungen ändern, müssen Sie möglicherweise die Anzahl der Knoten in einem Knotenpool skalieren. Die Anzahl der Knoten kann erhöht oder verringert werden.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Um die Anzahl der Knoten in einem Knotenpool zu skalieren, verwenden Sie den Befehl [az aks node pool scale][az-aks-nodepool-scale]. Das folgende Beispiel skaliert die Anzahl der Knoten in *mynodepool* auf *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Listen Sie den Status Ihrer Knotenpools mit dem Befehl [az aks node pool list][az-aks-nodepool-list] erneut auf. Das folgende Beispiel zeigt, dass *mynodepool* den Zustand *Scaling* (Wird skaliert) mit einer neuen Anzahl von *5* Knoten aufweist:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Es dauert einige Minuten, bis die Skalierung abgeschlossen ist.

## <a name="delete-a-node-pool"></a>Löschen eines Knotenpools

Wenn Sie einen Pool nicht mehr benötigen, können Sie ihn löschen und die zugrunde liegenden VM-Knoten entfernen. Verwenden Sie zum Löschen eines Knotenpools den Befehl [az aks node pool delete][az-aks-nodepool-delete], und geben Sie den Namen des Knotenpools an. Das folgende Beispiel löscht den in den vorherigen Schritten erstellten Knotenpool *mynodepool*:

> [!CAUTION]
> Es gibt keine Wiederherstellungsoptionen für Datenverluste, die beim Löschen eines Knotenpools auftreten können. Wenn Pods nicht in anderen Knotenpools geplant werden können, sind diese Anwendungen nicht verfügbar. Stellen Sie sicher, dass Sie einen Knotenpool nicht löschen, wenn aktive Anwendungen keine Datensicherungen aufweisen oder nicht in anderen Knotenpools in Ihrem Cluster ausgeführt werden können.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Die folgende Beispielausgabe des Befehls [az aks node pool list][az-aks-nodepool-list] zeigt, dass sich *mynodepool* im Zustand *Deleting* (Wird gelöscht) befindet:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Das Löschen der Knoten und des Knotenpools dauert einige Minuten.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Angeben einer VM-Größe für einen Knotenpool

In den vorherigen Beispielen zur Erstellung eines Knotenpools wurde für die im Cluster erstellten Knoten eine VM-Standardgröße verwendet. Ein üblicheres Szenario ist es, Knotenpools mit unterschiedlichen VM-Größen und -Funktionen zu erstellen. Sie können z. B. einen Knotenpool erstellen, der Knoten mit einer großen Anzahl an CPUs oder mit viel Arbeitsspeicher enthält, oder einen Knotenpool, der GPU-Unterstützung bietet. Im nächsten Schritt teilen Sie dem Kubernetes-Planer durch [Verwenden von Taints und Toleranzen](#schedule-pods-using-taints-and-tolerations) mit, wie Sie den Zugriff auf Pods, die auf diesen Knoten ausgeführt werden können, einschränken können.

Erstellen Sie im folgenden Beispiel einen GPU-basierten Knotenpool, der die VM-Größe *Standard_NC6* verwendet. Diese virtuellen Computer werden von der NVIDIA Tesla K80-Karte unterstützt. Informationen zu den verfügbaren VM-Größen finden Sie unter [Größen für virtuelle Linux-Computer in Azure][vm-sizes].

Erstellen Sie erneut einen Knotenpool mit dem Befehl [az aks node pool add][az-aks-nodepool-add]. Geben Sie diesmal den Namen *gpunodepool* und mit dem Parameter `--node-vm-size` die Größe *Standard_NC6* an:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Die folgende Beispielausgabe des Befehls [az aks node pool list][az-aks-nodepool-list] zeigt, dass *gpunodepool* Knoten mit der folgenden *VmSize* (VM-Größe) *erstellt*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Es dauert einige Minuten, bis *gpunodepool* erfolgreich erstellt wurde.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Planen von Pods mit Taints und Toleranzen

Sie verfügen jetzt über zwei Knotenpools in Ihrem Cluster – den ursprünglich erstellten Standardknotenpool und den GPU-basierten Knotenpool. Verwenden Sie den Befehl [kubectl get nodes][kubectl-get], um die Knoten in Ihrem Cluster anzuzeigen. Die folgende Beispielausgabe zeigt einen Knoten in jedem Knotenpool:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Der Kubernetes-Planer kann Taints und Toleranzen verwenden, um einzuschränken, welche Workloads auf Knoten ausgeführt werden können.

* Ein **Taint** wird auf einen Knoten angewendet, der anzeigt, dass nur bestimmte Pods darauf geplant werden können.
* Für den Pod wird anschließend eine **Toleranz** angewendet, damit dieser den Taint des Knotens *tolerieren* kann.

Weitere Informationen zur Verwendung der erweiterten geplanten Kubernetes-Features finden Sie unter [Bewährte Methoden für erweiterte Planerfeatures in AKS][taints-tolerations].

Wenden Sie in diesem Beispiel mit dem Befehl [kubectl taint node][kubectl-taint] einen Taint auf Ihren GPU-basierten Knoten an. Geben Sie den Namen Ihres GPU-basierten Knotens aus der Ausgabe des vorherigen `kubectl get nodes` Befehls an. Der Taint wird als *Schlüssel:Wert* und dann eine Planungsoption angewendet. Das folgende Beispiel verwendet das Paar *sku=gpu* und definiert Pods, die ansonsten die Option *NoSchedule* aufweisen:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Das folgende einfache YAML-Beispielmanifest verwendet eine Toleranz, damit der Kubernetes-Planer einen NGINX-Pod auf dem GPU-basierten Knoten ausführen kann. Ein geeigneteres, aber zeitintensiveres Beispiel für die Ausführung eines Tensorflow-Auftrags für den MNIST-Datensatz finden Sie unter [Verwenden von GPUs für rechenintensive Workloads für AKS][gpu-cluster].

Erstellen Sie eine Datei mit dem Namen `gpu-toleration.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Planen Sie den Pod mit dem Befehl `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Es dauert einige Sekunden, um den Pod zu planen und das NGINX-Image per Pull abzurufen. Verwenden Sie den Befehl [kubectl describe pod][kubectl-describe], um den Podstatus anzuzeigen. Die folgende kompakte Beispielausgabe zeigt, dass die Toleranz *sku=gpu:NoSchedule* angewendet wird. Im Ereignisabschnitt hat der Planer den Pod dem GPU-basierten Knoten *aks-gpunodepool-28993262-vmss000000* zugewiesen:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Nur Pods mit diesem Taint können auf Knoten in *gpunodepool* geplant werden. Alle anderen Pods werden im Knotenpool *nodepool1* geplant. Wenn Sie weitere Knotenpools erstellen, können Sie mit zusätzlichen Taints und Toleranzen einschränken, welche Pods für diese Knotenressourcen geplant werden können.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Verwalteten von Knotenpools mithilfe einer Resource Manager-Vorlage

Wenn Sie eine Azure Resource Manager-Vorlage zum Erstellen und Verwalten von Ressourcen verwenden, können Sie die Einstellungen in Ihrer Vorlage normalerweise aktualisieren und neu bereitstellen, um die Ressource zu aktualisieren. Bei Knotenpools in AKS kann das Profil des Anfangsknotenpools nicht mehr aktualisiert werden, nachdem der AKS-Cluster erstellt wurde. Dieses Verhalten bedeutet, dass Sie eine vorhandene Resource Manager-Vorlage nicht aktualisieren können, um eine Änderung an den Knotenpools vorzunehmen und dann neu bereitzustellen. Stattdessen müssen Sie eine separate Resource Manager-Vorlage erstellen, die nur die Agent-Pools für einen vorhandenen AKS-Cluster aktualisiert.

Erstellen Sie eine Vorlage wie `aks-agentpools.json`, und fügen Sie das folgende Beispielmanifest ein. Diese Beispielvorlage konfiguriert die folgenden Einstellungen:

* Aktualisiert den *Linux*-Agent-Pool namens *myagentpool*, sodass dieser drei Knoten ausführt.
* Legt die Knoten im Knotenpool auf die Ausführung von Kubernetes, Version *1.12.8*, fest.
* Definiert die Knotengröße als *Standard_DS2_v2*.

Bearbeiten Sie diese Werte nach Bedarf, um Knotenpools nach Bedarf zu aktualisieren, hinzuzufügen oder zu löschen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Stellen Sie diese Vorlage mithilfe des Befehls [az group deployment create][az-group-deployment-create] bereit, wie im folgenden Beispiel gezeigt. Sie werden zur Eingabe des Namens und Speicherorts des vorhandenen AKS-Clusters aufgefordert:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Es kann ein paar Minuten dauern, bis Ihr AKS-Cluster aktualisiert wird, abhängig von den Knoteneinstellungen und Vorgängen, die Sie in Ihrer Resource Manager-Vorlage definieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie einen AKS-Cluster erstellt, der GPU-basierte Knoten enthält. Um unnötige Kosten zu reduzieren, können Sie *gpunodepool* oder den gesamten AKS-Cluster löschen.

Verwenden Sie zum Löschen des GPU-basierten Knotenpools den Befehl [az aks node pool delete][az-aks-nodepool-delete], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Um den Cluster selbst zu löschen, verwenden Sie den Befehl [az group delete][az-group-delete], um die AKS-Ressourcengruppe zu löschen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mehrere Knotenpools in einem AKS-Cluster erstellen und verwalten. Weitere Informationen zum knotenpoolübergreifenden Steuern von Pods finden Sie unter [Bewährte Methoden für erweiterte Planerfeatures in AKS][operator-best-practices-advanced-scheduler].

Informationen zum Erstellen und Verwenden von Windows Server-Containerknotenpools finden Sie unter [Erstellen eines Windows Server-Containers in AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
