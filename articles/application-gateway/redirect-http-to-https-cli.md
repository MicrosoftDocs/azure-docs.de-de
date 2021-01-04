---
title: Umleitung von HTTP an HTTPS mit der CLI
titleSuffix: Azure Application Gateway
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI eine HTTP-zu-HTTPS-Umleitung erstellen und ein Zertifikat für den TLS-Abschluss hinzufügen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 0d56a1c46f251307755416ef44991ac6f809f330
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566740"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über die Azure-Befehlszeilenschnittstelle

Sie können die Azure CLI verwenden, um ein [Anwendungsgateway](overview.md) mit einem Zertifikat für die TLS/SSL-Terminierung zu erstellen. Zum Umleiten des HTTP-Datenverkehrs an den HTTPS-Port in Ihrem Anwendungsgateway wird eine Routingregel verwendet. In diesem Beispiel erstellen Sie auch eine [VM-Skalierungsgruppe](../virtual-machine-scale-sets/overview.md) für den Back-End-Pool des Anwendungsgateways, die zwei virtuelle Computerinstanzen enthält.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines selbstsignierten Zertifikats
* Einrichten eines Netzwerks
* Erstellen eines Anwendungsgateways mit dem Zertifikat
* Hinzufügen eines Listeners und einer Umleitungsregel
* Erstellen einer VM-Skalierungsgruppe mit dem standardmäßigen Back-End-Pool

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Für den Einsatz in einer Produktionsumgebung sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat importieren. Für dieses Tutorial erstellen Sie mit dem openssl-Befehl ein selbstsigniertes Zertifikat und eine PFX-Datei.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Geben Sie Werte ein, die für Ihr Zertifikat sinnvoll sind. Sie können die Standardwerte übernehmen.

```console
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

Sie können [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) verwenden, um ein Anwendungsgateway namens *myAppGateway* zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. 

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

## <a name="add-a-listener-and-redirection-rule"></a>Hinzufügen eines Listeners und einer Umleitungsregel

### <a name="add-the-http-port"></a>Hinzufügen des HTTP-Ports

Sie können [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) verwenden, um den HTTP-Port zum Anwendungsgateway hinzuzufügen.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Hinzufügen des HTTP-Listeners

Sie können [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) verwenden, um den Listener namens *myListener* zum Anwendungsgateway hinzuzufügen.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Hinzufügen der Umleitungskonfiguration

Fügen Sie dem Anwendungsgateway mit [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create) die Konfiguration für die Umleitung von HTTP zu HTTPS hinzu.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Hinzufügen der Routingregel

Fügen Sie dem Anwendungsgateway mit [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) die Routingregel namens *rule2* mit der Umleitungskonfiguration hinzu.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe namens *myvmss*, die Server für den Back-End-Pool im Anwendungsgateway bereitstellt. Die virtuellen Computer in der Skalierungsgruppe sind *myBackendSubnet* und *appGatewayBackendPool* zugeordnet. Zum Erstellen der Skalierungsgruppe können Sie [az vmss create](/cli/azure/vmss#az-vmss-create) verwenden.

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

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, können Sie [az network public-ip show](/cli/azure/network/public-ip) verwenden. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Sicherheitswarnung](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf **Details** und anschließend auf **Webseite trotzdem laden**. Die gesicherte NGINX-Website wird dann wie im folgenden Beispiel angezeigt:

![Testen der Basis-URL im Anwendungsgateway](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Anwendungsgateways mit interner Umleitung über die Azure-Befehlszeilenschnittstelle](redirect-internal-site-cli.md)