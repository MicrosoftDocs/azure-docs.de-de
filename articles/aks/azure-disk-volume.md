---
title: Erstellen eines statischen Volumes für Pods in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie manuell ein Volume mit Azure-Datenträgern für die Verwendung mit Pods in Azure Kubernetes Service (AKS) erstellen.
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: d44c8a7241308c26a3f1148ec70a7a5730dd0c89
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900850"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Manuelles Erstellen und Verwenden eines Volumes mit Azure-Datenträgern in Azure Kubernetes Service (AKS)

Containerbasierte Anwendungen müssen häufig auf Daten in einem externen Datenvolume zugreifen und diese dauerhaft speichern. Wenn ein einzelner Pod Zugriff auf Speicher benötigt, können Sie Azure-Datenträger verwenden, um ein natives Volume für die Anwendungsnutzung bereitzustellen. In diesem Artikel wird das manuelle Erstellen eines Azure-Datenträgers und das Anfügen dieses Datenträgers an einen Pod in AKS veranschaulicht.

> [!NOTE]
> Ein Azure-Datenträger kann nur für jeweils einen Pod gleichzeitig bereitgestellt werden. Wenn Sie ein persistentes Volume für mehrere Pods freigeben müssen, verwenden Sie [Azure Files][azure-files-volume].

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Erstellen eines Azure-Datenträgers

Wenn Sie einen Azure-Datenträger für die Verwendung mit AKS erstellen, können Sie die Datenträgerressource in der Ressourcengruppe **Knoten** erstellen. Diese Vorgehensweise ermöglicht es dem AKS-Cluster, auf die Datenträgerressource zuzugreifen und diese zu verwalten. Wenn Sie den Datenträger stattdessen in einer separaten Ressourcengruppe erstellen, müssen Sie dem Azure Kubernetes Service-Dienstprinzipal (AKS) für Ihren Cluster die Rolle `Contributor` für die Ressourcengruppe des Datenträgers zuweisen. Alternativ können Sie für Berechtigungen die vom System zugewiesene verwaltete Identität anstelle des Dienstprinzipals verwenden. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

In diesem Artikel erstellen Sie den Datenträger in der Ressourcengruppe „Knoten“. Rufen Sie zunächst den Namen der Ressourcengruppe mit dem Befehl [az aks show][az-aks-show] ab, und fügen Sie den Abfrageparameter `--query nodeResourceGroup` hinzu. Im folgenden Beispiel wird der Knoten „Ressourcengruppe“ für den AKS-Clusternamen *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Erstellen Sie nun mit dem Befehl [az disk create][az-disk-create] einen Datenträger. Geben Sie den Knotennamen der Ressourcengruppe, den Sie mit dem vorherigen Befehl abgerufen haben, und dann einen Namen für die Datenträgerressource an, z.B. *myAKSDisk* : Das folgende Beispiel erstellt einen Datenträger mit *20*  GiB und gibt die ID des Datenträgers nach der Erstellung aus. Wenn Sie einen Datenträger für die Verwendung mit Windows Server-Containern erstellen möchten, fügen Sie den Parameter `--os-type windows` hinzu, um den Datenträger ordnungsgemäß zu formatieren.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-Datenträger werden nach SKU für eine bestimmte Größe berechnet. Diese SKUs reichen von 32 GiB für S4- oder P4-Datenträger bis 32 TiB für S80- oder P80-Datenträger (in der Vorschau). Der Durchsatz und die IOPS-Leistung eines verwalteten Premium-Datenträgers hängen sowohl von der SKU als auch von der Instanzgröße der Knoten im AKS-Cluster ab. Weitere Informationen finden Sie unter [Verwaltete Datenträger – Preise und Leistung][managed-disk-pricing-performance].

Die ID der Datenträgerressource wird angezeigt, sobald der Befehl erfolgreich abgeschlossen wurde, wie in der folgenden Beispielausgabe gezeigt. Diese Datenträger-ID wird verwendet, um den Datenträger im nächsten Schritt bereitzustellen.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Einbinden des Datenträgers als Volume

Um den Azure-Datenträger in Ihren Pod einzubinden, konfigurieren Sie das Volume in der Containerspezifikation. Erstellen Sie eine neue Datei namens „`azure-disk-pod.yaml`“ mit folgendem Inhalt. Aktualisieren Sie `diskName` mit dem Namen des im vorherigen Schritt erstellten Datenträgers und `diskURI` mit der in der Ausgabe des Befehls „disk create“ angezeigten Datenträger-ID. Wenn gewünscht, aktualisieren Sie auch den Wert `mountPath`. Dies ist der Pfad, unter dem der Azure-Datenträger im Pod eingebunden wird. Geben Sie für Windows Server-Container einen *mountPath* gemäß Windows-Pfadkonvention an, z. B. *D:* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Um den Pod zu erstellen, verwenden Sie den Befehl `kubectl`.

```console
kubectl apply -f azure-disk-pod.yaml
```

Sie verfügen nun über einen ausgeführten Pod mit einem Azure-Datenträger, der unter `/mnt/azure` eingebunden wurde. Sie können mit `kubectl describe pod mypod` überprüfen, ob der Datenträger erfolgreich eingebunden wurde. In der folgenden verkürzten Beispielausgabe ist das im Container eingebundene Volume angegeben:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Nächste Schritte

Entsprechenden bewährte Methoden finden Sie unter [Bewährte Methoden für die Speicherung und Sicherungen in AKS][operator-best-practices-storage].

Weitere Informationen zur Interaktion von AKS-Clustern mit Azure-Datenträgern finden Sie unter [Kubernetes-Plug-In für Azure-Datenträger][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
