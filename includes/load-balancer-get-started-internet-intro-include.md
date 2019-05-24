---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122100"
---
Ein Azure Load Balancer ist ein Layer-4-Load Balancer (TCP, UDP). Der Load Balancer sorgt für Hochverfügbarkeit, indem er eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einer Gruppe für den Lastenausgleich definiert wurden. Der Azure Load Balancer kann diese Dienste auch auf mehreren Ports, mehreren IP-Adressen oder beidem leisten.

Hierfür können Sie einen Load Balancer konfigurieren:

* einen Lastenausgleich des auf den virtuellen Computern (VMs) eingehenden Internetdatenverkehrs herzustellen. Wir bezeichnen einen Load Balancer in diesem Szenario als einen [Lastenausgleich mit Internetzugriff](../articles/load-balancer/load-balancer-internet-overview.md).
* Lastenausgleich für Datenverkehr zwischen VMs in einem virtuellen Netzwerk (VNet), zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen Computern und VMs in einem standortübergreifenden virtuellen Netzwerk. Wir bezeichnen einwn Load Balancer in diesem Szenario als einen [internen Load Balancer (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Weiterleiten von externem Datenverkehr an eine bestimmte VM-Instanz.
