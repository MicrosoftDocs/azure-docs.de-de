---
title: Umleiten externen Datenverkehrs mithilfe der CLI – Azure Application Gateway
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI ein Anwendungsgateway erstellen, das externen Webdatenverkehr an den entsprechenden Pool umleitet.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 838c2dc887790bb12b390261d94748595232d8b3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565856"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mit externer Umleitung durch die Azure CLI

Sie können mit der Azure CLI [eine Umleitung des Webdatenverkehrs](multiple-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](overview.md) erstellen. In diesem Tutorial konfigurieren Sie einen Listener und eine Regel, die den am Anwendungsgateway eingehenden Webdatenverkehr an eine externe Website umleitet.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten des Netzwerks
* Erstellen eines Listeners und einer Umleitungsregel
* Erstellen eines Anwendungsgateways

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

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk namens *myVNet* und ein Subnetz namens *myAGSubnet*. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse namens *myAGPublicIPAddress*. Diese Ressourcen werden verwendet, um Netzwerkkonnektivität für das Anwendungsgateway und die zugehörigen Ressourcen bereitzustellen.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Sie können [az network application-gateway create](/cli/azure/network/application-gateway) verwenden, um ein Anwendungsgateway namens *myAppGateway* zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Hinzufügen der Umleitungskonfiguration

Fügen Sie mit [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) die Umleitungskonfiguration hinzu, die Datenverkehr von *www\.contoso.org* an den Listener für *www\.contoso.com* an das Anwendungsgateway sendet.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Hinzufügen eines Listeners und einer Routingregel

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise weiterleiten kann. Erstellen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway) den Listener, bei dem der Front-End-Port mit [az network application-gateway frontend-port create](/cli/azure/network/application-gateway) erstellt wurde. Für den Listener ist eine Regel erforderlich, damit bekannt ist, an welches Ziel eingehender Datenverkehr gesendet werden soll. Erstellen Sie mit [az network application-gateway rule create](/cli/azure/network/application-gateway) eine grundlegende Regel namens *redirectRule*.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, können Sie [az network public-ip show](/cli/azure/network/public-ip) verwenden. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

Daraufhin sollte *bing.com* in Ihrem Browser aufgerufen werden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Anwendungsgateways mit interner Umleitung über die Azure-Befehlszeilenschnittstelle](redirect-internal-site-cli.md)
