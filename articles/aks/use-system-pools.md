---
title: Verwenden von Systemknotenpools in Azure Kubernetes Service (AKS)
description: Informationen zum Erstellen und Verwalten von Systemknotenpools in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 9c9479fca538c36f4f5eb430c4befb76e39370e6
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900025"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Verwalten von Systemknotenpools in Azure Kubernetes Service (AKS)

Im Azure Kubernetes Service (AKS) werden Knoten derselben Konfiguration zu *Knotenpools* zusammengefasst. Knotenpools enthalten die zugrunde liegenden virtuellen Computer, auf denen Ihre Anwendungen ausgeführt werden. Systemknotenpools und Benutzerknotenpools sind zwei verschiedene Knotenpoolmodi für AKS-Cluster. Systemknotenpools dienen dem primären Zweck, kritische Systempods wie `CoreDNS` und `metrics-server` zu hosten. Benutzerknotenpools dienen dem primären Zweck, Ihre Anwendungspods zu hosten. Anwendungspods lassen sich jedoch auf Systemknotenpools planen, wenn Sie nur einen Pool in Ihrem AKS-Cluster haben möchten. Jeder AKS-Cluster muss mindestens einen Systemknotenpool mit mindestens einem Knoten enthalten.

> [!Important]
> Wenn Sie für Ihren AKS-Cluster nur einen Systemknotenpool in einer Produktionsumgebung ausführen, sollten Sie für den Knotenpool mindestens drei Knoten verwenden.

## <a name="before-you-begin"></a>Voraussetzungen

* Azure CLI-Version 2.3.1 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern, die Systemknotenpools unterstützen.

* Siehe [Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Der AKS-Cluster muss mit VM-Skalierungsgruppen als VM-Typ sowie dem Load Balancer der *Standard* -SKU erstellt werden.
* Der Name eines Knotenpools darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Kleinbuchstaben beginnen. Bei Linux-Knotenpools muss die Länge zwischen 1 und 12 Zeichen betragen. Bei Windows-Knotenpools muss die Länge zwischen 1 und 6 Zeichen betragen.
* Zum Festlegen des Modus für einen Knotenpool muss eine API der Version 2020-03-01 oder höher verwendet werden. Cluster, die in älteren API-Versionen als 2020-03-01 erstellt werden, enthalten nur Benutzerknotenpools, können aber durch Ausführen der Schritte zum [Aktualisieren des Poolmodus](#update-existing-cluster-system-and-user-node-pools) migriert werden, sodass sie Systemknotenpools enthalten.
* Der Modus eines Knotenpools ist eine erforderliche Eigenschaft und muss explizit festgelegt werden, wenn ARM-Vorlagen oder direkte API-Aufrufe verwendet werden.

## <a name="system-and-user-node-pools"></a>System- und Benutzerknotenpools

Bei einem Systemknotenpool weist AKS seinen Knoten automatisch die Bezeichnung **kubernetes.azure.com/mode: system** zu. Dies bewirkt, dass AKS die Planung von Systempods in Knotenpools bevorzugt, die diese Bezeichnung enthalten. Diese Bezeichnung hindert Sie nicht an der Planung von Anwendungpods in Systemknotenpools. Es wird jedoch empfohlen, dass Sie kritische Systempods von Ihren Anwendungspods isolieren, um zu verhindern, dass falsch konfigurierte oder nicht autorisierte Anwendungspods versehentlich Systempods zerstören. Sie können dieses Verhalten durch Erstellen eines dedizierten Systemknotenpools erzwingen. Verwenden Sie den `CriticalAddonsOnly=true:NoSchedule`-Taint, um zu verhindern, dass Anwendungspods in Systemknotenpools geplant werden.

Für Systemknotenpools gelten folgende Einschränkungen:

* Bei Systempools muss als „osType“ Linux angegeben werden.
* Bei Benutzerknotenpools kann als „osType“ Linux oder Windows angegeben werden.
* Systempools müssen mindestens einen Knoten enthalten, während Benutzerknotenpools keine oder mehrere Knoten enthalten können.
* Für Systemknotenpools ist eine VM SKU mit mindestens zwei vCPUs und 4 GB Arbeitsspeicher erforderlich.
* Systemknotenpools müssen gemäß der [Formel für die mindestens erforderliche und maximal zulässige Anzahl von Pods][maximum-pods] mindestens 30 Pods unterstützen.
* Für Spot-Knotenpools sind Benutzerknotenpools erforderlich.
* Wenn Sie einen zusätzlichen Systemknotenpool hinzufügen oder ändern, welcher Knotenpool ein Systemknotenpool ist, werden die Systempods *NICHT* automatisch verschoben. Systempods können auch dann weiterhin im selben Knotenpool ausgeführt werden, wenn Sie diesen in einen Benutzerknotenpool ändern. Wenn Sie einen Knotenpool, auf dem Systempods ausgeführt werden und der zuvor ein Systemknotenpool war, löschen oder herunterskalieren, werden diese Systempods nach einem Zeitplan mit höherer Priorität im neuen Systemknotenpool erneut bereitgestellt.

Mit Knotenpools sind folgende Vorgänge möglich:

* Erstellen eines dedizierten Systemknotenpools (bevorzugen der Planung von Systempods in Knotenpools von `mode:system`)
* Sie können aus einem Systemknotenpool einen Benutzerknotenpool machen, sofern im AKS-Cluster ein anderer Systemknotenpool als Ersatz vorhanden ist.
* Sie können aus einem Benutzerknotenpool einen Systemknotenpool machen.
* Sie können Benutzerknotenpools löschen.
* Systemknotenpools können Sie löschen, wenn im AKS-Cluster ein anderer Systemknotenpool als Ersatz vorhanden ist.
* Ein AKS-Cluster kann mehrere Systemknotenpools enthalten, benötigt jedoch mindestens einen Systemknotenpool.
* Wenn Sie verschiedene unveränderliche Einstellungen für vorhandene Knotenpools ändern möchten, können Sie neue Knotenpools erstellen, um diese zu ersetzen. Ein Beispiel hierfür besteht darin, einen neuen Knotenpool mit einer neuen Einstellung für maximale Pods hinzuzufügen und den alten Knotenpool zu löschen.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Erstellen eines neuen AKS-Clusters mit einem Systemknotenpool

Beim Erstellen eines neuen AKS-Clusters wird automatisch ein Systemknotenpool mit einem Knoten erstellt. Für den anfänglichen Knotenpool ist standardmäßig ein Modus vom Typ „system“ festgelegt. Neue Knotenpools, die mit `az aks nodepool add` erstellt werden, sind Benutzerknotenpools, außer der Modusparameter (mode) wird explizit angegeben.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mithilfe des Befehls [az aks create][az-aks-create] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster namens *myAKSCluster* mit einem dedizierten Systempool erstellt, der einen Knoten enthält. Verwenden Sie in Ihren Produktionsworkloads Systemknotenpools mit mindestens drei Knoten. Dieser Vorgang kann mehrere Minuten dauern.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Hinzufügen eines dedizierten Systemknotenpools zu einem vorhandenen AKS-Cluster

> [!Important]
> Nachdem der Knotenpool erstellt wurde, können Sie Knoten-Taints nicht mehr über die CLI ändern.

Vorhandenen AKS-Clustern kann mindestens ein Systemknotenpool hinzugefügt werden. Es wird empfohlen, Ihre Anwendungspods in Benutzerknotenpools zu planen und Systemknotenpools nur für kritische Systempods zuzuweisen. Dadurch wird verhindert, dass nicht autorisierte Anwendungpods versehentlich Systempods zerstören. Erzwingen Sie dieses Verhalten mit dem `CriticalAddonsOnly=true:NoSchedule`-[Taint][aks-taints] für Ihre Systemknotenpools. 

Mit dem folgenden Befehl wird ein dedizierter Knotenpool mit dem Modustyp „system“ und der standardmäßigen Anzahl von drei Knoten hinzugefügt.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Anzeigen von Details zum Knotenpool

Mit dem folgenden Befehl können Sie die Details zu Ihrem Knotenpool anzeigen.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Für Systemknotenpools wurde ein Modus vom Typ **system** definiert, für Benutzerknotenpools ein Modus vom Typ **user** . Vergewissern Sie sich bei einem Systempool, dass der Taint auf `CriticalAddonsOnly=true:NoSchedule` festgelegt ist, wodurch verhindert wird, dass Anwendungspods in diesem Knotenpool geplant werden können.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aktualisieren von vorhandenen System- und Benutzerknotenpools in Clustern

> [!NOTE]
> Zum Festlegen des Modus für einen Systemknotenpool muss eine API der Version 2020-03-01 oder höher verwendet werden. Cluster, die in älteren API-Versionen als 2020-03-01 erstellt werden, enthalten somit nur Benutzerknotenpools. Aktualisieren Sie den Modus vorhandener Knotenpools mit den folgenden Befehlen in der neuesten Version der Azure-Befehlszeilenschnittstelle, um die Funktionen und Vorteile von Systemknotenpools für ältere Cluster zu ermöglichen.

Der Modus für System- und Benutzerknotenpools kann geändert werden. Sie können aus einem Systemknotenpool nur dann einen Benutzerpool machen, wenn im AKS-Cluster bereits ein anderer Systemknotenpool vorhanden ist.

Mit diesem Befehl wird aus einem Systemknotenpool ein Benutzerknotenpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Mit diesem Befehl wird aus einem Benutzerknotenpool ein Systemknotenpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Löschen eines Systemknotenpools

> [!Note]
> Wenn Sie in AKS-Clustern älter als API-Version 2020-03-02 Systemknotenpools verwenden möchten, fügen Sie einen neuen Systemknotenpool hinzu, und löschen Sie anschließend den ursprünglichen Standardknotenpool.

Im AKS-Cluster müssen mindestens zwei Systemknotenpools vorhanden sein, damit Sie einen davon löschen können.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Cluster löschen möchten, verwenden Sie den Befehl [az group delete][az-group-delete], um die AKS-Ressourcengruppe zu löschen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Systemknotenpools in einem AKS-Cluster erstellen und verwalten. Weitere Informationen zum Verwenden mehrerer Knotenpools finden Sie unter [Verwenden mehrerer Knotenpools][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
