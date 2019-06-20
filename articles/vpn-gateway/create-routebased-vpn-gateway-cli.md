---
title: 'Erstellen eines routenbasierten Azure-VPN-Gateways: CLI | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie ein VPN-Gateway mithilfe von CLI erstellen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: f5f62a6bfa1baa205e0496dd901f1f1eef660079
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391241"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Erstellen eines routenbasierten VPN-Gateways mithilfe von CLI

In diesem Artikel erfahren Sie, wie Sie schnell ein routenbasiertes Azure-VPN-Gateway mithilfe von Azure CLI erstellen. Ein VPN-Gateway wird beim Herstellen einer VPN-Verbindung mit Ihrem lokalen Netzwerk verwendet. Sie können Verbindungen mit VNets auch mit einem VPN-Gateway herstellen.

Mit den Schritten in diesem Artikel werden ein VNet, ein Subnetz, ein Gatewaysubnetz und ein routenbasiertes VPN-Gateway (Gateway des virtuellen Netzwerks) erstellt. Das Erstellen eines VPN-Gateways kann 45 Minuten oder länger dauern. Wenn die Gatewayerstellung abgeschlossen ist, können Sie Verbindungen herstellen. Diese Schritte erfordern ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit dem Befehl [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen **VNet1** am Standort **USA, Osten** erstellt:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Hinzufügen eines Gatewaysubnetzes

Das Gatewaysubnetz enthält die reservierten IP-Adressen, die von den Diensten des virtuellen Netzwerkgateways verwendet werden. Verwenden Sie die folgenden Beispiele, um ein Gatewaysubnetz hinzufügen:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Anfordern einer öffentlichen IP-Adresse

Ein VPN-Gateway muss über eine dynamisch zugewiesene öffentliche IP-Adresse verfügen. Die öffentliche IP-Adresse wird dem VPN-Gateway zugeordnet, das Sie für Ihr virtuelles Netzwerk erstellen. Fordern Sie gemäß des folgenden Beispiels eine öffentliche IP-Adresse an:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Erstellen des VPN-Gateways

Erstellen Sie das VPN-Gateway mit dem Befehl [az network vnet-gateway create](/cli/azure/group).

Wenn Sie diesen Befehl mit dem Parameter `--no-wait` ausführen, werden kein Feedback und keine Ausgabe angezeigt. Der Parameter `--no-wait` ermöglicht die Erstellung des Gateways im Hintergrund. Das bedeutet nicht, dass die Erstellung des VPN-Gateways sofort abgeschlossen ist.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Das Erstellen eines VPN-Gateways kann 45 Minuten oder länger dauern.

## <a name="viewgw"></a>Anzeigen des VPN-Gateways

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Die Antwort sieht wie folgt aus:

```
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Anzeigen der öffentlichen IP-Adresse

Um die öffentliche IP-Adresse anzuzeigen, die dem Gateway zugewiesen ist, verwenden Sie das folgende Beispiel:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Der dem Feld **ipAddress** zugeordnete Wert ist die öffentliche IP-Adresse Ihres VPN-Gateways.

Beispielantwort:

```
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group). Damit löschen Sie die Ressourcengruppe mit allen enthaltenen Ressourcen.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Nächste Schritte

Sobald das Gateway fertig gestellt ist, können Sie eine Verbindung Ihres virtuellen Netzwerks mit einem anderen VNet herstellen. Alternativ können Sie eine Verbindung zwischen dem virtuellen Netzwerk und einem lokalen Standort erstellen.

> [!div class="nextstepaction"]
> [Herstellen einer Site-to-Site-Verbindung](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Herstellen einer Point-to-Site-Verbindung](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Herstellen einer Verbindung mit einem anderen VNet](vpn-gateway-vnet-vnet-rm-ps.md)
