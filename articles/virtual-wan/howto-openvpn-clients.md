---
title: Konfigurieren von OpenVPN-Clients für Azure Virtual WAN
description: Schritte zum Konfigurieren von OpenVPN-Clients für Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491000"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurieren eines OpenVPN-Clients für Azure Virtual WAN

Dieser Artikel hilft Ihnen beim Konfigurieren von Clients mit dem **OpenVPN**-Protokoll&reg;. Sie können auch den Azure VPN Client für Windows 10 verwenden, um eine Verbindung über das OpenVPN-Protokoll herzustellen. Weitere Anweisungen finden Sie [hier](openvpn-azure-ad-client.md).

## <a name="before-you-begin"></a>Voraussetzungen

Erstellen Sie eine Benutzer-VPN-Konfiguration (Point-to-Site). Stellen Sie sicher, dass Sie den Tunneltyp „OpenVPN“ auswählen. Die einzelnen Schritte finden Sie unter [Erstellen einer P2S-Konfiguration für Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Benutzer-VPN (Point-to-Site) finden Sie unter [Erstellen von Benutzer-VPN-Verbindungen](virtual-wan-point-to-site-portal.md).

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
