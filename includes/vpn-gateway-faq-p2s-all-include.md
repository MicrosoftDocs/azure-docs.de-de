---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 20f0d6a88fead155225cff69ab89635de587a46d
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455164"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Wie viele VPN-Clientendpunkte kann meine Punkt-zu-Standort-Konfiguration umfassen?

Das hängt von der Gateway-SKU ab. Weitere Informationen zur Anzahl von unterstützten Verbindungen finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="supportedclientos"></a>Welche Clientbetriebssysteme kann ich bei Point-to-Site-Verbindungen verwenden?

Folgende Clientbetriebssysteme werden unterstützt:

* Windows 7 (32 Bit und 64 Bit)
* Windows Server 2008 R2 (nur 64 Bit)
* Windows 8.1 (32 Bit und 64 Bit)
* Windows Server 2012 (nur 64 Bit)
* Windows Server 2012 R2 (nur 64 Bit)
* Windows Server 2016 (nur 64 Bit)
* Windows 10
* Mac OS X-Version 10.11 oder höher
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Können Proxys und Firewalls mit der Punkt-zu-Standort-Funktion durchlaufen werden?

Azure unterstützt drei Arten von P2S-VPN-Optionen:

* Secure Sockets Tunneling Protocol (SSTP). SSTP ist eine Microsoft-eigene SSL-basierte Lösung, die Firewalls durchdringen kann, da die meisten Firewalls den von SSL verwendeten TCP-Port 443 öffnen.

* OpenVPN. OpenVPN ist eine SSL-basierte Lösung, die Firewalls durchdringen kann, weil die meisten Firewalls den von SSL verwendeten TCP-Port 443 öffnen.

* IKEv2-VPN. IKEv2-VPN ist eine standardbasierte IPsec-VPN-Lösung, die UDP-Port 500 und 4500 und IP-Protokollnummer 50 verwendet. Firewalls öffnen nicht immer diese Ports, daher kann IKEv2-VPN unter Umständen Proxys und Firewalls nicht überwinden.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Wird die VPN-Verbindung eines für „Punkt zu Standort“ konfigurierten Clientcomputers nach einem Neustart automatisch wiederhergestellt?

Standardmäßig wird die VPN-Verbindung des Clientcomputers nicht automatisch wiederhergestellt.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Werden automatische Verbindungswiederherstellung und DDNS bei Punkt-zu-Standort-Verbindungen auf den VPN-Clients unterstützt?

Automatische Verbindungswiederherstellung und DDNS werden in Punkt-zu-Standort-VPNs derzeit nicht unterstützt.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kann ich im gleichen virtuellen Netzwerk sowohl Standort-zu-Standort- als auch Punkt-zu-Standort-Konfigurationen verwenden?

Ja. Beim Resource Manager-Bereitstellungsmodell müssen Sie als VPN-Typ für Ihr Gateway „RouteBased“ festlegen. Für das klassische Bereitstellungsmodell benötigen Sie ein dynamisches Gateway. Point-to-Site-Konfigurationen werden für VPN-Gateways mit statischem Routing oder für richtlinienbasierte Gateways nicht unterstützt.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kann ich einen Punkt-zu-Standort-Client so konfigurieren, dass er gleichzeitig eine Verbindung mit mehreren virtuellen Netzwerken herstellt?

Nein. Ein Point-to-Site-Client kann nur eine Verbindung mit Ressourcen im VNET herstellen, in dem sich das Gateway für virtuelle Netzwerke befindet.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Wie viel Durchsatz kann ich bei einer Standort-zu-Standort- oder bei einer Punkt-zu-Standort-Verbindung erwarten?

Der genaue Durchsatz der VPN-Tunnel lässt sich nur schwer ermitteln. IPsec und SSTP sind VPN-Protokolle mit hohem Kryptografieaufwand. Außerdem wird der Durchsatz durch die Latenz und die Bandbreite zwischen Ihrem Standort und dem Internet eingeschränkt. Bei einem VPN-Gateway nur mit IKEv2-P2S-VPN-Verbindungen hängt der erwartete Gesamtdurchsatz von der Gateway-SKU ab. Weitere Informationen zum Durchsatz finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Kann ich für Point-to-Site-Verbindungen einen beliebigen VPN-Softwareclient mit SSTP- und/oder IKEv2-Unterstützung verwenden?

Nein. Sie können nur den nativen VPN-Client unter Windows für SSTP und den nativen VPN-Client unter Mac für IKEv2 verwenden. Allerdings können Sie den OpenVPN-Client auf allen Plattformen verwenden, um über das OpenVPN-Protokoll eine Verbindung herzustellen. Informationen finden Sie in der Liste der unterstützten Clientbetriebssysteme.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Unterstützt Azure IKEv2-VPN unter Windows?

IKEv2 wird unter Windows 10 und Server 2016 unterstützt. Zur Verwendung von IKEv2 müssen Sie jedoch Updates installieren und lokal einen Registrierungsschlüsselwert festlegen. Betriebssystemversionen vor Windows 10 werden nicht unterstützt und können nur SSTP oder **OpenVPN® Protocol** verwenden.

Vorbereitung von Windows 10 oder Server 2016 für IKEv2:

1. Installieren Sie das Update.

   | Betriebssystemversion | Date | Anzahl/Link |
   |---|---|---|
   | Windows Server 2016<br>Windows 10, Version 1607 | 17. Januar 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Version 1703 | 17. Januar 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10, Version 1709 | 22. März 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Legen Sie den Registrierungsschlüsselwert fest. Erstellen Sie den REG_DWORD-Schlüssel „HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload“, oder legen Sie ihn in der Registrierung auf 1 fest.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Was geschieht, wenn ich sowohl SSTP als auch IKEv2 für P2S-VPN-Verbindungen konfiguriere?

Wenn Sie sowohl SSTP als auch IKEv2 in einer gemischten Umgebung (bestehend aus Windows- und Mac-Geräten) konfigurieren, versucht der Windows-VPN-Client immer zuerst den IKEv2-Tunnel, weicht jedoch auf SSTP aus, wenn die IKEv2-Verbindung nicht erfolgreich ist. MacOSX stellt nur eine Verbindung über IKEv2 her.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Welche anderen Plattformen, außer Windows und Mac, werden von Azure für P2S-VPN unterstützt?

Azure unterstützt Windows, Mac und Linux für P2S-VPN.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Ich verfüge bereits über ein bereitgestelltes Azure-VPN-Gateway. Kann ich RADIUS und/oder IKEv2-VPN darauf aktivieren?

Ja. Sie können diese neuen Features aus bereits bereitgestellten Gateways per PowerShell oder über das Azure-Portal aktivieren, sofern die verwendete Gateway-SKU RADIUS bzw. IKEv2 unterstützt. Die Basic-SKU des VPN-Gateways weist beispielsweise keine Unterstützung von RADIUS oder IKEv2 auf.
