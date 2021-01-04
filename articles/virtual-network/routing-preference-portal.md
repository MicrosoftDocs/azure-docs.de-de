---
title: 'Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse: Azure-Portal'
description: Hier erfahren Sie, wie Sie eine öffentliche IP-Adresse mit der Routingpräferenz für Internetdatenverkehr erstellen.
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
ms.openlocfilehash: ef1e33a2e43f26dcaf794b2ed81f27d39639b52d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533977"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe des Azure-Portals

In diesem Artikel erfahren Sie, wie Sie die [Routingpräferenz](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) über das ISP-Netzwerk (Option **Internet**) für eine öffentliche IP-Adresse konfigurieren. Nach der Erstellung der öffentlichen IP-Adresse können Sie sie den folgenden Azure-Ressourcen für eingehenden und ausgehenden Internetdatenverkehr zuordnen:

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

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Erstellen einer öffentlichen IP-Adresse mit einer Routingpräferenz
1. Melden Sie sich beim [Azure-Portal](https://preview.portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen**. 
3. Geben Sie in das Suchfeld *Öffentliche IP-Adresse* ein.
3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adresse** aus. Wählen Sie anschließend auf der Seite **Öffentliche IP-Adressen** die Option **Erstellen** aus.
3. Wählen Sie **Internet** aus den Optionen für **Routingpräferenz** aus.

      ![Erstellen einer öffentlichen IP-Adresse](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Öffentliche IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. Die Routingpräferenz unterstützt derzeit jedoch nur IPv4.

Sie können die oben erstellte öffentliche IP-Adresse einem virtuellen [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen. Lesen Sie den Abschnitt zur CLI auf der Tutorialseite [Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer](associate-public-ip-address-vm.md#azure-cli), um die öffentliche IP-Adresse Ihrem virtuellen Computer zuzuordnen. Sie können die oben erstellte öffentliche IP-Adresse auch einer [Azure Load Balancer](../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration des Lastenausgleichs zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [öffentliche IP-Adressen mit Routingpräferenz](routing-preference-overview.md).
- [Konfigurieren der Routingpräferenz für einen virtuellen Computer](tutorial-routing-preference-virtual-machine-portal.md)
- [Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe von PowerShell](routing-preference-powershell.md)
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
