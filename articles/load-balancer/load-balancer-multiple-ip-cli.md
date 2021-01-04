---
title: Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen mit Azure CLI
titleSuffix: Azure Load Balancer
description: Informationen zum Zuweisen von mehreren IP-Adressen zu einem virtuellen Computer mit Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 06dfa65236bf1aa5cfde626c5574ffdf487eb045
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698358"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen mit Azure CLI

In diesem Artikel wird beschrieben, wie Sie Azure Load Balancer mit mehreren IP-Adressen an einer sekundären Netzwerkschnittstellenkarte (Network Interface Card, NIC) verwenden. In diesem Szenario verwenden wir zwei virtuelle Computer unter Windows mit jeweils einer primären und einer sekundären NIC. Die sekundären NICs haben jeweils zwei IP-Konfigurationen. Jeder virtuelle Computer hostet die beiden Websites „contoso.com“ und „fabrikam.com“. Jede Website ist an eine der IP-Konfigurationen der sekundären NIC gebunden. Wir verwenden Azure Load Balancer, um zwei Front-End-IP-Adressen verfügbar zu machen (jeweils eine für jede Website), damit der Datenverkehr an die entsprechende IP-Konfiguration für die Website verteilt werden kann. Dieses Szenario verwendet die gleiche Portnummer auf beiden Front-Ends sowie beide Back-End-Pool-IP-Adressen.

![Abbildung zum LB-Szenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Schritte für den Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen

Führen Sie die folgenden Schritte aus, um das in diesem Artikel beschriebene Szenario umzusetzen:

1. [Installieren und konfigurieren Sie die Azure CLI anhand der Schritte im verlinkten Artikel](/cli/azure/install-azure-cli?view=azure-cli-latest), und melden Sie sich dann an Ihrem Azure-Konto an.
2. [Erstellen Sie eine Ressourcengruppe](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) mit dem Namen *contosofabrikam* wie folgt:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Erstellen Sie eine Verfügbarkeitsgruppe](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) für die beiden VMs. Führen Sie für dieses Szenario den folgenden Befehl aus:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Erstellen Sie ein virtuelles Netzwerk](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) mit dem Namen *myVNet* und ein Subnetz mit dem Namen *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Erstellen Sie den Lastenausgleich](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mit dem Namen *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Erstellen Sie zwei dynamische öffentliche IP-Adressen für die Front-End-IP-Konfigurationen Ihres Lastenausgleichs:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Erstellen Sie die beiden Front-End-IP-Konfigurationen *contosofe* und *fabrikamfe*:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Erstellen Sie die Back-End-Adresspools (*contosopool* und *fabrikampool*), einen [Test (](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*) und die Lastenausgleichsregeln (*HTTPc* und *HTTPf*):

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Führen Sie den folgenden Befehl aus, und überprüfen Sie die Ausgabe, um [sicherzustellen, dass der Lastenausgleich richtig erstellt wurde](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Erstellen Sie die öffentliche IP](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp* und das [Speicherkonto](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* für den ersten virtuellen Computer VM1 wie folgt:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Erstellen Sie die Netzwerkschnittstellen](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) für VM1, fügen Sie eine zweite IP-Konfiguration (*VM1-ipconfig2*) hinzu, und [erstellen Sie den virtuellen Computer](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) wie folgt:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Wiederholen Sie die Schritte 10 bis 11 für Ihre zweite VM:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Abschließend müssen Sie DNS-Ressourceneinträge konfigurieren, um auf die entsprechende Front-End-IP-Adresse des Lastenausgleichs zu verweisen. Sie können Ihre Domänen in Azure DNS hosten. Weitere Informationen zur Verwendung von Azure DNS mit Lastenausgleich finden Sie unter [Verwenden von Azure DNS mit anderen Azure-Diensten](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie in [Verwenden von Lastenausgleichsdiensten in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md), wie Sie Lastenausgleichsdienste in Azure kombinieren.
- Erfahren Sie in [Log Analytics für den Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md), wie Sie in Azure verschiedene Protokolltypen verwenden, um den Load Balancer zu verwalten und eventuelle Fehler zu beheben.