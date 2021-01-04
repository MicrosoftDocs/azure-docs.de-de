---
title: Was sind Azure Firewall Manager-Sicherheitspartneranbieter?
description: Informieren Sie sich über Azure Firewall Manager-Sicherheitspartneranbieter.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490107"
---
# <a name="what-are-security-partner-providers"></a>Was sind Sicherheitspartneranbieter?

Mit *Sicherheitspartneranbietern* in Azure Firewall Manager können Sie Ihre vertrauten erstklassigen SECaaS-Angebote (Security-as-a-Service) von Drittanbietern verwenden, um den Internetzugriff für Ihre Benutzer zu schützen.

Mit einigen schnellen Konfigurationsschritten können Sie einen Hub mit einem unterstützten Sicherheitspartner schützen und den Internetdatenverkehr von Ihren virtuellen Netzwerken (VNETs) oder Filialstandorten innerhalb einer Region weiterleiten und filtern. Dazu können Sie die automatisierte Routenverwaltung verwenden, ohne benutzerdefinierte Routen (User Defined Routes, UDRs) einrichten und verwalten zu müssen.

Sie können geschützte, mithilfe eines Sicherheitspartners Ihrer Wahl konfigurierte Hubs in mehreren Azure-Regionen bereitstellen, um Konnektivität und Sicherheit für Benutzer in diesen Regionen rund um die Welt zu gewährleisten. Dank der Möglichkeit, das Angebot des Sicherheitspartners für den Internet-/SaaS-Anwendungsdatenverkehr und die Azure Firewall für privaten Datenverkehr in den geschützten Hubs zu nutzen, können Sie jetzt Ihre Sicherheitsperipherie in Azure aufbauen, die sich in der Nähe ihrer weltweit verteilten Benutzer und Anwendungen befindet.

Die unterstützten Sicherheitspartner sind **Zscaler**, **[Check Point](check-point-overview.md)** und **iboss**.

![Sicherheitspartneranbieter](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Wichtige Szenarien

Mithilfe der Sicherheitspartner können Sie den Internetdatenverkehr in folgenden Szenarien filtern:

- Virtuelles Netzwerk (VNET) zu Internet

   Nutzen Sie erweiterten, benutzerfähigen Internetschutz für Ihre in Azure ausgeführten Cloudworkloads.

- Filiale zu Internet

   Nutzen Sie die Azure-Konnektivität und die globale Verteilung, um auf einfache Weise eine NSaaS-Drittanbieterfilterung für Szenarien vom Typ „Filiale-zu-Internet“ hinzuzufügen. Sie können Ihr globales Übertragungsnetzwerk und die Sicherheitsperipherie mithilfe von Azure Virtual WAN erstellen.

Die folgenden Szenarien werden unterstützt:
- VNet/Filiale zu Internet über einen Sicherheitspartneranbieter und sonstiger Datenverkehr (Spoke zu Spoke, Spoke zu Filiale, Filiale zu Spoke) über Azure Firewall.
- VNet/Filiale zu Internet über Sicherheitspartneranbieter

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Best Practices für das Filtern von Internetdatenverkehr in geschützten virtuellen Hubs

Der Internetdatenverkehr umfasst in der Regel Webdatenverkehr. Er beinhaltet aber auch Datenverkehr, der an SaaS-Anwendungen wie Microsoft 365 und öffentliche Azure-PaaS-Dienste wie Azure Storage, Azure SQL usw. gerichtet ist. Im Folgenden finden Sie empfohlene Best Practices zur Behandlung des Datenverkehrs für diese Dienste:

### <a name="handling-azure-paas-traffic"></a>Behandeln von Azure-PaaS-Datenverkehr
 
- Schützen Sie sich mit der Azure Firewall, wenn der Datenverkehr meist aus Azure-PaaS-Datenverkehr besteht und der Ressourcenzugriff für Ihre Anwendungen mit IP-Adressen, FQDNs, Diensttags oder FQDN-Tags gefiltert werden kann.

- Verwenden Sie die Partnerlösung eines Drittanbieters in Ihren Hubs, wenn der Datenverkehr aus SaaS-Anwendungszugriffen besteht oder Sie (beispielsweise für Ihre VDI [Virtual Desktop Infrastructure]-Workloads) eine benutzerfähige Filterung oder erweiterte Filterfunktionen für das Internet benötigen.

![Alle Szenarien für Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Behandeln des Datenverkehrs von Microsoft 365

In global verteilten Filialszenarien sollten Sie Microsoft 365-Datenverkehr direkt am Filialstandort umleiten, bevor Sie den verbleibenden Internetdatenverkehr an den geschützten Azure-Hub senden.

Bei Microsoft 365 sind Netzwerklatenz und -leistung für eine positive Benutzererfahrung von entscheidender Bedeutung. Um diese optimale Leistung und Benutzerfreundlichkeit zu erzielen, müssen Kunden Microsoft 365 direkt und lokal implementieren, bevor sie es in Betracht ziehen, den übrigen Internetdatenverkehr über Azure weiterzuleiten.

Gemäß den [Microsoft 365-Prinzipien für Netzwerkkonnektivität](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) müssen wichtige Microsoft 365-Verbindungen lokal vom Filialstandort oder Mobilgerät des Benutzers direkt über das Internet an den nächstgelegenen Netzwerk-POP (Point-of-Presence) von Microsoft weitergeleitet werden.

Darüber hinaus werden Microsoft 365-Verbindungen aus Gründen des Datenschutzes verschlüsselt und nutzen aus Leistungsgründen effiziente, proprietäre Protokolle. Daher ist es weder praktisch noch wirkungsvoll, diese Verbindungen mit herkömmlichen Sicherheitslösungen auf Netzwerkebene zu schützen. Aus diesen Gründen wird dringend empfohlen, dass Kunden Microsoft 365-Datenverkehr direkt von Filialstandorten senden, bevor der übrige Datenverkehr über Azure übertragen wird. Microsoft verfügt über Partnerschaften mit mehreren Anbietern von SD-WAN-Lösungen, die in Azure und Microsoft 365 integriert sind und es Kunden einfach machen, das direkte und lokale Internetbreakout in Microsoft 365 zu aktivieren. Weitere Informationen finden Sie unter [Was ist Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des Angebots eines Sicherheitspartners in einem geschützten Hub unter Verwendung von Azure Firewall Manager](deploy-trusted-security-partner.md)
