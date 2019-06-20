---
title: Netzwerkübergreifende Azure-Verbindungen | Microsoft-Dokumentation
description: Auf dieser Seite wird ein Anwendungsszenario für netzwerkübergreifende Verbindungen und Lösungen beschrieben, das auf Azure-Netzwerkfeatures basiert.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866004"
---
# <a name="cross-network-connectivity"></a>Netzwerkübergreifende Verbindungen

Fabrikam Inc. hat eine große Niederlassung und eine umfangreiche Azure-Bereitstellung im Osten der USA. Die Back-End-Konnektivität zwischen den lokalen und Azure-basierten Bereitstellungen wird bei Fabrikam mit ExpressRoute realisiert. Contoso Ltd. verfügt entsprechend über eine Niederlassung und eine Azure-Bereitstellung im Westen der USA (USA, Westen). Die Back-End-Konnektivität zwischen den lokalen und Azure-basierten Bereitstellungen wird bei Contoso mit ExpressRoute realisiert.  

Fabrikam Inc. übernimmt Contoso Ltd. Nach der Fusion möchte Fabrikam die beiden Netzwerke miteinander verbinden. Die folgende Abbildung veranschaulicht das Szenario:

 [![1]][1]

Die gestrichelten Pfeile in der Mitte der obigen Abbildung stellen die gewünschten Netzwerkverbindungen dar. Es sollen drei Arten von übergreifenden Verbindungen verwendet werden: 1) Querverbindung von Fabrikam- und Contoso-VNETs, 2) regionsübergreifende lokale und VNET-Querverbindungen (Verbindung des lokalen Fabrikam-Netzwerks mit dem Contoso-VNET und des lokalen Contoso-Netzwerks mit dem Fabrikam-VNET) und 3) Querverbindung des lokalen Fabrikam- und Contoso-Netzwerks. 

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Contoso Ltd. vor der Übernahme.

[![2]][2]

Die folgende Tabelle enthält die bestehenden Routen einer VM unter dem Contoso-Abonnement vor der Übernahme. Laut Tabelle ist die VM im VNET neben den Standardkomponenten auch über den VNET-Adressraum und das lokale Contoso-Netzwerk informiert. 

[![4]][4]

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Fabrikam Inc. vor der Übernahme.

[![3]][3]

Die folgende Tabelle enthält die bestehenden Routen einer VM unter dem Fabrikam-Abonnement vor der Übernahme. Laut Tabelle ist die VM im VNET neben den Standardkomponenten auch über den VNET-Adressraum und das lokale Fabrikam-Netzwerk informiert.

[![5]][5]

In diesem Artikel gehen wir Schritt für Schritt vor. Es wird beschrieben, wie Sie die gewünschten Querverbindungen mit den folgenden Azure-Netzwerkfeatures erzielen:

* [Peering in virtuellen Netzwerken][Virtual network peering] 
* [ExpressRoute-Verbindungen für virtuelle Netzwerke][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Herstellen von Querverbindungen für VNETs

Durch das Peering virtueller Netzwerke (VNET-Peering) wird beim Verbinden von zwei virtuellen Netzwerken die optimale und beste Netzwerkleistung erzielt. Beim VNET-Peering wird das Peering von zwei VNETs unterstützt, die sich beide in derselben Azure-Region (VNET-Peering) oder in zwei unterschiedlichen Azure-Regionen (globales VNET-Peering) befinden. 

Wir konfigurieren nun das globale VNET-Peering zwischen den VNETs in den Azure-Abonnements von Contoso und Fabrikam. Informationen zur Erstellung des VNET-Peerings zwischen den virtuellen Netzwerken finden Sie im Artikel [Erstellen eines Peerings in virtuellen Netzwerken][Configure VNet peering].

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration des globalen VNET-Peerings dargestellt.

[![6]][6]

In der folgenden Tabelle sind die Routen enthalten, die der VM des Contoso-Abonnements bekannt sind. Achten Sie auf den letzten Eintrag der Tabelle. Dieser Eintrag ist das Ergebnis der Querverbindung der virtuellen Netzwerke.

[![7]][7]

In der folgenden Tabelle sind die Routen enthalten, die der VM des Fabrikam-Abonnements bekannt sind. Achten Sie auf den letzten Eintrag der Tabelle. Dieser Eintrag ist das Ergebnis der Querverbindung der virtuellen Netzwerke.

[![8]][8]

Per VNET-Peering werden zwei virtuelle Netzwerke direkt verknüpft (für den Eintrag *VNetGlobalPeering* in den obigen beiden Tabellen ist kein nächster Hop enthalten).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Querverbindung von VNETs in den lokalen Netzwerken

Wir können für eine ExpressRoute-Leitung eine Verbindung mit mehreren virtuellen Netzwerken herstellen. Unter [Einschränkungen bei Abonnements und Diensten][Subscription limits] ist angegeben, für wie viele virtuelle Netzwerke maximal eine Verbindung mit einer ExpressRoute-Leitung hergestellt werden kann. 

Wir stellen eine Verbindung zwischen der ExpressRoute-Leitung von Fabrikam und dem VNET des Contoso-Abonnements sowie entsprechend zwischen der ExpressRoute-Leitung von Contoso und dem VNET des Fabrikam-Abonnements her, um eine Querverbindung zwischen virtuellen Netzwerken und den lokalen Netzwerken zu erzielen. Wir müssen eine Autorisierung erstellen und verwenden, um ein virtuelles Netzwerk mit einer ExpressRoute-Leitung in einem anderen Abonnement zu verbinden.  Lesen Sie den folgenden Artikel: [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung][Connect-ER-VNet].

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration der ExpressRoute-Querverbindung mit den virtuellen Netzwerken dargestellt.

[![9]][9]

In der folgenden Tabelle ist die Routingtabelle des privaten Peerings der ExpressRoute-Leitung von Contoso Ltd. dargestellt, nachdem per ExpressRoute für die virtuellen Netzwerke eine Querverbindung mit den lokalen Netzwerken hergestellt wurde. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden virtuellen Netzwerken gehören.

[![10]][10]

In der folgenden Tabelle ist die Routingtabelle des privaten Peerings der ExpressRoute-Leitung von Fabrikam Inc. dargestellt, nachdem per ExpressRoute für die virtuellen Netzwerke eine Querverbindung mit den lokalen Netzwerken hergestellt wurde. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden virtuellen Netzwerken gehören.

[![11]][11]

In der folgenden Tabelle sind die Routen enthalten, die der VM des Contoso-Abonnements bekannt sind. Achten Sie in der Tabelle auf die Einträge vom Typ *Gateway für virtuelle Netzwerke*. Die VM sieht Routen beider lokalen Netzwerke.

[![12]][12]

In der folgenden Tabelle sind die Routen enthalten, die der VM des Fabrikam-Abonnements bekannt sind. Achten Sie in der Tabelle auf die Einträge vom Typ *Gateway für virtuelle Netzwerke*. Die VM sieht Routen beider lokalen Netzwerke.

[![13]][13]

>[!NOTE]
>Im Fabrikam- bzw. Contoso-Abonnement können Sie auch über Spoke-VNET-Verbindungen mit dem entsprechenden Hub-VNET verfügen. (In den Architekturdiagrammen dieses Artikels ist das Hub-and-Spoke-Modell nicht dargestellt.) Die Querverbindungen zwischen den Hub-VNET-Gateways und ExpressRoute ermöglicht auch die Kommunikation zwischen Hubs und Spokes im Osten und Westen.
>

## <a name="cross-connecting-on-premises-networks"></a>Herstellen von Querverbindungen zwischen lokalen Netzwerken

ExpressRoute Global Reach ermöglicht die Konnektivität zwischen lokalen Netzwerken, die mit unterschiedlichen ExpressRoute-Leitungen verbunden sind. Wir konfigurieren Global Reach zwischen den ExpressRoute-Leitungen von Contoso und Fabrikam. Da sich die ExpressRoute-Leitungen unter verschiedenen Abonnements befinden, müssen wir eine Autorisierung erstellen und verwenden. Der Artikel [Konfigurieren von ExpressRoute Global Reach][Configure Global Reach] enthält eine Schritt-für-Schritt-Anleitung.

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration von Global Reach dargestellt.

[![14]][14]

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Contoso Ltd. nach der Konfiguration von Global Reach. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden lokalen Netzwerken gehören. 

[![15]][15]

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Fabrikam Inc. nach der Konfiguration von Global Reach. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden lokalen Netzwerken gehören.

[![16]][16]

## <a name="next-steps"></a>Nächste Schritte

Antworten auf weitere Fragen zu VNETs und zum VNET-Peering finden Sie unter [FAQs zu virtuellen Netzwerken][VNet-FAQ]. Antworten auf weitere Fragen zu ExpressRoute und zur VNET-Konnektivität finden Sie unter [ExpressRoute – FAQ][ER-FAQ].

Global Reach wurde noch nicht in allen Ländern/Regionen eingeführt. Ob Global Reach in den gewünschten Ländern/Regionen verfügbar ist, erfahren Sie unter [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Das Anwendungsszenario"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "ExpressRoute-Routingtabelle vor der Übernahme (Contoso)"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "ExpressRoute-Routingtabelle vor der Übernahme (Fabrikam)"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "VM-Routen vor der Übernahme (Contoso)"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "VM-Routen vor der Übernahme (Fabrikam)"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Architektur nach dem VNET-Peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VM-Routen nach dem VNET-Peering (Contoso)"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VM-Routen nach dem VNET-Peering (Fabrikam)"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Architektur nach der ExpressRoute-Querverbindung"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "ExpressRoute-Routingtabelle nach Querverbindung von ExR und VNETs (Contoso)"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "ExpressRoute-Routingtabelle nach Querverbindung von ExR und VNETs (Fabrikam)"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "VM-Routen nach Querverbindung von ExR und VNETs (Contoso)"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "VM-Routen nach Querverbindung von ExR und VNETs (Fabrikam)"
[14]: ./media/cross-network-connectivity/globalreach.png "Architektur nach der Konfiguration von Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "ExpressRoute-Routingtabelle nach Global Reach (Contoso)"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "ExpressRoute-Routingtabelle nach Global Reach (Fabrikam)"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq