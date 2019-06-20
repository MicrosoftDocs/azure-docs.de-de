---
title: Übersicht über IPv6 für Azure Virtual Network (Vorschau)
titlesuffix: Azure Virtual Network
description: IPv6-Beschreibung der IPv6-Endpunkte und -Datenpfade in einem virtuellen Azure-Netzwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131073"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Was ist IPv6 für Azure Virtual Network? (Vorschau)

IPv6 für Azure Virtual Network (VNET) ermöglicht es Ihnen, Anwendungen in Azure mit IPv6- und IPv4-Konnektivität sowohl innerhalb eines virtuellen Netzwerks als auch aus dem und in das Internet bereitzustellen. Aufgrund der Erschöpfung öffentlicher IPv4-Adressen werden neue Netzwerke für Mobilität und Internet der Dinge (IoT) häufig auf IPv6 aufgebaut. Selbst langjährig etablierte ISP- und Mobilnetzwerke werden auf IPv6 umgestellt. Reine IPv4-Dienste können sowohl in vorhandenen als auch in entstehenden Märkten einen echten Nachteil darstellen. Die Dual Stack-IPv4/IPv6-Konnektivität ermöglicht es in Azure gehosteten Diensten, diese Technologielücke mit global verfügbaren Dual Stack-Diensten zu schließen, die problemlos eine Verbindung sowohl mit den vorhandenen IPv4- als auch mit diesen neuen IPv6-Geräten und -Netzwerken herstellen können.

Die ursprüngliche IPv6-Konnektivität von Azure macht es einfach, eine Dual Stack-Internetverbindung (IPv4/IPv6) für Anwendungen bereitzustellen, die in Azure gehostet werden. Sie ermöglicht eine einfache Bereitstellung von VMs mit IPv6-Konnektivität mit Lastenausgleich für eingehende und ausgehende initiierte Verbindungen. Dieses Feature ist noch verfügbar. Weitere Informationen finden Sie [hier](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 für das virtuelle Azure-Netzwerk bietet viel umfangreichere Funktionen, sodass vollständige IPv6-Lösungsarchitekturen in Azure bereitgestellt werden können.

> [!Important]
> IPv6 für Azure Virtual Network ist derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die folgende Abbildung zeigt eine einfache Dual-Stack-Bereitstellung (IPv4/IPv6) in Azure:

![Abbildung: IPv6-Netzwerkbereitstellung](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vorteile

Vorteile von Azure Virtual Network IPv6:

- Hilft dabei, die Reichweite Ihrer in Azure gehosteten Anwendungen auf die wachsenden Märkte für Mobilgeräte und das Internet der Dinge auszuweiten.
- Dual Stack-IPv4/IPv6-VMs stellen maximale Flexibilität bei der Dienstbereitstellung zur Verfügung. Eine einzige Dienstinstanz kann sowohl mit IPv4- als auch IPv6-fähigen Internetclients eine Verbindung herstellen.
- Baut auf der bereits lange etablierten IPv6-Konnektivität zwischen Azure-VMs und dem Internet auf.
- Standardmäßig sicher, da die IPv6-Verbindung mit dem Internet nur dann hergestellt wird, wenn Sie sie in Ihrer Bereitstellung explizit anfordern.

## <a name="capabilities"></a>Funktionen

IPv6-Unterstützung für virtuelle Computer umfasst die folgenden Funktionen:

- Azure-Kunden können ihren eigenen virtuellen IPv6-Netzwerkadressraum definieren, um den Anforderungen ihrer Anwendungen und Kunden gerecht zu werden, oder eine nahtlose Integration in ihren lokalen IP-Bereich vornehmen.
- Virtuelle Dual Stack-Netzwerke (IPv4 und IPv6) mit Dual Stack-Subnetzen ermöglichen es Anwendungen, eine Verbindung mit IPv4- und IPv6-Ressourcen in ihrem virtuellen Netzwerk oder im Internet herzustellen.
- Schützen von Ressourcen mit IPv6-Regeln für Netzwerksicherheitsgruppen
- Passen Sie das Routing von IPv6-Datenverkehr in Ihrem virtuellen Netzwerk mit benutzerdefinierten Routen an, insbesondere wenn Sie Network Virtual Appliances zur Erweiterung Ihrer Anwendung nutzen.
- IPv6-Load Balancer-Unterstützung zur Erstellung robuster, skalierbarer Anwendungen, einschließlich Azure DNS-Unterstützung für AAAA-Adresseinträge für öffentliche IPv6-IP-Adressen.
- Mit Inplace-Upgrades können Sie die IPv6-Konnektivität problemlos zu vorhandenen IPv4-basierten Implementierungen hinzufügen.
- Erstellen Sie Dual Stack-Anwendungen, die automatisch mit VM-Skalierungsgruppen für Ihre Last skaliert werden.

## <a name="limitations"></a>Einschränkungen
Die Vorschauversion von IPv6 für das virtuelle Azure-Netzwerk weist die folgenden Einschränkungen auf:
- IPv6 für Azure Virtual Network (Vorschau) ist in allen globalen Azure-Regionen verfügbar, aber nur in globalem Azure, nicht in den Government Clouds.   
- Die Portalunterstützung für die Vorschau ist für einen Großteil der IPv6-Konfiguration (aber nicht für die gesamte Konfiguration) schreibgeschützt, jedoch bietet IPv6 für Virtual Network vollständige Unterstützung und Dokumentation (mit Beispielen) für die IPv6-Bereitstellung mit Azure PowerShell und der Befehlszeilenschnittstelle (CLI).
- Die Unterstützung von Network Watcher für die Vorschau ist auf NSG-Flussprotokolle und Netzwerkpaketaufzeichnungen beschränkt.
- Die Unterstützung von Lastenausgleich für die Vorschau ist zunächst auf den Load Balancer im Tarif „Basic“ beschränkt.
- Öffentliche IP-Adressen auf Instanzebene (öffentliche IP-Adressen direkt auf einer VM) werden in der Vorschau nicht unterstützt.  
- Peering virtueller Netzwerke wird (regional und global) in der Vorschau nicht unterstützt. 

## <a name="pricing"></a>Preise

Azure-IPv6-Ressourcen und -Bandbreite werden zu den gleichen Tarifen wie IPv4 berechnet. Es gibt keine zusätzlichen oder abweichenden Gebühren für IPv6. Sie können Details zu den Preisen für [öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [Netzwerkbandbreite](https://azure.microsoft.com/pricing/details/bandwidth/) oder [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) anzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [eine IPv6-Dual Stack-Anwendung mithilfe von Azure PowerShell bereitstellen](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Erfahren Sie, wie Sie [eine IPv6-Dual Stack-Anwendung mithilfe der Azure CLI bereitstellen](virtual-network-ipv4-ipv6-dual-stack-cli.md).
