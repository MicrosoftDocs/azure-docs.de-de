---
title: 'Erstellen eines NAT-Gateways: Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein NAT-Gateway mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) erstellen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: a4d034aefe59a661bfb0694feba36a669aa274ac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007272"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Erstellen eines NAT-Gateways mithilfe der Azure CLI

In diesem Tutorial erfahren Sie, wie Sie den Azure Virtual Network NAT-Dienst verwenden. Sie erstellen ein NAT-Gateway, um ausgehende Konnektivität für einen virtuellen Computer in Azure bereitzustellen. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.71 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myResourceGroupNAT** am Standort **eastus2** erstellt:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Erstellen des NAT-Gateways

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Für den Zugriff auf das öffentliche Internet benötigen Sie mindestens eine öffentliche IP-Adresse für das NAT-Gateway. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in **myResourceGroupNAT** eine öffentliche IP-Ressource namens **myPublicIP** zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Erstellen des Präfixes für öffentliche IP-Adressen

Sie können für ein NAT-Gateway mehrere öffentliche IP-Adressressourcen und/oder Präfixes für öffentliche IP-Adressen verwenden. In diesem Szenario wird zur Veranschaulichung eine Präfixressource für öffentliche IP-Adressen hinzugefügt.   Verwenden Sie [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create), um eine Präfixressource für öffentliche IP-Adressen mit dem Namen **myPublicIPprefix** in **myResourceGroupNAT** zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Erstellen einer NAT-Gatewayressource

In diesem Abschnitt erfahren Sie, wie Sie mit der NAT-Gatewayressource die folgenden Komponenten des NAT-Diensts erstellen und konfigurieren:
  - Öffentlicher IP-Adresspool und Präfix für öffentliche IP-Adressen für ausgehende Datenflüsse, die von der NAT-Gatewayressource übersetzt werden
  - Ändern des Leerlauftimeouts von vier Minuten (Standardwert) in zehn Minuten

Erstellen Sie mit [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) ein globales Azure NAT-Gateway namens **myNATgateway**. Der Befehl verwendet die öffentliche IP-Adresse **myPublicIP** und das Präfix für öffentliche IP-Adressen **myPublicIPprefix**. Mit dem Befehl wird das Leerlauftimeout in **10** Minuten geändert.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Das NAT-Gateway ist nun verwendungsbereit, und Sie müssen nur noch konfigurieren, welche Subnetze eines virtuellen Netzwerks das Gateway verwenden sollen.

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Sie müssen zunächst das virtuelle Netzwerk erstellen, damit Sie einen virtuellen Computer bereitstellen und das NAT-Gateway verwenden können.

Erstellen Sie mithilfe von [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) ein virtuelles Netzwerk mit dem Namen **myVnet** und dem Subnetz **mySubnet** in **myResourceGroupNAT**.  Der IP-Adressraum für das virtuelle Netzwerk ist **192.168.0.0/16**. Das Subnetz im virtuellen Netzwerk ist **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurieren des NAT-Diensts für das Quellsubnetz

Mit [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) konfigurieren Sie das Quellsubnetz **mySubnet** im virtuellen Netzwerk **myVnet** so, dass die spezifische NAT-Gatewayressource **myNATgateway** verwendet wird.  Mit diesem Befehl wird der NAT-Dienst im angegebenen Subnetz aktiviert.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Der gesamte ausgehende Datenverkehr an Ziele im Internet verwendet jetzt das NAT-Gateway.  Eine UDR muss nicht konfiguriert werden.

## <a name="create-a-vm-to-use-the-nat-service"></a>Erstellen eines virtuellen Computers zur Verwendung des NAT-Diensts

Sie erstellen nun einen virtuellen Computer zur Verwendung des NAT-Diensts.  Dieser virtuelle Computer verfügt über eine öffentliche IP-Adresse, die als öffentliche IP auf Instanzebene verwendet wird, um den Zugriff auf den virtuellen Computer zu ermöglichen.  Der NAT-Dienst erkennt die Flussrichtung und ersetzt in Ihrem Subnetz das Standardziel im Internet. Die öffentliche IP-Adresse des virtuellen Computers wird nicht für ausgehende Verbindungen verwendet.

### <a name="create-public-ip-for-source-vm"></a>Erstellen einer öffentlichen IP-Adresse für den virtuellen Quellcomputer

Sie erstellen eine öffentliche IP-Adresse, die für den Zugriff auf den virtuellen Computer verwendet werden soll.  Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in **myResourceGroupNAT** eine öffentliche IP-Ressource namens **myPublicIPVM** zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Erstellen einer NSG-Regel für virtuelle Computer

Da öffentliche Standard-IP-Adressen standardmäßig sicher sind, müssen Sie eine NSG erstellen, um den SSH-Zugriff in eingehender Richtung zuzulassen. Erstellen Sie mit [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) eine NSG-Ressource namens **myNSG** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Verfügbarmachen des SSH-Endpunkts auf einem virtuellen Quellcomputer

Sie erstellen in der NSG eine Regel für den SSH-Zugriff auf den virtuellen Quellcomputer. Erstellen Sie mit [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) in **myResourceGroupNAT** eine NSG-Regel namens **ssh** in der NSG mit dem Namen **myNSG**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Erstellen einer NIC für virtuelle Computer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) eine Netzwerkschnittstelle, und ordnen Sie sie der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zu. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Erstellen eines virtuellen Computers

Erstellen Sie den virtuellen Computer mit [az vm create](/cli/azure/vm#az-vm-create).  Sie generieren SSH-Schlüssel für diesen virtuellen Computer und speichern den privaten Schlüssel zur späteren Verwendung.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Warten Sie, bis der virtuelle Computer bereitgestellt wurde, und fahren Sie dann mit den übrigen Schritten fort.

## <a name="discover-the-ip-address-of-the-vm"></a>Ermitteln der IP-Adresse des virtuellen Computers

Als Erstes müssen Sie die IP-Adresse des virtuellen Computers ermitteln, den Sie erstellt haben. Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des virtuellen Computers ab. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in einen Editor ein, damit Sie sie für den Zugriff auf den virtuellen Computer verwenden können.

### <a name="sign-in-to-vm"></a>Anmelden beim virtuellen Computer

Die SSH-Anmeldeinformationen sollten in der Cloud Shell-Instanz aus dem vorherigen Vorgang gespeichert werden.  Öffnen Sie [Azure Cloud Shell](https://shell.azure.com) im Browser. Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen.

```bash
ssh <ip-address-destination>
```

Sie können den NAT-Dienst nun verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein NAT-Gateway und einen virtuellen Computer für seine Verwendung erstellt. 

Sehen Sie sich die Metriken in Azure Monitor an, um Informationen zum Betrieb Ihres NAT-Diensts anzuzeigen. Diagnostizieren Sie Probleme wie die Ressourcenüberlastung von verfügbaren SNAT-Ports.  Die Ressourcenüberlastung von SNAT-Ports wird durch Hinzufügen zusätzlicher Ressourcen für öffentliche IP-Adressen und/oder zusätzlicher Präfixressourcen für öffentliche IP-Adressen behoben.


- Erfahren Sie mehr über [Azure Virtual Network NAT](./nat-overview.md).
- Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe der Azure CLI](./quickstart-create-nat-gateway-cli.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe von Azure PowerShell](./quickstart-create-nat-gateway-powershell.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe des Azure-Portals](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]

