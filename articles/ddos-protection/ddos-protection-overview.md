---
title: Übersicht über Azure DDoS Protection Standard
description: Erfahren Sie, wie der Azure DDoS Protection-Standard in Verbindung mit bewährten Anwendungsentwurfsmethoden einen Schutz vor DDoS-Angriffen darstellt.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c3931cafb6c755c6119bf8deb4ecc6b58ea7bc51
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938442"
---
# <a name="azure-ddos-protection-standard-overview"></a>Übersicht über Azure DDoS Protection Standard

DDoS-Angriffe (Distributed Denial of Service) stellen eines der größten Verfügbarkeits- und Sicherheitsprobleme für Kunden dar, die ihre Anwendungen in die Cloud verschieben. Ein DDoS-Angriff hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit sie für berechtigte Benutzer nicht mehr verfügbar ist. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden.

Alle Eigenschaften in Azure sind ohne zusätzliche Kosten durch DDoS Protection Basic der Infrastruktur von Azure geschützt. Die Skalierung und Kapazität des global bereitgestellten Azure-Netzwerks bietet Schutz vor häufigen Vermittlungsschichtangriffen durch Always On-Datenverkehrsüberwachung und Risikominderung in Echtzeit. DDoS Protection Basic erfordert keine Konfiguration oder Anwendungsänderungen durch den Benutzer. Alle Azure-Dienste einschließlich der PaaS-Dienste wie Azure DNS werden von DDoS Protection Basic geschützt.

Azure DDoS Protection Standard, kombiniert mit bewährten Methoden des Anwendungsentwurfs, bietet erweiterte Features zur DDoS-Risikominderung, um vor DDoS-Angriffen zu schützen. Er wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen in einem virtuellen Netzwerk zu schützen. Der Schutz kann einfach in jedem neuen oder vorhandenen virtuellen Netzwerk aktiviert werden und erfordert keine Änderung von Anwendung oder Ressource. Er hat mehrere Vorteile gegenüber dem Basisdienst, z.B. Protokollierung, Warnungen und Telemetrie. 

![Azure DDoS Protection-Dienstvergleich](./media/ddos-protection-overview/ddos-comparison.png)

In Azure DDoS Protection werden keine Kundendaten gespeichert.

## <a name="features"></a>Features

- **Native Plattformintegration:** Nativ in Azure integriert. Umfasst die Konfiguration über das Azure-Portal. DDoS Protection Standard erkennt Ihre Ressourcen und die Ressourcenkonfiguration.
- **Sofort einsatzbereiter Schutz:** Dank vereinfachter Konfiguration sind alle Ressourcen in einem virtuellen Netzwerk sofort geschützt, sobald DDoS Protection Standard aktiviert wird. Es sind weder Benutzereingriffe noch Benutzerdefinitionen erforderlich. DDoS Protection Standard wehrt einen Angriff sofort automatisch ab, sobald er entdeckt wurde.
- **Stets verfügbare Überwachung des Datenverkehrs:** Die Datenverkehrsmuster Ihrer Anwendungen werden 24 Stunden am Tag und 7 Tage die Woche auf Anzeichen für DDoS-Angriffe überwacht. Abwehrmaßnahmen werden bei Überschreitung der Schutzrichtlinien ausgeführt.
- **Adaptive Optimierung:** Dank einer intelligenten Profilerstellung lernt die Funktion den Datenverkehr Ihrer Anwendung kontinuierlich besser kennen. Auf dieser Basis wird das Profil ausgewählt und aktualisiert, das am besten zu Ihrem Dienst passt. Das Profil passt sich den Veränderungen des Datenverkehrs mit der Zeit an.
- **Mehrschichtiger Schutz**: Bietet umfassenden DDoS-Schutz in Kombination mit einer Web Application Firewall (WAF) zum Schutz sowohl auf Netzwerkebene (Ebene 3 und 4, angeboten von Azure DDoS Protection Standard) als auch auf Anwendungsebene (Schicht 7, angeboten von einer WAF). WAF-Angebote umfassen [Azure Application Gateway-WAF-SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sowie Web Application Firewall-Angebote von Drittanbietern, die in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) verfügbar sind.
- **Umfangreiche Angriffsabwehr:** Über 60 verschiedene Angriffstypen können mit einer weltweiten Kapazität zum Schutz vor den größten bekannten DDoS-Angriffen abgewehrt werden.
- **Angriffsanalysen**: Rufen Sie während eines Angriffs detaillierte Berichte in 5-Minuten-Inkrementen und nach dem Angriff eine vollständige Zusammenfassung ab. Streamen Sie Datenflussprotokolle der Entschärfung an [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) oder an ein Offline-SIEM-System (Security Information and Event Management), um einen Angriff annähernd in Echtzeit zu überwachen.
- **Angriffsmetriken:** Mit Azure Monitor kann auf eine Zusammenfassung der Metriken für jeden Angriff zugegriffen werden.
- **Angriffswarnungen:** Mit integrierten Angriffsmetriken können Warnungen am Anfang und Ende eines Angriffs sowie währenddessen konfiguriert werden. Warnungen werden in Ihre Betriebssoftware wie Microsoft Azure Monitor-Protokolle, Splunk, Azure Storage, E-Mail und das Azure-Portal integriert.
- **DDoS Rapid Response**: Wenden Sie sich an das DRR-Team (DDoS Protection Rapid Response), um Hilfe bei der Untersuchung und Analyse von Angriffen zu erhalten. Weitere Informationen finden Sie unter [DDoS Rapid Response](ddos-rapid-response.md).
- **Kostengarantie:** Dienstguthaben für Datenübertragung und horizontale Anwendungsskalierung bei dokumentierten DDoS-Angriffen.

## <a name="pricing"></a>Preise

Informationen zu den Preisen für Azure DDoS Protection Standard finden Sie unter [Azure DDoS Protection Standard: Preisübersicht](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines DDoS Protection-Plans](manage-ddos-protection.md)