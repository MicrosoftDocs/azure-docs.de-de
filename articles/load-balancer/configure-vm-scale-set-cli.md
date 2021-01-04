---
title: Konfigurieren von VM-Skalierungsgruppen mit einer vorhandenen Azure Load Balancer-Instanz – Azure CLI
description: Hier erfahren Sie, wie Sie eine VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe der Azure CLI konfigurieren.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518208"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie eine VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz konfigurieren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen 

- Sie benötigen einen vorhandenen Standard-SKU-Lastenausgleich in dem Abonnement, in dem die VM-Skalierungsgruppe bereitgestellt werden soll.

- Sie benötigen eine Azure Virtual Network-Instanz für die VM-Skalierungsgruppe.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Bereitstellen einer VM-Skalierungsgruppe mit einem vorhandenen Lastenausgleich

Ersetzen Sie die Werte in den spitzen Klammern durch die Namen der Ressourcen in Ihrer Konfiguration.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Im folgenden Beispiel wird eine VM-Skalierungsgruppe mit den folgenden Ressourcen bereitgestellt:

- Eine VM-Skalierungsgruppe namens **myVMSS**
- Ein Azure Load Balancer namens **myLoadBalancer**
- Ein Back-End-Pool für den Lastenausgleich namens **myBackendPool**
- Ein virtuelles Azure-Netzwerk namens **myVnet**
- Ein Subnetz namens **mySubnet**
- Eine Ressourcengruppe namens **myResourceGroup**
- Ubuntu-Serverimage für die VM-Skalierungsgruppe

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Nachdem die Skalierungsgruppe erstellt wurde, kann der Back-End-Port für eine Lastenausgleichsregel, die von einem Integritätstest des Load Balancers verwendet wird, nicht mehr geändert werden. Zum Ändern des Ports können Sie den Integritätstest entfernen, indem Sie die Azure-VM-Skalierungsgruppe aktualisieren, den Port aktualisieren und dann den Integritätstest erneut konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz konfiguriert.  Weitere Informationen über VM-Skalierungsgruppen und Lastenausgleiche finden Sie unter:

- [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)
- [Was sind Skalierungsgruppen für virtuelle Computer?](../virtual-machine-scale-sets/overview.md)
                                