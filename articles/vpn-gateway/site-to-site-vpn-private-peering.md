---
title: Site-to-Site-VPN-Verbindungen über privates ExpressRoute-Peering
description: Dieser Artikel hilft Ihnen, Site-to-Site-VPN über privates ExpressRoute-Peering zu aktivieren, um Datenverkehr zu verschlüsseln.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 62ceafad0210065700e5c9734cfe9a055208ef35
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657889"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Konfigurieren einer Site-to-Site-VPN-Verbindung über privates ExpressRoute-Peering

Sie können ein Site-to-Site-VPN über ein privates ExpressRoute-Peering mithilfe einer RFC 1918-IP-Adresse zu einem virtuellen Netzwerkgateway konfigurieren. Diese Konfiguration bietet die folgenden Vorteile:

* Datenverkehr über privates Peering wird verschlüsselt.

* Point-to-Site-Benutzer, die sich mit einem virtuellen Netzwerkgateway verbinden, können mithilfe von ExpressRoute (über den Site-to-Site-Tunnel) auf lokale Ressourcen zugreifen.

>[!NOTE]
>Dieses Feature wird nur bei zonenredundanten Gateways unterstützt. Beispiel: VpnGw1AZ, VpnGw2AZ usw.
>

Vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind, um diese Konfiguration abzuschließen:

* Sie verfügen über eine funktionierende ExpressRoute-Leitung, die mit dem VNet verbunden ist, in dem das VPN-Gateway erstellt wird.

* Sie können Ressourcen über eine (private) RFC1918-IP-Adresse im virtuellen Netzwerk über die ExpressRoute-Leitung erreichen.

## <a name="routing"></a><a name="routing"></a>Routing

**Abbildung 1** zeigt ein Beispiel für eine VPN-Verbindung über privates ExpressRoute-Peering. In diesem Beispiel sehen Sie ein Netzwerk innerhalb des lokalen Netzwerks, das über privates ExpressRoute-Peering mit dem VPN-Gateway des Azure-Hubs verbunden ist. Ein wichtiger Aspekt dieser Konfiguration ist das Routing zwischen den lokalen Netzwerken und Azure über die ExpressRoute- und VPN-Pfade.

Abbildung 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Abbildung 1":::

Das Einrichten der Konnektivität ist unkompliziert:

1. Richten Sie eine ExpressRoute-Verbindung mit einer ExpressRoute-Leitung und privatem Peering ein.

1. Richten Sie die VPN-Konnektivität mithilfe der Schritte in diesem Artikel ein.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Datenverkehr von lokalen Netzwerken zu Azure

Für den Datenverkehr von lokalen Netzwerken zu Azure werden die Azure-Präfixe sowohl über das private ExpressRoute-Peering-BGP als auch über das VPN-BGP angekündigt. Dies führt zu zwei Netzwerkrouten (Pfaden) zu Azure aus den lokalen Netzwerken:

• Eine Netzwerkroute über den durch IPSec geschützten Pfad.

• Eine direkte Netzwerkroute über ExpressRoute ohne IPSec-Schutz.

Um die Kommunikation zu verschlüsseln, muss für das VPN-verbundene Netzwerk in **Abbildung 1** sichergestellt werden, dass die Azure-Routen über das lokale VPN-Gateway dem direkten ExpressRoute-Pfad gegenüber bevorzugt werden.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Datenverkehr von Azure zu lokalen Netzwerken

Die gleiche Anforderung gilt für den Datenverkehr von Azure zu lokalen Netzwerken. Damit der IPsec-Pfad dem direkten ExpressRoute-Pfad (ohne IPsec) gegenüber bevorzugt wird, haben Sie zwei Möglichkeiten:

• **Kündigen Sie in der VPN-BGP-Sitzung für das VPN-verbundene Netzwerk spezifischere Präfixe an**. Sie können einen größeren Bereich, der das VPN-verbundene Netzwerk über privates ExpressRoute-Peering umfasst, und dann spezifischere Bereiche in der VPN-BGP-Sitzung ankündigen. Kündigen Sie z. B. 10.0.0.0/16 über ExpressRoute und 10.0.1.0/24 über das VPN an.

• **Kündigen Sie getrennte Präfixe für VPN und ExpressRoute an**. Wenn die Bereiche für das VPN-verbundene Netzwerk von anderen ExpressRoute-verbundenen Netzwerken getrennt sind, können Sie die Präfixe in den VPN- bzw. ExpressRoute-BGP-Sitzungen ankündigen. Kündigen Sie z. B. 10.0.0.0/24 über ExpressRoute und 10.0.1.0/24 über das VPN an.

In diesen beiden Beispielen sendet Azure den Datenverkehr über die VPN-Verbindung und nicht ohne VPN-Schutz direkt über ExpressRoute an 10.0.1.0/24.

>[!Warning]
>Wenn Sie die gleichen Präfixe über ExpressRoute- und VPN-Verbindungen ankündigen, verwendet Azure den ExpressRoute-Pfad direkt ohne VPN-Schutz.
>

## <a name="portal-steps"></a><a name="portal"></a>Portalschritte

1. Konfigurieren Sie eine Site-to-Site-Verbindung. Informationen zu den Schritten finden Sie im Artikel [Site-to-Site-Konfiguration](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Achten Sie darauf, eine zonenredundante Gateway-SKU für das Gateway auszuwählen. 

   Zonenredundante SKUs enthalten „AZ“ am Ende der SKUs. Zum Beispiel **VpnGw1AZ**. Zonenredundante Gateways sind nur in Regionen verfügbar, in denen der Verfügbarkeitszonendienst verfügbar ist. Informationen über die Regionen, in denen Verfügbarkeitszonen unterstützt werden, finden Sie unter [Regionen, die Verfügbarkeitszonen in Azure unterstützen](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Private Gateway-IP-Adressen":::
1. Aktivieren Sie private IP-Adressen für das Gateway. Wählen Sie **Konfiguration** aus, und legen Sie dann **Private Gateway-IP-Adressen** auf **Aktiviert** fest. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.
1. Wählen Sie auf der Seite **Übersicht** die Option **Mehr anzeigen**, um die private IP-Adresse anzuzeigen. Notieren Sie sich diese Informationen zur späteren Verwendung in den Konfigurationsschritten.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Seite „Übersicht“" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Wählen Sie **Konfiguration** aus, um **Private Azure-IP-Adresse verwenden** für die Verbindung zu aktivieren. Legen Sie **Private Azure-IP-Adresse verwenden** auf **Aktiviert** fest, und wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Private Gateway-IP-Adressen – Aktiviert":::
1. Pingen Sie von Ihrer Firewall aus die private IP-Adresse an, die Sie in Schritt 3 notiert haben. Die private IP-Adresse sollte über das private ExpressRoute-Peering erreichbar sein.
1. Verwenden Sie diese private IP-Adresse als Remote-IP-Adresse für Ihre lokale Firewall, um den Site-to-Site-Tunnel über das private ExpressRoute-Peering einzurichten.

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell-Schritte

1. Konfigurieren Sie eine Site-to-Site-Verbindung. Informationen zu den Schritten finden Sie im Artikel [Konfigurieren eines Site-to-Site-VPNs](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Achten Sie darauf, eine zonenredundante Gateway-SKU für das Gateway auszuwählen. Zonenredundante SKUs enthalten „AZ“ am Ende der SKUs. Beispiel: VpnGw1AZ.
1. Legen Sie das Flag zur Verwendung der privaten IP-Adresse auf dem Gateway mithilfe der folgenden PowerShell-Befehle fest:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Es sollte eine öffentliche und eine private IP-Adresse angezeigt werden. Notieren Sie die IP-Adresse unter dem Abschnitt „TunnelIpAddresses“ der Ausgabe. Diese Information wird in einem späteren Schritt verwendet.
1. Legen Sie die Verbindung zur Verwendung der privaten IP-Adresse mithilfe des folgenden PowerShell-Befehls fest:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Pingen Sie von Ihrer Firewall aus die private IP-Adresse an, die Sie in Schritt 2 notiert haben. Sie sollte über das private ExpressRoute-Peering erreichbar sein.
1. Verwenden Sie diese private IP-Adresse als Remote-IP-Adresse für Ihre lokale Firewall, um den Site-to-Site-Tunnel über das private ExpressRoute-Peering einzurichten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VPN Gateways finden Sie unter [Was ist ein VPN Gateway?](vpn-gateway-about-vpngateways.md)
