---
title: Erstellen eines privaten Azure Kubernetes Service-Clusters
description: Erfahren Sie, wie Sie einen privaten Azure Kubernetes Service-Cluster (AKS) erstellen.
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 450d68e26c5a3fc1ecfbaf6a3be6b5f698ee65e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183259"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Erstellen eines privaten Azure Kubernetes Service-Clusters

In einem privaten Cluster besitzt die Steuerungsebene oder der API-Server interne IP-Adressen, die im Dokument [RFC1918 – -Allocation for Private Internet](https://tools.ietf.org/html/rfc1918) definiert sind. Durch die Verwendung eines privaten Clusters können Sie sicherstellen, dass der Netzwerkdatenverkehr zwischen Ihrem API-Server und den Knotenpools ausschließlich im privaten Netzwerk verbleibt.

Die Steuerungsebene oder der API-Server befindet sich in einem von Azure Kubernetes Service (AKS) verwalteten Azure-Abonnement. Der Cluster oder Knotenpool eines Kunden befindet sich im Abonnement des Kunden. Der Server und der Cluster oder Knotenpool können über den [Azure Private Link-Dienst][private-link-service] im virtuellen Netzwerk des API-Servers und einen privaten Endpunkt, der im Subnetz des AKS-Clusters des Kunden verfügbar gemacht wird, miteinander kommunizieren.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Ein privater Cluster ist in öffentlichen Regionen, Azure Government-Regionen und Azure China 21ViaNet-Regionen mit [AKS-Unterstützung](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service) verfügbar.

> [!NOTE]
> Azure Government-Standorte werden unterstützt, allerdings wird US Gov Texas derzeit aufgrund von fehlender Unterstützung von Private Link nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Azure CLI, Version 2.2.0 oder höher

## <a name="create-a-private-aks-cluster"></a>Erstellen eines privaten AKS-Clusters

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe für Ihren AKS-Cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standardnetzwerke (Basic) 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dabei gilt: `--enable-private-cluster` ist ein obligatorisches Flag für einen privaten Cluster. 

### <a name="advanced-networking"></a>Erweiterte Netzwerke  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Dabei gilt: `--enable-private-cluster` ist ein obligatorisches Flag für einen privaten Cluster. 

> [!NOTE]
> Wenn CIDR der Docker-Bridge-Adresse (172.17.0.1/16) mit der Subnetz-CIDR in Konflikt steht, ändern Sie die Docker-Bridge-Adresse entsprechend.

## <a name="options-for-connecting-to-the-private-cluster"></a>Optionen zum Herstellen einer Verbindung mit dem privaten Cluster

Der Endpunkt des API-Servers weist keine öffentliche IP-Adresse auf. Zum Verwalten des API-Servers müssen Sie einen virtuellen Computer verwenden, der Zugriff auf das Azure Virtual Network (VNET) des AKS-Clusters hat. Es gibt mehrere Optionen zum Einrichten der Netzwerkkonnektivität mit dem privaten Cluster.

* Erstellen Sie einen virtuellen Computer in demselben Azure Virtual Network (VNET) wie dem des AKS-Clusters.
* Verwenden Sie einen virtuellen Computer in einem separaten Netzwerk, und richten Sie ein [Peering virtueller Netzwerke][virtual-network-peering] ein.  Weitere Informationen zu dieser Option finden Sie im nachstehenden Abschnitt.
* Verwenden Sie eine [ExpressRoute- oder VPN][express-route-or-VPN]-Verbindung.

Das Erstellen eines virtuellen Computers in demselben VNET wie dem des AKS-Clusters ist die einfachste Option.  ExpressRoute und VPNs (virtuelle private Netzwerke) erhöhen die Kosten und erfordern zusätzliche Netzwerkkomplexität.  Beim Peering virtueller Netzwerke müssen Sie Ihre Netzwerk-CIDR-Bereiche planen, um sicherzustellen, dass es keine überlappenden Bereiche gibt.

## <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Wie bereits erwähnt, ist das Peering virtueller Netzwerke eine Möglichkeit für den Zugriff auf Ihren privaten Cluster. Wenn Sie das Peering virtueller Netzwerke verwenden möchten, müssen Sie eine Verknüpfung zwischen dem virtuellen Netzwerk und der privaten DNS-Zone einrichten.
    
1. Wechseln Sie im Azure-Portal zur Knotenressourcengruppe.  
2. Wählen Sie die private DNS-Zone aus.   
3. Wählen Sie im linken Bereich den Link **Virtuelles Netzwerk** aus.  
4. Erstellen Sie eine neue Verknüpfung, um das virtuelle Netzwerk des virtuellen Computers der privaten DNS-Zone hinzuzufügen. Es dauert ein paar Minuten, bis der DNS-Zonenlink verfügbar wird.  
5. Navigieren Sie im Azure-Portal zu der Ressourcengruppe mit dem virtuellen Netzwerk Ihres Clusters.  
6. Wählen Sie im rechten Bereich das virtuelle Netzwerk aus. Der Name des virtuellen Netzwerks hat die Form *aks-vnet-\** .  
7. Wählen Sie im linken Bereich **Peerings** aus.  
8. Wählen Sie **Hinzufügen** aus, fügen Sie das virtuelle Netzwerk des virtuellen Computers hinzu, und erstellen Sie dann das Peering.  
9. Wechseln Sie zu dem virtuellen Netzwerk, in dem sich der virtuelle Computer befindet, wählen Sie **Peerings** und dann das virtuelle AKS-Netzwerk aus, und erstellen Sie dann das Peering. Wenn die Adressbereiche im virtuellen AKS-Netzwerk mit denen im virtuellen Netzwerk des virtuellen Computers in Konflikt stehen, schlägt das Peering fehl. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub-and-Spoke mit benutzerdefiniertem DNS

[Hub-and-Spoke-Architekturen](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) werden in Azure häufig für die Bereitstellung von Netzwerken verwendet. In vielen dieser Bereitstellungen sind die DNS-Einstellungen in den Spoke-VNets so konfiguriert, dass sie auf eine zentrale DNS-Weiterleitung verweisen, um eine lokale und Azure-basierte DNS-Auflösung zu ermöglichen. Beim Bereitstellen eines AKS-Clusters in einer solchen Netzwerkumgebung sind einige besondere Überlegungen zu berücksichtigen.

![Hub-and-Spoke für privaten Cluster](media/private-clusters/aks-private-hub-spoke.png)

1. Wenn ein privater Cluster bereitgestellt wird, werden standardmäßig ein privater Endpunkt (1) und eine private DNS-Zone (2) in der vom Cluster verwalteten Ressourcengruppe erstellt. Der Cluster verwendet einen A-Eintrag in der privaten Zone, um die IP-Adresse des privaten Endpunkts für die Kommunikation mit dem API-Server aufzulösen.

2. Die private DNS-Zone ist nur mit dem virtuellen Netzwerk verbunden, an das die Clusterknoten angefügt sind (3). Dies bedeutet, dass der private Endpunkt nur von Hosts in diesem verknüpften virtuellen Netzwerk aufgelöst werden kann. In Szenarien, in denen im VNET kein benutzerdefiniertes DNS konfiguriert ist (Standardkonfiguration), funktioniert dies problemlos, da die Hosts auf 168.63.129.16 für das DNS verweisen, das Einträge in der privaten DNS-Zone aufgrund der Verknüpfung auflösen kann.

3. In Szenarien, in denen das virtuelle Netzwerk, das Ihren Cluster enthält, über benutzerdefinierte DNS-Einstellungen verfügt (4), schlägt die Clusterbereitstellung fehl, es sei denn, die private DNS-Zone ist mit dem virtuellen Netzwerk verknüpft, das die benutzerdefinierten DNS-Auflösungen enthält (5). Diese Verknüpfung kann manuell erstellt werden, nachdem die private Zone während der Clusterbereitstellung erstellt wurde, oder über die Automatisierung bei Erkennung der Erstellung der Zone mithilfe von ereignisbasierten Bereitstellungsmechanismen (z. B. Azure Event Grid und Azure Functions).

## <a name="dependencies"></a>Abhängigkeiten  

* Der Private Link-Dienst wird nur von Load Balancer Standard unterstützt. Der Load Balancer Basic wird nicht unterstützt.  
* Um einen benutzerdefinierten DNS-Server zu verwenden, fügen Sie die Azure DNS-IP-Adresse 168.63.129.16 als Upstream-DNS-Server im benutzerdefinierten DNS-Server hinzu.

## <a name="limitations"></a>Einschränkungen 
* IP-autorisierte Bereiche können nicht auf den privaten API-Serverendpunkt angewandt werden, sie gelten nur für den öffentlichen API-Server.
* [Verfügbarkeitszonen][availability-zones] werden derzeit für bestimmte Regionen unterstützt. 
* [Die Einschränkungen des Azure Private Link-Diensts][private-link-service] gelten für private Cluster.
* Keine Unterstützung für Azure DevOps Microsoft-gehostete Agents mit privaten Clustern. Erwägen Sie die Verwendung von [selbstgehosteten Agents][devops-agents]. 
* Für Kunden, die Azure Container Registry für die Arbeit mit privatem AKS aktivieren müssen, muss ein Peering des virtuellen Netzwerks der Container Registry mit dem virtuellen Netzwerk des Agent-Clusters ausgeführt werden.
* Keine aktuelle Unterstützung für Azure Dev Spaces
* Keine Unterstützung für das Konvertieren vorhandener AKS-Cluster in private Cluster
* Das Löschen oder Ändern des privaten Endpunkts im Kundensubnetz führt dazu, dass der Cluster nicht mehr funktioniert. 
* Azure Monitor wird für Livedaten von Containern derzeit nicht unterstützt.
* Betriebszeit-SLA wird derzeit nicht unterstützt.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md