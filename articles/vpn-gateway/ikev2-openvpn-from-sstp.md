---
title: Übergang von SSTP zu OpenVPN oder IKEv2 | Azure-VPN-Gateway
description: In diesem Artikel erfahren Sie, wie Sie das Limit von 128 gleichzeitigen Verbindungen für SSTP überwinden.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 59aa6f5560917651d8f60c667145b0953bf72ef5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660864"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Übergang von SSTP zum OpenVPN-Protokoll oder zu IKEv2

Mit einer P2S-VPN-Gatewayverbindung (Point-to-Site) können Sie von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Dieser Artikel bezieht sich auf das Resource Manager-Bereitstellungsmodell und erläutert Möglichkeiten, das Limit von 128 gleichzeitigen Verbindungen für SSTP durch den Wechseln zum OpenVPN-Protokoll oder zu IKEv2 aufzuheben.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welches Protokoll verwendet P2S?

P2S-VPN kann eins der folgenden Protokolle verwenden:

* **OpenVPN&reg;-Protokoll**, ein auf SSL/TLS basierendes VPN-Protokoll. Eine SSL-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von SSL verwendeten TCP-Port 443 für ausgehenden Datenverkehr öffnen. OpenVPN kann zum Herstellen einer Verbindung von Android-, iOS- (Versionen 11.0 und höher), Windows-, Linux- und Mac-Geräten (OSX-Version 10.13 und höher) verwendet werden.

* **Secure Socket Tunneling-Protokoll (SSTP)** , ein proprietäres SSL-basiertes VPN-Protokoll. Eine SSL-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von SSL verwendeten TCP-Port 443 für ausgehenden Datenverkehr öffnen. SSTP wird nur auf Windows-Geräten unterstützt. Azure unterstützt alle Versionen von Windows, die über SSTP verfügen (Windows 7 und höher). **SSTP unterstützt unabhängig von der Gateway-SKU nur bis zu 128 gleichzeitige Verbindungen**.

* IKEv2-VPN, eine standardbasierte IPsec-VPN-Lösung. IKEv2-VPN kann zum Herstellen einer Verbindung von Mac-Geräten (OSX-Version 10.11 und höher) verwendet werden.


>[!NOTE]
>IKEv2 und OpenVPN für P2S sind ausschließlich für das Resource Manager-Bereitstellungsmodell verfügbar. Für das klassische Bereitstellungsmodell sind sie nicht verfügbar. Die Basic-Gateway-SKU unterstützt die Protokolle IKEv2 und OpenVPN nicht. Wenn Sie die Basic-SKU verwenden, müssen Sie das Gateway löschen und ein neues Gateway für ein virtuelles Netzwerk mit Produktions-SKU erstellen.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrieren von SSTP zu IKEv2 oder OpenVPN

In einigen Fällen möchten Sie mehr als 128 gleichzeitige P2S-Verbindungen mit einem VPN-Gateway unterstützen, verwenden jedoch SSTP. In einem solchen Fall müssen Sie zu einem der Protokolle IKEv2 oder OpenVPN wechseln.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Option 1: Hinzufügen von IKEv2 als Ergänzung zu SSTP auf dem Gateway

Dies ist die einfachste Möglichkeit. SSTP und IKEv2 können gleichzeitig auf demselben Gateway vorhanden sein und eine größere Anzahl gleichzeitiger Verbindungen ermöglichen. Sie können IKEv2 einfach auf dem vorhandenen Gateway aktivieren und den Client erneut herunterladen.

Das Hinzufügen von IKEv2 zu einem vorhandenen SSTP-VPN-Gateway hat keine Auswirkungen auf vorhandene Clients, und Sie können sie für die Verwendung von IKEv2 in kleinen Batches konfigurieren oder nur die neuen Clients für die Verwendung von IKEv2 konfigurieren. Wenn ein Windows-Client sowohl für SSTP als auch für IKEv2 konfiguriert ist, wird zunächst versucht, eine Verbindung über IKEv2 herzustellen. Wenn dies nicht funktioniert, wird auf SSTP zurückgegriffen.

**IKEv2 verwendet nicht die UDP-Standardports. Daher müssen Sie sicherstellen, dass diese Ports nicht durch die Firewall des Benutzers blockiert werden. Die verwendeten Ports sind UDP 500 und 4500.**

Um einem vorhandenen Gateway IKEv2 hinzuzufügen, wechseln Sie einfach unter dem Gateway des virtuellen Netzwerks im Portal zur Registerkarte „Point-to-Site-Konfiguration“, und wählen Sie im Dropdownfeld **IKEv2 und SSTP (SSL)** aus.

![Screenshot der Seite „Point-to-Site Konfiguration“ mit geöffneter Dropdownliste „Tunneltyp“ und ausgewählter Option „IKEv2 und SSTP(SSL)“.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Option 2: Entfernen von SSTP und Aktivieren von OpenVPN auf dem Gateway

Da sowohl SSTP als auch OpenVPN TLS-basierte Protokolle sind, können sie nicht auf demselben Gateway gleichzeitig vorhanden sein. Wenn Sie von SSTP zu OpenVPN wechseln möchten, müssen Sie SSTP deaktivieren und OpenVPN auf dem Gateway aktivieren. Dieser Vorgang bewirkt, dass die vorhandenen Clients ihre Verbindung mit dem VPN-Gateway verlieren, bis das neue Profil auf dem Client konfiguriert wurde.

Wenn Sie möchten, können Sie OpenVPN neben IKEv2 aktivieren. OpenVPN ist TLS-basiert und verwendet den TCP-Standardport 443. Um zu OpenVPN zu wechseln, navigieren Sie unter dem Gateway des virtuellen Netzwerks im Portal zur Registerkarte „Point-to-Site-Konfiguration“, und wählen Sie im Dropdownfeld **OpenVPN (SSL)** oder **IKEv2 und OpenVPN (SSL)** aus.

![point-to-site](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Nachdem das Gateway konfiguriert wurde, können vorhandene Clients erst wieder eine Verbindung herstellen, wenn Sie [die OpenVPN-Clients bereitstellen und konfigurieren](./vpn-gateway-howto-openvpn-clients.md).

Wenn Sie Windows 10 verwenden, können Sie auch [Azure VPN Client für Windows](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client) verwenden.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
### <a name="what-are-the-client-configuration-requirements"></a>Welche Anforderungen an die Clientkonfiguration müssen erfüllt sein?

>[!NOTE]
>Bei Windows-Clients müssen Sie über Administratorrechte auf dem Clientgerät verfügen, um die VPN-Verbindung von dem Clientgerät zu Azure zu initiieren.
>

Benutzer verwenden die nativen VPN-Clients auf Windows- und Mac-Geräten für P2S. Azure bietet eine ZIP-Datei mit der VPN-Clientkonfiguration, die Einstellungen enthält, die diese nativen Clients zum Herstellen einer Verbindung mit Azure benötigen.

* Bei Windows-Geräten besteht die VPN-Clientkonfiguration aus einem Installer-Paket, das Benutzer auf ihren Geräten installieren.
* Bei Mac-Geräten besteht sie aus der Datei „mobileconfig“, die Benutzer auf ihren Geräten installieren.

Die ZIP-Datei enthält zudem die Werte einiger wichtiger Einstellungen in Azure, mit denen Sie ein eigenes Profil für diese Geräte erstellen können. Zu den Werten zählen die VPN-Gatewayadresse, konfigurierte Tunneltypen, Routen und das Stammzertifikat für die Gatewayüberprüfung.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welche Gateway-SKUs unterstützen P2S-VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Gateway-SKU-Empfehlungen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welche IKE/IPsec-Richtlinien werden in VPN-Gateways für P2S konfiguriert?


**IKEv2**

|**Verschlüsselung** | **Integrität** | **PRF** | **DH-Gruppe** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Verschlüsselung** | **Integrität** | **PFS-Gruppe** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welche TLS-Richtlinien werden in VPN-Gateways für P2S konfiguriert?
**TLS**

|**Richtlinien** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Wie konfiguriere ich eine P2S-Verbindung?

Eine P2S-Konfiguration erfordert einige bestimmte Schritte. Die folgenden Artikel enthalten die Schritte, anhand derer Sie eine P2S-Konfiguration durchführen können, und Links zur Konfiguration der VPN-Clientgeräte:

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der RADIUS-Authentifizierung](point-to-site-how-to-radius-ps.md)

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der nativen Azure-Zertifikatauthentifizierung](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurieren von OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der RADIUS-Authentifizierung](point-to-site-how-to-radius-ps.md)

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der nativen Azure-Zertifikatauthentifizierung](vpn-gateway-howto-point-to-site-rm-ps.md)

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**