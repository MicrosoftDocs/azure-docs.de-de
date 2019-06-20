---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177958"
---
Diese häufig gestellten Fragen beziehen sich auf P2S-Verbindungen, die das klassische Bereitstellungsmodell verwenden.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Welche Clientbetriebssysteme kann ich bei Point-to-Site-Verbindungen verwenden?

Folgende Clientbetriebssysteme werden unterstützt:

* Windows 7 (32 Bit und 64 Bit)
* Windows Server 2008 R2 (nur 64 Bit)
* Windows 8 (32 Bit und 64 Bit)
* Windows 8.1 (32 Bit und 64 Bit)
* Windows Server 2012 (nur 64 Bit)
* Windows Server 2012 R2 (nur 64 Bit)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Kann ich für Point-to-Site-Verbindungen einen beliebigen VPN-Softwareclient mit SSTP-Unterstützung verwenden?

Nein. Nur die aufgelisteten Windows-Betriebssystemversionen werden unterstützt.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Wie viele VPN-Clientendpunkte kann meine Point-to-Site-Konfiguration umfassen?

Bis zu 128 VPN-Clients können gleichzeitig mit einem virtuellen Netzwerk verbunden sein.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kann ich für Punkt-zu-Standort-Verbindungen meine eigene interne PKI-Stammzertifizierungsstelle verwenden?

Ja. Bisher konnten nur selbstsignierte Stammzertifikate verwendet werden. Sie können immer noch bis zu 20 Stammzertifikate hochladen.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Können Proxys und Firewalls mit der Point-to-Site-Funktion durchlaufen werden?

Ja. Wir verwenden das Secure Socket Tunneling-Protokoll (SSTP), um eine Tunnelverbindung durch Firewalls zu ermöglichen. Dieser Tunnel wird als HTTPS-Verbindung angezeigt.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Wird die VPN-Verbindung eines für „Punkt zu Standort“ konfigurierten Clientcomputers nach einem Neustart automatisch wiederhergestellt?

Standardmäßig wird die VPN-Verbindung des Clientcomputers nicht automatisch wiederhergestellt.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Werden automatische Verbindungswiederherstellung und DDNS bei Point-to-Site-Verbindungen auf den VPN-Clients unterstützt?

Nein. Automatische Verbindungswiederherstellung und DDNS werden in Point-to-Site-VPNs derzeit nicht unterstützt.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Kann ich im gleichen virtuellen Netzwerk sowohl Site-to-Site- als auch Point-to-Site-Konfigurationen verwenden?

Ja. Beide Lösungen können verwendet werden, wenn Sie über einen routenbasierten VPN-Typ für Ihr Gateway verfügen. Für das klassische Bereitstellungsmodell benötigen Sie ein dynamisches Gateway. Point-to-Site-Konfigurationen werden für VPN-Gateways mit statischem Routing oder Gateways, die das **-VpnType PolicyBased**-Cmdlet verwenden, nicht unterstützt.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kann ich einen Punkt-zu-Standort-Client so konfigurieren, dass er gleichzeitig eine Verbindung mit mehreren virtuellen Netzwerken herstellt?

Ja. Allerdings dürfen sich weder die IP-Präfixe noch die Point-to-Site-Adressräume der virtuellen Netzwerke überschneiden.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Wie viel Durchsatz kann ich bei einer Standort-zu-Standort- oder bei einer Punkt-zu-Standort-Verbindung erwarten?

Der genaue Durchsatz der VPN-Tunnel lässt sich nur schwer ermitteln. IPsec und SSTP sind VPN-Protokolle mit hohem Kryptografieaufwand. Außerdem wird der Durchsatz durch die Latenz und die Bandbreite zwischen Ihrem Standort und dem Internet eingeschränkt.
