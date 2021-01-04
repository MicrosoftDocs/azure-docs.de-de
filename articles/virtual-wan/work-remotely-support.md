---
title: Azure Virtual WAN und Remotearbeit
description: Auf dieser Seite wird beschrieben, wie Sie Azure Virtual WAN nutzen können, um Remotearbeit aufgrund der COVID-19-Pandemie zu ermöglichen.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023488"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN und Unterstützung von Remotearbeit

>[!NOTE]
>In diesem Artikel wird beschrieben, wie Sie Azure Virtual WAN, Azure, das Microsoft-Netzwerk und das Azure-Partnerökosystem nutzen können, um remote zu arbeiten und Netzwerkprobleme zu beheben, die Sie aufgrund der COVID-19-Krise bewältigen müssen.
>

Möchten Sie schnell Konnektivität für Remotebenutzer bereitstellen?
Sehen Sie plötzlich eine Notwendigkeit, viel mehr als die geplante Anzahl von Benutzern zu unterstützen?
Muss der Benutzer von zu Hause aus eine Verbindung herstellen und nicht nur auf die Cloud zugreifen, sondern auch lokale Einrichtungen erreichen können?
Müssen die Remotebenutzer Ressourcen hinter einem privaten WAN erreichen können?
Müssen die Benutzer auf Intra-Cloudressourcen zugreifen, ohne dass eine Konnektivität zwischen Regionen eingerichtet werden muss?
Da diese globale Pandemie zu noch nie dagewesenen Änderungen in unserem Umfeld führt, steht Ihnen das Azure Virtual WAN-Team zur Verfügung, um Ihre Konnektivitätsanforderungen zu erfüllen.

Azure Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Diese Funktionen umfassen Branchverbindungen (über die Konnektivitätsautomatisierung von Virtual WAN-Partnereinrichtungen wie SD-WAN oder VPN CPE), Site-to-Site-VPN-Konnektivität, Remotebenutzer-VPN (Point-to-Site), private Konnektivität (ExpressRoute), Intra-Cloudkonnektivität (transitive Konnektivität für virtuelle Netzwerke), VPN ExpressRoute-Interkonnektivität, Routing, Azure Firewall, Verschlüsselung für private Konnektivität usw. Sie müssen nicht all diese Anwendungsfälle abdecken, um Virtual WAN nutzen zu können. Sie können mit nur einem Anwendungsfall loslegen und das Netzwerk abhängig von der weiteren Entwicklung anpassen.

![Virtual WAN-Diagramm](./media/virtual-wan-about/virtualwan1.png)

Lassen Sie uns in Bezug auf Remotebenutzer sehen, was Sie brauchen, um das Netzwerk einzurichten und zu nutzen:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Einrichten der Remotebenutzerkonnektivität

Sie können sich über eine IPsec/IKE (IKEv2)- oder OpenVPN-Verbindung mit Ihren Ressourcen in Azure verbinden. Bei einer derartigen Verbindung muss für den Remotebenutzer ein VPN-Client konfiguriert werden. Bei diesem Client kann es sich um den [Azure-VPN-Client](https://go.microsoft.com/fwlink/?linkid=2117554), einen OpenVPN-Client oder einen beliebigen Client handeln, der IKEv2 unterstützt. Weitere Informationen finden Sie unter [Herstellen einer Point-to-Site-Verbindung](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Konnektivität vom Remotebenutzer zu lokalen Einrichtungen

Hier haben Sie zwei Möglichkeiten:

* Richten Sie eine Site-to-Site-Konnektivität mit einem beliebigen vorhandenen VPN-Gerät ein. Wenn Sie das IPSec-VPN-Gerät mit dem Azure Virtual WAN-Hub verbinden, besteht automatisch Konnektivität zwischen dem Point-to-Site-Benutzer-VPN (Remotebenutzer) und dem Site-to-Site-VPN. Weitere Informationen zum Einrichten eines Site-to-Site-VPN von Ihrem lokalen VPN-Gerät zu Azure Virtual WAN finden Sie unter [Erstellen einer Site-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

* Verbinden Sie Ihre ExpressRoute-Leitung mit dem Virtual WAN-Hub. Zum Herstellen einer Verbindung zu einer ExpressRoute-Leitung muss ein ExpressRoute-Gateway in Virtual WAN bereitgestellt werden. Nach der Bereitstellung besteht automatisch eine Konnektivität zwischen dem Point-to-Site-Benutzer-VPN und dem ExpressRoute-Benutzer. Informationen zum Erstellen der ExpressRoute-Verbindung finden Sie unter [Erstellen einer ExpressRoute-Verbindung per Virtual WAN](virtual-wan-expressroute-portal.md). Sie können eine vorhandene ExpressRoute-Leitung verwenden, um eine Verbindung mit Azure Virtual WAN herzustellen.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Vorhandener Basic Virtual WAN-Kunde

Basic Virtual WAN unterstützt nur Site-to-Site-VPN. Damit Remotebenutzer eine Verbindung herstellen können, müssen Sie ein Virtual WAN-Upgrade auf Standard Virtual WAN durchführen. Anweisungen für ein Upgrade eines virtuellen WAN finden Sie unter [Upgrade eines virtuellen WAN von Basic auf Standard](upgrade-virtual-wan.md).

## <a name="additional-information"></a><a name="other considerations"></a>Weitere Informationen

Virtual WAN unterstützt einen Hub pro Region/Standort. Informationen zu Standorten finden Sie in dem Artikel [Virtual WAN-Partner und -Standorte](virtual-wan-locations-partners.md). Jeder Hub unterstützt bis zu 10.000 Remotebenutzerverbindungen, 1.000 Branchverbindungen, vier ExpressRoute-Leitungen und bis zu 500 VNet-Verbindungen. Wenn Sie die Remotebenutzer zentral hochskalieren, sollten Sie bei Fragen nicht zögern, Hilfe anzufordern, indem Sie eine E-Mail an azurevirtualwan@microsoft.com senden. Öffnen Sie ein Supportticket im Azure-Portal, wenn Sie technischen Support benötigen, um schnell Hilfe zu erhalten.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie in der [Übersicht über Virtual WAN](virtual-wan-about.md).