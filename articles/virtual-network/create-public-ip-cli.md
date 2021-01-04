---
title: 'Erstellen einer öffentlichen IP-Adresse: Azure CLI'
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI eine öffentliche IP-Adresse erstellen.
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 76af225b6f93e0a47799234e3e2b430344f68787
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009992"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse mithilfe der Azure CLI

In diesem Artikel wird gezeigt, wie Sie mithilfe der Azure CLI eine öffentliche IP-Adressressource erstellen. Weitere Informationen zu den Ressourcen, denen diese zugeordnet werden kann, zum Unterschied zwischen der SKU „Basic“ und „Standard“ und andere zugehörige Informationen finden Sie unter [Öffentliche IP-Adressen](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  In diesem Beispiel konzentrieren wir uns ausschließlich auf IPv4-Adressen. Weitere Informationen zu IPv6-Adressen finden Sie unter [IPv6 für Azure VNET](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe namens **myResourceGroup** am Speicherort **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Öffentliche IP-Adresse erstellen

---
# <a name="standard-sku---using-zones"></a>[**Standard-SKU: Verwenden von Zonen**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), um in **myResourceGroup** eine zonenredundante öffentliche Standard-IP-Adresse namens **myStandardZRPublicIP** zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Führen Sie für Versionen der API, die älter als 2020-08-01 ist, den oben genannten Befehl aus, ohne einen Zonenparameter anzugeben, um eine zonenredundante IP-Adresse zu erstellen. 
>

Verwenden Sie den folgenden Befehl, um eine standardmäßige zonale öffentliche IP-Adresse in Zone 2 namens **myStandardZonalPublicIP** in **myResourceGroup** zu erstellen:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Beachten Sie, dass die oben genannten Optionen für Zonen nur eine gültige Auswahl in Regionen mit [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) sind.

# <a name="standard-sku---no-zones"></a>[**Standard-SKU: Keine Zonen**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), um eine öffentliche Standard-IP-Adresse als nicht zonale Ressource namens **myStandardPublicIP** in **myResourceGroup** zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Diese Auswahl ist in allen Regionen gültig und die Standardauswahl für öffentliche Standard-IP-Adressen in Regionen ohne [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-create-public-ip-basic)

Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), um in **myResourceGroupLB** eine statische öffentliche IP-Adresse des Typs „Basic“ namens **myBasicPublicIP** zu erstellen.  Öffentliche IP-Adressen des Typs „Basic“ verwenden nicht das Konzept von Verfügbarkeitszonen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann **dynamische** IP-Zuweisung ausgewählt werden, indem die Zuweisungsmethode (allocation-method) in „Dynamic“ geändert wird.

---

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den oben aufgeführten einzelnen Variablen finden Sie unter [Verwalten öffentlicher IP-Adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
