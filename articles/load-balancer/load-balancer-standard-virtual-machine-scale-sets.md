---
title: Azure Load Balancer Standard und VM-Skalierungsgruppen
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: In diesem Lernpfad beginnen Sie mit Azure Load Balancer Standard und VM-Skalierungsgruppen.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697440"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer mit Azure-VM-Skalierungsgruppen

Bei der Arbeit mit VM-Skalierungsgruppen und Lastenausgleich müssen folgende Richtlinien berücksichtigt werden:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Portweiterleitung und NAT-Regeln für eingehenden Datenverkehr:
  * Nachdem die Skalierungsgruppe erstellt wurde, kann der Back-End-Port für eine Lastenausgleichsregel, die von einem Integritätstest des Load Balancers verwendet wird, nicht mehr geändert werden. Zum Ändern des Ports können Sie den Integritätstest entfernen, indem Sie die Azure-VM-Skalierungsgruppe aktualisieren, den Port aktualisieren und dann den Integritätstest erneut konfigurieren.
  * Bei Verwendung der VM-Skalierungsgruppe im Back-End-Pool des Lastenausgleichsmoduls werden die standardmäßigen NAT-Regeln für eingehenden Datenverkehr automatisch erstellt.
## <a name="inbound-nat-pool"></a>NAT-Eingangspool:
  * Jede VM-Skalierungsgruppe muss mindestens über einen NAT-Pool für eingehenden Datenverkehr verfügen. 
  * Bei einem NAT-Eingangspool handelt es sich um eine Sammlung eingehender NAT-Regeln für eingehenden Datenverkehr. Ein NAT-Eingangspool kann nicht mehrere VM-Skalierungsgruppen unterstützen.
  * Wenn Sie einen NAT-Pool aus einer vorhandenen VM-Skalierungsgruppe löschen möchten, müssen Sie zunächst den NAT-Pool aus der Skalierungsgruppe entfernen. Ein vollständiges Beispiel unter Verwendung der CLI ist nachfolgend dargestellt:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Lastenausgleichsregeln:
  * Bei Verwendung der VM-Skalierungsgruppe im Back-End-Pool des Lastenausgleichsmoduls werden die standardmäßigen Lastenausgleichsregeln automatisch erstellt.
## <a name="outbound-rules"></a>Ausgangsregeln:
  *  Zum Erstellen einer Ausgangsregel für einen Back-End-Pool, der bereits durch eine Lastenausgleichsregel referenziert wird, müssen Sie im Portal zuerst die Option **Implizite Ausgangsregeln erstellen** auf **Nein** festlegen, wenn die Lastenausgleichsregel für eingehenden Datenverkehr erstellt wird.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Erstellen einer Lastenausgleichsregel" border="true":::

Mit den folgenden Methoden können Sie eine VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz bereitstellen.

* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe des Azure-Portals](./configure-vm-scale-set-portal.md).
* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe von Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe der Azure CLI](./configure-vm-scale-set-cli.md).