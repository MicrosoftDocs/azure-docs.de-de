---
title: Azure VPN Gateway | Microsoft-Dokumentation
description: Hier erfahren Sie, was ein VPN-Gateway ist und wie Sie damit eine Verbindung mit virtuellen Azure-Netzwerken herstellen können. Der Artikel geht unter anderem auf standortübergreifende IPsec/IKE-Site-to-Site- und -VNet-zu-VNet-Lösungen sowie auf Point-to-Site-VPNs ein.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 05/22/2019
ms.author: cherylmc
ms.openlocfilehash: 79697d44ea3e5126d43169f36c550046af3bc366
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170606"
---
# <a name="what-is-vpn-gateway"></a>Was ist VPN Gateway?

Ein VPN-Gateway ist eine spezielle Art von Gateway für virtuelle Netzwerke, das verwendet wird, um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet zu senden. Ein VPN-Gateway kann aber auch verwendet werden, um verschlüsselten Datenverkehr zwischen virtuellen Azure-Netzwerken über das Microsoft-Netzwerk zu senden. Ein virtuelles Netzwerk kann jeweils nur über ein einzelnes VPN-Gateway verfügen. Sie können jedoch mehrere Verbindungen mit dem gleichen VPN-Gateway herstellen. Wenn Sie mehrere Verbindungen mit dem gleichen VPN-Gateway herstellen, wird die für das Gateway zur Verfügung stehende Bandbreite auf alle VPN-Tunnel aufgeteilt.

## <a name="whatis"></a>Was ist ein Gateway für virtuelle Netzwerke?

Ein Gateway für virtuelle Netzwerke besteht aus mindestens zwei virtuellen Computern, die für ein spezielles von Ihnen erstelltes Subnetz bereitgestellt werden, das als *Gatewaysubnetz* bezeichnet wird. Die virtuellen Computer im Gatewaysubnetz werden erstellt, wenn Sie das Gateway für virtuelle Netzwerke erstellen. Virtuelle Computer im Gateway für virtuelle Netzwerke werden so konfiguriert, dass sie spezifische Routingtabellen und Gatewaydienste für das Gateway enthalten. Sie können die virtuellen Computer, die Teil des Gateways für virtuelle Netzwerke sind, nicht direkt konfigurieren, und im Gatewaysubnetz dürfen keine weiteren Ressourcen bereitgestellt werden.

Die Erstellung eines Gateways des virtuellen Netzwerks kann bis zu 45 Minuten dauern. Bei der Erstellung des Gateways des virtuellen Netzwerks werden Gateway-VMs für das Gatewaysubnetz bereitgestellt und mit den von Ihnen angegebenen Einstellungen konfiguriert. Eine der Einstellungen, die Sie konfigurieren, ist der Gatewaytyp. Der Gatewaytyp „VPN“ gibt an, dass es sich beim Typ des erstellten Gateways des virtuellen Netzwerks um ein VPN-Gateway handelt. Nach der Erstellung eines VPN-Gateways können Sie eine IPsec/IKE-VPN-Tunnelverbindung zwischen dem VPN-Gateway und einem anderen VPN-Gateway (VNet-to-VNet) oder eine standortübergreifende IPsec/IKE-VPN-Tunnelverbindung zwischen dem VPN-Gateway und einem lokalen VPN-Gerät ( Site-to-Site) erstellen. Sie können auch eine Point-to-Site-VPN-Verbindung (VPN über IKEv2 oder SSTP) erstellen und so an einem Remotestandort (beispielsweise auf einer Konferenz oder von zu Hause aus) eine Verbindung mit Ihrem virtuellen Netzwerk herstellen.

## <a name="configuring"></a>Konfigurieren von VPN Gateway

Eine VPN Gateway-Verbindung basiert auf mehreren, mit spezifischen Einstellungen konfigurierten Ressourcen. Die meisten der Ressourcen können separat konfiguriert werden. Bei manchen ist allerdings eine bestimmte Reihenfolge zu beachten.

### <a name="settings"></a>Einstellungen

Die Einstellungen, die Sie für die einzelnen Ressourcen auswählen, sind für eine erfolgreiche Verbindungserstellung entscheidend. Informationen zu einzelnen Ressourcen und Einstellungen für VPN Gateway finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md). Dieser Artikel enthält Informationen zu Gatewaytypen, Gateway-SKUs, VPN-Typen, Verbindungstypen, Gatewaysubnetzen, lokalen Netzwerkgateways und verschiedenen anderen Ressourceneinstellungen, die Sie ggf. berücksichtigen sollten.

### <a name="tools"></a>Bereitstellungstools

Sie können zunächst mit einem Konfigurationstool wie dem Azure-Portal Ressourcen erstellen und konfigurieren. Später können Sie mit einem anderen Tool (beispielsweise PowerShell) zusätzliche Ressourcen konfigurieren oder ggf. vorhandene Ressourcen ändern. Derzeit können nicht alle Ressourcen und Ressourceneinstellungen über das Azure-Portal konfiguriert werden. Sollte ein bestimmtes Konfigurationstool benötigt werden, ist dies in den Anleitungen der Artikel zu den einzelnen Verbindungstopologien angegeben. 

### <a name="models"></a>Bereitstellungsmodell

Für Azure stehen derzeit zwei Bereitstellungsmodelle zur Verfügung. Die Konfigurationsschritte für ein VPN-Gateway hängen davon ab, mit welchem Bereitstellungsmodell Sie das virtuelle Netzwerk erstellt haben. Wenn Sie Ihr VNET beispielsweise mit dem klassischen Bereitstellungsmodell erstellt haben, verwenden Sie die Richtlinien und Anleitungen für das klassische Bereitstellungsmodell, um die Einstellungen für das VPN Gateway zu erstellen und zu konfigurieren. Weitere Informationen zu Bereitstellungsmodellen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Planungstabelle

Die folgende Tabelle kann Ihnen dabei helfen, die beste Verbindungsoption für Ihre Lösung zu finden.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>Gateway-SKUs

Beim Erstellen eines Gateways des virtuellen Netzwerks geben Sie die gewünschte Gateway-SKU an. Wählen Sie die SKU aus, die Ihre Anforderungen im Bezug auf Workloadtypen, Durchsätze, Funktionen und SLAs erfüllt. Weitere Informationen zu Gateway-SKUs, einschließlich der unterstützten Funktionen, Produktions- und Dev-Tests sowie Konfigurationsschritten finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="benchmark"></a>Gateway-SKUs nach Tunnel, Verbindung und Durchsatz

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Diagramme zur Verbindungstopologie

Wichtig: Für VPN-Gateway-Verbindungen sind verschiedene Konfigurationen verfügbar. Sie müssen ermitteln, welche Konfiguration am besten zu Ihren Anforderungen passt. In den folgenden Abschnitten können Sie sich Informationen und Topologiediagramme zu den folgenden VPN-Gatewayverbindungen ansehen: Die folgenden Abschnitte enthalten Tabellen, in denen Folgendes aufgelistet ist:

* Verfügbares Bereitstellungsmodell
* Verfügbare Konfigurationstools
* Direkte Links zu Artikeln, sofern verfügbar

Orientieren Sie sich bei der Wahl einer geeigneten Verbindungstopologie an den Diagrammen und Beschreibungen. Die Diagramme zeigen die grundlegenden Topologien, aber Sie können auch komplexere Topologien erstellen, indem Sie die Diagramme als Anhaltspunkte verwenden.

## <a name="s2smulti"></a>Site-to-Site und Multi-Site (IPsec-/IKE-VPN-Tunnel)

### <a name="S2S"></a>Site-to-Site

Eine VPN Gateway-S2S-Verbindung (Site-to-Site) ist eine Verbindung über einen VPN-Tunnel vom Typ „IPsec/IKE“ (IKEv1 oder IKEv2). S2S-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden. Für S2S-Verbindungen wird ein lokales VPN-Gerät benötigt, dem eine öffentliche IP-Adresse zugewiesen ist und das sich nicht hinter einer NAT-Einheit befindet. Informationen zum Auswählen eines VPN-Geräts finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md#s2s).

![Beispiel für Site-to-Site-Verbindung per Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multi-Site

Bei dieser Art von Verbindung handelt es sich um eine Abwandlung der Site-to-Site-Verbindung. Sie erstellen mehrere VPN-Verbindung über Ihr Gateway für virtuelle Netzwerke, durch die in der Regel mehrere lokale Standorte verbunden werden. Bei Verwendung mehrerer Verbindungen müssen Sie den VPN-Typ „RouteBased“ verwenden (wird bei Verwendung klassischer VNets als dynamisches Gateway bezeichnet). Da jedes virtuelle Netzwerk nur über ein einzelnes VPN-Gateway verfügen kann, wird die verfügbare Bandbreite von allen über das Gateway laufenden Verbindungen gemeinsam genutzt. Diese Art von Verbindung wird häufig als Multi-Site-Verbindung bezeichnet.

![Beispiel für Multi-Site-Verbindung per Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Bereitstellungsmodelle und -methoden für Site-to-Site und Multi-Site

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Point-to-Site (VPN über IKEv2 oder SSTP)

Mit einer P2S-VPN-Gatewayverbindung (Point-to-Site) können Sie von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Diese Lösung ist nützlich für Telearbeiter, die an einem Remotestandort (beispielsweise zu Hause oder in einer Konferenz) eine Verbindung mit Azure-VNETs herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung.

Im Gegensatz zu S2S-Verbindungen ist für P2S-Verbindungen keine lokale öffentliche IP-Adresse bzw. kein VPN-Gerät erforderlich. P2S-Verbindungen können mit S2S-Verbindungen über das gleiche VPN-Gateway verwendet werden – vorausgesetzt, alle Konfigurationsanforderungen beider Verbindungen sind kompatibel. Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [About Point-to-Site VPN](point-to-site-about.md) (Informationen zu P2S-VPN).


![Beispiel für Point-to-Site-Verbindung per Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Bereitstellungsmodelle und -methoden für P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>VNet-zu-VNet-Verbindungen (IPsec-/IKE-VPN-Tunnel)

Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPsec/IKE bereitzustellen. Die VNet-zu-VNet-Kommunikation kann sogar mit Multi-Site-Verbindungskonfigurationen kombiniert werden. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren.

Die verbundenen VNets können wie folgt angeordnet sein:

* In derselben Region oder in verschiedenen Regionen
* In demselben Abonnement oder in verschiedenen Abonnements 
* In demselben Bereitstellungsmodell oder in verschiedenen Bereitstellungsmodellen

![Beispiel für VNet-zu-VNet-Verbindung per Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Verbindungen zwischen Bereitstellungsmodellen

In Azure sind zurzeit zwei Bereitstellungsmodelle verfügbar: klassisches Modell und Resource Manager-Modell. Wenn Sie Azure seit einiger Zeit verwenden, verfügen Sie wahrscheinlich über Azure-VMs und Instanzrollen, die in einem klassischen VNet ausgeführt werden. Ihre neueren VMs und Rolleninstanzen werden möglicherweise in einem in Resource Manager erstellten VNet ausgeführt. Sie können eine Verbindung zwischen den VNets erstellen, damit die Ressourcen in einem VNet direkt mit Ressourcen im anderen VNet kommunizieren können.

### <a name="vnet-peering"></a>VNet-Peering

Sofern Ihr virtuelles Netzwerk bestimmte Anforderungen erfüllt, können Sie Ihre Verbindung ggf. mithilfe von VNet-Peering erstellen. Beim VNet-Peering wird kein Gateway für das virtuelle Netzwerk verwendet. Weitere Informationen finden Sie unter [VNet-Peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Bereitstellungsmodelle und -methoden für VNet-zu-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (private Verbindung)

Mit ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Microsoft Cloud ausdehnen. Mit ExpressRoute können Sie Verbindungen mit Microsoft-Clouddiensten herstellen, z. B. Microsoft Azure, Office 365 und CRM Online. Die Konnektivität kann über ein Any-to-Any-Netzwerk (IP VPN), ein Point-to-Point-Ethernet-Netzwerk oder eine virtuelle Querverbindung über einen Konnektivitätsanbieter in einer Co-Location-Einrichtung bereitgestellt werden.

ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Auf diese Weise können ExpressRoute-Verbindungen eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet bieten.

Bei ExpressRoute-Verbindungen ist ein Gateway für virtuelle Netzwerke Teil der erforderlichen Konfiguration. Bei einer ExpressRoute-Verbindung wird das Gateway für virtuelle Netzwerke nicht mit dem Gatewaytyp „Vpn“, sondern mit „ExpressRoute“ konfiguriert. Datenverkehr, der über eine ExpressRoute-Verbindung übertragen wird, ist standardmäßig nicht verschlüsselt. Es besteht jedoch die Möglichkeit, eine Lösung zu erstellen, die es ermöglicht, verschlüsselten Datenverkehr über eine ExpressRoute-Verbindung zu senden. Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Parallel bestehende Site-to-Site- und ExpressRoute-Verbindungen

ExpressRoute ist eine direkte, private Verbindung aus Ihrem WAN mit Microsoft Services (einschließlich Azure), die nicht über das öffentliche Internet verläuft. Site-to-Site-VPN-Datenverkehr wird verschlüsselt über das öffentliche Internet gesendet. Die Möglichkeit, Site-to-Site-VPN- und ExpressRoute-Verbindungen für dasselbe virtuelle Netzwerk zu konfigurieren, hat mehrere Vorteile.

Sie können eine Site-to-Site-VPN-Verbindung als sicheren Failoverpfad für ExpressRoute konfigurieren oder für die Verbindung mit Websites nutzen, die nicht Teil Ihres Netzwerks, aber über ExpressRoute verbunden sind. Diese Konfiguration erfordert zwei Gateways für das gleiche virtuelle Netzwerk: eins mit dem Gatewaytyp „Vpn“ und eins mit dem Gatewaytyp „ExpressRoute“.

![Beispiel für gleichzeitig bestehende ExpressRoute- und VPN Gateway-Verbindungen](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Koexistenz von Bereitstellungsmodellen und -methoden für S2S und ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Preise

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Weitere Informationen zu Gateway-SKUs für VPN Gateway finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>Häufig gestellte Fragen

Häufig gestellte Fragen zu VPN-Gateways finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md).
- Sehen Sie sich die [Abonnements und Diensteinschränkungen](../azure-subscription-service-limits.md#networking-limits) an.
- Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) von Azure.
