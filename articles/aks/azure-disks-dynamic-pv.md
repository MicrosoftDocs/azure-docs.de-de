---
title: Dynamisches Erstellen von Azure-Datenträgervolumes
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie ein persistentes Volume mit Azure-Datenträgern in Azure Kubernetes Service (AKS) dynamisches erstellen.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ad51bfdf8c494e763921de880926b839cdb7be62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021638"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamisches Erstellen und Verwenden eines persistenten Volumes mit Azure-Datenträgern in Azure Kubernetes Service (AKS)

Ein persistentes Volume stellt ein Speicherelement dar, das für die Verwendung in Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet und dynamisch oder statisch bereitgestellt werden. Dieser Artikel zeigt Ihnen, wie Sie dynamisch persistente Volumes mit Azure-Datenträgern erstellen, die von einem einzelnen Pod in einem AKS-Cluster (Azure Kubernetes Service) verwendet werden.

> [!NOTE]
> Ein Azure-Datenträger kann nur mit einem *Zugriffsmodus* vom Typ *ReadWriteOnce* eingebunden werden. Dadurch ist er für einen Knoten in AKS verfügbar. Wenn Sie ein persistentes Volume für mehrere Knoten freigeben möchten, verwenden Sie [Azure Files][azure-files-pvc].

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="built-in-storage-classes"></a>Integrierte Speicherklassen

Mit einer Speicherklasse wird festgelegt, wie eine Speichereinheit dynamisch in einem persistenten Volume erstellt wird. Weitere Informationen zu Kubernetes-Speicherklassen finden Sie unter [Kubernetes-Speicherklassen][kubernetes-storage-classes].

Jeder AKS-Cluster enthält vier vorab erstellte Speicherklassen, von denen zwei für die Arbeit mit Azure-Datenträgern konfiguriert sind:

* Die Speicherklasse *default* stellt einen Azure SSD Standard-Datenträger bereit.
    * Der Standardspeicher basiert auf SSD Standard-Laufwerken und stellt eine kostengünstige Speicherlösung mit zuverlässiger Leistung dar. 
* Die Speicherklasse *managed-premium* stellt einen Azure Premium-Datenträger bereit.
    * Premium-Datenträger zeichnen sich durch SSD-basierte hohe Leistung und geringe Wartezeit aus. Sie eignen sich hervorragend für virtuelle Computer, auf denen die Produktionsworkload ausgeführt wird. Wenn die AKS-Knoten in dem Cluster Storage Premium verwenden, wählen Sie die *managed-premium*-Klasse aus.
    
Wenn Sie eine der Standardspeicherklassen verwenden, können Sie die Volumegröße nicht mehr aktualisieren, nachdem die Speicherklasse erstellt wurde. Wenn Sie die Volumegröße auch nach der Erstellung einer Speicherklasse noch ändern können möchten, fügen Sie einer der Standardspeicherklassen die Zeile `allowVolumeExpansion: true` hinzu, oder Sie erstellen Ihre eigene, benutzerdefinierte Speicherklasse. Beachten Sie, dass eine Reduzierung der PVC-Größe nicht unterstützt wird (zur Vermeidung von Datenverlusten). Sie können eine vorhandene Speicherklasse mit dem Befehl `kubectl edit sc` bearbeiten. 

Wenn Sie beispielsweise einen Datenträger mit einer Größe von 4 TiB verwenden möchten, müssen Sie eine Speicherklasse erstellen, die `cachingmode: None` definiert, da [Datenträgerzwischenspeicherungen für Datenträger ab 4 TiB nicht unterstützt werden](../virtual-machines/premium-storage-performance.md#disk-caching).

Weitere Informationen zu Speicherklassen und der Erstellung eigener Speicherklassen finden Sie unter [Speicheroptionen für Anwendungen in AKS][storage-class-concepts].

Mit dem Befehl [kubectl get sc][kubectl-get] können Sie die vorab erstellten Speicherklassen anzeigen. Im folgenden Beispiel werden die in einem AKS-Cluster verfügbaren vorab erstellten Speicherklassen gezeigt:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Ansprüche für persistente Volumes werden in GiB angegeben, verwaltete Azure-Datenträger werden jedoch nach SKU für eine bestimmte Größe abgerechnet. Diese SKUs reichen von 32 GiB für S4- oder P4-Datenträger bis 32 TiB für S80- oder P80-Datenträger (in der Vorschau). Der Durchsatz und die IOPS-Leistung eines verwalteten Premium-Datenträgers hängen sowohl von der SKU als auch von der Instanzgröße der Knoten im AKS-Cluster ab. Weitere Informationen finden Sie unter [Verwaltete Datenträger – Preise und Leistung][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Erstellen eines Anspruchs auf ein persistentes Volume

Ein Anspruch auf ein persistentes Volume (Persistent Volume Claim, PVC) wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen. In diesem Fall kann ein PVC eine der zuvor erstellten Speicherklassen verwenden, um einen verwalteten Azure Standard- oder Premium-Datenträger zu erstellen.

Erstellen Sie eine Datei namens `azure-premium.yaml`, und fügen Sie das folgende Manifest ein. Der Anspruch erfordert einen Datenträger namens `azure-managed-disk` mit einer Größe von *5 GB* und *ReadWriteOnce*-Zugriff. Als Speicherklasse ist *managed-premium* angegeben.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Verwenden Sie zum Erstellen eines Datenträgers, der den Standardspeicher verwendet, `storageClassName: default` statt *managed-premium*.

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] einen Anspruch auf ein persistentes Volume, und geben Sie die Datei *azure-premium.yaml* an:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Verwenden des persistenten Volumes

Nachdem der Anspruch auf ein persistentes Volumes erstellt und der Datenträger erfolgreich bereitgestellt wurde, kann ein Pod mit Zugriff auf den Datenträger erstellt werden. Das folgende Manifest erstellt einen grundlegenden NGINX-Pod, der den Anspruch auf das persistente Volume *azure-managed-disk* verwendet, um den Azure-Datenträger im Pfad `/mnt/azure` einzubinden. Geben Sie für Windows Server-Container einen *mountPath* gemäß Windows-Pfadkonvention an, z. B. *D:* .

Erstellen Sie eine Datei namens `azure-pvc-disk.yaml`, und fügen Sie das folgende Manifest ein.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
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
        claimName: azure-managed-disk
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply], wie im folgenden Beispiel gezeigt wird:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Sie verfügen nun über einen ausgeführten Pod mit Ihrem Azure-Datenträger, der im Verzeichnis `/mnt/azure` eingebunden wurde. Diese Konfiguration wird u.U. angezeigt, wenn Sie Ihren Pod über `kubectl describe pod mypod` überprüfen, wie in dem folgenden verkürzten Beispiel gezeigt wird:

```console
$ kubectl describe pod mypod

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

## <a name="use-ultra-disks"></a>Verwenden von Ultra-Datenträgern
Informationen zum Nutzen von Ultra-Datenträgern finden Sie unter [Verwenden von Ultra-Datenträgern in Azure Kubernetes Service (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Sichern eines persistenten Volumes

Um die Daten in Ihrem persistenten Volume zu sichern, erstellen Sie eine Momentaufnahme von dem verwalteten Datenträger für das Volume. Als Möglichkeit zum Wiederherstellen der Daten können Sie dann mithilfe dieser Momentaufnahme einen wiederhergestellten Datenträger erstellen und diesen an Pods anfügen.

Rufen Sie zuerst mit dem Befehl `kubectl get pvc` den Volumenamen ab, z.B. für den PVC mit dem Namen *azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Dieser Volumename bildet den Namen des zugrunde liegenden Azure-Datenträgers. Fragen Sie mit [az disk list][az-disk-list] die Datenträger-ID ab, und geben Sie den Namen Ihres PVC-Volumes an, wie im folgenden Beispiel gezeigt wird:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Verwenden Sie die Datenträger-ID, um mit [az snapshot create][az-snapshot-create] einen Momentaufnahmedatenträger zu erstellen. Im folgenden Beispiel wird in der gleichen Ressourcengruppe wie der AKS-Cluster (*MC_myResourceGroup_myAKSCluster_eastus*) eine Momentaufnahme mit dem Namen *pvcSnapshot* erstellt. Es können möglicherweise Berechtigungsprobleme auftreten, wenn Sie Momentaufnahmen erstellen und Datenträger in Ressourcengruppen wiederherstellen, auf die der AKS-Cluster keinen Zugriff hat.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Die Erstellung der Momentaufnahme kann einige Minuten dauern, abhängig von der Menge der Daten auf Ihrem Datenträger.

## <a name="restore-and-use-a-snapshot"></a>Wiederherstellen und Verwenden einer Momentaufnahme

Um den Datenträger wiederherzustellen und mit einem Kubernetes-Pod zu verwenden, verwenden Sie bei der Erstellung eines Datenträgers mit [az disk create][az-disk-create] die Momentaufnahme als Quelle. Bei diesem Vorgang wird die ursprüngliche Ressource beibehalten, wenn Sie danach auf die ursprüngliche Datenmomentaufnahme zugreifen müssen. Im folgenden Beispiel wird ein Datenträger namens *pvcRestored* mit der Momentaufnahme *pvcSnapshot* erstellt:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Geben Sie die ID des Datenträgers im Manifest an, um den wiederhergestellten Datenträger mit einem Pod zu verwenden. Rufen Sie mit dem Befehl [az disk show][az-disk-show] die Datenträger-ID ab. Im folgenden Beispiel wird die Datenträger-ID für die Momentaufnahme *pvcRestored* abgerufen, die Sie im vorherigen Schritt erstellt haben:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Erstellen Sie ein Podmanifest mit dem Namen `azure-restored.yaml`, und geben Sie den Datenträger-URI an, den Sie im vorherigen Schritt abgerufen haben. Im folgenden Beispiel wird ein einfacher NGINX-Webserver erstellt, wobei der wiederhergestellte Datenträger als Volume unter */mnt/azure* eingebunden wird:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply], wie im folgenden Beispiel gezeigt wird:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Sie können mit `kubectl describe pod mypodrestored` Details zum Pod anzeigen, z.B. mit dem folgenden gekürzten Beispiel, das Informationen zum Volume anzeigt:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Nächste Schritte

Entsprechenden bewährte Methoden finden Sie unter [Bewährte Methoden für die Speicherung und Sicherungen in AKS][operator-best-practices-storage].

Erfahren Sie mehr über persistente Kubernetes-Volumes bei Verwendung von Azure-Datenträgern.

> [!div class="nextstepaction"]
> [Kubernetes-Plug-In für Azure-Datenträger][azure-disk-volume]

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
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
