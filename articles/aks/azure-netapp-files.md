---
title: Integrieren von Azure NetApp Files in Azure Kubernetes Service
description: Erfahren Sie, wie Sie Azure NetApp Files in Azure Kubernetes Service integrieren.
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: bc65c3dfad4c27c1650054c6836fbbbf07a7dbf2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126252"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrieren von Azure NetApp Files in Azure Kubernetes Service

[Azure NetApp Files][anf] ist ein leistungsstarker Dateispeicherdienst auf Unternehmensniveau, der in Azure ausgeführt wird. Dieser Artikel zeigt Ihnen, wie Sie Azure NetApp Files in Azure Kubernetes Service (AKS) integrieren.

## <a name="before-you-begin"></a>Voraussetzungen
Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Ihr AKS-Cluster muss sich auch [in einer Region befinden, die Azure NetApp Files unterstützt][anf-regions].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

### <a name="limitations"></a>Einschränkungen

Bei Verwendung von Azure NetApp Files gelten folgende Einschränkungen:

* Azure NetApp Files ist nur [in ausgewählten Azure-Regionen][anf-regions] verfügbar.
* Bevor Sie Azure NetApp Files verwenden können, muss Ihnen der Zugriff auf den Azure NetApp Files-Dienst gewährt werden. Zum Beantragen des Zugriffs können Sie das [Formular für die Übermittlung einer Azure NetApp Files-Warteliste][anf-waitlist] verwenden. Sie können erst auf den Azure NetApp Files-Dienst zugreifen, wenn Sie die offizielle Bestätigungs-E-Mail vom Azure NetApp Files-Team erhalten haben.
* Nach der ersten Bereitstellung eines AKS-Clusters wird nur die statische Bereitstellung für Azure NetApp Files unterstützt.
* Wenn Sie die dynamische Bereitstellung mit Azure NetApp Files verwenden möchten, installieren und konfigurieren Sie [NetApp Trident](https://netapp-trident.readthedocs.io/) Version 19.07 oder höher.

## <a name="configure-azure-netapp-files"></a>Konfigurieren von Azure NetApp Files

> [!IMPORTANT]
> Bevor Sie den *Microsoft.NetApp* -Ressourcenanbieter registrieren können, müssen Sie das [Formular für die Übermittlung einer Azure NetApp Files-Warteliste][anf-waitlist] für Ihr Abonnement ausfüllen. Sie können die Ressource erst registrieren, nachdem Sie die offizielle Bestätigungs-E-Mail vom Azure NetApp Files-Team erhalten haben.

Registrieren des *Microsoft NetApp* -Ressourcenanbieters:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dieser Vorgang kann einige Zeit in Anspruch nehmen.

Wenn Sie ein Azure NetApp-Konto für die Verwendung mit AKS erstellen, müssen Sie das Konto in der **Knoten** -Ressourcengruppe erstellen. Rufen Sie zunächst den Namen der Ressourcengruppe mit dem Befehl [az aks show][az-aks-show] ab, und fügen Sie den Abfrageparameter `--query nodeResourceGroup` hinzu. Im folgenden Beispiel wird die Knoten-Ressourcengruppe für den AKS-Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Erstellen Sie ein Azure NetApp Files-Konto in **Knoten** -Ressourcengruppe und in derselben Region wie Ihr AKS-Cluster, indem Sie [az netappfiles account create][az-netappfiles-account-create] verwenden. Im folgenden Beispiel wird ein Konto namens *myaccount1* in der Ressourcengruppe *MC_myResourceGroup_myAKSCluster_eastus* und in der Region *eastus* erstellt:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Erstellen Sie mit [az netappfiles pool create][az-netappfiles-pool-create] einen neuen Kapazitätspool. Im folgenden Beispiel wird ein neuer Kapazitätspool namens *mypool1* mit einer Größe von 4 TB und dem Servicelevel *Premium* erstellt:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Erstellen Sie ein Subnetz, um mithilfe von [az network vnet subnet create][az-network-vnet-subnet-create][an Azure NetApp Files zu delegieren][anf-delegate-subnet]. *Dieses Subnetz muss sich dabei im selben virtuellen Netzwerk wie Ihr AKS-Cluster befinden.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Erstellen Sie ein Volume mithilfe von [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Erstellen von PersistentVolume

Listen Sie die Details Ihres Volumes mithilfe von [az netappfiles volume show][az-netappfiles-volume-show] auf.

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Erstellen Sie eine Datei `pv-nfs.yaml`, die ein PersistentVolume definiert. Ersetzen Sie `path` durch das *creationToken* und `server` durch die *ipAddress* aus dem vorherigen Befehl. Beispiel:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aktualisieren Sie *server* und *path* (Pfad) auf die Werte Ihres NFS-Volumes (Network File System), das Sie im vorherigen Schritt erstellt haben. Erstellen Sie das PersistentVolume mit dem Befehl [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pv-nfs.yaml
```

Vergewissern Sie sich, dass der *Status* von PersistentVolume *Verfügbar* ist, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Erstellen von PersistentVolumeClaim

Erstellen Sie eine Datei `pvc-nfs.yaml`, die ein PersistentVolume definiert. Beispiel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] den PersistentVolumeClaim:

```console
kubectl apply -f pvc-nfs.yaml
```

Vergewissern Sie sich, dass der *Status* von PersistentVolumeClaim *Gebunden* ist, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Einbinden mit einem Pod

Erstellen Sie eine Datei `nginx-nfs.yaml`, die einen Pod definiert, der einen PersistentVolumeClaim verwendet. Beispiel:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply].

```console
kubectl apply -f nginx-nfs.yaml
```

Vergewissern Sie sich, dass der Pod *Ausgeführt* wird, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pod nginx-nfs
```

Stellen Sie sicher, dass Ihr Volume in den Pod eingebunden wurde, indem Sie [kubectl exec][kubectl-exec] verwenden, um eine Verbindung mit dem Pod herzustellen, und überprüfen Sie dann mit `df -h`, ob das Volume eingebunden ist.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure NetApp Files finden Sie unter [Was ist Azure NetApp Files?][anf]. Weitere Informationen zum Verwenden von NFS mit AKS finden Sie unter [Manuelles Erstellen und Verwenden eines Volumes eines Linux-NFS-Servers (Network File System) mit Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
