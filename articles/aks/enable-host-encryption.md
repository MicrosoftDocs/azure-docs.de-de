---
title: Aktivieren der hostbasierten Verschlüsselung in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie eine hostbasierte Verschlüsselung in einem Azure Kubernetes Service-Cluster (AKS) konfigurieren.
services: container-service
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ac28c698a766f1f3febaff582038906f658d58dd
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071849"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Hostbasierte Verschlüsselung in Azure Kubernetes Service (AKS) (Vorschau)

Mit hostbasierter Verschlüsselung werden die auf dem VM-Host der VMs Ihres AKS-Agent-Knotens gespeicherten Daten ruhend verschlüsselt und verschlüsselt an den Speicherdienst übermittelt. Dies bedeutet, dass temporäre Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden. Der Cache von Betriebssystem- und sonstigen Datenträgern wird im Ruhezustand entweder mit von der Plattform verwalteten Schlüsseln oder vom Kunden verwalteten Schlüsseln verschlüsselt, je nachdem, welcher Verschlüsselungstyp auf diesen Datenträgern festgelegt wurde. Standardmäßig werden bei der Verwendung von AKS Betriebssystem- und sonstige Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt. Dies bedeutet, dass die Caches für diese Datenträger standardmäßig auch im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden.  Sie können Ihre eigenen verwalteten Schlüssel wie in [Bring Your Own Key (BYOK) mit Azure-Datenträgern in Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md) beschrieben angeben. Der Cache für diese Datenträger wird dann auch mit dem Schlüssel verschlüsselt, den Sie in diesem Schritt angeben.


## <a name="before-you-begin"></a>Voraussetzungen

Diese Funktion kann nur bei der Erstellung des Clusters oder bei der Erstellung eines Knotenpools festgelegt werden.

> [!NOTE]
> Die hostbasierte Verschlüsselung ist in [Azure-Regionen][supported-regions] verfügbar, die die serverseitige Verschlüsselung von verwalteten Azure-Datenträgern unterstützen, und nur mit bestimmten [unterstützten VM-Größen][supported-sizes].

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass die CLI-Erweiterung `aks-preview` (v0.4.73 oder höher) installiert ist.

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Um einen AKS-Cluster zu erstellen, der eine hostbasierte Verschlüsselung hostet, benötigen Sie die aktuelle CLI-Erweiterung *aks-preview*. Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], oder suchen Sie mit dem Befehl [az extension update][az-extension-update] nach verfügbaren Updates:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Einschränkungen

- Kann nur in neuen Knotenpools oder neuen Clustern aktiviert werden.
- Kann nur in [Azure-Regionen][supported-regions] aktiviert werden, die die serverseitige Verschlüsselung von verwalteten Azure-Datenträgern unterstützen, und nur mit bestimmten [unterstützten VM-Größen][supported-sizes].
- Erfordert einen AKS-Cluster und einen Knotenpool, der auf VM-Skalierungsgruppen (Virtual Machine Scale Sets, VMSS) als *VM set type* basiert.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Verwenden der hostbasierten Verschlüsselung in neuen Clustern (Vorschau)

Konfigurieren Sie die Cluster-Agent-Knoten für die Verwendung der hostbasierten Verschlüsselung, wenn der Cluster erstellt wird. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Wenn Sie Cluster ohne hostbasierte Verschlüsselung erstellen möchten, lassen Sie den Parameter `--enable-encryption-at-host` weg.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Verwenden der hostbasierten Verschlüsselung auf vorhandenen Clustern (Vorschau)

Sie können die hostbasierte Verschlüsselung auf vorhandenen Clustern aktivieren, indem Sie dem Cluster einen neuen Knotenpool hinzufügen. Konfigurieren Sie einen neuen Knotenpool für die Verwendung der hostbasierten Verschlüsselung mit dem Parameter `--enable-encryption-at-host`.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Wenn Sie neue Knotenpools ohne das Feature der hostbasierten Verschlüsselung erstellen möchten, lassen Sie den Parameter `--enable-encryption-at-host` weg.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][best-practices-security] und erfahren Sie mehr über [Serverseitige Verschlüsselung von verwalteten Azure-Datenträgern](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
