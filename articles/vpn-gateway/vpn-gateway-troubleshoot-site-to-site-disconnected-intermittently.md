---
title: Problembehandlung bei zeitweiligen Unterbrechungen von Azure-Site-to-Site-VPN-Verbindungen
description: Erfahren Sie, wie Sie das Problem beheben, wenn die Standort-zu-Standort-VPN-Verbindung regelmäßig getrennt wird.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: genli
ms.openlocfilehash: 582077c46f5fc7176b457cf0d392af48fbe7d40b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369338"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Problembehandlung: Unterbrechungen der Verbindung des Azure-Standort-zu-Standort-VPN

Möglicherweise tritt bei Ihnen das Problem auf, dass eine neue oder bereits vorhandene Microsoft Azure Standort-zu-Standort-VPN-Verbindung nicht stabil ist oder regelmäßig getrennt wird. Dieser Artikel enthält Schritte, mit denen Sie die Ursache ausfindig machen und das Problem beheben können. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

### <a name="prerequisite-step"></a>Voraussetzung

Überprüfen Sie, um welchen Typ des virtuellen Netzwerkgateways von Azure es sich handelt:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Überprüfen Sie auf der **Übersichtsseite** des virtuellen Netzwerkgateways die Typinformationen.
    
    ![Übersicht über das Gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Schritt 1: Überprüfen, ob das lokale VPN-Gerät validiert wurde

1. Überprüfen Sie, ob [das VPN-Gerät und die Version des Betriebssystems validiert wurden](vpn-gateway-about-vpn-devices.md#devicetable). Wenn das VPN-Gerät nicht validiert wurde, müssen Sie sich möglicherweise beim Gerätehersteller erkundigen, ob ein Kompatibilitätsproblem vorliegt.
2. Stellen Sie sicher, dass das VPN-Gerät ordnungsgemäß konfiguriert ist. Weitere Informationen finden Sie unter [Bearbeiten von Gerätekonfigurationsbeispielen](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Schritt 2: Überprüfen der Sicherheitszuordnungseinstellungen (für richtlinienbasierte virtuelle Azure-Netzwerkgateways)

1. Stellen Sie sicher, dass das virtuelle Netzwerk, Subnetze und Bereiche in der Definition **Lokales Netzwerkgateway** in Microsoft Azure mit der Konfiguration auf dem lokalen VPN-Gerät identisch sind.
2. Stellen Sie sicher, dass die Sicherheitszuordnungseinstellungen übereinstimmen.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Schritt 3: Überprüfen auf benutzerdefinierte Routen oder Netzwerksicherheitsgruppen auf dem Gatewaysubnetz

Eine benutzerdefinierte Route auf dem Gatewaysubnetz schränkt möglicherweise einen Teil des Datenverkehrs ein und lässt einen anderen zu. Deshalb scheint die VPN-Verbindung für einen Teil des Datenverkehrs unzuverlässig und für einen anderen brauchbar zu sein. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Schritt 4: Überprüfen der Einstellung „ein VPN-Tunnel pro Subnetzpaar“ (für richtlinienbasierte virtuelle Netzwerkgateways)

Stellen Sie sicher, dass das lokale VPN-Gerät so eingestellt ist, dass es für richtlinienbasierte virtuelle Netzwerkgateways über **einen VPN-Tunnel pro Subnetzpaar** verfügt.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Schritt 5: Überprüfen auf Sicherheitszuordnungsbegrenzung (für richtlinienbasierte virtuelle Netzwerkgateways)

Beim richtlinienbasierten virtuellen Netzwerkgateway ist die Sicherheitszuordnung auf 200 Subnetzpaare beschränkt. Wenn die Anzahl der Subnetze des virtuellen Azure-Netzwerks multipliziert mit der Anzahl der lokalen Subnetze größer als 200 ist, kommt es zur Trennung der Verbindung einzelner Subnetze.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Schritt 6: Überprüfen der Adresse der externen Schnittstelle des lokalen VPN-Geräts

- Wenn die IP-Adresse des VPN-Geräts mit Internetzugriff in der Definition **Lokales Netzwerkgateway** in Azure enthalten ist, kann es vereinzelt zur Trennung der Verbindung kommen.
- Die externe Schnittstelle des Geräts muss sich direkt im Internet befinden. Es darf sich keine Netzwerkadressübersetzung (Network Address Translation, NAT) oder Firewall zwischen dem Internet und dem Gerät befinden.
-  Wenn Sie Firewall Clustering konfigurieren, um über eine virtuelle IP-Adresse zu verfügen, müssen Sie den Cluster unterbrechen und das VPN-Gerät direkt an eine öffentliche Schnittstelle anschließen, mit der das Gateway kompatibel ist.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Schritt 7: Überprüfen, ob beim lokalen VPN-Gerät Perfect Forward Secrecy aktiviert ist

Die Funktion **Perfect Forward Secrecy** kann zu den Problemen mit der Trennung der Verbindung führen. Wenn beim VPN-Gerät **Perfect Forward Secrecy** aktiviert ist, sollten Sie diese Funktion deaktivieren. [Aktualisieren Sie dann die IPsec-Richtlinie des virtuellen Netzwerkgateways](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](./tutorial-site-to-site-portal.md)
- [Konfigurieren einer IPsec/IKE-Richtlinie für Standort-zu-Standort-VPN-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md)
