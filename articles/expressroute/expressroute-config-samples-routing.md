---
title: Beispiele für die Routerkonfiguration – Azure ExpressRoute | Microsoft-Dokumentation
description: Diese Seite enthält Konfigurationsbeispiele für Router von Cisco und Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367471"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Beispiele für die Routerkonfiguration zum Einrichten und Verwalten des Routings
Diese Seite enthält Schnittstellen- und Routingkonfigurationsbeispiele für Router der Reihen Cisco IOS-XE und Juniper MX für die Arbeit mit ExpressRoute. Diese Beispiele dienen nur als Leitfaden und müssen nicht wie angegeben verwendet werden. Sie können mit Ihrem Anbieter an der Ausarbeitung geeigneter Konfigurationen für Ihr Netzwerk zusammenarbeiten. 

> [!IMPORTANT]
> Die Beispiele auf dieser Seite sind ausschließlich als Leitfaden konzipiert. Sie müssen mit dem Vertriebsteam/Technikteam Ihres Anbieters und Ihrem Netzwerkteam zusammenarbeiten, um für Ihre Anforderungen geeignete Konfigurationen zu bestimmen. Microsoft bietet keine Unterstützung bei Problemen im Zusammenhang mit Konfigurationen, die auf dieser Seite aufgeführt sind. Sie müssen sich bei Problemen an den Gerätehersteller wenden.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Einstellungen für MTU und TCP MSS für Routerschnittstellen
* Die MTU (Maximum Transmission Unit, Maximale Übertragungseinheit) für die ExpressRoute-Schnittstelle ist 1.500, also der übliche MTU-Standardwert für eine Ethernetschnittstelle eines Routers. Nur wenn Ihr Router standardmäßig einen anderen MTU-Wert aufweist, müssen Sie einen Wert für die Routerschnittstelle angeben.
* Im Gegensatz zu einem Azure VPN Gateway muss der TCP MSS-Wert (Maximum Segment Size, maximale Segmentgröße) für eine ExpressRoute-Verbindung nicht angegeben werden.

Die unten aufgeführten Beispiele zur Routerkonfiguration gelten für alle Peerings. Unter [ExpressRoute-Peerings](expressroute-circuit-peerings.md) und [Routinganforderungen für ExpressRoute](expressroute-routing.md) finden Sie weitere Details.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE-basierte Router
Die Beispiele in diesem Abschnitt beziehen sich auf Router, auf denen die IOS-XE-Betriebssystemfamilie ausgeführt wird.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurieren von Schnittstellen und Unterschnittstellen
Sie benötigen auf jedem Router, den Sie mit Microsoft verbinden, eine Unterschnittstelle pro Peering. Eine Unterschnittstelle kann anhand einer VLAN-ID oder eines gestapelten Paars von VLAN-IDs und einer IP-Adresse identifiziert werden.

**Dot1Q-Schnittstellendefinition**

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit einer einzelnen VLAN-ID bereit. Die VLAN-ID ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-Schnittstellendefinition**

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit zwei VLAN-IDs bereit. Falls die äußere VLAN-ID (s-Tag) verwendet wird, bleibt sie über alle Peerings gleich. Die innere VLAN-ID (c-Tag) ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Einrichten von eBGP-Sitzungen
Sie müssen für jedes Peering eine BGP-Sitzung bei Microsoft einrichten. Mit dem folgenden Beispiel können Sie eine BGP-Sitzung bei Microsoft einrichten. Wenn die IPv4-Adresse, die Sie für Ihre Unterschnittstelle verwendet haben, "a.b.c.d" war, lautet die IP-Adresse des BGP-Nachbarn (Microsoft) "a.b.c.d+1". Das letzte Oktett in der IPv4-Adresse des BGP-Nachbarn ist immer eine gerade Zahl.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Einrichten von Präfixen für die Ankündigung über die BGP-Sitzung
Sie können den Router so konfigurieren, dass Microsoft bestimmte Präfixe angekündigt werden. Dies ist mit dem folgenden Beispiel möglich.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Routenzuordnungen
Anhand von Routenzuordnungen und Präfixlisten können Sie Präfixe filtern, die in Ihrem Netzwerk weitergegeben werden. Diese Aufgabe können Sie mit dem folgenden Beispiel durchführen. Stellen Sie sicher, dass entsprechende Präfixlisten eingerichtet wurden.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Router der Juniper MX-Serie
Die Beispiele in diesem Abschnitt gelten für Router der Juniper MX-Serie.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurieren von Schnittstellen und Unterschnittstellen

**Dot1Q-Schnittstellendefinition**

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit einer einzelnen VLAN-ID bereit. Die VLAN-ID ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ-Schnittstellendefinition**

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit zwei VLAN-IDs bereit. Falls die äußere VLAN-ID (s-Tag) verwendet wird, bleibt sie über alle Peerings gleich. Die innere VLAN-ID (c-Tag) ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Einrichten von eBGP-Sitzungen
Sie müssen für jedes Peering eine BGP-Sitzung bei Microsoft einrichten. Mit dem folgenden Beispiel können Sie eine BGP-Sitzung bei Microsoft einrichten. Wenn die IPv4-Adresse, die Sie für Ihre Unterschnittstelle verwendet haben, "a.b.c.d" war, lautet die IP-Adresse des BGP-Nachbarn (Microsoft) "a.b.c.d+1". Das letzte Oktett in der IPv4-Adresse des BGP-Nachbarn ist immer eine gerade Zahl.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Einrichten von Präfixen für die Ankündigung über die BGP-Sitzung
Sie können den Router so konfigurieren, dass Microsoft bestimmte Präfixe angekündigt werden. Dies ist mit dem folgenden Beispiel möglich.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Routenzuordnungen
Anhand von Routenzuordnungen und Präfixlisten können Sie Präfixe filtern, die in Ihrem Netzwerk weitergegeben werden. Diese Aufgabe können Sie mit dem folgenden Beispiel durchführen. Stellen Sie sicher, dass entsprechende Präfixlisten eingerichtet wurden.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md) .

