---
title: Durchführen von Upgrades für AKS-Knotenimages (Azure Kubernetes Service)
description: Erfahren Sie, wie Sie für die Images auf AKS-Clusterknoten und Knotenpools ein Upgrade durchführen.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531478"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Upgrade für AKS-Knotenimages (Azure Kubernetes Service)

AKS unterstützt Upgrades für Images auf einem Knoten, sodass Sie mit den aktuellsten Betriebssystem- und Laufzeitupdates auf dem neuesten Stand sind. AKS stellt ein neues Image pro Woche mit den neuesten Updates zur Verfügung. Es ist daher von Vorteil, die Images Ihres Knotens regelmäßig auf die neuesten Features, einschließlich Linux- oder Windows-Patches, zu aktualisieren. In diesem Artikel erfahren Sie, wie Sie ein Upgrade für AKS-Clusterknotenimages durchführen und Knotenpoolimages aktualisieren, ohne ein Upgrade der Kubernetes-Version durchzuführen.

Weitere Informationen zu den neuesten von AKS bereitgestellten Images finden Sie in den [Versionshinweisen zu AKS](https://github.com/Azure/AKS/releases).

Informationen zum Upgrade der Kubernetes-Version für Ihren Cluster finden Sie unter [Aktualisieren eines AKS-Clusters][upgrade-cluster].

> [!NOTE]
> Der AKS-Cluster muss VM-Skalierungsgruppen für die Knoten verwenden.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Überprüfen, ob der Knotenpool über das neueste Knotenimage verfügt

Mit dem folgenden Befehl können Sie anzeigen, welches die neueste für Ihren Knotenpool verfügbare Knotenimageversion ist: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

In der Ausgabe sehen Sie die `latestNodeImageVersion`, wie im folgenden Beispiel:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Für `nodepool1` ist also `AKSUbuntu-1604-2020.10.28` die neueste verfügbare Knotenimageversion. Mit dem folgenden Befehl können Sie diese Version jetzt mit der aktuellen Knotenimageversion vergleichen, die von Ihrem Knotenpool verwendet wird:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Beispielausgabe:

```output
"AKSUbuntu-1604-2020.10.08"
```

In diesem Beispiel könnten Sie ein Upgrade von der aktuellen Imageversion `AKSUbuntu-1604-2020.10.08` auf die neueste Version `AKSUbuntu-1604-2020.10.28` durchführen. 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Aktualisieren aller Knoten in allen Knotenpools

Das Upgrade für Knotenimages wird mit `az aks upgrade` durchgeführt. Verwenden Sie folgenden Befehl, um das Upgrade für das Knotenimage durchzuführen:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Prüfen Sie während des Upgrades den Status der Knotenimages mit dem folgenden `kubectl`-Befehl, um die Bezeichnungen zu erhalten und die aktuellen Knotenimageinformationen herauszufiltern:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wenn das Upgrade beendet ist, verwenden Sie `az aks show`, um die aktualisierten Details zum Knotenpool abzurufen. Das aktuelle Knotenimage wird in der `nodeImageVersion`-Eigenschaft angezeigt.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Aktualisieren eines bestimmten Knotenpools

Das Durchführen eines Upgrades für das Image in einem Knotenpool ähnelt dem Durchführen eines Upgrades für das Image in einem Cluster.

Um das Betriebssystemimage des Knotenpools zu aktualisieren, ohne ein Upgrade des Kubernetes-Clusters durchzuführen, verwenden Sie im folgenden Beispiel die Option `--node-image-only`:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Prüfen Sie während des Upgrades den Status der Knotenimages mit dem folgenden `kubectl`-Befehl, um die Bezeichnungen zu erhalten und die aktuellen Knotenimageinformationen herauszufiltern:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wenn das Upgrade beendet ist, verwenden Sie `az aks nodepool show`, um die aktualisierten Details zum Knotenpool abzurufen. Das aktuelle Knotenimage wird in der `nodeImageVersion`-Eigenschaft angezeigt.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Aktualisieren von Knotenimages mit Knotenanstiegen

Zur Beschleunigung des Upgradeprozesses für Knotenimages können Sie Ihre Knotenimages mit einem anpassbaren Knotenanstiegswert aktualisieren. Standardmäßig verwendet AKS einen zusätzlichen Knoten, um Upgrades zu konfigurieren.

Wenn Sie die Geschwindigkeit von Upgrades erhöhen möchten, verwenden Sie den Wert `--max-surge`, um die Anzahl der Knoten zu konfigurieren, die für Upgrades verwendet werden sollen, damit diese schneller abgeschlossen werden. Weitere Informationen zu den Kompromissen verschiedener `--max-surge`-Einstellungen finden Sie unter [Anpassen des Upgrades für Knotenanstiege][max-surge].

Der folgende Befehl legt den maximalen Anstiegswert für die Durchführung eines Upgrades des Knotenimages fest:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Prüfen Sie während des Upgrades den Status der Knotenimages mit dem folgenden `kubectl`-Befehl, um die Bezeichnungen zu erhalten und die aktuellen Knotenimageinformationen herauszufiltern:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Verwenden Sie `az aks nodepool show`, um die aktualisierten Knotenpooldetails zu erhalten. Das aktuelle Knotenimage wird in der `nodeImageVersion`-Eigenschaft angezeigt.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den neuesten Knotenimages finden Sie in den [AKS-Versionshinweisen](https://github.com/Azure/AKS/releases).
- Erfahren Sie, wie Sie die Kubernetes-Version durch [Aktualisieren eines AKS-Clusters][upgrade-cluster] aktualisieren können.
- [Automatisches Anwenden von Cluster- und Knotenpoolupgrades mit GitHub-Aktionen][github-schedule]
- Weitere Informationen zu mehreren Knotenpools und zum Durchführen von Upgrades für Knotenpools erhalten Sie unter [Erstellen und Verwalten von mehreren Knotenpools][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
