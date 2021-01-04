---
title: Azure Load Balancer-SKUs
description: Übersicht über Azure Load Balancer-SKUs
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698086"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer-SKUs

Azure Load Balancer verfügt über zwei SKUs.

## <a name="sku-comparison"></a><a name="skus"></a> SKU-Vergleich

Der Lastenausgleich unterstützt sowohl Standard- als auch Basic-SKUs. Diese SKUs unterscheiden sich in Bezug auf Skalierung, Features und Preise. Jedes Szenario, das mit dem Lastenausgleich im Tarif „Basic“ möglich ist, kann auch mit dem Lastenausgleich im Tarif „Standard“ erstellt werden.

Weitere Informationen zu den Unterschieden finden Sie in der folgenden Tabelle. Weitere Informationen finden Sie unter [Übersicht: Azure Load Balancer Standard](./load-balancer-overview.md).

>[!NOTE]
> Microsoft empfiehlt den Lastenausgleich im Tarif „Standard“.
Eigenständige virtuelle Computer, Verfügbarkeitsgruppen und VM-Skalierungsgruppen können nur mit einer SKU, nie mit beiden verbunden werden. Die SKU für den Lastenausgleich muss mit der SKU für öffentliche IP-Adressen übereinstimmen, wenn Sie sie mit öffentlichen IP-Adressen verwenden. SKUs für den Lastenausgleich und SKUs für öffentliche IP-Adressen sind nicht änderbar.

| | Load Balancer Standard | Load Balancer Basic |
| --- | --- | --- |
| **[Größe des Back-End-Pools](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Unterstützt bis zu 1.000 Instanzen | Unterstützt bis zu 300 Instanzen |
| **Endpunkte des Back-End-Pools** | Virtuelle Computer oder VM-Skalierungsgruppen in einem einzelnen Netzwerk | Virtuelle Computer in einer einzelnen Verfügbarkeitsgruppe oder VM-Skalierungsgruppe |
| **[Integritätstests](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Verhalten bei Ausfall während Integritätstest](./load-balancer-custom-probe-overview.md#probedown)** | TCP-Verbindungen bleiben bei Ausfall eines Instanztests __und__ bei Ausfall aller Tests bestehen. | TCP-Verbindungen bleiben bei Ausfall eines Instanztests bestehen. Sämtliche TCP-Verbindungen werden bei Ausfällen aller Tests beendet. |
| **Verfügbarkeitszonen** | Zonenredundante und zonale Front-Ends für eingehenden und ausgehenden Datenverkehr | Nicht verfügbar |
| **Diagnose** | [Mehrdimensionale Azure Monitor-Metriken](./load-balancer-standard-diagnostics.md) | [Azure Monitor-Protokolle](./load-balancer-monitor-log.md) |
| **Hochverfügbarkeitsports** | [Verfügbar für den internen Lastenausgleich](./load-balancer-ha-ports-overview.md) | Nicht verfügbar |
| **Standardmäßig sicher** | Für eingehende Datenflüsse geschlossen, sofern nicht durch eine Netzwerksicherheitsgruppe zugelassen. Interner Datenverkehr vom VNET zum internen Lastenausgleich ist zulässig. | Standardmäßig geöffnet. Netzwerksicherheitsgruppe optional. |
| **Regeln für ausgehenden Datenverkehr** | [Deklarative ausgehende NAT-Konfiguration](./load-balancer-outbound-connections.md#outboundrules) | Nicht verfügbar |
| **TCP-Zurücksetzung bei Leerlauf** | [Verfügbar für jede Regel](./load-balancer-tcp-reset.md) | Nicht verfügbar |
| **[Mehrere Front-Ends](./load-balancer-multivip-overview.md)** | Eingehend und [ausgehend](./load-balancer-outbound-connections.md) | Nur eingehend |
| **Verwaltungsvorgänge** | Die meisten Vorgänge < 30 Sekunden | Meist 60 bis 90+ Sekunden |
| **SLA** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Nicht verfügbar | 

Weitere Informationen finden Sie unter [Load Balancer-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Lesen Sie für den Standard-Load Balancer auch die ausführlicheren Informationen unter [Übersicht](./load-balancer-overview.md), [Preise](https://aka.ms/lbpricing) und [SLA](https://aka.ms/lbsla).

## <a name="limitations"></a>Einschränkungen

- SKUs sind nicht veränderlich. Sie können die SKU einer vorhandenen Ressource nicht ändern.
- Eine Ressource eines eigenständigen virtuellen Computers, eine Verfügbarkeitsgruppenressource oder eine Ressource einer VM-Skalierungsgruppe kann auf eine SKU verweisen, nie auf beide.
- [Verschiebevorgänge:](../azure-resource-manager/management/move-resource-group-and-subscription.md)
  - Vorgänge zum Verschieben von Ressourcengruppen (innerhalb desselben Abonnements) werden für Load Balancer Standard und für öffentliche IP-Standardadressen **unterstützt**. 
  - [Vorgänge zum Verschieben von Abonnementgruppen](../azure-resource-manager/management/move-support-resources.md) werden für Load Balancer Standard und für öffentliche IP-Standardadressen **nicht** unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten mit einer Load Balancer-Instanz finden Sie unter [Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer im Azure-Portal](quickstart-load-balancer-standard-public-portal.md).
- Informationen zu [Standard Load Balancer und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md)
- Informationen zu [Integritätstests](load-balancer-custom-probe-overview.md)
- Informationen zur Verwendung von [Load Balancer für ausgehende Verbindungen](load-balancer-outbound-connections.md)
- Informationen zu [Load Balancer Standard mit Lastenausgleichsregeln für HA-Ports](load-balancer-ha-ports-overview.md)
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).