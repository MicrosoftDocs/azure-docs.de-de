---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs: Azure CLI'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie über die Azure-Befehlszeilenschnittstelle einen öffentlichen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: b00d0c83758d0349fd3926e0c263b65af2e4dc92
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96021179"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe der Azure CLI

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie mithilfe der Azure CLI einen öffentlichen Lastenausgleich und drei virtuelle Computer erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Schnellstart ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe:

* Benannt als **CreatePubLBQS-rg**. 
* Standort: **eastus**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network---standard"></a>Konfigurieren des virtuellen Netzwerks: Standard

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) ein virtuelles Netzwerk:

* Name: **myVNet**
* Adresspräfix: **10.1.0.0/16**
* Subnetzname: **myBackendSubnet**
* Subnetzpräfix: **10.1.0.0/24**
* In der Ressourcengruppe **CreatePubLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myBastionIP**
* In **CreatePubLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Erstellen eines Bastionsubnetzes

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Bastionsubnetz:

* Name: **AzureBastionSubnet**
* Adresspräfix: **10.1.1.0/24**
* Virtuelles Netzwerk: **myVNet**
* In Ressourcengruppe **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Erstellen eines Bastionhosts

Erstellen Sie mit [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) einen Bastionhost:

* Name: **myBastionHost**
* In **CreatePubLBQS-rg**.
* Der öffentlichen IP-Adresse **myBastionIP** zugeordnet
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Standort: **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für einen Standard-Lastenausgleich müssen die virtuellen Computer unter der Back-End-Adresse über Netzwerkschnittstellen verfügen, die einer Netzwerksicherheitsgruppe angehören. 

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Netzwerksicherheitsgruppe: **myNSG** (aus dem vorherigen Schritt)
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Protokoll **(*)**
* Richtung: **Eingehend**
* Quelle: **(*)**
* Ziel: **(*)**
* Zielport: **Port 80**
* Zugriff: **Zulassen**
* Priorität: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Erstellen von Back-End-Servern: Standard

In diesem Abschnitt wird Folgendes erstellt:

* Drei Netzwerkschnittstellen für die virtuellen Computer
* Drei virtuelle Computer als Back-End-Server für den Lastenausgleich

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Erstellen von Netzwerkschnittstellen für die virtuellen Computer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) drei Netzwerkschnittstellen:

* Namen: **myNicVM1**, **myNicVM2** und **myNicVM3**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) die virtuellen Computer:

### <a name="vm1"></a>VM1
* Name: **myVM1**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 1**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Name: **myVM2**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 2**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Name **myVM3**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM3**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 3**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Die Bereitstellung der virtuellen Computer kann eine Weile dauern.

## <a name="create-a-public-ip-address---standard"></a>Erstellen einer öffentlichen IP-Adresse: Standard

Um über das Internet auf Ihre Web-App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Lastenausgleich. 

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) für Folgendes:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myPublicIP**
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

So erstellen Sie eine zonenredundante öffentliche IP-Adresse in Zone 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Erstellen eines Standard-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [az network lb create](/cli/azure/network/lb#az-network-lb-create) einen öffentlichen Lastenausgleich:

* Name: **myLoadBalancer**
* Front-End-Pool namens **myFrontEnd**
* Back-End-Pool namens **myBackEndPool**
* Zugeordnet zur öffentlichen IP-Adresse **myPublicIP**, die im vorherigen Schritt erstellt wurde 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) eine Lastenausgleichsregel:

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.
* Aktivieren Sie die TCP-Zurücksetzung.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Hinzufügen virtueller Computer zum Back-End-Pool des Lastenausgleichs

Fügen Sie mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) die virtuellen Computer zum Back-End-Pool hinzu:

* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Erstellen der Konfiguration für Ausgangsregeln
Ausgangsregeln für den Lastenausgleich konfigurieren das ausgehende SNAT für virtuelle Computer im Back-End-Pool. 

Weitere Informationen zu ausgehenden Verbindungen finden Sie unter [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).

Für die Konfiguration für ausgehenden Datenverkehr kann eine öffentliche IP-Adresse oder ein entsprechendes Präfix verwendet werden.

### <a name="public-ip"></a>Öffentliche IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), um eine einzelne IP-Adresse für die ausgehende Konnektivität zu erstellen.  

* Name: **myPublicIPOutbound**
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

So erstellen Sie eine zonenredundante öffentliche IP-Adresse in Zone 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Präfix für öffentliche IP-Adressen

Verwenden Sie [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create), um ein öffentliches IP-Präfix für die ausgehende Konnektivität zu erstellen.

* Name: **myPublicIPPrefixOutbound**
* In **CreatePubLBQS-rg**.
* Präfixlänge: **28**

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
So erstellen Sie ein zonenredundantes öffentliche IP-Präfix in Zone 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Weitere Informationen zum Skalieren der NAT für ausgehenden Datenverkehr und der ausgehenden Konnektivität finden Sie unter [Skalieren der NAT für ausgehenden Datenverkehr mit mehreren IP-Adressen](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Erstellen der ausgehenden Front-End-IP-Konfiguration

Erstellen Sie mit [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) eine neue Front-End-IP-Konfiguration:

Verwenden Sie abhängig von Ihrer Entscheidung im vorherigen Schritt die Befehle für die öffentliche IP-Adresse oder die Befehle für das öffentliche IP-Präfix.

#### <a name="public-ip"></a>Öffentliche IP-Adresse

* Name: **myFrontEndOutbound**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zur öffentlichen IP-Adresse **myPublicIPOutbound**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Präfix für öffentliche IP-Adressen

* Name: **myFrontEndOutbound**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum öffentlichen IP-Präfix **myPublicIPPrefixOutbound**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Erstellen eines ausgehenden Pools

Erstellen Sie mit [az network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) einen neuen ausgehenden Pool:

* Name: **myBackEndPoolOutbound**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Erstellen einer Ausgangsregel

Erstellen Sie mit [az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create) eine neue Ausgangsregel für den ausgehenden Back-End-Pool:

* Name: **myOutboundRule**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum Lastenausgleich **myLoadBalancer**
* Zugeordnet zum Front-End **myFrontEndOutbound**
* Protokoll: **Alle**
* Leerlauftimeout: **15**
* Ausgehende Ports: **10000**
* Zugeordnet zum Back-End-Pool **myBackEndPoolOutbound**

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Hinzufügen virtueller Computer zum ausgehenden Pool

Fügen Sie mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) die virtuellen Computer dem ausgehenden Pool hinzu:


* Back-End-Adresspool: **myBackEndPoolOutbound**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network---basic"></a>Konfigurieren des virtuellen Netzwerks: Basic

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) ein virtuelles Netzwerk:

* Name: **myVNet**
* Adresspräfix: **10.1.0.0/16**
* Subnetzname: **myBackendSubnet**
* Subnetzpräfix: **10.1.0.0/24**
* In der Ressourcengruppe **CreatePubLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myBastionIP**
* In **CreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Erstellen eines Bastionsubnetzes

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Bastionsubnetz:

* Name: **AzureBastionSubnet**
* Adresspräfix: **10.1.1.0/24**
* Virtuelles Netzwerk: **myVNet**
* In Ressourcengruppe **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Erstellen eines Bastionhosts

Erstellen Sie mit [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) einen Bastionhost:

* Name: **myBastionHost**
* In **CreatePubLBQS-rg**.
* Der öffentlichen IP-Adresse **myBastionIP** zugeordnet
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Standort: **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für einen Standard-Lastenausgleich müssen die virtuellen Computer unter der Back-End-Adresse über Netzwerkschnittstellen verfügen, die einer Netzwerksicherheitsgruppe angehören. 

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Netzwerksicherheitsgruppe: **myNSG** (aus dem vorherigen Schritt)
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Protokoll **(*)**
* Richtung: **Eingehend**
* Quelle: **(*)**
* Ziel: **(*)**
* Zielport: **Port 80**
* Zugriff: **Zulassen**
* Priorität: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Erstellen von Back-End-Servern: Basic

In diesem Abschnitt wird Folgendes erstellt:

* Drei Netzwerkschnittstellen für die virtuellen Computer
* Verfügbarkeitsgruppe für die virtuellen Computer
* Drei virtuelle Computer als Back-End-Server für den Lastenausgleich


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Erstellen von Netzwerkschnittstellen für die virtuellen Computer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) drei Netzwerkschnittstellen:


* Namen: **myNicVM1**, **myNicVM2** und **myNicVM3**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Erstellen einer Verfügbarkeitsgruppe für virtuelle Computer

Erstellen Sie mit [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) die Verfügbarkeitsgruppe:

* Name: **myAvSet**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) die virtuellen Computer:

### <a name="vm1"></a>VM1
* Name: **myVM1**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 1**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Name: **myVM2**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 2**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Name **myVM3**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM3**
* Image des virtuellen Computers: **win2019datacenter**
* Zone: **Zone 3**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Die Bereitstellung der virtuellen Computer kann eine Weile dauern.

## <a name="create-a-public-ip-address---basic"></a>Erstellen einer öffentlichen IP-Adresse: Basic

Um über das Internet auf Ihre Web-App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Lastenausgleich. 

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) für Folgendes:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myPublicIP**
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Erstellen eines Basic-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [az network lb create](/cli/azure/network/lb#az-network-lb-create) einen öffentlichen Lastenausgleich:

* Name: **myLoadBalancer**
* Front-End-Pool namens **myFrontEnd**
* Back-End-Pool namens **myBackEndPool**
* Zugeordnet zur öffentlichen IP-Adresse **myPublicIP**, die im vorherigen Schritt erstellt wurde 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) eine Lastenausgleichsregel:

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Hinzufügen virtueller Computer zum Back-End-Pool des Lastenausgleichs

Fügen Sie mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) die virtuellen Computer zum Back-End-Pool hinzu:

* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreatePubLBQS-rg**.
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Installieren von IIS

Verwenden Sie [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), um IIS auf den virtuellen Computern zu installieren, und legen Sie die Standardwebsite auf den Computernamen fest.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des Lastenausgleichs ab. 

Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testen des Lastenausgleichs" border="true":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den Befehl [az group delete](/cli/azure/group#az-group-delete), um die Ressourcengruppe, den Lastenausgleich und alle zugehörigen Ressourcen zu entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde Folgendes vermittelt:

* Erstellen eines Standard-Lastenausgleichs oder eines öffentlichen Lastenausgleichs
* Anfügen von virtuellen Computern 
* Konfigurieren der Datenverkehrsregel für den Lastenausgleich sowie des Integritätstests
* Testen des Lastenausgleichs

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)