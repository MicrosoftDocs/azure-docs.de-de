---
title: Angriffstypen, die mit Azure DDoS Protection Standard abgewehrt werden
description: Erfahren Sie, vor welchen Angriffstypen Azure DDoS Protection Standard schützt.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991773"
---
# <a name="types-of-ddos-attacks-overview"></a>Übersicht über Typen von DDoS-Angriffen

Die folgenden Arten von Angriffen können mit DDoS Protection Standard abgewehrt werden:

- **Volumetrische Angriffe**: Diese Angriffe überfluten die Netzwerkebene mit einer beträchtlichen Menge scheinbar berechtigten Datenverkehrs. Dazu zählen UDP-Überflutungen, Verstärkungsüberflutungen und andere Überflutungen mit gefälschten Paketen. DDoS Protection Standard wehrt diese bis zu mehreren Gigabytes großen Angriffe ab, indem sie mithilfe des weltweiten Netzwerks von Azure automatisch absorbiert und bereinigt werden.
- **Protokollangriffe**: Diese Angriffe machen den Zugriff auf ein Ziel unmöglich, indem sie eine Schwachstelle in den Schichten 3 und 4 des Protokollstapels ausnutzen. Dazu gehören SYN-Flutangriffe, Reflexionsangriffe und andere Protokollangriffe. DDoS Protection Standard wehrt diese Angriffe ab und unterscheidet dabei zwischen schädlichem und berechtigtem Datenverkehr. Nach Interaktion mit dem Client wird der schädliche Datenverkehr gesperrt. 
- **Angriffe in der Ressourcenschicht (Anwendungsschicht)** : Das Ziel dieser Art von Angriffen sind Webanwendungspakete, um die Datenübertragung zwischen Hosts zu unterbrechen. Dazu zählen Verletzungen des HTTP-Protokolls, die Einschleusung von SQL-Befehlen, XSS-Angriffe (Cross-Site Scripting) und andere Angriffe auf Ebene 7. Verwenden Sie eine Web Application Firewall wie die [Web Application Firewall von Azure Application Gateway](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Kombination mit DDoS Protection Standard zum Schutz vor diesen Angriffen. Im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) finden Sie auch Webanwendungsfirewall-Angebote von Drittanbietern.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

Mit DDoS Protection Standard werden Ressourcen in einem virtuellen Netzwerk geschützt, einschließlich öffentlicher IP-Adressen, die virtuellen Computern zugeordnet sind, interner Lastenausgleichsmodule und Anwendungsgateways. In Kombination mit der Web Application Firewall von Application Gateway oder einer Web Application Firewall eines Drittanbieters, die mit einer öffentlichen IP-Adresse in einem virtuellen Netzwerk bereitgestellt wird, stellt DDoS Protection Standard eine vollständige Abwehrfunktion für Ebene 3 bis Ebene 7 bereit.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen DDoS-Schutzplan erstellen](manage-ddos-protection.md).