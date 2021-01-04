---
title: Hosten mehrerer Websites mithilfe der CLI
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, mit dem mehrere Websites gehostet werden.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 350962aed89d04c5508e7b2c50e8a838cd5a7174
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566145"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways als Host für mehrere Websites mit der Azure-Befehlszeilenschnittstelle

Sie können mit der Azure-Befehlszeilenschnittstelle ein [Hosting mehrerer Websites](multiple-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](overview.md) erstellen. In diesem Artikel definieren Sie Back-End-Adresspools mithilfe von VM-Skalierungsgruppen. Anschließend konfigurieren Sie Listener und Regeln basierend auf Domänen in Ihrem Besitz, um sicherzustellen, dass Webdatenverkehr von geeigneten Servern in den Pools empfangen wird. In diesem Artikel wird vorausgesetzt, dass Sie mehrere Domänen besitzen, und es werden Beispiele zu *www\.contoso.com* und *www\.fabrikam.com* verwendet.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten des Netzwerks
* Erstellen eines Anwendungsgateways
* Erstellen von Back-End-Listenern
* Erstellen von Routingregeln
* Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools
* Erstellen eines CNAME-Eintrags in Ihrer Domäne

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="Anwendungsgateway für mehrere Standorte":::

Sie können für dieses Verfahren auch [Azure PowerShell](tutorial-multiple-sites-powershell.md) verwenden.

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

Erstellen Sie mit *az network vnet create* ein virtuelles Netzwerk und ein Subnetz namens [myAGSubnet](/cli/azure/network/vnet). Dann können Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) das Subnetz hinzufügen, das von den Back-End-Servern benötigt wird. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse namens *myAGPublicIPAddress*.

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

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Sie können [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) verwenden, um das Anwendungsgateway zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myAGPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben. 

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

### <a name="add-the-backend-pools"></a>Hinzufügen der Back-End-Pools

Fügen Sie mit [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create) die Back-End-Pools hinzu, die für die Back-End-Server benötigt werden.
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Hinzufügen der Listener

Fügen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) die Listener hinzu, die zum Weiterleiten von Datenverkehr erforderlich sind.

>[!NOTE]
> Mit der SKU für Application Gateway oder WAF v2 können Sie auch bis zu fünf Hostnamen pro Listener konfigurieren und Platzhalterzeichen im Hostnamen verwenden. Weitere Informationen finden Sie unter [Hostnamen mit Platzhalterzeichen im Listener](multiple-site-overview.md#wildcard-host-names-in-listener-preview).
>Um in der Azure CLI mehrere Hostnamen und Platzhalterzeichen in einem Listener zu verwenden, müssen Sie `--host-names` statt `--host-name` verwenden. Sie können bis zu fünf Hostnamen als durch Leerzeichen getrennte Werte angeben. Zum Beispiel, `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Hinzufügen von Routingregeln

Regeln werden in der Reihenfolge ihrer Auflistung verarbeitet. Der Datenverkehr wird unter Verwendung der ersten übereinstimmenden Regel weitergeleitet, unabhängig von spezifischen Eigenschaften. Wenn Sie beispielsweise eine Regel mit einem einfachen Listener und eine Regel mit einem Listener für mehrere Standorte auf demselben Port aktiviert haben, muss die Regel mit dem Listener für mehrere Standorte vor der Regel mit dem einfachen Listener aufgeführt sein, damit die Regel für mehrere Standorte wie erwartet funktioniert. 

In diesem Beispiel erstellen Sie zwei neue Regeln und löschen die Standardregel, die beim Bereitstellen des Anwendungsgateways erstellt wurde. Sie können die Regel mit [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) hinzufügen.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Erstellen von VM-Skalierungsgruppen

In diesem Beispiel erstellen Sie drei VM-Skalierungsgruppen, die die drei Back-End-Pools im Anwendungsgateway unterstützen. Die erstellten Skalierungsgruppen werden *myvmss1*, *myvmss2* und *myvmss3* genannt. Jede Skalierungsgruppe enthält zwei VM-Instanzen, auf denen Sie IIS installieren.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Erstellen eines CNAME-Eintrags in Ihrer Domäne

Nachdem das Anwendungsgateway mit der zugehörigen öffentlichen IP-Adresse erstellt wurde, können Sie die DNS-Adresse abrufen und zum Erstellen eines CNAME-Eintrags in Ihrer Domäne verwenden. Sie können [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) verwenden, um die DNS-Adresse des Anwendungsgateways abzurufen. Kopieren Sie den *fqdn*-Wert der DNSSettings, und verwenden Sie ihn als Wert für den erstellten CNAME-Eintrag. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Die Verwendung von A-Einträgen wird nicht empfohlen, weil sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Geben Sie Ihren Domänennamen in die Adressleiste Ihres Browsers ein. Beispiel: http:\//www.contoso.com.

![Testen der Contoso-Website im Anwendungsgateway](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Ändern Sie die Adresse in Ihre andere Domäne. Die Ausgabe sollte in etwa wie folgt aussehen:

![Testen der Fabrikam-Website im Anwendungsgateway](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis](./tutorial-url-route-cli.md)
