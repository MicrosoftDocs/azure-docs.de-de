---
title: Informationen zu Azure VPN Gateway
description: Hier erfahren Sie, was VPN Gateway ist und wie Sie eine VPN Gateway-Instanz verwenden, um mit virtuellen Netzwerken eine Site-to-Site-, VNet-to-Vnet- oder Point-to-Site-Verbindung (IPsec/IKE) herzustellen.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperf-fy21q1
ms.openlocfilehash: c19e1156f897873defaba4cebaabb904d034ead3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032744"
---
# <a name="what-is-vpn-gateway"></a>Was ist VPN Gateway?

Ein VPN-Gateway ist eine spezielle Art von Gateway für virtuelle Netzwerke, das verwendet wird, um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet zu senden. Ein VPN-Gateway kann aber auch verwendet werden, um verschlüsselten Datenverkehr zwischen virtuellen Azure-Netzwerken über das Microsoft-Netzwerk zu senden. Ein virtuelles Netzwerk kann jeweils nur über ein einzelnes VPN-Gateway verfügen. Sie können jedoch mehrere Verbindungen mit dem gleichen VPN-Gateway herstellen. Wenn Sie mehrere Verbindungen mit dem gleichen VPN-Gateway herstellen, wird die für das Gateway zur Verfügung stehende Bandbreite auf alle VPN-Tunnel aufgeteilt.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Was ist ein Gateway für virtuelle Netzwerke?

Ein Gateway für virtuelle Netzwerke besteht aus mindestens zwei VMs, die in einem von Ihnen erstellten speziellen Subnetz namens *Gatewaysubnetz* bereitgestellt werden. VMs für das Gateway für virtuelle Netzwerke enthalten Routingtabellen und führen bestimmte Gatewaydienste aus. Diese VMs werden beim Erstellen des Gateways für virtuelle Netzwerke erstellt. Sie können die VMs, die Teil des Gateways für virtuelle Netzwerke sind, nicht direkt konfigurieren.

Wenn Sie ein virtuelles Netzwerkgateway konfigurieren, konfigurieren Sie eine Einstellung, die den Gatewaytyp angibt. Der Gatewaytyp bestimmt, wie das Gateway für virtuelle Netzwerke verwendet wird und welche Aktionen es ausführt. Der Gatewaytyp „VPN“ gibt an, dass es sich beim Typ des erstellten Gateways des virtuellen Netzwerks um ein VPN-Gateway handelt. Dadurch unterscheidet es sich von einem ExpressRoute-Gateway, das einen anderen Gatewaytyp verwendet. Ein virtuelles Netzwerk kann zwei virtuelle Netzwerkgateways besitzen, ein VPN-Gateway und ein ExpressRoute-Gateway. Weitere Informationen finden Sie unter [Gatewaytypen](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

Die Erstellung eines Gateways des virtuellen Netzwerks kann bis zu 45 Minuten dauern. Bei der Erstellung des Gateways des virtuellen Netzwerks werden Gateway-VMs für das Gatewaysubnetz bereitgestellt und mit den von Ihnen angegebenen Einstellungen konfiguriert. Nach der Erstellung eines VPN-Gateways können Sie eine IPsec/IKE-VPN-Tunnelverbindung zwischen dem VPN-Gateway und einem anderen VPN-Gateway (VNet-to-VNet) oder eine standortübergreifende IPsec/IKE-VPN-Tunnelverbindung zwischen dem VPN-Gateway und einem lokalen VPN-Gerät ( Site-to-Site) erstellen. Sie können auch eine Point-to-Site-VPN-Verbindung (VPN über OpenVPN, IKEv2 oder SSTP) erstellen und so von einem Remotestandort aus (beispielsweise in einer Konferenz oder zu Hause) eine Verbindung mit Ihrem virtuellen Netzwerk herstellen.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Konfigurieren von VPN Gateway

Eine VPN Gateway-Verbindung basiert auf mehreren, mit spezifischen Einstellungen konfigurierten Ressourcen. Die meisten der Ressourcen können separat konfiguriert werden. Bei manchen ist allerdings eine bestimmte Reihenfolge zu beachten.

### <a name="design"></a><a name="diagrams"></a>Entwurf

Wichtig: Für VPN-Gateway-Verbindungen sind verschiedene Konfigurationen verfügbar. Sie müssen ermitteln, welche Konfiguration am besten zu Ihren Anforderungen passt. Point-to-Site, Site-to-Site und parallel bestehende ExpressRoute-/Site-to-Site-Verbindungen weisen z. B. alle unterschiedliche Anweisungen und Konfigurationsanforderungen auf. Informationen zum Entwurf und zum Anzeigen von Verbindungstopologiediagrammen finden Sie unter [Entwurf](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Planungstabelle

Die folgende Tabelle kann Ihnen dabei helfen, die beste Verbindungsoption für Ihre Lösung zu finden.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Einstellungen

Die Einstellungen, die Sie für die einzelnen Ressourcen auswählen, sind für eine erfolgreiche Verbindungserstellung entscheidend. Informationen zu einzelnen Ressourcen und Einstellungen für VPN Gateway finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md). Dieser Artikel enthält Informationen zu Gatewaytypen, Gateway-SKUs, VPN-Typen, Verbindungstypen, Gatewaysubnetzen, lokalen Netzwerkgateways und verschiedenen anderen Ressourceneinstellungen, die Sie ggf. berücksichtigen sollten.

### <a name="deployment-tools"></a><a name="tools"></a>Bereitstellungstools

Sie können zunächst mit einem Konfigurationstool wie dem Azure-Portal Ressourcen erstellen und konfigurieren. Später können Sie mit einem anderen Tool (beispielsweise PowerShell) zusätzliche Ressourcen konfigurieren oder ggf. vorhandene Ressourcen ändern. Derzeit können nicht alle Ressourcen und Ressourceneinstellungen über das Azure-Portal konfiguriert werden. Sollte ein bestimmtes Konfigurationstool benötigt werden, ist dies in den Anleitungen der Artikel zu den einzelnen Verbindungstopologien angegeben.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKUs

Beim Erstellen eines Gateways des virtuellen Netzwerks geben Sie die gewünschte Gateway-SKU an. Wählen Sie die SKU aus, die Ihre Anforderungen im Bezug auf Workloadtypen, Durchsätze, Funktionen und SLAs erfüllt.

* Weitere Informationen zu Gateway-SKUs (einschließlich unterstützter Funktionen, Produktions- und Dev-Test-Workloads sowie Konfigurationsschritte) finden Sie im Artikel „Informationen zu VPN Gateway-Einstellungen“ unter [Gateway-SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
* Informationen zu Legacy-SKUs finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (Legacy-SKUs)](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKUs nach Tunnel, Verbindung und Durchsatz

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Verfügbarkeitszonen

VPN-Gateways können in Azure-Verfügbarkeitszonen bereitgestellt werden. So erzielen Sie Stabilität, Skalierbarkeit und eine höhere Verfügbarkeit für die Gateways des virtuellen Netzwerks. Durch die Bereitstellung von Gateways in Azure-Verfügbarkeitszonen werden die Gateways innerhalb einer Region physisch und logisch getrennt. Gleichzeitig wird die Konnektivität Ihres lokalen Netzwerks mit Azure vor Ausfällen auf Zonenebene geschützt. Unter [Informationen zu zonenredundanten Gateways für das virtuelle Netzwerk in Azure-Verfügbarkeitszonen](about-zone-redundant-vnet-gateways.md) erfahren Sie mehr dazu.

## <a name="pricing"></a><a name="pricing"></a>Preise

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Weitere Informationen zu Gateway-SKUs für VPN Gateway finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

Häufig gestellte Fragen zu VPN-Gateways finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Neuerungen

Abonnieren Sie den RSS-Feed, und zeigen Sie die neuesten VPN Gateway-Featureupdates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) an.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md).
- Sehen Sie sich die [Abonnements und Diensteinschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) an.
- Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) von Azure.
