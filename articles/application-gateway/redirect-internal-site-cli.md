---
title: Interne Umleitung mithilfe der CLI
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie mithilfe der Azure CLI ein Anwendungsgateway erstellen, das internen Webdatenverkehr an den entsprechenden Pool umleitet.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: b443fa7c2d6c644fc1173295f89813c18657d160
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566723"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mit interner Umleitung über die Azure-Befehlszeilenschnittstelle

Sie können mit der Azure CLI [eine Umleitung des Webdatenverkehrs](multiple-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](overview.md) erstellen. In diesem Tutorial definieren Sie mithilfe einer VM-Skalierungsgruppe einen Back-End-Pool. Anschließend konfigurieren Sie Listener und Regeln basierend auf Domänen in Ihrem Besitz, um sicherzustellen, dass Webdatenverkehr an die richtigen Pools gesendet wird. In diesem Tutorial wird vorausgesetzt, dass Sie mehrere Domänen besitzen, und es werden Beispiele zu *www\.contoso.com* und *www\.contoso.org* verwendet.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten des Netzwerks
* Erstellen eines Anwendungsgateways
* Hinzufügen der Listener und einer Umleitungsregel
* Erstellen einer VM-Skalierungsgruppe mit dem Back-End-Pool
* Erstellen eines CNAME-Eintrags in Ihrer Domäne

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

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk namens *myVNet* und ein Subnetz namens *myAGSubnet*. Dann können Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) das Subnetz namens *myBackendSubnet* hinzufügen, das vom Back-End-Serverpool benötigt wird. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) eine öffentliche IP-Adresse namens *myAGPublicIPAddress*.

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

Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt wird. Nachdem das Anwendungsgateway erstellt wurde, sehen Sie diese neuen Features:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.


## <a name="add-listeners-and-rules"></a>Hinzufügen der Listener und Regeln 

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an den Back-End-Pool weiterleiten kann. In diesem Tutorial erstellen Sie zwei Listener für Ihre beiden Domänen. In diesem Beispiel werden Listener für die Domänen *www\.contoso.com* und *www\.contoso.org* erstellt.

Fügen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) die Back-End-Listener hinzu, die zum Weiterleiten von Datenverkehr erforderlich sind.

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Hinzufügen der Umleitungskonfiguration

Fügen Sie mit [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create) die Umleitungskonfiguration hinzu, die Datenverkehr von *www\.contoso.org* an den Listener für *www\.contoso.com* im Anwendungsgateway sendet.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Hinzufügen von Routingregeln

Regeln werden in der Reihenfolge verarbeitet, in der sie erstellt werden, und Datenverkehr wird anhand der ersten Regel weitergeleitet, die mit der an das Anwendungsgateway gesendeten URL übereinstimmt. Wenn Sie beispielsweise eine Regel mit einem einfachen Listener und eine Regel mit einem Listener für mehrere Standorte auf demselben Port aktiviert haben, muss die Regel mit dem Listener für mehrere Standorte vor der Regel mit dem einfachen Listener aufgeführt sein, damit die Regel für mehrere Standorte wie erwartet funktioniert. 

In diesem Beispiel erstellen Sie zwei neue Regeln und löschen die Standardregel, die erstellt wurde.  Sie können die Regel mit [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) hinzufügen.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Erstellen von VM-Skalierungsgruppen

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, die den von Ihnen erstellten Back-End-Pool unterstützt. Die von Ihnen erstellte Skalierungsgruppe heißt *myvmss* und enthält zwei VM-Instanzen, auf denen Sie NGINX installieren.

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

Führen Sie diesen Befehl im Shellfenster aus:

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

## <a name="create-cname-record-in-your-domain"></a>Erstellen eines CNAME-Eintrags in Ihrer Domäne

Nachdem das Anwendungsgateway mit der zugehörigen öffentlichen IP-Adresse erstellt wurde, können Sie die DNS-Adresse abrufen und zum Erstellen eines CNAME-Eintrags in Ihrer Domäne verwenden. Sie können [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) verwenden, um die DNS-Adresse des Anwendungsgateways abzurufen. Kopieren Sie den *fqdn*-Wert der DNSSettings, und verwenden Sie ihn als Wert für den erstellten CNAME-Eintrag. Die Verwendung von A-Einträgen wird nicht empfohlen, weil sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Geben Sie Ihren Domänennamen in die Adressleiste Ihres Browsers ein. Beispiel: http:\//www.contoso.com.

![Testen der Contoso-Website im Anwendungsgateway](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Ändern Sie die Adresse in Ihre andere Domäne, z. B. http:\//www.contoso.org. Danach sollten Sie sehen, dass der Datenverkehr an den Listener für www\.contoso.com zurückgeleitet wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Hinzufügen der Listener und einer Umleitungsregel
> * Erstellen einer VM-Skalierungsgruppe mit dem Back-End-Pool
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne
