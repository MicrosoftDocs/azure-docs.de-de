---
title: Übersicht über IPv6 für Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie IPv6-Unterstützung für Azure Load Balancer und VMs mit Lastenausgleich bereitstellen.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, Azure Load Balancer, dualer Stapel, öffentliche IP, natives IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861945"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Übersicht über IPv6 für Azure Load Balancer


>[!NOTE] 
>Azure Load Balancer unterstützt zwei unterschiedliche Typen: Basic und Standard. In diesem Artikel wird der Load Balancer vom Typ „Basic“ beschrieben. Weitere Informationen zum Load Balancer vom Typ „Standard“ finden Sie unter [Übersicht: Azure Load Balancer Standard (Preview)](load-balancer-standard-overview.md).

Load Balancer mit Internetzugriff können mit einer IPv6-Adresse bereitgestellt werden. Zusätzlich zu IPv4-Konnektivität werden dadurch die folgenden Funktionen ermöglicht:

* Native End-to-End-IPv6-Konnektivität zwischen öffentlichen Internetclients und Azure Virtual Machines (VMs) über den Load Balancer.
* Native ausgehende End-to-End-IPv6-Konnektivität zwischen VMs und öffentlichen IPv6-fähigen Internetclients.

Die folgende Abbildung zeigt die IPv6-Funktionalität für Azure Load Balancer.

![Azure Load Balancer mit IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Nach der Bereitstellung kann ein IPv4- oder IPv6-fähiger Internetclient mit den öffentlichen IPv4- oder IPv6-Adressen (oder Hostnamen) des Azure Load Balancers mit Internetzugriff kommunizieren. Der Load Balancer leitet mithilfe der Netzwerkadressübersetzung (NAT) die IPv6-Pakete an private IPv6-Adressen von VMs weiter. Der IPv6-Internetclient kann nicht direkt mit der IPv6-Adresse der VMs kommunizieren.

## <a name="features"></a>Features

Native IPv6-Unterstützung für über Azure Resource Manager bereitgestellte VMs ermöglicht:

1. IPv6-Dienste mit Lastenausgleich für IPv6-Clients im Internet
2. Native IPv6- und IPv4-Endpunkte auf VMs („dualer Stapel“)
3. Native IPv6-Verbindungen, die durch eingehenden und ausgehenden Datenverkehr initiiert werden
4. Unterstützte Protokolle wie TCP, UDP und HTTP(S) ermöglichen eine Vielzahl von Dienstarchitekturen

## <a name="benefits"></a>Vorteile

Diese Funktionalität ermöglicht folgende wichtige Vorteile:

* Erfüllen von gesetzlichen Vorschriften, die es erfordern, dass auf neue Anwendungen mit reinen IPv6-Clients zugegriffen werden kann
* Ermöglichen, dass mobile Entwickler und Entwickler für das Internet der Dinge (IOT) Azure Virtual Machines mit dualem Stapel (IPv4 und IPv6) verwenden können, um die wachsende Märkte Mobilität und IOT zu bedienen

## <a name="details-and-limitations"></a>Details und Einschränkungen

Details

* Der Azure DNS-Dienst enthält IPv4-A- und IPv6-AAAA-Namenseinträge und antwortet für den Load Balancer mit beiden Einträgen. Der Client wählt die Adresse (IPv4 oder IPv6) für die Kommunikation aus.
* Wenn eine VM eine Verbindung mit einem öffentlichen, mit dem Internet verbundenen IPv6-Gerät initiiert, wird die IPv6-Quelladresse der VM mithilfe der Netzwerkadressübersetzung (NAT) in die öffentliche IPv6-Adresse des Load Balancers übersetzt.
* VMs mit dem Linux-Betriebssystems müssen so konfiguriert werden, dass sie eine IPv6-IP-Adresse über DHCP erhalten. Viele der Linux-Images im Azure-Katalog sind bereits so konfiguriert, dass sie IPv6 unverändert unterstützen. Weitere Informationen finden Sie unter [Konfigurieren von DHCPv6 für virtuelle Linux-Computer](load-balancer-ipv6-for-linux.md)
* Wenn Sie einen Integritätstest mit dem Load Balancer verwenden möchten, erstellen Sie einen IPv4-Test und verwenden ihn für IPv4- und IPv6-Endpunkte. Wenn der Dienst auf Ihrer VM ausfällt, werden sowohl IPv4- als auch IPv6-Endpunkte aus der Rotation entfernt.

Einschränkungen

* Sie können im Azure-Portal keine IPv6-Lastenausgleichsregeln hinzufügen. Die Regeln können nur mithilfe der Vorlage, der CLI und PowerShell erstellt werden.
* Sie können vorhandene VMs nicht für die Verwendung von IPv6-Adressen aktualisieren. Sie müssen neue VMs bereitstellen.
* Eine einzelne IPv6-Adresse kann einer einzelnen Netzwerkschnittstelle in jeder VM zugewiesen werden.
* Die öffentlichen IPv6-Adressen können keiner VM zugewiesen werden. Sie können nur einem Load Balancer zugewiesen werden.
* Sie können Reverse-DNS-Lookup nicht für Ihre öffentlichen IPv6-Adressen konfigurieren.
* Die VMs mit den IPv6-Adressen können keinem Azure-Clouddienst angehören. Sie können mit einem virtuellen Azure-Netzwerk (VNet) verbunden sein und miteinander über die IPv4-Adressen kommunizieren.
* Private IPv6-Adressen können auf einzelnen VMs in einer Ressourcengruppe bereitgestellt werden, jedoch können sie nicht über Skalierungsgruppen in einer Ressourcengruppe bereitgestellt werden.
* Azure-VMs können nicht über IPv6 Verbindungen mit anderen VMs, anderen Azure-Diensten oder lokalen Geräten herstellen. Sie können über IPv6 nur mit Azure Load Balancer kommunizieren. Allerdings können sie mit diesen anderen Ressourcen über IPv4 kommunizieren.
* Schutz durch Netzwerksicherheitsgruppen (NSG) für IPv4 wird in Bereitstellungen mit dualem Stapel (IPv4 und IPv6) unterstützt. NSGs gelten nicht für die IPv6-Endpunkte.
* Auf den IPv6-Endpunkt auf der VM kann nicht direkt über das Internet zugegriffen werden. Er befindet sich hinter einem Load Balancer. Nur auf die Ports, die in den Load Balancer-Regeln angegeben sind, kann über IPv6 zugegriffen werden.
* Das Ändern des Parameters IdleTimeout wird für IPv6 **derzeit nicht unterstützt**. Der Standardwert ist vier Minuten.
* Das Ändern des Parameters loadDistributionMethod wird für IPv6 **derzeit nicht unterstützt**.
* Reservierte IPv6-IP-Adressen (wobei IPAllocationMethod = static) werden **derzeit nicht unterstützt**.
* NAT64 (Übersetzung von IPv6 in IPv4) wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie einen Load Balancer mit IPv6 bereitstellen.

* [Verfügbarkeit von IPv6 nach Region](https://go.microsoft.com/fwlink/?linkid=828357)
* [Bereitstellen eines Load Balancers mit IPv6 mithilfe einer Vorlage](load-balancer-ipv6-internet-template.md)
* [Bereitstellen eines Load Balancers mit IPv6 mithilfe von Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Bereitstellen eines Load Balancers mit IPv6 mithilfe der Azure-CLI](load-balancer-ipv6-internet-cli.md)
