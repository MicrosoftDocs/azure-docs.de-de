---
title: Hinzufügen von IPv6 zu einer IPv4-Anwendung in Azure Virtual Network – Azure-Befehlszeilenschnittstelle
titlesuffix: Azure Virtual Network
description: In diesem Artikel wird erläutert, wie IPv6-Adressen in einer vorhandenen Anwendung in Azure Virtual Network über die Azure-Befehlszeilenschnittstelle bereitgestellt werden.
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
ms.openlocfilehash: de8d63fb5181e324738e082cbb36a40ee59e36b0
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "96005247"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Hinzufügen von IPv6 zu einer IPv4-Anwendung in Azure Virtual Network – Azure-Befehlszeilenschnittstelle

In diesem Artikel wird erläutert, wie Sie über die Azure-Befehlszeilenschnittstelle IPv6-Adressen in einer Anwendung, in der öffentliche IPv4-IP-Adressen verwendet werden, in einem Azure VNET für eine Load Balancer Standard-Instanz hinzufügen. Das direkte Upgrade umfasst ein virtuelles Netzwerk und ein Subnetz, Load Balancer Standard mit IPv4- und IPV6-Front-End-Konfigurationen, VMs mit NICs mit IPv4- und IPv6-Konfigurationen, eine Netzwerksicherheitsgruppe und öffentliche IP-Adressen.

## <a name="prerequisites"></a>Voraussetzungen

- In diesem Artikel wird davon ausgegangen, dass Sie eine Load Balancer Standard-Instanz wie unter [Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer mit der Azure-Befehlszeilenschnittstelle](../load-balancer/quickstart-load-balancer-standard-public-cli.md) bereitgestellt haben.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-ipv6-addresses"></a>Erstellen von IPv6-Adressen

Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IPv6-Adresse für Ihre Load Balancer Standard-Instanz. Im folgenden Beispiel wird in der Ressourcengruppe *myResourceGroupSLB* eine öffentliche IPv6-Adresse mit dem Namen *PublicIP_v6* erstellt:

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurieren des Front-Ends für den IPv6-Lastenausgleich

Konfigurieren Sie den Lastenausgleich mit der neuen IPv6-IP-Adresse, indem Sie [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) wie folgt ausführen:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurieren des Back-End-Pools für den IPv6-Lastenausgleich

Erstellen Sie den Back-End-Pool für Netzwerkadapter mit IPv6-Adressen, indem Sie [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) wie folgt ausführen:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurieren von Regeln für den IPv6-Lastenausgleich

Erstellen Sie mit [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) Regeln für den IPv6-Lastenausgleich.

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Hinzufügen von IPv6-Adressbereichen

Fügen Sie dem virtuellen Netzwerk und Subnetz, die die Load Balancer-Instanz hosten, IPv6-Adressbereiche wie folgt hinzu:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Hinzufügen der IPv6-Konfiguration zu Netzwerkadaptern

Konfigurieren Sie die Netzwerkadapter der virtuellen Computer mit einer IPv6-Adresse, indem Sie [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) wie folgt ausführen:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Anzeigen des virtuellen IPv6-Dual Stack-Netzwerks im Azure-Portal

Sie können das virtuelle IPv6-Dual Stack-Netzwerk im Azure-Portal wie folgt anzeigen:
1. Geben Sie in der Suchleiste des Portals *myVnet* ein.
2. Wenn **myVnet** in den Suchergebnissen angezeigt wird, wählen Sie diesen Eintrag aus. Dadurch wird die Seite **Übersicht** des virtuellen Dual Stack-Netzwerks namens *myVNet* gestartet. Das virtuelle Dual Stack-Netzwerk zeigt die drei NICs mit IPv4- und IPv6-Konfigurationen im Dual Stack-Subnetz namens *mySubnet* an.

  ![Virtuelles IPv6-Dual Stack-Netzwerk in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine vorhandene Load Balancer Standard-Instanz mit einer IPv4-Front-End-Konfiguration in eine Dual Stack-Konfiguration (IPv4 und IPv6) aktualisiert. Außerdem haben Sie den NICs der VMs im Back-End-Pool IPv6-Konfigurationen hinzugefügt. Weitere Informationen zur IPv6-Unterstützung in virtuellen Azure-Netzwerken finden Sie unter [Was ist IPv6 für Azure Virtual Network?](ipv6-overview.md)
