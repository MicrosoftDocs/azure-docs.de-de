---
title: Erstellen eines Anwendungsgateways mit SSL-Beendigung – Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen und ein Zertifikat für die SSL-Beendigung hinzufügen.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 5/20/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d9007b3f1d4eee436452a3fa75b2880b9e5be461
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955686"
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mit SSL-Beendigung mithilfe der Azure-Befehlszeilenschnittstelle

Sie können über die Azure-Befehlszeilenschnittstelle ein [Anwendungsgateway](overview.md) mit einem Zertifikat für die [SSL-Beendigung](ssl-overview.md) erstellen, das eine [VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) als Back-End-Server verwendet. In diesem Beispiel enthält die Skalierungsgruppe zwei VM-Instanzen, die zum standardmäßigen Back-End-Pool des Anwendungsgateways hinzugefügt werden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines selbstsignierten Zertifikats
> * Einrichten eines Netzwerks
> * Erstellen eines Anwendungsgateways mit dem Zertifikat
> * Erstellen einer VM-Skalierungsgruppe mit dem standardmäßigen Back-End-Pool

Sie können für dieses Verfahren auch [Azure PowerShell](tutorial-ssl-powershell.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Für die Produktion sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat importieren. Für diesen Artikel erstellen Sie mit dem openssl-Befehl ein selbstsigniertes Zertifikat und eine PFX-Datei.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Geben Sie Werte ein, die für Ihr Zertifikat sinnvoll sind. Sie können die Standardwerte übernehmen.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Geben Sie das Kennwort für das Zertifikat ein. In diesem Beispiel wird *Azure123456!* verwendet.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe.

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

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Sie können [az network application-gateway create](/cli/azure/network/application-gateway) verwenden, um das Anwendungsgateway zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. 

Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myAGPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben. In diesem Beispiel ordnen Sie das erstellte Zertifikat und das zugehörige Kennwort zu, wenn Sie das Anwendungsgateway erstellen. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt wird. Nachdem das Anwendungsgateway erstellt wurde, sehen Sie diese neuen Features:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, die Server für den standardmäßigen Back-End-Pool im Anwendungsgateway bereitstellt. Die virtuellen Computer in der Skalierungsgruppe sind *myBackendSubnet* und *appGatewayBackendPool* zugeordnet. Zum Erstellen der Skalierungsgruppe können Sie [az vmss create](/cli/azure/vmss#az-vmss-create) verwenden.

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

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, können Sie [az network public-ip show](/cli/azure/network/public-ip) verwenden.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. In diesem Beispiel lautet die URL **https://52.170.203.149** .

![Sicherheitswarnung](./media/tutorial-ssl-cli/application-gateway-secure.png)

Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf **Details** und anschließend auf **Webseite trotzdem laden**. Die gesicherte NGINX-Website wird dann wie im folgenden Beispiel angezeigt:

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Anwendungsgateways als Host für mehrere Websites](./tutorial-multiple-sites-cli.md)
