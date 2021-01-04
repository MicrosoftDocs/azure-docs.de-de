---
title: 'Befehlszeilenschnittstelle: Bereitstellen eines privaten Endpunkts für eine Web-App mithilfe der Azure-Befehlszeilenschnittstelle'
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen privaten Endpunkt für Ihre Web-App bereitstellen.
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 21f937d95c6cd0dafa27daae50d7a74648af7bdc
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006105"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Erstellen einer App Service-App und Bereitstellen eines privaten Endpunkts mithilfe der Azure-Befehlszeilenschnittstelle

Dieses Beispielskript dient zum Erstellen einer App in App Service mit den zugehörigen Ressourcen sowie zum Bereitstellen eines privaten Endpunkts.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie eine Ressource erstellen können, müssen Sie eine Ressourcengruppe zum Hosten der Web-App, des virtuellen Netzwerks und anderer Netzwerkkomponenten erstellen. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe namens *myResourceGroup* am Standort *francecentral* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Erstellen eines App Service-Plans

Sie müssen einen App Service-Plan zum Hosten Ihrer Web-App erstellen.
Erstellen Sie einen App Service-Plan mithilfe von [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create).
In diesem Beispiel wird ein App Service-Plan namens *myAppServicePlan* am Standort *francecentral* mit der SKU *P1V2* und einem einzelnen Worker erstellt: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Nachdem Sie nun über einen App Service-Plan verfügen, können Sie eine Web-App bereitstellen.
Erstellen Sie eine Web-App mithilfe von [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create.
In diesem Beispiel wird eine Web-App namens *mySiteName* im Plan *myAppServicePlan* erstellt.

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Erstellen eines VNET

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. In diesem Beispiel wird ein virtuelles Standardnetzwerk namens *myVNet* mit einem einzelnen Subnetz namens *mySubnet* erstellt:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Konfigurieren des Subnetzes 

Sie müssen das Subnetz aktualisieren, um Netzwerkrichtlinien für private Endpunkte zu deaktivieren. Aktualisieren Sie eine Subnetzkonfiguration mit dem Namen *mySubnet* mit [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Erstellen des privaten Endpunkts

Erstellen Sie den privaten Endpunkt für Ihre Web-App mithilfe von [az network private-endpoint create](/cli/azure/network/private-endpoint). In diesem Beispiel wird ein privater Endpunkt namens *myPrivateEndpoint* im Subnetz *mySubnet* des VNET *myVNet* erstellt. Dabei werden eine Verbindung namens *myConnectionName* mit der Ressourcen-ID der eigenen Web-App (/subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp) sowie der Gruppenparameter *sites* für den Web-App-Typ verwendet. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Konfigurieren der privaten Zone

Zum Schluss muss eine private DNS-Zone namens *privatelink.azurewebsites.net* erstellt werden, die mit dem VNET verknüpft ist, um den DNS-Namen der Web-App aufzulösen.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
- Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
