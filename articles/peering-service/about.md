---
title: Übersicht über Azure Peering Service
description: Erhalten Sie eine Übersicht über Azure Peering Service.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026728"
---
# <a name="azure-peering-service-overview"></a>Übersicht über Azure Peering Service

Azure Peering Service ist ein Netzwerkdienst zur Verbesserung der Kundenkonnektivität mit Microsoft Cloud Services wie Microsoft 365, Dynamics 365, SaaS-Diensten (Software-as-a-Service), Azure und anderen Microsoft-Diensten, auf die über das öffentliche Internet zugegriffen werden kann. Microsoft ist eine Partnerschaft mit Internetdienstanbietern (ISPs), Internet Exchange-Partnern (IXPs) und SDCI-Anbietern (Software-Defined Cloud Interconnect) auf der ganzen Welt eingegangen, um eine zuverlässige und hochgradig leistungsfähige öffentliche Konnektivität mit optimalem Routing vom Kunden zum Microsoft-Netzwerk zu ermöglichen.

Mit Peering Service können Kunden einen gut verbundenen Partnerdienstanbieter in einer bestimmten Region auswählen. Die öffentliche Konnektivität ist für hohe Zuverlässigkeit und minimale Latenz von Clouddiensten zum Standort des Endbenutzers optimiert.

![Verteilte Konnektivität mit der Microsoft-Cloud](./media/peering-service-about/peering-service-what.png)

Kunden können sich auch für Peering Service-Telemetrie anmelden, z. B. Benutzerlatenzmessungen für das Microsoft-Netzwerk, BGP-Routenüberwachung und Warnungen zu Leaks und Hijacking, indem sie die Peering Service-Verbindung im Azure-Portal registrieren. 

Um Peering Service zu verwenden, müssen Kunden sich nicht bei Microsoft registrieren. Die einzige Anforderung besteht darin, einen [Peering Service-Partner](location-partners.md) zu kontaktieren, um den Dienst zu erhalten. Zum Anmelden für die Peering Service-Telemetrie müssen sich Kunden im Azure-Portal für sie registrieren.

Eine Anleitung zum Registrieren von Peering Service finden Sie unter [Registrieren von Peering Service – Azure-Portal](azure-portal.md). 

> [!NOTE]
> Dieser Artikel richtet sich an Netzwerkarchitekten, die für die Konnektivität ihres Unternehmens mit der Cloud und dem Internet verantwortlich sind.


## <a name="what-is-peering-service"></a>Was ist Peering Service?

Peering Service ist:

- Ein IP-Dienst, der das öffentliche Internet verwendet. 
- Eine Kollaborationsplattform für die Zusammenarbeit mit Dienstanbietern als Mehrwertdienst, der eine optimale und zuverlässige Weiterleitung an den Kunden über Dienstanbieterpartner an die Microsoft-Cloud über das öffentliche Netzwerk bieten soll.

Peering Service ist kein Produkt für private Verbindungen wie Azure ExpressRoute und kein VPN-Produkt.

> [!NOTE]
> Weitere Informationen zu ExpressRoute finden Sie in der [ExpressRoute-Dokumentation](../expressroute/index.yml).
>

## <a name="background"></a>Hintergrund

Microsoft 365, Dynamics 365 und andere Microsoft-SaaS-Dienste werden in mehreren Microsoft-Rechenzentren gehostet und sind von jedem geografischen Standort aus zugänglich. Das globale Microsoft-Netzwerk verfügt über Microsoft Edge-PoP-Standorte (Point of Presence) auf der ganzen Welt, an denen über die Dienstanbieter eine Verbindung mit einem Endbenutzer hergestellt werden kann. 

Microsoft und die Partnerdienstanbieter stellen sicher, dass der Datenverkehr für die mit einer Peering Service-Verbindung registrierten Präfixe über die nächstgelegenen Microsoft Edge-PoP-Standorte in das globale Microsoft-Netzwerk eintritt und dieses verlässt. Microsoft stellt sicher, dass der von den mit Peering Service-Verbindungen registrierten Präfixen ausgehende Netzwerkdatenverkehr über die nächstgelegenen Microsoft Edge-PoP-Standorte im globalen Microsoft-Netzwerk weitergeleitet wird.

![Microsoft-Netzwerk und öffentliche Konnektivität](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Weitere Informationen zum globalen Microsoft-Netzwerk finden Sie unter [Globales Microsoft-Netzwerk](../networking/microsoft-global-network.md).
>

## <a name="why-use-peering-service"></a>Gründe für die Verwendung von Peering Service

Unternehmen, die einen Zugriff auf die Cloud über das Internet erhalten möchten, eine SD-WAN-Architektur in Erwägung ziehen oder in großem Umfang Microsoft-SaaS-Dienste nutzen, benötigen eine robuste und leistungsstarke Internetkonnektivität. Kunden können diese Umstellung mit Peering Service erreichen. Microsoft hat sich mit Dienstanbietern zusammengetan, um eine zuverlässige und leistungsorientierte öffentliche Konnektivität mit der Microsoft-Cloud bereitzustellen. Einige der wichtigsten Kundenfeatures sind hier aufgeführt:

- Bestes öffentliches Routing über das Internet zu Microsoft Azure Cloud Services für optimale Leistung und Zuverlässigkeit
- Möglichkeit, den bevorzugten Dienstanbieter für die Verbindung mit der Microsoft-Cloud auszuwählen
- Datenverkehrsbezogene Erkenntnisse wie Latenzberichte und Präfixüberwachung
- Optimale Netzwerkhops (AS-Hops) aus der Microsoft-Cloud
- Routenanalysen und Statistiken: Ereignisse für ([BGP-](https://en.wikipedia.org/wiki/Border_Gateway_Protocol))Routenanomalien (Leak- oder Hijackingerkennung) und suboptimales Routing.

### <a name="robust-reliable-peering"></a>Stabiles, zuverlässiges Peering

Peering Service nutzt zwei Arten von Redundanz:

- **Lokale Redundanz**

   Microsoft und die Dienstanbieter stellen über mehrere Microsoft Edge-PoP-Standorte Verbindungen untereinander her, um Peering Service bereitzustellen. An jedem Standort muss die Verbindung ein Failover über zwei Router hinweg unterstützen.

   Jeder Peeringstandort wird mit redundanten unterschiedlichen Peeringlinks bereitgestellt.

- **Georedundanz**

   Microsoft ist an mehreren Metrostandorten mit Dienstanbietern vernetzt, sodass der Datenverkehr über alternative Standorte an und von Microsoft geleitet wird, wenn einer der Edge-Knoten eine beeinträchtigte Leistung aufweist. Microsoft leitet Datenverkehr in seinem globalen Netzwerk für eine optimale Leistung mithilfe von SDN-basierten Routingrichtlinien weiter.

    Bei dieser Art von Redundanz wird der kürzeste Routingpfad verwendet, indem immer der nächste Microsoft Edge-PoP für den Endbenutzer ausgewählt und sichergestellt wird, dass der Kunde nur einen Netzwerkhop (AS-Hop) von Microsoft entfernt ist.

   ![Georedundanz](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optimales Routing

Die folgende Routingtechnik wird bevorzugt:

-  **Cold-Potato-Routing**

   Das softwaredefinierte Cold-Potato-Routingverfahren bietet Kontrolle über den Netzwerkdatenverkehr aus der Microsoft-Cloud. Mit ihm wird sichergestellt, dass der Datenverkehr im außerordentlich zuverlässigen globalen Microsoft-Netzwerk mit hoher Kapazität und geringer Latenz verbleibt, bis er sich so nah wie möglich am Ziel befindet.
   
   Das Routing ohne Cold-Potato-Technik wird als Hot-Potato-Routing bezeichnet. Beim Hot-Potato-Routing wird der Datenverkehr aus der Microsoft-Cloud über das Internet geleitet.

   ![Cold-Potato-Routing](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Überwachungsplattform

   Die Dienstüberwachung ist ein Angebot zum Analysieren des Datenverkehrs und des Routings von Kunden. Sie bietet die folgenden Funktionen: 

-  **Anomalieerkennung für Internet-BGP-Routen**
          
   Mit diesem Dienst können Routinganomalieereignisse wie das Hijacking der Route an die Präfixe des Kunden erkannt und Warnungen für diese ausgegeben werden.

-  **Kundenlatenz**

   Dieser Dienst überwacht die Routingleistung zwischen dem Standort des Kunden und Microsoft. 
   
   Die Routingleistung wird gemessen, indem die Roundtripzeit vom Client bis zum Erreichen des Microsoft Edge-PoP überprüft wird. Kunden können die Latenzberichte für verschiedene geografische Standorte anzeigen.

   Bei der Überwachung werden die Ereignisse im Fall einer Dienstbeeinträchtigung aufgezeichnet.

   ![Überwachungsplattform für Peering Service](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Datenverkehrsschutz

Das Routing erfolgt nur über einen bevorzugten Pfad, der beim Registrieren des Kunden bei Peering Service definiert wird.

Microsoft garantiert, dass der Datenverkehr über bevorzugte Pfade weitergeleitet wird, auch wenn schädliche Aktivitäten erkannt werden.

BGP-Routenanomalien werden ggf. im Azure-Portal gemeldet.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Peering Service-Verbindungen finden Sie unter [Peering Service-Verbindungen](connection.md).
- Informationen zur Peering Service-Verbindungstelemetrie finden Sie unter [Peering Service-Verbindungstelemetrie](connection-telemetry.md).
- Einen Dienstanbieter können Sie unter [Peering Service: Partner und Standorte](location-partners.md) finden.
- Informationen zum Onboarding einer Peering Service-Verbindung finden Sie unter [Onboardingmodell eines Peering Service-Modells](onboarding-model.md).
- Informationen zum Registrieren einer Verbindung über das Azure-Portal finden Sie unter [Registrieren einer Peering Service-Verbindung über das Azure-Portal](azure-portal.md).
- Informationen zum Messen der Telemetrie finden Sie unter [Messen der Verbindungstelemetrie](measure-connection-telemetry.md).