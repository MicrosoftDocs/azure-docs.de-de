---
title: include file
description: include file
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/13/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 813c8e92907a60046c2e53f97d4dd05125076241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133079"
---
<a name="virtual-networking-limits-classic"></a>Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über das **klassische** Bereitstellungsmodell verwaltet werden (pro Abonnement). Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Virtuelle Netzwerke |50 |100 |
| Lokale Netzwerksites |20 |Wenden Sie sich an den Support. |
| DNS-Server pro virtuellem Netzwerk |20 |20 |
| Private IP-Adressen pro virtuellem Netzwerk |4\.096 |4\.096 |
| Parallele TCP-Flüsse pro NIC eines virtuellen Computers bzw. einer Rolleninstanz |500\.000 bis 1.000.000 für zwei oder mehr NICs. |500\.000 bis 1.000.000 für zwei oder mehr NICs. |
| Netzwerksicherheitsgruppen |200 |200 |
| NSG-Regeln pro NSG |1\.000 |1\.000 |
| Benutzerdefinierte Routingtabellen |200 |200 |
| Benutzerdefinierte Routen pro Routingtabelle |400 |400 |
| Öffentliche IP-Adressen (dynamisch) |5 |Support kontaktieren |
| Reservierte öffentliche IP-Adressen |20 |Support kontaktieren |
| Öffentliche VIP-Adressen pro Bereitstellung |5 |Support kontaktieren |
| Private VIP-Adressen (interner Lastenausgleich) pro Bereitstellung |1 |1 |
| Endpunkt-Zugriffssteuerungslisten (ACLs) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Netzwerkgrenzwerte – Azure Resource Manager
Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über den **Azure Resource Manager** pro Region pro Abonnement verwaltet werden. Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vor Kurzem wurden die Standardgrenzwerte auf die maximalen Grenzwerte erhöht. Falls die Spalte für den maximalen Grenzwert nicht vorhanden ist, gibt es für die entsprechende Ressource keine anpassbaren Grenzwerte. Wenn diese Grenzwerte für Sie in der Vergangenheit durch den Support erhöht wurden und die aktualisierten Grenzwerte in den folgenden Tabellen nicht angezeigt werden, [erstellen Sie eine kostenlose Online-Kundensupportanfrage](../articles/azure-resource-manager/resource-manager-quota-errors.md).

| Resource | Standard/maximales Limit | 
| --- | --- |
| Virtuelle Netzwerke |1\.000 |
| Subnetze pro virtuellem Netzwerk |3,000 |
| VNET-Peerings pro virtuellem Netzwerk |500 |
| DNS-Server pro virtuellem Netzwerk |20 |
| Private IP-Adressen pro virtuellem Netzwerk |65\.536 |
| Private IP-Adressen pro Netzwerkschnittstelle |256 |
| Private IP-Adressen pro virtuellem Computer |256 |
| Parallele TCP-Flüsse pro NIC eines virtuellen Computers bzw. einer Rolleninstanz |500\.000 |
| Netzwerkschnittstellenkarten |65\.536 |
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

#### <a name="publicip-address"></a>Grenzwerte für öffentliche IP-Adressen
| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Öffentliche IP-Adressen – dynamisch | 1\.000 für Basic. |Wenden Sie sich an den Support. |
| Öffentliche IP-Adressen – statisch | 1\.000 für Basic. |Wenden Sie sich an den Support. |
| Öffentliche IP-Adressen – statisch | 200 für Standard.|Wenden Sie sich an den Support. |
| Präfixgröße für öffentliche IP-Adressen | /28 | Wenden Sie sich an den Support. |

#### <a name="load-balancer"></a>Load Balancer-Grenzwerte
Die folgenden Grenzwerte gelten nur für Netzwerkressourcen, die über den Azure Resource Manager pro Region pro Abonnement verwaltet werden. Erfahren Sie, wie Sie [die aktuelle Ressourcennutzung anhand der Grenzwerte Ihres Abonnements anzeigen](../articles/networking/check-usage-against-limits.md).

| Resource | Standard/maximales Limit |
| --- | --- |
| Load Balancer | 1\.000 | 
| Regeln pro Ressource, Basic | 250 |
| Regeln pro Ressource, Standard | 1\.500 | 
| Regeln pro IP-Konfiguration | 299 |
| Regeln pro NIC | 300 |
| Front-End-IP-Konfigurationen, Basic | 200 |
| Front-End-IP-Konfigurationen, Standard | 600 |
| Back-End-Pool, Basic | 100, eine Verfügbarkeitsgruppe |
| Back-End-Pool, Standard | 1\.000, einzelnes virtuelles Netzwerk |
| Back-End-Ressourcen pro Load Balancer, Standard<sup>1</sup> | 150 |
| Ports für hohe Verfügbarkeit, Standard | 1 pro internem Front-End |

<sup>1</sup>Das Limit liegt bei bis zu 150 Ressourcen in jeder Kombination aus eigenständigen VM-Ressourcen, Verfügbarkeitsgruppenressourcen und VM-Skalierungsgruppenressourcen.

