---
title: Einrichten von IP-Adressen für das Verbinden nach einer Notfallwiederherstellung und einem Failover in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie IP-Adressen für die Verbindung mit virtuellen Azure-Computern nach einer Notfallwiederherstellung und einem Failover von lokalen Computern mit Azure Site Recovery einrichten.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 2e1cbb2446501d0afda29eba179e388b5a22e6a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772256"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Einrichten von IP-Adressen für Verbindungen mit virtuellen Azure-Computern nach einem Failover

In diesem Artikel werden die Netzwerkanforderungen für die Verbindung mit Azure-VMs erläutert, nachdem der Service [Azure Site Recovery](site-recovery-overview.md) für die Replikation und das Failover zu Azure verwendet wird.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Die Verbindungsmethoden, die Sie verwenden können
> * Wie Sie eine andere IP-Adresse für replizierte Azure-VMs verwenden
> * Wie Sie nach einem Failover IP-Adressen für Azure-VMs beibehalten

## <a name="connecting-to-replica-vms"></a>Herstellen einer Verbindung mit Replikat-VMs

Bei der Planung der Replikation und Failoverstrategie ist eine der wichtigsten Fragen, wie eine Verbindung zum virtuellen Azure-Computer nach einem Failover hergestellt werden kann. Es gibt verschiedene Auswahlmöglichkeiten beim Entwerfen der Netzwerkstrategie für die virtuellen Azure-Replikatcomputer:

- **Verwenden einer anderen IP-Adresse**: Sie können wählen, einen anderen IP-Adressbereich für das replizierte Netzwerk des virtuellen Azure-Computers zu verwenden. In diesem Szenario erhält der virtuelle Computer eine neue IP-Adresse nach einem Failover. Dazu ist ein DNS-Update erforderlich.
- **Behalten derselben IP-Adresse**: Sie möchten möglicherweise für das Azure-Netzwerk nach dem Failover denselben IP-Adressbereich verwenden, der sich an Ihrem lokalen Primärstandort befindet. Das Verwenden derselben IP-Adressen vereinfacht die Wiederherstellung, indem Probleme im Zusammenhang mit dem Netzwerk nach dem Failover reduziert werden. Wenn Sie in Azure replizieren, müssen Sie Routen mit dem neuen Speicherort der IP-Adressen nach dem Failover aktualisieren.

## <a name="retaining-ip-addresses"></a>Beibehalten der IP-Adressen

Site Recovery bietet die Möglichkeit, feste IP-Adressen beizubehalten, wenn ein Failover in Azure mit einem Subnetzfailover erfolgt.

- Mit einem Subnetzfailover existiert ein bestimmtes Subnetz an Standort 1 oder 2, jedoch nie gleichzeitig an beiden Standorten.
- Um den IP-Adressraum im Falle eines Failovers beizubehalten, sorgen Sie programmgesteuert dafür, dass die Routerinfrastruktur die Subnetze von einem Standort an einen anderen verschiebt.
- Die Subnetze verschieben sich während des Failovers mit den zugeordneten geschützten virtuellen Computern. Der größte Nachteil ist, dass Sie im Fall eines Fehlers das gesamte Subnetz verschieben müssen.


### <a name="failover-example"></a>Beispiel für ein Failover

Sehen wir uns mithilfe des fiktiven Unternehmens Woodgrove Bank ein Beispiel für ein Failover auf Azure an.

- Woodgrove Bank hostet die Geschäftsanwendungen an einem lokalen Standort. Sie hosten ihre mobilen Apps in Azure.
- Es ist eine Standort-zu-Standort-VPN-Konnektivität zwischen deren lokalen Edgenetzwerk und dem virtuellen Azure-Netzwerk vorhanden. Aufgrund dieser VPN-Konnektivität erscheint das virtuelle Netzwerk in Azure als Erweiterung des lokalen Netzwerks.
- Woodgrove möchte die lokalen Workloads in Azure mit Site Recovery replizieren.
  - Woodgrove verfügt über Apps, die von hartcodierten IP-Adressen abhängig sind, daher müssen die IP-Adressen der Apps nach einem Failover zu Azure beibehalten werden.
  - In Azure ausgeführte Ressourcen verwenden den IP-Adressbereich 172.16.1.0/24, 172.16.2.0/24.

![Vor dem Subnetzfailover](./media/site-recovery-network-design/network-design7.png)

**Infrastruktur vor dem Failover**


Da Woodgrove in der Lage sein muss, seine virtuellen Computer zu Azure zu replizieren und gleichzeitig die IP-Adressen beizubehalten, muss das Unternehmen folgende Schritte ausführen:


1. Erstellen eines virtuellen Azure-Netzwerks, in dem die Azure-VMs nach einem Failover der lokalen Computer erstellt werden. Dies sollte eine Erweiterung des lokalen Netzwerks sein, sodass für Anwendungen ein nahtloses Failover ausgeführt werden kann.
2. In Site Recovery weisen sie den Computereigenschaften vor dem Failover die gleiche IP-Adresse zu. Nach dem Failover weist Site Recovery diese Adresse der Azure-VM zu.
3. Nachdem das Failover ausgeführt wird und die Azure-VMs mit der gleichen IP-Adresse erstellt wurden, verbinden sie sich mithilfe einer [VNet-zu-VNet-Verbindung](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mit dem Netzwerk. Für diese Aktion kann ein Skript erstellt werden.
4. Sie müssen Routen ändern, um darauf zu reagieren, dass 192.168.1.0/24 sich jetzt in Azure befindet.


**Infrastruktur nach einem Failover**

![Nach dem Subnetzfailover](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Standort-zu-Standort-Verbindung

Nach einem Failover kann Woodgrove zusätzlich zu einer VNet-zu-VNet-Verbindung eine Standort-zu-Standort-VPN-Konnektivität einrichten:
- Beim Einrichten einer Standort-zu-Standort-Verbindung im Azure-Netzwerk können Sie nur dann Datenverkehr an den lokalen Standort (local-network) weiterleiten, wenn sich der IP-Adressbereich vom lokalen IP-Adressbereich unterscheidet. Der Grund dafür ist, dass Azure Stretchingsubnetze nicht unterstützt. Sie können also im Azure-Netzwerk das lokale Netzwerk 192.168.1.0/24 nicht hinzufügen, wenn Sie über das lokale Subnetz 192.168.1.0/24 verfügen. Dies ist zu erwarten, da Azure nicht weiß, dass keine aktiven Computer im Subnetz vorhanden sind und dass das Subnetz nur für die Notfallwiederherstellung erstellt wird.
- Es dürfen keine Konflikte zwischen den Subnetzen im Netzwerk und dem lokalen Netzwerk vorliegen, damit ein korrekten Netzwerkdatenverkehr aus einem Azure-Netzwerk gewährleistet wird.




## <a name="assigning-new-ip-addresses"></a>Zuweisen von neuen IP-Adressen

In diesem [Blogbeitrag](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) wird erläutert, wie die Azure-Netzwerkinfrastruktur festgelegt wird, wenn Sie IP-Adressen nach dem Failover nicht beibehalten müssen. Es wird mit der Beschreibung einer Anwendung begonnen, danach wird die Erstellung eines lokalen Netzwerks in Azure erklärt, und schließlich finden Sie Informationen zum Ausführen von Failoverszenarios.

## <a name="next-steps"></a>Nächste Schritte
[Ausführen eines Failovers](site-recovery-failover.md)
