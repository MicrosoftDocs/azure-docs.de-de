---
title: 'Azure-Sicherheitsvergleichstest V2: Netzwerksicherheit'
description: 'Azure-Sicherheitsvergleichstest V2: Netzwerksicherheit'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 38ab16fbadee245810a4486170dd54922c0c38f9
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558911"
---
# <a name="security-control-v2-network-security"></a>Sicherheitskontrolle V2: Netzwerksicherheit

Netzwerksicherheit deckt Steuerelemente zum Sichern und Schützen von Azure-Netzwerken ab. Dies umfasst das Sichern von virtuellen Netzwerken, das Einrichten privater Verbindungen, das Verhindern und Entschärfen externer Angriffe und das Sichern des DNS.

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementieren der Sicherheit für internen Datenverkehr

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-1 | 9.2, 9.4, 14.1, 14.2, 14.3 | AC-4, CA-3, SC-7 |

Stellen Sie sicher, dass alle virtuellen Azure-Netzwerke einem Prinzip der Unternehmenssegmentierung unterliegen, das sich den geschäftlichen Risiken anpasst. Jedes System, das ein höheres Risiko für das Unternehmen darstellen könnte, sollte innerhalb eines eigenen virtuellen Netzwerks isoliert und mit einer Netzwerksicherheitsgruppe (NSG) und/oder Azure Firewall ausreichend geschützt werden. 

Beschränken oder ermöglichen Sie den Datenverkehr zwischen internen Ressourcen gemäß Ihren Anwendungen und der Strategie der Unternehmenssegmentierung auf der Basis von Netzwerksicherheitsgruppen-Regeln. Bei bestimmten klar definierten Anwendungen (z. B. einer App mit drei Ebenen) kann dies ein äußerst sicherer Ansatz sein (standardmäßig verweigern, als Ausnahme zulassen). Dies ist möglicherweise nicht gut skalierbar, wenn viele Anwendungen und Endpunkte miteinander interagieren. Sie können auch die Azure-Firewall verwenden, wenn für eine große Anzahl von Unternehmenssegmenten oder Spokes (in einer Hub/Spoke-Topologie) eine zentrale Verwaltung erforderlich ist. 

Verwenden Sie die adaptive Netzwerkhärtung in Azure Security Center, um Netzwerksicherheitsgruppen-Konfigurationen zu empfehlen, die Ports und Quell-IP-Adressen gemäß externer Regeln für den Netzwerk-Datenverkehr einschränken.

Verwenden Sie Azure Sentinel, um die Verwendung von unsicheren Legacyprotokollen wie SSL/TLSv1, TLSv1, LM/SMBv1, WDigest, nicht signierte LDAP-Bindungen und schwache Chiffren in Kerberos zu ermitteln.

- [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../../virtual-network/tutorial-filter-network-traffic.md)

- [Bereitstellen und Konfigurieren von Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Adaptive Netzwerkhärtung in Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Arbeitsmappe für unsichere Protokolle in Azure Sentinel](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: Verbinden privater Netzwerke

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-2 | – | CA-3, AC-17, MA-4 |

Stellen Sie mit Azure ExpressRoute oder dem virtuellen privaten Azure-Netzwerk (VPN) private Verbindungen mit Azure-Rechenzentren und lokaler Infrastruktur in einer Housingumgebung her. ExpressRoute-Verbindungen werden nicht über das öffentliche Internet hergestellt und bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit und weniger Latenz als herkömmliche Internetverbindungen. Für Point-to-Site-VPN und Site-to-Site-VPN können Sie lokale Geräte oder Netzwerke mit einem virtuellen Netzwerk verbinden, indem Sie eine beliebige Kombination dieser VPN-Optionen und Azure ExpressRoute verwenden.

Stellen Sie eine Verbindung von zwei oder mehr virtuellen Netzwerken in Azure mittels Peering virtueller Netzwerke oder über Private Link her. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet. 

- [ExpressRoute-Konnektivitätsmodelle](../../expressroute/expressroute-connectivity-models.md) 

- [Was ist VPN Gateway?](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering von virtuellen Netzwerken](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Einrichten des Zugriffs über das private Netzwerk auf Azure-Dienste

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Ermöglichen Sie mit Azure Private Link von Ihren virtuellen Netzwerken aus privaten Zugriff auf Azure-Dienste, ohne über das Internet zu gehen. Verwenden Sie in Situationen, in denen Azure Private Link noch nicht verfügbar ist, Azure Virtual Network-Dienstendpunkte.  Azure Virtual Network-Dienstendpunkte bieten über eine optimierte, über das Azure-Backbone-Netzwerk führende Route sicheren Zugriff auf Dienste.  

Der private Zugriff ist neben der Authentifizierung und der von Azure-Diensten gebotenen Datenverkehrssicherheit eine zusätzliche Verteidigungsmaßnahme. 

- [Grundlegendes zu Azure Private Link](../../private-link/private-link-overview.md)

- [Grundlegendes zu VNET-Dienstendpunkten](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Schützen von Anwendungen und Diensten vor externen Netzwerkangriffen.

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-4 | 9.5, 12.3, 12.9 | SC-5, SC-7 |

Schützen Sie Azure-Ressourcen vor Angriffen aus externen Netzwerken einschließlich verteilter Denial-of-Service-Angriffe (DDoS), anwendungsspezifischer Angriffe und unerwünschtem und potenziell schädlichem Internetdatenverkehr. Azure bietet native Funktionen für Folgendes:
-   Schützen Sie mit der Azure-Firewall Anwendungen und Dienste vor potenziell schädlichem Datenverkehr aus dem Internet und von anderen externen Standorten. 

-   Schützen Sie mit WAF-Funktionen (Web Application Firewall) in Azure Application Gateway, Azure Front Door und Azure Content Delivery Network (CDN) Ihre Anwendungen, Dienste und APIs gegen Angriffe auf Anwendungsebene. 

-   Schützen Sie Ihre Ressourcen durch Aktivieren des DDoS-Standardschutzes in Ihren virtuellen Azure-Netzwerken vor DDoS-Angriffen. 
-   Verwenden Sie Azure Security Center, um Risiken durch Fehlkonfigurationen im Zusammenhang mit den oben genannten Punkten zu erkennen. 

- [Azure Firewall-Dokumentation](../../firewall/index.yml)

- [Bereitstellen von Azure WAF](../../web-application-firewall/overview.md)

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](../../ddos-protection/manage-ddos-protection.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Bereitstellen von Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-5 | 12.6, 12.7 | SI-4 |

Verwenden Sie auf Azure Firewall-Threat Intelligence basierende Filterung, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Wenn eine Payloaduntersuchung erforderlich ist, können Sie ein Angriffserkennungs-/Eindringschutzsystem (IDS/IPS) eines Drittanbieters mit Payloaduntersuchungsfunktionen aus Azure Marketplace bereitstellen. Alternativ können Sie eine hostbasierte IDS/IPS- oder Endpunkterkennungs- und Antwortlösung (EDR) in Verbindung mit einem netzwerkbasierten IDS/IPS oder stattdessen verwenden.  

Hinweis: Bei einer gesetzlichen oder sonstigen Anforderung der Verwendung von IDS/IPS müssen Sie sicherstellen, dass sie stets auf hochwertige Warnungen für Ihre SIEM-Lösung abgestimmt ist. 

- [Bereitstellen von Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace umfasst IDS-Funktionen von Drittanbietern](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [ATP-EDR-Funktion von Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Vereinfachen Sie Netzwerksicherheitsregeln durch Nutzung von Diensttags und Anwendungssicherheitsgruppen (ASGs). 

Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen im Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Sie können auch Anwendungssicherheitsgruppen verwenden, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Anstatt eine Richtlinie auf Basis expliziter IP-Adressen in Netzwerksicherheitsgruppen zu definieren, können Sie mit Anwendungssicherheitsgruppen die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren.

- [Grundlegendes zu Diensttags und deren Verwendung](../../virtual-network/service-tags-overview.md)

- [Grundlegendes zu Anwendungssicherheitsgruppen und deren Verwendung](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| NS-7 | – | SC-20, SC-21 |

Befolgen Sie die bewährten Methoden für die DNS-Sicherheit, um häufige Angriffe wie verwaistes DNS, DNS-Verstärkungsangriffe, DNS-Vergiftungen und -Spoofing usw. zu vermeiden. 

Wenn Azure DNS als autorisierender DNS-Dienst verwendet wird, stellen Sie sicher, dass DNS-Zonen und -Einträge vor versehentlicher oder böswilliger Änderung mit Azure RBAC und Ressourcensperren geschützt werden. 

- [Azure DNS – Übersicht](../../dns/dns-overview.md)

- [Secure Domain Name System (DNS) Deployment Guide](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) (Bereitstellungshandbuch für Secure Domain Name System)

- [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../fundamentals/subdomain-takeover.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
