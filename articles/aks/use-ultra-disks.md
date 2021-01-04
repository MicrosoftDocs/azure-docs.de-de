---
title: Aktivieren der Unterstützung für Disk Ultra in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Ultra Disks in einem Azure Kubernetes Service-Cluster (AKS) aktivieren und konfigurieren.
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 049c2682a8f61bb658083b0418a4fcf99dc477a5
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900036"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Verwenden von Azure Ultra Disks in Azure Kubernetes Service (Vorschauversion)

[Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md) bieten hohen Durchsatz, einen hohen IOPS-Wert und einen Datenträgerspeicher mit durchgängig geringer Latenz für zustandsbehaftete Anwendungen. Ein Hauptvorteil von Ultra Disks ist die Möglichkeit zum dynamischen Ändern der SSD-Leistung zusammen mit Ihren Workloads, ohne dass Sie Ihre Agent-Knoten neu starten müssen. Ultra Disks eignen sich für datenintensive Workloads.

## <a name="before-you-begin"></a>Voraussetzungen

Diese Funktion kann nur bei der Erstellung des Clusters oder bei der Erstellung eines Knotenpools festgelegt werden.

> [!IMPORTANT]
> Azure Ultra Disks erfordern Knotenpools in Verfügbarkeitszonen und Regionen, die diese Datenträger unterstützen, sowie bestimmte VM-Serien. Weitere Informationen finden Sie unter [**Umfang und Einschränkungen für die allgemeine Verfügbarkeit von Ultra Disks**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Registrieren der Previewfunktion `EnableUltraSSD`

Um einen AKS-Cluster oder einen Knotenpool zu erstellen, der Ultra Disks verwenden kann, müssen Sie das Featureflag `EnableUltraSSD` in Ihrem Abonnement aktivieren.

Registrieren Sie das `EnableUltraSSD`-Featureflag mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie mithilfe des Befehls [az provider register][az-provider-register] die Registrierung des Ressourcenanbieters *Microsoft.ContainerService* :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Um einen AKS-Cluster oder Knotenpool zu erstellen, der Ultra Disks verwenden kann, benötigen Sie die aktuelle CLI-Erweiterung *aks-preview* . Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], oder installieren Sie mit dem Befehl [az extension update][az-extension-update] alle verfügbaren Updates:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Einschränkungen
- Weitere Informationen finden Sie unter [**Umfang und Einschränkungen für die allgemeine Verfügbarkeit von Ultra Disks**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).
- Der unterstützte Größenbereich für Ultra Disks liegt zwischen 100 und 1.500.

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Erstellen eines neuen Clusters, der Ultra Disks verwenden kann

Erstellen Sie mithilfe der folgenden CLI-Befehle einen AKS-Cluster, der Ultra Disks verwenden kann. Verwenden Sie das Flag `--aks-custom-headers`, um das Feature `EnableUltraSSD` festzulegen.

Erstellen Sie eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Erstellen Sie den AKS-Cluster mit Unterstützung für Ultra Disks.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Wenn Sie Cluster ohne Unterstützung für Ultra Disks erstellen möchten, lassen Sie den benutzerdefinierten Parameter `--aks-custom-headers` weg.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Aktivieren von Ultra Disks in einem vorhandenen Cluster

Sie können Ultra Disks in vorhandenen Clustern aktivieren, indem Sie dem Cluster einen neuen Knotenpool hinzufügen, der Ultra Disks unterstützt. Konfigurieren Sie einen neuen Knotenpool für die Verwendung der hostbasierten Verschlüsselung mit dem `--aks-custom-headers`-Flag.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Wenn Sie neue Knotenpools ohne Unterstützung für Ultra Disks erstellen möchten, lassen Sie den benutzerdefinierten Parameter `--aks-custom-headers` weg.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Dynamisches Verwenden von Ultra Disks mit einer Speicherklasse

Zum Verwenden von Ultra Disks in unseren Bereitstellungen oder zustandsbehafteten Konfigurationen können Sie eine [Speicherklasse für die dynamische Bereitstellung](azure-disks-dynamic-pv.md) verwenden.

### <a name="create-the-storage-class"></a>Erstellen der Speicherklasse

Mit einer Speicherklasse wird festgelegt, wie eine Speichereinheit dynamisch in einem persistenten Volume erstellt wird. Weitere Informationen zu Kubernetes-Speicherklassen finden Sie unter [Kubernetes-Speicherklassen][kubernetes-storage-classes].

In diesem Fall erstellen Sie eine Speicherklasse, die auf Ultra Disks verweist. Erstellen Sie eine Datei namens `azure-ultra-disk-sc.yaml`, und fügen Sie das folgende Manifest ein.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Erstellen Sie die Speicherklasse mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie die Datei *azure-ultra-disk-sc.yaml* an:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Erstellen eines Anspruchs auf ein persistentes Volume

Ein Anspruch auf ein persistentes Volume (Persistent Volume Claim, PVC) wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen. In diesem Fall kann ein Anspruch auf ein persistentes Volume die zuvor erstellte Speicherklasse zum Erstellen einer Ultra Disk verwenden.

Erstellen Sie eine Datei namens `azure-ultra-disk-pvc.yaml`, und fügen Sie das folgende Manifest ein. Der Anspruch fordert einen Datenträger namens `ultra-disk` mit einer Größe von *1.000 GB* und *ReadWriteOnce* -Zugriff an. Als Speicherklasse ist *ultra-disk-sc* angegeben.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] einen Anspruch auf ein persistentes Volume, und geben Sie die Datei *azure-ultra-disk-pvc.yaml* an:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Verwenden des persistenten Volumes

Nachdem der Anspruch auf ein persistentes Volumes erstellt und der Datenträger erfolgreich bereitgestellt wurde, kann ein Pod mit Zugriff auf den Datenträger erstellt werden. Das folgende Manifest erstellt einen grundlegenden NGINX-Pod, der den Anspruch auf das persistente Volume *ultra-disk* verwendet, um den Azure-Datenträger im Pfad `/mnt/azure` einzubinden.

Erstellen Sie eine Datei namens `nginx-ultra.yaml`, und fügen Sie das folgende Manifest ein.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply], wie im folgenden Beispiel gezeigt wird:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Sie verfügen nun über einen ausgeführten Pod mit Ihrem Azure-Datenträger, der im Verzeichnis `/mnt/azure` eingebunden wurde. Diese Konfiguration wird u.U. angezeigt, wenn Sie Ihren Pod über `kubectl describe pod nginx-ultra` überprüfen, wie in dem folgenden verkürzten Beispiel gezeigt wird:

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Ultra Disks finden Sie unter [Verwenden von Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md).
- Weitere Informationen zu bewährten Methoden bei der Speicherung finden Sie unter [Best Practices für Speicherung und Sicherungen in Azure Kubernetes Service (AKS)][operator-best-practices-storage].

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
