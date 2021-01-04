---
title: Aktivieren der hostbasierten Verschlüsselung in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie eine hostbasierte Verschlüsselung in einem Azure Kubernetes Service-Cluster (AKS) konfigurieren.
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 14ec39272bf2f434aaa57217a90667a62e82901a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183293"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Hostbasierte Verschlüsselung in Azure Kubernetes Service (AKS) (Vorschau)

Mit hostbasierter Verschlüsselung werden die auf dem VM-Host der VMs Ihres AKS-Agent-Knotens gespeicherten Daten ruhend verschlüsselt und verschlüsselt an den Speicherdienst übermittelt. Dies bedeutet, dass temporäre Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden. Der Cache von Betriebssystem- und sonstigen Datenträgern wird im Ruhezustand entweder mit von der Plattform verwalteten Schlüsseln oder vom Kunden verwalteten Schlüsseln verschlüsselt, je nachdem, welcher Verschlüsselungstyp auf diesen Datenträgern festgelegt wurde. Standardmäßig werden bei der Verwendung von AKS Betriebssystem- und sonstige Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt. Dies bedeutet, dass die Caches für diese Datenträger standardmäßig auch im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden.  Sie können Ihre eigenen verwalteten Schlüssel wie in [Bring Your Own Key (BYOK) mit Azure-Datenträgern in Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md) beschrieben angeben. Der Cache für diese Datenträger wird dann auch mit dem Schlüssel verschlüsselt, den Sie in diesem Schritt angeben.


## <a name="before-you-begin"></a>Voraussetzungen

Diese Funktion kann nur bei der Erstellung des Clusters oder bei der Erstellung eines Knotenpools festgelegt werden.

> [!NOTE]
> Die hostbasierte Verschlüsselung ist in [Azure-Regionen][supported-regions] verfügbar, die die serverseitige Verschlüsselung von verwalteten Azure-Datenträgern unterstützen, und nur mit bestimmten [unterstützten VM-Größen][supported-sizes].

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass die `aks-preview`-CLI-Erweiterung v0.4.55 oder höher installiert ist.
- Stellen Sie sicher, dass das `EncryptionAtHost`-Featureflag unter `Microsoft.Compute` aktiviert ist.
- Stellen Sie sicher, dass das `EnableEncryptionAtHostPreview`-Featureflag unter `Microsoft.ContainerService` aktiviert ist.

### <a name="register-encryptionathost--preview-features"></a>Registrieren der `EncryptionAtHost`-Vorschaufunktionen

Sie müssen die Featureflags `EnableEncryptionAtHostPreview` und `EncryptionAtHost` in Ihrem Abonnement aktivieren, um einen AKS-Cluster zu erstellen, der eine hostbasierte Verschlüsselung verwendet.

Registrieren Sie das `EncryptionAtHost`-Featureflag mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

Wenn Sie fertig sind, aktualisieren Sie die Registrierung des `Microsoft.ContainerService`- und `Microsoft.Compute`-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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

Konfigurieren Sie die Cluster-Agent-Knoten für die Verwendung der hostbasierten Verschlüsselung, wenn der Cluster erstellt wird. Verwenden Sie das `--aks-custom-headers`-Flag, um den `EnableEncryptionAtHost`-Header festzulegen.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Wenn Sie Cluster ohne hostbasierte Verschlüsselung erstellen möchten, lassen Sie den benutzerdefinierten Parameter `--aks-custom-headers` weg.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Verwenden der hostbasierten Verschlüsselung auf vorhandenen Clustern (Vorschau)

Sie können die hostbasierte Verschlüsselung auf vorhandenen Clustern aktivieren, indem Sie dem Cluster einen neuen Knotenpool hinzufügen. Konfigurieren Sie einen neuen Knotenpool für die Verwendung der hostbasierten Verschlüsselung mit dem `--aks-custom-headers`-Flag.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Wenn Sie neue Knotenpools ohne das Feature der hostbasierten Verschlüsselung erstellen möchten, lassen Sie den benutzerdefinierten Parameter `--aks-custom-headers` weg.

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