---
title: Konfigurieren von kubenet-Netzwerken in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie kubenet-Netzwerke (grundlegend) in Azure Kubernetes Service (AKS) konfigurieren, um einen AKS-Cluster in einem vorhandenen virtuellen Netzwerk und Subnetz bereitzustellen.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 6cb083e823583105f04aaa59a99357b2b2b2426b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034053"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)

Standardmäßig verwenden AKS-Cluster [kubenet][kubenet]. Ein virtuelles Azure-Netzwerk sowie ein Subnetz werden für Sie erstellt. Mit *kubenet* erhalten Knoten eine IP-Adresse aus dem Subnetz des virtuellen Azure-Netzwerks. Pods erhalten eine IP-Adresse von einem logisch unterschiedlichen Adressraum zum Subnetz des virtuellen Azure-Netzwerks der Knoten. Die Netzwerkadressübersetzung (NAT, Network Address Translation) wird dann so konfiguriert, dass die Pods Ressourcen im virtuellen Azure-Netzwerk erreichen können. Die Quell-IP-Adresse des Datenverkehrs wird mit NAT in die primäre IP-Adresse des Knotens übersetzt. Dieser Ansatz reduziert die Anzahl der IP-Adressen, die Sie in Ihrem Netzwerkadressraum für die Verwendung von Pods reservieren müssen, erheblich.

Mit [Azure Container Networking Interface (CNI)][cni-networking] erhält jeder Pod eine IP-Adresse aus dem Subnetz, mit der direkt darauf zugegriffen werden kann. Diese IP-Adressen müssen in Ihrem Netzwerkadressraum eindeutig sein und im Voraus geplant werden. Jeder Knoten verfügt über einen Konfigurationsparameter für die maximale Anzahl von Pods, die er unterstützt. Die entsprechende Anzahl von IP-Adressen pro Knoten wird dann im Voraus für diesen Knoten reserviert. Dieser Ansatz erfordert mehr Planung und führt oft zu einer Erschöpfung der IP-Adresse oder der Notwendigkeit, Cluster in einem größeren Subnetz neu zu erstellen, wenn die Anforderungen Ihrer Anwendung wachsen. Sie können die maximale Anzahl von Pods, die auf einem Knoten bereitgestellt werden können, zum Zeitpunkt der Clustererstellung oder beim Erstellen neuer Knotenpools konfigurieren. Wenn Sie die maximale Anzahl von Pods beim Erstellen neuer Knotenpools nicht angeben, wird der Standardwert 110 für Kubenet verwendet.

Dieser Artikel veranschaulicht die Verwendung von *kubenet*-Netzwerken zum Erstellen und Verwenden eines virtuellen Netzwerksubnetzes für einen AKS-Cluster. Weitere Informationen zu Netzwerkoptionen und -überlegungen finden Sie unter [Netzwerkkonzepte für Kubernetes und AKS][aks-network-concepts].

## <a name="prerequisites"></a>Voraussetzungen

* Das virtuelle Netzwerk des AKS-Clusters muss ausgehende Internetkonnektivität zulassen.
* In einem Subnetz sollte nicht mehr als ein AKS-Cluster erstellt werden.
* AKS-Cluster dürfen für den Adressbereich des Kubernetes-Diensts, den Adressbereich für den Pod oder den Adressbereich für das virtuelle Clusternetzwerk nicht `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16` oder `192.0.2.0/24` verwenden.
* Der vom AKS-Cluster verwendete Dienstprinzipal muss zumindest über die Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md#network-contributor) für das Subnetz in Ihrem virtuellen Netzwerk verfügen. Sie müssen auch über die entsprechenden Berechtigungen verfügen (z. B. „Abonnementbesitzer“), um einen Dienstprinzipal zu erstellen und ihm Berechtigungen zuzuweisen. Wenn Sie eine [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md) anstelle der integrierten Rolle des Netzwerkmitwirkenden definieren möchten, sind die folgenden Berechtigungen erforderlich:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Um Windows Server-Knotenpools verwenden zu können, müssen Sie Azure CNI verwenden. Die Verwendung von „kubenet“ als das Netzwerkmodell ist für Windows Server-Container nicht verfügbar.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.65 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Übersicht über kubenet-Netzwerke mit eigenem Subnetz

In vielen Umgebungen gibt es definierte virtuelle Netzwerke und Subnetze mit zugeordneten IP-Adressbereichen. Diese Ressourcen des virtuellen Netzwerks werden verwendet, um mehrere Dienste und Anwendungen zu unterstützen. Um Netzwerkkonnektivität zu gewährleisten, können AKS-Cluster *kubenet* (grundlegender Netzwerkbetrieb) oder Azure CNI (*erweiterter Netzwerkbetrieb*) verwenden.

Mit *kubenet* erhalten nur die Knoten eine IP-Adresse im Subnetz des virtuellen Netzwerks. Pods können nicht direkt miteinander kommunizieren. Stattdessen werden benutzerdefiniertes Routing (User Defined Routing, UDR) und IP-Weiterleitung für die Konnektivität zwischen Pods über Knoten verwendet. Standardmäßig werden UDRs und die IP-Weiterleitungskonfiguration vom AKS-Dienst erstellt und verwaltet, Sie haben jedoch die Möglichkeit, eine [eigene Routingtabelle für benutzerdefinierte Routenverwaltung zu verwenden][byo-subnet-route-table]. Sie können auch Pods hinter einem Dienst bereitstellen, der eine zugewiesene IP-Adresse empfängt und einen Lastenausgleich des Datenverkehrs für die Anwendung durchführt. Das folgende Diagramm zeigt, wie die AKS-Knoten eine IP-Adresse im Subnetz des virtuellen Netzwerks empfangen, aber nicht die Pods:

![Kubenet-Netzwerkmodell mit einem AKS-Cluster](media/use-kubenet/kubenet-overview.png)

Azure unterstützt maximal 400 Routen in einer UDR, also können Sie keinen AKS-Cluster mit mehr als 400 Knoten haben. AKS-Features wie z. B. [virtuelle Knoten][virtual-nodes] und Azure-Netzwerkrichtlinien werden mit *kubenet* nicht unterstützt.  Sie können [Calico-Netzwerkrichtlinien][calico-network-policies] verwenden, da diese mit kubenet unterstützt werden.

Mit *Azure CNI* empfängt jeder Pod eine IP-Adresse im IP-Subnetz und kann direkt mit anderen Pods und Diensten kommunizieren. Ihre Cluster können so groß wie der IP-Adressbereich sein, den Sie angeben. Allerdings muss der IP-Adressbereich im Voraus geplant werden, und alle IP-Adressen werden von den AKS-Knoten basierend auf der maximalen Anzahl von Pods genutzt, die sie unterstützen können. Erweiterte Netzwerkfeatures und -szenarien wie z. B. [virtuelle Knoten][virtual-nodes] oder Netzwerkrichtlinien (entweder Azure oder Calico) werden mit *Azure CNI* unterstützt.

### <a name="limitations--considerations-for-kubenet"></a>Einschränkungen und Erwägungen für kubenet

* Beim Entwurf von kubenet ist ein zusätzlicher Hop erforderlich, wodurch der Pod-Kommunikation eine geringfügige Wartezeit hinzugefügt wird.
* Routingtabellen und benutzerdefinierte Routen sind für die Verwendung von kubenet erforderlich, wodurch die Vorgänge komplexer werden.
* Direkte Pod-Adressierung wird aufgrund des kubenet-Designs für kubenet nicht unterstützt.
* Im Gegensatz zu Azure CNI-Clustern können mehrere kubenet-Clustern Subnetze nicht gemeinsam verwenden.
* Zu den **in kubenet nicht unterstützten** Funktionen gehören:
   * [Azure-Netzwerkrichtlinien](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), aber Calico-Netzwerkrichtlinien werden in kubenet unterstützt.
   * [Windows-Knotenpools](./windows-faq.md)
   * [Add-On für virtuelle Knoten](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>IP-Adressenverfügbarkeit und -auslastung

Ein häufiges Problem bei *Azure CNI* ist, dass der zugewiesene IP-Adressbereich zu klein ist, um weitere Knoten hinzuzufügen, wenn Sie einen Cluster skalieren oder upgraden. Das Netzwerkteam kann vielleicht auch keinen IP-Adressbereich zur Verfügung zu stellen, der groß genug ist, Ihre erwarteten Anwendungsanforderungen zu unterstützen.

Als Kompromiss können Sie einen AKS-Cluster erstellen, der *kubenet* verwendet und eine Verbindung mit einem vorhandenen Subnetz eines virtuellen Netzwerks herstellt. Mit diesem Ansatz können die Knoten definierte IP-Adressen empfangen, ohne dass vorab eine große Anzahl von IP-Adressen für alle möglichen Pods reserviert werden muss, die im Cluster ausgeführt werden könnten.

Mit *kubenet* können Sie einen viel kleineren IP-Adressbereich verwenden und große Cluster und Anwendungsanforderungen unterstützen. Sie könnten beispielsweise noch mit einem */27*-IP-Adressbereich in Ihrem Subnetz einen Cluster mit 20-25 Knoten mit genügend Platz zum Skalieren oder Aktualisieren ausführen. Diese Clustergröße unterstützt bis zu *2.200-2.750* Pods (mit standardmäßig maximal 110 Pods pro Knoten). Die maximale Anzahl von Pods pro Knoten, die Sie mit *Kubenet* in AKS konfigurieren können, ist 110.

Die folgenden grundlegenden Berechnungen zeigen den Unterschied zwischen Netzwerkmodellen im Vergleich:

- **kubenet** – ein einfacher */24*-IP-Adressbereich kann bis zu *251* Knoten im Cluster unterstützen (jedes Subnetz eines virtuellen Azure-Netzwerks reserviert die ersten drei IP-Adressen für Verwaltungsvorgänge).
  - Diese Knotenanzahl könnte bis zu *27.610* Pods unterstützen (mit standardmäßig maximal 110 Pods pro Knoten mit *kubenet*).
- **Azure CNI** – derselbe grundlegende */24*-Subnetzadressbereich könnte nur maximal *8* Knoten im Cluster unterstützen.
  - Diese Knotenanzahl könnte nur bis zu *240* Pods unterstützen (mit standardmäßig maximal 30 Pods pro Knoten mit *Azure CNI*).

> [!NOTE]
> Bei diesen Maximalwerten werden keine Upgrade- oder Skalierungsvorgänge berücksichtigt. In der Praxis können Sie die maximale Anzahl von Knoten, die der Subnetz-IP-Adressbereich unterstützt, nicht ausführen. Sie müssen einige IP-Adressen zur Verwendung während der Skalierungs- oder Upgradevorgänge verfügbar lassen.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering virtueller Netzwerke und ExpressRoute-Verbindungen

Um lokale Konnektivität zu bieten, kann sowohl der *kubenet*- als auch der *Azure CNI*-Netzwerkansatz [Peering in virtuellen Netzwerken][vnet-peering] oder [ExpressRoute-Verbindungen][express-route] verwenden. Planen Sie Ihre IP-Adressbereiche sorgfältig, um Überschneidungen und falsches Datenverkehrsrouting zu verhindern. In vielen lokalen Netzwerken wird z.B. ein *10.0.0.0/8*-Adressbereich verwendet, der über die ExpressRoute-Verbindung angekündigt wird. Sie sollten Ihre AKS-Cluster in Subnetzen virtueller Azure-Netzwerke außerhalb dieses Adressbereichs erstellen, z. B. *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Auswählen eines zu verwendenden Netzwerkmodells

Die Wahl des für Ihren AKS-Cluster zu verwendenden Netzwerk-Plug-Ins ist in der Regel ein Kompromiss zwischen Flexibilität und erweiterten Konfigurationsanforderungen. Die folgenden Überlegungen helfen bei der Entscheidung, welches Netzwerkmodell sich wohl am besten eignet.

Verwenden Sie *kubenet* unter folgenden Bedingungen:

- Ihr IP-Adressraum ist beschränkt.
- Die Podkommunikation findet überwiegend innerhalb des Clusters statt.
- Erweiterte AKS-Features wie virtuelle Knoten oder Azure-Netzwerkrichtlinien sind nicht erforderlich.  Verwenden Sie [Calico-Netzwerkrichtlinien][calico-network-policies].

Verwenden Sie *Azure CNI* unter folgenden Bedingungen:

- Sie haben genügend verfügbaren IP-Adressraum.
- Podkommunikation findet überwiegend mit außerhalb des Clusters befindlichen Ressourcen statt.
- Sie möchten keine benutzerdefinierten Routen für Podverbindungen verwalten.
- Erweiterte AKS-Features wie virtuelle Knoten oder Azure-Netzwerkrichtlinien sind erforderlich.  Verwenden Sie [Calico-Netzwerkrichtlinien][calico-network-policies].

Weitere Informationen zur Entscheidung, welches Netzwerkmodell Sie verwenden, finden Sie unter [Vergleich der Netzwerkmodelle und ihres Supportumfang][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes

Erstellen Sie für den Einstieg in die Verwendung von *kubenet* und Ihrem eigenen Subnetz des virtuellen Netzwerks zunächst mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wenn Ihnen kein Subnetz eines virtuellen Netzwerks zur Verfügung steht, erstellen Sie diese Netzwerkressourcen mit dem Befehl [az network vnet create][az-network-vnet-create]. Im folgenden Beispiel erhält das virtuelle Netzwerk den Namen *myVnet* und das Adresspräfix *192.168.0.0/16*. Ein Subnetz mit dem Namen *myAKSSubnet* mit dem Adresspräfix *192.168.1.0/24* wird erstellt.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Erstellen eines Dienstprinzipals und Zuweisen von Berechtigungen

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird ein Azure Active Directory-Dienstprinzipal verwendet. Der Dienstprinzipal muss über Berechtigungen zum Verwalten des virtuellen Netzwerks und Subnetzes verfügen, das der AKS-Knoten verwendet. Verwenden Sie zum Erstellen eines Dienstprinzipals den Befehl [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Die folgende Beispielausgabe zeigt Anwendungs-ID und Kennwort für Ihren Dienstprinzipal. Diese Werte werden in zusätzlichen Schritten zum Zuweisen einer Rolle für den Dienstprinzipal und anschließendes Erstellen des AKS-Clusters verwendet:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Um in den weiteren Schritten die richtigen Delegierungen zuzuweisen, verwenden Sie die Befehle [az network vnet show][az-network-vnet-show] und [az network vnet subnet show][az-network-vnet-subnet-show], um die erforderlichen Ressourcen-IDs abzurufen. Diese Ressourcen-IDs werden als Variablen gespeichert, und auf sie wird in den verbleibenden Schritten verwiesen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Weisen Sie nun dem Dienstprinzipal für den AKS-Cluster mithilfe des Befehls [az role assignment create][az-role-assignment-create] Berechtigungen vom Typ *Netzwerkmitwirkender* für das virtuelle Netzwerk zu. Geben Sie Ihre eigene *\<appId>* wie in der Ausgabe des vorherigen Befehls an, um den Dienstprinzipal zu erstellen:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Erstellen eines AKS-Clusters im virtuellen Netzwerk

Sie haben jetzt ein virtuelles Netzwerk und Subnetz erstellt sowie Berechtigungen für einen Dienstprinzipal zur Verwendung dieser Netzwerkressourcen erstellt und zugewiesen. Erstellen Sie nun mithilfe des Befehls [az aks create][az-aks-create] einen AKS-Cluster in Ihrem virtuellen Netzwerk und Subnetz. Definieren Sie wie in der Ausgabe des vorherigen Befehls Ihre eigene Dienstprinzipal- *\<appId>* und Ihr eigenes *\<password>* , um den Dienstprinzipal zu erstellen.

Die folgenden IP-Adressbereiche sind auch als Teil des Clustererstellungsprozesses definiert:

* Mit *--service-cidr* werden interne Dienste im AKS-Cluster einer IP-Adresse zugewiesen. Dieser IP-Adressbereich sollte ein Adressraum sein, der ansonsten in Ihrer Netzwerkumgebung nicht verwendet wird. Dies schließt auch lokale Netzwerkbereiche ein, wenn Sie Ihre virtuellen Azure-Netzwerke mithilfe von ExpressRoute oder einer Site-to-Site-VPN-Verbindung verbinden (oder dies in Zukunft planen).

* Die *--dns-service-ip*-Adresse muss die *10.* Adresse Ihres Dienst-IP-Adressbereichs sein.

* *--pod-cidr* muss ein großer Adressraum sein, der nicht an anderer Stelle in Ihrer Netzwerkumgebung verwendet wird. Dieser Bereich schließt alle lokalen Netzwerkbereiche ein, wenn Sie mit ExpressRoute oder Site-to-Site-VPN-Verbindungen eine Verbindung Ihrer virtuellen Azure-Netzwerke herstellen möchten oder dies planen.
    * Dieser Adressbereich muss groß genug sein für die Anzahl der Knoten, auf die Sie erwartungsgemäß hochskalieren werden. Sie können diesen Adressbereich nicht ändern, nachdem der Cluster bereitgestellt wurde, wenn Sie mehrere Adressen für zusätzliche Knoten benötigen.
    * Mit dem Pod-IP-Adressbereich wird jedem Knoten im Cluster ein */24*-Adressraum zugewiesen. Im folgenden Beispiel weist *--pod-cidr* von *10.244.0.0/16* dem ersten Knoten *10.244.0.0/24* zu, dem zweiten Knoten *10.244.1.0/24* und dem dritten Knoten *10.244.2.0/24*.
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
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Wenn Sie einen AKS-Cluster zum Einschließen einer [Calico-Netzwerkrichtlinie][calico-network-policies] ermächtigen möchten, können Sie den folgenden Befehl verwenden.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Wenn Sie einen AKS-Cluster erstellen, werden eine Netzwerksicherheitsgruppe und Routingtabelle automatisch erstellt. Diese Netzwerkressourcen werden von der AKS-Steuerungsebene verwaltet. Die Netzwerksicherheitsgruppe wird automatisch den virtuellen NICs auf Ihren Knoten zugeordnet. Die Routingtabelle wird automatisch dem Subnetz des virtuellen Netzwerks zugeordnet. Regeln für Netzwerksicherheitsgruppen und Routingtabellen werden beim Erstellen und Verfügbarmachen von Diensten automatisch aktualisiert.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Einbinden Ihres eigenen Subnetzes und Ihrer eigenen Routingtabelle mit kubenet

Mit kubenet muss eine Routingtabelle in Ihren Clustersubnetzen vorhanden sein. AKS unterstützt das Einbinden Ihres eigenen vorhandenen Subnetzes und Ihrer Routingtabelle.

Falls Ihr benutzerdefiniertes Subnetz keine Routingtabelle enthält, erstellt AKS automatisch eine Routingtabelle und fügt ihr während des gesamten Clusterlebenszyklus Regeln hinzu. Wenn Ihr benutzerdefiniertes Subnetz bei der Clustererstellung bereits eine Routingtabelle enthält, wird diese bei Clustervorgänge von AKS berücksichtigt, und es werden entsprechende Regeln für Cloudanbietervorgänge hinzugefügt/aktualisiert.

> [!WARNING]
> Benutzerdefinierte Regeln können der benutzerdefinierten Routingtabelle hinzugefügt und aktualisiert werden. Vom Kubernetes-Cloudanbieter hinzugefügte Regeln dürfen dagegen nicht aktualisiert oder entfernt werden. Regeln wie „0.0.0.0/0“ müssen in jeder Routingtabelle enthalten und dem Ziel Ihres Internetgateways (beispielsweise einer virtuellen Netzwerkappliance oder einem anderen Ausgangsgateway) zugeordnet sein. Achten Sie beim Aktualisieren von Regeln unbedingt darauf, dass nur Ihre benutzerdefinierten Regeln geändert werden.

Weitere Informationen zur Einrichtung einer benutzerdefinierten Routingtabelle finden Sie [hier][custom-route-table].

Für die erfolgreiche Weiterleitung von Anforderungen in einem Kubenet-Netzwerk sind organisierte Routingtabellenregeln erforderlich. Routingtabellen müssen daher für jeden Cluster, der auf sie angewiesen ist, sorgfältig verwaltet werden. Eine Routingtabelle kann nicht von mehreren Clustern genutzt werden, da sich die Pod-CIDRs verschiedener Cluster möglicherweise überschneiden, was zu unerwartetem und fehlerhaftem Routing führen kann. Wenn Sie mehrere Cluster im gleichen virtuellen Netzwerk konfigurieren oder jedem Cluster ein eigenes virtuelles Netzwerk zuweisen, berücksichtigen Sie die folgenden Einschränkungen.

Einschränkungen:

* Vor der Erstellung des Clusters müssen Berechtigungen zugewiesen werden. Stellen Sie sicher, dass Sie einen Dienstprinzipal mit Schreibberechtigungen für das benutzerdefinierte Subnetz und die benutzerdefinierte Routingtabelle verwenden.
* Vor dem Erstellen des AKS-Clusters muss dem Subnetz eine benutzerdefinierte Routingtabelle zugeordnet werden.
* Die zugeordnete Routingtabellenressource kann nach der Clustererstellung nicht mehr aktualisiert werden. Benutzerdefinierte Regeln in der Routingtabelle können jedoch geändert werden.
* Von jedem AKS-Cluster muss eine einzelne, eindeutige Routingtabelle für alle Subnetze verwendet werden, die dem Cluster zugeordnet sind. Eine Routingtabelle kann nicht von mehreren Clustern genutzt werden, da dies zu Überschneidungen bei Pod-CIDRs sowie zu Konflikten bei Routingregeln führen kann.

Nachdem Sie eine benutzerdefinierte Routingtabelle erstellt und dem Subnetz in Ihrem virtuellen Netzwerk zugeordnet haben, können Sie einen neuen AKS-Cluster erstellen, von dem Ihre Routingtabelle genutzt wird.
Sie müssen die Subnetz-ID für den Ort verwenden, an dem Sie Ihren AKS-Cluster bereitstellen möchten. Dieses Subnetz muss ebenfalls Ihrer benutzerdefinierten Routingtabelle zugeordnet werden.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Nächste Schritte

Da jetzt ein AKS-Cluster in Ihrem vorhandenen Subnetz des virtuellen Netzwerks bereitgestellt ist, können Sie den Cluster jetzt wie gewohnt verwenden. Steigen Sie ein in das [Erstellen von Apps mithilfe von Azure Dev Spaces][dev-spaces], das [Bereitstellen vorhandener Apps mithilfe von Helm][use-helm] oder in das [Erstellen neuer Apps mithilfe von Helm][develop-helm].

<!-- LINKS - External -->
[dev-spaces]: ../dev-spaces/index.yml
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
