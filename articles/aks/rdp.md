---
title: RDP-Verbindung mit AKS-Windows Server-Knoten
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie eine RDP-Verbindung mit Windows Server-Knoten in Azure Kubernetes Service-Clustern (AKS) zur Problembehandlung und für Wartungsaufgaben erstellen.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 4f221a98adb779d32fc5567b201699672c8b90f5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183276"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Herstellen einer RDP-Verbindung mit Windows Server-Knoten in Azure Kubernetes Service-Clustern (AKS) zur Wartung oder Problembehandlung

Während des Lebenszyklus des Azure Kubernetes Service-Clusters (AKS) müssen Sie möglicherweise auf einen AKS-Windows Server-Knoten zugreifen. Dieser Zugriff kann zur Wartung, Protokollsammlung oder für andere Vorgänge der Problembehandlung erforderlich sein. Sie können auf den AKS-Windows Server-Knoten über RDP zugreifen. Wenn Sie mit SSH auf den AKS-Windows Server-Knoten zugreifen möchten und Zugriff auf das gleiche Schlüsselpaar haben, das während der Clustererstellung verwendet wurde, können Sie alternativ die Schritte in [Zugreifen per SSH auf Azure Kubernetes Service-Clusterknoten (AKS)][ssh-steps] ausführen. Aus Sicherheitsgründen werden die AKS-Knoten nicht im Internet verfügbar gemacht.

In diesem Artikel wird gezeigt, wie Sie eine RDP-Verbindung mit einem AKS-Knoten über die privaten IP-Adressen erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über einen AKS-Cluster mit einem Windows Server-Knoten verfügen. Wenn Sie einen AKS-Cluster benötigen, lesen Sie den Artikel [Erstellen eines Windows Server-Containers in einem AKS-Cluster (Azure Kubernetes Service) mit der Azure-Befehlszeilenschnittstelle][aks-windows-cli]. Sie benötigen Windows-Administratorbenutzernamen und -kennwort für den Windows Server-Knoten, bei dem Sie ein Problem behandeln möchten. Wenn Sie diese nicht kennen, können Sie sie anhand der Anweisungen unter [Zurücksetzen von Remotedesktopdienste oder dem zugehörigen Administratorkennwort auf einer Windows-VM](../virtual-machines/troubleshooting/reset-rdp.md) zurücksetzen. Sie benötigen auch einen RDP-Client wie z. B. [Microsoft Remotedesktop][rdp-mac].

Außerdem muss mindestens die Version 2.0.61 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Bereitstellen eines virtuellen Computers in dem gleichen Subnetz, in dem sich Ihr Cluster befindet

Die Windows Server-Knoten Ihres AKS-Clusters haben keine IP-Adressen für externen Zugriff. Um eine RDP-Verbindung herzustellen, können Sie einen virtuellen Computer mit einer öffentlich zugänglichen IP-Adresse in dem gleichen Subnetz bereitstellen, in dem sich Ihre Windows Server-Knoten befinden.

Im folgenden Beispiel wird eine VM mit dem Namen *myVM* in der Ressourcengruppe *myResourceGroup* erstellt.

Rufen Sie zuerst das Subnetz ab, das von Ihrem Windows Server-Knotenpool verwendet wird. Um die Subnetz-ID zu erhalten, benötigen Sie den Namen des Subnetzes. Um den Namen des Subnetzes zu erhalten, benötigen Sie den Namen des virtuellen Netzes. Den Namen des virtuellen Netzes rufen Sie ab, indem Sie Ihren Cluster nach der Liste der Netzwerke abfragen. Um den Cluster abzufragen, benötigen Sie seinen Namen. Alle diese Angaben erhalten Sie durch Ausführen des folgenden Befehls in Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nachdem Sie nun über SUBNET_ID verfügen, führen Sie den folgenden Befehl im gleichen Azure Cloud Shell-Fenster aus, um den virtuellen Computer zu erstellen:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Die folgende Beispielausgabe zeigt, dass der virtuelle Computer erfolgreich erstellt wurde, und zeigt die öffentliche IP-Adresse des virtuellen Computers an.

```console
13.62.204.18
```

Notieren Sie sich die öffentliche IP-Adresse des virtuellen Computers. Sie verwenden diese Adresse in einem späteren Schritt.

## <a name="allow-access-to-the-virtual-machine"></a>Zulassen des Zugriffs auf den virtuellen Computer

Subnetze mit AKS-Knotenpools werden standardmäßig mit NSGs (Netzwerksicherheitsgruppen) geschützt. Um Zugriff auf den virtuellen Computer zu erhalten, müssen Sie den Zugriff in der NSG aktivieren.

> [!NOTE]
> Die NSGs werden vom AKS-Dienst gesteuert. Alle an der NSG vorgenommenen Änderungen werden jederzeit von der Steuerungsebene überschrieben.
>

Rufen Sie zunächst die Ressourcengruppe sowie den NSG-Namen der NSG ab, der die Regel hinzugefügt werden soll:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Erstellen Sie dann die NSG-Regel:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Abrufen der Knotenadresse

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Listen Sie mithilfe des Befehls [kubectl get][kubectl-get] die interne IP-Adresse der Windows Server-Knoten auf:

```console
kubectl get nodes -o wide
```

Die folgende Beispielausgabe zeigt die internen IP-Adressen aller Knoten in dem Cluster einschließlich der Windows Server-Knoten an.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Notieren Sie sich zur Problembehandlung die interne IP-Adresse des Windows Server-Knotens. Sie verwenden diese Adresse in einem späteren Schritt.

## <a name="connect-to-the-virtual-machine-and-node"></a>Herstellen einer Verbindung mit dem virtuellen Computer und Knoten

Stellen Sie mit einem RDP-Client wie z.B. [Microsoft-Remotedesktop][rdp-mac] eine Verbindung mit der öffentlichen IP-Adresse des virtuellen Computers her, den Sie zuvor erstellt haben.

![Abbildung des Herstellens der Verbindung mit dem virtuellen Computer über einen RDP-Client](media/rdp/vm-rdp.png)

Nachdem Sie eine Verbindung mit dem virtuellen Computer hergestellt haben, stellen Sie mit einem RDP-Client aus Ihrem virtuellen Computer eine Verbindung mit der *internen IP-Adresse* des Windows Server-Knotens her.

![Abbildung des Herstellens der Verbindung mit dem Windows Server-Knoten über einen RDP-Client](media/rdp/node-rdp.png)

Sie sind jetzt mit Ihrem Windows Server-Knoten verbunden.

![Abbildung des Befehlszeilenfensters im Windows Server-Knoten](media/rdp/node-session.png)

Sie können nun alle Befehle zur Problembehandlung im *CMD*-Fenster ausführen. Da Windows Server-Knoten Windows Server Core verwenden, gibt es keine vollständige GUI oder andere GUI-Tools, wenn Sie per RDP eine Verbindung mit einem Windows Server-Knoten herstellen.

## <a name="remove-rdp-access"></a>Entfernen des RDP-Zugriffs

Abschließend beenden Sie die RDP-Verbindung mit dem Windows Server-Knoten und dann die RDP-Sitzung mit dem virtuellen Computer. Nachdem Sie beide RDP-Sitzungen beendet haben, löschen Sie den virtuellen Computer mit dem Befehl [az vm delete][az-vm-delete]:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

Und die NSG-Regel:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie zusätzliche Problembehandlungsdaten benötigen, können Sie [die Kubernetes-Masterknotenprotokolle anzeigen][view-master-logs] oder [Azure Monitor][azure-monitor-containers] verwenden.

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md