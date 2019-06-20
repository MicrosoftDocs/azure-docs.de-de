---
title: 'Konfigurieren von OpenVPN in Azure VPN Gateway: PowerShell | Microsoft-Dokumentation'
description: Schritte zum Konfigurieren von OpenVPN für Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002965"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurieren von OpenVPN-Clients für Azure VPN Gateway für Point-to-Site

In diesem Artikel erfahren Sie, wie Sie **OpenVPN® Protocol** für Azure VPN Gateway einrichten. In diesem Artikel wird davon ausgegangen, dass Sie in einer Point-to-Site-Umgebung arbeiten. Falls nicht, befolgen Sie die Anweisungen in Schritt 1, um ein Point-to-Site-VPN zu erstellen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Erstellen eines Point-to-Site-VPN

Wenn Sie noch keine funktionsfähige Point-to-Site-Umgebung haben, folgen Sie der Anweisung zu deren Erstellung. Unter [Erstellen eines Point-to-Site-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) erfahren Sie, wie Sie ein Point-to-Site-VPN-Gateway mit nativer Azure-Zertifikatauthentifizierung erstellen und konfigurieren. 

> [!IMPORTANT]
> Die Basic-SKU wird für OpenVPN nicht unterstützt.

## <a name="enable"></a>2. Aktivieren von OpenVPN auf dem Gateway

Aktivieren Sie OpenVPN auf Ihrem Gateway. Vergewissern Sie sich, dass das Gateway bereits für Point-to-Site (IKEv2 oder SSTP) konfiguriert ist, bevor Sie die folgenden Befehle ausführen:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von OpenVPN finden Sie unter [Konfigurieren von OpenVPN-Clients](vpn-gateway-howto-openvpn-clients.md).

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
