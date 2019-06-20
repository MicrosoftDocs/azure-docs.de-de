---
title: 'Aktivieren von Web Application Firewall: Azure CLI'
description: Erfahren Sie, wie Sie mit Azure CLI Webdatenverkehr mit einer Web Application Firewall für ein Anwendungsgateway einschränken.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 5/20/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1822fe032a7c7a6382dbae2cb9f7095d1d076008
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955492"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Einschränken des Webdatenverkehrs mit einer Web Application Firewall mit Azure CLI

Sie können den Datenverkehr mit einer [Web Application Firewall](waf-overview.md) (WAF) für ein [Anwendungsgateway](overview.md) einschränken. Die WAF verwendet [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-Regeln, um Ihre Anwendung zu schützen. Diese Regeln beinhalten den Schutz vor Angriffen z.B. durch Einschleusung von SQL-Befehlen, siteübergreifendes Scripting und Sitzungsübernahmen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways mit aktivierter WAF
> * Erstellen einer Skalierungsgruppe für virtuelle Computer
> * Erstellen eines Speicherkontos und Konfigurieren der Diagnose

![Web Application Firewall – Beispiel](./media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Sie können für dieses Verfahren auch [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Azure-Ressourcengruppe mit dem Namen *myResourceGroupAG*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Das virtuelle Netzwerk und die Subnetze werden verwendet, um Netzwerkkonnektivität für das Anwendungsgateway und die zugehörigen Ressourcen bereitzustellen. Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) und [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein virtuelles Netzwerk namens *myVNet* und ein Subnetz namens *myAGSubnet*. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) eine öffentliche IP-Adresse namens *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Erstellen eines Anwendungsgateways mit einer WAF

Sie können [az network application-gateway create](/cli/azure/network/application-gateway) verwenden, um ein Anwendungsgateway namens *myAppGateway* zu erstellen. Wenn Sie über die Azure-Befehlszeilenschnittstelle ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Das Anwendungsgateway wird dem Subnetz *myAGSubnet* und der IP-Adresse *myAGPublicIPAddress* zugewiesen, das bzw. die Sie zuvor erstellt haben.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Es kann einige Minuten dauern, bis das Anwendungsgateway erstellt ist. Nachdem das Anwendungsgateway erstellt wurde, sehen Sie diese neuen Features:

- *appGatewayBackendPool*: Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
- *appGatewayBackendHttpSettings*: Gibt an, dass zur Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
- *appGatewayHttpListener*: Der Standardlistener, der *appGatewayBackendPool* zugeordnet ist.
- *appGatewayFrontendIP*: Hiermit wird *myAGPublicIPAddress* zu *appGatewayHttpListener* zugewiesen.
- *rule1*:Die Standardroutingregel, die *appGatewayHttpListener* zugeordnet ist.

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, die zwei Server für den Back-End-Pool im Anwendungsgateway bereitstellt. Die virtuellen Computer in der Skalierungsgruppe sind dem Subnetz *myBackendSubnet* zugeordnet. Zum Erstellen der Skalierungsgruppe können Sie [az vmss create](/cli/azure/vmss#az-vmss-create) verwenden.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Erstellen eines Speicherkontos und Konfigurieren der Diagnose

In diesem Artikel verwendet das Anwendungsgateway ein Speicherkonto, um Daten zum Zweck der Erkennung und Prävention zu speichern. Sie können auch Azure Monitor-Protokolle oder Event Hub verwenden, um Daten aufzuzeichnen. 

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) ein Speicherkonto namens *myagstore1*.

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Konfigurieren der Diagnose

Konfigurieren Sie die Diagnose so, dass Daten in den Protokollen „ApplicationGatewayAccessLog“, „ApplicationGatewayPerformanceLog“ und „ApplicationGatewayFirewallLog“ aufgezeichnet werden. Ersetzen Sie `<subscriptionId>` durch den Bezeichner Ihres Abonnements, und konfigurieren Sie dann mit [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) die Diagnose.

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Um die öffentliche IP-Adresse des Anwendungsgateways abzurufen, verwenden Sie [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste Ihres Browsers ein.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Anwendungsgateways mit SSL-Beendigung](./tutorial-ssl-cli.md)
