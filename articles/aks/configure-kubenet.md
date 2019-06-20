---
title: Konfigurieren von kubenet-Netzwerken in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie kubenet-Netzwerke (grundlegend) in Azure Kubernetes Service (AKS) konfigurieren, um einen AKS-Cluster in einem vorhandenen virtuellen Netzwerk und Subnetz bereitzustellen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: cde7d692e8bb37e874c6e55e5584d96e3b13af31
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497187"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)

Standardmäßig verwenden AKS-Cluster [kubenet][kubenet], und ein virtuelles Azure-Netzwerk sowie ein Subnetz werden für Sie erstellt. Mit *kubenet* erhalten Knoten eine IP-Adresse aus dem Subnetz des virtuellen Azure-Netzwerks. Pods erhalten eine IP-Adresse von einem logisch unterschiedlichen Adressraum zum Subnetz des virtuellen Azure-Netzwerks der Knoten. Die Netzwerkadressübersetzung (NAT, Network Address Translation) wird dann so konfiguriert, dass die Pods Ressourcen im virtuellen Azure-Netzwerk erreichen können. Die Quell-IP-Adresse des Datenverkehrs wird mit NAT in die primäre IP-Adresse des Knotens übersetzt. Dieser Ansatz reduziert die Anzahl der IP-Adressen, die Sie in Ihrem Netzwerkadressraum für die Verwendung von Pods reservieren müssen, erheblich.

Mit [Azure Container Networking Interface (CNI)][cni-networking] erhält jeder Pod eine IP-Adresse aus dem Subnetz und kann direkt angesprochen werden. Diese IP-Adressen müssen in Ihrem Netzwerkadressraum eindeutig sein und im Voraus geplant werden. Jeder Knoten verfügt über einen Konfigurationsparameter für die maximale Anzahl von Pods, die er unterstützt. Die entsprechende Anzahl von IP-Adressen pro Knoten wird dann im Voraus für diesen Knoten reserviert. Dieser Ansatz erfordert mehr Planung und führt oft zu einer Erschöpfung der IP-Adresse oder der Notwendigkeit, Cluster in einem größeren Subnetz neu zu erstellen, wenn die Anforderungen Ihrer Anwendung wachsen.

Dieser Artikel veranschaulicht die Verwendung von *kubenet*-Netzwerken zum Erstellen und Verwenden eines virtuellen Netzwerksubnetzes für einen AKS-Cluster. Weitere Informationen zu Netzwerkoptionen und -überlegungen finden Sie unter [Netzwerkkonzepte für Kubernetes und AKS][aks-network-concepts].

> [!WARNING]
> Um Windows Server-Knotenpools (derzeit als Vorschau in AKS) verwenden zu können, müssen Sie Azure CNI verwenden. Die Verwendung von „kubenet“ als das Netzwerkmodell ist für Windows Server-Container nicht verfügbar.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.65 oder höher muss installiert und konfiguriert sein. Führen Sie  `az --version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie weitere Informationen unter [Installieren der Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Übersicht über kubenet-Netzwerke mit eigenem Subnetz

In vielen Umgebungen gibt es definierte virtuelle Netzwerke und Subnetze mit zugeordneten IP-Adressbereichen. Diese Ressourcen des virtuellen Netzwerks werden verwendet, um mehrere Dienste und Anwendungen zu unterstützen. Um Netzwerkkonnektivität zu gewährleisten, können AKS-Cluster *kubenet* (grundlegender Netzwerkbetrieb) oder Azure CNI (*erweiterter Netzwerkbetrieb*) verwenden.

Mit *kubenet* erhalten nur die Knoten eine IP-Adresse im Subnetz des virtuellen Netzwerks. Pods können nicht direkt miteinander kommunizieren. Stattdessen werden benutzerdefiniertes Routing (User Defined Routing, UDR) und IP-Weiterleitung für die Konnektivität zwischen Pods über Knoten verwendet. Sie können auch Pods hinter einem Dienst bereitstellen, der eine zugewiesene IP-Adresse empfängt und einen Lastenausgleich des Datenverkehrs für die Anwendung durchführt. Das folgende Diagramm zeigt, wie die AKS-Knoten eine IP-Adresse im Subnetz des virtuellen Netzwerks empfangen, aber nicht die Pods:

![Kubenet-Netzwerkmodell mit einem AKS-Cluster](media/use-kubenet/kubenet-overview.png)

Azure unterstützt maximal 400 Routen in einer UDR, also können Sie keinen AKS-Cluster mit mehr als 400 Knoten haben. AKS-Features wie z.B. [virtuelle Knoten][virtual-nodes] oder Netzwerkrichtlinien werden mit *kubenet* nicht unterstützt.

Mit *Azure CNI* empfängt jeder Pod eine IP-Adresse im IP-Subnetz und kann direkt mit anderen Pods und Diensten kommunizieren. Ihre Cluster können so groß wie der IP-Adressbereich sein, den Sie angeben. Allerdings muss der IP-Adressbereich im Voraus geplant werden, und alle IP-Adressen werden von den AKS-Knoten basierend auf der maximalen Anzahl von Pods genutzt, die sie unterstützen können. Erweiterte Netzwerkfeatures und Szenarien wie z.B. [virtuelle Knoten] [ virtual-nodes] oder Netzwerkrichtlinien werden mit *Azure CNI* unterstützt.

### <a name="ip-address-availability-and-exhaustion"></a>IP-Adressenverfügbarkeit und -auslastung

Ein häufiges Problem bei *Azure CNI* ist, dass der zugewiesene IP-Adressbereich zu klein ist, um weitere Knoten hinzuzufügen, wenn Sie einen Cluster skalieren oder upgraden. Das Netzwerkteam kann vielleicht auch keinen IP-Adressbereich zur Verfügung zu stellen, der groß genug ist, Ihre erwarteten Anwendungsanforderungen zu unterstützen.

Als Kompromiss können Sie einen AKS-Cluster erstellen, der *kubenet* verwendet und eine Verbindung mit einem vorhandenen Subnetz eines virtuellen Netzwerks herstellt. Mit diesem Ansatz können die Knoten definierte IP-Adressen empfangen, ohne dass vorab eine große Anzahl von IP-Adressen für alle möglichen Pods reserviert werden muss, die im Cluster ausgeführt werden könnten.

Mit *kubenet* können Sie einen viel kleineren IP-Adressbereich verwenden und große Cluster und Anwendungsanforderungen unterstützen. Sie könnten beispielsweise noch mit einem */27*-IP-Adressbereich einen Cluster mit 20-25 Knoten mit genügend Platz zum Skalieren oder Aktualisieren ausführen. Diese Clustergröße unterstützt bis zu *2.200-2.750* Pods (mit standardmäßig maximal 110 Pods pro Knoten). Die maximale Anzahl von Pods pro Knoten, die Sie mit *Kubenet* in AKS konfigurieren können, ist 250.

Die folgenden grundlegenden Berechnungen zeigen den Unterschied zwischen Netzwerkmodellen im Vergleich:

- **kubenet** – ein einfacher */24*-IP-Adressbereich kann bis zu *251* Knoten im Cluster unterstützen (jedes Subnetz eines virtuellen Azure-Netzwerks reserviert die ersten drei IP-Adressen für Verwaltungsvorgänge).
  - Diese Knotenanzahl könnte bis zu *27.610* Pods unterstützen (mit standardmäßig maximal 110 Pods pro Knoten mit *kubenet*).
- **Azure CNI** – derselbe grundlegende */24*-Subnetzadressbereich könnte nur maximal *8* Knoten im Cluster unterstützen.
  - Diese Knotenanzahl könnte nur bis zu *240* Pods unterstützen (mit standardmäßig maximal 30 Pods pro Knoten mit *Azure CNI*).

> [!NOTE]
> Bei diesen Maximalwerten werden keine Upgrade- oder Skalierungsvorgänge berücksichtigt. In der Praxis können Sie die maximale Anzahl von Knoten, die der Subnetz-IP-Adressbereich unterstützt, nicht ausführen. Sie müssen einige IP-Adressen zur Verwendung während der Skalierungs- oder Upgradevorgänge verfügbar lassen.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering virtueller Netzwerke und ExpressRoute-Verbindungen

Um lokale Konnektivität zu bieten, kann sowohl der *kubenet*- als auch der *Azure CNI*-Netzwerkansatz [Peering in virtuellen Netzwerken][vnet-peering] oder [ExpressRoute-Verbindungen][express-route] verwenden. Planen Sie Ihre IP-Adressbereiche sorgfältig, um Überschneidungen und falsches Datenverkehrsrouting zu verhindern. In vielen lokalen Netzwerken wird z.B. ein *10.0.0.0/8*-Adressbereich verwendet, der über die ExpressRoute-Verbindung angekündigt wird. Sie sollten Ihre AKS-Cluster in Subnetzen virtueller Azure-Netzwerke außerhalb dieses Adressbereichs erstellen, z.B. *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Auswählen eines zu verwendenden Netzwerkmodells

Die Wahl des für Ihren AKS-Cluster zu verwendenden Netzwerk-Plug-Ins ist in der Regel ein Kompromiss zwischen Flexibilität und erweiterten Konfigurationsanforderungen. Die folgenden Überlegungen helfen bei der Entscheidung, welches Netzwerkmodell sich wohl am besten eignet.

Verwenden Sie *kubenet* unter folgenden Bedingungen:

- Ihr IP-Adressraum ist beschränkt.
- Die Podkommunikation findet überwiegend innerhalb des Clusters statt.
- Erweiterte Features wie virtuelle Knoten oder Netzwerkrichtlinie sind nicht erforderlich.

Verwenden Sie *Azure CNI* unter folgenden Bedingungen:

- Sie haben genügend verfügbaren IP-Adressraum.
- Podkommunikation findet überwiegend mit außerhalb des Clusters befindlichen Ressourcen statt.
- Sie möchten die UDRs nicht verwalten.
- Sie benötigen erweiterte Features wie virtuelle Knoten oder Netzwerkrichtlinie.

> [!NOTE]
> Kuberouter ermöglicht das Aktivieren der Netzwerkrichtlinie bei Verwendung von kubenet und kann als Daemonset in einem AKS-Cluster installiert werden. Bedenken Sie dabei, dass Kuberouter sich noch in der Betaphase befindet und von Microsoft kein Support für das Projekt geleistet wird.

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes

Erstellen Sie für den Einstieg in die Verwendung von *kubenet* und Ihrem eigenen Subnetz des virtuellen Netzwerks zunächst eine Ressourcengruppe mit dem Befehl [az group create] [ az-group-create]. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wenn Ihnen kein Subnetz eines virtuellen Netzwerks zur Verfügung steht, erstellen Sie diese Netzwerkressourcen mit dem Befehl [az network vnet create] [ az-network-vnet-create]. Im folgenden Beispiel erhält das virtuelle Netzwerk den Namen *myVnet* und das Adresspräfix *10.0.0.0/8*. Ein Subnetz mit dem Namen *myAKSSubnet* mit dem Adresspräfix *10.240.0.0/16* wird erstellt.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Erstellen eines Dienstprinzipals und Zuweisen von Berechtigungen

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird ein Azure Active Directory-Dienstprinzipal verwendet. Der Dienstprinzipal muss über Berechtigungen zum Verwalten des virtuellen Netzwerks und Subnetzes verfügen, das der AKS-Knoten verwendet. Verwenden Sie zum Erstellen eines Dienstprinzipals den Befehl [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Die folgende Beispielausgabe zeigt Anwendungs-ID und Kennwort für Ihren Dienstprinzipal. Diese Werte werden in zusätzlichen Schritten zum Zuweisen einer Rolle für den Dienstprinzipal und anschließendes Erstellen des AKS-Clusters verwendet:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Um in den weiteren Schritten die richtigen Delegierungen zuzuweisen, verwenden Sie die Befehle [az network vnet show][az-network-vnet-show] und [az network vnet subnet show][az-network-vnet-subnet-show], um die erforderlichen Ressourcen-IDs zu erhalten. Diese Ressourcen-IDs werden als Variablen gespeichert, und auf sie wird in den verbleibenden Schritten verwiesen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Weisen Sie nun dem Dienstprinzipal für den AKS-Cluster *Mitwirkender*-Berechtigungen für das virtuelle Netzwerk mithilfe des Befehls [az role assignment create][az-role-assignment-create] zu. Geben Sie Ihre eigene *\<App-ID>* wie in der Ausgabe des vorherigen Befehls an, um den Dienstprinzipal zu erstellen:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Erstellen eines AKS-Clusters im virtuellen Netzwerk

Sie haben jetzt ein virtuelles Netzwerk und Subnetz erstellt sowie Berechtigungen für einen Dienstprinzipal zur Verwendung dieser Netzwerkressourcen erstellt und zugewiesen. Erstellen Sie nun mithilfe des Befehls [az aks erstellen][az-aks-create] einen AKS-Cluster in Ihrem virtuellen Netzwerk und Subnetz. Definieren Sie wie in der Ausgabe des vorherigen Befehls Ihre eigene Dienstprinzipal- *\<>* und Ihr eigenes *\<Kennwort>* , um den Dienstprinzipal zu erstellen.

Die folgenden IP-Adressbereiche sind auch als Teil des Clustererstellungsprozesses definiert:

* Mit *--service-cidr* werden interne Dienste im AKS-Cluster einer IP-Adresse zugewiesen. Dieser IP-Adressbereich muss ein Adressraum sein, der nicht an anderer Stelle in Ihrer Netzwerkumgebung verwendet wird. Dieser Bereich schließt alle lokalen Netzwerkbereiche ein, wenn Sie mit ExpressRoute oder Site-to-Site-VPN-Verbindungen eine Verbindung Ihrer virtuellen Azure-Netzwerke herstellen möchten oder dies planen.

* Die *--dns-service-ip*-Adresse muss die *10.* Adresse Ihres Dienst-IP-Adressbereichs sein.

* *--pod-cidr* muss ein großer Adressraum sein, der nicht an anderer Stelle in Ihrer Netzwerkumgebung verwendet wird. Dieser Bereich schließt alle lokalen Netzwerkbereiche ein, wenn Sie mit ExpressRoute oder Site-to-Site-VPN-Verbindungen eine Verbindung Ihrer virtuellen Azure-Netzwerke herstellen möchten oder dies planen.
    * Dieser Adressbereich muss groß genug sein für die Anzahl der Knoten, auf die Sie erwartungsgemäß zentral hochskalieren werden. Sie können diesen Adressbereich nicht ändern, nachdem der Cluster bereitgestellt wurde, wenn Sie mehrere Adressen für zusätzliche Knoten benötigen.
    * Mit dem Pod-IP-Adressbereich wird jedem Knoten im Cluster ein */24*-Adressraum zugewiesen. Im folgenden Beispiel weist *--pod-cidr* von *192.168.0.0/16* dem ersten Knoten *192.168.0.0/24* zu, dem zweiten Knoten *192.168.1.0/24* und dem dritten Knoten *192.168.2.0/24*.
    * Beim Skalieren oder Upgraden des Clusters weist die Azure-Plattform weiterhin jedem neuen Knoten einen Pod-IP-Adressbereich zu.
    
* Über *--docker-bridge-address* können AKS-Knoten mit der zugrunde liegenden Verwaltungsplattform kommunizieren. Diese IP-Adresse darf nicht innerhalb des IP-Adressbereichs des virtuellen Netzwerk Ihres Clusters liegen und sollte sich nicht mit anderen in Ihrem Netzwerk verwendeten Adressbereichen überschneiden.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Wenn Sie einen AKS-Cluster erstellen, werden eine Netzwerksicherheitsgruppe und Routingtabelle erstellt. Diese Netzwerkressourcen werden von der AKS-Steuerungsebene verwaltet. Die Netzwerksicherheitsgruppe wird automatisch den virtuellen NICs auf Ihren Knoten zugeordnet. Die Routingtabelle wird automatisch dem Subnetz des virtuellen Netzwerks zugeordnet. Regeln für Netzwerksicherheitsgruppen und Routingtabellen werden beim Erstellen und Verfügbarmachen von Diensten automatisch aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Da jetzt ein AKS-Cluster in Ihrem vorhandenen Subnetz des virtuellen Netzwerks bereitgestellt ist, können Sie den Cluster jetzt wie gewohnt verwenden. Informationen zu den ersten Schritten finden Sie unter [Azure Dev Spaces – Schnelle, iterative Kubernetes-Bereitstellung für Teams][dev-spaces], [Verwenden von Draft mit Azure Kubernetes Service (AKS)][use-draft] oder [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
