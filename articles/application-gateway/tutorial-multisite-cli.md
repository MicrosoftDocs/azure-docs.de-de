---
title: Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites – Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, mit dem mehrere Websites gehostet werden.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 5508a1dbd105fc47a4ed7b3484f55532904956ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60407115"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites mit der Azure-Befehlszeilenschnittstelle

Sie können mit der Azure-Befehlszeilenschnittstelle ein [Hosting mehrerer Websites](application-gateway-multi-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](application-gateway-introduction.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von VM-Skalierungsgruppen. Anschließend konfigurieren Sie Listener und Regeln basierend auf Domänen in Ihrem Besitz, um sicherzustellen, dass Webdatenverkehr von geeigneten Servern in den Pools empfangen wird. In diesem Tutorial wird vorausgesetzt, dass Sie mehrere Domänen besitzen und die Beispiele zu *www.contoso.com* und *www.fabrikam.com* verwenden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Erstellen der Listener und Routingregeln
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

![Beispiel zum Routing für mehrere Websites](./media/tutorial-multisite-cli/scenario.png)

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

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

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
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.

### <a name="add-the-backend-pools"></a>Hinzufügen der Back-End-Pools

Fügen Sie mit [az network application-gateway address-pool create](/cli/azure/network/application-gateway) die Back-End-Pools namens *contosoPool* und *fabrikamPool* hinzu, die für die Back-End-Server benötigt werden.

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

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an den Back-End-Pool weiterleiten kann. In diesem Tutorial erstellen Sie zwei Listener für Ihre beiden Domänen. In diesem Beispiel werden Listener für die Domänen *www.contoso.com* und *www.fabrikam.com* erstellt. 

Fügen Sie mit [az network application-gateway http-listener create](/cli/azure/network/application-gateway) die Listener namens *contosoListener* und *fabrikamListener* hinzu, die zum Weiterleiten des Datenverkehrs erforderlich sind.

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

Regeln werden in der Reihenfolge verarbeitet, in der sie erstellt werden, und Datenverkehr wird anhand der ersten Regel weitergeleitet, die mit der an das Anwendungsgateway gesendeten URL übereinstimmt. Wenn Sie beispielsweise eine Regel mit einem einfachen Listener und eine Regel mit einem Listener für mehrere Standorte auf demselben Port aktiviert haben, muss die Regel mit dem Listener für mehrere Standorte vor der Regel mit dem einfachen Listener aufgeführt sein, damit die Regel für mehrere Standorte wie erwartet funktioniert. 

In diesem Beispiel erstellen Sie zwei neue Regeln und löschen die Standardregel, die während der Erstellung des Anwendungsgateways erstellt wurde. Sie können die Regel mit [az network application-gateway rule create](/cli/azure/network/application-gateway) hinzufügen.

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

In diesem Beispiel erstellen Sie drei VM-Skalierungsgruppen, die die drei Back-End-Pools im Anwendungsgateway unterstützen. Die erstellten Skalierungsgruppen werden *myvmss1*, *myvmss2* und *myvmss3* genannt. Jede Skalierungsgruppe enthält zwei VM-Instanzen, auf denen Sie NGINX installieren.

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
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Erstellen eines CNAME-Eintrags in Ihrer Domäne

Nachdem das Anwendungsgateway mit der zugehörigen öffentlichen IP-Adresse erstellt wurde, können Sie die DNS-Adresse abrufen und zum Erstellen eines CNAME-Eintrags in Ihrer Domäne verwenden. Sie können [az network public-ip show](/cli/azure/network/public-ip) verwenden, um die DNS-Adresse des Anwendungsgateways abzurufen. Kopieren Sie den *fqdn*-Wert der DNSSettings, und verwenden Sie ihn als Wert für den erstellten CNAME-Eintrag. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Die Verwendung von A-Einträgen wird nicht empfohlen, weil sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Geben Sie Ihren Domänennamen in die Adressleiste Ihres Browsers ein. Z.B. http://www.contoso.com.

![Testen der Contoso-Website im Anwendungsgateway](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Ändern Sie die Adresse in Ihre andere Domäne. Die Ausgabe sollte in etwa wie folgt aussehen:

![Testen der Fabrikam-Website im Anwendungsgateway](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Erstellen der Listener und Routingregeln
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, was Sie mit dem Anwendungsgateway tun können.](application-gateway-introduction.md)
