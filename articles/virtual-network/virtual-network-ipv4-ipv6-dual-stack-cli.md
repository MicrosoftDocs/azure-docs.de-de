---
title: Bereitstellen einer IPv6-Anwendung mit dualem Stapel – CLI
titlesuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie eine Anwendung mit doppeltem Stapel (IPv4 und IPv6) mit Load Balancer Basic mithilfe der Azure CLI bereitstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3808ff9b57a900b8a1d55b66f8c652d070107322
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "96012049"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Bereitstellen einer IPv6-Anwendung mit dualem Stapel und Basic-Load Balancer – Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Anwendung mit dualem Stapel (IPv4 und IPv6) und Load Balancer im Tarif „Basic“ bereitstellen, die ein virtuelles Netzwerk mit dualem Stapel, ein Subnetz mit dualem Stapel, eine Load Balancer-Instanz im Tarif „Basic“ mit dualen Front-End-Konfigurationen (IPv4 und IPv6), virtuelle Computer mit NICs mit einer dualen IP-Konfiguration, duale Netzwerksicherheitsgruppenregeln sowie duale öffentliche IP-Adressen umfasst.

Informationen zum Bereitstellen einer Anwendung mit dualem Stapel (IPv4 und IPv6) unter Verwendung von Load Balancer Standard finden Sie unter [Bereitstellen einer IPv6-Dual Stack-Anwendung mit Load Balancer Standard in Azure Virtual Network – CLI (Vorschau)](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.49 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihr virtuelles Dual Stack-Netzwerk erstellen können, müssen Sie eine Ressourcengruppe mit [az group create](/cli/azure/group) erstellen. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *DsResourceGroup01* am Standort *eastus*.

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Erstellen der öffentlichen IPv4- and IPv6-Adressen für den Load Balancer
Um auf Ihre IPv4- und IPv6-Endpunkte im Internet zugreifen zu können, benötigen Sie öffentliche IPv4- und IPv6-Adressen für den Load Balancer. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse. Das folgende Beispiel erstellt öffentliche IPv4- und IPv6-Adressen mit den Namen *dsPublicIP_v4* und *dsPublicIP_v6* in der Ressourcengruppe *DsResourceGroup01*:

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Erstellen öffentlicher IP-Adressen für VMs

Um remote auf Ihre VMs im Internet zugreifen zu können, benötigen Sie öffentliche IPv4-Adressen für die VMs. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse.

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Erstellen eines Load Balancers im Tarif „Basic“

In diesem Abschnitt konfigurieren Sie die duale Front-End-IP-Adresse (IPv4 ind IPv6) und den Back-End-Adresspool für den Load Balancer und erstellen anschließend den Load Balancer im Tarif „Basic“.

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Erstellen Sie mit [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) einen Load Balancer im Tarif „Basic“ mit dem Namen **dsLB**, der einen Front-End-Pool namens **dsLbFrontEnd_v4** und einen Back-End-Pool namens **dsLbBackEndPool_v4** enthält und der öffentlichen IPv4-Adresse **dsPublicIP_v4** zugeordnet ist, die Sie im vorherigen Schritt erstellt haben. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Erstellen des IPv6-Front-Ends

Erstellen Sie eine IPv6-Front-End-IP-Adresse mit [az network lb frontend-ip-create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Im folgenden Beispiel wird eine Front-End-IP-Konfiguration mit dem Namen *dsLbFrontEnd_v6* erstellt und die Adresse *dsPublicIP_v6* angefügt:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurieren des IPv6-Back-End-Adresspools

Erstellen Sie einen IPv6-Back-End-Adresspool mit [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). Das folgende Beispiel erstellt einen Back-End-Adresspool mit dem Namen *dsLbBackEndPool_v6*, um VMs mit IPv6-NIC-Konfigurationen einzubeziehen:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests
Erstellen Sie mit [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) einen Integritätstest zur Überwachung der Integrität von virtuellen Computern. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. 

Erstellen Sie mit [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) eine Load Balancer-Regel. Das folgende Beispiel erstellt Load Balancer-Regeln mit den Namen *dsLBrule_v4* und *dsLBrule_v6* und führt Lastenausgleich des Datenverkehrs an *TCP*-Port *80* zu den IPv4- und IPv6-Front-End-IP-Konfigurationen aus:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen
Bevor Sie einige VMs bereitstellen, müssen Sie unterstützende Netzwerkressourcen erstellen: Verfügbarkeitsgruppe, Netzwerksicherheitsgruppe, virtuelles Netzwerk und virtuelle NICs. 
### <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen
Fügen Sie die virtuellen Computer in eine Verfügbarkeitsgruppe ein, um die Verfügbarkeit Ihrer App zu optimieren.

Erstellen Sie mithilfe von [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) eine Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *dsAVset* erstellt:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe für die Regeln, durch die die eingehende und ausgehende Kommunikation in Ihrem VNET gesteuert wird.

#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen einer Netzwerksicherheitsgruppe mit [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für eingehende und ausgehende Verbindungen

Erstellen Sie eine Netzwerksicherheitsgruppen-Regel zum Zulassen von RDP-Verbindungen über Port 3389, Internetverbindungen über Port 80 und für ausgehende Verbindungen mit [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen *dsVNET* mit den Subnetzen *dsSubNET_v4* und *dsSubNET_v6* erstellt:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie virtuelle NICs für jede VM mit [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Das folgende Beispiel erstellt eine virtuelle NIC für jede VM. Jede NIC verfügt über zwei IP-Konfigurationen (eine IPv4-Konfiguration, eine IPv6-Konfiguration). Sie erstellen die IPV6-Konfiguration mit [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie mit [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) die virtuellen Computer. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind. 

Erstellen Sie den virtuellen Computer *dsVM0* wie folgt:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Erstellen Sie den virtuellen Computer *dsVM1* wie folgt:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Anzeigen des virtuellen IPv6-Dual Stack-Netzwerks im Azure-Portal
Sie können das virtuelle IPv6-Dual Stack-Netzwerk im Azure-Portal wie folgt anzeigen:
1. Geben Sie in der Suchleiste des Portals *dsVnet* ein.
2. Wenn **myVirtualNetwork** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen. Dadurch wird die Seite **Übersicht** des virtuellen Dual Stack-Netzwerks namens *dsVnet* gestartet. Das virtuelle Dual Stack-Netzwerk zeigt die beiden NICs mit IPv4- und IPv6-Konfigurationen im Dual Stack-Subnetz namens *dsSubnet*.

  ![Virtuelles IPv6-Dual Stack-Netzwerk in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Load Balancer im Tarif „Basic“ mit einer dualen Front-End-IP-Konfiguration (IPv4 und IPv6) erstellt. Sie haben außerdem zwei virtuelle Computer mit NICs mit dualen IP-Konfigurationen (IPV4 und IPv6) erstellt, die dem Back-End-Pool des Load Balancers hinzugefügt wurden. Weitere Informationen zur IPv6-Unterstützung in virtuellen Azure-Netzwerken finden Sie unter [Was ist IPv6 für Azure Virtual Network?](ipv6-overview.md)
