---
title: Erstellen eines Azure Private Link-Diensts mithilfe der Azure CLI
description: Informationen zum Erstellen eines Azure Private Link-Diensts mithilfe der Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfffafaab2e2d4ef6b165ef03beb827342c94608
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96018051"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Erstellen eines Private Link-Diensts mithilfe der Azure CLI
In diesem Artikel erfahren Sie, wie Sie mit der Azure CLI einen Private Link-Dienst in Azure erstellen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist die aktuelle Azure CLI-Version erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-private-link-service"></a>Erstellen eines Private Link-Diensts
### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie ein virtuelles Netzwerk erstellen können, müssen Sie eine Ressourcengruppe zum Hosten des virtuellen Netzwerks erstellen. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Bei diesem Beispiel wird am Standort *westcentralus* eine Ressourcengruppe namens *myResourceGroup* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) ein virtuelles Netzwerk. Bei diesem Beispiel wird ein virtuelles Standardnetzwerk mit dem Namen *myVirtualNetwork* und dem Subnetz *mySubnet* erstellt:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Erstellen eines Subnetzes
Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Subnetz für das virtuelle Netzwerk. Bei diesem Beispiel wird das Subnetz *mySubnet* im virtuellen Netzwerk *myVirtualNetwork* erstellt:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Erstellen eines internen Load Balancers 
Erstellen Sie mit [az network lb create](/cli/azure/network/lb#az-network-lb-create) einen internen Load Balancer. Bei diesem Beispiel wird ein interner Load Balancer mit dem Namen *myILB* in der Ressourcengruppe *myResourceGroup* erstellt. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Erstellen des Integritätstests für den Load Balancer

Ein Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass diese Netzwerkdatenverkehr empfangen können. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet werden und beim Test überprüft wurde, dass sie fehlerfrei sind. Erstellen Sie mit [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest) einen Integritätstest zur Überwachung der Integrität von virtuellen Computern. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mit einer Lastenausgleichsregel wird die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport definiert. Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest) eine Lastenausgleichsregel mit dem Namen *myHTTPRule*, die an Port 80 des Front-End-Pools *myFrontEnd* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *myBackEndPool* sendet, wobei ebenfalls der Port 80 verwendet wird. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

Bei diesem Beispiel wird das Erstellen virtueller Computer nicht behandelt. Sie können die Schritte unter [Schnellstart: Erstellen eines internen Load Balancers für den Lastenausgleich virtueller Computer mit der Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) befolgen, um zwei virtuelle Computer zu erstellen, die als Back-End-Server für den Load Balancer verwendet werden. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Deaktivieren von Netzwerkrichtlinien für einen Private Link-Dienst im Subnetz 
Der Private Link-Dienst benötigt innerhalb eines virtuellen Netzwerks eine IP-Adresse aus einem beliebigen Subnetz Ihrer Wahl. Derzeit werden für diese IP-Adressen keine Netzwerkrichtlinien unterstützt.  Daher müssen wir die Netzwerkrichtlinien für das Subnetz deaktivieren. Aktualisieren Sie das Subnetz mit [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update), um Netzwerkrichtlinien für den Privat Link-Dienst zu deaktivieren.

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Erstellen eines Private Link-Diensts mithilfe von Load Balancer Standard 
 
Erstellen Sie mit [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) einen Private Link-Dienst unter Verwendung der Front-End-IP-Konfiguration von Load Balancer Standard. Bei diesem Beispiel wird ein Private Link-Dienst mit dem Namen *myPLS* unter Verwendung einer Load Balancer Standard-Instanz mit dem Namen *myLoadBalancer* in der Ressourcengruppe *myResourceGroup* erstellt. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Notieren Sie sich nach der Erstellung die ID des Private Link-Diensts. Sie benötigen diese später zum Anfordern der Verbindung mit diesem Dienst.  
 
In dieser Phase ist Ihr Private Link-Dienst erfolgreich erstellt und bereit, den Datenverkehr zu empfangen. Beachten Sie, dass das obige Beispiel nur zur Veranschaulichung der Erstellung des Private Link-Diensts mithilfe der Azure CLI dient.  Wir haben weder die Load Balancer-Back-End-Pools noch Anwendungen in den Back-End-Pools konfiguriert, um auf den Datenverkehr zu lauschen. Wenn Sie den gesamten Datenverkehrsfluss verfolgen möchten, sollten Sie Ihre Anwendung unbedingt hinter Ihrer Load Balancer Standard-Instanz konfigurieren.  
 
Als Nächstes veranschaulichen wir, wie dieser Dienst mithilfe der Azure CLI einem privaten Endpunkt in einem anderen virtuellen Netzwerk zugeordnet wird. Wiederum ist das Beispiel auf die Erstellung des privaten Endpunkts und das Herstellen einer Verbindung mit dem zuvor mit der Azure CLI erstellten Private Link-Dienst beschränkt. Darüber hinaus können Sie im virtuellen Netzwerk virtuelle Computer zum Senden/Empfangen von Datenverkehr an den privaten Endpunkt erstellen.        
 
## <a name="private-endpoints"></a>Private Endpunkte

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks 
Erstellen Sie mit  [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) ein virtuelles Netzwerk. In diesem Beispiel wird ein virtuelles Netzwerk mit dem Namen  *myPEVNet*  in der Ressourcengruppe *myResourcegroup* erstellt: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Erstellen des Subnetzes 
Erstellen Sie mit  [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Subnetz im virtuellen Netzwerk. In diesem Beispiel wird ein virtuelles Netzwerk mit dem Namen  *mySubnet*  im virtuellen Netzwerk *myPEVnet* in der Ressourcengruppe *myResourcegroup* erstellt: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Deaktivieren von Netzwerkrichtlinien für private Endpunkte im Subnetz 
Ein privater Endpunkt kann in einem beliebigen Subnetz Ihrer Wahl innerhalb eines virtuellen Netzwerks erstellt werden. Derzeit werden für private Endpunkte keine Netzwerkrichtlinien unterstützt.  Daher müssen wir die Netzwerkrichtlinien für das Subnetz deaktivieren. Aktualisieren Sie das Subnetz mit [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update), um Netzwerkrichtlinien für private Endpunkte zu deaktivieren. 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Erstellen eines privaten Endpunkts und Verbinden mit einem Private Link-Dienst 
Erstellen Sie einen privaten Endpunkt zur Nutzung des Private Link-Diensts, der zuvor in Ihrem virtuellen Netzwerk erstellt wurde:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Sie können mit `az network private-link-service show` die *private-connection-resource-id* für den Private Link-Dienst abrufen. Die ID sieht wie folgt aus:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Anzeigen von Private Link-Dienstverbindungen 
 
Zeigen Sie mit [az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show) Verbindungsanforderungen für Ihren Private Link-Dienst an.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum [Azure Private Link-Dienst](private-link-service-overview.md)
