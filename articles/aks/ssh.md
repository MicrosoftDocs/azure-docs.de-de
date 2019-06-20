---
title: Zugreifen per SSH auf Azure Kubernetes Service-Clusterknoten (AKS)
description: Erfahren Sie, wie Sie eine SSH-Verbindung mit Azure Kubernetes Service-Clusterknoten (AKS) zur Problembehandlung und für Wartungsaufgaben erstellen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 57eacca75d711c5125a2856a7b6219cd2ec5306b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242030"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Herstellen einer SSH-Verbindung mit Azure Kubernetes Service-Clusterknoten (AKS) zur Wartung oder Problembehandlung

Während des Lebenszyklus des Azure Kubernetes Service-Clusters (AKS) müssen Sie möglicherweise auf einen AKS-Knoten zugreifen. Dieser Zugriff kann zur Wartung, Protokollsammlung oder für andere Vorgänge der Problembehandlung erforderlich sein. Sie können mithilfe von SSH (Secure Shell) auf AKS-Knoten zugreifen, einschließlich Windows Server-Knoten (zurzeit in der Vorschauversion in AKS). Außerdem können Sie [RDP-Verbindungen (Remotedesktopprotokoll) zum Herstellen einer Verbindung zu Windows Server-Knoten verwenden][aks-windows-rdp]. Aus Sicherheitsgründen werden die AKS-Knoten nicht im Internet verfügbar gemacht.

In diesem Artikel wird gezeigt, wie Sie eine SSH-Verbindung mit einem AKS-Knoten über die privaten IP-Adressen erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie noch einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.64 der Azure CLI installiert und konfiguriert sein. Führen Sie  `az --version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie weitere Informationen unter [Installieren der Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Hinzufügen Ihres öffentlichen SSH-Schlüssels

Standardmäßig werden SSH-Schlüssel abgerufen oder generiert und dann beim Erstellen eines AKS-Clusters den Knoten hinzugefügt. Wenn Sie andere SSH-Schlüssel angeben müssen als die, die Sie beim Erstellen Ihres AKS-Clusters verwendet haben, fügen Sie unter Linux Ihren öffentlichen SSH-Schlüssel den AKS-Knoten hinzu. Bei Bedarf können Sie einen SSH-Schlüssel mithilfe von [macOS, Linux][ssh-nix] oder [Windows][ssh-windows] erstellen. Speichern Sie das Schlüsselpaar in einem OpenSSH-Format anstelle des PuTTY-Standardformats für private Schlüssel, wenn Sie PuttyGen zum Erstellen des Schlüsselpaars verwenden.

> [!NOTE]
> Derzeit können SSH-Schlüssel Linux-Knoten nur mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) hinzugefügt werden. Verwenden Sie, wenn Sie Windows Server-Knoten verwenden, die beim Erstellen des AKS-Clusters bereitgestellten SSH-Schlüssel, und fahren Sie mit dem Schritt [Abrufen der AKS-Knotenadresse](#get-the-aks-node-address) fort. Andernfalls können Sie [RDP-Verbindungen (Remotedesktopprotokoll) zum Herstellen einer Verbindung zu Windows Server-Knoten verwenden][aks-windows-rdp].

Die Schritte zum Abrufen der privaten IP-Adresse der AKS-Knoten unterscheidet sich je nach Typ des AKS-Clusters, den Sie ausführen:

* Für die meisten AKS-Cluster können Sie die folgenden Schritte ausführen, um [die IP-Adresse für normale AKS-Cluster abzurufen](#add-ssh-keys-to-regular-aks-clusters).
* [Führen Sie die Schritte für auf VM-Skalierungsgruppen basierenden AKS-Cluster aus](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters), wenn Sie in AKS Previewfunktionen verwenden, die VM-Skalierungsgruppen wie mehrere Knotenpools oder Windows Server-Containerunterstützung verwenden.

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Hinzufügen von SSH-Schlüsseln zu normalen AKS-Clustern

Führen Sie die folgenden Schritte aus, um unter Linux einem AKS-Knoten Ihren SSH-Schlüssel hinzuzufügen:

1. Rufen Sie den Ressourcengruppennamen für Ihre AKS-Clusterressourcen mit [az aks show][az-aks-show] ab. Geben Sie Ihre eigene Hauptressourcengruppe und den Namen Ihres AKS-Clusters an. Der Clustername wird der Variablen namens *CLUSTER_RESOURCE_GROUP* zugewiesen:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Listen Sie die virtuellen Computer in der AKS-Clusterressourcengruppe mit dem Befehl [az vm list][az-vm-list] auf. Diese virtuellen Computer sind Ihre AKS-Knoten:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Die folgende Beispielausgabe zeigt die AKS-Knoten:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Zum Hinzufügen Ihrer SSH-Schlüssel zum Knoten verwenden Sie den Befehl [az vm user update][az-vm-user-update]. Geben Sie den Ressourcengruppennamen und dann einen der AKS-Knoten an, die Sie im vorherigen Schritt abgerufen haben. Der Benutzername für die AKS-Knoten lautet standardmäßig *azureuser*. Geben Sie den Speicherort Ihres eigenen öffentlichen SSH-Schlüssels an, z.B. *~/.ssh/id_rsa.pub*, oder fügen Sie den Inhalt Ihres öffentlichen SSH-Schlüssels ein:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Hinzufügen von SSH-Schlüsseln zu AKS-Clustern, die auf VM-Skalierungsgruppen basieren

Um Ihren SSH-Schlüssel einem Linux AKS-Knoten hinzuzufügen, der Teil einer VM-Skalierungsgruppe ist, führen Sie die folgenden Schritte aus:

1. Rufen Sie den Ressourcengruppennamen für Ihre AKS-Clusterressourcen mit [az aks show][az-aks-show] ab. Geben Sie Ihre eigene Hauptressourcengruppe und den Namen Ihres AKS-Clusters an. Der Clustername wird der Variablen namens *CLUSTER_RESOURCE_GROUP* zugewiesen:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Rufen Sie nun die VM-Skalierungsgruppe für Ihren AKS-Cluster mithilfe des Befehls [az vmss list][az-vmss-list] ab. Der Name der VM-Skalierungsgruppe wird der Variablen namens *SCALE_SET_NAME* zugewiesen:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Um Ihre SSH-Schlüssel den Knoten in einer VM-Skalierungsgruppe hinzuzufügen, verwenden Sie den Befehl [az vmss extension set][az-vmss-extension-set]. Die Clusterressourcengruppe und der Name der VM-Skalierungsgruppe werden anhand der oben genannten Befehle bereitgestellt. Der Benutzername für die AKS-Knoten lautet standardmäßig *azureuser*. Aktualisieren Sie bei Bedarf den Speicherort Ihres eigenen öffentlichen SSH-Schlüssels, z.B. *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Wenden Sie den SSH-Schlüssel mithilfe des Befehls [az vmss update-instances][az-vmss-update-instances] auf die Knoten an:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Abrufen der AKS-Knotenadresse

Die AKS-Knoten werden nicht im Internet öffentlich verfügbar gemacht. Zum Herstellen einer SSH-Verbindung mit den AKS-Knoten verwenden Sie die private IP-Adresse. Im nächsten Schritt erstellen Sie in Ihrem AKS-Cluster einen Hilfspod, mit dem Sie über SSH eine Verbindung mit dieser privaten IP-Adresse des Knotens herstellen können. Die Schritte zum Abrufen der privaten IP-Adresse der AKS-Knoten unterscheidet sich je nach Typ des AKS-Clusters, den Sie ausführen:

* Für die meisten AKS-Cluster können Sie die folgenden Schritte ausführen, um [die IP-Adresse für normale AKS-Cluster abzurufen](#ssh-to-regular-aks-clusters).
* [Führen Sie die Schritte für auf VM-Skalierungsgruppen basierenden AKS-Cluster aus](#ssh-to-virtual-machine-scale-set-based-aks-clusters), wenn Sie in AKS Previewfunktionen verwenden, die VM-Skalierungsgruppen wie mehrere Knotenpools oder Windows Server-Containerunterstützung verwenden.

### <a name="ssh-to-regular-aks-clusters"></a>SSH für normale AKS-Cluster

Zeigen Sie die private IP-Adresse eines AKS-Clusterknotens mit dem Befehl [az vm list-ip-addresses][az-vm-list-ip-addresses] an. Geben Sie den Namen Ihrer eigenen AKS-Clusterressourcengruppe an, den Sie in einem vorherigen Schritt mit [az-aks-show][az-aks-show] abgerufen haben:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Die folgende Beispielausgabe zeigt die privaten IP-Adressen der AKS-Knoten:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH für AKS-Cluster, die auf VM-Skalierungsgruppen basieren

Listen Sie mithilfe des [kubectl get-Befehls][kubectl-get] die interne IP-Adresse der Knoten auf:

```console
kubectl get nodes -o wide
```

Die folgende Beispielausgabe zeigt die interne IP-Adresse aller Knoten in dem Cluster einschließlich eines Windows Server-Knotens an.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Notieren Sie zur Problembehandlung die interne IP-Adresse des Knotens. Sie verwenden diese Adresse in einem späteren Schritt.

## <a name="create-the-ssh-connection"></a>Erstellen der SSH-Verbindung

Zum Erstellen einer SSH-Verbindung mit einem AKS-Knoten führen Sie einen Hilfspod in Ihrem AKS-Cluster aus. Dieser Hilfspod bietet SSH-Zugriff auf den Cluster und dann zusätzlichen SSH-Knotenzugriff. Zum Erstellen und Verwenden dieses Hilfspods führen Sie die folgenden Schritte aus:

1. Führen Sie ein `debian`-Containerimage aus, und fügen Sie eine Terminalsitzung daran an. Dieser Container kann zum Erstellen einer SSH-Sitzung mit einem beliebigen Knoten im AKS-Cluster verwendet werden:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Wenn Sie Windows Server-Knoten verwenden (zurzeit in der Vorschauversion in AKS), fügen Sie dem Befehl einen Knotenselektor hinzu, um die Ausführung eines Debian-Containers in einem Linux-Knoten wie folgt festzulegen:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Das Debian-Basisimage enthält keine SSH-Komponenten. Sobald die Terminalsitzung mit dem Container verbunden ist, installieren Sie einen SSH-Client mit `apt-get` wie folgt:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Listen Sie in einem neuen Terminalfenster, das nicht mit Ihrem Container verbunden ist, die Pods in Ihrem AKS-Cluster mit dem Befehl [kubectl get pods][kubectl-get] auf. Der im vorherigen Schritt erstellte Pod beginnt mit dem Namen *aks-ssh*, wie es im folgenden Beispiel gezeigt wird:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Im ersten Schritt dieses Artikels haben Sie dem AKS-Knoten Ihren öffentlichen SSH-Schlüssel hinzugefügt. Nun kopieren Sie Ihren privaten SSH-Schlüssel in den Pod. Dieser private Schlüssel wird verwendet, um die SSH-Verbindung mit den AKS-Knoten zu erstellen.

    Geben Sie Ihren eigenen *aks-ssh*-Podnamen an, den Sie im vorherigen Schritt erhalten haben. Ändern Sie bei Bedarf *~/.ssh/id_rsa* in den Speicherort Ihres privaten SSH-Schlüssels:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Wenn Sie sich wieder in der Terminalsitzung für Ihren Container befinden, aktualisieren Sie die Berechtigungen für den kopierten privaten SSH-Schlüssel `id_rsa`, sodass er für Benutzer schreibgeschützt ist:

    ```console
    chmod 0600 id_rsa
    ```

1. Erstellen Sie jetzt eine SSH-Verbindung mit Ihrem AKS-Knoten. Auch hier lautet der Standardbenutzername für AKS-Knoten *azureuser*. Bestätigen Sie die Aufforderung zum Fortsetzen der Verbindung, da zuerst die Vertrauenswürdigkeit des SSH-Schlüssels überprüft wird. Anschließend wird die Bash-Eingabeaufforderung Ihres AKS-Knotens angezeigt:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Entfernen des SSH-Zugriffs

Wenn Sie fertig sind, führen Sie `exit` für die SSH-Sitzung und dann `exit` für die interaktive Containersitzung aus. Beim Schließen dieser Containersitzung wird der Pod, der für den SSH-Zugriff vom AKS-Cluster verwendet wurde, gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie zusätzliche Problembehandlungsdaten benötigen, können Sie [die Kubelet-Protokolle anzeigen][view-kubelet-logs] oder [die Kubernetes-Masterknotenprotokolle anzeigen][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
