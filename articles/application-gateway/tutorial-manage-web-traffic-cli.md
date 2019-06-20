---
title: 'Verwalten von Webdatenverkehr: Azure CLI'
description: Erfahren Sie, wie Sie unter Verwendung der Azure CLI ein Anwendungsgateway mit einer VM-Skalierungsgruppe zum Verwalten von Webdatenverkehr erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/1/2019
ms.author: victorh
ms.openlocfilehash: d60c756fcf0b527731b8a1f31a8d93f108c91665
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146229"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure CLI

Mit dem Anwendungsgateway wird der Webdatenverkehr mit Servern, die Sie verwalten, verwaltet und abgesichert. Sie können mit der Azure CLI ein [Anwendungsgateway](overview.md) erstellen, das eine [VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) für Back-End-Server verwendet. In diesem Beispiel enthält die Skalierungsgruppe zwei VM-Instanzen. Die Skalierungsgruppe wird dem Standard-Back-End-Pool des Anwendungsgateways hinzugefügt.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Erstellen einer VM-Skalierungsgruppe mit dem standardmäßigen Back-End-Pool

Sie können für dieses Verfahren auch [Azure PowerShell](tutorial-manage-web-traffic-powershell.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, muss für diese Schnellstartanleitung die Azure CLI in Version 2.0.4 oder höher ausgeführt werden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupAG* am Standort *eastus* erstellt.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen 

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk namens *myVNet* und ein Subnetz namens *myAGSubnet*. Dann können Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) das Subnetz namens *myBackendSubnet* hinzufügen, das von den Back-End-Servern benötigt wird. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse namens *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Erstellen Sie mit [az network application-gateway create](/cli/azure/network/application-gateway) das Anwendungsgateway namens *myAppGateway*. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt wird. Nachdem das Anwendungsgateway erstellt wurde, werden die folgenden neuen Funktionen angezeigt:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer VM-Skalierungsgruppe

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, die Server für den Back-End-Pool im Anwendungsgateway bereitstellt. Die virtuellen Computer in der Skalierungsgruppe sind *myBackendSubnet* und *appGatewayBackendPool* zugeordnet. Verwenden Sie zum Erstellen der Skalierungsgruppe [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installieren von NGINX

Sie können nun NGINX für die VM-Skalierungsgruppe installieren, um die HTTP-Verbindung mit dem Back-End-Pool zu testen.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, verwenden Sie [az network public-ip show](/cli/azure/network/public-ip). Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste Ihres Browsers ein.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Nächste Schritte

[Einschränken des Webdatenverkehrs mit einer Web Application Firewall](./tutorial-restrict-web-traffic-cli.md)
