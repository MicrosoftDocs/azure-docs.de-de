---
title: 'Tutorial: Umleitung auf URL-Pfadbasis per CLI'
titleSuffix: Azure Application Gateway
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle ein Anwendungsgateway mit Umleitung des Datenverkehrs auf URL-Pfadbasis erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 36ba593a1d8cd2e50293eaf77dc9ec864245df4c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566587"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Tutorial: Erstellen eines Anwendungsgateways mit Umleitung auf URL-Pfadbasis mithilfe der Azure-Befehlszeilenschnittstelle

Sie können mit der Azure-Befehlszeilenschnittstelle [Routingregeln auf URL-Pfadbasis](tutorial-url-route-cli.md) konfigurieren, wenn Sie ein [Anwendungsgateway](./overview.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md). Danach erstellen Sie URL-Routingregeln, die sicherstellen, dass Webdatenverkehr an den richtigen Back-End-Pool umgeleitet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Hinzufügen der Listener und Routingregeln
> * Erstellen von VM-Skalierungsgruppen für Back-End-Pools

Das folgende Beispiel zeigt Websitedatenverkehr, der von den Ports 8080 und 8081 gesendet und an die gleichen Back-End-Pools geleitet wird:

![URL-Routingbeispiel](./media/tutorial-url-redirect-cli/scenario.png)

Sie können dieses Tutorial auch mit [Azure PowerShell](tutorial-url-redirect-powershell.md) durcharbeiten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Erstellen einer Application Gateway-Instanz

Erstellen Sie mit [az network application-gateway create](/cli/azure/network/application-gateway) das Anwendungsgateway namens „myAppGateway“. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt wird. Nachdem das Anwendungsgateway erstellt wurde, sehen Sie diese neuen Features:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.


### <a name="add-backend-pools-and-ports"></a>Hinzufügen von Back-End-Pools und Ports

Um die Back-End-Adresspools namens *imagesBackendPool* und *videoBackendPool* zu Ihrem Anwendungsgateway hinzuzufügen, verwenden Sie den Befehl [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool). Die Front-End-Ports für die Pools fügen Sie mit [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) hinzu. 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Hinzufügen der Listener und Regeln

### <a name="add-listeners"></a>Hinzufügen der Listener

Fügen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener) die Back-End-Listener namens *backendListener* und *redirectedListener* hinzu, die zum Weiterleiten des Datenverkehrs erforderlich sind.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Hinzufügen der standardmäßigen URL-Pfadzuordnung

URL-Pfadzuordnungen stellen sicher, dass bestimmte URLs an bestimmte Back-End-Pools weitergeleitet werden. Sie können mithilfe der Befehle [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) und [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule) die URL-Pfadzuordnungen *imagePathRule* und *videoPathRule* hinzufügen.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Hinzufügen der Umleitungskonfiguration

Sie können die Umleitung für den Listener mithilfe von [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) konfigurieren.

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Hinzufügen der URL-Pfadzuordnung für die Umleitung

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Hinzufügen von Routingregeln

Die Routingregel ordnet die URL-Pfadzuordnungen den von Ihnen erstellten Listenern zu. Sie können mit dem Befehl [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) die Regeln *defaultRule* und *redirectedRule* hinzufügen.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
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

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, verwenden Sie [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Beispiel: `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, `http://40.121.222.19:8080/video/test.htm` oder `http://40.121.222.19:8081/images/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Ändern Sie die URL in http://&lt;IP-Adresse&gt;:8080/images/test.html, und ersetzen Sie dabei &lt;IP-Adresse&gt; durch Ihre IP-Adresse. Die anschließende Anzeige sollte in etwa wie im folgenden Beispiel aussehen:

![Testen der Images-URL im Anwendungsgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Ändern Sie die URL in http://&lt;ip-address&gt;:8080/video/test.html, und ersetzen Sie dabei &lt;IP-Adresse&gt; durch Ihre IP-Adresse. Die anschließende Anzeige sollte in etwa wie im folgenden Beispiel aussehen:

![Testen der Video-URL im Anwendungsgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Ändern Sie jetzt die URL in http://&lt;ip-address&gt;:8081/images/test.htm, und ersetzen Sie &lt;IP-Adresse&gt; durch Ihre IP-Adresse. Nun sollten Sie feststellen, dass der Datenverkehr an den Back-End-Pool für Bilder unter http://&lt;ip-address&gt;:8080/images zurückgeleitet wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, welche Möglichkeiten das Anwendungsgateway bietet.](./overview.md)