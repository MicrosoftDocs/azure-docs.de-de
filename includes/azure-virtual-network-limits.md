---
title: include file
description: include file
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 9751bd082947e66b3fc5ea47448857558301a1e7
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97366345"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Netzwerkgrenzwerte – Azure Resource Manager
Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über den **Azure Resource Manager** pro Region pro Abonnement verwaltet werden. Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vor Kurzem wurden die Standardgrenzwerte auf die maximalen Grenzwerte erhöht. Falls die Spalte für den maximalen Grenzwert nicht vorhanden ist, gibt es für die entsprechende Ressource keine anpassbaren Grenzwerte. Wenn diese Grenzwerte für Sie in der Vergangenheit durch den Support erhöht wurden und die aktualisierten Grenzwerte in den folgenden Tabellen nicht angezeigt werden, [erstellen Sie eine kostenlose Online-Kundensupportanfrage](../articles/azure-resource-manager/templates/error-resource-quota.md).

| Resource | Begrenzung | 
| --- | --- |
| Virtuelle Netzwerke |1\.000 |
| Subnetze pro virtuellem Netzwerk |3,000 |
| VNET-Peerings pro virtuellem Netzwerk |500 |
| [Gateways für virtuelle Netzwerke (VPN-Gateways) pro virtuellem Netzwerk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways für virtuelle Netzwerke (ExpressRoute-Gateways) pro virtuellem Netzwerk](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-Server pro virtuellem Netzwerk |20 |
| Private IP-Adressen pro virtuellem Netzwerk |65.536 |
| Private IP-Adressen pro Netzwerkschnittstelle |256 |
| Private IP-Adressen pro virtuellem Computer |256 |
| Öffentliche IP-Adressen pro Netzwerkschnittstelle |256 |
| Öffentliche IP-Adressen pro virtuellem Computer |256 |
| [Parallele TCP- oder UDP-Flows pro NIC eines virtuellen Computers bzw. einer Rolleninstanz](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Netzwerkschnittstellenkarten |65.536 |
| Netzwerksicherheitsgruppen |5\.000 |
| NSG-Regeln pro NSG |1\.000 |
| Angegebene IP-Adressen und -Bereiche für die Quelle oder das Ziel in einer Sicherheitsgruppe |4\.000 |
| Anwendungssicherheitsgruppen |3,000 |
| Anwendungssicherheitsgruppen pro IP-Konfiguration pro NIC |20 |
| IP-Konfigurationen pro Anwendungssicherheitsgruppe |4\.000 |
| Anwendungssicherheitsgruppen, die in allen Sicherheitsregeln einer Netzwerksicherheitsgruppe angegeben werden können |100 |
| Benutzerdefinierte Routingtabellen |200 |
| Benutzerdefinierte Routen pro Routingtabelle |400 |
| Punkt-zu-Standort-Stammzertifikate pro Azure VPN Gateway |20 |
| TAPs für virtuelle Netzwerke |100 |
| Netzwerkschnittstellen-TAP-Konfigurationen pro virtuellem Netzwerk-TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Grenzwerte für öffentliche IP-Adressen
| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Öffentliche IP-Adressen<sup>1</sup> | 10 für Basic. | Wenden Sie sich an den Support. |
| Statische öffentliche IP-Adressen<sup>1</sup> | 10 für Basic. | Wenden Sie sich an den Support. |
| Öffentliche Standard-IP-Adressen<sup>1</sup> | 10 | Wenden Sie sich an den Support. |
| [Öffentliche IP-Adressen pro Ressourcengruppe](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Wenden Sie sich an den Support. | 
| Präfixe von öffentlichen IP-Adressen | begrenzt durch die Anzahl der öffentlichen Standard-IPs in einem Abonnement | Wenden Sie sich an den Support. |
| Präfixlänge für öffentliche IP-Adressen | /28 | Wenden Sie sich an den Support. |

<sup>1</sup>Die Standardgrenzwerte für öffentliche IP-Adressen unterscheiden sich je nach Typ der Angebotskategorie, z. B. kostenlose Testversion, nutzungsbasierte Bezahlung, CSP. Beispielsweise ist der Standardwert für Enterprise Agreement-Abonnements 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Load Balancer-Grenzwerte
Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über den Azure Resource Manager pro Region pro Abonnement verwaltet werden. Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

**Load Balancer Standard**

| Resource                                | Begrenzung         |
|-----------------------------------------|-------------------------------|
| Load Balancer                          | 1\.000                         |
| Regeln pro Ressource                      | 1\.500                         |
| Regeln pro NIC (für alle IPs einer NIC) | 300                           |
| Front-End-IP-Konfigurationen              | 600                           |
| Größe des Back-End-Pools                       | 1\.000 IP-Konfigurationen, einzelnes virtuelles Netzwerk |
| Back-End-Ressourcen pro Lastenausgleich <sup>1<sup> | 250                   |
| Hochverfügbarkeitsports                 | 1 pro internem Front-End       |
| Ausgangsregeln pro Lastenausgleichsmodul        | 600                           |
| Lastenausgleichsmodule pro virtuellem Computer                   | 2 (1x öffentlich, 1x intern)   |

<sup>1</sup>Der Grenzwert liegt bei bis zu 150 Ressourcen in beliebiger Kombination aus eigenständigen VM-Ressourcen, Verfügbarkeitsgruppenressourcen und Platzierungsgruppen für Skalierungsgruppen.

**Load Balancer Basic**

| Resource                                | Begrenzung        |
|-----------------------------------------|------------------------------|
| Load Balancer                          | 1\.000                        |
| Regeln pro Ressource                      | 250                          |
| Regeln pro NIC (für alle IPs einer NIC) | 300                          |
| Front-End-IP-Konfigurationen              | 200                          |
| Größe des Back-End-Pools                       | 300 IP-Konfigurationen, einzelne Verfügbarkeitsgruppe |
| Verfügbarkeitsgruppen pro Lastenausgleichsmodul     | 1                            |
| Lastenausgleichsmodule pro virtuellem Computer                   | 2 (1x öffentlich, 1x intern)  |

<a name="virtual-networking-limits-classic"></a>Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über das **klassische** Bereitstellungsmodell verwaltet werden (pro Abonnement). Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Virtuelle Netzwerke |100 |100 |
| Lokale Netzwerksites |20 |50 |
| DNS-Server pro virtuellem Netzwerk |20 |20 |
| Private IP-Adressen pro virtuellem Netzwerk |4\.096 |4\.096 |
| Parallele TCP-Flüsse pro NIC eines virtuellen Computers bzw. einer Rolleninstanz |500.000 bis 1.000.000 für zwei oder mehr NICs. |500.000 bis 1.000.000 für zwei oder mehr NICs. |
| Netzwerksicherheitsgruppen |200 |200 |
| NSG-Regeln pro NSG |200 |1\.000 |
| Benutzerdefinierte Routingtabellen |200 |200 |
| Benutzerdefinierte Routen pro Routingtabelle |400 |400 |
| Öffentliche IP-Adressen (dynamisch) |500 |500 |
| Reservierte öffentliche IP-Adressen |500 |500 |
| Öffentliche IP-Adresse pro Bereitstellung |5 |Support kontaktieren |
| Private IP-Adressen (interner Lastenausgleich) pro Bereitstellung |1 |1 |
| Endpunkt-Zugriffssteuerungslisten (ACLs) |50 |50 |
