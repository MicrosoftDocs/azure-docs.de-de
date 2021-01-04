---
title: Weiterleiten von Webdatenverkehr basierend auf der URL – Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie Webdatenverkehr basierend auf der URL mithilfe der Azure-Befehlszeilenschnittstelle an spezifische skalierbare Serverpools weiterleiten.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8fed99fe0b1de52d2e2d0018dfd8867b54b63b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566519"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Weiterleiten von Webdatenverkehr basierend auf der URL mit der Azure-Befehlszeilenschnittstelle

Als IT-Administrator, der Webdatenverkehr verwaltet, möchten Sie Ihren Kunden oder Benutzern dabei helfen, schnellstmöglich die Informationen zu erhalten, die sie benötigen. Zur Verbesserung der Benutzerfreundlichkeit können Sie unter anderem verschiedene Arten von Webdatenverkehr an verschiedene Serverressourcen weiterleiten. In diesem Artikel erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle verwenden, um Application Gateway-Routing für verschiedene Arten von Datenverkehr aus Ihrer Anwendung einzurichten und zu konfigurieren. Der Datenverkehr wird dann auf der Grundlage der URL an verschiedene Serverpools weitergeleitet.

![URL-Routingbeispiel](./media/tutorial-url-route-cli/scenario.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen einer Ressourcengruppe für die benötigten Netzwerkressourcen
* Erstellen der Netzwerkressourcen
* Erstellen eines Anwendungsgateways für den Datenverkehr aus Ihrer Anwendung
* Angeben von Serverpools und Routingregeln für die verschiedenen Arten von Datenverkehr
* Erstellen einer Skalierungsgruppe für jeden Pool, damit der Pool automatisch skaliert werden kann
* Testen, ob die verschiedenen Arten von Datenverkehr an den richtigen Pool weitergeleitet werden

Dieses Verfahren kann auch mit [Azure PowerShell](tutorial-url-route-powershell.md) oder mit dem [Azure-Portal](create-url-route-portal.md) durchgearbeitet werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe von `az group create` eine Ressourcengruppe.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupAG* am Standort *eastus* erstellt.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Erstellen Sie mithilfe von `az network vnet create` ein virtuelles Netzwerk namens *myVNet* und ein Subnetz namens *myAGSubnet*. Fügen Sie dann mithilfe von `az network vnet subnet create` ein Subnetz namens *myBackendSubnet* hinzu, das von den Back-End-Servern benötigt wird. Erstellen Sie mithilfe von `az network public-ip create` die öffentliche IP-Adresse *myAGPublicIPAddress*.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Erstellen des App-Gateways mit einer URL-Zuordnung

Verwenden Sie `az network application-gateway create`, um ein Anwendungsgateway namens *myAppGateway* zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird *myAGSubnet* und *myAGPublicIPAddress* zugewiesen.

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


|Funktion  |BESCHREIBUNG  |
|---------|---------|
|appGatewayBackendPool     |Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.|
|appGatewayBackendHttpSettings     |Gibt an, dass zur Kommunikation der Port 80 und ein HTTP-Protokoll verwendet werden.|
|appGatewayHttpListener     |Der Standardlistener, der „appGatewayBackendPool“ zugeordnet ist.|
|appGatewayFrontendIP     |Weist „myAGPublicIPAddress“ zu „appGatewayHttpListener“ zu.|
|rule1     |Die Standardroutingregel, die „appGatewayHttpListener“ zugeordnet ist.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Hinzufügen von Back-End-Pools für Bilder und Videos sowie eines Ports

Fügen Sie Ihrem Anwendungsgateway mithilfe von `az network application-gateway address-pool create` Back-End-Pools namens *imagesBackendPool* und *videoBackendPool* hinzu. Verwenden Sie `az network application-gateway frontend-port create`, um den Front-End-Port für die Pools hinzuzufügen.

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

### <a name="add-a-backend-listener"></a>Hinzufügen eines Back-End-Listeners

Fügen Sie mithilfe von `az network application-gateway http-listener create` den Back-End-Listener namens *backendListener* hinzu. Dieser ist zum Weiterleiten von Datenverkehr erforderlich.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Hinzufügen einer URL-Pfadzuordnung

URL-Pfadzuordnungen stellen sicher, dass bestimmte URLs an bestimmte Back-End-Pools weitergeleitet werden. Erstellen Sie mithilfe von `az network application-gateway url-path-map create` und `az network application-gateway url-path-map rule create` URL-Pfadzuordnungen namens *imagePathRule* und *videoPathRule*.

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

### <a name="add-a-routing-rule"></a>Hinzufügen einer Routingregel

Die Routingregel ordnet die URL-Zuordnungen dem von Ihnen erstellten Listener zu. Fügen Sie mithilfe von `az network application-gateway rule create` eine Regel namens *rule2* hinzu.

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

In diesem Artikel erstellen Sie drei VM-Skalierungsgruppen, die die drei von Ihnen erstellten Back-End-Pools unterstützen. Sie erstellen Skalierungsgruppen namens *myvmss1*, *myvmss2* und *myvmss3*. Jede Skalierungsgruppe enthält zwei VM-Instanzen, auf denen Sie NGINX installieren.

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

Verwenden Sie „az network public-ip show“, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Beispiel: `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` oder `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Ändern Sie die URL in http://&lt;IP-Adresse&gt;:8080/images/test.html, und ersetzen Sie dabei &lt;IP-Adresse&gt; durch Ihre IP-Adresse. Die anschließende Anzeige sollte in etwa wie im folgenden Beispiel aussehen:

![Testen der Images-URL im Anwendungsgateway](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Ändern Sie die URL in http://&lt;ip-address&gt;:8080/video/test.html, und ersetzen Sie dabei &lt;IP-Adresse&gt; durch Ihre IP-Adresse. Die anschließende Anzeige sollte in etwa wie im folgenden Beispiel aussehen.

![Testen der Video-URL im Anwendungsgateway](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Anwendungsgateways mit Umleitung auf URL-Pfadbasis](./tutorial-url-redirect-cli.md)
