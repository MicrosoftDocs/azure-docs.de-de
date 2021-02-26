---
title: Präfix für öffentliche IPv6-Adressen in virtuellen Azure-Netzwerken
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr über das Präfix für öffentliche IPv6-Adressen in virtuellen Azure-Netzwerken.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538205"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Reserviertes Präfix für öffentliche IPv6-Adressen

In Azure sind virtuelle Dual-Stack-Netzwerke (IPv4 und IPv6) sowie virtuelle Computer (VMs) standardmäßig sicher, da sie keine Internetkonnektivität haben. Mit öffentlichen IPv6-Adressen, die Sie von Azure erhalten, können Sie Ihren Azure Load Balancer-Instanzen und VMs problemlos IPv6-Internetkonnektivität hinzufügen.

Alle öffentlichen IP-Adressen, die Sie reservieren, sind einer Azure-Region Ihrer Wahl und Ihrem Azure-Abonnement zugeordnet. Sie können eine reservierte (statische) öffentliche IPv6-Adresse zwischen den Azure Load Balancer-Instanzen oder VMs in Ihrem Abonnement verschieben. Sie können die Zuordnung der öffentlichen IPv6-Adresse vollständig aufheben, und sie wird für Sie aufbewahrt, bis Sie zur Verwendung bereit sind.

> [!WARNING]
> Achten Sie darauf, dass Sie Ihre öffentlichen IP-Adressen nicht versehentlich löschen. Durch das Löschen einer öffentlichen IP-Adresse wird sie aus Ihrem Abonnement entfernt, und Sie können sie nicht wiederherstellen (auch nicht mithilfe des Azure-Supports).

Zusätzlich zum Reservieren einzelner IPv6-Adressen können Sie zusammenhängende Bereiche von Azure-IPv6-Adressen (als IP-Präfix bezeichnet) zur Verwendung reservieren.  Ähnlich wie einzelne IP-Adressen sind auch reservierte Präfixe einer Azure-Region Ihrer Wahl und Ihrem Azure-Abonnement zugeordnet. Das Reservieren eines vorhersagbaren, zusammenhängenden Bereichs von Adressen bietet viele Verwendungsmöglichkeiten. Beispielsweise können Sie die IP-*Filterung* Ihrer in Azure gehosteten Anwendungen für Ihr Unternehmen und Ihre Kunden erheblich vereinfachen, da Ihre statischen IP-Bereiche problemlos in lokale Firewalls programmiert werden können.  Sie können nach Bedarf einzelne öffentliche IP-Adressen aus Ihrem IP-Präfix erstellen, und wenn Sie diese einzelnen öffentlichen IP-Adressen löschen, werden sie in den reservierten Bereich *zurückgegeben*, sodass Sie sie später wieder verwenden können. Alle IP-Adressen in Ihrem IP-Präfix sind für Ihre ausschließliche Nutzung reserviert, bis Sie Ihr Präfix löschen.



## <a name="ipv6-prefix-sizes"></a>IPv6-Präfixgrößen
Die folgenden Präfixgrößen für öffentliche IP-Adressen sind verfügbar:

-  Minimale IPv6-Präfixgröße: /127 = 2 Adressen
-  Maximale IPv6-Präfixgröße: /124 = 16 Adressen

Die Präfixgröße wird als CIDR-Maskengröße (Classless Inter-Domain Routing) angegeben. So stellt beispielsweise eine Maske von /128 eine einzelne IPv6-Adresse dar, da IPv6-Adressen aus 128 Bits bestehen.

## <a name="pricing"></a>Preise
 
Informationen zu den Kosten im Zusammenhang mit der Nutzung von öffentlichen Azure-IP-Adressen – sowohl einzelnen IP-Adressen als auch IP-Bereichen – finden Sie unter [Preise für öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Einschränkungen
IPv6 wird bei öffentlichen IP-Adressen des Typs „Basic“ nur mit dynamischer Zuordnung unterstützt. Das bedeutet, dass sich die IPv6-Adresse ändert, wenn Sie Ihre Anwendung (VMs oder Load Balancer-Instanzen) in Azure löschen und erneut bereitstellen. Öffentliche IPv6-IP-Adressen des Typs „Standard“ unterstützen lediglich die statische (reservierte) Zuordnung, obwohl interne Load Balancer Standard-Instanzen auch die dynamische Zuordnung innerhalb des Subnetzes unterstützen können, dem sie zugewiesen sind.  

Als bewährte Vorgehensweise wird empfohlen, dass Sie öffentliche IP-Adressen und Load Balancer-Instanzen des Typs „Standard“ für Ihre IPv6-Anwendungen verwenden.

## <a name="next-steps"></a>Nächste Schritte
- Reservieren Sie ein [Präfix für öffentliche IPv6-Adressen](ipv6-reserve-public-ip-address-prefix.md).
- Erfahren Sie mehr über [IPv6-Adressen](ipv6-overview.md).
- Erfahren Sie, [wie Sie öffentliche IP-Adressen (IPv4 und IPv6) in Azure erstellen und verwenden](virtual-network-public-ip-address.md).
