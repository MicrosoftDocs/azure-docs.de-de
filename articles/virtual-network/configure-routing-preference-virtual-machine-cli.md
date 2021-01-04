---
title: 'Konfigurieren der Routingpräferenz für einen virtuellen Computer: Azure CLI'
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle (CLI) einen virtuellen Computer mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: ccd04a43e6781e8d58234cc382b2739d800e5fe7
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510674"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie die Routingpräferenz für einen virtuellen Computer konfigurieren. Der Internetdatenverkehr vom virtuellen Computer wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Artikel wird gezeigt, wie Sie einen virtuellen Computer mit einer öffentlichen IP-Adresse erstellen, die zum Weiterleiten von Datenverkehr über das öffentliche Internet unter Verwendung der Azure CLI festgelegt ist.

> [!IMPORTANT]
> „Routingpräferenz“ ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
1. Wenn Sie Cloud Shell bereits verwenden, fahren Sie mit Schritt 2 fort. Öffnen Sie eine Befehlssitzung, und melden Sie sich mit `az login` in Azure an.
2. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region „USA, Osten“ erstellt:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Um über das Internet auf Ihre virtuellen Computer zugreifen zu können, müssen Sie eine öffentliche IP-Adresse erstellen. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse. Im folgenden Beispiel wird eine öffentliche IP-Adresse des Routingpräferenztyps *Internet* in der Region *USA, Osten* erstellt:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Bevor Sie einen virtuellen Computer bereitstellen, müssen Sie unterstützende Netzwerkressourcen erstellen: Netzwerksicherheitsgruppe, virtuelles Netzwerk und eine virtuelle NIC.

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe für die Regeln, durch die die eingehende und ausgehende Kommunikation in Ihrem VNET mit [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) gesteuert wird.

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVNET* mit einem Subnetz namens *mySubNet* erstellt:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Erstellen einer NIC

Erstellen Sie mit [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) eine virtuelle NIC für den virtuellen Computer. Im folgenden Beispiel wird eine virtuelle NIC erstellt, die an den virtuellen Computer angefügt wird.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) einen virtuellen Computer. Im folgenden Beispiel werden ein virtueller Computer mit Windows Server 2019 und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group#az-group-delete) entfernen:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Routingpräferenz in öffentlichen IP-Adressen](routing-preference-overview.md).
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Weitere Informationen zu den [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address)
