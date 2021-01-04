---
title: Partnerschaften mit Azure DDoS Protection Standard
description: Informationen zu den Partnerschaftsmöglichkeiten, die sich durch Azure DDoS Protection Standard ergeben können.
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 4cc0fd970f9adc26f5fdb024f501dcb0edf77bde
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992419"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnerschaften mit Azure DDoS Protection Standard
In diesem Artikel werden die Partnerschaftsmöglichkeiten beschrieben, die sich durch Azure DDoS Protection Standard ergeben können. Dieser Artikel soll Produktmanagern und Geschäftsentwicklern helfen, die Investitionspfade zu verstehen, und einen Einblick in die Wertschöpfungsmöglichkeiten durch Partnerschaften geben.

## <a name="background"></a>Hintergrund
DDoS-Angriffe (Distributed Denial-of-Service) stellen eines der größten Verfügbarkeits- und Sicherheitsprobleme für Kunden dar, die ihre Anwendungen in die Cloud verschieben. Da Erpressung und Hacktivismus die häufigsten Beweggründe für DDoS-Angriffe sind, haben sie in Art, Umfang und Häufigkeit ihres Auftretens ständig zugenommen, da sie relativ einfach und schnell auszuführen sind.

Azure DDoS Protection bietet Gegenmaßnahmen gegen die ausgefeiltesten DDoS-Bedrohungen und nutzt dabei das globale Ausmaß von Azure-Netzwerken. Der Dienst bietet erweiterte DDoS-Risikominderungsfunktionen für Anwendungen und Ressourcen, die in virtuellen Netzwerken bereitgestellt werden.

Technologiepartner können die Ressourcen ihrer Kunden mit Azure DDoS Protection Standard nativ schützen, um den Problemen der Verfügbarkeit und Zuverlässigkeit aufgrund von DDoS-Angriffen zu begegnen.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Einführung in Azure DDoS Protection Standard
Azure DDoS Protection Standard bietet erweiterte DDoS-Risikominderungsfunktionen für DDoS-Angriffe der Ebenen 3 und 4. Der Azure DDoS Protection Standard-Dienst weist die folgenden Hauptfeatures auf.

### <a name="adaptive-real-time-tuning"></a>Adaptive Echtzeitoptimierung
Für jede geschützte Anwendung optimiert Azure DDoS Protection Standard die Schwellenwerte für DDoS-Risikominderungsrichtlinien basierend auf den Datenverkehrs-Profilmustern der Anwendung automatisch. Der Dienst führt diese Anpassung mithilfe von zwei Erkenntnissen durch:

- Automatische Ermittlung der Datenverkehrsmuster pro Kunde (pro IP) für Schichten 3 und 4
- Minimieren falsch positiver Ergebnisse – davon ausgehend, dass die Skalierung in Azure das Absorbieren signifikanter Datenverkehrsmengen erlaubt

![Adaptive Echtzeitoptimierung](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Angriffsanalysen, Telemetriedaten, Überwachung und Warnungen
Azure DDoS Protection identifiziert und entschärft DDoS-Angriffe ohne jegliches Eingreifen des Benutzers.

- Wenn sich die geschützte Ressource in dem durch Azure Security Center abgedeckten Abonnement befindet, sendet DDoS Protection Standard automatisch eine Warnung an Security Center, sobald ein DDoS-Angriff auf die geschützte Anwendung erkannt und entschärft wird.
- Um benachrichtigt zu werden, wenn eine Risikominderung für eine geschützte öffentliche IP-Adresse aktiv ist, können Sie alternativ eine [Warnung für die den DDoS-Angriff betreffende Metrik konfigurieren](telemetry-monitoring-alerting.md#configure-alerts-for-ddos-protection-metrics).
- Sie können außerdem Warnungen für die anderen DDoS-Metriken erstellen und [Angriffsanalysen konfigurieren](telemetry-monitoring-alerting.md), um das Ausmaß des Angriffs, den verworfenen Datenverkehr, die Angriffsvektoren, die wichtigsten Mitwirkenden und andere Details zu verstehen.

![DDoS-Metriken](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS Rapid Response (DRR)
Kunden von DDoS Protection Standard haben jetzt während eines aktiven Angriffs Zugang zum [Rapid Response-Team](ddos-rapid-response.md). DRR kann sowohl bei der Untersuchung eines Angriffs während eines Angriffs als auch bei der Analyse nach einem Angriff helfen.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-Garantie und Kostenschutz
Der DDoS Protection Standard-Dienst wird durch eine SLA von 99,99 % abgedeckt, und der Kostenschutz bietet Ressourcenguthaben für Aufskalieren während eines dokumentierten Angriffs. Weitere Informationen finden Sie unter [SLA für Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Empfohlene Partnerszenarien
Im Folgenden werden die wichtigsten Vorteile aufgeführt, die Sie durch Integration in Azure DDoS Protection Standard erzielen können:

- Die angebotenen Dienste der Partner (Load Balancer, Web Application Firewall, Firewall usw.) für Ihre Kunden werden automatisch durch Azure DDoS Protection Standard im Back-End geschützt (White-Label).
- Partner besitzen Zugriff auf Angriffsanalysen und Telemetriedaten von Azure DDoS Protection Standard, die sie in ihre eigenen Produkte integrieren können, um eine einheitliche Benutzererfahrung zu bieten.  
- Partner besitzen Zugriff auf DDoS Rapid Response-Support für Probleme im Zusammenhang mit DDoS, auch wenn Azure Rapid Response nicht verwendet wird.
- Die geschützten Anwendungen der Partner werden durch eine DDoS-SLA-Garantie und Kostenschutz bei DDoS-Angriffen unterstützt.

## <a name="technical-integration-overview"></a>Technische Integration: Übersicht
Möglichkeiten für Azure DDoS Protection Standard-Partnerschaften werden über das Azure-Portal, APIs und die CLI bzw. PS zur Verfügung gestellt.

### <a name="integrate-with-ddos-protection-standard"></a>Integration in DDoS Protection Standard
Die folgenden Schritte sind erforderlich, damit Partner die Integration in Azure DDoS Protection Standard konfigurieren können:
1. Erstellen eines DDoS Protection-Plans im gewünschten Abonnement (Partnerabonnement). Schrittweise Anleitungen dazu finden Sie unter [Erstellen eines DDoS Protection Standard-Plans](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Für einen bestimmten Mandanten muss nur ein DDoS Protection-Plan erstellt werden. 
2. Bereitstellen eines Diensts mit einem öffentlichen Endpunkt in Ihren Partnerabonnements, z. B. Load Balancer, Firewalls und Web Application Firewall. 
3. Aktivieren von Azure DDoS Protection Standard für das virtuelle Netzwerk des Diensts, der über öffentliche Endpunkte verfügt, unter Verwendung des im ersten Schritt erstellten DDoS Protection-Plans. Schrittweise Anleitungen dazu finden Sie unter [Aktivieren des DDoS Protection Standard-Plans](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network).
   > [!IMPORTANT] 
   > Nachdem Azure DDoS Protection Standard für ein virtuelles Netzwerk aktiviert wurde, werden alle öffentlichen IP-Adressen in diesem virtuellen Netzwerk automatisch geschützt. Der Ursprung dieser öffentlichen IP-Adressen kann sich innerhalb von Azure (Clientabonnement) oder außerhalb von Azure befinden. 
4. Optional können Sie Azure DDoS Protection Standard-Telemetriedaten und -Angriffsanalysen in Ihr anwendungsspezifisches Dashboard für Kunden integrieren. Weitere Informationen zum Verwenden von Telemetriedaten finden Sie unter [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry-monitoring-alerting.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Anleitungen zum Onboarding und technische Dokumentation

- [Azure DDoS Protection-Produktseite](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection Standard overview (Übersicht über Azure DDoS Protection Standard)](ddos-protection-overview.md)
- [API-Referenz zu Azure DDoS Protection](/rest/api/virtualnetwork/ddosprotectionplans)
- [API-Referenz zu Azure Virtual Network](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Hier erhalten Sie Hilfe

- Wenn Sie Fragen zu Anwendungs-, Dienst- oder Produktintegrationen in Azure DDoS Protection Standard haben, wenden Sie sich an die [Azure-Sicherheitscommunity](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Verfolgen Sie den Informationsaustausch auf [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Markteinführung

- Das primäre Programm für eine Partnerschaft mit Microsoft ist das [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph-Sicherheitsintegrationen fallen in die Kategorie [Unabhängiger MPN-Softwareanbieter (ISV)](https://partner.microsoft.com/saas-solution-guide).
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) ist das Programm speziell für Microsoft-Sicherheitspartner, das Sie dabei unterstützt, Ihre Sicherheitsprodukte zu verbessern und die Auffindbarkeit ihrer Integrationen für Microsoft-Sicherheitsprodukte durch Kunden zu verbessern.

## <a name="next-steps"></a>Nächste Schritte
Anzeigen vorhandener Partnerintegrationen:

- [Barracuda WAF-as-a-Service](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF von Radware](https://www.radware.com/resources/microsoft-azure/)