---
title: Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe der Azure CLI
titlesuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI eine öffentliche IP-Adresse mit der Routingpräferenz für Internetdatenverkehr erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: c3ce69dc9364d71a501f122a7150ad4a59869422
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96534011"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI die Routingpräferenz über das ISP-Netzwerk (Option **Internet**) für eine öffentliche IP-Adresse konfigurieren. Nach der Erstellung der öffentlichen IP-Adresse können Sie sie den folgenden Azure-Ressourcen für eingehenden und ausgehenden Internetdatenverkehr zuordnen:

* Virtueller Computer
* VM-Skalierungsgruppe
* Azure Kubernetes Service (AKS)
* Lastenausgleich mit Internetzugriff
* Application Gateway
* Azure Firewall

Die Routingpräferenz für die öffentliche IP-Adresse ist für alle Azure-Dienste standardmäßig auf das globale Netzwerk von Microsoft festgelegt und kann jedem Azure-Dienst zugeordnet werden.

> [!IMPORTANT]
> „Routingpräferenz“ ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.49 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region **USA, Osten** erstellt:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Erstellen Sie eine öffentliche IP-Adresse mit Routingpräferenz vom Typ **Internet** mithilfe des Befehls [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) im unten dargestellten Format.

Mit dem folgenden Befehl wird eine neue öffentliche IP-Adresse mit dem Routingpräferenztyp **Internet** in der Azure-Region **USA, Osten** erstellt.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Die Routingpräferenz unterstützt derzeit nur öffentliche IPv4-IP-Adressen.

Sie können die oben erstellte öffentliche IP-Adresse einem virtuellen [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen. Lesen Sie den Abschnitt zur CLI auf der Tutorialseite: [Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer](associate-public-ip-address-vm.md#azure-cli), um die öffentliche IP-Adresse Ihrem virtuellen Computer zuzuordnen. Sie können die oben erstellte öffentliche IP-Adresse auch einer [Azure Load Balancer](../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration des Lastenausgleichs zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Routingpräferenz in öffentlichen IP-Adressen](routing-preference-overview.md). 
- [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe der Azure CLI](configure-routing-preference-virtual-machine-cli.md)

