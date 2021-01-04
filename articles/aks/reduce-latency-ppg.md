---
title: Verwenden von Näherungsplatzierungsgruppen zur Verringerung der Wartezeit für AKS-Cluster (Azure Kubernetes Service)
description: Hier erfahren Sie, wie Sie mithilfe von Näherungsplatzierungsgruppen die Wartezeit für Ihre AKS-Clusterworkloads verringern.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fa81e293bc5e53a852bdb404f9e6d41c4297647b
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349034"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Verringern der Wartezeit mithilfe von Näherungsplatzierungsgruppen

> [!Note]
> Wenn Sie Näherungsplatzierungsgruppen mit AKS verwenden, betrifft die Colocation nur die Agent-Knoten. Die Wartezeit zwischen Knoten und die entsprechende Wartezeit zwischen gehosteten Pods werden verbessert. Die Colocation hat keine Auswirkung auf die Platzierung der Steuerungsebene eines Clusters.

Wenn Sie Ihre Anwendung in Azure bereitstellen, führt die Verteilung von VM-Instanzen (Virtual Machine, virtueller Computer) auf Regionen oder Verfügbarkeitszonen zu Netzwerkwartezeit, die die Gesamtleistung Ihrer Anwendung beeinträchtigen kann. Eine Näherungsplatzierungsgruppe ist eine logische Gruppierung, die dazu dient, eine geringe physische Entfernung zwischen Azure-Computeressourcen sicherzustellen. Einige Anwendungsbereiche wie Spiele, technische Simulationen und Hochfrequenzhandel (High-Frequency Trading, HFT) erfordern geringe Wartezeiten und eine schnelle Aufgabenausführung. In solchen HPC-Szenarien (High Performance Computing) empfiehlt sich ggf. der Einsatz von [Näherungsplatzierungsgruppen](../virtual-machines/linux/co-location.md#proximity-placement-groups) (PPG) für die Knotenpools Ihres Clusters.

## <a name="before-you-begin"></a>Voraussetzungen

Der Artikel setzt voraus, dass Sie mindestens Version 2.14 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

### <a name="limitations"></a>Einschränkungen

* Eine Näherungsplatzierungsgruppe kann höchstens einer Verfügbarkeitszone zugeordnet werden.
* Ein Knotenpool muss Virtual Machine Scale Sets verwenden, um eine Näherungsplatzierungsgruppe zuzuordnen.
* Ein Knotenpool kann eine Näherungsplatzierungsgruppe nur für die Erstellungszeit des Knotenpools zuordnen.

## <a name="node-pools-and-proximity-placement-groups"></a>Knotenpools und Näherungsplatzierungsgruppen

Die erste Ressource, die Sie mit einer Näherungsplatzierungsgruppe bereitstellen, wird einem bestimmten Rechenzentrum zugeordnet. Weitere Ressourcen, die mit der gleichen Näherungsplatzierungsgruppe bereitgestellt werden, werden im gleichen Rechenzentrum platziert. Wenn alle Ressourcen, von denen die Näherungsplatzierungsgruppe verwendet wird, beendet (Zuordnung aufgehoben) oder gelöscht wurden, ist sie nicht mehr zugeordnet.

* Viele Knotenpools können einer einzelnen Näherungsplatzierungsgruppe zugeordnet werden.
* Ein Knotenpool darf immer nur einer einzelnen Näherungsplatzierungsgruppe zugeordnet werden.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurieren von Näherungsplatzierungsgruppen mit Verfügbarkeitszonen

> [!NOTE]
> Während Näherungsplatzierungsgruppen einen Knotenpool benötigen, um höchstens eine Verfügbarkeitszone nutzen zu können, gilt für VMs in einer einzelnen Zone immer noch die [grundlegende Azure VM-SLA von 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/).

Näherungsplatzierungsgruppen sind ein Knotenpoolkonzept und mit jedem einzelnen Knotenpool verbunden. Die Verwendung einer PPG-Ressource hat keine Auswirkungen auf die Verfügbarkeit der AKS-Steuerungsebene. Dies kann Auswirkungen darauf haben, wie ein Cluster mit Zonen gestaltet werden sollte. Um sicherzustellen, dass ein Cluster über mehrere Zonen verteilt ist, wird der folgende Entwurf empfohlen.

* Stellen Sie einen Cluster mit dem ersten Systempool bereit, der drei Zonen verwendet und dem keine Näherungsplatzierungsgruppe zugeordnet ist. Dadurch wird sichergestellt, dass die Pods des Systems in einem dedizierten Knotenpool landen, der sich über mehrere Zonen verteilen wird.
* Fügen Sie zusätzliche Benutzerknotenpools mit einer eindeutigen Zone und einer jedem Pool zugeordneten Näherungsplatzierungsgruppe hinzu. Ein Beispiel ist „nodepool1“ in Zone 1 und PPG1, „nodepool2“ in Zone 2 und PPG2, „nodepool3“ in Zone 3 mit PPG3. Dadurch wird auf Clusterebene sichergestellt, dass die Knoten über mehrere Zonen verteilt sind und jeder einzelne Knotenpool in der festgelegten Zone mit einer dedizierten PPG-Ressource verbunden ist.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Erstellen eines neuen AKS-Clusters mit einer Näherungsplatzierungsgruppe

Im folgenden Beispiel wird der Befehl [az group create][az-group-create] verwendet, um eine Ressourcengruppe namens *myResourceGroup* in der Region *centralus* zu erstellen. Anschließend wird mit dem Befehl [az aks create][az-aks-create] ein AKS-Cluster mit dem Namen *myAKSCluster* erstellt.

Beschleunigter Netzwerkbetrieb trägt erheblich zur Verbesserung der Netzwerkleistung virtueller Computer bei. Es empfiehlt sich daher, Näherungsplatzierungsgruppen zusammen mit beschleunigtem Netzwerkbetrieb zu verwenden. Beschleunigter Netzwerkbetrieb wird von AKS standardmäßig in [unterstützten VM-Instanzen](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints) verwendet. Hierzu zählen die meisten virtuellen Azure-Computer mit mindestens zwei vCPUs.

Erstellen Sie einen neuen AKS-Cluster mit einer Näherungsplatzierungsgruppe, die dem ersten Systemknotenpool zugeordnet ist:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Führen Sie den folgenden Befehl aus, und speichern Sie die zurückgegebene ID:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Die Ausgabe des Befehls enthält den für anstehende CLI-Befehle erforderlichen *ID-Wert* :

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Verwenden Sie im folgenden Befehl die Ressourcen-ID der Näherungsplatzierungsgruppe für den Wert *myPPGResourceID* :

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Hinzufügen einer Näherungsplatzierungsgruppe in einen vorhandenen Cluster

Sie können einem vorhandenen Cluster eine Näherungsplatzierungsgruppe hinzufügen, indem Sie einen neuen Knotenpool erstellen. Anschließend können Sie vorhandene Workloads optional zum neuen Knotenpool migrieren und den ursprünglichen Knotenpool löschen.

Verwenden Sie die gleiche Näherungsplatzierungsgruppe, die Sie zuvor erstellt haben, um sicherzustellen, dass sich Agent-Knoten in beiden Knotenpools in Ihrem AKS-Cluster physisch im gleichen Rechenzentrum befinden.

Verwenden Sie die Ressourcen-ID aus der zuvor erstellten Näherungsplatzierungsgruppe, und fügen Sie mithilfe des Befehls [`az aks nodepool add`][az-aks-nodepool-add] einen neuen Knotenpool hinzu:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Bereinigung

Wenn Sie den Cluster löschen möchten, löschen Sie die AKS-Ressourcengruppe mithilfe des Befehls [`az group delete`][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Näherungsplatzierungsgruppen finden Sie [hier][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
