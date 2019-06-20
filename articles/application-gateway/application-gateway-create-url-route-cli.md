---
title: Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis – Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle Routingregeln auf URL-Pfadbasis für ein Anwendungsgateway und eine VM-Skalierungsgruppe erstellen.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 061156a455664a5a3f0b4c4497d24f4e8ff6eea7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135647"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis mithilfe der Azure-Befehlszeilenschnittstelle

Sie können mit der Azure-Befehlszeilenschnittstelle [Routingregeln auf URL-Pfadbasis](application-gateway-url-route-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](application-gateway-introduction.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe einer [VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Anschließend erstellen Sie Routingregeln, die sicherstellen, dass Webdatenverkehr an die richtigen Server in den Pools gesendet wird.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways mit URL-Zuordnung
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools

![URL-Routingbeispiel](./media/application-gateway-create-url-route-cli/scenario.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="create-the-application-gateway-with-url-map"></a>Erstellen des Anwendungsgateways mit URL-Zuordnung

Sie können [az network application-gateway create](/cli/azure/network/application-gateway) verwenden, um ein Anwendungsgateway namens *myAppGateway* zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myAGPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben. 

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

 Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt ist. Nachdem das Anwendungsgateway erstellt wurde, sehen Sie diese neuen Features:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*: Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.


### <a name="add-image-and-video-backend-pools-and-port"></a>Hinzufügen von Back-End-Pools und Port für Images und Videos

Sie können Back-End-Pools namens *imagesBackendPool* und *videoBackendPool* zu Ihrem Anwendungsgateway hinzufügen. Verwenden Sie dazu [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create). Den Front-End-Port für die Pools fügen Sie mit [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) hinzu. 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Hinzufügen eines Back-End-Listeners

Fügen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway) den Back-End-Listener namens *backendListener* hinzu, der zum Weiterleiten von Datenverkehr erforderlich ist.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Hinzufügen einer URL-Pfadzuordnung

URL-Pfadzuordnungen stellen sicher, dass bestimmte URLs an bestimmte Back-End-Pools weitergeleitet werden. Sie können mithilfe der Befehle [az network application-gateway url-path-map create](/cli/azure/network/application-gateway) und [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway) die URL-Pfadzuordnungen *imagePathRule* und *videoPathRule* hinzufügen.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Hinzufügen einer Routingregel

Die Routingregel ordnet die URL-Zuordnungen dem von Ihnen erstellten Listener zu. Sie können mit dem Befehl [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) die Regel *rule2* hinzufügen.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Erstellen von VM-Skalierungsgruppen

In diesem Beispiel erstellen Sie drei VM-Skalierungsgruppen, die die drei von Ihnen erstellten Back-End-Pools unterstützen. Die erstellten Skalierungsgruppen werden *myvmss1*, *myvmss2* und *myvmss3* genannt. Jede Skalierungsgruppe enthält zwei VM-Instanzen, auf denen Sie NGINX installieren.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
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
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installieren von NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, können Sie [az network public-ip show](/cli/azure/network/public-ip) verwenden. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Beispiel: `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` oder `http://40.121.222.19:8080/video/test.htm`.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testen der Basis-URL im Anwendungsgateway](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Ändern Sie die URL bis zum Ende der Basis-URL in `http://<ip-address>:8080/video/test.html`. Die Ausgabe sollte in etwa wie folgt aussehen:

![Testen der Images-URL im Anwendungsgateway](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Ändern Sie die URL in `http://<ip-address>:8080/video/test.html`. Die Ausgabe sollte in etwa wie folgt aussehen.

![Testen der Video-URL im Anwendungsgateway](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways mit URL-Zuordnung
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools

Weitere Informationen zu Anwendungsgateways und den zugehörigen Ressourcen finden Sie in den Artikeln mit empfohlenen Vorgehensweisen.
