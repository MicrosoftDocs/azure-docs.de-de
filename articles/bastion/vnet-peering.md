---
title: VNET-Peering und die Azure Bastion-Architektur
description: In diesem Artikel erfahren Sie, wie Sie das VNET-Peering und Azure Bastion gemeinsam zum Herstellen einer Verbindung mit VMs verwenden.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 472261666c86b666efd09c7217d12e5a795a50d9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094885"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNET-Peering und Azure Bastion (Vorschau)

Azure Bastion kann zusammen mit VNET-Peering verwendet werden. Wenn VNET-Peering konfiguriert ist, müssen Sie Azure Bastion nicht in jedem Peering-VNET bereitstellen. Wenn Sie also in einem virtuellen Netzwerk (VNET) einen Azure Bastion-Host konfiguriert haben, kann dieser zum Herstellen einer Verbindung mit VMs in einem Peering-VNET verwendet werden, ohne dass ein zusätzlicher Bastionhost bereitgestellt werden muss. Weitere Informationen zum VNET-Peering finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion funktioniert mit den folgenden Peeringtypen:

* **Peering virtueller Netzwerke:** Herstellen von Verbindungen zwischen virtuellen Netzwerken in derselben Azure-Region.
* **Globales Peering virtueller Netzwerke:** Herstellen von Verbindungen zwischen virtuellen Netzwerken über Azure-Regionen hinweg.

## <a name="architecture"></a>Aufbau

Wenn VNET-Peering konfiguriert ist, kann Azure Bastion in Hub-and-Spoke- oder Full-Mesh-Topologien bereitgestellt werden. Diese Bereitstellung von Azure Bastion erfolgt pro virtuellem Netzwerk und nicht pro Abonnement/Konto oder virtuellem Computer.

Nachdem Sie den Azure Bastion-Dienst in Ihrem virtuellen Netzwerk bereitgestellt haben, steht die RDP-/SSH-Funktion für alle VMs im gleichen VNET und in allen Peering-VNETs zur Verfügung. So können Sie Bastion zentral in nur einem VNET bereitstellen und trotzdem VMs erreichen, die in einem Peering-VNET bereitgestellt wurden.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagramm von Design und Architektur":::

In dieser Abbildung ist die Architektur einer Azure Bastion-Bereitstellung in einem Hub-and-Spoke-Modell dargestellt. Sie sehen in diesem Diagramm die folgende Konfiguration:

* Der Bastionhost wird im zentralen virtuellen Netzwerk am Hub bereitgestellt.
* Eine zentrale Netzwerksicherheitsgruppe (NSG) wird bereitgestellt.
* Für die Azure-VM ist keine öffentliche IP-Adresse erforderlich.

**Schritte:**

1. Stellen Sie in einem HTML5-Browser eine Verbindung mit dem Azure-Portal her.
1. Wählen Sie die VM aus, mit der Sie eine Verbindung herstellen möchten.
1. Azure Bastion wird nahtlos über das Peering-VNET erkannt.
1. Mit nur einem Klick wird die RDP- oder SSH-Sitzung im Browser geöffnet. Informationen zu den Grenzwerten für gleichzeitige RDP- und SSH-Sitzungen finden Sie unter [RDP- und SSH-Sitzungen](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Herstellen einer Verbindung":::

   Weitere Informationen zur Verbindung mit einer VM über Azure Bastion finden Sie unter:

   * [Herstellen einer Verbindung mit einem virtuellen Windows-Computer mit Azure Bastion](bastion-connect-vm-rdp.md)
   * [Herstellen einer SSH-Verbindung mit einem virtuellen Linux-Computer über Azure Bastion](bastion-connect-vm-ssh.md)

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).