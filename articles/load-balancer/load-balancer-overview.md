---
title: Was versteht man unter Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Übersicht über Features, Architektur und Implementierung des Azure Load Balancers. Hier erfahren Sie, wie Load Balancer funktioniert und wie Sie den Dienst in der Cloud verwenden.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 96c0242414c67d97fc324977b1259f63c27c3a26
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696811"
---
# <a name="what-is-azure-load-balancer"></a>Was versteht man unter Azure Load Balancer?

*Lastenausgleich* bezieht sich auf die gleichmäßige Verteilung von Last (oder eingehendem Netzwerkdatenverkehr) auf eine Gruppe von Back-End-Ressourcen oder Server. 

Azure Load Balancer setzt in der vierten Schicht des OSI-Modells (Open Systems Interconnection) an. Der Dienst ist der zentrale Kontaktpunkt für Clients. Load Balancer verteilt Datenflüsse, die beim Front-End des Lastenausgleichs eingehen, auf die Instanzen des Back-End-Pools. Diese Datenflüsse werden gemäß den konfigurierten Lastenausgleichsregeln und Integritätstests behandelt. Bei den Back-End-Poolinstanzen kann es sich um virtuelle Azure-Computer oder um Instanzen in einer VM-Skalierungsgruppe handeln.

Ein **[öffentlicher Lastenausgleich](./components.md#frontend-ip-configurations)** kann ausgehende Verbindungen für virtuelle Computer in Ihrem virtuellen Netzwerk bereitstellen. Diese Verbindungen werden durch die Übersetzung der privaten IP-Adressen in öffentliche IP-Adressen hergestellt. Öffentliche Load Balancer-Instanzen werden verwendet, um einen Lastausgleich für den eingehenden Internetdatenverkehr Ihrer virtuellen Computer vorzunehmen.

Ein **[interner (oder privater) Lastenausgleich](./components.md#frontend-ip-configurations)** wird verwendet, wenn private IP-Adressen nur am Front-End benötigt werden. Interne Lastenausgleichsmodule werden verwendet, um einen Lastausgleich für Datenverkehr innerhalb eines virtuellen Netzwerks vorzunehmen. Auf ein Lastenausgleichs-Front-End kann auch über ein lokales Netzwerk in einem Hybridszenario zugegriffen werden.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Abbildung: Durchführen eines Lastenausgleichs für Anwendungen mit mehreren Ebenen mithilfe eines öffentlichen und eines internen Load Balancers*

Weitere Informationen zu den einzelnen Lastenausgleichskomponenten finden Sie unter [Azure Load Balancer-Komponenten](./components.md).

## <a name="why-use-azure-load-balancer"></a>Gründe für die Verwendung von Azure Load Balancer
Mit Load Balancer Standard können Sie Ihre Anwendungen skalieren und hochverfügbare Dienste erstellen. Der Lastenausgleich unterstützt Szenarien mit eingehenden und ausgehenden Verbindungen. Der Lastenausgleich sorgt für eine niedrige Wartezeit und einen hohen Durchsatz und kann eine zentrale Skalierung auf Millionen von Datenflüssen für alle TCP- und UDP-Anwendungen durchführen.

Im Anschluss finden Sie einige Schlüsselszenarien für Load Balancer Standard:

- Vornehmen eines Lastausgleichs für **[internen](./quickstart-load-balancer-standard-internal-portal.md)** und **[externen](./tutorial-load-balancer-standard-manage-portal.md)** Datenverkehr auf virtuellen Azure-Computern

- Erhöhen der Verfügbarkeit durch Verteilung von Ressourcen **[innerhalb](./tutorial-load-balancer-standard-public-zonal-portal.md)** von und **[zwischen](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** Zonen

- Konfigurieren der **[ausgehenden Konnektivität](./load-balancer-outbound-connections.md)** für virtuelle Azure-Computer

- Verwenden von **[Integritätstests](./load-balancer-custom-probe-overview.md)** zur Überwachung von Ressourcen mit Lastenausgleich

- Verwenden der **[Portweiterleitung](./tutorial-load-balancer-port-forwarding-portal.md)** zum Zugreifen auf virtuelle Computer in einem virtuellen Netzwerk basierend auf der öffentlichen IP-Adresse und dem Port

- Aktivieren der Unterstützung für den **[Lastenausgleich](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** für **[IPv6](../virtual-network/ipv6-overview.md)**

- Load Balancer Standard stellt mehrdimensionale Metriken über [Azure Monitor](../azure-monitor/overview.md) bereit.  Diese Metriken können für eine bestimmte Dimension gefiltert, gruppiert und unterteilt werden.  Sie liefern aktuelle und vergangenheitsbezogene Einblicke in die Leistung und Integrität Ihres Diensts.  Resource Health (Ressourcenintegrität) wird ebenfalls unterstützt. Ausführlichere Informationen finden Sie unter **[Load Balancer Standard-Diagnose mit Metriken, Warnungen und Ressourcenintegrität](load-balancer-standard-diagnostics.md)** .

- Vornehmen eines Lastausgleichs für Dienste an **[mehreren Ports, mehreren IP-Adressen oder beidem](./load-balancer-multivip-overview.md)**

- Verschieben der Ressourcen von **[internen](./move-across-regions-internal-load-balancer-portal.md)** und **[externen](./move-across-regions-external-load-balancer-portal.md)** Lastenausgleichsmodulen zwischen Azure-Regionen

- Vornehmen eines Lastausgleichs für TCP- und UDP-Datenflüsse an allen Ports gleichzeitig mithilfe von **[Hochverfügbarkeitsports](./load-balancer-ha-ports-overview.md)**

### <a name="secure-by-default"></a><a name="securebydefault"></a>Standardmäßig sicher

Load Balancer Standard basiert auf dem Zero Trust-Netzwerksicherheitsmodell. Load Balancer Standard ist standardmäßig sicher und Bestandteil Ihres virtuellen Netzwerks. Das virtuelle Netzwerk ist ein privates und isoliertes Netzwerk.  Das bedeutet, dass Load Balancer Standard-Instanzen sowie öffentliche Standard-IP-Adressen für eingehende Datenflüsse geschlossen sind, sofern sie nicht durch Netzwerksicherheitsgruppen geöffnet werden. NSGs werden verwendet, um zulässigen Datenverkehr explizit zuzulassen.  Wenn Sie über keine NSG für ein Subnetz oder für eine NIC Ihrer VM-Ressource verfügen, ist diese Ressource für Datenverkehr nicht erreichbar. Weitere Informationen zu NSGs und ihrer Verwendung in Ihrem Szenario finden Sie unter [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).
Load Balancer Basic ist standardmäßig für das Internet geöffnet. Außerdem werden von Load Balancer keine Kundendaten gespeichert.

## <a name="pricing-and-sla"></a>Preise und SLA

Informationen zu den Preisen für Load Balancer Standard finden Sie unter [Load Balancer – Preise](https://azure.microsoft.com/pricing/details/load-balancer/).
Basic Load Balancer wird kostenlos angeboten.
Weitere Informationen finden Sie unter [SLA für Load Balancer](https://aka.ms/lbsla). Für Load Balancer Basic steht keine SLA zur Verfügung.

## <a name="whats-new"></a>Neuigkeiten

Abonnieren Sie den RSS-Feed, und zeigen Sie die neuesten Azure Load Balancer-Featureupdates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) an.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit einem Lastenausgleich finden Sie unter [Erstellen eines öffentlichen Standardlastenausgleichs](quickstart-load-balancer-standard-public-portal.md).

Weitere Informationen zu den Einschränkungen und Komponenten von Azure Load Balancer finden Sie unter [Azure Load Balancer-Komponenten](./components.md) und [Azure Load Balancer-Konzepte](./concepts.md).