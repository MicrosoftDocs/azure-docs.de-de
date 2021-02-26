---
title: 'Azure Virtual WAN: Benutzer-VPN-Clientprofile'
description: Diese Informationen unterstützen Sie bei der Arbeit mit der Clientprofildatei.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980914"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Arbeiten mit Benutzer-VPN-Clientprofildateien

Die Profildateien enthalten Informationen, die zum Konfigurieren einer VPN-Verbindung erforderlich sind. Dieser Artikel hilft Ihnen beim Abrufen und Verstehen der Informationen, die für ein Benutzer-VPN-Clientprofil erforderlich sind.

## <a name="download-the-profile"></a>Herunterladen des Profils

Sie können die Schritte im Artikel [Herunterladen eines globalen oder hubbasierten Profils für Benutzer-VPN-Clients](global-hub-profile.md) verwenden, um die ZIP-Datei für das Clientprofil herunterzuladen.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Der Ordner **OpenVPN** enthält das Profil *ovpn*, das geändert werden muss, um den Schlüssel und das Zertifikat einzuschließen. Weitere Informationen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Virtual WAN-Benutzer-VPN finden Sie unter [Tutorial: Erstellen einer Benutzer-VPN-Verbindung per Azure Virtual WAN](virtual-wan-point-to-site-portal.md).
